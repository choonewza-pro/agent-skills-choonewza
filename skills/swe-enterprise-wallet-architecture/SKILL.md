---
name: swe-enterprise-wallet-architecture
description: Designs and reviews high-concurrency digital wallet and credit-deduction systems using double-entry bookkeeping, saga patterns, distributed locking, and idempotency controls. Use when designing a wallet system, reviewing payment/credit architecture, choosing between locking strategies, implementing saga or outbox patterns, setting up ledger schemas, or load-testing financial transactions.
metadata:
  author: choonewza@gmail.com
  version: "1.0.0"
---

# Enterprise Wallet & Credit-Deduction Architecture

Core reference for building production-grade digital wallet systems under high concurrency. Covers PostgreSQL schema design, concurrency controls, fault tolerance, security, and load testing.

**When to load supplementary files:**

- Choosing a locking strategy → see [LOCKING-STRATEGIES.md](references/LOCKING-STRATEGIES.md)
- Implementing Saga / Outbox → see [SAGA-OUTBOX.md](references/SAGA-OUTBOX.md)
- Security & Idempotency middleware → see [SECURITY.md](references/SECURITY.md)
- Load testing with k6 → see [LOAD-TESTING.md](references/LOAD-TESTING.md)
- Full ledger schema (DDL) → see [SCHEMA.md](references/SCHEMA.md)

---

## The 15 Financial Mishaps — Quick Reference

| #   | Mishap                       | Severity | Fix                                            | Difficulty |
| --- | ---------------------------- | -------- | ---------------------------------------------- | ---------- |
| 1   | Race Condition / TOCTOU      | Critical | `SELECT FOR UPDATE` or Atomic UPDATE           | 4/10       |
| 2   | Duplicate Request            | High     | Idempotency Key + body hash                    | 5/10       |
| 3   | Partial Failure              | High     | Saga + Compensating Transaction                | 7/10       |
| 4   | Lost Update                  | High     | Optimistic Locking or Atomic UPDATE            | 4/10       |
| 5   | Negative Balance             | High     | `CHECK (balance >= 0)` constraint              | 2/10       |
| 6   | Double Spending              | Critical | Serializable Isolation or Double-Entry Ledger  | 6/10       |
| 7   | Cache Stale                  | Medium   | Cache Invalidation on write                    | 5/10       |
| 8   | Retry Storm                  | Medium   | Circuit Breaker + Exponential Backoff + Jitter | 5/10       |
| 9   | Eventual Consistency         | Medium   | Read from Primary for payment paths            | 8/10       |
| 10  | Inconsistent Balance         | High     | Double-Entry Bookkeeping as source of truth    | 5/10       |
| 11  | Out-of-Order Processing      | Medium   | FIFO Queue (SQS FIFO / RabbitMQ)               | 6/10       |
| 12  | Deadlock                     | Medium   | Sort lock IDs consistently before acquiring    | 6/10       |
| 13  | Replay Attack                | High     | Digital Signature + Nonce + Timestamp window   | 5/10       |
| 14  | Phantom Read                 | Low      | Repeatable Read or Serializable isolation      | 4/10       |
| 15  | Integer Overflow / Precision | Medium   | `DECIMAL(19,4)` or BigInt sub-units            | 2/10       |

---

## Core Architecture Principles

### 1. Always use Double-Entry Bookkeeping

Never update a `balance` column directly. Every transaction must produce a DEBIT and CREDIT posting that sums to zero:

```
∑ Debits − ∑ Credits = 0
```

Account types: **Asset**, **Liability**, **Equity**, **Revenue**, **Expense**  
Current balance = computed from ledger postings, not stored as a field.

Full DDL → [SCHEMA.md](references/SCHEMA.md)

### 2. Choose the right locking strategy

**Atomic UPDATE** (recommended for high-throughput):

```sql
UPDATE wallets
SET balance = balance - :cost
WHERE user_id = :uid AND balance >= :cost
RETURNING id, balance;
```

Zero deadlock risk. Fails gracefully when balance is insufficient (0 rows affected).

**Pessimistic Locking** (for complex multi-step business logic):

```sql
BEGIN;
SELECT balance FROM wallets WHERE user_id = :uid FOR UPDATE;
-- validate conditions in application layer
UPDATE wallets SET balance = balance - :cost WHERE user_id = :uid;
COMMIT;
```

Full comparison → [LOCKING-STRATEGIES.md](references/LOCKING-STRATEGIES.md)

### 3. Enforce balance safety at the database layer

```sql
ALTER TABLE wallets ADD CONSTRAINT chk_balance_non_negative CHECK (balance >= 0);
```

Add a Deferred Constraint Trigger on `ledger_postings` to verify debit/credit balance at COMMIT time. Full DDL → [SCHEMA.md](references/SCHEMA.md)

### 4. Use Saga for cross-service operations

Steps: deduct credit (PENDING) → call external API → mark SUCCESS. On failure: compensate (REFUND) automatically.

Never wrap an external HTTP call inside a database transaction.

Full Saga + Outbox implementation → [SAGA-OUTBOX.md](references/SAGA-OUTBOX.md)

### 5. Use Fencing Tokens with Distributed Locks

Plain Redis locks are vulnerable to GC Pause: the lock expires while the process is frozen, another process acquires it, and the zombie process writes stale data after recovering.

Fix: attach a monotonically increasing Fencing Token to every lock. The database rejects writes with an outdated token:

```sql
UPDATE wallets
SET balance = balance - 100, last_fence_token = :new_token
WHERE id = :wallet_id AND last_fence_token < :new_token;
```

Full implementation → [LOCKING-STRATEGIES.md](references/LOCKING-STRATEGIES.md)

### 6. Idempotency is mandatory

Every payment endpoint must require an `Idempotency-Key` header. The middleware must:

1. Scope the cache key as `idem:{userId}:{method}:{path}:{key}`
2. Use `SET key "IN_PROGRESS" NX EX 120` to prevent parallel processing
3. Hash the request body — return `409` if the same key is reused with different payload

Full middleware code → [SECURITY.md](references/SECURITY.md)

---

## Decision Guide

**Choosing a locking mechanism:**

- Simple deduction, high throughput → **Atomic UPDATE**
- Multi-condition check before deduct → **Pessimistic Locking (FOR UPDATE)**
- Multi-server, must prevent zombie writes → **Distributed Lock + Fencing Token**
- Read-heavy, write-light → **Optimistic Locking (lock_version)**

**Choosing an outbox delivery method:**

- Simple setup acceptable, slight latency OK → **Polling-based** (batch job queries outbox table)
- Low latency, production scale → **CDC-based** (Debezium reads WAL directly, no query load)
- Maximum performance, skip outbox table entirely → **`pg_logical_emit_message()`** (write event directly to WAL inside the transaction)

**Concurrent users estimation:**

```
Concurrent Users = (Hourly Sessions × Avg Session Duration in seconds) / 3600
```

Apply 2.5× safety margin for viral/spike scenarios.

---

## Architecture Trade-off Summary

| Mechanism                  | Throughput  | Deadlock Risk | Hot Account Perf | Zombie Protection |
| -------------------------- | ----------- | ------------- | ---------------- | ----------------- |
| Pessimistic Locking        | Medium-Low  | Medium        | Poor             | High              |
| Optimistic Locking         | High        | None          | Poor             | Medium            |
| Atomic UPDATE              | Highest     | None          | High             | High              |
| Distributed Lock (plain)   | High        | None          | Good             | Low               |
| Distributed Lock + Fencing | Medium-High | None          | High             | Highest           |
