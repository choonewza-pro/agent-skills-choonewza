# 🧠 Agent Skills Repository

```bash
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza
```

> ชุดคู่มือ `SKILL.md` สำหรับ AI agent ที่ทำงานร่วมกับโค้ดและกระบวนการพัฒนาในโปรเจกต์ Next.js  
> ออกแบบมาให้ agent อ่านแล้วปฏิบัติตามได้ทันที — ไม่ต้องเดา ไม่ต้องถาม

---

## 📦 Skills ทั้งหมด

โฟลเดอร์ `skills/` มี **19 skills** แบ่งตามหมวดหมู่:

### 🔧 Development & Deployment

| Skill                                                                                      | คำอธิบาย                                                                                                                                                                                        |
| :----------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`new-deploy-docker-production`](skills/new-deploy-docker-production/SKILL.md)             | Build และ run Docker image สำหรับ Next.js app แบบ local — ตรวจชื่อ image/container, เลือก env file, เช็ค Docker daemon, ตรวจ tag ซ้ำก่อนรัน                                                     |
| [`new-git-commit-guide`](skills/new-git-commit-guide/SKILL.md)                             | คู่มือเขียน commit message ตาม conventional commit — กำหนด type, scope, checklist ก่อน commit, ข้อควรระวังเรื่อง `.env` และ `--no-verify`                                                       |
| [`new-project-onboarding`](skills/new-project-onboarding/SKILL.md)                         | คู่มือ onboarding สำหรับ developer ใหม่ — ตั้งค่าเบื้องต้น, stack, คำสั่งพื้นฐาน, ตรวจ `package.json` / `.env.example` / `Dockerfile`                                                           |
| [`new-typescript-guidelines`](skills/new-typescript-guidelines/SKILL.md)                   | คู่มือแนวทางปฏิบัติ (Coding Conventions) สำหรับ TypeScript และสถาปัตยกรรม App Router ของโปรเจกต์ KruChiron                                                                                      |
| [`new-readme-engineering`](skills/new-readme-engineering/SKILL.md)                         | คู่มือการเขียนและดูแลรักษา README.md สำหรับโปรเจกต์ซอฟต์แวร์ — เน้นการตรวจสอบโค้ดเบสก่อนเขียน ความถูกต้องของข้อมูล และโครงสารสนเทศที่มืออาชีพใช้                                               |
| [`new-prevent-encoding-mojibake`](skills/new-prevent-encoding-mojibake/SKILL.md)           | ป้องกันปัญหา encoding ภาษาไทย/อักขระหลายไบต์ (Mojibake) ใน Windows — แนวทางเขียนไฟล์, ตรวจจับ, และกู้คืนข้อความภาษาไทยที่เสียหาย                                                                |
| [`new-small-model-prompt-architect`](skills/new-small-model-prompt-architect/SKILL.md)     | วิเคราะห์ ปรับแต่ง และสร้าง System Prompt สำหรับโมเดลขนาดเล็ก (Gemma, Qwen, Llama, Phi, Mistral, DeepSeek) ที่รันบน Ollama — ออกแบบโครงสร้าง prompt เพื่อประสิทธิภาพสูงสุดกับข้อจำกัดของโมเดลเล็ก|
| [`swe-enterprise-wallet-architecture`](skills/swe-enterprise-wallet-architecture/SKILL.md) | ออกแบบและตรวจสอบระบบกระเป๋าเงินดิจิทัล (Digital Wallet) และระบบหักเครดิตที่มี Concurrency สูง ด้วยหลักการ Double-Entry Bookkeeping, Saga Pattern, Distributed Locking, และ Idempotency Controls|
| [`swe-mcp-server-development`](skills/swe-mcp-server-development/SKILL.md)                 | แนะนำการสร้าง MCP Server ที่เปิดเผย tools, resources และ prompts ให้กับ LLM application — ครอบคลุมการออกแบบ, การพัฒนา, และการทดสอบ MCP Server                                                  |
| [`swe-review-architecture-project`](skills/swe-review-architecture-project/SKILL.md)       | รีวิว Tech Stack, Dependencies, และโครงสร้างโปรเจคอย่างละเอียด พร้อม Version matrix และคำแนะนำด้าน security/compatibility                                                                      |
| [`swe-codebase-reverse-engineering`](skills/swe-codebase-reverse-engineering/SKILL.md)     | วิเคราะห์และแกะสถาปัตยกรรมระบบ/โค้ดเบสเดิม (Reverse Engineering) ออกมาเป็นเอกสาร docs/ (Architecture, API, DB, Logic, Auth, Edge Cases) จากโค้ดจริง                                          |
| [`swe-legacy-system-migration`](skills/swe-legacy-system-migration/SKILL.md)               | แนวทางการ Migrate/Rewrite ระบบเดิมไปสู่ภาษาหรือสแต็กใหม่แบบเป็นขั้นตอน (Reverse Engineer → Spec → Clean Scaffold → Incremental Migration → Automated Verification)                             |

