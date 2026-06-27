# Saga & Transactional Outbox

## Contents
- Saga Pattern overview
- Saga Orchestrator implementation (Node.js + Prisma)
- Transactional Outbox: Polling vs CDC
- Bypassing the Outbox via pg_logical_emit_message

---

## Saga Pattern

Use when a workflow calls an external service (AI API, payment gateway) that cannot participate in a database ACID transaction.

**Three transaction types in a Saga:**

| Type | Description |
|------|-------------|
| **Compensable** | Can be reversed — e.g., refund the credit |
| **Pivot** | Point of no return — if this succeeds, continue forward |
| **Retryable** | After the pivot — guaranteed idempotent, safe to retry |

**Flow for AI generation:**

```
Deduct credit (PENDING) → Call AI API → Mark SUCCESS
                                ↓ on failure
                         Compensate: REFUND credit
```

---

## Saga Orchestrator — Node.js + Prisma

```typescript
const runAIGenerationSaga = async (
  userId: string,
  cost: number,
  idempotencyKey: string,
  prompt: string
) => {
  let ledgerId: string | null = null;

  try {
    // Step 1: Deduct credit and create PENDING ledger entry atomically
    const { ledgerId: id } = await prisma.$transaction(async (tx) => {
      const wallet = await tx.wallet.findUnique({ where: { userId } });
      if (!wallet || wallet.balance < cost) throw new Error('INSUFFICIENT_CREDITS');

      const previousBalance = wallet.balance;
      const newBalance = wallet.balance - cost;

      await tx.wallet.update({
        where: { userId },
        data: { balance: newBalance },
      });

      const ledger = await tx.walletLedger.create({
        data: {
          walletId: wallet.id,
          type: 'CHARGE',
          amount: cost,
          previousBalance,
          newBalance,
          idempotencyKey,
          status: 'PENDING',
          referenceType: 'AI_IMAGE_GENERATION',
        },
      });

      return { ledgerId: ledger.id };
    });

    ledgerId = id;

    // Step 2: Call external service (outside DB transaction)
    const aiResponse = await callExternalAIImageAPI(prompt);

    // Step 3: Mark SUCCESS
    await prisma.walletLedger.update({
      where: { id: ledgerId },
      data: { status: 'SUCCESS', referenceId: aiResponse.imageJobId },
    });

    return { success: true, imageUrl: aiResponse.imageUrl };

  } catch (error: any) {
    // Compensate: refund if credit was deducted
    if (ledgerId) {
      await refundCreditCompensatory(userId, cost, idempotencyKey);
    }
    throw new Error('TRANSACTION_FAILED_AND_CREDITS_REFUNDED_SAFELY');
  }
};

const refundCreditCompensatory = async (
  userId: string,
  cost: number,
  originalIdempotencyKey: string
) => {
  await prisma.$transaction(async (tx) => {
    const wallet = await tx.wallet.update({
      where: { userId },
      data: { balance: { increment: cost } },
    });

    await tx.walletLedger.create({
      data: {
        walletId: wallet.id,
        type: 'REFUND',
        amount: cost,
        previousBalance: wallet.balance - cost,
        newBalance: wallet.balance,
        idempotencyKey: `refund:${originalIdempotencyKey}`,
        status: 'COMPLETED',
        referenceType: 'SAGA_COMPENSATION',
        referenceId: originalIdempotencyKey,
      },
    });
  });
};
```

---

## Transactional Outbox Pattern

Solves the Dual-Write Problem: writing to the database AND publishing an event must be atomic.

### Option A — Polling-based

1. Write business data + outbox message in the same DB transaction
2. A background job polls the outbox table and publishes to Kafka/SQS
3. Mark messages as delivered

**Trade-off:** Simple to implement. Adds polling query load. Introduces seconds of latency.

### Option B — CDC-based with Debezium (recommended)

1. Write business data + outbox message in the same DB transaction
2. Debezium reads PostgreSQL Write-Ahead Log (WAL) directly — no polling
3. Events arrive at Kafka in milliseconds

**PostgreSQL outbox table best practices:**
- Use `REPLICA IDENTITY DEFAULT` (not FULL) — FULL bloats WAL with full row images
- Keep the table narrow: only keys and reference IDs, no large JSON payloads
- Partition by time (daily or hourly) and drop old partitions — faster than `DELETE` loops which create WAL tombstones and vacuum pressure

### Option C — pg_logical_emit_message (maximum performance)

Skip the outbox table entirely. Write the event directly into WAL inside the transaction:

```sql
BEGIN;
-- Business logic: deduct credit, write ledger entries ...

-- Emit event transactionally into WAL (only reaches WAL on COMMIT)
SELECT pg_logical_emit_message(
  true,                                              -- transactional
  'credit_events',                                   -- channel name
  '{"event_type": "DEDUCTED", "userId": "user-1"}'  -- payload
);
COMMIT;
```

Debezium captures this from WAL and forwards to Kafka within milliseconds. No outbox table, no table bloat.

**Use this when:** event throughput is very high and you want to eliminate outbox table overhead.

---

## Saga Recovery & Reconciliation

In a production system, application servers can crash, network connections can drop, or external APIs can time out in the middle of a Saga execution (e.g., after the credits are deducted but before the external API returns a response).

This leaves the `wallet_ledger` entry in a `PENDING` state indefinitely.

### Mitigation: Reconciliation Background Job

To guarantee eventual consistency, run a background cron job (or worker process) that scans for stuck transactions and reconciles them.

```typescript
const reconcileStuckSagas = async () => {
  // Find PENDING ledger entries older than 15 minutes
  const threshold = new Date(Date.now() - 15 * 60 * 1000);
  
  const stuckLedgers = await prisma.walletLedger.findMany({
    where: {
      status: 'PENDING',
      createdAt: { lt: threshold },
    },
  });

  for (const ledger of stuckLedgers) {
    try {
      // 1. Query the external service's status using the idempotency key or reference ID
      const externalStatus = await checkExternalServiceStatus(ledger.idempotencyKey);

      if (externalStatus.status === 'SUCCESS') {
        // External operation succeeded: Mark our ledger as SUCCESS
        await prisma.walletLedger.update({
          where: { id: ledger.id },
          data: { status: 'SUCCESS', referenceId: externalStatus.referenceId },
        });
      } else if (externalStatus.status === 'FAILED' || externalStatus.status === 'NOT_FOUND') {
        // External operation failed or never occurred: Compensate (Refund)
        await refundCreditCompensatory(ledger.walletId, ledger.amount, ledger.idempotencyKey);
        
        await prisma.walletLedger.update({
          where: { id: ledger.id },
          data: { status: 'FAILED' },
        });
      }
    } catch (err) {
      console.error(`Failed to reconcile ledger ${ledger.id}:`, err);
    }
  }
};
```
