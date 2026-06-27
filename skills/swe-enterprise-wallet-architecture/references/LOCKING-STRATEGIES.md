# Locking Strategies

## Contents
- Pessimistic Locking (SELECT FOR UPDATE)
- Atomic UPDATE
- Optimistic Locking
- Distributed Lock (Redis)
- Fencing Token pattern

---

## Pessimistic Locking — SELECT FOR UPDATE

Best for: complex business logic that requires multiple checks before deduction.

```typescript
const deductCreditPessimistic = async (userId: string, cost: number) => {
  return await prisma.$transaction(async (tx) => {
    const [wallet]: any = await tx.$queryRawUnsafe(
      'SELECT id, balance FROM "Wallet" WHERE "userId" = $1 FOR UPDATE',
      userId
    );

    if (!wallet || wallet.balance < cost) {
      throw new Error('INSUFFICIENT_CREDITS');
    }

    return tx.wallet.update({
      where: { id: wallet.id },
      data: { balance: { decrement: cost } },
    });
  });
};
```

**Risks:** Queue buildup on hot accounts. Deadlock if locks are acquired in inconsistent order across tables — always sort IDs before locking.

---

## Atomic UPDATE

Best for: high-throughput deductions with a single balance check.

```typescript
const deductCreditAtomic = async (userId: string, cost: number) => {
  const result = await prisma.$queryRaw<{ id: string; balance: number }[]>`
    UPDATE "Wallet"
    SET balance = balance - ${cost}
    WHERE "userId" = ${userId} AND balance >= ${cost}
    RETURNING id, balance
  `;

  if (!result || result.length === 0) {
    throw new Error('INSUFFICIENT_CREDITS_OR_WALLET_NOT_FOUND');
  }

  return result[0];
};
```

Zero deadlock risk. The database handles the check-and-update atomically. Preferred for AI credit billing, game credits, and similar pay-per-use workloads.

---

## Optimistic Locking — lock_version

Best for: read-heavy workloads where write conflicts are rare.

```sql
UPDATE wallets
SET balance = balance - :cost, lock_version = lock_version + 1
WHERE user_id = :uid AND lock_version = :expected_version;
```

If 0 rows affected → version conflict → retry with exponential backoff.

**Risk:** Under hot-account write contention, retry loops amplify load. Not suitable for high-write scenarios.

---

## Distributed Lock with Fencing Token

Use when multiple application servers share one database and zombie processes (from GC pause or network partition) must be prevented from writing stale data.

### The GC Pause Problem

1. Server A acquires Redis lock (token=41, TTL=10s)
2. Server A enters GC pause for 15s — lock expires
3. Server B acquires the same lock (token=42)
4. Server B completes its write successfully
5. Server A recovers from GC pause and writes stale data — **data corrupted**

### Fencing Token Fix

Every lock acquisition generates a monotonically increasing token. The database rejects writes where the token is not strictly greater than the last accepted token.

```typescript
import Redis from 'ioredis';
const redis = new Redis(process.env.REDIS_URL);

const executeWithDistributedLock = async (
  userId: string,
  action: (fencingToken: number) => Promise<any>
) => {
  const lockKey = `lock:wallet:${userId}`;
  const ttl = 5000;

  const fencingToken = await redis.incr('fencing_token_sequence');
  const lockToken = `token_${fencingToken}`;

  const acquired = await redis.set(lockKey, lockToken, 'PX', ttl, 'NX');
  if (!acquired) {
    throw new Error('LOCK_ACQUISITION_FAILED_TRANSACTION_IN_PROGRESS');
  }

  try {
    return await action(fencingToken);
  } finally {
    // Lua script: only delete if token matches (prevents deleting another server's lock)
    const releaseScript = `
      if redis.call("get", KEYS[1]) == ARGV[1] then
        return redis.call("del", KEYS[1])
      else
        return 0
      end
    `;
    await redis.eval(releaseScript, 1, lockKey, lockToken);
  }
};
```

### Database-side Fencing Check

```sql
UPDATE wallets
SET
  balance = balance - 100.00,
  last_fence_token = :new_token
WHERE
  id = :wallet_id
  AND last_fence_token < :new_token;
```

0 rows affected = stale token → reject the write.

### When to use ZooKeeper / etcd instead of Redis

Redis Monotonic INCR is sufficient for most use cases. Use ZooKeeper or etcd when you need:
- Strict leader election guarantees
- Built-in monotonic epoch sequences
- Stronger consistency than Redis single-node provides

---

## Database Constraint Error Handling

When using **Atomic UPDATE** or **Pessimistic Locking** with a database-level `CHECK (balance >= 0)` constraint, if a user's balance is insufficient, the database will throw a Check Constraint Violation error.

Your application layer should catch this specific error and map it to a user-friendly error message, rather than letting a generic 500 error bubble up.

### Prisma Example (PostgreSQL)

PostgreSQL's error code for Check Constraint Violation is `23514`. In Prisma, this is wrapped under the `P2010` (Raw query failed) or `P2004` (Assertion failed on DB constraint) codes.

```typescript
import { Prisma } from '@prisma/client';

const deductCreditWithErrorHandling = async (userId: string, cost: number) => {
  try {
    return await deductCreditAtomic(userId, cost);
  } catch (error: any) {
    // Catch PostgreSQL Check Constraint Violation (23514) via Prisma
    if (
      error instanceof Prisma.PrismaClientKnownRequestError &&
      error.code === 'P2010' && // Raw query failed
      error.meta?.message?.includes('violates check constraint')
    ) {
      throw new Error('INSUFFICIENT_BALANCE');
    }

    // Direct check if using standard Prisma methods that trigger constraint checks
    if (error.code === 'P2004' || error.message?.includes('violates check constraint')) {
      throw new Error('INSUFFICIENT_BALANCE');
    }

    throw error;
  }
};
```
