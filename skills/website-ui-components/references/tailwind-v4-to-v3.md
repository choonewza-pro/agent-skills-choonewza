# แปลง Tailwind v4 → v3

เมื่อคัดลอกโค้ดจากเอกสาร Shadcn, โค้ดจาก AI หรือแหล่งอื่นที่ใช้ Tailwind v4 ต้องแปลง syntax ตามกฎด้านล่าง

## ค่า CSS Variable แบบ Arbitrary

```diff
- origin-(--radix-tooltip-content-transform-origin)
+ origin-[var(--radix-tooltip-content-transform-origin)]

- bg-(--my-color)
+ bg-[var(--my-color)]
```

**กฎ:** `property-(--var)` → `property-[var(--var)]`

## Data Attribute Variants

```diff
- data-open:animate-in
+ data-[state=open]:animate-fade-in

- data-closed:animate-out
+ data-[state=closed]:animate-fade-out
```

**กฎ:** `data-open:` / `data-closed:` → `data-[state=open]:` / `data-[state=closed]:`

## Size Utility

```diff
- size-4
+ h-4 w-4
```

**หมายเหตุ:** `size-*` รองรับใน Tailwind v3.4+ ถ้าเวอร์ชันโปรเจกต์รองรับ ใช้ `size-*` ได้ ตรวจเวอร์ชันจาก `package.json`

## Descendant Selectors

```diff
- **:data-[slot=kbd]:relative
  (ลบออก — ไม่รองรับใน v3)
```

**กฎ:** prefix `**:` (descendant selector) ไม่มีใน v3

## `has-data-*` Shorthand

```diff
- has-data-[slot=kbd]:pr-1.5
+ has-[[data-slot=kbd]]:pr-1.5
```

**กฎ:** `has-data-[attr]:` → `has-[[data-attr]]:` (หรือลบออกถ้าไม่จำเป็น)

## `in-data-*` Shorthand

```diff
- in-data-[slot=button-group]:rounded-lg
  (ลบออก — ไม่รองรับใน v3)
```

**กฎ:** `in-data-*` ไม่มีใน v3

## `not-*` Modifier

```diff
- active:not-aria-[haspopup]:translate-y-px
  (ตรวจว่าเวอร์ชัน v3 ของคุณรองรับหรือไม่ อาจต้องปรับโครงสร้าง)
```

## ขั้นตอนติดตั้ง Shadcn Component

เมื่อติดตั้ง component ใหม่ผ่าน `npx shadcn add <component>`:

1. **ติดตั้ง** component ตามปกติ
2. **ตรวจสอบ** ไฟล์ที่สร้างใน `src/components/ui/`
3. **แปลง** syntax Tailwind v4 ทั้งหมดตามกฎด้านบน
4. **ทดสอบ** ว่า component แสดงผลถูกต้อง

> **ห้าม** ใช้โค้ดที่ Shadcn สร้างมาโดยไม่ตรวจ syntax v4
