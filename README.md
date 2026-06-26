# 🧠 Agent Skills Repository

> ชุดคู่มือ `SKILL.md` สำหรับ AI agent ที่ทำงานร่วมกับโค้ดและกระบวนการพัฒนาในโปรเจกต์ Next.js  
> ออกแบบมาให้ agent อ่านแล้วปฏิบัติตามได้ทันที — ไม่ต้องเดา ไม่ต้องถาม

---

## 📦 Skills ทั้งหมด

โฟลเดอร์ `skills/` มี **8 skills** แบ่งตามหมวดหมู่:

### 🔧 Development & Deployment

| Skill | คำอธิบาย |
| :--- | :--- |
| [`deploy-docker-production`](skills/deploy-docker-production/SKILL.md) | Build และ run Docker image สำหรับ Next.js app แบบ local — ตรวจชื่อ image/container, เลือก env file, เช็ค Docker daemon, ตรวจ tag ซ้ำก่อนรัน |
| [`git-commit-guide`](skills/git-commit-guide/SKILL.md) | คู่มือเขียน commit message ตาม conventional commit — กำหนด type, scope, checklist ก่อน commit, ข้อควรระวังเรื่อง `.env` และ `--no-verify` |
| [`project-onboarding`](skills/project-onboarding/SKILL.md) | คู่มือ onboarding สำหรับ developer ใหม่ — ตั้งค่าเบื้องต้น, stack, คำสั่งพื้นฐาน, ตรวจ `package.json` / `.env.example` / `Dockerfile` |

### 🐛 Debugging & Code Review

| Skill | คำอธิบาย |
| :--- | :--- |
| [`swe-debug-mantra`](skills/swe-debug-mantra/SKILL.md) | 4-step debugging discipline — reproduce, trace fail path, falsify hypothesis, cross-reference breadcrumbs — ต้อง recite mantra ก่อนเริ่ม debug ทุกครั้ง |
| [`swe-scrutinize`](skills/swe-scrutinize/SKILL.md) | การรีวิวแผนงาน, PR หรือโค้ดเชิงลึกในฐานะคนนอก (Outsider-perspective) — ทบทวนความจำเป็น ความซับซ้อน และแกะเส้นทางการทำงานของโค้ดจนสุดทาง |
| [`swe-post-mortem`](skills/swe-post-mortem/SKILL.md) | การเขียนบันทึกประวัติการแก้ไขบั๊กเชิงลึกสำหรับโปรแกรมเมอร์ (Root Cause Analysis - RCA) — อธิบายสาเหตุ กลไก วิธีแก้ไข และวิธีการป้องกัน |

### 📝 Reporting & Communication

| Skill | คำอธิบาย |
| :--- | :--- |
| [`swe-reports-guide`](skills/swe-reports-guide/SKILL.md) | แนวทางเขียน feature/bug fix report สำหรับผู้บริหาร — กำหนดโครงสร้างโฟลเดอร์ `docs/dev-reports/` และมาตรฐานการเขียน |
| [`swe-management-talk`](skills/swe-management-talk/SKILL.md) | แปลงเนื้อหา engineer-to-engineer เป็นภาษาสำหรับผู้บริหาร — รองรับหลาย channel: JIRA, Slack, standup, email, meeting talking-points |

---

## 💡 เงื่อนไขการเรียกใช้งาน (Triggers & Usage)

AI Agent จะดึงคู่มือ (Skills) เหล่านี้ไปทำงานโดยอัตโนมัติเมื่อเจอกลุ่มคำสั่ง คีย์เวิร์ด หรือสถานการณ์ดังต่อไปนี้:

### 🔧 Development & Deployment
* **`deploy-docker-production`**
  * **ทำงานเมื่อ:** ต้องการ build Docker image หรือ run container บนเครื่อง local อย่างปลอดภัยโดยอ้างอิงชื่อโปรเจกต์จาก repo จริง
  * **คีย์เวิร์ดที่ใช้ถาม:** 
    * *"ช่วย build docker หน่อย"*
    * *"รัน container ของโปรเจกต์นี้ด้วย docker-compose/docker file"*
    * *"deploy production ในเครื่อง local ยังไง"*
* **`git-commit-guide`**
  * **ทำงานเมื่อ:** กำลังจะบันทึกโค้ด (commit) หรือต้องการให้เขียน commit message
  * **คีย์เวิร์ดที่ใช้ถาม:** 
    * *"commit ให้หน่อย"*, *"ช่วยเขียน commit message"*
    * *"git commit"*
    * *"ต้องใส่ scope อะไรตอน commit"*
* **`project-onboarding`**
  * **ทำงานเมื่อ:** มีนักพัฒนาคนใหม่เข้ามาเริ่มต้นใช้งานโปรเจกต์
  * **คีย์เวิร์ดที่ใช้ถาม:**
    * *"โปรเจกต์นี้ตั้งค่าอย่างไร"*, *"เริ่มรันแอปยังไง"*
    * *"โปรเจกต์นี้ใช้ stack อะไรบ้าง"*
    * *"ขอวิธี setup ตัวฐานข้อมูล/docker สำหรับคนใหม่"*

