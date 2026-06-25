# Agent Skills Repository

โปรเจกต์นี้เก็บชุดคู่มือ `SKILL.md` สำหรับ agent ที่ทำงานร่วมกับโค้ดและกระบวนการพัฒนาในโปรเจกต์ Next.js

## มีอะไรบ้าง

โฟลเดอร์ `skills/` มี 3 skill หลัก:

- `deploy-docker-production`
  - คู่มือสร้างและรัน Docker image สำหรับแอป Next.js
  - ช่วยตรวจชื่อ image/container, เลือก env file, เช็ค Docker daemon, ตรวจ tag/container ซ้ำก่อนรัน
  - ใช้สำหรับ deploy แอป local ด้วย Docker อย่างปลอดภัย

- `git-commit-guide`
  - คู่มือเขียน commit message ตาม conventional commit
  - อธิบาย type (`feat`, `fix`, `refactor`, `style`, `chore`, `docs`, `perf`)
  - ระบุ scope ที่เหมาะกับโครงสร้าง repo นี้ และ checklist ก่อน commit
  - เน้นข้อควรระวัง เช่น ห้าม commit `.env` และห้ามใช้ `--no-verify` โดยไม่มีเหตุผล

- `project-onboarding`
  - คู่มือ onboarding สำหรับ developer ใหม่เข้าดูโปรเจกต์
  - อธิบายการตั้งค่าเบื้องต้น, stack ที่ใช้, และคำสั่งพื้นฐานที่ปลอดภัย
  - แนะนำให้ตรวจ `package.json`, `.env.example`, `Dockerfile`, `next.config.ts`

## จุดประสงค์ของ repo นี้

- เก็บคำแนะนำการทำงานแบบ agent-friendly
- ช่วย developer รู้วิธี deploy Docker, commit code, และเข้าสู่โปรเจกต์ได้ง่ายขึ้น
- เป็น repository สำหรับฝึกเขียน skill-based documentation

## ไฟล์สำคัญ

- `skills/deploy-docker-production/SKILL.md`
- `skills/git-commit-guide/SKILL.md`
- `skills/project-onboarding/SKILL.md`
- `skills/project-onboarding/assets/templates/setup-table.md`
- `skills/project-onboarding/references/*.md`
- `skills/project-onboarding/scripts/check-onboarding.sh`

## วิธีติดตั้ง

หากใช้เครื่องมือ skill manager ให้รันคำสั่งตัวอย่างนี้เพื่อเพิ่ม skill ลงในระบบ:

```bash
npx skills add deploy-docker-production
npx skills add git-commit-guide
npx skills add project-onboarding
```