### 🐛 Debugging & Code Review

| Skill                                                  | คำอธิบาย                                                                                                                                                |
| :----------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [`swe-debug-mantra`](skills/swe-debug-mantra/SKILL.md) | 4-step debugging discipline — reproduce, trace fail path, falsify hypothesis, cross-reference breadcrumbs — ต้อง recite mantra ก่อนเริ่ม debug ทุกครั้ง |
| [`swe-scrutinize`](skills/swe-scrutinize/SKILL.md)     | การรีวิวแผนงาน, PR หรือโค้ดเชิงลึกในฐานะคนนอก (Outsider-perspective) — ทบทวนความจำเป็น ความซับซ้อน และแกะเส้นทางการทำงานของโค้ดจนสุดทาง                 |
| [`swe-post-mortem`](skills/swe-post-mortem/SKILL.md)   | การเขียนบันทึกประวัติการแก้ไขบั๊กเชิงลึกสำหรับโปรแกรมเมอร์ (Root Cause Analysis - RCA) — อธิบายสาเหตุ กลไก วิธีแก้ไข และวิธีการป้องกัน                  |

### 📝 Reporting & Communication

| Skill                                                                                | คำอธิบาย                                                                                                                            |
| :----------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| [`new-implementation-docs-guide`](skills/new-implementation-docs-guide/SKILL.md)     | บันทึกผลการพัฒนาและสถานะ (DONE/IN PROGRESS/BLOCKED) ต่อท้ายไฟล์เอกสารแผนงาน (`_IMPLEMENTATION_PLANS/` หรือ spec) โดยคง requirement เดิมไว้ |
| [`swe-reports-guide`](skills/swe-reports-guide/SKILL.md)                             | แนวทางเขียน feature/bug fix report สำหรับผู้บริหาร — กำหนดโครงสร้างโฟลเดอร์ `docs/dev-reports/` และมาตรฐานการเขียน                  |
| [`swe-management-talk`](skills/swe-management-talk/SKILL.md)                         | แปลงเนื้อหา engineer-to-engineer เป็นภาษาสำหรับผู้บริหาร — รองรับหลาย channel: JIRA, Slack, standup, email, meeting talking-points  |

### 🧪 QA & Testing

| Skill                                                    | คำอธิบาย                                                                                                                                 |
| :------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------- |
| [`swe-test-engineer`](skills/swe-test-engineer/SKILL.md) | วิเคราะห์ requirements และสร้าง test cases ด้วย Boundary Value Analysis (BVA), Equivalence Partitioning (EP) และเทคนิคอื่นๆ — รองรับทั้ง business users และ technical users |

---

## 💡 เงื่อนไขการเรียกใช้งาน (Triggers & Usage)

AI Agent จะดึงคู่มือ (Skills) เหล่านี้ไปทำงานโดยอัตโนมัติเมื่อเจอกลุ่มคำสั่ง คีย์เวิร์ด หรือสถานการณ์ดังต่อไปนี้:

### 🔧 Development & Deployment

- **`new-deploy-docker-production`**
  - **ทำงานเมื่อ:** ต้องการ build Docker image หรือ run container บนเครื่อง local อย่างปลอดภัยโดยอ้างอิงชื่อโปรเจกต์จาก repo จริง
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วย build docker หน่อย"_
    - _"รัน container ของโปรเจกต์นี้ด้วย docker-compose/docker file"_
    - _"deploy production ในเครื่อง local ยังไง"_
- **`new-git-commit-guide`**
  - **ทำงานเมื่อ:** กำลังจะบันทึกโค้ด (commit) หรือต้องการให้เขียน commit message
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"commit ให้หน่อย"_, _"ช่วยเขียน commit message"_
    - _"git commit"_
    - _"ต้องใส่ scope อะไรตอน commit"_
