# 🧠 Agent Skills Repository

> ชุดคู่มือ `SKILL.md` สำหรับ AI agent ที่ทำงานร่วมกับโค้ดและกระบวนการพัฒนาในโปรเจกต์ Next.js  
> ออกแบบมาให้ agent อ่านแล้วปฏิบัติตามได้ทันที — ไม่ต้องเดา ไม่ต้องถาม

---

## 📦 Skills ทั้งหมด

โฟลเดอร์ `skills/` มี **11 skills** แบ่งตามหมวดหมู่:

### 🔧 Development & Deployment

| Skill                                                                                      | คำอธิบาย                                                                                                                                                                                        |
| :----------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`new-deploy-docker-production`](skills/new-deploy-docker-production/SKILL.md)             | Build และ run Docker image สำหรับ Next.js app แบบ local — ตรวจชื่อ image/container, เลือก env file, เช็ค Docker daemon, ตรวจ tag ซ้ำก่อนรัน                                                     |
| [`new-git-commit-guide`](skills/new-git-commit-guide/SKILL.md)                             | คู่มือเขียน commit message ตาม conventional commit — กำหนด type, scope, checklist ก่อน commit, ข้อควรระวังเรื่อง `.env` และ `--no-verify`                                                       |
| [`new-project-onboarding`](skills/new-project-onboarding/SKILL.md)                         | คู่มือ onboarding สำหรับ developer ใหม่ — ตั้งค่าเบื้องต้น, stack, คำสั่งพื้นฐาน, ตรวจ `package.json` / `.env.example` / `Dockerfile`                                                           |
| [`new-typescript-guidelines`](skills/new-typescript-guidelines/SKILL.md)                   | คู่มือแนวทางปฏิบัติ (Coding Conventions) สำหรับ TypeScript และสถาปัตยกรรม App Router ของโปรเจกต์ KruChiron                                                                                      |
| [`new-readme-maintenance`](skills/new-readme-maintenance/SKILL.md)                         | คู่มือการเขียนและดูแลรักษาเอกสารประกอบฟีเจอร์ (README.md) ในโค้ดเบส — กำหนดโครงสร้างเอกสารและการอัปเดตทุกครั้งที่มีการสร้างหรือแก้ไขฟีเจอร์                                                     |
| [`swe-enterprise-wallet-architecture`](skills/swe-enterprise-wallet-architecture/SKILL.md) | ออกแบบและตรวจสอบระบบกระเป๋าเงินดิจิทัล (Digital Wallet) และระบบหักเครดิตที่มี Concurrency สูง ด้วยหลักการ Double-Entry Bookkeeping, Saga Pattern, Distributed Locking, และ Idempotency Controls |
| [`swe-review-architecture-project`](skills/swe-review-architecture-project/SKILL.md)       | รีวิว Tech Stack, Dependencies, และโครงสร้างโปรเจคอย่างละเอียด พร้อม Version matrix และคำแนะนำด้าน security/compatibility                                                                       |

### 🐛 Debugging & Code Review

| Skill                                                  | คำอธิบาย                                                                                                                                                |
| :----------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [`swe-debug-mantra`](skills/swe-debug-mantra/SKILL.md) | 4-step debugging discipline — reproduce, trace fail path, falsify hypothesis, cross-reference breadcrumbs — ต้อง recite mantra ก่อนเริ่ม debug ทุกครั้ง |
| [`swe-scrutinize`](skills/swe-scrutinize/SKILL.md)     | การรีวิวแผนงาน, PR หรือโค้ดเชิงลึกในฐานะคนนอก (Outsider-perspective) — ทบทวนความจำเป็น ความซับซ้อน และแกะเส้นทางการทำงานของโค้ดจนสุดทาง                 |
| [`swe-post-mortem`](skills/swe-post-mortem/SKILL.md)   | การเขียนบันทึกประวัติการแก้ไขบั๊กเชิงลึกสำหรับโปรแกรมเมอร์ (Root Cause Analysis - RCA) — อธิบายสาเหตุ กลไก วิธีแก้ไข และวิธีการป้องกัน                  |

