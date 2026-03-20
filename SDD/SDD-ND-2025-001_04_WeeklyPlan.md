# SDD-ND-2025-001 | MODULE 04: KẾ HOẠCH THỰC ĐƠN TUẦN
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_04 |
|:---|:---|
| **Module:** | 04 — Kế Hoạch Thực Đơn Tuần |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | §6.5 UC-W01, UC-W02, UC-W03; §9.2; BR-007; CR-001 |
| **Màn hình trong module:** | SCR-015, SCR-017 |

---

## MỤC LỤC MODULE 04

- [SCR-015: Kế Hoạch Thực Đơn Tuần](#scr-015-kế-hoạch-thực-đơn-tuần)
- [SCR-017: Danh Sách Mua Sắm Tuần](#scr-017-danh-sách-mua-sắm-tuần)
- [Logic Tự Động Lên Thực Đơn Tuần](#logic-tự-động-lên-thực-đơn-tuần)

---

## SCR-015: KẾ HOẠCH THỰC ĐƠN TUẦN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-015 |
| **Tên màn hình** | Kế Hoạch Thực Đơn Tuần |
| **Use Case** | UC-W01, UC-W02, UC-W03 |
| **Navigation From** | Tab bar "Tuần", SCR-010 (link "xem thêm 3 ngày") |
| **Navigation To** | SCR-011 (Bottom Sheet đổi món ô), SCR-012 (chi tiết món), SCR-017 (danh sách mua tuần) |
| **Kiểu màn hình** | Scrollable, Tab Primary |

### Mục Đích
Cho phép người dùng xem, tạo tự động và chỉnh sửa kế hoạch thực đơn cho cả tuần (7 ngày × 3 bữa = 21 bữa). Cung cấp tổng quan calo mỗi ngày và tạo danh sách mua sắm tổng hợp.

### Layout Tổng Thể

```
┌────────────────────────────────────────────────────────┐
│  HEADER                                                │
│  Thực đơn tuần                             [Tuần này ▼]│  ← Week selector dropdown
│  T2 19/3 — CN 25/3                                    │
├────────────────────────────────────────────────────────┤
│  ACTION BAR                                            │
│  [🎲 Tự động điền tuần]      [🛒 Tạo danh sách mua]  │
├────────────────────────────────────────────────────────┤
│  WEEK GRID (scrollable horizontal)                     │
│                                                        │
│  Bữa      T2    T3    T4    T5    T6    T7    CN      │  ← Header row (fixed left)
│  ─────────────────────────────────────────────────    │
│  Sáng ┊ [01] [02] [03] [04] [05] [06] [07]           │  ← Row 1
│       ┊                                               │
│  Trưa ┊ [08] [09] [10] [11] [12] [13] [14]           │  ← Row 2
│       ┊                                               │
│  Tối  ┊ [15] [16] [17] [18] [19] [20] [21]           │  ← Row 3
│       ┊                                               │
│  ─────────────────────────────────────────────────    │
│  Kcal ┊1290  1450  ...   ...   ...   ...   ...       │  ← Daily calorie totals
│                                                        │
├────────────────────────────────────────────────────────┤
│  DAILY DETAIL PANEL (tap ngày → expand)               │
│  Thứ Hai, 19/3 — 1.290 kcal                          │
│  [Card bữa sáng]                                      │
│  [Card bữa trưa]                                      │
│  [Card bữa tối]                                       │
└────────────────────────────────────────────────────────┘
│  [🏠 Hôm nay] [📅 Tuần] [🔍] [♡] [👤]              │
└────────────────────────────────────────────────────────┘
```

### Week Grid — Chi Tiết

#### Grid Header (Sticky)

```
┌──────┬─────┬─────┬─────┬─────┬─────┬─────┬─────┐
│ Bữa  │ T2  │ T3  │ T4  │ T5  │ T6  │ T7  │ CN  │
│      │19/3 │20/3 │21/3 │22/3 │23/3 │24/3 │25/3 │
└──────┴─────┴─────┴─────┴─────┴─────┴─────┴─────┘

Column width: fixed (không auto-resize)
├── "Bữa" column: 52px (left sticky)
└── Day columns: 96px each (cuộn ngang)

Today column:
├── Header: bold text, color-primary-500
└── Background tint: color-primary-50 cho cả cột
```

#### Week Plan Cell

**Cell đã điền:**
```
┌────────────────┐
│ [Ảnh 4:3 nhỏ] │  ← 96×72px, radius-md
│ Tên món (1 ln) │  ← text-caption, 2 dòng tối đa, truncate
│ 🔥 320 kcal   │  ← text-caption, color-gray-500
└────────────────┘
Width: 96px, Height: 112px (không fixed — auto)
Background: color-cream
Border: 1px solid color-gray-100
Radius: radius-md (8px)
Padding: 4px
```

**Cell trống:**
```
┌────────────────┐
│       +        │  ← Icon "+" 24px, color-gray-300
│  Thêm món      │  ← text-caption, color-gray-400
└────────────────┘
Background: color-gray-50
Border: 1.5px dashed color-gray-200
```

**Cell — Today indicator:**
```
Top border highlight: 3px solid color-primary-500
```

**Cell interactions:**
```
Tap filled cell:
├── Short tap: open Bottom Sheet đổi món (SCR-011)
└── Long press (0.5s): action menu
    • Xem chi tiết món (→ SCR-012)
    • Đổi món
    • Xóa khỏi ô này

Tap empty cell:
└── Open Bottom Sheet tìm & chọn món
    (mini explore with filter by mealType + dietGoals)

Drag & Drop (bước 4 trong luồng §9.2):
├── Long-press cell để grab
├── Drag sang ô khác (highlight drop zone)
├── Drop: hoán đổi 2 món
└── Haptic: Medium on grab, Heavy on drop
```

#### Daily Calorie Row (Footer của grid)

```
┌──────┬─────┬─────┬─────┬─────┬─────┬─────┬─────┐
│ Kcal │1290 │1450 │ ?   │ ?   │ ?   │ ?   │ ?   │
└──────┴─────┴─────┴─────┴─────┴─────┴─────┴─────┘

Màu của số kcal:
├── Trong mục tiêu ±15%: color-success-500
├── Dưới mục tiêu 15%+: color-info-500
├── Trên mục tiêu 15%+: color-error-500
└── Chưa đủ 3 bữa: color-gray-400 (italic, VD: "~820?")

"?": Ngày chưa điền đủ 3 bữa → hiện kcal ước tính của bữa đã có
```

### Daily Detail Panel

Khi tap vào header cột của 1 ngày, panel bên dưới grid expand hiển thị 3 bữa của ngày đó:

```
Tap "T2 19/3" column header:
→ Expanded panel slides down

┌───────────────────────────────────────────────────┐
│  Thứ Hai, 19 tháng 3  🔥 1.290 / 1.500 kcal ✅  │
│  ─────────────────────────────────────────────    │
│  🌅 Bữa sáng                                     │
│  [Card full: Cháo yến mạch — 320 kcal]           │  ← Same as SCR-010 meal card
│                                                   │
│  ☀️ Bữa trưa                                     │
│  [Card full: Cơm gà — 580 kcal]                  │
│                                                   │
│  🌙 Bữa tối                                      │
│  [Card full: Canh chua cá — 390 kcal]            │
│                                                   │
│  [🛒 Xem nguyên liệu ngày này]                   │
└───────────────────────────────────────────────────┘

Default: ngày hôm nay được expand sẵn khi vào màn hình
```

### Week Selector

```
Dropdown button: "Tuần này ▼"
Tap → Date picker dropdown:
┌────────────────────────────────┐
│  Tuần này (T2 19/3 — CN 25/3) │  ← Bold, selected
│  Tuần trước                    │
│  Tuần tới                      │
│  Chọn ngày bất kỳ... 📅       │
└────────────────────────────────┘

Rules:
├── Xem tối đa 4 tuần trong quá khứ (Free)
├── Xem tối đa 2 tuần trong tương lai (Free)
└── Không giới hạn (Premium)
```

### Tự Động Điền Tuần (UC-W02)

```
Tap [🎲 Tự động điền tuần]:

Step 1: Confirm dialog
┌────────────────────────────────────────┐
│  Tự động lên thực đơn tuần            │
│                                        │
│  Hệ thống sẽ tự động điền 21 bữa     │
│  phù hợp mục tiêu của bạn.           │
│                                        │
│  ⚠️ Thực đơn hiện tại sẽ bị thay thế │
│     (nếu đã có)                        │
│                                        │
│  [Hủy]    [Tự động điền ngay]         │
└────────────────────────────────────────┘

Step 2: Loading animation
┌────────────────────────────────────────┐
│  [Animated bowl spinning]              │
│  Đang tạo thực đơn tuần...            │
│  Đảm bảo không lặp món, cân bằng     │
│  dinh dưỡng theo mục tiêu của bạn    │
└────────────────────────────────────────┘
Thời gian: 1-3 giây (show animation tối thiểu 1.5s)

Step 3: Success
→ Grid fill animation: các ô lần lượt fill từ T2 Sáng → CN Tối
   (stagger animation: mỗi ô delay 50ms)
→ Daily calorie row hiển thị đầy đủ
→ Confetti burst 🎉
→ Toast: "Thực đơn tuần đã sẵn sàng! Bạn có thể chỉnh sửa bất kỳ ô nào"

Step 4: (Tùy chọn) User chỉnh sửa từng ô
→ Long-press để drag & drop
→ Tap để đổi món (bottom sheet)
```

### Validation Thực Đơn Tuần

```
Sau khi auto-fill, hệ thống validate:
1. Không có 2 ô cùng món trong cùng 1 tuần
2. Mỗi ngày có calo trong khoảng mục tiêu ±15%
3. Mỗi bữa phù hợp với mealType (sáng/trưa/tối)

Nếu có conflict (do user chỉnh sửa):
├── Ô vi phạm: red border + warning icon
└── Hover/tap warning icon → tooltip:
    "Bạn đã ăn món này vào [ngày X] trong tuần này"
    hoặc "Calo ngày này vượt mục tiêu 25%"
```

---

## SCR-017: DANH SÁCH MUA SẮM TUẦN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-017 |
| **Tên màn hình** | Danh Sách Mua Sắm Tuần |
| **Use Case** | UC-W03 |
| **Navigation From** | SCR-015 (tap "Tạo danh sách mua") |
| **Navigation To** | Back → SCR-015 |
| **Xem thêm:** | SCR-016 (Shopping List Day — cùng component) |

### Mục Đích
Tổng hợp toàn bộ nguyên liệu từ 21 bữa trong tuần, gộp các nguyên liệu trùng, phân nhóm theo danh mục và cho phép chia sẻ/xuất danh sách.

### Layout

```
┌────────────────────────────────────────────┐
│  ← Danh sách mua sắm              [Chia sẻ]│
│  Tuần T2 19/3 — CN 25/3                   │
│  21 bữa · 48 nguyên liệu                  │  ← Summary
├────────────────────────────────────────────┤
│  ACTION BAR                                │
│  [Tích tất cả đã có] [Reset]  [Xem theo ngày]│
├────────────────────────────────────────────┤
│  PROGRESS                                  │
│  ████████░░░░░░░░  12/48 đã có sẵn        │  ← Progress bar
├────────────────────────────────────────────┤
│  INGREDIENT LIST (grouped)                 │
│                                            │
│  🥬 Rau củ (12 loại)              [Mở rộng]│
│  ─────────────────────────────────────    │
│  ☑  Cà chua                   400g    [›] │  ← Đã tích = đã có
│  ☐  Hành tây                  2 củ       │
│  ☐  Cà rốt                    300g       │
│  ...                                       │
│                                            │
│  🥩 Thịt cá (8 loại)            [Thu gọn] │
│  ─────────────────────────────────────    │
│  ☐  Thịt gà                   1.2 kg     │
│  ☐  Cá lóc                    400g       │
│  ...                                       │
│                                            │
│  🧂 Gia vị (10 loại)                      │
│  🌾 Ngũ cốc (6 loại)                      │
│  📦 Khác (12 loại)                         │
│                                            │
└────────────────────────────────────────────┘
│  [🛒 Tạo danh sách chỉ những gì cần mua]  │  ← Sticky CTA
└────────────────────────────────────────────┘
```

### Logic Gộp Nguyên Liệu

```
Aggregation rules:
1. Collect all ingredients from all dishes in WeekPlan (only filled slots)
2. Normalize name: lowercase + remove diacritics for matching
   ("Tỏi" = "tỏi" = "toi" — same ingredient)
3. Group by normalized name:
   ├── Same unit: sum amounts
   │   (200g tỏi + 50g tỏi = 250g tỏi)
   └── Different units: keep separate rows
       (200ml sữa + 1 hộp sữa = 2 rows)
4. Round up to nearest 0.5 unit
5. Group by category: rau_cu / thit_ca / gia_vi / ngu_coc / khac

Display name: capitalize first letter
Sort within category: by total amount DESC
```

### Checkbox & "Đã Có Sẵn" Logic

```
Checkbox behavior:
├── Unchecked: cần mua
├── Checked: đã có sẵn → gạch ngang text, opacity 50%
└── Counter: "12/48 đã có sẵn" updates realtime

[Tích tất cả đã có]: mass check all → giả định đã có tất cả
[Reset]: uncheck tất cả

Persist: giữ trong session
Khi generate "cần mua" → chỉ lấy unchecked items
```

### Chia Sẻ Danh Sách

```
Tap [Chia sẻ] hoặc sticky CTA:

→ Filter: chỉ lấy UNCHECKED items (cần mua)
→ Generate text format:

"📋 Danh sách mua sắm tuần — NutriDay
(T2 19/3 — CN 25/3)

🥬 Rau củ:
• Hành tây — 2 củ
• Cà rốt — 300g
• ...

🥩 Thịt cá:
• Thịt gà — 1.2 kg
• ...

(Tạo bởi NutriDay app)"

→ Native Share Sheet mở:
   ├── Zalo (deeplink)
   ├── Messenger
   ├── WhatsApp
   ├── Copy to clipboard
   └── Save as note (tùy platform)
```

### Xem Theo Ngày (Toggle)

```
[Xem theo ngày] button → toggle view:

Grouped by DAY thay vì category:
Thứ Hai:
  • Yến mạch 50g, chuối 1 quả, sữa 200ml (bữa sáng)
  • ... (bữa trưa)
  • ... (bữa tối)

Thứ Ba:
  ...

Useful for: mua sắm theo ngày, không mua dư tuần
```

---

## LOGIC TỰ ĐỘNG LÊN THỰC ĐƠN TUẦN

### Algorithm (Batch Suggestion — §9.2)

```
Input: userId, weekStartDate, weekEndDate, dietGoals, servings, allergies

For each day in [T2, T3, T4, T5, T6, T7, CN]:
  existingPlan = fetch DayPlan(userId, day)
  if existingPlan: skip (don't overwrite existing)

  For each mealType in [breakfast, lunch, dinner]:
    if slot already filled: skip

    usedDishIds = collect all dishIds used this week so far
    usedDishIds += collect dishIds from MealHistory(userId, last 7 days)

    candidates = filterDishes(dietGoals, mealType, allergies)
                   .exclude(usedDishIds)
                   .sortByRelevanceScore()

    if candidates.empty:
      candidates = filterDishes(dietGoals, mealType, allergies)
                     .exclude(usedThisWeek only)  // nới lỏng

    selectedDish = candidates[0]
    weekPlan[day][mealType] = selectedDish
    usedDishIds.add(selectedDish.id)

Validate:
├── Kiểm tra không có 2 ô giống nhau trong tuần
└── Kiểm tra tổng calo mỗi ngày trong mục tiêu ±15%

Return: 21-slot WeekPlan
```

### Performance

```
Target: < 2 giây để generate toàn bộ 21 bữa
Implementation: Batch query (không gọi 21 lần API)
POST /week-plans/generate
Body: { weekStartDate, weekEndDate }
Response: { weekPlan: {...} }
```

---

*SDD-ND-2025-001_04 | Module 04: Kế Hoạch Thực Đơn Tuần | v1.0 | NỘI BỘ*
