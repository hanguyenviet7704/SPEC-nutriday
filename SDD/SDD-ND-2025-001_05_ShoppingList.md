# SDD-ND-2025-001 | MODULE 05: DANH SÁCH NGUYÊN LIỆU & MUA SẮM
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_05 |
|:---|:---|
| **Module:** | 05 — Danh Sách Nguyên Liệu & Mua Sắm |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | §6.4 UC-S01, UC-S02, UC-S03; BR-006 |
| **Màn hình trong module:** | SCR-016 (Shopping List Day) |

> **Lưu ý:** SCR-017 (Shopping List Tuần) đã được mô tả trong Module 04. Module này tập trung vào SCR-016 (danh sách nguyên liệu theo ngày) và các shared behaviors.

---

## MỤC LỤC MODULE 05

- [SCR-016: Danh Sách Nguyên Liệu Ngày](#scr-016-danh-sách-nguyên-liệu-ngày)
- [Shared Component: Ingredient List](#shared-component-ingredient-list)
- [Share & Export Behavior](#share--export-behavior)

---

## SCR-016: DANH SÁCH NGUYÊN LIỆU NGÀY

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-016 |
| **Tên màn hình** | Danh Sách Nguyên Liệu Hôm Nay |
| **Use Case** | UC-S01, UC-S02, UC-S03 |
| **Navigation From** | SCR-010 (tap Shopping CTA), SCR-012 (tap "Thêm nguyên liệu vào DS mua") |
| **Navigation To** | Back → nguồn navigate |
| **Kiểu màn hình** | Scrollable list với sticky header |

### Mục Đích
Hiển thị danh sách tổng hợp tất cả nguyên liệu cần thiết cho thực đơn hôm nay (3 bữa), cho phép đánh dấu nguyên liệu đã có sẵn và chia sẻ danh sách cần mua.

### Layout Tổng Thể

```
┌────────────────────────────────────────────┐
│  ← Nguyên liệu hôm nay         [Chia sẻ ↗]│  ← Header
│  Thứ Năm, 19 tháng 3                      │
│  3 bữa · 15 nguyên liệu                   │
├────────────────────────────────────────────┤
│  PROGRESS BAR                              │
│  ████████░░░░░░  5/15 đã có sẵn   [Reset] │
├────────────────────────────────────────────┤
│  ACTION CHIPS                              │
│  [Xem theo bữa]  [Xem theo danh mục]      │  ← Default: theo danh mục
├────────────────────────────────────────────┤
│  INGREDIENT LIST                           │
│                                            │
│  🥬 Rau củ                        (4 loại)│  ← Category header (collapsible)
│  ─────────────────────────────────────    │
│  ☐  Chuối                     1 quả       │  ← Row
│  ☐  Cà chua                   2 quả       │
│  ☑  Hành lá                   1 bó        │  ← Checked = đã có
│  ☐  Ớt đỏ                    2 quả       │
│                                            │
│  🥩 Thịt cá                       (3 loại)│
│  ─────────────────────────────────────    │
│  ☐  Thịt gà                   300g        │
│  ☐  Cá lóc                    200g        │
│  ☐  Tôm                       100g        │
│                                            │
│  🧂 Gia vị                       (5 loại) │
│  ─────────────────────────────────────    │
│  ☐  Tỏi                       3 tép       │
│  ☐  Mật ong                   1 muỗng canh│
│  ☑  Muối                      (đã có)     │
│  ☑  Nước mắm                  (đã có)     │
│  ☐  Tiêu                      1/2 muỗng   │
│                                            │
│  🌾 Ngũ cốc                       (2 loại)│
│  📦 Khác                          (1 loại) │
│                                            │
└────────────────────────────────────────────┘
│  [🛒 Tạo danh sách chỉ cần mua (10 loại)] │  ← Sticky CTA
└────────────────────────────────────────────┘
```

### Category Header Component

```
┌──────────────────────────────────────────────────┐
│  [Icon]  Tên danh mục          (N loại)  [▲/▼]  │
└──────────────────────────────────────────────────┘
Height: 44px
Background: color-gray-50
Tap: Collapse/Expand danh mục (animation 200ms)
Icon categories:
├── 🥬 Rau củ (rau_cu)
├── 🥩 Thịt cá (thit_ca)
├── 🧂 Gia vị (gia_vi)
├── 🌾 Ngũ cốc (ngu_coc)
└── 📦 Khác (khac)
```

### Ingredient Row Component

```
Height: 56px (tapped) / 48px (untapped)

┌─────────────────────────────────────────────────┐
│  [☐/☑]  Tên nguyên liệu         Số lượng Đơn vị│
└─────────────────────────────────────────────────┘

States:
Unchecked (cần mua):
├── Checkbox: empty square, border color-gray-300
├── Name: text-body-md, color-gray-800, normal weight
└── Amount: text-body-sm, color-gray-600

Checked (đã có sẵn):
├── Checkbox: filled green, checkmark white
├── Name: text-body-md, color-gray-400, strikethrough
└── Amount: color-gray-300
└── Row background: color-gray-50 (subtle)

Animation khi check:
├── Checkbox: scale bounce + fill (200ms spring)
├── Text: cross through animation (300ms)
├── Row: subtle background change
└── Haptic: Light

Long press on row: Action sheet
├── 📝 Thêm ghi chú (VD: "mua loại nhỏ")
├── ✏️ Chỉnh số lượng
└── 🗑️ Xóa khỏi danh sách (không xóa khỏi công thức)
```

### Xem Theo Bữa (Toggle View)

```
Tap "Xem theo bữa" chip:
View switches to:

🌅 Bữa sáng — Cháo yến mạch chuối mật ong
─────────────────────────────────────────
☐ Yến mạch                    50g
☐ Chuối                        1 quả
☐ Mật ong                      1 muỗng canh
☐ Sữa tươi không đường         200ml

☀️ Bữa trưa — Cơm gà xào sả ớt
─────────────────────────────────────────
☐ Gạo                          150g (đã gộp từ 2 món)
☐ Thịt gà                      300g
...

🌙 Bữa tối — Canh chua cá lóc
...

Lưu ý: trong view này KHÔNG gộp nguyên liệu trùng
(Gạo xuất hiện 2 lần nếu cả trưa và tối đều có cơm)
```

### Progress Bar

```
Position: sticky dưới header
Height: 8px bar + text
Background: color-gray-200
Fill: color-primary-400
Text: "N/M đã có sẵn" — text-label-sm, color-gray-600

Update: realtime khi tick/untick checkbox
Khi 100%:
├── Bar fill = green gradient
└── Toast: "Tuyệt! Bạn đã có đủ nguyên liệu 🎉"
```

### Sticky CTA — Tạo Danh Sách Cần Mua

```
Tap "🛒 Tạo danh sách chỉ cần mua (N loại)":
N = số item unchecked

→ Mở Share Sheet với text đã format:
"🛒 Cần mua hôm nay — NutriDay
Thứ Năm, 19/3

🥬 Rau củ:
• Chuối — 1 quả
• Cà chua — 2 quả
• Ớt đỏ — 2 quả

🥩 Thịt cá:
• Thịt gà — 300g
• Cá lóc — 200g
• Tôm — 100g

🧂 Gia vị:
• Tỏi — 3 tép
• Mật ong — 1 muỗng canh
• Tiêu — 1/2 muỗng

🌾 Ngũ cốc:
• Yến mạch cán dẹt — 50g
• Gạo — 150g

─────────────────
Tạo từ NutriDay app"

Options:
├── Copy to clipboard (show toast "Đã sao chép")
├── Share to Zalo
├── Share to Messenger
└── Native share sheet (other apps)
```

---

## SHARED COMPONENT: INGREDIENT LIST

### Component Props (Sử dụng lại ở SCR-016, SCR-017, SCR-012)

```typescript
interface IngredientListProps {
  ingredients: Ingredient[];
  servings: number;
  mode: 'shopping' | 'detail' | 'readonly';
  groupBy: 'category' | 'meal';
  showCheckbox: boolean;
  onCheckChange?: (ingredientId: string, checked: boolean) => void;
  onSubstitutePress?: (ingredient: Ingredient) => void;
}
```

### Aggregation Logic (UC-S01)

```
Dùng ở: SCR-016 (day), SCR-017 (week)

Step 1: Collect all ingredients from selected dishes
Step 2: Normalize names for deduplication
  normalize(name) = name.toLowerCase().trim()
                        .normalize('NFD')
                        .replace(/[\u0300-\u036f]/g, '')
  // "Tỏi" → "toi", "tỏi" → "toi"

Step 3: Group by normalized name
  if same unit → sum amounts, keep original display name (first occurrence)
  if different units → keep as separate rows

Step 4: Round amounts
  gram/ml/đơn vị nhỏ → round up to nearest 5g or 10ml
  quả/củ/bó → round to nearest 0.5

Step 5: Sort
  within category: amount DESC
  category order: rau_cu, thit_ca, ngu_coc, gia_vi, khac

Step 6: Return aggregated list with counts per category
```

---

## SHARE & EXPORT BEHAVIOR

### Text Format Specification

```
Tiêu đề:
"[emoji danh mục] Danh sách mua sắm [ngày/tuần] — NutriDay"

Separator: "─────────────────"

Mỗi item: "• [Tên nguyên liệu] — [Số lượng] [Đơn vị]"

Footer: "─────────────────\nTạo từ NutriDay app"

Encoding: UTF-8, line endings: \n
Max length: ~2000 chars (for clipboard)
If exceeds: split by category, offer multiple messages
```

### Platform Behavior

| Platform | Action | Behavior |
|:---|:---|:---|
| iOS | Share Sheet | Native share sheet + "Copy" option |
| Android | Share Intent | Native share intent |
| Both | Copy to Clipboard | Inline copy button + toast |

### Edge Cases

| Tình huống | Xử lý |
|:---|:---|
| Tất cả đã tích "đã có" | CTA text: "Đã có đủ nguyên liệu! Muốn chia sẻ danh sách không?" |
| Thực đơn chưa có (0 món) | Empty state: "Chưa có thực đơn để tạo danh sách" + CTA về Home |
| Nguyên liệu amount = 0 sau gộp | Ẩn row đó (không hiển thị) |
| Đơn vị không xác định | Hiển thị nguyên (VD: "1 ít" / "tùy khẩu vị") |

---

*SDD-ND-2025-001_05 | Module 05: Danh Sách Nguyên Liệu & Mua Sắm | v1.0 | NỘI BỘ*
