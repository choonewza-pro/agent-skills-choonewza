# Writing Tests with AI: Context Extraction & Specific Prompt Guide

Used by `swe-test-planner` to gather code context, extract concrete test values via `swe-test-engineer`, and produce high-quality test prompts for AI test generators or test writers.

---

## 1. Core Mindset: Writing Tests with AI

AI Coding Assistants ช่วยเขียน Test ได้รวดเร็วมาก แต่คุณภาพของ Test จะขึ้นอยู่กับ **Context** และ **Prompt** ที่เราป้อนให้อย่างมีนัยสำคัญ

```
┌─────────────────────────────────────────────────────────────┐
│ ❌ Vague Prompt         → ❌ Vague Tests (Generic, Flaky)    │
│ ✅ Good Context +       → ✅ Useful Tests (Contract-bound,   │
│    Specific Prompt          Production-ready)               │
└─────────────────────────────────────────────────────────────┘
```

### กฎทอง 2 ข้อ:
1. **AI ควรเป็น "ผู้ช่วยร่าง Test" (Drafting Assistant) ไม่ใช่ผู้ตัดสินว่าระบบถูกต้องแล้ว:**
   AI ไม่รู้ Business Context ที่แท้จริงหากเราไม่บอก มันจะพยายามเขียน Test ให้ Pass ตาม Code ที่เห็น ซึ่งอาจกลายเป็นการ "ล็อก Bug ให้อยู่ใน Test" หากโค้ดเดิมมีข้อผิดพลาด
2. **AI เก่งเรื่อง Pattern Matching แต่ต้องมี Pattern ให้เห็นก่อน:**
   หากไม่ป้อนไฟล์ Test ตัวอย่างในโปรเจกต์ AI จะสุ่ม Style ขึ้นมาเอง (เช่น สลับไปใช้ Jest ในโปรเจกต์ Vitest, ใช้ Assertions แปลกๆ, หรือตั้งชื่อ Test ไม่ตรงกับทีม)

---

## 2. The 4 Essential Context Elements (ค้นหาใน Code)

ก่อนจะให้ AI หรือ Engineer เขียน Test สำหรับฟีเจอร์ใดๆ `swe-test-planner` ต้องสแกนโค้ดเพื่อเตรียมข้อมูลอย่างน้อย 4 ด้านนี้:

```
                  ┌──────────────────────────────┐
                  │   AI Test Context Package    │
                  └──────────────┬───────────────┘
         ┌───────────────────────┼───────────────────────┐
         ▼                       ▼                       ▼
┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐
│  1. Source Code  │    │ 2. Imports/Types │    │ 3. Style Pattern │    │ 4. Mock Targets  │
│  Target File     │    │ Interfaces, DTOs │    │ Existing Test    │    │ DB, External API │
│  Functions & API │    │ Schemas (Zod/Joi)│    │ Runner & Syntax  │    │ What NOT to mock │
└──────────────────┘    └──────────────────┘    └──────────────────┘    └──────────────────┘
```

| Context Element | สิ่งที่ต้องสแกนหาใน Codebase | เหตุผลและความสำคัญ |
|---|---|---|
| **1. Source Code ที่จะทดสอบ** | • File path ที่ชัดเจน (เช่น `src/services/order.ts`)<br>• ชื่อ Function / Method / Class<br>• Parameter types และ Return type | เพื่อกำหนดขอบเขตและ Contract ชัดเจน ไม่ให้ AI ทดสอบออกนอกเรื่อง |
| **2. Imports และ Type ที่เกี่ยวข้อง** | • Schema validation (Zod, Joi, Yup, Class-validator)<br>• TypeScript interfaces, types, DTOs<br>• Enum definitions และ error classes | ให้ AI สร้าง Mock Data และ Assertion ที่ Type-safe 100% |
| **3. Test เดิมใน Project (Style Pattern)** | • ค้นหาไฟล์ test ที่มีอยู่แล้ว (เช่น `src/**/__tests__/*.test.ts`)<br>• ระบุ Test Runner (Vitest, Jest, Playwright)<br>• โครงสร้าง Test (Describe/it, AAA layout, `it.each`)<br>• สไตล์การตั้งชื่อและการ Mock | **สำคัญมาก:** AI เลียนแบบสไตล์เดิมได้ดีเยี่ยมเมื่อเห็นตัวอย่าง ทำให้โค้ดกลมกลืนกับทั้งโปรเจกต์ |
| **4. Dependency ที่ต้อง Mock** | • External APIs (Stripe, Twilio, SendGrid, S3)<br>• Database client (Prisma, Drizzle, TypeORM, Mongoose)<br>• Queue, Cache (Redis), Timers | ระบุชัดเจนว่าชิ้นส่วนไหนต้อง Mock และ**ชิ้นส่วนไหนห้าม Mock** (เช่น Domain Entity, Pure Utils) |

---

## 3. Condition & Value Extraction via `swe-test-engineer`

เพื่อไม่ให้ Prompt มีแค่คำสั่งลอยๆ `swe-test-planner` จะสแกนโค้ดและใช้หลักการจาก [`swe-test-engineer`](../../swe-test-engineer/SKILL.md) ในการขุดหา **ค่าตัวเลข, ข้อความ, เงื่อนไข และสถานะจริง** ในโค้ด:

