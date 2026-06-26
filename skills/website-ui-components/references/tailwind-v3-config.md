# Tailwind CSS v3 — การตั้งค่าและสี

## ไฟล์ตั้งค่า

| ไฟล์ | หน้าที่ |
|---|---|
| `nt-km4u-website/tailwind.config.ts` | Theme, สี, keyframes, animations, plugins |
| `nt-km4u-website/postcss.config.mjs` | PostCSS กับ `tailwindcss` + `autoprefixer` |

## ปลั๊กอิน

- `@tailwindcss/typography` — สำหรับ prose styling
- `tailwind-scrollbar` — สำหรับ scrollbar utilities (`nocompatible: true`)

## สีของโปรเจกต์

### สี Shadcn (semantic)

ใช้รูปแบบ `hsl(var(--token))`:

- `background`, `foreground`, `border`, `input`, `ring`
- `primary` / `primary-foreground`
- `secondary` / `secondary-foreground`
- `destructive` / `destructive-foreground`
- `muted` / `muted-foreground`
- `accent` / `accent-foreground`
- `popover` / `popover-foreground`
- `card` / `card-foreground`

### สี Custom

| ชื่อ | ค่า | คำอธิบาย |
|---|---|---|
| `nt` | `#F2D173` | สีทอง NT brand |
| `nt-content` | `#545859` | สีเนื้อหา |
| `nt-border` | `#B0BEC5` | สีเส้นขอบ |
| `focus-outline` | `#617D8B` | สี outline เมื่อ focus |

## Animation ที่ใช้ได้

ปลั๊กอิน `tailwindcss-animate` **ไม่ได้ติดตั้ง** ให้ใช้ animation ที่กำหนดใน `tailwind.config.ts` แทน:

| Class | Keyframe | ระยะเวลา |
|---|---|---|
| `animate-fade-in` | opacity 0→1, scale 0.95→1 | 150ms ease-out |
| `animate-fade-out` | opacity 1→0, scale 1→0.95 | 100ms ease-in |
| `animate-wiggle` | rotate ±3deg | 200ms ease-in-out |
| `animate-waving-hand` | wave rotation | 2s linear infinite |

### ตัวอย่างการใช้ animation

```tsx
// ✅ ใช้ animation ของโปรเจกต์
"data-[state=open]:animate-fade-in data-[state=closed]:animate-fade-out"

// ❌ ใช้ไม่ได้ (ต้องใช้ปลั๊กอิน tailwindcss-animate)
"animate-in fade-in-0 zoom-in-95"
```
