---
name: website-ui-components
description: รวม Tailwind CSS v3, shadcn/ui Components และ UI Elements ทั้งหมดของ nt-km4u-website ได้แก่ Button, Select, Item, Tooltip, DateTimePicker, DateTimePickerRange และ Data Visualization
---

# NT-KM4U Website — UI Components & Styling Skill

This skill consolidates all UI component usage guidelines, Tailwind CSS v3 configuration, and data visualization standards for the `nt-km4u-website` frontend application.

---

## 1. Tailwind CSS v3

> **⚠️ This project uses Tailwind CSS v3, NOT v4.**

All utility classes, config syntax, and plugin usage must follow **Tailwind CSS v3** conventions. Shadcn UI components generated via `npx shadcn` default to Tailwind v4 syntax and **must be manually converted** before use.

### Configuration Files

| File | Purpose |
| --- | --- |
| `nt-km4u-website/tailwind.config.ts` | Theme, colors, keyframes, animations, plugins |
| `nt-km4u-website/postcss.config.mjs` | PostCSS with `tailwindcss` + `autoprefixer` |

### Plugins

- `@tailwindcss/typography` — prose styling
- `tailwind-scrollbar` — scrollbar utilities (`nocompatible: true`)

### Project Colors (Shadcn + Custom)

Shadcn semantic colors use `hsl(var(--token))` pattern:

- `background`, `foreground`, `border`, `input`, `ring`
- `primary` / `primary-foreground`
- `secondary` / `secondary-foreground`
- `destructive` / `destructive-foreground`
- `muted` / `muted-foreground`
- `accent` / `accent-foreground`
- `popover` / `popover-foreground`
- `card` / `card-foreground`

Custom colors:

- `nt` — `#F2D173` (NT brand gold)
- `nt-content` — `#545859`
- `nt-border` — `#B0BEC5`
- `focus-outline` — `#617D8B`

### Tailwind v4 → v3 Conversion Reference

When copying code from Shadcn docs, AI-generated code, or any Tailwind v4 source, convert the following:

#### Arbitrary CSS Variable Values
```diff
- origin-(--radix-tooltip-content-transform-origin)
+ origin-[var(--radix-tooltip-content-transform-origin)]

- bg-(--my-color)
+ bg-[var(--my-color)]
```
**Rule:** `property-(--var)` → `property-[var(--var)]`

#### Data Attribute Variants
```diff
- data-open:animate-in
+ data-[state=open]:animate-fade-in

- data-closed:animate-out
+ data-[state=closed]:animate-fade-out
```
**Rule:** `data-open:` / `data-closed:` → `data-[state=open]:` / `data-[state=closed]:`

#### Size Utility
```diff
- size-4
+ h-4 w-4
```
**Note:** `size-*` is supported in Tailwind v3.4+. If your project version supports it, `size-*` is fine. Check `package.json` for the exact version.

#### Descendant Selectors
```diff
- **:data-[slot=kbd]:relative
  (removed — not supported in v3)
```
**Rule:** `**:` prefix (descendant selector) does not exist in v3.

#### `has-data-*` Shorthand
```diff
- has-data-[slot=kbd]:pr-1.5
+ has-[[data-slot=kbd]]:pr-1.5
```
**Rule:** `has-data-[attr]:` → `has-[[data-attr]]:` (or remove if unnecessary)

#### `in-data-*` Shorthand
```diff
- in-data-[slot=button-group]:rounded-lg
  (removed — not supported in v3)
```
**Rule:** `in-data-*` does not exist in v3.

#### Animation Utilities (`tailwindcss-animate` plugin)

Shadcn v4 components use `animate-in`, `animate-out`, `fade-in-0`, `zoom-in-95`, etc. from the `tailwindcss-animate` plugin. **This plugin is NOT installed in our project.**

Use the custom animations defined in `tailwind.config.ts` instead:

```tsx
// ✅ Use project's custom animations
"data-[state=open]:animate-fade-in data-[state=closed]:animate-fade-out"

// ❌ NOT available (requires tailwindcss-animate plugin)
"animate-in fade-in-0 zoom-in-95"
```