### 📝 Reporting & Communication

| Skill                                                        | คำอธิบาย                                                                                                                           |
| :----------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------- |
| [`swe-reports-guide`](skills/swe-reports-guide/SKILL.md)     | แนวทางเขียน feature/bug fix report สำหรับผู้บริหาร — กำหนดโครงสร้างโฟลเดอร์ `docs/dev-reports/` และมาตรฐานการเขียน                 |
| [`swe-management-talk`](skills/swe-management-talk/SKILL.md) | แปลงเนื้อหา engineer-to-engineer เป็นภาษาสำหรับผู้บริหาร — รองรับหลาย channel: JIRA, Slack, standup, email, meeting talking-points |

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
- **`new-readme-maintenance`**
  - **ทำงานเมื่อ:** มีการสร้าง พัฒนา หรือแก้ไขโมดูลฟีเจอร์ต่างๆ ในโค้ดเบส และจำเป็นต้องอัปเดตหรือสร้าง README.md ของฟีเจอร์นั้นๆ
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ช่วยอัปเดต README ของฟีเจอร์นี้หน่อย"_
    - _"สร้าง README.md ตามไกด์ไลน์"_
    - _"ต้องเขียนอธิบายฟีเจอร์นี้ใน README อย่างไร"_
- **`swe-enterprise-wallet-architecture`**
  - **ทำงานเมื่อ:** ต้องการออกแบบ ตรวจสอบ หรือพัฒนาสถาปัตยกรรมระบบ Wallet, ระบบการเงิน, การป้องกันการหักเงินซ้ำ หรือการล็อกข้อมูลที่มีการทำงานพร้อมกันสูง
  - **คีย์เวิร์ดที่ใช้ถาม:**
    - _"ออกแบบระบบกระเป๋าเงิน (digital wallet)"_, _"ทำระบบหักเงิน/เครดิต"_
    - _"เลือกวิธีกดล็อกฐานข้อมูล (pessimistic vs optimistic vs atomic update)"_
    - _"ทำระบบป้องกันเงินติดลบ/หักเงินซ้ำซ้อน (double spending)"_
    - _"สถาปัตยกรรมบัญชีแยกประเภทคู่ (double-entry ledger)"_
    - _"ทำ Saga หรือ Transactional Outbox"_

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

---

## 📂 โครงสร้างโปรเจกต์

```
agent-skills-choonewza/
├── README.md
├── .agent/
│   └── skills/
│       └── git-commit-guide/      # skill ที่ใช้ภายใน repo นี้เอง
└── skills/
    ├── new-deploy-docker-production/
    │   └── SKILL.md
    ├── new-git-commit-guide/
    │   └── SKILL.md
    ├── new-project-onboarding/
    │   ├── SKILL.md
    │   ├── assets/templates/
    │   ├── evals/
    │   ├── references/
    │   └── scripts/
    ├── new-typescript-guidelines/
    │   └── SKILL.md
    ├── new-readme-maintenance/
    │   └── SKILL.md
    ├── swe-debug-mantra/
    │   └── SKILL.md
    ├── swe-enterprise-wallet-architecture/
    │   ├── README.md
    │   ├── SKILL.md
    │   └── references/
    ├── swe-management-talk/
    │   └── SKILL.md
    ├── swe-post-mortem/
    │   └── SKILL.md
    ├── swe-reports-guide/
    │   └── SKILL.md
    ├── swe-review-architecture-project/
    │   ├── SKILL.md
    │   └── README.md
    └── swe-scrutinize/
        └── SKILL.md
```

---

## 🚀 วิธีติดตั้ง

เพิ่ม skill ลงในโปรเจกต์ด้วย skill manager:

```bash
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --list
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-git-commit-guide
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-project-onboarding
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-deploy-docker-production
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-typescript-guidelines
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill new-readme-maintenance
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-debug-mantra
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-scrutinize
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-post-mortem
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-reports-guide
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-management-talk
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-enterprise-wallet-architecture
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
