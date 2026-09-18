# Auto-Scan Heuristics

Used by `swe-test-planner` Step 1 (Discover) and Step 2 (Likelihood).

---

## Step 1: Feature Discovery

### Folder Patterns to Scan

Scan the project root for these patterns to find features. Try them in order
and stop at the first match per category.

#### Routes / Pages (Frontend)
```
app/**/page.tsx          ← Next.js App Router
app/**/page.jsx
pages/**/*.tsx           ← Next.js Pages Router
pages/**/*.jsx
src/views/**/*.vue       ← Vue
src/pages/**/*.tsx
src/screens/**/*.tsx     ← React Native
```

#### API Endpoints (Backend)
```
app/**/route.ts          ← Next.js App Router API
pages/api/**/*.ts        ← Next.js API Routes
src/controllers/**/*.ts
src/handlers/**/*.ts
src/routes/**/*.ts
src/api/**/*.ts
src/resolvers/**/*.ts    ← GraphQL
```

#### Services / Domain Logic
```
src/services/**/*.ts
src/usecases/**/*.ts
src/domain/**/*.ts
src/modules/**/*.ts
lib/**/*.ts
```

#### Configs / Key Integrations
```
src/lib/**/*.ts
src/providers/**/*.ts
src/adapters/**/*.ts
```

### Grouping Files into Features

After collecting file paths, group them into **Features** by:

1. **Route segment** — `/app/payment/` → Feature: "Payment"
2. **Module folder name** — `services/OrderService.ts` → Feature: "Order"
3. **Controller/Handler prefix** — `AuthController` → Feature: "Auth"

If a feature name is ambiguous, use the folder name as-is.

Merge files with the same feature name into one feature row.

### Detecting Existing Tests

For each feature, check if test files already exist:
```
**/__tests__/**
**/*.test.ts
**/*.test.tsx
**/*.spec.ts
**/*.spec.tsx
```

If tests exist → mark `has_tests: true` → apply Likelihood modifier −1.

---

## Step 1b: AI Test Context Discovery (for Top-Risk Features)

For features classified as 🔴 Critical or 🟠 High, perform in-depth code scanning to extract context needed for AI test generation:

### 1. Existing Test Style Discovery (Pattern Blueprint)
Scan for existing test files across the project to provide AI with a concrete pattern to mimic:
- Find any existing test file (e.g. `src/**/*.test.ts`)
- Inspect:
  - **Runner & Imports:** `import { describe, it, expect, vi } from 'vitest'` vs `jest`
  - **Structure:** `describe('Feature', () => { it('should...', () => { ... }) })`
  - **Mocking style:** `vi.mock()` vs `jest.mock()` vs dependency injection
  - **Assertions:** `.toBe()`, `.toEqual()`, `.toMatchObject()`
- Record 1 representative test file path as `style_reference_test`.

### 2. Imports & Type Extraction
Inspect the feature's source files for:
- **Validation Schemas:** `zod` (`z.object`, `z.string()`), `joi`, `yup`, `class-validator`
- **Interfaces & Types:** `interface`, `type`, DTOs, request/response models
- Record the schema/type file paths.

### 3. Mock Candidates Discovery
Analyze imports to separate dependencies:
- **Mock Targets:** External APIs (`stripe`, `twilio`), ORM/DB clients (`prisma`, `drizzle`, `typeorm`), HTTP clients (`axios`, `fetch`), message brokers (`bullmq`, `redis`).
- **Do NOT Mock:** Pure utility functions, domain models, validation schemas, error classes.

### 4. Condition & Boundary Extraction (via swe-test-engineer)
Scan target feature logic and schemas for concrete values using [`swe-test-engineer`](../../swe-test-engineer/SKILL.md) rules:
- **BVA (Numeric / Date / Time):** Grep for `<`, `<=`, `>`, `>=`, `.min(`, `.max(`. Extract exact numbers (e.g., `amount: 1..50000` → boundaries `[0, 1, 2, 49999, 50000, 50001]`).
- **EP (Enums / Formats / Strings):** Grep for `enum `, `z.enum(`, union literals `'a' | 'b'`, regex validators. Extract valid partitions and invalid partitions (empty, invalid format, null/undefined).
- **STT (Workflow & Status):** Grep for status fields (`status: 'pending' | 'paid' | 'failed'`), state machine configurations, switch/case transitions. Extract valid and forbidden transitions.

---

## Step 2: Likelihood Signals

### Git Command

```bash
git log --since="90 days ago" --pretty=format: --name-only | grep -v "^$" | sort | uniq -c | sort -rn | head -50
```

To scope commits to a specific feature's files:
```bash
git log --since="90 days ago" --pretty=format: --name-only -- "src/services/payment*" | grep -v "^$" | wc -l
```

Use the total commit count per feature's file set as the primary Likelihood input.
See [scoring.md](scoring.md) for the count-to-score mapping.

### Recent Bug Fix Detection

```bash
git log --since="90 days ago" --oneline | grep -iE "(fix|hotfix|patch|bug)" 
```

Then check if the commit touches any of the feature's files.
If yes → apply Likelihood +1 modifier.

### New Feature Detection

```bash
git log --diff-filter=A --since="30 days ago" --pretty=format: --name-only | sort -u
```

Files added in the last 30 days with no corresponding test file → Likelihood +1.

### External Dependency Detection

Grep feature files for common external call patterns:
```bash
grep -rE "(fetch\(|axios\.|got\.|http\.|https\.|sdk\.|client\.)" <feature_files>
grep -rE "(stripe|twilio|sendgrid|firebase|supabase|prisma|sequelize)" <feature_files>
```

If found → Likelihood +1.

### Async / Queue / Job Detection

```bash
grep -rE "(setTimeout|setInterval|cron|queue|job|worker|async|await|Promise\.all)" <feature_files>
```

If found → Likelihood +1.

---

## Fallback: No Git Available

When `git log` fails (not a git repo, no history, shallow clone):

1. Set all Likelihood scores to **3** (Medium) as baseline
2. Apply file-pattern modifiers only (external deps, async, new files without tests)
3. Print warning at top of output:

```
⚠️  Git unavailable — Likelihood scores are heuristic estimates only.
    For more accurate results, run in a repository with git history.
```

---

## Framework Detection

Detect the framework early to pick the right folder patterns:

| Signal File | Framework |
|-------------|-----------|
| `next.config.js` / `next.config.ts` | Next.js |
| `nuxt.config.ts` | Nuxt.js |
| `vite.config.ts` + `src/views/` | Vue + Vite |
| `angular.json` | Angular |
| `package.json` with `"react-native"` | React Native |
| `*.py` + `views.py` / `urls.py` | Django |
| `*.java` + `@RestController` | Spring Boot |
| `go.mod` | Go |

If framework is unknown, use generic patterns:
```
src/**/*.ts  src/**/*.js  app/**/*.ts  lib/**/*.ts
```