Available custom animations:

| Class | Keyframe | Duration |
| --- | --- | --- |
| `animate-fade-in` | opacity 0→1, scale 0.95→1 | 150ms ease-out |
| `animate-fade-out` | opacity 1→0, scale 1→0.95 | 100ms ease-in |
| `animate-wiggle` | rotate ±3deg | 200ms ease-in-out |
| `animate-waving-hand` | wave rotation | 2s linear infinite |

#### `not-*` Modifier
```diff
- active:not-aria-[haspopup]:translate-y-px
  (check if supported in your v3 version; may need restructuring)
```

### Shadcn Component Installation Workflow

When installing new Shadcn components via `npx shadcn add <component>`:

1. **Install** the component normally
2. **Review** the generated file in `src/components/ui/`
3. **Convert** all Tailwind v4 syntax using the reference above
4. **Test** that the component renders correctly

> **Do NOT** blindly use Shadcn-generated code without reviewing for v4 syntax.

---

## 2. Core Styling Priority

When building new features, creating components, or refactoring existing UI, you **MUST** prioritize:

1. **shadcn/ui Components**: Always choose `shadcn/ui` components (located in `src/components/ui/` or installed via `npx shadcn add`) as the primary building blocks.
2. **Tailwind CSS v3**: Use Tailwind CSS v3 utility classes for custom layouts, spacing, typography, and styling.

### Technology Stack Rules

- **Version Constraint**: The project operates strictly on **Tailwind CSS v3**.
- **Syntax Compatibility**: Ensure all utility classes follow v3 syntax.
- **shadcn/ui Prioritization**: For standard UI elements (Buttons, Inputs, Dialogs, Selects, DropdownMenus, Tooltips, RadioGroups, etc.), check `src/components/ui/` first before creating custom implementations.
- **DropdownMenu Sizing & Styling**: When using `DropdownMenu` from `shadcn/ui`, explicitly define layout constraints on `DropdownMenuContent`:
  ```tsx
  <DropdownMenuContent align="end" className="w-auto min-w-[260px]">
  ```

### Best Practices

- **Consistency**: Maintain consistent spacing, typography, and color tokens defined in `tailwind.config.ts`.
- **Server Components vs Client Components**: `shadcn/ui` components often allow parent components to remain Server Components. Leverage this to keep components server-rendered where possible.
- **`cn()` utility**: Use `cn` from `@/lib/utils` to merge Tailwind classes.

---

## 3. Button Styling

You **MUST** use the `Button` component from `shadcn/ui` (`import { Button } from "@/components/ui/button"`).

### Button Style Mappings

Apply these exact combinations of `className` constants and `variant` props based on context:

| Context | Style Constant | Variant | Meaning |
| :--- | :--- | :--- | :--- |
| **Primary** | `BUTTON_MAIN_PRIMARY_STYLE` | `variant="default"` | Main target, new action, add, submit, next |
| **Danger** | `BUTTON_MAIN_DANGER_STYLE` | `variant="default"` | Delete, remove, destructive actions |
| **Secondary** | `BUTTON_MAIN_SECONDARY_STYLE` | `variant="default"` | Cancel, previous, close windows |
| **Warning** | `BUTTON_MAIN_WARNING_STYLE` | `variant="default"` | Alerts, warnings |
| **Info** | `BUTTON_MAIN_INFO_STYLE` | `variant="default"` | Additional information, learn more |
| **Success** | `BUTTON_MAIN_SUCCESS_STYLE` | `variant="default"` | Success acknowledgment (e.g., "OK") |

### Import Pattern

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

### Example with `cn()` utility

```tsx
import { cn } from "@/lib/utils";

<Button 
  onClick={handleAction} 
  type="button" 
  size="lg" 
  variant="default" 
  className={cn(BUTTON_MAIN_PRIMARY_STYLE, "other_css_class")}
>
  button_text
</Button>
```

---

## 4. Select Components

Choose the appropriate select component based on your UI design:

