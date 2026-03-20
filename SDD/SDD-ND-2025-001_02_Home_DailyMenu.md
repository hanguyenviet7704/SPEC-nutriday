# SDD-ND-2025-001 | MODULE 02: TRANG CHỦ & GỢI Ý THỰC ĐƠN NGÀY
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_02 |
|:---|:---|
| **Module:** | 02 — Trang Chủ & Gợi Ý Thực Đơn Ngày |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | §6.2 UC-M01 → UC-M04; §9.1; BR-001, BR-002 |
| **Màn hình trong module:** | SCR-010, SCR-011 (Bottom Sheet Đổi Món) |

---

## MỤC LỤC MODULE 02

- [SCR-010: Trang Chủ — Thực Đơn Hôm Nay](#scr-010-trang-chủ--thực-đơn-hôm-nay)
- [SCR-011: Bottom Sheet — Đổi Món](#scr-011-bottom-sheet--đổi-món)
- [Luồng Logic Gợi Ý Ngày](#luồng-logic-gợi-ý-ngày)

---

## SCR-010: TRANG CHỦ — THỰC ĐƠN HÔM NAY

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-010 |
| **Tên màn hình** | Trang Chủ / Home — Thực Đơn Hôm Nay |
| **Use Case** | UC-M01, UC-M02, UC-M03, UC-M04 |
| **Navigation From** | SCR-009 (Onboarding complete), SCR-001 (token valid), Tab bar "Hôm nay" |
| **Navigation To** | SCR-011 (đổi món), SCR-012 (chi tiết món), SCR-016 (danh sách nguyên liệu) |
| **Kiểu màn hình** | Scrollable Feed, Primary Tab |

### Mục Đích
Màn hình chính của ứng dụng. Hiển thị thực đơn 3 bữa hôm nay, cho phép đổi món, xem tóm tắt dinh dưỡng và truy cập nhanh các tính năng liên quan.

### Layout Tổng Thể

```
┌────────────────────────────────────────────┐
│  HEADER                                    │  ← Sticky, không scroll
│  [Avatar] Xin chào, Minh Tú!     [🔔][⚙️]│
│  [🏃 Giảm cân] [💪 Tăng cân]             │  ← Diet goal chips (scrollable horizontal)
├────────────────────────────────────────────┤
│  SCROLLABLE CONTENT                        │
│                                            │
│  ┌────────────────────────────────────┐    │
│  │  Thứ Năm, 19 tháng 3              │    │  ← Date display
│  │  Thực đơn hôm nay 🍽️             │    │
│  │  [Nút: 🎲 Gợi ý ngẫu nhiên]      │    │
│  └────────────────────────────────────┘    │
│                                            │
│  ── Bữa sáng (7:00) ──────────────────    │  ← Section header
│  ┌────────────────────────────────────┐    │
│  │ [Ảnh 16:9]               [♡] [···]│    │  ← Meal Card
│  │ Cháo yến mạch chuối mật ong        │
│  │ ⏱ 15 phút    🔥 320 kcal          │
│  │              [Xem công thức] [Đổi] │
│  └────────────────────────────────────┘    │
│                                            │
│  ── Bữa trưa (12:00) ─────────────────    │
│  ┌────────────────────────────────────┐    │
│  │ [Ảnh 16:9]               [♡] [···]│    │
│  │ Cơm gà xào sả ớt                   │
│  │ ⏱ 30 phút    🔥 580 kcal          │
│  │              [Xem công thức] [Đổi] │
│  └────────────────────────────────────┘    │
│                                            │
│  ── Bữa tối (18:00) ──────────────────    │
│  ┌────────────────────────────────────┐    │
│  │ [Ảnh 16:9]               [♡] [···]│    │
│  │ Canh chua cá lóc                   │
│  │ ⏱ 35 phút    🔥 390 kcal          │
│  │              [Xem công thức] [Đổi] │
│  └────────────────────────────────────┘    │
│                                            │
│  ┌────────────────────────────────────┐    │  ← Nutrition Summary Card
│  │  Tổng dinh dưỡng hôm nay          │
│  │  🔥 1.290 kcal / ~1.500 kcal mục tiêu│
│  │  ██████████░░░░  86%              │
│  │                                    │
│  │  Protein  Carb    Fat             │
│  │  ██████░  ███░░   ████░           │
│  │  65g      142g    38g             │
│  └────────────────────────────────────┘    │
│                                            │
│  ┌────────────────────────────────────┐    │  ← Shopping CTA
│  │  🛒 Xem nguyên liệu cần mua       │    │
│  │  15 nguyên liệu cho hôm nay →     │    │
│  └────────────────────────────────────┘    │
│                                            │
│  ── Gợi ý thêm cho bạn ──────────────     │  ← Scrollable horizontal strip
│  [Card nhỏ] [Card nhỏ] [Card nhỏ] →      │
│                                            │
│  [  Có thêm 3 ngày gợi ý sẵn →  ]        │  ← Link to weekly plan
│                                            │
└────────────────────────────────────────────┘
│  [🏠 Hôm nay] [📅 Tuần] [🔍] [♡] [👤]   │  ← Bottom Tab Bar (Fixed)
└────────────────────────────────────────────┘
```

### Vùng Màn Hình — Chi Tiết

#### A. Header (Sticky — không cuộn)

```
Height: 72px + status bar
Background: color-white
Border bottom: 1px solid color-gray-200 (xuất hiện khi scroll > 0)

Left side:
├── Avatar: 40×40px circular
│   ├── Có ảnh: ảnh user
│   └── Không có: gradient xanh + initial
└── Greeting text:
    ├── "Xin chào, [Tên]!" — text-label-md, color-gray-800
    └── [Diet goal chips] — scrollable horizontal, height 28px

Right side:
├── [🔔] Notification button
│   ├── Badge đỏ nếu có thông báo chưa đọc
│   └── Tap → Notification screen (Phase 2)
└── [⚙️] Settings quick access
    └── Tap → SCR-025 (Settings)
```

**Diet Goal Chips trong header:**
```
Compact chips (height 24px):
- Chip = icon + tên ngắn (VD: "🏃 Giảm cân", "💚 Healthy")
- Nếu chọn nhiều mục tiêu: scrollable horizontal
- Tap chip → filter quick (không navigate, filter meal cards)
```

#### B. Date & Action Bar

```
Date: "Thứ Năm, 19 tháng 3" — text-heading-3, color-gray-800
Sub: "Thực đơn hôm nay" — text-body-sm, color-gray-500

[🎲 Gợi ý ngẫu nhiên] button:
├── Style: Secondary/Outline button
├── Icon: Dice icon (Phosphor)
├── Text: "Gợi ý ngẫu nhiên"
├── Tap: Trigger UC-M03 — Slot machine animation + replace all 3 cards
└── Analytics: track event "random_suggest_tapped"
```

#### C. Meal Cards (3 cards)

**Section Header:**
```
Left: ─── Bữa sáng (7:00) ───────
Right: [time badge: "07:00 - 09:00"]
Color: color-gray-500, text-label-sm
Divider line: dashed, color-gray-200
```

**Meal Card Component (full detail):**
```
┌─────────────────────────────────────────────┐
│ [Image: 343×194px (16:9), lazy load, blur  │
│  hash placeholder]                    [♡]  │
│                              [Badge bữa]   │
│                              🌅 Bữa sáng  │
├─────────────────────────────────────────────┤
│ Cháo yến mạch chuối mật ong               │  ← text-heading-3
│ ⏱ 15 phút    🔥 320 kcal    👤 1 người   │  ← text-body-sm, icons 16px
├─────────────────────────────────────────────┤
│ [Xem công thức →]        [🔄 Đổi món]     │  ← 2 action buttons
└─────────────────────────────────────────────┘

[♡] icon: top-right trên ảnh
├── Default: heart-outline, white
└── Saved: heart-fill, color-accent-500
    Animation: scale bounce + fill (400ms)

[···] more options: overlay trên ảnh
└── Action sheet:
    • Thêm vào tuần
    • Không thích món này (góp ý cho thuật toán)
    • Báo lỗi thông tin
```

**Badge bữa ăn:**
```
Bữa sáng: 🌅 Background #FFF3E0, text #E65100
Bữa trưa: ☀️ Background #FFF9C4, text #F57F17
Bữa tối:  🌙 Background #EDE7F6, text #4527A0
```

**Trạng thái Meal Card:**

| State | Mô tả | UI |
|:---|:---|:---|
| Loading | Thực đơn đang được tạo | Skeleton loader toàn card |
| Loaded | Thực đơn đã có | Card đầy đủ thông tin |
| Swapping | Đang đổi sang món mới | Overlay loading nhẹ (opacity 40%) |
| Error | Không tải được ảnh | Gray placeholder + dish icon |
| Empty | Chưa có món cho bữa này | Empty mini-state trong card |

**Action Buttons:**
```
[Xem công thức →]
├── Style: Ghost button
├── Color: color-primary-500
├── Tap → Navigate SCR-012 (Dish Detail)
└── Analytics: "dish_detail_opened"

[🔄 Đổi món]
├── Style: Secondary outline button
├── Color: color-gray-600
├── Tap → Show SCR-011 (Bottom Sheet)
└── Analytics: "swap_dish_tapped"
```

#### D. Nutrition Summary Card

```
┌─────────────────────────────────────────────┐
│  Tổng dinh dưỡng hôm nay                   │  ← text-heading-3
├─────────────────────────────────────────────┤
│  🔥 1.290 kcal / 1.500 kcal mục tiêu       │
│  ████████████████░░░░░░░░  86%             │
│  Progress bar: color-success-500 nếu 80-100%│
│               color-warning-500 nếu 60-79% │
│               color-error-500 nếu > 110%   │
├─────────────────────────────────────────────┤
│  Protein        Carb          Fat           │
│  ██████░░░░     █████████░   ████░░░░       │
│  65g / 90g      142g / 175g  38g / 55g     │
│  P: #3B82F6     C: #F59E0B   F: #EF4444   │
└─────────────────────────────────────────────┘

Tap card → Expand/Collapse chi tiết hơn (Fiber, Sodium nếu có)
```

**Calo Target Logic:**
```
Không có tài khoản: target mặc định = 1.500 kcal
Có tài khoản + chế độ:
├── Giảm cân: 1.400 kcal
├── Tăng cân: 2.200 kcal
├── Tiểu đường: 1.600 kcal
├── Ăn chay: 1.600 kcal
├── Eat clean: 1.700 kcal
├── Healthy: 1.800 kcal
├── Ít tinh bột: 1.600 kcal
└── Nhiều protein: 2.000 kcal
(Đây là giá trị default — Phase 2 sẽ tính theo BMR cá nhân)
```

#### E. Shopping CTA Banner

```
┌─────────────────────────────────────────────┐
│ 🛒  Nguyên liệu cần mua hôm nay            │
│     [15 nguyên liệu · 4 danh mục]    →     │
└─────────────────────────────────────────────┘
Background: color-primary-50
Border left: 4px solid color-primary-400
Tap → Navigate SCR-016 (Shopping List Daily)
```

#### F. Quick Discover Strip (Horizontal Scroll)

```
"Gợi ý thêm cho bạn" section:
├── Header: text-heading-3 + "Xem tất cả" link → SCR-020 (Explore)
└── Horizontal scroll list:
    ├── [Compact Dish Card] × 5-8 cards
    ├── Cards là các món phù hợp dietGoal nhưng chưa gợi ý hôm nay
    └── "Xem thêm →" card ở cuối → SCR-020

Card nhỏ (128×200px):
┌────────────────┐
│ [Image 128×90] │
│ Tên món (2 ln) │
│ 🔥 kcal        │
└────────────────┘
```

### Trạng Thái Toàn Màn Hình

#### State 1: Loading (Lần đầu vào ngày)

```
Header: hiển thị bình thường
3 Meal Cards: Skeleton loader (shimmer)
Nutrition Card: Skeleton
Shopping CTA: không hiển thị
Loading time target: < 1 giây (NFR-01)
```

#### State 2: Loaded — Có thực đơn hôm nay

```
Hiển thị đầy đủ như wireframe trên
```

#### State 3: Lỗi kết nối (Offline)

```
Header: bình thường
Toast bottom: "Đang xem thực đơn đã lưu (chế độ offline)"
Nội dung: từ cache (NFR-07)
Banner nhỏ top: "📵 Ngoại tuyến — Thực đơn từ lần truy cập trước" (color-warning-100 bg)
Nút "Đổi món": disabled (cần kết nối)
```

#### State 4: Guest sau 3 ngày

```
Sau 3 ngày dùng guest mode:
Banner persistent ở dưới Header:
┌──────────────────────────────────────────────┐
│ 💾 Lưu thực đơn & lịch sử của bạn!          │
│    Tạo tài khoản miễn phí →  [Đăng ký ngay] │
│                               [×] Để sau     │
└──────────────────────────────────────────────┘
Banner màu color-primary-50, border color-primary-300
Hiện 1 lần/ngày, dismiss = "Để sau" ẩn trong 24h
```

#### State 5: Không đủ dữ liệu món ăn

```
Rất hiếm khi xảy ra — nếu filter quá chặt:
Toast info: "Đang tìm thêm gợi ý phù hợp với bạn..."
System nới lỏng điều kiện (xem §9.1 BRD note)
```

### Filter Nhanh (UC-M04)

```
Filter sheet (hiển thị khi tap icon filter):
Bottom sheet với 3 nhóm filter:

⏱ Thời gian nấu:
  [Nhanh < 15p]  [15-30 phút]  [> 30 phút]

🔥 Mức calo:
  [Thấp < 400]  [Vừa 400-600]  [Cao > 600]

📊 Mức độ khó:
  [Dễ]  [Trung bình]  [Khó]

[Áp dụng bộ lọc]    [Đặt lại]
```

**Filter Logic:**
- Client-side filtering (không reload API)
- Filter state giữ trong session, reset khi thoát app
- Nếu filter không có kết quả cho 1 bữa nào đó → show "Không tìm thấy với bộ lọc này" + [Xóa bộ lọc]
- Chip hiển thị filter đang active ở trên 3 cards

### Refresh & Pull-to-Refresh

```
Pull-to-Refresh gesture:
├── Refresh icon animation (custom bowl spinning)
├── Gọi lại suggestion API
├── Cập nhật 3 meal cards
└── Toast: "Thực đơn đã được làm mới"

Auto-refresh:
├── Mỗi khi mở app (foreground resume)
├── Chỉ refresh nếu có mạng
└── Không refresh nếu đã có DayPlan cho hôm nay + user không kéo
```

### Analytics Events

| Event | Trigger | Parameters |
|:---|:---|:---|
| `home_screen_viewed` | Màn hình mount | `{ hasMenu: bool, dietGoals: [] }` |
| `dish_detail_opened` | Tap "Xem công thức" | `{ dishId, mealType, source: 'home' }` |
| `swap_dish_tapped` | Tap "Đổi món" | `{ dishId, mealType }` |
| `random_suggest_tapped` | Tap "Gợi ý ngẫu nhiên" | `{ previousDishIds: [] }` |
| `dish_saved` | Tap ♡ | `{ dishId, action: 'save'/'unsave' }` |
| `shopping_list_opened` | Tap Shopping CTA | `{ source: 'home_cta' }` |

---

## SCR-011: BOTTOM SHEET — ĐỔI MÓN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-011 |
| **Tên màn hình** | Bottom Sheet — Chọn Món Thay Thế |
| **Use Case** | UC-M02 |
| **Trigger From** | SCR-010 (tap "Đổi món"), SCR-015 (Weekly Plan tap ô) |
| **Kiểu component** | Bottom Sheet (không phải screen riêng) |

### Mục Đích
Cho phép người dùng xem 5 gợi ý thay thế cho 1 bữa ăn và chọn món mới.

### Layout

```
┌────────────────────────────────────────────┐  ← Backdrop overlay rgba(0,0,0,0.4)
│                                            │
│                                            │
├────────────────────────────────────────────┤  ← Bottom sheet bắt đầu
│              ──── (handle)                 │  ← 32px wide drag handle
│                                            │
│  Chọn món thay thế                        │  ← text-heading-3
│  Bữa sáng · Thứ Năm, 19/3               │  ← Subtitle: bữa + ngày
│  ─────────────────────────────────────    │
│                                            │
│  ┌────────────────────────────────────┐   │
│  │ [Ảnh 80×80]  Bánh mì trứng        │   │  ← Item 1
│  │              ⏱ 10p  🔥 350 kcal  │   │
│  │              [💚 Eat clean]        │   │
│  └────────────────────────────────────┘   │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─    │
│  ┌────────────────────────────────────┐   │
│  │ [Ảnh 80×80]  Phở gà               │   │  ← Item 2
│  │              ⏱ 20p  🔥 420 kcal  │   │
│  └────────────────────────────────────┘   │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─    │
│  [... 3 items nữa ...]                    │
│  ─────────────────────────────────────    │
│                                            │
│  ┌──────────────────────────────────────┐ │
│  │      Xác nhận thay đổi              │ │  ← Primary button (disabled until selected)
│  └──────────────────────────────────────┘ │
│  [Hủy]                                    │  ← Ghost button
│                                            │
└────────────────────────────────────────────┘
  Safe area bottom
```

### Dish Item trong Bottom Sheet

```
Height: 88px per row
┌───────────────────────────────────────────────┐
│ [Ảnh 80×72px  Tên món (text-heading-3)   [○] │
│  rounded-lg]  ⏱ 10 phút  🔥 350 kcal         │
│               [Chip: dietGoal]                 │
└───────────────────────────────────────────────┘

Radio button [○]:
├── Default: unchecked circle
└── Selected: filled circle + color-primary-500 ring
   Animation: fill + scale pulse (200ms)

Tap row → select item (replace previous selection)
```

### Behavior

```
Initial state:
- 5 items loaded từ API
- Không có item nào selected
- Button "Xác nhận" = disabled

User taps item:
- Item gets selected (radio fill)
- Button "Xác nhận" = enabled
- Có thể tap khác để đổi selection

User taps "Xác nhận":
→ Close bottom sheet (slide down 350ms)
→ Meal card (bữa tương ứng) update với animation:
  Card cũ: slide out trái (200ms)
  Card mới: slide in phải (200ms)
→ DayPlan record updated (local + API nếu online)
→ Nutrition summary recalculates
→ Haptic: Medium impact
→ Toast: "Đã đổi sang [tên món mới]" (2s)

User taps "Hủy" hoặc swipe down hoặc tap backdrop:
→ Close bottom sheet
→ Không có thay đổi
```

### API Call cho 5 gợi ý thay thế

```
GET /meals/suggestions?
  mealType=breakfast
  &excludeIds=[list of dishes in last 7 days]
  &dietGoals=[user goals]
  &limit=5

Response: Array<Dish> — 5 món sorted by relevance score
Điều kiện:
- Cùng mealType với bữa đang đổi
- Phù hợp với dietGoals của user
- Chưa xuất hiện trong MealHistory 7 ngày gần nhất
- Không phải món đang hiển thị

Loading state: Skeleton 5 rows trong sheet
Error state: "Không thể tải gợi ý. [Thử lại]"
```

### Trường Hợp Đặc Biệt

| Tình huống | Xử lý |
|:---|:---|
| API chưa trả về (loading) | Skeleton 5 rows trong sheet, không disable backdrop |
| API lỗi | Error state với retry button |
| < 5 kết quả hợp lệ | Hiển thị bao nhiêu có bấy nhiêu (min 1) |
| 0 kết quả (rất hiếm) | Message: "Hiện không có gợi ý thay thế phù hợp" + button "Duyệt tất cả món" → SCR-020 |
| User đã đổi 3+ lần trong ngày | Không chặn, nhưng log analytics |

---

## LUỒNG LOGIC GỢI Ý NGÀY

### Flow Diagram

```
User mở app
    │
    ▼
Check DayPlan(userId, today) trong DB/Cache
    │
    ├── DayPlan tồn tại và đủ 3 bữa
    │       └──► Display cached DayPlan (< 0.1s)
    │
    └── DayPlan không tồn tại / thiếu bữa
            │
            ▼
        Run Suggestion Engine:

        For each mealType [breakfast, lunch, dinner]:

        1. Lấy dietGoals của user
        2. Truy vấn:
           SELECT * FROM Dish
           WHERE mealType = ?
             AND status = 'published'
             AND JSON_CONTAINS(dietGoals, userGoals) > 0
             AND id NOT IN (
               SELECT dishId FROM MealHistory
               WHERE userId = ?
               AND date >= DATE_SUB(TODAY, 7)
             )
        3. Tính relevanceScore cho từng món
        4. Sort DESC by relevanceScore
        5. Pick top 1

        (Fallback: nới lỏng window 7d → 3d → 1d → bỏ điều kiện)

        └──► Lưu DayPlan record
             └──► Display 3 Meal Cards
```

### Performance Requirements (từ NFR)

| Metric | Target | Đo lường |
|:---|:---|:---|
| Time to Interactive (TTI) màn hình Home | ≤ 2 giây (4G) | React Native Perf Monitor |
| API suggestion response | ≤ 300ms (p95) | k6 load test |
| Skeleton → Content transition | ≤ 100ms | Visual perception |

### Caching Strategy

```
DayPlan caching:
├── Local cache: AsyncStorage / MMKV
├── Cache key: "dayplan_[userId]_[YYYY-MM-DD]"
├── TTL: cuối ngày (23:59:59 của ngày đó)
├── Invalidate when: user taps "Đổi món" (after confirm)
└── Offline: serve từ cache, no network call

Image caching:
├── React Native Fast Image với cache strategy
├── Disk cache: max 200MB
├── Memory cache: 50MB
└── Placeholder: BlurHash (16×9 ratio) cho đến khi ảnh load xong
```

---

*SDD-ND-2025-001_02 | Module 02: Trang Chủ & Gợi Ý Thực Đơn Ngày | v1.0 | NỘI BỘ*