- **`new-project-onboarding`**
  - **ทำงานเมื่อ:** มีนักพัฒนาคนใหม่เข้ามาเริ่มต้นใช้งานโปรเจกต์
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"โปรเจกต์นี้ตั้งค่าอย่างไร"_, _"เริ่มรันแอปยังไง"_
    - _"โปรเจกต์นี้ใช้ stack อะไรบ้าง"_
    - _"ขอวิธี setup ตัวฐานข้อมูล/docker สำหรับคนใหม่"_
- **`new-typescript-guidelines`**
  - **ทำงานเมื่อ:** มีข้อสงสัยหรือต้องการปรับแต่งแนวทางเขียนโค้ด TypeScript โครงสร้างโฟลเดอร์ และข้อกำหนดของ App Router
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"แนวทางการเขียน TypeScript ในโปรเจกต์นี้เป็นอย่างไร"_
    - _"โฟลเดอร์เก็บโค้ดตรงนี้ควรใช้แบบไหนตามไกด์ไลน์"_
    - _"ช่วยตรวจสอบไวยากรณ์หรือจัดโครงสร้างไฟล์ตาม typescript-guidelines"_
- **`new-readme-engineering`**
  - **ทำงานเมื่อ:** ต้องการเขียน, รีวิว, หรือปรับปรุง README.md สำหรับโปรเจกต์ซอฟต์แวร์ใดๆ
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยอัปเดต README ของฟีเจอร์นี้หน่อย"_
    - _"สร้าง README.md ให้โปรเจกต์นี้"_
    - _"review my README"_
    - _"clean up my documentation"_
- **`new-prevent-encoding-mojibake`**
  - **ทำงานเมื่อ:** ต้องเขียนไฟล์ที่มีภาษาไทยหรืออักขระหลายไบต์ใน Windows, หรือพบข้อความภาษาไทยแสดงผลเป็นอักษรประหลาด (Mojibake)
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ไทยในไฟล์เป็นตัวอักษรแปลกๆ"_, _"โมจิเบค是什麼"_
    - _"encoding ภาษาไทยพัง"_
    - _"เขียนไฟล์ภาษาไทยแล้วอ่านไม่ได้"_
    - _"ช่วยกู้คืนไฟล์ภาษาไทยที่เสีย"_
    - พิมพ์คำสั่ง `/prevent-encoding-mojibake`
- **`new-small-model-prompt-architect`**
  - **ทำงานเมื่อ:** ต้องการออกแบบหรือปรับแต่ง System Prompt สำหรับโมเดลภาษาเล็กที่รันด้วย Ollama
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยเขียน system prompt ให้โมเดลเล็ก"_
    - _"ปรับ prompt ให้เหมาะกับ Gemma/Qwen/Llama"_
    - _"ทำ prompt architecting"_
- **`swe-enterprise-wallet-architecture`**
  - **ทำงานเมื่อ:** ต้องการออกแบบ ตรวจสอบ หรือพัฒนาสถาปัตยกรรมระบบ Wallet, ระบบการเงิน, การป้องกันการหักเงินซ้ำ หรือการล็อกข้อมูลที่มีการทำงานพร้อมกันสูง
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ออกแบบระบบกระเป๋าเงิน (digital wallet)"_, _"ทำระบบหักเงิน/เครดิต"_
    - _"เลือกวิธีกดล็อกฐานข้อมูล (pessimistic vs optimistic vs atomic update)"_
    - _"ทำระบบป้องกันเงินติดลบ/หักเงินซ้ำซ้อน (double spending)"_
    - _"สถาปัตยกรรมบัญชีแยกประเภทคู่ (double-entry ledger)"_
    - _"ทำ Saga หรือ Transactional Outbox"_
- **`swe-mcp-server-development`**
  - **ทำงานเมื่อ:** ต้องการพัฒนา, ออกแบบ, หรือแก้ไข MCP Server สำหรับเชื่อมต่อกับ LLM application
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยสร้าง MCP server"_
    - _"ออกแบบ tool/resource/prompt สำหรับ MCP"_
    - _"วิธีทำ Model Context Protocol server"_
- **`swe-review-architecture-project`**
  - **ทำงานเมื่อ:** ต้องการรีวิว Tech Stack, Dependencies, สถาปัตยกรรม และโครงสร้างโปรเจกต์
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"รีวิวสถาปัตยกรรมโปรเจกต์นี้ให้หน่อย"_
    - _"ตรวจ version matrix และ dependency compatibility"_
