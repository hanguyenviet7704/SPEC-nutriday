# SDD-ND-2025-001 | MODULE 06: TÌM KIẾM & KHÁM PHÁ
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_06 |
|:---|:---|
| **Module:** | 06 — Tìm Kiếm & Khám Phá |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | §6.6 UC-F01, UC-F02, UC-F03; CR-003; BR-009 |
| **Màn hình trong module:** | SCR-018, SCR-019, SCR-020 |

---

## MỤC LỤC MODULE 06

- [SCR-020: Màn Hình Khám Phá (Explore)](#scr-020-màn-hình-khám-phá-explore)
- [SCR-018: Tìm Kiếm Theo Tên Món](#scr-018-tìm-kiếm-theo-tên-món)
- [SCR-019: Tìm Kiếm Theo Nguyên Liệu Sẵn Có](#scr-019-tìm-kiếm-theo-nguyên-liệu-sẵn-có)

---

## SCR-020: MÀN HÌNH KHÁM PHÁ (EXPLORE)

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-020 |
| **Tên màn hình** | Khám Phá Món Ăn |
| **Use Case** | UC-F03 |
| **Navigation From** | Tab bar "Khám phá", SCR-010 (link "Xem tất cả"), SCR-021 (Favorites) |
| **Navigation To** | SCR-018 (search), SCR-019 (ingredient search), SCR-012 (dish detail) |
| **Kiểu màn hình** | Scrollable, Tab Primary |

### Mục Đích
Màn hình trung tâm để khám phá tất cả món ăn trong database. Hỗ trợ lọc theo chế độ ăn, bữa ăn, sắp xếp và tìm kiếm.

### Layout Tổng Thể

```
┌────────────────────────────────────────────┐
│  SEARCH BAR (Sticky)                       │
│  ┌─────────────────────────────────────┐   │
│  │ 🔍  Tìm kiếm món ăn...   [Filter⚙]│   │
│  └─────────────────────────────────────┘   │
│  [🥦 Tìm theo nguyên liệu →]              │  ← Quick access to SCR-019
├────────────────────────────────────────────┤
│  FILTER TABS (Horizontal Scroll)           │
│  [Tất cả] [🏃Giảm cân] [🌿Ăn chay] [🩺Tiểu đường] ... │
├────────────────────────────────────────────┤
│  MEAL TYPE FILTER                          │
│  [Tất cả]  [🌅 Sáng]  [☀️ Trưa]  [🌙 Tối] │
├────────────────────────────────────────────┤
│  SORT & ACTIVE FILTERS                     │
│  Sắp xếp: [Phổ biến ▼]  [Đang lọc: 2 ✕] │
├────────────────────────────────────────────┤
│  RESULTS COUNT                             │
│  Tìm thấy 47 món ăn                       │
├────────────────────────────────────────────┤
│  DISH GRID (2 cột)                         │
│                                            │
│  ┌──────────────┐  ┌──────────────┐        │
│  │ [Ảnh 1:1]   │  │ [Ảnh 1:1]   │        │
│  │ Tên món      │  │ Tên món      │        │
│  │ 🔥 kcal|⏱p │  │ 🔥 kcal|⏱p │        │
│  │ [💚][🏃]     │  │ [💚]         │        │
│  └──────────────┘  └──────────────┘        │
│                                            │
│  ┌──────────────┐  ┌──────────────┐        │
│  │ ...          │  │ ...          │        │
│  └──────────────┘  └──────────────┘        │
│                                            │
│  [Load more / Infinite scroll]            │
│                                            │
└────────────────────────────────────────────┘
│  [🏠 Hôm nay] [📅 Tuần] [🔍] [♡] [👤]   │
└────────────────────────────────────────────┘
```

### Search Bar

```
Height: 48px
Background: color-gray-100
Radius: radius-full (pill shape)
Padding: 14px left (icon + gap), 14px right

Left: 🔍 search icon (20px, color-gray-400)
Placeholder: "Tìm kiếm món ăn..." (color-gray-400)
Right: [Filter ⚙] icon button

Tap anywhere on search bar:
→ Navigate SCR-018 (Search Screen)
→ Keyboard opens automatically

[Filter ⚙] tap:
→ Open Filter Bottom Sheet (không navigate)
```

### Diet Filter Tabs (Horizontal Scroll)

```
Tab list:
[Tất cả] [🏃 Giảm cân] [💪 Tăng cân] [🩺 Tiểu đường]
[🥗 Eat Clean] [🌿 Ăn chay] [💚 Healthy]
[🍚 Ít tinh bột] [🥩 Nhiều protein]

Style:
├── Active tab: color-primary-500 bg, white text, underline
├── Inactive tab: color-gray-100 bg, color-gray-600 text
└── Scroll: horizontal, no scrollbar

Default: "Tất cả" selected (hoặc auto-select user's primary goal)

Tap tab → filter results (client-side, instant)
```

### Meal Type Filter

```
4 chips: [Tất cả] [🌅 Bữa sáng] [☀️ Bữa trưa] [🌙 Bữa tối]
Style: pill chips (same as diet filter but smaller)
Can combine with diet tab filter
```

### Filter Bottom Sheet

```
┌────────────────────────────────────────────┐
│  ─────                                     │
│  Bộ lọc nâng cao                          │  ← text-heading-3
│  ─────────────────────────────────────    │
│                                            │
│  ⏱ Thời gian nấu                         │  ← Section
│  [< 15 phút] [15-30 phút] [> 30 phút]    │
│                                            │
│  🔥 Mức calo (mỗi khẩu phần)             │
│  [Thấp < 400] [Vừa 400-600] [Cao > 600]  │
│                                            │
│  📊 Mức độ khó                            │
│  [Dễ] [Trung bình] [Khó]                  │
│                                            │
│  🏷️ Tag đặc biệt                          │
│  [Không chiên] [Ít dầu mỡ] [Nhanh gọn]  │
│  [Ít rác thải] [1 nồi/chảo]              │
│                                            │
│  ─────────────────────────────────────    │
│  [Đặt lại tất cả]    [Áp dụng (47 món)] │
└────────────────────────────────────────────┘
```

### Sort Options

```
Dropdown: "Sắp xếp: Phổ biến ▼"
Options:
├── Phổ biến (default — by selection count)
├── Mới nhất (by createdAt)
├── Ít calo nhất (calo ASC)
├── Nhanh nhất (prepTime+cookTime ASC)
└── Tên A-Z
```

### Dish Card (2-column Grid)

```
Card size: 168×220px
┌──────────────────┐
│ [Ảnh 168×126px] │  ← 4:3 ratio, lazy load, BlurHash
│            [♡]  │  ← favorite button overlay
├──────────────────┤
│ Tên món ăn       │  ← text-heading-3 (16pt), 2 dòng, ellipsis
│ 🔥 320 | ⏱ 15p  │  ← text-body-sm, color-gray-500
│ [💚 Eat clean]   │  ← Diet chip(s), max 2 visible
└──────────────────┘

Tap card → Navigate SCR-012 (Dish Detail)
Tap [♡] → Save/Unsave favorite
Long press → Quick action sheet:
  • Thêm vào thực đơn hôm nay — bữa [sáng/trưa/tối]
  • Xem chi tiết
  • Lưu yêu thích
```

### Empty States

| Điều kiện | Illustration | Message |
|:---|:---|:---|
| Kết quả = 0 sau filter | Kính lúp + dấu hỏi | "Không tìm thấy món phù hợp với bộ lọc này" |
| Database trống | Đĩa trống | "Chưa có dữ liệu. Vui lòng thử lại sau" |
| Network error | Wifi với dấu x | "Không thể tải danh sách. Kiểm tra kết nối" |

### Infinite Scroll / Pagination

```
Page size: 20 items
Threshold: load more khi còn 5 items từ bottom
Loading: skeleton card 2 items

End of list: "Đã hiển thị tất cả [N] món ăn"
```

---

## SCR-018: TÌM KIẾM THEO TÊN MÓN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-018 |
| **Tên màn hình** | Tìm Kiếm Theo Tên |
| **Use Case** | UC-F01 |
| **Navigation From** | SCR-020 (tap search bar) |
| **Navigation To** | SCR-012 (dish detail), back → SCR-020 |

### Mục Đích
Cho phép tìm kiếm món ăn theo tên với hỗ trợ auto-complete và tìm kiếm không dấu.

### Layout

```
┌────────────────────────────────────────────┐
│  ┌─────────────────────────────────────┐   │  ← Search bar (active, keyboard open)
│  │ ←  🔍  pho...              [×]     │   │
│  └─────────────────────────────────────┘   │
├────────────────────────────────────────────┤
│  (Khi đang gõ — AutoComplete Suggestions)  │
│                                            │
│  Gợi ý:                                   │
│  🔍 Phở bò                               │  ← Suggestion item
│  🔍 Phở gà                               │
│  🔍 Phở chay                             │
│  🔍 Phở xào                              │
│  🔍 Phở cuốn                             │
│                                            │
├────────────────────────────────────────────┤
│  (Sau khi submit / chọn gợi ý)            │
│  Kết quả cho "phở" — 5 món               │
│                                            │
│  ┌──────────────┐  ┌──────────────┐        │  ← Grid results
│  │ Phở bò       │  │ Phở gà       │        │
│  │ 🔥 420 kcal  │  │ 🔥 380 kcal  │        │
│  └──────────────┘  └──────────────┘        │
│  ...                                       │
│                                            │
├────────────────────────────────────────────┤
│  Tìm kiếm gần đây                         │  ← (Initial state — before typing)
│  pho bo  [×]                              │
│  com ga  [×]                              │
│  canh chua  [×]                           │
│  [Xóa lịch sử tìm kiếm]                  │
└────────────────────────────────────────────┘
```

### Search Behavior

```
Trigger autocomplete: khi gõ ≥ 2 ký tự
Debounce: 200ms (tránh gọi API liên tục)
API endpoint: GET /dishes/search?q=[query]&limit=5 (autocomplete)
             GET /dishes/search?q=[query] (full search)

Unaccented search:
├── Normalize query: "pho bo" → "phở bò"
├── Backend: MySQL FULLTEXT search + unaccent
├── "mi quang" → "mì quảng"
└── Case insensitive

Response time: < 300ms (NFR-02)

Autocomplete items (max 5):
├── Show dish name + diet chip
├── Tap item → go to full search with that term
└── No navigation to dish detail from autocomplete

Submit (Enter / Search button):
→ Full search, show grid results
→ Add to recent searches

Results display:
├── Same 2-column grid as SCR-020
└── Highlight matching text in results (bold)
```

### Recent Searches

```
Stored in AsyncStorage
Max 10 recent searches
Format: [{ query, timestamp }] sorted by most recent

Display:
├── "Tìm kiếm gần đây" header
├── Each item: 🕐 icon + query text + [×] to remove
└── Tap: populate search bar + run search

[Xóa lịch sử tìm kiếm]:
└── Clear all + show empty state
```

### Empty & Error States

| State | Trigger | UI |
|:---|:---|:---|
| No recent searches | First time | "Bắt đầu gõ để tìm kiếm món ăn" |
| No results | Search with 0 results | "Không tìm thấy '[query]'" + gợi ý tìm kiếm liên quan |
| Network error | API fail | "Tìm kiếm không khả dụng. Thử lại." |

---

## SCR-019: TÌM KIẾM THEO NGUYÊN LIỆU SẴN CÓ

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-019 |
| **Tên màn hình** | Tìm Món Theo Nguyên Liệu Tủ Lạnh |
| **Use Case** | UC-F02 |
| **Navigation From** | SCR-020 (tap "Tìm theo nguyên liệu"), SCR-021 (Favorites section) |
| **Navigation To** | SCR-012 (dish detail), back → SCR-020 |
| **CR Liên Quan** | CR-003 |

### Mục Đích
Cho phép người dùng nhập danh sách nguyên liệu đang có trong tủ lạnh, ứng dụng gợi ý món ăn có thể nấu ngay hoặc chỉ thiếu vài nguyên liệu.

### Layout Tổng Thể

```
┌────────────────────────────────────────────┐
│  ← Tìm món theo nguyên liệu               │  ← Header
├────────────────────────────────────────────┤
│                                            │
│  Bạn có sẵn nguyên liệu gì?              │  ← text-heading-2
│  Nhập để tìm món có thể nấu ngay          │  ← text-body-md
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │ + Thêm nguyên liệu...               │  │  ← Input field
│  └──────────────────────────────────────┘  │
│                                            │
│  Đã thêm (3):                             │
│  [Trứng ×]  [Hành lá ×]  [Cà chua ×]    │  ← Ingredient chips with delete
│                                            │
│  Gợi ý nhanh:                             │  ← Popular ingredients
│  [Tỏi] [Hành tây] [Gừng] [Tiêu]         │
│  [Thịt heo] [Trứng] [Rau muống]          │
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │   🔍 Tìm món có thể nấu (3 ng.liệu)│  │  ← Primary CTA button
│  └──────────────────────────────────────┘  │
│                                            │
├────────────────────────────────────────────┤
│  KẾT QUẢ (sau khi tìm)                    │
│                                            │
│  ✅ Nấu được ngay (2 món)                 │  ← Section 1
│  ──────────────────────────────           │
│  ┌───────────────────────────────────┐    │
│  │ [Ảnh]  Trứng chiên cà chua       │    │
│  │        Đủ: Trứng ✓ Cà chua ✓    │    │
│  │        🔥 280 kcal | ⏱ 10 phút  │    │
│  └───────────────────────────────────┘    │
│  ┌───────────────────────────────────┐    │
│  │ [Ảnh]  Canh cà chua trứng        │    │
│  │        Đủ: Trứng ✓ Cà chua ✓    │    │
│  │        Hành lá ✓                 │    │
│  │        🔥 180 kcal | ⏱ 15 phút  │    │
│  └───────────────────────────────────┘    │
│                                            │
│  🛒 Thiếu 1-2 nguyên liệu (5 món)        │  ← Section 2
│  ──────────────────────────────           │
│  ┌───────────────────────────────────┐    │
│  │ [Ảnh]  Cơm rang trứng            │    │
│  │        Đủ: Trứng ✓ Hành lá ✓    │    │
│  │        Cần thêm: 🛒 Gạo (cơm)   │    │  ← Missing ingredient highlighted
│  │        🔥 450 kcal | ⏱ 20 phút  │    │
│  └───────────────────────────────────┘    │
│                                            │
│  Thiếu 3+ nguyên liệu (12 món)           │  ← Section 3 (collapsed by default)
│  [Xem thêm ▼]                             │
└────────────────────────────────────────────┘
```

### Ingredient Input

```
Input field behavior:
├── Tap → keyboard opens
├── Type từ 2 ký tự → autocomplete dropdown
├── Dropdown: max 8 suggestions từ ingredient master list
├── Tap suggestion → add as chip
├── Press Enter/Done → add typed text as chip (even if not in master list)
└── Max 20 ingredients

Ingredient chip:
├── Text: tên nguyên liệu
├── [×] button: remove chip
└── Animation: slide in from input (200ms)

Quick Suggestions (Popular):
└── Pre-defined list of 15 most common ingredients in DB
    Tap → add as chip immediately
```

### Kết Quả Tìm Kiếm

```
API: POST /dishes/search-by-ingredients
Body: { ingredients: ["Trứng", "Hành lá", "Cà chua"] }
Response time: < 500ms

Response format:
{
  canMakeNow: Dish[],      // có đủ ≥ 80% nguyên liệu
  nearlyReady: {           // thiếu 1-2 nguyên liệu
    dish: Dish,
    missingIngredients: string[]
  }[],
  partial: {               // thiếu 3+ (optional section)
    dish: Dish,
    missingCount: number
  }[]
}
```

**Result Card Layout:**

```
┌─────────────────────────────────────────────┐
│ [Ảnh 80×80px  [Tên món — text-heading-3]   │
│  rounded-lg]  [Match status line]           │
│               Đủ: [chip ✓] [chip ✓]        │
│               Cần thêm: [chip 🛒]           │
│               🔥 [kcal] | ⏱ [phút]         │
│                              [Xem công thức]│
└─────────────────────────────────────────────┘

Match chips:
├── ✓ chip: background color-success-100, text color-success-600
└── 🛒 chip: background color-warning-100, text color-warning-600
   Tap 🛒 chip → jump to ingredient, offer to add to shopping list

[Xem công thức]:
→ Navigate SCR-012 với pre-populated servings
```

### Sort Logic

```
Sort results:
1. canMakeNow first (100% match → sorted by rating/popularity)
2. nearlyReady second (sorted by fewest missing ingredients)
3. partial last (sorted by % match DESC)

Tie-breaking: by relevanceScore (standard algorithm)
```

### Empty State

```
0 results:
┌──────────────────────────────────────────┐
│ [Sad vegetable illustration]             │
│ Chưa tìm thấy món phù hợp              │
│ Thử thêm vài nguyên liệu khác          │
│ hoặc bỏ bớt một số không cần thiết     │
│                                          │
│ [Khám phá tất cả món ăn →]             │
└──────────────────────────────────────────┘
```

### Accessibility & UX Notes

```
Input UX:
├── Keyboard: returnKeyType="done" để thêm ingredient
├── After add: keyboard stays open for next ingredient
└── "Tìm kiếm" button triggers API only (not on each chip add)

Performance:
└── API called only when user taps "Tìm món" — not on each chip change
    (avoid too many API calls as user adds ingredients one by one)
```

---

*SDD-ND-2025-001_06 | Module 06: Tìm Kiếm & Khám Phá | v1.0 | NỘI BỘ*
