# Enterprise Wallet & Credit-Deduction Architecture Skill

นี่คือ **Agent Skill** สำหรับระบบ Agent เพื่อช่วยในการออกแบบ ตรวจสอบ (Audit) และพัฒนาสถาปัตยกรรมระบบกระเป๋าเงินดิจิทัล (Digital Wallet) และระบบหักเครดิตที่มีการทำงานพร้อมกันสูง (High-Concurrency) โดยเน้นความถูกต้องและปลอดภัยสูงสุดระดับระบบการเงิน (Financial-Grade)

---

## 📂 โครงสร้างของ Skill

สัญญะและเอกสารอ้างอิงภายในโฟลเดอร์นี้ถูกแบ่งออกเป็นสองส่วนหลัก เพื่อป้องกันปัญหา Context Bloat ของ AI Agent:

```
swe-enterprise-wallet-architecture/
├── README.md               # เอกสารแนะนำฉบับนี้
├── SKILL.md                # ไฟล์หลักที่ Agent จะโหลดไปใช้งาน (เก็บแนวคิดหลักและคีย์เวิร์ดสำหรับทริกเกอร์)
└── references/             # โฟลเดอร์เก็บเอกสารอ้างอิงเชิงลึกและโค้ดตัวอย่าง
    ├── SCHEMA.md           # ออกแบบฐานข้อมูล PostgreSQL DDL ด้วยระบบ Double-Entry Bookkeeping
    ├── LOCKING-STRATEGIES.md # การจัดการ Concurrency (Pessimistic, Optimistic, Atomic, Distributed Lock)
    ├── SAGA-OUTBOX.md      # การทำธุรกรรมข้าม Service ด้วย Saga Pattern และ Transactional Outbox
    ├── SECURITY.md         # ระบบความปลอดภัยและมิดเดิลแวร์สำหรับควบคุมความซ้ำซ้อน (Idempotency)
    └── LOAD-TESTING.md     # การทดสอบการรับโหลดของระบบทำธุรกรรมด้วย k6
```

---

## 🧠 วิธีการทำงานของ Skill นี้ (How it works)

ตัวแทน AI (Agent) จะทำการโหลดเนื้อหาใน [SKILL.md](SKILL.md) เข้าไปใน Context อัตโนมัติเมื่อมีคำถามหรือคำสั่งที่ตรงกับเงื่อนไขใน Frontmatter:

### คำหลักสำหรับการทริกเกอร์ (Trigger Keywords)
* ออกแบบระบบจ่ายเงิน / หักเงิน / Wallet
* ป้องกันปัญหาการกดซ้ำ (Double Spending / Idempotency)
* ออกแบบตารางบัญชีแยกประเภท (Double-Entry Ledger Schema)
* การจัดคิวหรือทำธุรกรรมข้ามระบบ (Saga / Outbox Pattern)
* ปรับแต่งประสิทธิภาพการเขียนในฐานข้อมูลพร้อมกันสูง (Concurrency Lock)

---

## 🛡️ หัวข้อหลักที่ครอบคลุม (Key Concepts Covered)

1. **Double-Entry Bookkeeping**: การบันทึกบัญชีแยกประเภทคู่ที่มียอดเดบิตและเครดิตดุลกันเสมอ เพื่อเป็นแหล่งอ้างอิงความถูกต้อง (Source of Truth) แทนการอัปเดตคอลัมน์ยอดเงินตรงๆ
2. **Concurrency Control**: การเลือกใช้วิธีล็อกที่เหมาะสม เช่น **Atomic UPDATE** สำหรับงานความเร็วสูง หรือ **Distributed Lock + Fencing Token** ร่วมกับ Redis เพื่อป้องกันปัญหาประมวลผลซ้ำซ้อนจากกรณีเซิร์ฟเวอร์ค้าง (GC Pause)
3. **Idempotency**: การสร้างมิดเดิลแวร์ตรวจสอบ `Idempotency-Key` เพื่อป้องกันการหักเงินซ้ำจากการกดซ้ำหรือระบบเครือข่ายขัดข้อง
4. **Saga Pattern & Recovery**: การจัดการ Transaction แบบกระจายศูนย์สำหรับระบบที่มีการเชื่อมต่อไปยัง API ภายนอก พร้อมระบบเยียวยาข้อผิดพลาด (Compensating Transaction) และระบบตรวจสอบความสอดคล้องย้อนหลัง (Reconciliation)
5. **Security Controls**: การปกป้องข้อมูลทางการเงินตามมาตรฐาน OWASP API Security เช่น BOLA, BOPLA และการทำ Cryptographic Merkle-chain สำหรับการตรวจสอบการแก้ไขข้อมูลย้อนหลัง
