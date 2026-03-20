# SDD-ND-2025-001 | MODULE 03: CHI TIẾT MÓN ĂN & CÔNG THỨC
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_03 |
|:---|:---|
| **Module:** | 03 — Chi Tiết Món Ăn & Công Thức |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | §6.3 UC-R01 → UC-R05; CR-002, CR-005; BR-003, BR-010 |
| **Màn hình trong module:** | SCR-012, SCR-013, SCR-014 |

---

## MỤC LỤC MODULE 03

- [SCR-012: Chi Tiết Món Ăn](#scr-012-chi-tiết-món-ăn)
- [SCR-013: Xem Công Thức & Timer](#scr-013-xem-công-thức--timer)
- [SCR-014: Gợi Ý Thay Thế Nguyên Liệu](#scr-014-gợi-ý-thay-thế-nguyên-liệu)

---

## SCR-012: CHI TIẾT MÓN ĂN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-012 |
| **Tên màn hình** | Chi Tiết Món Ăn |
| **Use Case** | UC-R01, UC-R02, UC-R05 |
| **Navigation From** | SCR-010 (tap card / "Xem công thức"), SCR-015 (tap ô trong tuần), SCR-020 (Explore), SCR-021 (Favorites), SCR-011 (tap item trong bottom sheet) |
| **Navigation To** | SCR-013 (Xem công thức), SCR-014 (Thay thế nguyên liệu), SCR-016 (Shopping từ màn hình này) |
| **Kiểu màn hình** | Scroll dọc, Hero image trên cùng |

### Mục Đích
Cung cấp đầy đủ thông tin về món ăn: ảnh gallery, thông tin dinh dưỡng, danh sách nguyên liệu với khả năng điều chỉnh khẩu phần, và các bước nấu ăn.

### Layout Tổng Thể

```
┌────────────────────────────────────────────┐
│ STATUS BAR (transparent overlay on image)  │
├────────────────────────────────────────────┤
│                                            │
│  [HERO IMAGE GALLERY                       │  ← 343×257px (4:3), swipeable
│   full-width, với page dots]      [♡][···]│  ← Floating buttons over image
│                                            │
│  1/3  ●●○  (ảnh 1 trong 3)               │  ← Image dots indicator
├────────────────────────────────────────────┤
│  SCROLLABLE CONTENT AREA:                  │
│                                            │
│  Cháo yến mạch chuối mật ong             │  ← text-heading-1
│  [🌅 Bữa sáng] [💚 Eat clean] [🏃 Giảm cân] │  ← Diet tags (horizontal scroll)
│                                            │
│  ── Quick Info Row ──────────────────────  │
│  ⏱ 15 phút  🔥 320 kcal  👨‍🍳 Dễ  🥦 Chay │
│                                            │
│  ── Khẩu phần ───────────────────────────  │
│  Cho:  [−]  [2 người]  [+]                │  ← Stepper component
│  (Định lượng tự động điều chỉnh)          │
│                                            │
│  ── Thông tin dinh dưỡng ────────────────  │
│  ┌────────────────────────────────────┐   │
│  │  🔥 320 kcal/khẩu phần           │   │
│  │                                    │   │
│  │  Protein    Carb       Fat        │   │
│  │  ████░░     ███████░   ██░░░      │   │
│  │  9g          52g        7g        │   │
│  │  ·──────── fiber: 4g ──────────·  │   │
│  └────────────────────────────────────┘   │
│  [✅ Đã được chuyên gia dinh dưỡng xác nhận] │  ← Trust badge
│                                            │
│  ── Nguyên liệu (2 người) ──────────────  │
│  Rau củ (2)                               │
│  ☐  Chuối           1 quả    [›]         │  ← Ingredient row with replace
│  ☐  Yến mạch cán dẹt  50g              │
│  ─────────────────────────────────────    │
│  Khác (2)                                 │
│  ☐  Sữa tươi không đường  200ml  [›]    │
│  ☐  Mật ong           1 muỗng canh       │
│                                            │
│  ── Các bước nấu ────────────────────────  │
│  [Tab: Nấu ăn] [Tab: Tips & Lưu ý]       │
│                                            │
│  Bước 1/2 — Nấu yến mạch           ⏱5p  │
│  [Ảnh bước nếu có]                        │
│  Cho yến mạch và sữa vào nồi nhỏ...      │
│  [▶ Bắt đầu 5:00]                         │
│                                            │
│  Bước 2/2 — Hoàn thiện                   │
│  Múc cháo ra bát, xếp chuối...            │
│                                            │
├────────────────────────────────────────────┤
│  [🛒 Thêm vào danh sách mua sắm]          │  ← Sticky bottom CTA
│  [▶ Bắt đầu nấu]                          │  ← Primary sticky CTA
└────────────────────────────────────────────┘
```

### Vùng Màn Hình — Chi Tiết

#### A. Hero Image Gallery

```
Image area:
├── Width: 100% (full-bleed)
├── Height: adaptive ~4:3 ratio (max 257px on 375px phone)
├── Image count: tối đa 5 ảnh (từ DB)
├── Swipe: horizontal swipe để xem ảnh tiếp
├── Page indicator: dots ở bottom of image
│   ├── Active: white filled circle, 8px
│   └── Inactive: white outline circle, 6px
└── BlurHash placeholder khi ảnh đang load

Floating buttons (overlay on image, top-right):
├── Back button [←] — top-left, 40×40px, semi-transparent bg
├── [♡] Favorite button — top-right
│   ├── Default: heart-outline, white, semi-transparent bg
│   └── Saved: heart-fill, color-accent-500
│   └── Animation: bounce scale + fill on tap
└── [···] More options — next to heart
    ├── Dropdown:
    │   • Thêm vào thực đơn hôm nay
    │   • Thêm vào kế hoạch tuần
    │   • Chia sẻ món ăn
    │   • Báo lỗi thông tin
    └── Position: below button (action sheet style)

Status bar:
└── Transparent mode với content màu trắng
    Chuyển về color-white sau khi scroll xuống
```

#### B. Dish Title & Tags

```
Title: text-heading-1 (24pt, Bold)
Margin-top: 16px từ bottom của image area

Diet tags (horizontal scroll):
├── Chips: compact style, height 28px
├── Color coding:
│   ├── Bữa sáng/Trưa/Tối: xem badge colors
│   └── Dietgoal chips: color-primary-100/text
└── Max 6 chips visible, rest hidden behind scroll
```

#### C. Quick Info Row

```
4 info chips nằm ngang:
┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
│ ⏱ 15p │ │🔥 320  │ │👨‍🍳 Dễ │ │ 🥦 Chay│
└────────┘ └────────┘ └────────┘ └────────┘

Prep time: tổng (prepTime + cookTime) từ DB
Calo: dựa vào servings hiện tại
Difficulty: easy/medium/hard → Dễ/Trung bình/Khó
Dietary tag nếu applicable (Chay/Thuần chay/Không gluten)
```

#### D. Servings Stepper (UC-R02 — Điều Chỉnh Khẩu Phần)

```
┌──────────────────────────────────────────┐
│  Cho:    [─]    2 người    [+]           │
└──────────────────────────────────────────┘

[─] button: giảm (disabled khi = 1)
[+] button: tăng (max = 10)
Value: text-heading-3, color-primary-500

Khi thay đổi servings:
1. Re-calculate tất cả ingredient amounts (× multiplier)
2. Re-calculate calo, protein, carb, fat
3. Animation: số cũ fade out, số mới fade in (150ms)
4. Ingredients list update ngay lập tức (no API call — client-side)

Rounding rule:
├── Đơn vị gram: làm tròn đến 5g (VD: 37.5g → 40g)
├── Đơn vị ml: làm tròn đến 10ml
├── Đơn vị quả/củ: làm tròn đến 0.5 (1.5 quả)
├── Đơn vị muỗng: làm tròn đến 0.5
└── Không đổi đơn vị (200g không thành 0.2kg)

Haptic: Light khi thay đổi
```

#### E. Thông Tin Dinh Dưỡng

```
Card:
├── Background: color-cream
├── Radius: radius-lg
└── Padding: 16px

Calo display:
├── "🔥 [X] kcal" — text-heading-2, color-accent-500
└── "/khẩu phần" — text-body-sm, color-gray-600
    Khi servings > 1: "(tổng: [X×N] kcal)"

Macro bars (3 bars: Protein, Carb, Fat):
├── Label: Protein/Carb/Fat — text-label-sm
├── Amount: "9g" — text-body-sm, bold
├── Bar width: proportional to % of total calories
│   Protein: 9 × 4 = 36 kcal / 320 = 11.25% of calo (but displayed proportional to each other)
├── Colors: Protein = #3B82F6, Carb = #F59E0B, Fat = #EF4444
└── Bar height: 8px, radius-full

Chất xơ (Fiber): hiển thị dưới bars nếu có data
"Chất xơ: 4g" — text-body-sm

Trust badge:
┌──────────────────────────────────────────┐
│ ✅ Đã được chuyên gia dinh dưỡng xác nhận│
│    Thông tin mang tính tham khảo         │
└──────────────────────────────────────────┘
Background: color-success-100
Text: color-success-500, text-label-sm
Icon: CheckCircle — Phosphor
Tap: show modal giải thích quy trình xét duyệt
```

#### F. Danh Sách Nguyên Liệu

```
Section header: "Nguyên liệu ([N] người)"
Với N = servings hiện tại

Grouped by category:
• Rau củ (2 items)
• Thịt cá (0 — ẩn nếu không có)
• Ngũ cốc (1 item)
• Gia vị (1 item)
• Khác (1 item)

Category header: text-label-md, color-gray-500, uppercase

Ingredient row:
┌──────────────────────────────────────────────┐
│ ☐  [Icon danh mục?]  Tên nguyên liệu        │
│                       [Số lượng] [Đơn vị]  › │
└──────────────────────────────────────────────┘

Checkbox: (optional shopping list behavior)
├── Khi user tích → gạch ngang text (opacity 60%)
└── Persist trong session (reset khi back)

[›] arrow: xuất hiện nếu có substitutes
└── Tap → Navigate SCR-014 (Substitute suggestions)

Khi servings thay đổi: số lượng animate thay đổi
(counter animation: old → new, 300ms)
```

#### G. Tab: Các Bước Nấu & Tips

```
Tab bar:
[Cách nấu]  [Tips & Lưu ý]
└── Underline indicator, color-primary-500
```

**Tab "Cách nấu" — xem SCR-013**

**Tab "Tips & Lưu ý":**
```
Bổ sung tips tổng quát của món ăn:
• Mẹo chọn nguyên liệu tươi ngon
• Biến tấu món ăn
• Cách bảo quản
• Lưu ý cho người ăn kiêng

Defined trong DB field: tips (nullable text)
```

#### H. Sticky Bottom Actions

```
Position: absolute bottom, above safe area
Background: color-white
Shadow: shadow-lg (upward)
Padding: 12px 16px

Layout:
[🛒 Thêm nguyên liệu vào danh sách mua]  ← Secondary button, full-width
[▶ Bắt đầu nấu]                          ← Primary button, full-width

Tap "Thêm nguyên liệu vào danh sách mua":
→ Add dish ingredients to ShoppingList
→ Toast: "Đã thêm 4 nguyên liệu vào danh sách mua" + [Xem →]

Tap "Bắt đầu nấu":
→ Navigate SCR-013 (Recipe steps — cooking mode)
→ Screen orientation: allow landscape for easier reading while cooking
```

### Accessibility Cho Màn Hình Nấu Ăn

```
Cooking accessibility:
├── Font size: minimum 16pt (dễ đọc từ xa khi đang nấu)
├── High contrast: text contrast ratio ≥ 7:1
├── Screen timeout: auto-prevent screen sleep khi đang ở SCR-013
└── VoiceOver: announce từng bước khi navigate
```

### Data Requirements

```
API: GET /dishes/{dishId}
Response phải bao gồm:
├── id, name, mealType, dietGoals, difficulty
├── prepTime, cookTime, calories, protein, carb, fat, fiber
├── status (must be 'published')
├── images: [] (up to 5 URLs — thumbnail, medium, full)
├── ingredients: [{ name, amount, unit, category, substitutes: [] }]
├── steps: [{ order, title, description, timerSeconds, imageUrl }]
├── tips: nullable text
└── nutritionistApprovedAt: timestamp

Khi servings != 1:
Tính client-side: ingredient.amount × (servings / baseServings)
```

### Edge Cases

| Tình huống | Xử lý |
|:---|:---|
| Dish không có ảnh | Show placeholder: icon fork+knife trên nền color-cream |
| Dish không có tips | Tab "Tips & Lưu ý" ẩn |
| Dish không có steps (lỗi data) | Show "Đang cập nhật công thức..." |
| Ingredient không có substitute | Không show [›] arrow |
| Servings × amount = rất nhỏ (< 0.5) | Hiển thị "< 1 [unit]" hoặc "một ít" |
| Dish là "Ăn chay" nhưng có nguyên liệu thịt | Warning badge (data integrity issue — report to admin) |
| Offline | Serve từ cache nếu đã xem trước. Nếu chưa cache → "Cần kết nối để xem chi tiết" |

---

## SCR-013: XEM CÔNG THỨC & TIMER NẤU ĂN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-013 |
| **Tên màn hình** | Công Thức Nấu — Chế Độ Nấu Ăn |
| **Use Case** | UC-R01 (xem steps), UC-R03 (Timer) |
| **Navigation From** | SCR-012 (tap "Bắt đầu nấu") |
| **Navigation To** | Back → SCR-012 |
| **CR Liên Quan** | CR-005 |

### Mục Đích
Hiển thị từng bước nấu ăn theo dạng có thể cuộn, với timer tích hợp cho các bước cần thời gian. Tối ưu cho việc đọc trong khi đang nấu.

### Layout — Chế Độ Nấu Ăn

```
┌────────────────────────────────────────────┐
│  ← Chi tiết        [Bước 1/2]  [Xong ✓]  │  ← Header
│  Cháo yến mạch chuối mật ong              │
├────────────────────────────────────────────┤
│  [Progress bar: 50% (bước 1/2)]           │  ← Thin progress bar
├────────────────────────────────────────────┤
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │  ● BƯỚC 1                           │  │  ← Step number (active)
│  │  Nấu yến mạch                       │  │
│  │                                      │  │
│  │  [Ảnh bước nếu có — 4:3]            │  │  ← Optional step image
│  │                                      │  │
│  │  Cho yến mạch và sữa tươi vào nồi   │  │  ← text-body-lg (16pt min)
│  │  nhỏ. Đun lửa vừa, khuấy đều tay   │  │
│  │  khoảng 5 phút cho đến khi cháo     │  │
│  │  sánh lại và không còn lợn cợn.     │  │
│  │                                      │  │
│  │  ┌────────────────────────────────┐  │  │  ← Timer component
│  │  │  ⏱  5:00     [▶ Bắt đầu]     │  │  │
│  │  └────────────────────────────────┘  │  │
│  └──────────────────────────────────────┘  │
│                                            │
│  ╔══════════════════════════════════════╗  │
│  ║  ● BƯỚC 2 (preview — next step)     ║  │  ← Preview bước kế tiếp
│  ║  Hoàn thiện...                       ║  │    Opacity 60%, blur nhẹ
│  ╚══════════════════════════════════════╝  │
│                                            │
├────────────────────────────────────────────┤
│  [← Bước trước]         [Bước tiếp theo →]│  ← Navigation buttons
└────────────────────────────────────────────┘
```

### Step Indicator & Progress

```
Progress bar (top):
├── Height: 4px
├── Background: color-gray-200
├── Fill: color-primary-400
└── Fill = (currentStep / totalSteps) × 100%

Step number badge:
├── "● BƯỚC N" — text-label-lg, color-primary-500
└── Số vòng tròn: 32px diameter, color-primary-500 fill, text white

Step title: text-heading-3
Step description: text-body-lg (16pt), color-gray-800, line-height 26px
(Font lớn hơn bình thường để dễ đọc khi đang nấu)
```

### Timer Component (UC-R03)

```
Trạng thái IDLE (chưa bắt đầu):
┌─────────────────────────────────────────┐
│  ⏱  5:00 (thời gian cài)  [▶ Bắt đầu] │
└─────────────────────────────────────────┘
Background: color-cream
Border: 1px color-gray-200

Trạng thái RUNNING:
┌─────────────────────────────────────────┐
│  ⏱  4:32          [⏸ Dừng]  [↺ Reset] │
└─────────────────────────────────────────┘
Background: color-primary-100
Border: 1.5px color-primary-400
Countdown color: color-primary-600, text-heading-2

Trạng thái PAUSED:
┌─────────────────────────────────────────┐
│  ⏱  4:32 (tạm dừng)  [▶ Tiếp]  [↺]   │
└─────────────────────────────────────────┘

Trạng thái WARNING (còn < 30s):
Background: color-warning-100
Countdown: color-warning-600
Timer blinks mỗi giây

Trạng thái DONE:
┌─────────────────────────────────────────┐
│  ✅  Xong! Kiểm tra và tiếp tục bước   │
└─────────────────────────────────────────┘
Background: color-success-100
Animation: confetti nhỏ burst
Notification: Vibration pattern + sound
```

**Timer Background Running (UC-R03):**
```
Khi timer đang chạy và user:
├── Khóa màn hình: timer TIẾP TỤC chạy background (BackgroundTask)
├── Mở app khác: timer TIẾP TỤC
├── Mở lại app: màn hình hiển thị thời gian hiện tại chính xác
└── Khi hết giờ (background):
    ├── Local Notification: "[Tên món] — Bước N xong rồi! 🍳"
    ├── Sound: cooking timer bell sound
    └── Vibration: 3 lần buzz ngắn

Multiple timers:
└── Cho phép chạy nhiều timer cùng lúc (khác bước)
    (VD: vừa nấu rau vừa nấu thịt)
    Mỗi timer có tên bước riêng trong notification
```

### Navigation Giữa Các Bước

```
Swipe gesture:
├── Swipe trái: bước tiếp theo
└── Swipe phải: bước trước

Buttons:
├── [← Bước trước]: disabled khi ở bước 1
└── [Bước tiếp theo →]: "Xong" khi ở bước cuối

Khi tap "Bước tiếp theo":
├── Nếu timer đang chạy → Alert: "Timer đang chạy. Tiếp tục?"
│   [Tiếp tục] → stop timer, go to next step
│   [Để timer chạy] → go to next step, timer continues background
└── Nếu timer không chạy → đi thẳng

Khi tap "Xong ✓" (bước cuối):
→ Animate: Checkmark lớn + "Chúc mừng! Món ăn đã sẵn sàng 🎉"
→ Option: [Xác nhận đã ăn] → MealHistory.isCompleted = true
           [Quay về chi tiết]
→ Analytics: "recipe_completed"
```

### Accessibility — Cooking Mode

```
Screen always-on: Prevent screen sleep khi ở SCR-013
Large text: tất cả text ≥ 16pt
High contrast mode: text-gray-900 trên white
VoiceOver:
├── Announce step number: "Bước 1 trong 5: [title]"
├── Timer: announce remaining time mỗi 60s
└── Completion: announce "Bước N hoàn thành"
```

---

## SCR-014: GỢI Ý THAY THẾ NGUYÊN LIỆU

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-014 |
| **Tên màn hình** | Gợi Ý Thay Thế Nguyên Liệu |
| **Use Case** | UC-R04 |
| **Navigation From** | SCR-012 (tap [›] trên ingredient row) |
| **Navigation To** | Back → SCR-012 |
| **Kiểu** | Bottom Sheet hoặc Modal |

### Mục Đích
Cho phép người dùng xem và chọn nguyên liệu thay thế khi không có nguyên liệu gốc hoặc bị dị ứng.

### Layout — Bottom Sheet

```
┌────────────────────────────────────────────┐  ← Backdrop overlay
├────────────────────────────────────────────┤
│              ─────────                     │  ← Handle
│                                            │
│  Thay thế nguyên liệu                     │  ← text-heading-3
│  ─────────────────────────────────────    │
│                                            │
│  Nguyên liệu gốc:                         │  ← text-label-sm, color-gray-500
│  🥛 Sữa tươi không đường — 200ml         │  ← text-body-lg, color-gray-800
│                                            │
│  Có thể thay bằng:                        │  ← text-label-sm, color-gray-500
│                                            │
│  ┌────────────────────────────────────┐   │
│  │ [○] 🥛 Sữa hạnh nhân — 200ml     │   │  ← Substitute option 1
│  │       ⚠️ Giảm protein, thêm chất  │   │
│  │          béo lành mạnh            │   │
│  │       [💚 Ăn chay thuần]          │   │
│  └────────────────────────────────────┘   │
│                                            │
│  ┌────────────────────────────────────┐   │
│  │ [○] 🥛 Sữa đậu nành — 200ml      │   │  ← Substitute option 2
│  │       ℹ️ Protein tương đương     │   │
│  │          sữa bò                   │   │
│  └────────────────────────────────────┘   │
│                                            │
│  ┌────────────────────────────────────┐   │
│  │ [○] 💧 Nước lọc — 200ml          │   │  ← Emergency substitute
│  │       ⚠️ Giảm đáng kể protein    │   │
│  │          và calo. Không khuyến    │   │
│  │          khích cho chế độ giảm cân│   │
│  └────────────────────────────────────┘   │
│                                            │
│  ┌──────────────────────────────────────┐ │
│  │   Áp dụng thay thế đã chọn         │ │  ← Primary button (disabled until selected)
│  └──────────────────────────────────────┘ │
│  [Giữ nguyên liệu gốc]                   │  ← Ghost button
└────────────────────────────────────────────┘
```

### Substitute Impact Indicators

```
Impact badge icons:
├── ✅ Tương đương: "Giá trị dinh dưỡng tương đương"
├── ⚠️ Chú ý: thay đổi có ý nghĩa về dinh dưỡng
├── ❌ Không khuyến khích: ảnh hưởng lớn đến mục tiêu
└── 💚 Tốt hơn (cho 1 chỉ tiêu cụ thể)

Impact text format: "[Tác động]: [Mô tả ngắn]"
VD: "Giảm 40% protein, tăng 20% chất béo"
Text: text-body-sm, color-warning-600 (cho ⚠️)

Nguồn: substitutes array trong ingredient data của DB
Do Nutritionist cài đặt sẵn khi upload dữ liệu
```

### Behavior

```
Tap "Áp dụng thay thế":
→ Close sheet
→ Trên SCR-012: ingredient row thay đổi:
  ├── Gạch ngang tên gốc: ~~Sữa tươi không đường~~
  ├── Hiển thị thêm dòng: "→ Sữa hạnh nhân 200ml ✓"
  └── Calo & macro recalculate (server-side logic nếu dữ liệu thay đổi)

Persists trong session:
└── Khi navigate về SCR-010, thay thế không được giữ
    (chỉ trong context xem công thức)
    Trong tương lai (Phase 2): "Lưu thay thế cho món này"
```

---

*SDD-ND-2025-001_03 | Module 03: Chi Tiết Món Ăn & Công Thức | v1.0 | NỘI BỘ*