| Component | Path | Best Used For | Features |
| :--- | :--- | :--- | :--- |
| **Base Select** | `src/components/ui/select.tsx` | Standard shadcn-style forms, filter bars, dense layouts. | Standard borders, compact height, custom viewports. |
| **Material Select** | `src/components/ui/material-select.tsx` | Forms aligning with Material-style inputs. | Floating labels, transitions, error messages. |
| **Material Select with Icon** | `src/components/ui/material-select-with-icon.tsx` | Category selections, technology filters, lists with visual aids. | Floating labels, Lucide icons, custom React elements, or image URLs. |

### Base Select (`select.tsx`)

```tsx
import {
  Select, SelectContent, SelectItem, SelectTrigger, SelectValue,
} from "@/components/ui/select";

function GenericSelect() {
  return (
    <Select defaultValue="option-1">
      <SelectTrigger className="w-[180px]">
        <SelectValue placeholder="Select an option" />
      </SelectTrigger>
      <SelectContent>
        <SelectItem value="option-1">Option 1</SelectItem>
        <SelectItem value="option-2">Option 2</SelectItem>
      </SelectContent>
    </Select>
  );
}
```

### Material Select (`material-select.tsx`)

```tsx
import { MaterialSelect, MaterialSelectOption } from "@/components/ui/material-select";
import { useState } from "react";

function ControlledForm() {
  const [value, setValue] = useState("");
  
  const options: MaterialSelectOption[] = [
    { value: "thai", label: "ภาษาไทย" },
    { value: "eng", label: "English" },
  ];

  return (
    <MaterialSelect
      label="เลือกภาษา (Language)"
      placeholder="เลือกภาษา..."
      options={options}
      value={value}
      onValueChange={setValue}
      error={value === "" ? "กรุณาเลือกภาษา" : undefined}
    />
  );
}
```

### Material Select with Icon (`material-select-with-icon.tsx`)

```tsx
import { Sparkles, Home, Settings } from "lucide-react";
import { MaterialSelectWithIcon } from "@/components/ui/material-select-with-icon";
import { useState } from "react";

function FeaturePicker() {
  const [selected, setSelected] = useState("");

  const options = [
    { value: "home", label: "หน้าแรก", icon: Home },            // Lucide Component
    { value: "react", label: "React JS", icon: "https://..." },  // Image URL
    { value: "fire", label: "ยอดฮิต", icon: <span>🔥</span> },   // React Node
  ];

  return (
    <MaterialSelectWithIcon
      label="คุณลักษณะเด่น"
      placeholder="เลือกหัวข้อ..."
      options={options}
      value={selected}
      onValueChange={setSelected}
      iconClassName="w-4 h-4 rounded-sm"
    />
  );
}
```

### Select Best Practices

- **Always initialize state as a string**: `const [val, setVal] = useState("");` — NOT `undefined` or `null`.
- **Floating Label Alignment**: Do not override wrapper background (`bg-background`) or add top margins that obstruct the floating label text.

---

## 5. Item Components

The `Item` system is a set of composable sub-components for building list items, content cards, and grouped rows.

**Source:** `@/components/ui/item`

### Component Hierarchy

```
ItemGroup (คอนเทนเนอร์หลักสำหรับรวมกลุ่ม Item)
└── Item (การ์ด/แถวรายการหลัก)
    ├── ItemHeader (ส่วนหัวด้านบนสุด — full-width row)
    ├── ItemMedia (ส่วนแสดงสื่อ: icon, avatar, image)
    ├── ItemContent (ส่วนห่อหุ้มข้อความ — flex-1, expands to fill)
    │   ├── ItemTitle (หัวข้อหลัก)
    │   └── ItemDescription (รายละเอียด)
    ├── ItemActions (ส่วนใส่ปุ่ม Action ต่างๆ)
    └── ItemFooter (ส่วนท้ายด้านล่างสุด — full-width row)
```

### Rendering Order

Place sub-components inside `Item` in this exact order:

1. **`ItemHeader`** — (optional) Full-width top row. Uses `basis-full`.
2. **`ItemMedia`** — (optional) Left-aligned media slot.
3. **`ItemContent`** — (required) Main text area. Expands with `flex-1`.
4. **`ItemActions`** — (optional) Right-aligned action buttons.
5. **`ItemFooter`** — (optional) Full-width bottom row. Uses `basis-full`.

