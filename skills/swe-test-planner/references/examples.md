# Worked Example: swe-test-planner Output

This file shows a **complete, realistic output** from `swe-test-planner` for
a Next.js e-commerce project. Use this as the gold-standard reference for
output format, tone, and level of detail.

---

## Input Context

- **Project:** `nextjs-shop` — Next.js 14 App Router, TypeScript, Prisma, Stripe
- **Framework detected:** Next.js (found `next.config.ts`)
- **Git available:** Yes
- **Scan date:** 2026-09-16

---

## Full Output (what the agent should produce)

---

## 🔍 Risk-based Test Plan

**Project:** nextjs-shop
**Scanned:** 2026-09-16
**Git range:** Last 90 days (143 commits total)
**Features found:** 8
**Framework detected:** Next.js (App Router)

---

## 📊 Risk Table

| # | Feature | Impact | Likelihood | Risk Score | Zone | Existing Tests | Recommended Technique |
|---|---------|--------|------------|------------|------|---------------|-----------------------|
| 1 | Payment (Stripe checkout) | 5 | 5 | 25 | 🔴 Critical | ❌ | STT + EP |
| 2 | Auth (Login / JWT) | 5 | 4 | 20 | 🔴 Critical | ❌ | STT |
| 3 | Order Management | 4 | 4 | 16 | 🔴 Critical | ✅ | STT + BVA |
| 4 | Product Search & Filter | 3 | 4 | 12 | 🟠 High | ❌ | EP |
| 5 | Cart | 4 | 2 | 8 | 🟡 Medium | ✅ | EP + BVA |
| 6 | User Profile | 3 | 2 | 6 | 🟡 Medium | ❌ | EP |
| 7 | Notification (Email) | 3 | 1 | 3 | 🟢 Low | ❌ | EP |
| 8 | Landing Page / Static | 1 | 1 | 1 | 🟢 Low | ❌ | — |

**Zone legend:**
- 🔴 **Critical** (≥ 16) — test first, block release if missing
- 🟠 **High** (9–15) — test in current sprint
- 🟡 **Medium** (4–8) — test when capacity allows
- 🟢 **Low** (1–3) — defer or test last

---

> 💬 **Impact Review** — Scores assigned by keyword heuristics. Do any look wrong for your project?
> *(e.g. "Cart should be 5 because we're launching next week and it's the core flow")*

*(Agent asks via AskUserQuestion here — example assumes user replies "Looks correct — proceed")*

---

## 🎯 Recommended Testing Approach

### 🔴 Critical — Test Immediately

- **Payment (Stripe checkout)** — Has payment state machine (cart → pending → paid → refunded → failed)
  → use **State Transition Testing (STT)** for status flow.
  Also has amount validation (min/max), card type enum → add **EP** for those inputs.

- **Auth (Login / JWT)** — Has session state (anonymous → authenticated → expired → locked)
  → use **STT** for session transitions. Role enum (admin/user/guest) → **EP**.

- **Order Management** — Has order status workflow (draft → confirmed → shipped → delivered → cancelled)
  → use **STT**. Also has numeric constraints (quantity ≥ 1, price > 0) → **BVA**.

### 🟠 High — Test This Sprint

- **Product Search & Filter** — Category dropdown, price range filter, sort enum
  → use **EP** for each input type. No workflow detected.

### 🟡 Medium — Schedule for Next Sprint

- **Cart** — Item quantity (numeric range), product type (enum), coupon code (enum/text)
  → **BVA** for quantity, **EP** for product type and coupon.

- **User Profile** — Field validation (name, email format, phone format) → **EP**.

### 🟢 Low — Defer

- **Notification (Email)** and **Landing Page** have negligible risk.
  Test only if time permits after Critical + High are covered.

---

## 🔧 Technique Guide

| Technique | When to Use | swe-test-engineer keyword |
|-----------|-------------|--------------------------|
| **BVA** | Numeric/time inputs with ranges (age, amount, quantity, date) | "boundary value" |
| **EP** | Enum, dropdown, role, boolean, text type inputs | "equivalence partitioning" |
| **STT** | Status fields, approval flows, state machines | "state transition" |
| **Mixed** | Feature has both range inputs AND enum/workflow inputs | List each condition separately |

---

## 🤖 AI Test Prompts & Context Package

### 🔴 Critical Feature 1: Payment (Stripe Checkout)

