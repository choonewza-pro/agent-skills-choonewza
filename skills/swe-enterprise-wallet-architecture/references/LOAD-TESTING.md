# Load Testing — k6

## Contents
- Concurrent user calculation
- Five test types
- Pre-test system tuning
- k6 script for wallet transactions

---

## Concurrent User Calculation

```
Concurrent Users = (Hourly Sessions × Avg Session Duration in seconds) / 3600
```

**Example:** 12,000 sessions/hour, average 120s per session:

```
12,000 × 120 / 3,600 = 400 concurrent users
```

Apply a **2.5× safety margin** to cover viral spikes: test at **1,000 concurrent users**.

---

## Five Test Types

| Test | VUs | Duration | Purpose |
|------|-----|----------|---------|
| **Smoke** | 1 | 1 min | Structural sanity check, catch basic bugs |
| **Load** | 400 | 30 min | Baseline latency at normal peak |
| **Stress** | 400→800→1200 | Stepped | Find breaking point, observe connection exhaustion |
| **Spike** | 0→2000 in 10s | Short | Test auto-scaling and retry behavior under sudden surge |
| **Soak** | 300 | 3–24 hrs | Detect memory leaks; measure degradation as table rows grow |

---

## Pre-Test System Tuning

Before running any test with >500 VUs, set the OS file descriptor limit on the load generator:

```bash
ulimit -n 65535
```

Without this, the OS cap on open connections becomes the bottleneck, not the server under test — producing misleading latency numbers.

Also set in k6 options:
```javascript
discardResponseBodies: true  // saves memory when response content is not validated
```

Keep load generator CPU below 80%. At 100% CPU, k6 cannot record latency timings accurately.

---

## k6 Script — Enterprise Wallet

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Counter, Trend, Rate } from 'k6/metrics';

const paymentFailureCounter = new Counter('payment_failures');
const p95TransactionDuration = new Trend('payment_transaction_latency');
const paymentSuccessRate = new Rate('payment_success_percentage');

export const options = {
  stages: [
    { duration: '30s', target: 100 },  // ramp up
    { duration: '2m',  target: 400 },  // hold at target
    { duration: '30s', target: 0   },  // ramp down
  ],
  thresholds: {
    http_req_failed:            ['rate<0.01'],   // network errors < 1%
    http_req_duration:          ['p(95)<350'],   // 95th percentile < 350ms
    payment_success_percentage: ['rate>0.99'],   // payment success > 99%
  },
};

export default function () {
  const url = 'https://api.your-platform.com/v1/payments/deduct';

  // Unique idempotency key per virtual user per iteration
  const idempotencyKey = `k6-${__VU}-${__ITER}-${Date.now()}-${Math.random()}`;

  const payload = JSON.stringify({
    userId: `user_${__VU}`,
    amount: 25.00,
    currency: 'THB',
    description: 'AI Credit Billing',
  });

  const params = {
    headers: {
      'Content-Type': 'application/json',
      'Idempotency-Key': idempotencyKey,
      'Authorization': 'Bearer <test-token>',
    },
  };

  const response = http.post(url, payload, params);

  const isOk = response.status === 200 || response.status === 201;
  paymentSuccessRate.add(isOk);
  p95TransactionDuration.add(response.timings.duration);

  if (!isOk) paymentFailureCounter.add(1);

  check(response, {
    'status is 200 or 201': (r) => r.status === 200 || r.status === 201,
    'has transactionId': (r) => !!JSON.parse(r.body).transactionId,
    'has remainingBalance': (r) => JSON.parse(r.body).remainingBalance !== undefined,
  });

  // Randomized think time: avoids thundering herd
  sleep(Math.random() * 1.5 + 0.5);
}
```

---

## Pass / Fail Criteria

| Metric | Threshold |
|--------|-----------|
| HTTP error rate | < 1% |
| p95 transaction latency | < 350ms |
| Payment success rate | > 99% |
| Negative balance occurrences | 0 |
| Duplicate deductions | 0 |