### Item Props Reference

| Component | Key Props |
|---|---|
| `Item` | `variant` (`"default"` / `"outline"` / `"muted"`), `size` (`"default"` / `"sm"` / `"xs"`), `asChild` |
| `ItemMedia` | `variant` (`"default"` / `"icon"` / `"image"`) |
| `ItemGroup` | Default `gap-4`, reduces for smaller sizes |

### Import Pattern

```tsx
import {
  ItemGroup, Item, ItemHeader, ItemMedia, ItemContent,
  ItemTitle, ItemDescription, ItemActions, ItemFooter, ItemSeparator,
} from "@/components/ui/item";
```

### Example: Basic List Item

```tsx
<Item>
  <ItemMedia>
    <div className="w-10 h-10 rounded-lg bg-slate-400 shadow-md flex items-center justify-center">
      <DocumentIcon className="w-8 h-8 text-white" />
    </div>
  </ItemMedia>
  <ItemContent>
    <ItemTitle>{data.title}</ItemTitle>
    <ItemDescription>{data.creator.name}</ItemDescription>
  </ItemContent>
  <ItemActions className="flex flex-col">
    <Button variant="ghost" size="icon">...</Button>
  </ItemActions>
</Item>
```

### Example: Item with Header and Footer

```tsx
<Item variant="outline">
  <ItemHeader>
    <span className="text-xs text-muted-foreground">Category</span>
    <span className="text-xs text-muted-foreground">2 min ago</span>
  </ItemHeader>
  <ItemMedia variant="icon">
    <FileIcon className="text-primary" />
  </ItemMedia>
  <ItemContent>
    <ItemTitle>Document Title</ItemTitle>
    <ItemDescription>Author name and details</ItemDescription>
  </ItemContent>
  <ItemFooter>
    <span className="text-xs text-muted-foreground">3 comments</span>
    <Button variant="ghost" size="sm">View</Button>
  </ItemFooter>
</Item>
```

> **Legacy Components Removed**: Material Tailwind-based `ListItem`, `ListItemPrefix`, `ListItemSuffix` have been completely removed. Use the `Item` component system instead.

---

## 6. Tooltip

Ensure correct usage of the Shadcn UI `Tooltip` component, built on Radix UI primitives.

**Source:** `src/components/ui/tooltip.tsx`
**Exports:** `Tooltip`, `TooltipTrigger`, `TooltipContent`, `TooltipProvider`
**Prerequisite:** `<TooltipProvider>` is already placed in `src/app/layout.tsx`.

### Basic Usage (non-button element)

```tsx
import { Tooltip, TooltipContent, TooltipTrigger } from "@/components/ui/tooltip";

<Tooltip>
  <TooltipTrigger>
    <InfoIcon className="size-4" />
  </TooltipTrigger>
  <TooltipContent>
    <p>คำอธิบาย</p>
  </TooltipContent>
</Tooltip>
```

### With a Button — MUST use `asChild` ⚠️

When wrapping a `<Button>`, you **MUST** use `asChild` on `<TooltipTrigger>` to prevent nested `<button>` hydration errors.

```tsx
// ✅ CORRECT
<Tooltip>
  <TooltipTrigger asChild>
    <Button onClick={handleClick}>
      <Icon className="size-5" />
    </Button>
  </TooltipTrigger>
  <TooltipContent>
    <p>ข้อความ</p>
  </TooltipContent>
</Tooltip>
```

### SVG Icon Sizing Inside Button

The `<Button>` component applies a default SVG size via `[&_svg:not([class*='size-'])]:size-4`. To override, use `size-*` (not `w-* h-*`):

```tsx
// ✅ Correct — "size-" in className bypasses the default
<FaFont className="size-5" />

// ❌ Wrong — "w-" and "h-" do NOT bypass the default
<FaFont className="w-5 h-5" />
```

### TooltipContent Props

| Prop | Type | Default | Description |
| --- | --- | --- | --- |
| `side` | `"top" \| "right" \| "bottom" \| "left"` | `"top"` | Tooltip position |
| `sideOffset` | `number` | `0` | Distance from trigger |
| `className` | `string` | — | Additional classes (e.g., `z-[99999]` for modals) |

