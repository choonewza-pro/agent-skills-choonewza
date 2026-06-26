# Button — แนวทางการใช้งาน

**ต้อง** ใช้ `Button` component จาก shadcn/ui (`import { Button } from "@/components/ui/button"`)

## ตาราง Style

ใช้ค่า `className` constants และ `variant` props ตามบริบท:

| บริบท | Style Constant | Variant | ความหมาย |
|---|---|---|---|
| **หลัก (Primary)** | `BUTTON_MAIN_PRIMARY_STYLE` | `variant="default"` | เป้าหมายหลัก, สร้างใหม่, เพิ่ม, ส่ง, ถัดไป |
| **อันตราย (Danger)** | `BUTTON_MAIN_DANGER_STYLE` | `variant="default"` | ลบ, เอาออก, การกระทำที่ทำลาย |
| **รอง (Secondary)** | `BUTTON_MAIN_SECONDARY_STYLE` | `variant="default"` | ยกเลิก, ก่อนหน้า, ปิดหน้าต่าง |
| **เตือน (Warning)** | `BUTTON_MAIN_WARNING_STYLE` | `variant="default"` | แจ้งเตือน, คำเตือน |
| **ข้อมูล (Info)** | `BUTTON_MAIN_INFO_STYLE` | `variant="default"` | ข้อมูลเพิ่มเติม, เรียนรู้เพิ่ม |
| **สำเร็จ (Success)** | `BUTTON_MAIN_SUCCESS_STYLE` | `variant="default"` | ยืนยันสำเร็จ (เช่น "ตกลง") |

## วิธี Import

```tsx
import { 
  Button, 
  BUTTON_MAIN_PRIMARY_STYLE,
  BUTTON_MAIN_DANGER_STYLE,
  BUTTON_MAIN_SECONDARY_STYLE,
  BUTTON_MAIN_WARNING_STYLE,
  BUTTON_MAIN_INFO_STYLE,
  BUTTON_MAIN_SUCCESS_STYLE 
} from "@/components/ui/button";
```

## ตัวอย่างการใช้กับ `cn()`

```tsx
import { cn } from "@/lib/utils";

<Button 
  onClick={handleAction} 
  type="button" 
  size="lg" 
  variant="default" 
  className={cn(BUTTON_MAIN_PRIMARY_STYLE, "other_css_class")}
>
  ข้อความปุ่ม
</Button>
```
