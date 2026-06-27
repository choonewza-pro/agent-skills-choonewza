# Ledger Schema — PostgreSQL DDL

## Contents
- ENUM types
- Chart of Accounts (ledger_accounts)
- Transaction header (ledger_transactions)
- Journal postings (ledger_postings)
- Exchange rates (currency_exchange_rates)
- Wallet table with fencing token
- Wallet ledger (for simplified Saga tracking)
- Deferred constraint trigger (balance integrity)
- Indexes

---

## ENUM Types

```sql
CREATE TYPE account_category    AS ENUM ('ASSET', 'LIABILITY', 'EQUITY', 'REVENUE', 'EXPENSE');
CREATE TYPE normal_balance_type AS ENUM ('DEBIT', 'CREDIT');
CREATE TYPE journal_direction   AS ENUM ('DEBIT', 'CREDIT');
```

---

## Chart of Accounts

```sql
CREATE TABLE ledger_accounts (
  id             UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name           VARCHAR(256) NOT NULL UNIQUE,
  category       account_category NOT NULL,
  normal_balance normal_balance_type NOT NULL,
  currency       VARCHAR(3) NOT NULL,
  lock_version   INT NOT NULL DEFAULT 0,  -- for Optimistic Concurrency Control
  created_at     TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);
```

---

## Transaction Header

```sql
CREATE TABLE ledger_transactions (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  description     VARCHAR(1024) NOT NULL,
  idempotency_key VARCHAR(256) UNIQUE NOT NULL,  -- prevents duplicate transactions
  effective_at    TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
  created_at      TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);
```

---

## Journal Postings (Append-Only)

```sql
CREATE TABLE ledger_postings (
  id             UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  transaction_id UUID NOT NULL REFERENCES ledger_transactions(id) ON DELETE RESTRICT,
  account_id     UUID NOT NULL REFERENCES ledger_accounts(id) ON DELETE RESTRICT,
  direction      journal_direction NOT NULL,
  amount         DECIMAL(19,4) NOT NULL CHECK (amount > 0.0),  -- no zeros, no floats
  created_at     TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_postings_transaction_id ON ledger_postings(transaction_id);
CREATE INDEX idx_postings_account_id     ON ledger_postings(account_id);
```

Never UPDATE or DELETE ledger_postings. All corrections are new compensating entries.

---

## Deferred Constraint Trigger — Balance Integrity

Fires at COMMIT time. Rejects any transaction where debits ≠ credits.

```sql
CREATE OR REPLACE FUNCTION check_journal_entry_balance()
RETURNS TRIGGER AS $$
DECLARE
  v_net_amount DECIMAL(19,4);
BEGIN
  SELECT COALESCE(
    SUM(CASE WHEN direction = 'DEBIT' THEN amount ELSE -amount END), 0.0
  ) INTO v_net_amount
  FROM ledger_postings
  WHERE transaction_id = NEW.transaction_id;

  IF v_net_amount <> 0.0 THEN
    RAISE EXCEPTION
      'Double-entry balance mismatch for transaction %. Net difference: %',
      NEW.transaction_id, v_net_amount;
  END IF;

  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE CONSTRAINT TRIGGER trigger_verify_ledger_balance
AFTER INSERT OR UPDATE ON ledger_postings
DEFERRABLE INITIALLY DEFERRED
FOR EACH ROW
EXECUTE FUNCTION check_journal_entry_balance();
```

---

## Multi-Currency Exchange Rates

```sql
CREATE TABLE currency_exchange_rates (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  source_currency VARCHAR(3) NOT NULL,
  target_currency VARCHAR(3) NOT NULL,
  rate            DECIMAL(19,6) NOT NULL,
  effective_at    TIMESTAMPTZ NOT NULL,
  created_at      TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);
```

Always store the exchange rate at transaction time. Never recompute from current rates.

---

## Wallet Table (Simplified — for Saga-based systems)

```sql
CREATE TABLE wallets (
  id                UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id           UUID NOT NULL UNIQUE,
  balance           DECIMAL(19,4) NOT NULL DEFAULT 0 CHECK (balance >= 0),
  last_fence_token  BIGINT NOT NULL DEFAULT 0,  -- for Fencing Token pattern
  created_at        TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  updated_at        TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);
```

---

## Wallet Ledger (Saga Status Tracking)

```sql
CREATE TYPE ledger_entry_type   AS ENUM ('CHARGE', 'REFUND', 'TOPUP', 'ADJUSTMENT');
CREATE TYPE ledger_entry_status AS ENUM ('PENDING', 'SUCCESS', 'FAILED', 'REFUNDED');

CREATE TABLE wallet_ledger (
  id                UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  wallet_id         UUID NOT NULL REFERENCES wallets(id),
  type              ledger_entry_type NOT NULL,
  amount            DECIMAL(19,4) NOT NULL CHECK (amount > 0),
  previous_balance  DECIMAL(19,4) NOT NULL,
  new_balance       DECIMAL(19,4) NOT NULL,
  idempotency_key   VARCHAR(256) UNIQUE NOT NULL,
  status            ledger_entry_status NOT NULL DEFAULT 'PENDING',
  reference_type    VARCHAR(64),
  reference_id      VARCHAR(256),
  created_at        TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_wallet_ledger_wallet_id       ON wallet_ledger(wallet_id);
CREATE INDEX idx_wallet_ledger_idempotency_key ON wallet_ledger(idempotency_key);
```
