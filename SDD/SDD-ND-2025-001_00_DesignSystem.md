# SDD-ND-2025-001 | MODULE 00: DESIGN SYSTEM & UI FOUNDATION
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_00 |
|:---|:---|
| **Module:** | 00 — Design System & UI Foundation |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | BRD-ND-2025-001 §10.2 |
| **Nền tảng:** | iOS ≥ 15.0 / Android ≥ 8.0 (API 26) |

---

## MỤC LỤC MODULE 00

1. [Triết Lý Thiết Kế](#1-triết-lý-thiết-kế)
2. [Bảng Màu (Color Palette)](#2-bảng-màu-color-palette)
3. [Typography](#3-typography)
4. [Spacing & Grid System](#4-spacing--grid-system)
5. [Icon System](#5-icon-system)
6. [Component Library — Atoms](#6-component-library--atoms)
7. [Component Library — Molecules](#7-component-library--molecules)
8. [Component Library — Organisms](#8-component-library--organisms)
9. [Navigation Patterns](#9-navigation-patterns)
10. [Animation & Motion](#10-animation--motion)
11. [Accessibility Standards](#11-accessibility-standards)
12. [Responsive Breakpoints](#12-responsive-breakpoints)

---

## 1. TRIẾT LÝ THIẾT KẾ

### 1.1 Design Philosophy
**"Warm & Fresh"** — Giao diện truyền cảm hứng nấu ăn và ăn uống lành mạnh.

| Nguyên tắc | Mô tả | Áp dụng |
|:---|:---|:---|
| **Warmth** | Ấm áp, gần gũi, như bếp ăn gia đình | Màu kem, cam nhẹ, typography thân thiện |
| **Freshness** | Tươi mới, sức khỏe, thiên nhiên | Xanh lá, ảnh thực phẩm chất lượng cao |
| **Simplicity** | 3 bước là có thực đơn — không phức tạp | Tối giản UI, CTA rõ ràng, ít bước thao tác |
| **Trust** | Thông tin dinh dưỡng được chuyên gia xác nhận | Badge "Đã kiểm duyệt", nguồn gốc thông tin |
| **Delight** | Micro-animation tạo cảm xúc tích cực | Confetti, bounce, haptic feedback |

### 1.2 Tone of Voice
- **Thân thiện** không kiêu ngạo
- **Khuyến khích** không ép buộc
- **Rõ ràng** không rườm rà
- **Tiếng Việt tự nhiên** — tránh dịch máy từ tiếng Anh

---

## 2. BẢNG MÀU (COLOR PALETTE)

### 2.1 Primary Colors

| Token | Hex | RGB | Sử dụng |
|:---|:---|:---|:---|
| `color-primary-500` | `#2E7D52` | rgb(46, 125, 82) | CTA chính, icon active, link |
| `color-primary-400` | `#3D9467` | rgb(61, 148, 103) | Hover state, focus ring |
| `color-primary-300` | `#6AB187` | rgb(106, 177, 135) | Background nhẹ, progress bar fill |
| `color-primary-200` | `#A8D4B8` | rgb(168, 212, 184) | Chip/tag background |
| `color-primary-100` | `#E8F5EE` | rgb(232, 245, 238) | Card background nhạt, selected state |
| `color-primary-50`  | `#F4FAF6` | rgb(244, 250, 246) | Page background tonal |

### 2.2 Secondary Colors (Accent — Cam ấm)

| Token | Hex | RGB | Sử dụng |
|:---|:---|:---|:---|
| `color-accent-500` | `#E8651A` | rgb(232, 101, 26) | Highlight, badge, notification dot |
| `color-accent-400` | `#F07A34` | rgb(240, 122, 52) | Hover accent |
| `color-accent-300` | `#F5A06B` | rgb(245, 160, 107) | Warning light |
| `color-accent-100` | `#FDE9D8` | rgb(253, 233, 216) | Background cảnh báo nhẹ |

### 2.3 Neutral Colors

| Token | Hex | RGB | Sử dụng |
|:---|:---|:---|:---|
| `color-white` | `#FFFFFF` | rgb(255,255,255) | Background trang chính |
| `color-cream` | `#FFF8F0` | rgb(255,248,240) | Card background |
| `color-gray-50` | `#F9FAFB` | rgb(249,250,251) | Divider background |
| `color-gray-100` | `#F3F4F6` | rgb(243,244,246) | Input background |
| `color-gray-200` | `#E5E7EB` | rgb(229,231,235) | Border, divider |
| `color-gray-400` | `#9CA3AF` | rgb(156,163,175) | Placeholder text |
| `color-gray-600` | `#4B5563` | rgb(75,85,99) | Secondary text |
| `color-gray-800` | `#1F2937` | rgb(31,41,55) | Body text chính |
| `color-gray-900` | `#111827` | rgb(17,24,39) | Heading text |

### 2.4 Semantic Colors

| Token | Hex | Sử dụng |
|:---|:---|:---|
| `color-success-500` | `#16A34A` | Thành công, calo trong mục tiêu |
| `color-success-100` | `#DCFCE7` | Background success |
| `color-warning-500` | `#D97706` | Cảnh báo, gần vượt mục tiêu |
| `color-warning-100` | `#FEF3C7` | Background warning |
| `color-error-500` | `#DC2626` | Lỗi, calo vượt mục tiêu |
| `color-error-100` | `#FEE2E2` | Background error |
| `color-info-500` | `#2563EB` | Thông tin |
| `color-info-100` | `#DBEAFE` | Background info |

### 2.5 Dark Mode Tokens (Dự phòng Phase 2)
> *Dark mode không yêu cầu trong Phase 1 nhưng token đặt tên phải chuẩn bị sẵn để scale.*

---

## 3. TYPOGRAPHY

### 3.1 Font Families

| Role | Font | Fallback | Áp dụng |
|:---|:---|:---|:---|
| **Display / Heading** | SVN-Gilroy | Be Vietnam Pro, sans-serif | H1, H2, H3, nút CTA lớn |
| **Body / UI** | Be Vietnam Pro | system-ui, sans-serif | Body text, label, input, caption |

### 3.2 Type Scale

| Token | Font | Size | Weight | Line Height | Letter Spacing | Áp dụng |
|:---|:---|:---|:---|:---|:---|:---|
| `text-display-xl` | Gilroy | 32pt | Bold (700) | 40px | -0.5px | Tên app, splash screen |
| `text-display-lg` | Gilroy | 28pt | Bold (700) | 36px | -0.4px | Tiêu đề trang chính |
| `text-heading-1` | Gilroy | 24pt | SemiBold (600) | 32px | -0.3px | H1 section |
| `text-heading-2` | Gilroy | 20pt | SemiBold (600) | 28px | -0.2px | H2, card title lớn |
| `text-heading-3` | Gilroy | 18pt | SemiBold (600) | 26px | -0.1px | H3, modal title |
| `text-body-lg` | Be Vietnam Pro | 16pt | Regular (400) | 24px | 0 | Body text chính |
| `text-body-md` | Be Vietnam Pro | 15pt | Regular (400) | 22px | 0 | Card description |
| `text-body-sm` | Be Vietnam Pro | 14pt | Regular (400) | 20px | 0 | Caption, label nhỏ (**minimum**) |
| `text-label-lg` | Be Vietnam Pro | 15pt | Medium (500) | 20px | 0.1px | Button text, tab label |
| `text-label-md` | Be Vietnam Pro | 14pt | Medium (500) | 18px | 0.1px | Chip, badge text |
| `text-label-sm` | Be Vietnam Pro | 12pt | Medium (500) | 16px | 0.3px | Tag, helper text |
| `text-caption` | Be Vietnam Pro | 12pt | Regular (400) | 16px | 0 | Metadata, timestamp |

> **Quy tắc:** `text-body-sm` (14pt) là size tối thiểu cho mọi text có nội dung đọc được. Không dùng font < 12pt. Scale factor: iOS Dynamic Type, Android sp unit.

### 3.3 Font Weight Usage

| Weight | Dùng cho |
|:---|:---|
| **700 Bold** | Tên món ăn, tiêu đề trang, số liệu nổi bật (calo) |
| **600 SemiBold** | Heading, nút CTA primary |
| **500 Medium** | Label, secondary CTA, navigation tab |
| **400 Regular** | Body text, mô tả, placeholder |

---

## 4. SPACING & GRID SYSTEM

### 4.1 Base Unit
Base unit: **4px** (tất cả spacing là bội số của 4)

| Token | Value | Sử dụng |
|:---|:---|:---|
| `space-1` | 4px | Micro spacing, icon padding nhỏ |
| `space-2` | 8px | Padding nội tại components nhỏ |
| `space-3` | 12px | Gap giữa icon và text |
| `space-4` | 16px | Padding standard card, section padding |
| `space-5` | 20px | Spacing between list items |
| `space-6` | 24px | Margin section, bottom sheet handle area |
| `space-8` | 32px | Spacing lớn giữa sections |
| `space-10` | 40px | Padding page horizontal (large screen) |
| `space-12` | 48px | Bottom safe area padding |
| `space-16` | 64px | Hero image padding |

### 4.2 Grid Layout (Mobile)

```
├── Screen Width: 375px (iPhone base) / 360px (Android base)
├── Horizontal Margin: 16px (left + right)
├── Content Width: 343px (375 - 32)
├── Column: 4 columns, Gutter: 8px
├── Card Grid: 2 columns = (343 - 8) / 2 = 167.5px ≈ 168px each
└── Full-width card: 343px
```

### 4.3 Border Radius

| Token | Value | Sử dụng |
|:---|:---|:---|
| `radius-sm` | 4px | Chip, badge nhỏ |
| `radius-md` | 8px | Input field, button secondary |
| `radius-lg` | 12px | Card, modal |
| `radius-xl` | 16px | Card ảnh lớn, bottom sheet |
| `radius-2xl` | 24px | Bottom sheet handle area |
| `radius-full` | 9999px | Avatar, pill button, circular icon |

### 4.4 Shadows

| Token | CSS Box Shadow | Sử dụng |
|:---|:---|:---|
| `shadow-sm` | `0 1px 3px rgba(0,0,0,0.08)` | Card nhẹ |
| `shadow-md` | `0 4px 12px rgba(0,0,0,0.10)` | Card hover, active |
| `shadow-lg` | `0 8px 24px rgba(0,0,0,0.12)` | Modal, bottom sheet |
| `shadow-xl` | `0 16px 48px rgba(0,0,0,0.15)` | Floating button |

---

## 5. ICON SYSTEM

### 5.1 Icon Library
**Phosphor Icons** — bộ icon chính thức của NutriDay

| Size | Pixel | Sử dụng |
|:---|:---|:---|
| `icon-xs` | 16px | Badge, chip, inline text |
| `icon-sm` | 20px | Input prefix/suffix, list item |
| `icon-md` | 24px | Navigation bar, card action |
| `icon-lg` | 32px | Empty state, feature icon |
| `icon-xl` | 48px | Onboarding illustration |

### 5.2 Custom NutriDay Icons (Cần thiết kế thêm)

| Icon Name | Mô tả | Dùng ở màn hình |
|:---|:---|:---|
| `nd-chopsticks` | Đũa + bát — brand icon | Splash, onboarding |
| `nd-diet-goal-{8}` | 8 icon cho 8 chế độ ăn | Onboarding, filter |
| `nd-meal-breakfast` | Icon bữa sáng (mặt trời + đĩa) | Trang chủ, weekly plan |
| `nd-meal-lunch` | Icon bữa trưa | Trang chủ, weekly plan |
| `nd-meal-dinner` | Icon bữa tối | Trang chủ, weekly plan |
| `nd-nutrient-protein` | Protein macro icon | Chi tiết món |
| `nd-nutrient-carb` | Carb macro icon | Chi tiết món |
| `nd-nutrient-fat` | Fat macro icon | Chi tiết món |
| `nd-timer-active` | Timer đang chạy | Bước nấu ăn |
| `nd-shopping-bag` | Túi mua sắm | Shopping list |

---

## 6. COMPONENT LIBRARY — ATOMS

### 6.1 Button

#### Primary Button
```
┌─────────────────────────────────────────┐
│  [Icon?]  Văn bản nút                   │  ← height: 52px
└─────────────────────────────────────────┘
Background: color-primary-500
Text: color-white, text-label-lg
Radius: radius-lg (12px)
Padding: 16px horizontal, 14px vertical
Min width: 120px
Full-width: 100% (dùng trong forms)
```

**States:**
- **Default:** `#2E7D52` background
- **Pressed:** `#256645` (10% darker), scale 0.97, haptic Medium
- **Loading:** Spinner replace text, disabled interaction
- **Disabled:** `#A8D4B8` background, `color-white` text, opacity 60%

#### Secondary Button (Outline)
```
Border: 1.5px solid color-primary-500
Text: color-primary-500, text-label-lg
Background: transparent
Pressed: color-primary-100 background
```

#### Ghost Button
```
Text: color-primary-500
Background: transparent
No border
Pressed: color-primary-50 background
```

#### Destructive Button
```
Background: color-error-500 (#DC2626)
Pressed: #B91C1C
Dùng cho: Xóa, hủy bỏ, đăng xuất
```

#### Icon Button (Circular)
```
Size: 44px × 44px (minimum touch target)
Shape: radius-full
Background: color-gray-100
Icon: icon-md (24px), color-gray-600
Active: color-primary-100 background, color-primary-500 icon
```

### 6.2 Input Field

```
┌─────────────────────────────────────────────┐
│ [Icon prefix?]  Placeholder text    [Icon?] │  ← height: 52px
└─────────────────────────────────────────────┘
Background: color-gray-100
Border: 1.5px solid color-gray-200 (default)
Border focused: 1.5px solid color-primary-400
Border error: 1.5px solid color-error-500
Radius: radius-md (8px)
Padding: 16px left, 12px vertical
Text: text-body-lg, color-gray-800
Placeholder: text-body-lg, color-gray-400
```

**States:** Default → Focused → Filled → Error → Disabled

**Helper text:** 12pt, shown below input. Error: color-error-500. Hint: color-gray-400.

### 6.3 Checkbox

```
Size: 22px × 22px
Border: 1.5px solid color-gray-300 (unchecked)
Border checked: color-primary-500
Fill checked: color-primary-500
Check icon: color-white, icon-xs
Radius: radius-sm (4px)
Touch target: 44px × 44px (expand hit area)
Animation: scale bounce 0.8 → 1.1 → 1.0 on check
```

### 6.4 Chip / Tag

```
Height: 32px
Padding: 8px 12px
Radius: radius-full
Border: 1px solid color-gray-200 (default)
```

| State | Background | Text | Border |
|:---|:---|:---|:---|
| Default | color-white | color-gray-600 | color-gray-200 |
| Selected | color-primary-100 | color-primary-600 | color-primary-300 |
| Disabled | color-gray-50 | color-gray-400 | color-gray-100 |

### 6.5 Badge / Label

| Type | Background | Text | Dùng khi |
|:---|:---|:---|:---|
| Success | color-success-100 | color-success-500 | "Đã duyệt", "Đang áp dụng" |
| Warning | color-warning-100 | color-warning-500 | "Gần đủ", "Cần bổ sung" |
| Error | color-error-100 | color-error-500 | "Vượt calo", "Lỗi" |
| Neutral | color-gray-100 | color-gray-600 | "Nháp", "Chưa điền" |
| Accent | color-accent-100 | color-accent-500 | "Mới", "Hot" |

### 6.6 Avatar

| Size | Diameter | Border Radius | Dùng khi |
|:---|:---|:---|:---|
| `avatar-sm` | 32px | Full | Comment, list item |
| `avatar-md` | 44px | Full | Navigation header |
| `avatar-lg` | 64px | Full | Profile page |
| `avatar-xl` | 96px | Full | Profile edit page |

Placeholder khi không có ảnh: gradient xanh lá + initial chữ hoa đầu tên.

### 6.7 Progress Bar / Macro Bar

```
Macro progress bar (Protein / Carb / Fat):
Height: 8px
Background: color-gray-200
Fill Protein: #3B82F6 (blue)
Fill Carb: #F59E0B (amber)
Fill Fat: #EF4444 (red)
Radius: radius-full
Animation: grow from 0% to target % on mount (400ms ease-out)
```

---

## 7. COMPONENT LIBRARY — MOLECULES

### 7.1 Meal Card (Bữa ăn trên Trang Chủ)

```
┌────────────────────────────────────────────┐
│ [Ảnh món ăn 16:9]                   [♡]   │
│                               [Badge bữa]  │
├────────────────────────────────────────────┤
│ Tên món ăn (text-heading-3)                │
│ 📅 20 phút  |  🔥 420 kcal               │
│                         [Đổi món →]        │
└────────────────────────────────────────────┘
Width: full (343px)
Image height: 180px (16:9 ratio for 343px)
Card background: color-cream (#FFF8F0)
Radius: radius-xl (16px)
Shadow: shadow-md
Padding: 0 (image full-bleed top) → 12px (content area)
```

**States:**
- **Loading:** Skeleton loader (gray shimmer animation)
- **Default:** Ảnh rõ nét, thông tin đầy đủ
- **Saved:** Icon ♡ filled, color-accent-500
- **Swapping:** Overlay loading nhẹ trên card

**Interaction:**
- Tap card → Màn hình Chi tiết món ăn
- Tap [♡] → Lưu yêu thích (bounce animation)
- Tap [Đổi món] → Bottom sheet 5 gợi ý

### 7.2 Compact Dish Card (Grid 2 cột — Màn hình Khám Phá)

```
┌──────────────────┐
│ [Ảnh 1:1]  [♡]  │
├──────────────────┤
│ Tên món (2 dòng) │
│ 🔥 kcal | ⏱ min │
│ [Diet chip(s)]   │
└──────────────────┘
Width: 168px
Image: 168px × 168px (1:1)
Radius: radius-lg (12px)
```

### 7.3 Weekly Plan Cell (Ô trong lưới tuần)

```
┌─────────────────┐
│ [Ảnh 4:3 nhỏ]  │
│ Tên món (1 dòng)│
│ 🔥 xxx kcal    │
└─────────────────┘
Width: ~96px (7 cột trong tuần view)
Tap → Sheet đổi món cho ô đó
Empty state: nền trắng, icon "+" giữa
```

### 7.4 Ingredient Row (Dòng nguyên liệu)

```
☐  Tên nguyên liệu          [Số lượng] [Đơn vị]   [›]
   [Badge danh mục]
```
- Checkbox: 22px, bên trái
- Tap "›" → gợi ý thay thế
- Checked → gạch ngang text + opacity 50%
- Tap hold → long-press menu (Xóa, Ghi chú)

### 7.5 Recipe Step Card

```
┌────────────────────────────────────────────┐
│  [●] Bước 1                      [Ảnh?]   │
│      Nấu yến mạch                          │
│      Cho yến mạch và sữa vào nồi nhỏ,     │
│      đun lửa vừa khoảng 5 phút...          │
│                                            │
│  ┌─────────────────────────────────────┐   │
│  │  ⏱  5 phút         [Bắt đầu Timer] │   │
│  └─────────────────────────────────────┘   │
└────────────────────────────────────────────┘
```

### 7.6 Bottom Sheet — Đổi Món

```
┌────────────────────────────────────────────┐
│         ─────── (handle)                   │
│  Chọn món thay thế - Bữa sáng              │
│  ─────────────────────────────────────     │
│  [Dish Card nhỏ - 5 items dạng list]       │
│  • [Ảnh] Tên món | kcal | thời gian        │
│  ─────────────────────────────────────     │
│       [Hủy]          [Xác nhận]            │
└────────────────────────────────────────────┘
Height: dynamic (max 75% screen height)
Background: color-white
Radius top: radius-2xl (24px)
Overlay: rgba(0,0,0,0.4) backdrop
Dismiss: swipe down hoặc tap overlay
```

### 7.7 Nutrition Summary Bar

```
┌──────────────────────────────────────────────────────┐
│  Protein      Carb         Fat         Calo          │
│  ██████░░░   ████████░░   ████░░░░░   🔥 420 kcal   │
│  24g/60g     52g/130g     12g/50g                    │
└──────────────────────────────────────────────────────┘
```

### 7.8 Diet Goal Chip Set (Onboarding & Filter)

8 chips dạng grid 2×4, mỗi chip:
```
┌────────────────────────────┐
│  [Icon]  Tên chế độ ăn     │
└────────────────────────────┘
Unselected: border color-gray-200, bg color-white
Selected: border color-primary-500, bg color-primary-100, checkmark icon
Multi-select: cho phép chọn nhiều
```

| Icon | Tên chế độ | Mã |
|:---|:---|:---|
| 🏃 | Giảm cân | `weight_loss` |
| 💪 | Tăng cân | `weight_gain` |
| 🩺 | Tiểu đường | `diabetes` |
| 🥗 | Eat Clean | `eat_clean` |
| 🌿 | Ăn chay | `vegetarian` |
| 💚 | Healthy | `healthy` |
| 🍚 | Ít tinh bột | `low_carb` |
| 🥩 | Nhiều protein | `high_protein` |

---

## 8. COMPONENT LIBRARY — ORGANISMS

### 8.1 App Navigation Bar (Bottom Tab)

```
┌────────────────────────────────────────────────────┐
│   🏠        📅        🔍        ♡        👤       │
│  Hôm nay   Tuần     Khám phá  Yêu thích  Tôi      │
└────────────────────────────────────────────────────┘
Height: 64px + safe area bottom
Background: color-white
Border top: 1px solid color-gray-200
Active tab: color-primary-500 icon + label, indicator dot
Inactive: color-gray-400 icon + label
```

**Tab items:**
1. **Hôm nay** — `House` icon → Trang chủ (UC-M01)
2. **Tuần** — `CalendarBlank` icon → Kế hoạch tuần (UC-W01)
3. **Khám phá** — `MagnifyingGlass` icon → Tìm kiếm & Explore (UC-F01/F03)
4. **Yêu thích** — `Heart` icon → Danh sách yêu thích (UC-P01)
5. **Tôi** — `User` icon → Hồ sơ & Cài đặt (UC-P02)

### 8.2 Page Header

**Variant A — Simple (Back button)**
```
← [Title]                                    [Action?]
Height: 56px
Title: text-heading-3
Back: IconButton ← (44px tap area)
```

**Variant B — Home Header**
```
[Avatar] Xin chào, [Tên]!             [🔔] [⚙️]
         [Chip: Chế độ hiện tại]
Height: 72px
```

**Variant C — Search Header**
```
← [🔍 Tìm kiếm món ăn...]          [Filter icon]
Height: 64px
Input full-width minus back button
```

### 8.3 Empty State Component

```
        [Illustration SVG — 160px]

        Tiêu đề trạng thái rỗng
        (text-heading-2, center)

        Mô tả hành động tiếp theo
        (text-body-md, color-gray-600, center)

        [  Nút hành động chính  ]
```

| Tình huống | Illustration | Title | Action Button |
|:---|:---|:---|:---|
| Chưa có thực đơn | Đĩa ăn trống | "Chưa có thực đơn hôm nay" | "Gợi ý ngay" |
| Yêu thích trống | Trái tim rỗng | "Chưa lưu món nào" | "Khám phá món ăn" |
| Tìm kiếm không có kết quả | Kính lúp + dấu hỏi | "Không tìm thấy món phù hợp" | "Thử từ khóa khác" |
| Tuần chưa lên kế hoạch | Lịch trống | "Tuần này chưa có thực đơn" | "Tự động lên kế hoạch" |

### 8.4 Toast / Snackbar Notification

```
┌───────────────────────────────────────────┐
│  [✓/✕/!]  Thông báo ngắn gọn     [Undo?] │
└───────────────────────────────────────────┘
Position: bottom (above nav bar), center horizontal
Width: 327px max
Padding: 12px 16px
Radius: radius-lg (12px)
Shadow: shadow-lg
Duration: 3 giây auto-dismiss
Animation: slide up from bottom (300ms)
```

| Type | Icon | Background |
|:---|:---|:---|
| Success | `CheckCircle` | `#16A34A` |
| Error | `XCircle` | `#DC2626` |
| Warning | `Warning` | `#D97706` |
| Info | `Info` | `#2563EB` |

### 8.5 Skeleton Loader

Áp dụng cho tất cả content đang tải:
- **Màu:** `#E5E7EB` → `#F3F4F6` (shimmer animation)
- **Animation:** 1.5s linear infinite (left to right shimmer)
- **Shape:** Match đúng shape của content thật (rectangle cho text, circle cho avatar, image-shaped cho ảnh)

---

## 9. NAVIGATION PATTERNS

### 9.1 Navigation Architecture

```
App
├── Auth Stack (unauthenticated)
│   ├── SplashScreen
│   ├── WelcomeScreen
│   ├── LoginScreen
│   ├── RegisterScreen
│   ├── OTPVerificationScreen
│   └── OnboardingStack
│       ├── OnboardingGoalScreen
│       ├── OnboardingServingsScreen
│       └── OnboardingAllergiesScreen
│
└── Main Stack (authenticated / guest)
    └── BottomTabNavigator
        ├── Tab: HomeStack
        │   ├── HomeScreen (Hôm nay)
        │   ├── DishDetailScreen
        │   ├── RecipeScreen
        │   └── ShoppingListScreen
        ├── Tab: WeekStack
        │   ├── WeekPlanScreen
        │   └── WeekShoppingListScreen
        ├── Tab: ExploreStack
        │   ├── ExploreScreen
        │   ├── SearchScreen
        │   └── IngredientSearchScreen
        ├── Tab: FavoritesStack
        │   └── FavoritesScreen
        └── Tab: ProfileStack
            ├── ProfileScreen
            ├── MealHistoryScreen
            ├── EditProfileScreen
            └── SettingsScreen
```

### 9.2 Transition Animations

| Transition | Animation | Duration |
|:---|:---|:---|
| Push (tiến vào) | Slide từ phải → trái | 300ms |
| Pop (quay lại) | Slide từ trái → phải | 250ms |
| Modal/Sheet | Slide từ dưới → lên | 350ms ease-out |
| Tab switch | Fade crossfade | 200ms |
| Splash → Welcome | Fade + scale | 500ms |

---

## 10. ANIMATION & MOTION

### 10.1 Micro-interactions

| Tên | Trigger | Animation | Duration | Haptic |
|:---|:---|:---|:---|:---|
| **Save Favorite** | Tap ♡ icon | Scale 1.0→1.4→1.0, fill color-accent-500 | 400ms spring | Medium |
| **Remove Favorite** | Tap ♡ filled | Scale 1.0→0.8→1.0, unfill | 300ms | Light |
| **Swap Dish** | Tap "Đổi món" | Card slide out left, new card slide in right | 350ms | None |
| **Random Suggest** | Tap "Gợi ý ngẫu nhiên" | Slot machine roll animation trên 3 card | 800ms | None |
| **Week Plan Complete** | Tất cả 21 ô được điền | Confetti burst từ center màn hình | 1.5s | Success |
| **Timer Complete** | Timer = 0 | Vibration pattern + bounce icon | 2s | Error (heavy) |
| **Checkbox Check** | Tap checkbox | Scale bounce + fill | 200ms spring | Light |
| **Button Press** | Tap any button | Scale 0.97 | 100ms | Selection |
| **Loading Suggest** | Đang tạo gợi ý | Spinning bowl animation | Infinite until done | None |

### 10.2 Loading States

```
Skeleton Loading:
1. Component render với skeleton shape
2. Shimmer animation (1.5s loop)
3. Data loaded → fade in real content (200ms)

Tối đa skeleton time: 5 giây trước khi show error state
```

---

## 11. ACCESSIBILITY STANDARDS

### 11.1 Contrast Ratios (WCAG 2.1 AA)

| Combination | Ratio | Standard |
|:---|:---|:---|
| `color-gray-800` text trên `color-white` bg | 12.6:1 | ✅ AAA |
| `color-gray-600` text trên `color-white` bg | 5.9:1 | ✅ AA |
| `color-white` text trên `color-primary-500` bg | 4.8:1 | ✅ AA |
| `color-primary-500` text trên `color-white` bg | 4.8:1 | ✅ AA |
| `color-gray-400` placeholder trên `color-gray-100` bg | 3.1:1 | ⚠️ AA Large only |

> **Lưu ý:** Placeholder text (color-gray-400 trên color-gray-100) không đạt AA tiêu chuẩn cho text nhỏ — cần tăng contrast khi text size < 18pt. Đề xuất dùng `color-gray-500` (#6B7280) cho placeholder.

### 11.2 Touch Targets

- Minimum touch target: **44×44pt** (iOS HIG) / **48×48dp** (Android Material)
- Spacing giữa các touch targets: tối thiểu **8pt**
- FAB (Floating Action Button): **56×56pt**

### 11.3 Dynamic Font Sizing

- Tất cả text sử dụng `sp` (Android) / scalable font (iOS)
- Layout phải không bị vỡ khi font scale lên đến 200%
- Test scenarios: Default, Large, Larger, Accessibility (max scale)

### 11.4 Screen Reader Support

- Tất cả interactive elements có `accessibilityLabel` tiếng Việt
- Ảnh món ăn: `accessibilityLabel="Ảnh [tên món]"`
- Icon buttons: `accessibilityLabel` mô tả action (VD: "Lưu yêu thích", "Đổi món")
- Progress bars: `accessibilityValue` với % và text mô tả
- Chế độ VoiceOver/TalkBack: logical reading order (top-left → bottom-right)

---

## 12. RESPONSIVE BREAKPOINTS

### 12.1 Mobile Breakpoints

| Device | Screen Width | Layout |
|:---|:---|:---|
| iPhone SE / nhỏ | 320pt | 1 cột, margin 12px |
| iPhone base | 375pt | 1 cột, margin 16px (standard) |
| iPhone Pro Max | 430pt | 1 cột, margin 20px |
| Android small | 360dp | 1 cột, margin 16dp |
| Android large | 412dp | 1 cột, margin 18dp |

### 12.2 Tablet Breakpoints

| Device | Screen Width | Layout |
|:---|:---|:---|
| iPad (10") | 768pt | 2 cột cho grids, sidebar cho weekly plan |
| iPad Pro | 1024pt | 3 cột cho grids, master-detail pattern |
| Android Tablet | 768dp+ | Tương tự iPad |

### 12.3 Web Responsive (Admin CMS & Web App)

| Breakpoint | Width | Layout |
|:---|:---|:---|
| Mobile | < 768px | Stack, hamburger menu |
| Tablet | 768–1024px | Side nav collapsed, 2-col content |
| Desktop | > 1024px | Side nav expanded, full dashboard |

---

## PHỤ LỤC

### A. Danh Sách Màn Hình Toàn Bộ Ứng Dụng

| Screen ID | Tên Màn Hình | Module SDD | Use Case |
|:---|:---|:---|:---|
| SCR-001 | Splash Screen | Module 01 | — |
| SCR-002 | Welcome / Giới thiệu | Module 01 | UC-U01 |
| SCR-003 | Đăng ký tài khoản | Module 01 | UC-U02 |
| SCR-004 | Xác thực OTP | Module 01 | UC-U02 |
| SCR-005 | Đăng nhập | Module 01 | UC-U03 |
| SCR-006 | Quên mật khẩu | Module 01 | UC-U03 |
| SCR-007 | Onboarding: Chọn mục tiêu | Module 01 | UC-U04 |
| SCR-008 | Onboarding: Số người ăn | Module 01 | UC-U04 |
| SCR-009 | Onboarding: Dị ứng thực phẩm | Module 01 | UC-U04 |
| SCR-010 | Trang chủ — Thực đơn hôm nay | Module 02 | UC-M01 |
| SCR-011 | Bottom Sheet — Đổi món | Module 02 | UC-M02 |
| SCR-012 | Chi tiết món ăn | Module 03 | UC-R01 |
| SCR-013 | Công thức từng bước | Module 03 | UC-R01/R03 |
| SCR-014 | Gợi ý thay thế nguyên liệu | Module 03 | UC-R04 |
| SCR-015 | Kế hoạch thực đơn tuần | Module 04 | UC-W01 |
| SCR-016 | Danh sách nguyên liệu ngày | Module 05 | UC-S01 |
| SCR-017 | Danh sách mua sắm tuần | Module 05 | UC-S01/W03 |
| SCR-018 | Tìm kiếm theo tên | Module 06 | UC-F01 |
| SCR-019 | Tìm kiếm theo nguyên liệu | Module 06 | UC-F02 |
| SCR-020 | Màn hình Khám phá / Browse | Module 06 | UC-F03 |
| SCR-021 | Danh sách món yêu thích | Module 07 | UC-P01 |
| SCR-022 | Trang cá nhân & thống kê | Module 07 | UC-P02 |
| SCR-023 | Chỉnh sửa hồ sơ | Module 07 | UC-U05 |
| SCR-024 | Lịch sử thực đơn | Module 07 | UC-U06 |
| SCR-025 | Cài đặt | Module 09 | — |
| SCR-026 | Cài đặt thông báo | Module 09 | BR-012 |
| ADM-001 | Admin: Dashboard tổng quan | Module 08 | §6.8 |
| ADM-002 | Admin: Danh sách món ăn | Module 08 | §6.8 |
| ADM-003 | Admin: Thêm/Sửa món ăn | Module 08 | BR-008 |
| ADM-004 | Admin: Import Excel | Module 08 | BR-008 |
| ADM-005 | Admin: Nutritionist Review | Module 08 | BR-004 |
| ADM-006 | Admin: Báo cáo | Module 08 | §6.8 |

---

*SDD-ND-2025-001_00 | Design System | v1.0 | NỘI BỘ*