**Context Assets Discovered:**
- 📄 Source: `src/services/paymentService.ts` (`processCheckout`)
- 📑 Types & Schemas: `src/types/payment.ts` (`CheckoutPayload`, `PaymentStatus`)
- 🧪 Style Blueprint Test: `src/services/__tests__/cartService.test.ts` (Vitest, `describe/it`, AAA, `vi.mock`)
- 🔌 Dependencies to Mock: `stripe.paymentIntents`, `prisma.order`
- 🚫 Dependencies NOT to Mock: `calculateTax()` utility, currency formatting

**Specific Prompt for AI Assistant:**
> Write comprehensive unit tests for `processCheckout()` in `src/services/paymentService.ts`:
> - **Target:** `processCheckout(orderId: string, amount: number, paymentMethod: string)`
> - **Happy Path:**
>   - Valid payment method and positive amount creates Stripe PaymentIntent and updates order status to 'processing'.
> - **Negative / Error Cases:**
>   - Invalid/declined card triggers `PaymentFailedError` and updates order status to 'failed'.
>   - Non-existent orderId throws `NotFoundError`.
> - **Edge Cases (swe-test-engineer values):**
>   - BVA on amount: 0 THB (fails with InvalidAmountError), 1 THB (minimum valid, passes), 500,000 THB (maximum limit, passes), 500,001 THB (fails).
>   - EP on payment methods: valid partitions `['card', 'promptpay', 'truemoney']`, invalid partition `'unsupported_crypto'`.
>   - STT status transition: attempt transition from 'refunded' back to 'paid' must reject.
> - **Behavior to Verify:**
>   - Verify `stripe.paymentIntents.create` is invoked with correct idempotent key and metadata.
>   - Verify order transaction log record is written to DB.
> - **Constraints (สิ่งที่ห้ามทำ):**
>   - Do NOT mock `calculateTax` helper.
>   - Do NOT test internal database connection pool.
>   - Follow Vitest style from `src/services/__tests__/cartService.test.ts` using `vi.mock` and `expect().toMatchObject()`.

---

### 🔴 Critical Feature 2: Auth (Login & Session)

**Context Assets Discovered:**
- 📄 Source: `src/services/authService.ts` (`loginUser`)
- 📑 Types & Schemas: `src/types/auth.ts` (`LoginInput`, `UserRole`)
- 🧪 Style Blueprint Test: `src/services/__tests__/cartService.test.ts`
- 🔌 Dependencies to Mock: `prisma.user`, `jwt.sign`
- 🚫 Dependencies NOT to Mock: `bcrypt.compare` (or use deterministic salt)

**Specific Prompt for AI Assistant:**
> Write unit tests for `loginUser()` in `src/services/authService.ts`:
> - **Target:** `loginUser(email: string, password: string)`
> - **Happy Path:**
>   - Correct email and password returns JWT token and user profile (excluding password hash).
> - **Negative / Error Cases:**
>   - Non-existent email throws `InvalidCredentialsError`.
>   - Incorrect password throws `InvalidCredentialsError` (generic message to prevent email enumeration).
> - **Edge Cases (swe-test-engineer values):**
>   - EP email partitions: invalid partition `'user@'`, `'user.com'`, empty string `""` throw `ValidationError`.
>   - STT account state: locked user (`status === 'locked'`) cannot login and throws `AccountLockedError`.
> - **Behavior to Verify:**
>   - Verify password is verified with hash and plain password is never returned or logged.
> - **Constraints (สิ่งที่ห้ามทำ):**
>   - Follow existing AAA test pattern in `src/services/__tests__/cartService.test.ts`.

---

## ➡️ Next Steps

1. Start with 🔴 **Critical** features: Payment → Auth → Order using the **Specific Prompts** above.
2. For detailed boundary analysis or decision tables, run **swe-test-engineer** with the requirement.
3. Pass the generated Prompt & Context Package to **swe-test-unit-test-writer** (for unit tests) or **swe-test-integration-test-writer** (for API tests).

---

✅ Saved to `test-plan/risk-plan-2026-09-16.md`

---

## Notes on This Example

- **Payment Impact=5, Likelihood=5**: Stripe integration (external API +1), async webhook handler (+1),
  recent `fix: payment webhook retry` commit (+1), no test files found → base=3 from 18 commits + modifiers → 5
- **Cart Impact=4, Likelihood=2**: Only 3 commits in 90 days, has existing `cart.test.ts` (−1 modifier)
- **Landing Page Impact=1**: Matched keyword `landing` → Impact 1 immediately, no git activity
- **Notification Likelihood=1**: 0 commits in 90 days, falls in "Very Low" git bucket