- **`swe-codebase-reverse-engineering`**
  - **ทำงานเมื่อ:** ต้องการทำความเข้าใจ แกะระบบ หรือจัดทำเอกสารข้อกำหนดของโค้ดเบสเดิม/ระบบเก่าก่อนทำการ refactor หรือปรับปรุงใหญ่
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยแกะระบบโปรเจกต์นี้หน่อย"_, _"reverse engineer โค้ดเบสเก่า"_
    - _"document how this system actually works"_
    - _"ทำเอกสารระบบจากโค้ดเดิมก่อนเริ่มงาน"_
- **`swe-legacy-system-migration`**
  - **ทำงานเมื่อ:** ต้องการ Rewrite, ย้ายระบบ (Migrate), หรือ Port โค้ดเบสเดิมไปยังเทคโนโลยีใหม่โดยลดความเสี่ยงพฤติกรรมเพี้ยน
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ย้ายระบบนี้ไป Next.js/Go/Rust"_, _"migrate legacy codebase"_
    - _"rewrite ระบบเดิมเป็น stack ใหม่"_
    - _"วางแผนการย้ายระบบเก่า (system migration)"_

### 🐛 Debugging & Code Review

- **`swe-debug-mantra`**
  - **ทำงานเมื่อ:** เกิดข้อผิดพลาดในระบบ, พบบั๊ก หรือโปรแกรมแครช
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"มีบั๊กตรงนี้ช่วยดูหน่อย"_, _"โค้ดรันไม่ผ่านขึ้น error แบบนี้..."_
    - พิมพ์คำสั่ง `/swe-debug-mantra` หรือ `/debug-mantra`
    - วาง Stack trace หรือ error log ลงในแชท
- **`swe-scrutinize`**
  - **ทำงานเมื่อ:** ต้องการตรวจสอบโค้ด รีวิวสถาปัตยกรรม หรือตรวจสอบแผนงานเชิงลึก
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยรีวิว PR/โค้ดส่วนนี้ให้หน่อย"_, _"ขอความเห็นที่สอง (second opinion) เรื่องการออกแบบนี้"_
    - พิมพ์คำสั่ง `/swe-scrutinize` หรือ `/scrutinize`
    - _"ตรวจแผนการพัฒนาตัวนี้ว่าสมเหตุสมผลไหม"_
- **`swe-post-mortem`**
  - **ทำงานเมื่อ:** แก้ไขบั๊กเสร็จแล้วและต้องการบันทึกประวัติการแก้ไขเชิงลึก (RCA) ก่อนจะปิดงาน
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยเขียน post-mortem สำหรับบั๊กนี้หน่อย"_, _"ทำสรุป RCA ของปัญหาที่เพิ่งแก้เสร็จ"_
    - พิมพ์คำสั่ง `/swe-post-mortem` หรือ `/post-mortem`
    - _"เขียนสรุปสาเหตุรากเหง้า (root cause) และการป้องกันปัญหาในอนาคต"_

### 📝 Reporting & Communication

- **`new-implementation-docs-guide`**
  - **ทำงานเมื่อ:** ทำงานตามแผนงานหรือเอกสารสเปกเสร็จสิ้น (phase/feature) และต้องการบันทึกสถานะกลับไปยังเอกสารแผนงาน
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"update phase doc"_, _"mark as done"_
    - _"ปิด phase"_, _"อัปเดตเอกสารการพัฒนา"_
    - _"บันทึกสถานะลง implementation plan"_
- **`swe-reports-guide`**
  - **ทำงานเมื่อ:** ต้องจัดทำเอกสารสรุปความคืบหน้าการพัฒนาหรือการแก้ไขบั๊กเพื่อรายงานต่อหัวหน้างาน
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยเขียนสรุปฟีเจอร์นี้ส่งให้หัวหน้าหน่อย"_, _"ขอรายงานการแก้บั๊ก (bug fix report)"_
    - _"สร้างเอกสารสถานะการพัฒนาล่าสุด"_