---

## 7. Date & Time Pickers

All picker components are located in `src/components/ui/`.

### Single Pickers

| Component | Import Path | Value Type |
| --- | --- | --- |
| `DatePickerInput` | `@/components/ui/datepicker-input` | `Date` |
| `TimePickerInput` | `@/components/ui/timepicker-input` | `string` (`"HH:mm:ss"`) |
| `DateTimePickerInput` | `@/components/ui/datetimepicker-input` | `Date` |

#### DatePickerInput Example

```tsx
import { DatePickerInput } from "@/components/ui/datepicker-input";

const [date, setDate] = useState<Date>();

<DatePickerInput 
    value={date} 
    onChange={setDate} 
    placeholder="Select a date..." 
    format="dd/MM/yyyy" 
/>
```

#### TimePickerInput Example

```tsx
import { TimePickerInput } from "@/components/ui/timepicker-input";

const [time, setTime] = useState<string>("14:30:00");

<TimePickerInput 
    value={time} 
    onChange={setTime} 
    placeholder="Select time..." 
    showMilliseconds={false}
/>
```

#### DateTimePickerInput with React Hook Form

```tsx
import { Controller } from "react-hook-form";
import { DateTimePickerInput } from "@/components/ui/datetimepicker-input";

<Controller
  control={control}
  name="eventDate"
  render={({ field }) => (
    <DateTimePickerInput 
      value={field.value} 
      onChange={field.onChange} 
      placeholder="Select Event Date & Time..." 
    />
  )}
/>
```

### Range Pickers

| Component | Import Path | Value Type |
| --- | --- | --- |
| `DatePickerRangeInput` | `@/components/ui/datepicker-range-input` | `DateRange` |
| `DateTimePickerRangeInput` | `@/components/ui/datetimepicker-range-input` | `DateRange` |

#### DatePickerRangeInput Example

```tsx
import { DatePickerRangeInput } from "@/components/ui/datepicker-range-input";
import { DateRange } from "react-day-picker";

const [dateRange, setDateRange] = useState<DateRange>();

<DatePickerRangeInput
  value={dateRange}
  onChange={setDateRange}
  placeholder="Select date range..."
  format="dd/MM/yyyy"
/>
```

#### DateTimePickerRangeInput with React Hook Form

```tsx
import { Controller } from "react-hook-form";
import { DateTimePickerRangeInput } from "@/components/ui/datetimepicker-range-input";

<Controller
  control={control}
  name="eventPeriod"
  render={({ field }) => (
    <DateTimePickerRangeInput
      value={field.value}
      onChange={field.onChange}
      placeholder="Select Event Period..."
    />
  )}
/>
```

### Picker Best Practices

- **Forms**: Always use `<Controller />` from `react-hook-form` — these are not native `<input />` elements.
- **Default Values**: Use `new Date()` for DatePicker, `"00:00:00"` for TimePicker.
- **Zod Validation**: Use `z.date({ message: "..." })` for date pickers. For ranges, use `z.object({ from: z.date(), to: z.date() })`.
- **Hydration Mismatches**: Add `suppressHydrationWarning` to elements displaying dynamic dates initialized on the server.
- **DateRange Type**: Import from `"react-day-picker"`.

---

## 8. Data Visualization

### Preferred Tools

#### ApexCharts (`react-apexcharts`)
For most standard charts (Line, Bar, Pie, Area, etc.), use ApexCharts. The project already has `react-apexcharts` installed.

- **Import**: `import Chart from "react-apexcharts";`
- **Usage**: Use within `"use client"` components as it requires browser APIs.

#### CSS Grid / Flexbox
For simple, custom visualizations (progress bars, heatmaps, data boxes), use CSS Grid/Flexbox if it provides more control.

### Recommendation Process

If neither ApexCharts nor CSS solutions are suitable:

1. **Stop and Research**: Look for lightweight, compatible libraries.
2. **Propose**: Recommend a library to the user first.
3. **Wait for Approval**: Do not install new libraries without user consent.
