# Security & Idempotency

## Contents
- Idempotency Key middleware (Express + Redis)
- OWASP API Security Top 10 controls
- Ledger cryptographic seals

---

## Idempotency Key Middleware

Every payment endpoint MUST require an `Idempotency-Key` header.

### The Concurrent Retry Problem

When a client retries a timed-out request, both the original and the retry may arrive simultaneously. Without atomic locking, both pass the duplicate check and deduct credits twice.

### Implementation

```typescript
import { Request, Response, NextFunction } from 'express';
import crypto from 'crypto';
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL || '');

export const enforceIdempotency = async (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  const key = req.headers['idempotency-key'] as string;
  if (!key) {
    return res.status(400).json({ error: 'IDEMPOTENCY_KEY_REQUIRED' });
  }

  const userId = req.user?.id;
  const bodyHash = crypto
    .createHash('sha256')
    .update(JSON.stringify(req.body))
    .digest('hex');

  // Composite key: scope to user + method + path to prevent cross-endpoint reuse
  const cacheKey = `idem:${userId}:${req.method}:${req.path}:${key}`;

  // Atomic NX: only one request proceeds; parallel retries get 409
  const acquired = await redis.set(
    cacheKey,
    JSON.stringify({ status: 'PROCESSING', bodyHash }),
    'NX',
    'EX',
    120
  );

  if (!acquired) {
    const existing = await redis.get(cacheKey);
    if (existing) {
      const parsed = JSON.parse(existing);

      if (parsed.status === 'PROCESSING') {
        return res.status(409).json({ error: 'TRANSACTION_IN_PROGRESS_PLEASE_WAIT' });
      }

      // Same key, different payload → reject (prevents amount manipulation)
      if (parsed.bodyHash !== bodyHash) {
        return res.status(409).json({
          error: 'IDEMPOTENCY_KEY_REUSED_WITH_DIFFERENT_PAYLOAD',
        });
      }

      // Cache hit: replay the original response
      return res.status(parsed.statusCode).json(parsed.responseBody);
    }
  }

  // Intercept response to cache it after processing
  const originalJson = res.json;
  res.json = function (body) {
    redis.set(
      cacheKey,
      JSON.stringify({
        status: 'COMPLETED',
        bodyHash,
        statusCode: res.statusCode,
        responseBody: body,
      }),
      'EX',
      86400 // cache for 24 hours
    );
    return originalJson.call(this, body);
  };

  next();
};
```

---

## OWASP API Security Controls

### BOLA (Broken Object Level Authorization)
Never expose numeric IDs in payment endpoints. Always verify wallet ownership from the session token, not from a URL parameter.

```typescript
// Bad: anyone can query any wallet
GET /wallets/:walletId/balance

// Good: derive wallet from authenticated session
GET /me/wallet/balance  // server resolves userId from JWT
```

### BOPLA (Broken Object Property Level Authorization)
Validate all request bodies with a strict schema (e.g., Zod). Reject unknown properties. Prevents users from injecting `role: "ADMIN"` or `balance: 99999` alongside a normal request.

```typescript
import { z } from 'zod';

const DeductSchema = z.object({
  amount: z.number().positive().max(10000),
  description: z.string().max(256),
}).strict(); // .strict() rejects extra keys
```

### Rate Limiting
Apply per-user rate limits on payment endpoints to prevent bot-driven credit exhaustion.

---

## Ledger Cryptographic Seals (Merkle-chain)

Each ledger row stores a hash chained to the previous row's hash. Tampering with any historical record breaks the chain and is detected immediately.

```typescript
const hashLedgerEntry = (entry: LedgerEntry, previousHash: string): string => {
  const payload = JSON.stringify({
    id: entry.id,
    amount: entry.amount,
    direction: entry.direction,
    accountId: entry.accountId,
    transactionId: entry.transactionId,
    createdAt: entry.createdAt,
    previousHash,
  });

  return crypto.createHash('sha256').update(payload).digest('hex');
};
```

Store `entry_hash` and `previous_hash` on every `ledger_postings` row. A background integrity checker can verify the chain at any time.
