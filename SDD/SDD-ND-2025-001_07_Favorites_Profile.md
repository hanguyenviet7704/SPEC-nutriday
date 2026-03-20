# SDD-ND-2025-001 | MODULE 07: YÊU THÍCH, HỒ SƠ & LỊCH SỬ
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_07 |
|:---|:---|
| **Module:** | 07 — Yêu Thích, Hồ Sơ & Lịch Sử |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | §6.7 UC-P01, UC-P02; §6.1 UC-U05, UC-U06; BR-002 |
| **Màn hình trong module:** | SCR-021, SCR-022, SCR-023, SCR-024 |

---

## MỤC LỤC MODULE 07

- [SCR-021: Danh Sách Món Yêu Thích](#scr-021-danh-sách-món-yêu-thích)
- [SCR-022: Trang Cá Nhân & Thống Kê](#scr-022-trang-cá-nhân--thống-kê)
- [SCR-023: Chỉnh Sửa Hồ Sơ & Mục Tiêu](#scr-023-chỉnh-sửa-hồ-sơ--mục-tiêu)
- [SCR-024: Lịch Sử Thực Đơn](#scr-024-lịch-sử-thực-đơn)

---

## SCR-021: DANH SÁCH MÓN YÊU THÍCH

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-021 |
| **Tên màn hình** | Món Yêu Thích |
| **Use Case** | UC-P01, UC-R05 |
| **Navigation From** | Tab bar "Yêu thích" |
| **Navigation To** | SCR-012 (dish detail), SCR-020 (explore để thêm) |
| **Kiểu màn hình** | Scrollable grid, Tab Primary |

### Mục Đích
Hiển thị tất cả món ăn người dùng đã lưu yêu thích. Cho phép lọc, thêm vào thực đơn và quản lý danh sách.

### Layout Tổng Thể

```
┌────────────────────────────────────────────┐
│  HEADER                                    │
│  Yêu thích của bạn            [⋮ Quản lý] │
│  48 món đã lưu / 100 (Free)               │  ← Counter + tier limit
│  [||||||||||||||||░░░░]  48%              │  ← Usage bar (only for Free)
├────────────────────────────────────────────┤
│  FILTER CHIPS (Horizontal Scroll)          │
│  [Tất cả] [🌅 Sáng] [☀️ Trưa] [🌙 Tối]  │
│  [🏃 Giảm cân] [🌿 Ăn chay] [...]        │
├────────────────────────────────────────────┤
│  SORT                                      │
│  Sắp xếp: [Mới lưu nhất ▼]               │
├────────────────────────────────────────────┤
│  DISH GRID (2 cột)                         │
│                                            │
│  ┌──────────────┐  ┌──────────────┐        │
│  │ [Ảnh 1:1]   │  │ [Ảnh 1:1]   │        │
│  │         [♥] │  │         [♥] │        │
│  │ Tên món      │  │ Tên món      │        │
│  │ 🔥 kcal|⏱p │  │ 🔥 kcal|⏱p │        │
│  └──────────────┘  └──────────────┘        │
│  ...                                       │
│                                            │
└────────────────────────────────────────────┘
│  [🏠 Hôm nay] [📅 Tuần] [🔍] [♡] [👤]   │
└────────────────────────────────────────────┘
```

### Free vs Premium Limit

```
Free tier:
├── Max 100 saved dishes
├── Usage bar hiển thị: "48/100 món"
├── Khi đạt 90%: banner warning "Gần đầy danh sách yêu thích"
└── Khi đạt 100%: Block lưu thêm
    Toast: "Danh sách yêu thích đầy (100 món)
    Nâng cấp Premium để lưu không giới hạn
    hoặc xóa bớt món không còn dùng"
    [Nâng cấp] [Quản lý danh sách]

Premium tier:
├── Không giới hạn
└── Usage bar ẩn
```

### Dish Card Actions

```
Tap card: → SCR-012 (Dish Detail)
Long press: Action sheet
├── 🍽️ Thêm vào bữa [sáng/trưa/tối] hôm nay
├── 📅 Thêm vào thực đơn tuần
├── 🗑️ Xóa khỏi yêu thích
└── 🔗 Chia sẻ món ăn

[♥] filled icon on card: tap → Unsave confirmation
"Xóa [Tên món] khỏi yêu thích?"
[Hủy] [Xóa]
```

### Quản Lý Danh Sách ([⋮])

```
Dropdown menu:
├── Chọn nhiều (edit mode)
├── Sắp xếp & lọc
└── Xuất danh sách yêu thích (text format)

Edit mode:
├── Cards show checkbox (multi-select)
├── Top: "Đã chọn N món" counter
├── Bottom action bar:
│   [Thêm vào thực đơn tuần]  [Xóa đã chọn]
└── [Thoát chọn nhiều]
```

### Empty State

```
┌──────────────────────────────────────────┐
│ [Heart illustration with cooking utensils]│
│                                          │
│ Chưa có món yêu thích                   │
│                                          │
│ Khi xem công thức, nhấn ♡               │
│ để lưu món bạn thích                    │
│                                          │
│ [Khám phá món ăn ngay →]               │
└──────────────────────────────────────────┘
```

---

## SCR-022: TRANG CÁ NHÂN & THỐNG KÊ

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-022 |
| **Tên màn hình** | Trang Cá Nhân & Thống Kê |
| **Use Case** | UC-P02 |
| **Navigation From** | Tab bar "Tôi" |
| **Navigation To** | SCR-023 (edit profile), SCR-024 (meal history), SCR-025 (settings) |
| **Kiểu màn hình** | Scrollable, Tab Primary |

### Mục Đích
Tổng quan về hoạt động của người dùng, mục tiêu hiện tại, thống kê và truy cập nhanh các cài đặt.

### Layout Tổng Thể

```
┌────────────────────────────────────────────┐
│  PROFILE HEADER                            │
│  ┌────────────────────────────────────┐    │
│  │         [Avatar 80px]              │    │
│  │       Nguyễn Minh Tú              │    │  ← displayName
│  │  [🏃 Giảm cân] [🌿 Ăn chay]      │    │  ← Diet goal chips
│  │         [✏️ Chỉnh sửa hồ sơ]     │    │
│  └────────────────────────────────────┘    │
├────────────────────────────────────────────┤
│  STATS ROW                                 │
│  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  │
│  │  47  │  │  23  │  │  156 │  │  12  │  │
│  │ ngày │  │ tuần │  │ món  │  │  yêu │  │
│  │ dùng │  │ hoạt │  │ đã   │  │ thích│  │
│  │ app  │  │ động │  │  thử │  │      │  │
│  └──────┘  └──────┘  └──────┘  └──────┘  │
│  (Taps: ngày → history, món → explore)    │
├────────────────────────────────────────────┤
│  CALO CHART — 7 ngày gần nhất             │
│  ┌────────────────────────────────────┐    │
│  │  Calo hằng ngày (7 ngày qua)      │    │
│  │                                    │    │
│  │  1800 ──────────────────────────   │    │  ← Target line (dashed)
│  │       ▓   ▓▓  ▓    ▓   ▓▓   ▓    │    │  ← Bar chart
│  │      T7  CN  T2   T3  T4   T5  T6 │    │
│  │  [Mục tiêu: 1.500 kcal/ngày]     │    │
│  └────────────────────────────────────┘    │
│  * Chỉ hiển thị khi user đã "xác nhận     │
│    đã ăn" ít nhất 1 bữa/ngày              │
├────────────────────────────────────────────┤
│  MỤC TIÊU HIỆN TẠI                        │
│  ┌────────────────────────────────────┐    │
│  │ 🏃 Giảm cân                       │    │
│  │ Mục tiêu calo: 1.500 kcal/ngày   │    │
│  │ Số người ăn: 2 người             │    │
│  │ Dị ứng: Hải sản                  │    │
│  │                     [Chỉnh sửa →]│    │
│  └────────────────────────────────────┘    │
├────────────────────────────────────────────┤
│  QUICK LINKS                               │
│  ┌────────────────────────────────────┐    │
│  │ 📋 Lịch sử thực đơn            →  │    │  ← → SCR-024
│  │ ♡  Món yêu thích (48)          →  │    │  ← → SCR-021
│  │ 🔔 Cài đặt thông báo           →  │    │  ← → SCR-026
│  │ ⚙️ Cài đặt                     →  │    │  ← → SCR-025
│  │ 🆙 Nâng cấp Premium            →  │    │  ← Free tier only
│  │ ℹ️ Về NutriDay                  →  │    │
│  │ ❓ Trợ giúp & Phản hồi         →  │    │
│  └────────────────────────────────────┘    │
├────────────────────────────────────────────┤
│  ACCOUNT ACTIONS                           │
│  ┌────────────────────────────────────┐    │
│  │ 🔐 Đổi mật khẩu               →  │    │
│  │ 🗑️ Xóa tài khoản              →  │    │  ← Danger zone
│  │ 🚪 Đăng xuất                      │    │
│  └────────────────────────────────────┘    │
└────────────────────────────────────────────┘
│  [🏠 Hôm nay] [📅 Tuần] [🔍] [♡] [👤]   │
└────────────────────────────────────────────┘
```

### Stats Row

| Metric | Mô tả | API |
|:---|:---|:---|
| Ngày dùng app | Số ngày từ ngày đăng ký đến hôm nay (kể cả ngày không mở) | Tính từ `user.createdAt` |
| Tuần hoạt động | Số tuần user có ít nhất 1 DayPlan | Count weeks in DayPlan |
| Món đã thử | Số món unique đã xuất hiện trong DayPlan (không cần confirm ăn) | Count distinct dishId in DayPlan |
| Yêu thích | Số SavedMeal | Count SavedMeal |

### Calo Chart

```
Library: Victory Native hoặc react-native-chart-kit
Chart type: Bar chart
Data: MealHistory của 7 ngày (tính tổng calo từ các bữa isCompleted=true)

Target line: dashed horizontal line tại user's calo target
Bar colors:
├── Under target (< 85%): color-info-500 (blue)
├── In range (85-115%): color-success-500 (green)
└── Over target (> 115%): color-error-500 (red)

"Chưa có dữ liệu đủ":
└── Khi < 3 ngày có data → hiện placeholder:
    "Xác nhận đã ăn xong bữa để theo dõi calo thực tế"
```

### Xóa Tài Khoản

```
Tap "Xóa tài khoản":
Step 1: Warning modal
┌────────────────────────────────────────────┐
│  ⚠️ Xóa tài khoản                         │
│                                            │
│  Hành động này không thể hoàn tác.        │
│  Toàn bộ dữ liệu của bạn sẽ bị xóa:      │
│  • Lịch sử thực đơn                       │
│  • Danh sách yêu thích                    │
│  • Cài đặt và mục tiêu                    │
│                                            │
│  [Hủy]           [Tiếp tục xóa]           │
└────────────────────────────────────────────┘

Step 2: Xác nhận bằng mật khẩu / email
┌────────────────────────────────────────────┐
│  Nhập mật khẩu để xác nhận xóa tài khoản  │
│  [Password input]                          │
│  [Hủy]           [Xóa vĩnh viễn]          │
└────────────────────────────────────────────┘

Backend:
├── Soft delete: user.status = 'deleted', user.deletedAt = now()
├── Anonymize PII: email → anon_[hash], displayName = 'Người dùng đã xóa'
├── Keep: MealHistory (anonymized) cho stats
└── Delete: SavedMeal, DayPlan, tokens
Compliance: NFR-06 (Nghị định 13/2023)
```

---

## SCR-023: CHỈNH SỬA HỒ SƠ & MỤC TIÊU

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-023 |
| **Tên màn hình** | Chỉnh Sửa Hồ Sơ |
| **Use Case** | UC-U05 |
| **Navigation From** | SCR-022 (tap "Chỉnh sửa hồ sơ" hoặc "Chỉnh sửa" trong mục tiêu) |
| **Navigation To** | Back → SCR-022 (với data refreshed) |

### Layout

```
┌────────────────────────────────────────────┐
│  ← Chỉnh sửa hồ sơ                [Lưu]   │  ← Sticky header
├────────────────────────────────────────────┤
│  AVATAR SECTION                            │
│         [Avatar 96px]                      │
│         [📷 Thay đổi ảnh]                 │
│                                            │
│  Họ và tên                                │
│  [Nguyễn Minh Tú              ]            │
│                                            │
│  ─── Mục tiêu sức khỏe ──────────────     │
│  (Diet Goal Chips — multi-select)          │
│  [🏃 Giảm cân ✓] [🌿 Ăn chay ✓]         │
│  [💪 Tăng cân  ] [🩺 Tiểu đường ]        │
│  ...                                       │
│                                            │
│  ─── Số người ăn ─────────────────────    │
│  [─]  2 người  [+]                        │
│                                            │
│  ─── Dị ứng thực phẩm ─────────────────  │
│  [Hải sản ×] [+ Thêm...]                 │
│                                            │
│  ─── Thông báo ──────────────────────────  │
│  Nhắc nhở nấu ăn        [Toggle ON/OFF]   │
│  Thông báo hệ thống      [Toggle ON/OFF]  │
│                                            │
└────────────────────────────────────────────┘
```

### Save Logic

```
Tap [Lưu]:
→ Validate: name không rỗng
→ API PATCH /user/profile { displayName, dietGoals, servings, allergies }
→ Loading state trên button
→ Success:
  ├── Toast: "Đã lưu thay đổi"
  ├── Navigate back → SCR-022
  └── SCR-022 refresh data
→ Error:
  └── Toast error + keep on screen

Thay đổi mục tiêu sức khỏe:
└── Sau khi lưu → suggestion engine chạy lại cho ngày hôm nay
    Toast: "Đã cập nhật mục tiêu. Thực đơn hôm nay sẽ được làm mới."
    Nếu user đã có DayPlan hôm nay → hỏi:
    "Bạn có muốn làm mới thực đơn hôm nay không?"
    [Không, giữ nguyên] [Làm mới thực đơn]
```

### Avatar Upload

```
Tap "Thay đổi ảnh":
→ Action sheet:
  • 📷 Chụp ảnh mới
  • 🖼️ Chọn từ thư viện
  • 🗑️ Xóa ảnh (nếu có)

Upload flow:
→ Native image picker
→ Client-side crop: 1:1 ratio, min 200×200px
→ Upload to S3 via presigned URL
→ CloudFront resize: 200px (thumbnail), 400px (display)
→ Update user.avatarUrl
→ Preview ngay lập tức (optimistic UI)
Max file size: 5MB
```

---

## SCR-024: LỊCH SỬ THỰC ĐƠN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-024 |
| **Tên màn hình** | Lịch Sử Thực Đơn |
| **Use Case** | UC-U06 |
| **Navigation From** | SCR-022 (quick link "Lịch sử thực đơn") |
| **Navigation To** | SCR-012 (dish detail) |

### Mục Đích
Hiển thị thực đơn các ngày đã qua, cho phép xem lại công thức và lưu lại yêu thích.

### Layout

```
┌────────────────────────────────────────────┐
│  ← Lịch sử thực đơn                       │
│  [Tháng 3/2025 ▼]                         │  ← Month picker
├────────────────────────────────────────────┤
│                                            │
│  Tuần này                                 │  ← Section header
│  ─────────────────────────────────────    │
│                                            │
│  ┌────────────────────────────────────┐   │  ← Day group
│  │ Thứ Năm, 19/3          🔥 1.290 kcal│  │
│  │ ─────────────────────────────────  │   │
│  │ 🌅 Cháo yến mạch...   320 kcal [♡]│   │
│  │ ☀️ Cơm gà xào sả ớt  580 kcal [♡]│   │
│  │ 🌙 Canh chua cá lóc   390 kcal [♡]│   │
│  └────────────────────────────────────┘   │
│                                            │
│  ┌────────────────────────────────────┐   │
│  │ Thứ Tư, 18/3           🔥 1.420 kcal│  │
│  │ ...                                │   │
│  └────────────────────────────────────┘   │
│                                            │
│  Tuần trước                               │
│  ─────────────────────────────────────    │
│  [7 ngày của tuần trước, tương tự]        │
│                                            │
│  [Xem thêm — Tháng 3/2025]               │  ← Load more by week
│                                            │
└────────────────────────────────────────────┘
```

### Phân Trang

```
Free tier: Xem tối đa 30 ngày gần nhất
Premium: Không giới hạn

Hiển thị: 7 ngày/trang (1 tuần)
Load more: tap "Xem thêm"

Khi đạt giới hạn Free:
┌──────────────────────────────────────────┐
│ 🔒 Xem lịch sử từ tháng trước          │
│    cần gói Premium                      │
│    [Nâng cấp Premium]                   │
└──────────────────────────────────────────┘
```

### Meal Row Actions

```
Tap [♡] on meal row:
→ Save/Unsave favorite (same behavior as elsewhere)

Tap meal name:
→ Navigate SCR-012 (Dish Detail, readonly nếu muốn)

Tap dish trong history:
→ Bottom sheet options:
  • Xem công thức
  • Lưu yêu thích / Bỏ yêu thích
  • Thêm vào thực đơn hôm nay
  • Thêm vào tuần này
```

### Tháng Picker

```
Dropdown: "Tháng 3/2025 ▼"
Options: last 12 months (Free) / all history (Premium)
Format: "Tháng M/YYYY"
Default: current month
```

---

*SDD-ND-2025-001_07 | Module 07: Yêu Thích, Hồ Sơ & Lịch Sử | v1.0 | NỘI BỘ*