- **`swe-management-talk`**
  - **ทำงานเมื่อ:** ต้องการเปลี่ยนภาษาเทคนิค (Engineering jargon) ให้กลายเป็นภาษาระดับผู้บริหารตามช่องทางต่างๆ
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"เขียนสรุปสำหรับส่งเข้า Slack/Email ผู้บริหาร"_, _"ช่วยแก้ภาษาในโพสต์นี้ให้เป็นทางการและเหมาะกับ PM"_
    - _"ขอเนื้อหาอัปเดตสั้นๆ สำหรับประชุม standup"_
    - _"ช่วยทำสรุปแบบ executive summary"_

### 🧪 QA & Testing

- **`swe-test-engineer`**
  - **ทำงานเมื่อ:** ต้องการวิเคราะห์ requirements และสร้าง test cases อย่างเป็นระบบ
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยเขียน test cases ให้"_
    - _"ทำ Boundary Value Analysis"_
    - _"สร้าง test cases จาก requirement"_
    - _"ออกแบบชุดทดสอบสำหรับฟีเจอร์นี้"_

---

## 📂 โครงสร้างโปรเจกต์

```
agent-skills-choonewza/
├── README.md
├── .agent/
│   └── skills/
│       └── git-commit-guide/      # skill ที่ใช้ภายใน repo นี้เอง
├── docs/
│   ├── baseline-mcp-server.txt
│   └── claude_agent_skills_guide.md
├── opencode.json
└── skills/
    ├── new-deploy-docker-production/
    │   └── SKILL.md
    ├── new-git-commit-guide/
    │   └── SKILL.md
    ├── new-implementation-docs-guide/
    │   └── SKILL.md
    ├── new-prevent-encoding-mojibake/
    │   └── SKILL.md
    ├── new-project-onboarding/
    │   ├── SKILL.md
    │   ├── assets/templates/
    │   ├── evals/
    │   ├── references/
    │   └── scripts/
    ├── new-readme-engineering/
    │   └── SKILL.md
    ├── new-small-model-prompt-architect/
    │   └── SKILL.md
    ├── new-typescript-guidelines/
    │   └── SKILL.md
    ├── swe-codebase-reverse-engineering/
    │   └── SKILL.md
    ├── swe-debug-mantra/
    │   └── SKILL.md
    ├── swe-enterprise-wallet-architecture/
    │   ├── README.md
    │   ├── SKILL.md
    │   └── references/
    ├── swe-legacy-system-migration/
    │   └── SKILL.md
    ├── swe-management-talk/
    │   └── SKILL.md
    ├── swe-mcp-server-development/
    │   └── SKILL.md
    ├── swe-post-mortem/
    │   └── SKILL.md
    ├── swe-reports-guide/
    │   └── SKILL.md
    ├── swe-review-architecture-project/
    │   ├── README.md
    │   └── SKILL.md
    ├── swe-scrutinize/
    │   └── SKILL.md
    └── swe-test-engineer/
        └── SKILL.md
```

---

## 🚀 วิธีติดตั้ง

เพิ่ม skill ทั้งหมดหรือเลือกเฉพาะที่ต้องการลงในโปรเจกต์:

```bash
# ติดตั้งทั้งหมด
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza

# หรือดูรายชื่อ skill ก่อนเลือกติดตั้ง
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --list

# ตัวอย่างเลือกติดตั้งเฉพาะบาง skill
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-git-commit-guide
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-implementation-docs-guide
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-project-onboarding
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-deploy-docker-production
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-typescript-guidelines
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-readme-engineering
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-prevent-encoding-mojibake
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-small-model-prompt-architect
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-codebase-reverse-engineering
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-legacy-system-migration
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-debug-mantra
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-scrutinize
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-post-mortem
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-reports-guide
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-management-talk
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-enterprise-wallet-architecture
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-mcp-server-development
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-review-architecture-project
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-test-engineer
```

หรือ copy โฟลเดอร์ skill ที่ต้องการไปไว้ใน `.agent/skills/` ของโปรเจกต์เป้าหมายโดยตรง

---

## 🎯 จุดประสงค์ของ repo นี้

- เก็บคำแนะนำการทำงานแบบ **agent-friendly** — agent อ่านแล้วทำตามได้เลย
- ช่วย developer รู้วิธี deploy, commit, debug, รีวิวโค้ด, เขียนรายงาน และ onboard เข้าโปรเจกต์ได้ง่ายขึ้น
- เป็น repository สำหรับฝึกเขียน **skill-based documentation**

---

## 📄 License

MIT
