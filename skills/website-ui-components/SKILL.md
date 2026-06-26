---
name: website-ui-components
description: ใช้ skill นี้เมื่อสร้าง แก้ไข หรือเลือกใช้ UI component ของโปรเจกต์ nt-km4u-website เช่น Button, Select, Item, Tooltip, DateTimePicker และ Data Visualization รวมถึงการตั้งค่า Tailwind CSS v3 และการแปลง syntax จาก v4 เป็น v3
---

# NT-KM4U Website — UI Components & Styling Skill

รวมแนวทางการใช้ UI component, การตั้งค่า Tailwind CSS v3 และมาตรฐาน data visualization สำหรับ `nt-km4u-website`

## ข้อควรระวัง

- โปรเจกต์นี้ใช้ **Tailwind CSS v3 เท่านั้น** ห้ามใช้ syntax ของ v4
- Shadcn UI ที่ติดตั้งผ่าน `npx shadcn add` จะได้ syntax v4 **ต้องแปลงก่อนใช้งาน** → อ่าน `references/tailwind-v4-to-v3.md`
- ปลั๊กอิน `tailwindcss-animate` **ไม่ได้ติดตั้ง** ให้ใช้ animation ที่กำหนดใน `tailwind.config.ts` แทน
- ใช้ `shadcn/ui` component จาก `src/components/ui/` เป็นอันดับแรกเสมอ ก่อนสร้าง custom component
- ใช้ `cn()` จาก `@/lib/utils` สำหรับรวม Tailwind classes

## สรุปคอมโพเนนต์

| คอมโพเนนต์ | ไฟล์ต้นทาง | เอกสารอ้างอิง |
|---|---|---|
| Tailwind CSS v3 config & สี | `tailwind.config.ts` | `references/tailwind-v3-config.md` |
| แปลง v4 → v3 + ติดตั้ง Shadcn | — | `references/tailwind-v4-to-v3.md` |
| Button | `src/components/ui/button` | `references/button-styling.md` |
| Select (3 แบบ) | `src/components/ui/select.tsx` และอื่นๆ | `references/select-components.md` |
| Item system | `src/components/ui/item` | `references/item-components.md` |
| Tooltip | `src/components/ui/tooltip.tsx` | `references/tooltip-usage.md` |
| Date & Time Pickers | `src/components/ui/` | `references/datetime-pickers.md` |
| Data Visualization | `react-apexcharts` | `references/data-visualization.md` |

## แนวทางทั่วไป

- **ลำดับความสำคัญ**: shadcn/ui → Tailwind v3 utility → custom CSS
- **Server vs Client**: shadcn/ui มักให้ parent component เป็น Server Component ได้ ใช้ประโยชน์จากจุดนี้
- **ความสม่ำเสมอ**: ใช้ spacing, typography และ color tokens ที่กำหนดใน `tailwind.config.ts`
- **DropdownMenu**: กำหนด layout constraints บน `DropdownMenuContent` เสมอ เช่น `className="w-auto min-w-[260px]"`

## รูปแบบผลลัพธ์

เมื่อแนะนำ component ให้ระบุ:
- Import path ที่ถูกต้อง
- ตัวอย่างโค้ดสั้นๆ
- ข้อควรระวังเฉพาะ component นั้น (ถ้ามี)