### A. Boundary Value Analysis (BVA) สำหรับตัวเลข / เวลา
สแกนหาตัวแปรที่มีการเปรียบเทียบเงื่อนไข (`<`, `<=`, `>`, `>=`, `between`, `.min()`, `.max()`):
- **สกัดค่า:** Minimum, Maximum, Precision step (จำนวนเต็ม, ทศนิยม, วันที่)
- **สร้างขอบเขต:** `[min-1, min, min+1]`, `[max-1, max, max+1]`
- *ตัวอย่าง:* โค้ดมี `if (amount < 1 || amount > 50000)` → ค่า BVA คือ `0, 1, 2, 49999, 50000, 50001`

### B. Equivalence Partitioning (EP) สำหรับ Non-numeric & Enums
สแกนหา Enums, Union Types, Dropdown values, และ Formats:
- **Valid Partitions:** ค่าที่อนุญาตทั้งหมด (เช่น role: `'admin' | 'member' | 'guest'`)
- **Invalid Partitions:** ค่าที่ระบบต้องปฏิเสธ (เช่น empty string `""`, invalid email format, null/undefined, unauthorized role)

### C. State Transition Testing (STT) สำหรับ Status & Workflows
สแกนหา Status fields, State machines, และ Transition guards:
- **Valid Transitions:** สถานะที่ขยับต่อได้ (เช่น `pending → processing → paid`)
- **Invalid Transitions:** สถานะที่ห้ามกระโดดข้าม (เช่น `paid → pending`, `cancelled → paid`)

---

## 4. Specific AI Prompt Template

เมื่อวางแผนเสร็จ สกิลจะผลิต Prompt ในรูปแบบที่พร้อมส่งต่อให้ AI Assistant, `swe-test-engineer`, หรือ `swe-test-unit-test-writer`:

```markdown
### 🤖 Specific AI Test Prompt: [Feature Name] - [Function/Target]

**Context Assets to Provide:**
- 📄 Source: `[path/to/target.ts]`
- 📑 Types & Schemas: `[path/to/types.ts]`
- 🧪 Style Reference Test: `[path/to/existing.test.ts]` (use as convention blueprint)
- 🔌 Dependencies to Mock: `[e.g., prisma.user, stripeClient]`
- 🚫 Dependencies NOT to Mock: `[e.g., hashPassword util, Domain validators]`

**Ready-to-Use Prompt:**
> Write comprehensive unit tests for `[functionName()]` in `[path/to/target.ts]`:
> 
> 1. **Target:** `[functionName(param1, param2)]`
> 2. **Happy Path:**
>    - [Case 1: e.g. Valid payload returns 201 with created entity]
>    - [Case 2: e.g. Existing user updates profile successfully]
> 3. **Negative & Error Cases:**
>    - [Case 3: e.g. Missing required field 'name' throws ValidationError]
>    - [Case 4: e.g. Duplicate email throws DuplicateResourceError]
> 4. **Edge Cases (Derived via swe-test-engineer):**
>    - [BVA Case: e.g. Quantity boundary: 0 (fail), 1 (pass), 999 (pass), 1000 (fail)]
>    - [EP Case: e.g. Invalid email partition 'plainaddress', '@missingusername.com']
>    - [STT Case: e.g. Attempt transition from 'cancelled' to 'paid' fails]
> 5. **Behaviors to Verify (Side Effects & Contracts):**
>    - [Verify behavior 1: e.g. Verify password is hashed via bcrypt before calling db.save]
>    - [Verify behavior 2: e.g. Verify audit log event is dispatched with correct payload]
> 6. **Constraints & Guardrails (สิ่งที่ห้ามทำ):**
>    - Do NOT test private/internal variables or intermediate state
>    - Do NOT mock `hashPassword` helper — use the real function
>    - Follow Arrange-Act-Assert (AAA) pattern
>    - Mimic file structure, naming conventions, and mock syntax from `[path/to/existing.test.ts]`
```

---

## 5. Comparison: Vague vs Specific Prompt

### ❌ Vague Prompt (ไม่ควรใช้):
```
"Write tests for userService.js"
```
**ปัญหาที่เกิดขึ้น:**
- AI สุ่มตั้งชื่อ Test ตามใจ
- AI อาจลืม Test Negative Case สำคัญ
- AI อาจ Mock ทุกอย่างจนกลายเป็นไม่ได้ทดสอบอะไรจริง
- AI ไม่รู้ว่าโปรเจกต์ใช้ Vitest หรือ Jest

### ✅ Specific Prompt (สิ่งที่ Planner ต้องสร้าง):
```
"Write tests for createUser() in src/services/userService.ts:
- Happy path: valid user payload creates record and returns user DTO without password.
- Negative cases: missing email, invalid email format, duplicate email (throws ConflictError).
- Edge cases: name length at boundary 1 char (pass), 50 chars (pass), 51 chars (fail).
- Behavior to verify: verify password is hashed with bcrypt before storing, verify welcome email event is queued.
- Constraints: Don't mock validation schemas, follow existing test style in src/services/__tests__/authService.test.ts."
```

---

## 6. Checklist: Pre-Flight AI Test Generation

ก่อนส่งต่อให้ AI เขียน Test ตรวจสอบว่ามีครบทุกข้อ:
- [ ] มี Source code target พร้อม function signature
- [ ] มี Type / Schema ให้ AI อ้างอิง
- [ ] มีตัวอย่าง Test เดิมอย่างน้อย 1 ไฟล์ในโปรเจกต์ให้ AI ก๊อปปี้ Style
- [ ] แยกแยะชัดเจนว่าอะไรต้อง Mock และอะไรห้าม Mock
- [ ] ระบุครบ 6 องค์ประกอบ (Target, Happy, Negative, Edge, Verify Behavior, สิ่งที่ห้ามทำ)