### 🐛 Debugging & Code Review
* **`swe-debug-mantra`**
  * **ทำงานเมื่อ:** เกิดข้อผิดพลาดในระบบ, พบบั๊ก หรือโปรแกรมแครช
  * **คีย์เวิร์ดที่ใช้ถาม:**
    * *"มีบั๊กตรงนี้ช่วยดูหน่อย"*, *"โค้ดรันไม่ผ่านขึ้น error แบบนี้..."*
    * พิมพ์คำสั่ง `/swe-debug-mantra` หรือ `/debug-mantra`
    * วาง Stack trace หรือ error log ลงในแชท
* **`swe-scrutinize`**
  * **ทำงานเมื่อ:** ต้องการตรวจสอบโค้ด รีวิวสถาปัตยกรรม หรือตรวจสอบแผนงานเชิงลึก
  * **คีย์เวิร์ดที่ใช้ถาม:**
    * *"ช่วยรีวิว PR/โค้ดส่วนนี้ให้หน่อย"*, *"ขอความเห็นที่สอง (second opinion) เรื่องการออกแบบนี้"*
    * พิมพ์คำสั่ง `/swe-scrutinize` หรือ `/scrutinize`
    * *"ตรวจแผนการพัฒนาตัวนี้ว่าสมเหตุสมผลไหม"*
* **`swe-post-mortem`**
  * **ทำงานเมื่อ:** แก้ไขบั๊กเสร็จแล้วและต้องการบันทึกประวัติการแก้ไขเชิงลึก (RCA) ก่อนจะปิดงาน
  * **คีย์เวิร์ดที่ใช้ถาม:**
    * *"ช่วยเขียน post-mortem สำหรับบั๊กนี้หน่อย"*, *"ทำสรุป RCA ของปัญหาที่เพิ่งแก้เสร็จ"*
    * พิมพ์คำสั่ง `/swe-post-mortem` หรือ `/post-mortem`
    * *"เขียนสรุปสาเหตุรากเหง้า (root cause) และการป้องกันปัญหาในอนาคต"*

### 📝 Reporting & Communication
* **`swe-reports-guide`**
  * **ทำงานเมื่อ:** ต้องจัดทำเอกสารสรุปความคืบหน้าการพัฒนาหรือการแก้ไขบั๊กเพื่อรายงานต่อหัวหน้างาน
  * **คีย์เวิร์ดที่ใช้ถาม:**
    * *"ช่วยเขียนสรุปฟีเจอร์นี้ส่งให้หัวหน้าหน่อย"*, *"ขอรายงานการแก้บั๊ก (bug fix report)"*
    * *"สร้างเอกสารสถานะการพัฒนาล่าสุด"*
* **`swe-management-talk`**
  * **ทำงานเมื่อ:** ต้องการเปลี่ยนภาษาเทคนิค (Engineering jargon) ให้กลายเป็นภาษาระดับผู้บริหารตามช่องทางต่างๆ
  * **คีย์เวิร์ดที่ใช้ถาม:**
    * *"เขียนสรุปสำหรับส่งเข้า Slack/Email ผู้บริหาร"*, *"ช่วยแก้ภาษาในโพสต์นี้ให้เป็นทางการและเหมาะกับ PM"*
    * *"ขอเนื้อหาอัปเดตสั้นๆ สำหรับประชุม standup"*
    * *"ช่วยทำสรุปแบบ executive summary"*

---

## 📂 โครงสร้างโปรเจกต์

```
agent-skills-choonewza/
├── README.md
├── .agent/
│   └── skills/
│       └── git-commit-guide/      # skill ที่ใช้ภายใน repo นี้เอง
└── skills/
    ├── deploy-docker-production/
    │   └── SKILL.md
    ├── git-commit-guide/
    │   └── SKILL.md
    ├── project-onboarding/
    │   ├── SKILL.md
    │   ├── assets/templates/
    │   ├── evals/
    │   ├── references/
    │   └── scripts/
    ├── swe-debug-mantra/
    │   └── SKILL.md
    ├── swe-management-talk/
    │   └── SKILL.md
    ├── swe-post-mortem/
    │   └── SKILL.md
    ├── swe-reports-guide/
    │   └── SKILL.md
    └── swe-scrutinize/
        └── SKILL.md
```

---

## 🚀 วิธีติดตั้ง

เพิ่ม skill ลงในโปรเจกต์ด้วย skill manager:

```bash
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --list
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill git-commit-guide
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill project-onboarding
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill deploy-docker-production
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-debug-mantra
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-scrutinize
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-post-mortem
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-reports-guide
npx skills add https://github.com/choonewza-pro/agent-skills-choonewza --skill swe-management-talk
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
