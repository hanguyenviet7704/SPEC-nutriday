# SDD-ND-2025-001 | MODULE 08: ADMIN CMS — QUẢN LÝ NỘI DUNG
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_08 |
|:---|:---|
| **Module:** | 08 — Admin CMS |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | §6.8; BR-004, BR-008; §11.2 TC-008 |
| **Nền tảng:** | Web App (Desktop-first, ReactJS) |
| **Màn hình trong module:** | ADM-001 → ADM-006 |

---

## MỤC LỤC MODULE 08

- [Tổng Quan Admin CMS](#tổng-quan-admin-cms)
- [ADM-001: Dashboard Tổng Quan](#adm-001-dashboard-tổng-quan)
- [ADM-002: Danh Sách Món Ăn](#adm-002-danh-sách-món-ăn)
- [ADM-003: Thêm/Sửa Món Ăn](#adm-003-thêmsửa-món-ăn)
- [ADM-004: Import Excel Hàng Loạt](#adm-004-import-excel-hàng-loạt)
- [ADM-005: Nutritionist Review](#adm-005-nutritionist-review)
- [ADM-006: Báo Cáo & Thống Kê](#adm-006-báo-cáo--thống-kê)
- [Phân Quyền Admin](#phân-quyền-admin)

---

## TỔNG QUAN ADMIN CMS

### Phân Quyền

| Role | Quyền hạn | Màn hình truy cập |
|:---|:---|:---|
| **Super Admin** | Toàn quyền: quản lý user, phân quyền, xóa, publish | Tất cả |
| **Content Editor** | Thêm/sửa món (tạo Draft, gửi duyệt), không publish, không xóa | ADM-001 (limited), ADM-002, ADM-003, ADM-004 |
| **Nutritionist** | Review dinh dưỡng, Approve/Reject, không tạo/sửa | ADM-001 (limited), ADM-002 (readonly), ADM-005 |

### Layout Chung (Shell)

```
┌────────────────────────────────────────────────────────────────────┐
│  TOPBAR                                                            │
│  [🍽️ NutriDay Admin]    [Search...]    [🔔 Notif] [👤 Username ▼]│
├────────────────────────────────────────────────────────────────────┤
│ SIDEBAR │  MAIN CONTENT AREA                                       │
│ ──────  │  ──────────────────────────────────────────────────────  │
│ 📊 Dashboard      │                                               │
│ 🍜 Món ăn    ▼   │                                               │
│   ├ Danh sách     │                                               │
│   ├ Thêm mới      │                                               │
│   ├ Import Excel  │                                               │
│   └ Chờ duyệt     │                                               │
│ 📈 Báo cáo        │                                               │
│ 👥 Người dùng     │  (Super Admin only)                           │
│ ⚙️ Cài đặt        │  (Super Admin only)                           │
└─────────────────────────────────────────────────────────────────┘
```

**Responsive:** Desktop ≥ 1024px: sidebar expanded (240px). Tablet 768-1024px: sidebar collapsed (icons only). Mobile: hamburger menu.

---

## ADM-001: DASHBOARD TỔNG QUAN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | ADM-001 |
| **Tên màn hình** | Admin Dashboard |
| **Quyền truy cập** | All roles (nội dung khác nhau) |

### Layout

```
┌────────────────────────────────────────────────────────────┐
│  Dashboard — Tổng quan hệ thống NutriDay                  │
│  Cập nhật lần cuối: 19/03/2025 14:32                      │
├────────────────────────────────────────────────────────────┤
│  KPI CARDS ROW 1                                           │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │ 247      │  │ 240 min  │  │ 12       │  │ 3        │  │
│  │ Tổng     │  │ Target   │  │ Chờ      │  │ Cần bổ   │  │
│  │ món ăn   │  │ đạt 103% │  │ duyệt    │  │ sung ảnh │  │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  │
├────────────────────────────────────────────────────────────┤
│  COVERAGE TABLE — Tỉ lệ phủ theo chế độ ăn               │
│  ┌────────────────────────────────────────────────────┐   │
│  │ Chế độ          │ Số món │ Min 30 │ Status        │   │
│  │ ─────────────────────────────────────────────────  │   │
│  │ 🏃 Giảm cân     │  38    │  30    │ ✅ Đạt        │   │
│  │ 💪 Tăng cân     │  31    │  30    │ ✅ Đạt        │   │
│  │ 🩺 Tiểu đường   │  28    │  30    │ ⚠️ Còn thiếu 2│   │
│  │ 🥗 Eat Clean    │  35    │  30    │ ✅ Đạt        │   │
│  │ 🌿 Ăn chay      │  25    │  30    │ ❌ Thiếu 5   │   │
│  │ 💚 Healthy      │  40    │  30    │ ✅ Đạt        │   │
│  │ 🍚 Ít tinh bột  │  30    │  30    │ ✅ Đạt        │   │
│  │ 🥩 Nhiều protein│  20    │  30    │ ❌ Thiếu 10  │   │
│  └────────────────────────────────────────────────────┘   │
│  [⚠️ 2 chế độ ăn chưa đủ 30 món — cần bổ sung gấp]      │
├────────────────────────────────────────────────────────────┤
│  ROW 2: CHARTS                                            │
│  ┌───────────────────────┐  ┌───────────────────────────┐ │
│  │ Top 10 món được chọn │  │ Tỉ lệ Đổi món (7 ngày)   │ │
│  │ nhiều nhất            │  │                           │ │
│  │ [Horizontal bar chart]│  │ [Donut chart]             │ │
│  │ 1. Phở bò — 2.847    │  │ 32% tỉ lệ đổi           │ │
│  │ 2. Cơm gà — 2.234    │  │ (Target < 40%)           │ │
│  │ ...                   │  │                           │ │
│  └───────────────────────┘  └───────────────────────────┘ │
├────────────────────────────────────────────────────────────┤
│  ALERTS & ACTIONS                                         │
│  ┌────────────────────────────────────────────────────┐   │
│  │ ⚠️ 12 món đang chờ duyệt dinh dưỡng [Duyệt ngay →]│   │
│  │ ❌ 3 món thiếu ảnh minh họa [Xem danh sách →]      │   │
│  │ 🔴 Chế độ "Ăn chay" chỉ còn 25 món (chưa đủ 30)  │   │
│  └────────────────────────────────────────────────────┘   │
└────────────────────────────────────────────────────────────┘
```

### KPI Card Colors

```
Green (#16A34A): Đạt target (247/240 = 103%)
Orange (#D97706): Cảnh báo (< 10% dưới target)
Red (#DC2626): Chưa đạt hoặc cần action ngay
```

---

## ADM-002: DANH SÁCH MÓN ĂN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | ADM-002 |
| **Tên màn hình** | Quản Lý Danh Sách Món Ăn |
| **Quyền truy cập** | Super Admin: toàn quyền; Content Editor: CRUD; Nutritionist: readonly |

### Layout

```
┌────────────────────────────────────────────────────────────┐
│  Quản lý món ăn                                           │
│  [+ Thêm món mới]  [📥 Import Excel]  [📤 Export CSV]    │
├────────────────────────────────────────────────────────────┤
│  FILTERS                                                   │
│  [Search: tên món...]  [Trạng thái ▼]  [Chế độ ăn ▼]    │
│  [Bữa ăn ▼]  [Người tạo ▼]  [Sắp xếp ▼]  [Xóa bộ lọc] │
├────────────────────────────────────────────────────────────┤
│  BULK ACTIONS (hiện khi chọn nhiều)                       │
│  Đã chọn 5: [Publish] [Archive] [Xóa]                    │
├────────────────────────────────────────────────────────────┤
│  TABLE                                                     │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ ☐ │ Ảnh│ Tên món       │Bữa│ Calo│ Trạng thái│ Người│ │
│  │   │    │               │   │     │            │ tạo  │ │
│  │─────────────────────────────────────────────────────│  │
│  │ ☐ │[🖼]│ Cháo yến mạch │ S │ 320 │ ✅ Published│ LAN │ │
│  │   │    │ chuối mật ong │   │     │            │      │ │
│  │   │    │           [✏️][👁️][🗑️]│             │      │ │
│  │─────────────────────────────────────────────────────│  │
│  │ ☐ │[🖼]│ Phở bò        │T/T│ 480 │ 🕐 Chờ duyệt│ HUY│ │
│  │─────────────────────────────────────────────────────│  │
│  │ ☐ │[❌]│ Bún thịt nướng│ T │ 550 │ ✏️ Draft    │ HUY│ │
│  │   │    │ (thiếu ảnh)   │   │     │            │      │ │
│  └──────────────────────────────────────────────────────┘  │
│                                                            │
│  Hiển thị 1-20 trong 247 món  [< 1 2 3 ... 13 >]        │
└────────────────────────────────────────────────────────────┘
```

### Status Badges

| Status | Badge | Mô tả |
|:---|:---|:---|
| `draft` | ✏️ Nháp | Đang soạn thảo, chưa gửi duyệt |
| `pending_review` | 🕐 Chờ duyệt | Đã gửi Nutritionist review |
| `published` | ✅ Đã publish | Hiển thị cho người dùng |
| `rejected` | ❌ Từ chối | Nutritionist từ chối, cần sửa |
| `archived` | 📦 Lưu trữ | Ẩn khỏi người dùng nhưng không xóa |

### Row Actions

```
[✏️] Edit: Mở ADM-003 (form edit)
[👁️] Preview: Xem trước như người dùng thấy (readonly modal)
[🗑️] Delete:
├── Super Admin: Xóa vĩnh viễn (với confirm)
├── Content Editor: Soft delete (archive)
└── Không cho xóa nếu dish đã xuất hiện trong DayPlan/MealHistory
    → "Không thể xóa món đã có trong lịch sử người dùng. Chỉ có thể lưu trữ."
    Action: Archive (ẩn khỏi gợi ý, vẫn hiển thị trong history)
```

---

## ADM-003: THÊM/SỬA MÓN ĂN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | ADM-003 |
| **Tên màn hình** | Form Thêm/Sửa Món Ăn |
| **Quyền truy cập** | Super Admin, Content Editor |

### Layout — Form Nhiều Bước (Tabs)

```
┌────────────────────────────────────────────────────────────┐
│  ← Danh sách         Thêm món ăn mới    [Lưu nháp] [Gửi duyệt]│
├────────────────────────────────────────────────────────────┤
│  TABS: [Thông tin cơ bản] [Nguyên liệu] [Bước nấu] [Ảnh]  │
├────────────────────────────────────────────────────────────┤
│  TAB 1: THÔNG TIN CƠ BẢN                                  │
│                                                            │
│  Tên món ăn *                                             │
│  [Cháo yến mạch chuối mật ong                    ]        │
│                                                            │
│  Slug (URL) — tự động tạo từ tên:                         │
│  [chao-yen-mach-chuoi-mat-ong          ] [Tạo lại]        │
│                                                            │
│  Bữa ăn (chọn nhiều) *                                    │
│  [☑ Bữa sáng] [☐ Bữa trưa] [☐ Bữa tối]                  │
│                                                            │
│  Chế độ ăn phù hợp (chọn nhiều) *                        │
│  [☑ Giảm cân] [☑ Eat Clean] [☑ Healthy] [☑ Tiểu đường] │
│  [☐ Tăng cân] [☑ Ăn chay] [☐ Ít tinh bột] [☐ Nhiều P]  │
│                                                            │
│  Mức độ khó *                                             │
│  (○) Dễ  ( ) Trung bình  ( ) Khó                         │
│                                                            │
│  Thời gian *                                              │
│  Chuẩn bị: [5] phút     Nấu: [10] phút                   │
│                                                            │
│  THÔNG TIN DINH DƯỠNG (cho 1 khẩu phần) *               │
│  ┌────────────┬────────────┬────────────┬────────────┐    │
│  │ Calo (kcal)│ Protein (g)│ Carb (g)   │ Fat (g)    │    │
│  │ [320      ]│ [9        ]│ [52       ]│ [7        ]│    │
│  └────────────┴────────────┴────────────┴────────────┘    │
│  Chất xơ (g): [4]  (tùy chọn)                            │
│                                                            │
│  Tags (tùy chọn):                                         │
│  [Không chiên ×] [Nhanh ×] [+ Thêm tag]                  │
│                                                            │
│  Mô tả ngắn (hiển thị trong tooltip):                     │
│  [Textarea — max 200 chars                  ]              │
│                                                            │
│  Tips & Lưu ý:                                            │
│  [Rich text editor (Markdown supported)     ]              │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### Tab 2: Nguyên Liệu

```
┌────────────────────────────────────────────────────────────┐
│  TAB 2: NGUYÊN LIỆU                                       │
│  Cơ sở: 1 khẩu phần (người dùng sẽ điều chỉnh)          │
│  [+ Thêm nguyên liệu]  [📋 Paste từ Excel]               │
├────────────────────────────────────────────────────────────┤
│  Nguyên liệu 1:                                           │
│  Tên*: [Yến mạch cán dẹt        ] Category: [Ngũ cốc ▼]  │
│  Số lượng*: [50] Đơn vị*: [gram ▼]                       │
│  Thay thế: [+ Thêm gợi ý thay thế]                       │
│    ┌──────────────────────────────────────────────────┐   │
│    │ Tên thay thế: [Hạt quinoa    ] Lượng: [45] gram  │   │
│    │ Ghi chú: [Nhiều protein hơn 20%           ]      │   │
│    │ [Xóa thay thế]                                   │   │
│    └──────────────────────────────────────────────────┘   │
│  [🗑️ Xóa nguyên liệu]                                    │
│  ─────────────────────────────────────────────────────    │
│  Nguyên liệu 2:                                           │
│  Tên*: [Chuối                    ] Category: [Rau củ ▼]   │
│  Số lượng*: [1] Đơn vị*: [quả ▼]                        │
│  ...                                                       │
│  ─────────────────────────────────────────────────────    │
│  [+ Thêm nguyên liệu]                                     │
└────────────────────────────────────────────────────────────┘
```

### Tab 3: Bước Nấu

```
┌────────────────────────────────────────────────────────────┐
│  TAB 3: BƯỚC NẤU                                          │
│  [+ Thêm bước]  [Sắp xếp bằng kéo thả]                  │
├────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────────────────────────────────┐  │
│  │ ⠿ BƯỚC 1                              [🗑️ Xóa bước] │  │  ← ⠿ = drag handle
│  │ Tiêu đề*: [Nấu yến mạch                            ]│  │
│  │ Mô tả*: [Cho yến mạch và sữa vào nồi nhỏ, đun lửa │  │
│  │           vừa khoảng 5 phút, khuấy đều tay.         ]│  │
│  │ Timer (giây, để trống nếu không cần): [300        ] │  │
│  │ Ảnh bước (tùy chọn): [📁 Upload ảnh]              │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ ⠿ BƯỚC 2                              [🗑️]          │  │
│  │ Tiêu đề*: [Hoàn thiện                              ] │  │
│  │ Mô tả*: [Múc cháo ra bát, xếp chuối thái lát,      │  │
│  │           rưới mật ong. Dùng ngay khi còn nóng.     ]│  │
│  │ Timer: [   ] (để trống = không timer)               │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                            │
│  [+ Thêm bước]                                            │
└────────────────────────────────────────────────────────────┘
```

### Tab 4: Ảnh

```
┌────────────────────────────────────────────────────────────┐
│  TAB 4: ẢNH MINH HỌA                                      │
│  Tối đa 5 ảnh. Kéo thả để sắp xếp. Ảnh đầu = ảnh chính. │
│                                                            │
│  [Drop zone: kéo ảnh vào đây hoặc]                       │
│  ┌──────────────────────────────────────────────────────┐  │
│  │                                                      │  │
│  │          📁 Chọn file ảnh                           │  │
│  │     (JPG, PNG, WEBP — tối đa 5MB/ảnh)              │  │
│  │                                                      │  │
│  └──────────────────────────────────────────────────────┘  │
│                                                            │
│  Ảnh đã tải lên (2/5):                                    │
│  ┌──────┐ ┌──────┐ ┌──────────────┐                       │
│  │[Ảnh1]│ │[Ảnh2]│ │  + Thêm ảnh │                       │
│  │ ⠿ ×  │ │ ⠿ ×  │ │              │                       │
│  └──────┘ └──────┘ └──────────────┘                       │
│  ↑ Ảnh 1 là ảnh chính hiển thị cho người dùng            │
│                                                            │
│  ⚠️ Yêu cầu: Tỷ lệ 16:9 hoặc 4:3. Tối thiểu 1200×675px │
└────────────────────────────────────────────────────────────┘
```

### Workflow Submit

```
[Lưu nháp]:
→ Save với status = 'draft'
→ Toast: "Đã lưu nháp"
→ Không gửi cho Nutritionist review

[Gửi duyệt]:
→ Validate tất cả required fields
├── Lỗi → Highlight tab có lỗi + scroll to first error
└── Hợp lệ → Confirm modal:
    "Gửi [Tên món] cho Nutritionist review?
     Sau khi gửi, Nutritionist sẽ kiểm tra thông tin
     dinh dưỡng trước khi publish."
    [Hủy] [Gửi review]
    → status = 'pending_review'
    → Notify Nutritionist (email + in-app notification)
    → Toast: "Đã gửi review thành công"
    → Navigate back to ADM-002
```

### Validation Rules

| Field | Rule |
|:---|:---|
| Tên món | Required, max 200 chars, unique |
| Bữa ăn | Min 1 selected |
| Chế độ ăn | Min 1 selected |
| Calo | Required, > 0, reasonable range 50-3000 |
| Protein/Carb/Fat | Required, ≥ 0 |
| Nguyên liệu | Min 2 ingredients |
| Bước nấu | Min 1 step |
| Ảnh | Min 1 image required to submit for review |

---

## ADM-004: IMPORT EXCEL HÀNG LOẠT

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | ADM-004 |
| **Tên màn hình** | Import Hàng Loạt Từ Excel |
| **Quyền truy cập** | Super Admin, Content Editor |

### Mục Đích
Cho phép nhập hàng loạt dữ liệu món ăn từ file Excel template, đặc biệt hữu ích khi cần nhập 200+ món ăn ban đầu.

### Layout

```
┌────────────────────────────────────────────────────────────┐
│  Import món ăn hàng loạt                                  │
│                                                            │
│  BƯỚC 1: Tải template Excel                              │
│  ┌────────────────────────────────────────────────────┐   │
│  │  📥 Tải file template Excel                        │   │
│  │  [NutriDay_Import_Template_v1.0.xlsx]              │   │
│  │                                                    │   │
│  │  Template bao gồm:                                │   │
│  │  • Sheet "Dishes" — thông tin cơ bản + dinh dưỡng │   │
│  │  • Sheet "Ingredients" — nguyên liệu              │   │
│  │  • Sheet "Steps" — các bước nấu                   │   │
│  │  • Sheet "Lookup" — danh sách đơn vị, danh mục   │   │
│  │  • Sheet "Instructions" — hướng dẫn điền          │   │
│  └────────────────────────────────────────────────────┘   │
│                                                            │
│  BƯỚC 2: Upload file đã điền                            │
│  [Drop zone hoặc Browse file]                             │
│  [CT-ND-2025-001_Import_v1.0.xlsx — 248 KB]  [×]        │
│                                                            │
│  BƯỚC 3: Preview & Validate                             │
│  [Kiểm tra file →]                                        │
│                                                            │
│  PREVIEW RESULTS:                                         │
│  ✅ 45 món hợp lệ                                         │
│  ⚠️ 3 món có cảnh báo (thiếu ảnh — sẽ nhập với ảnh mặc định)│
│  ❌ 2 món lỗi (thiếu trường bắt buộc)                     │
│  ─────────────────────────────────────────────────────    │
│  Lỗi chi tiết:                                            │
│  Dòng 15 — Cà ri gà: Thiếu calo (cột D)                 │
│  Dòng 28 — Phở bò: Tên trùng với món đã có trong DB     │
│                                                            │
│  [Xem preview đầy đủ (45 món)]                           │
│  ─────────────────────────────────────────────────────    │
│  [Hủy]   [Import 45 món hợp lệ — bỏ qua 2 lỗi]         │
└────────────────────────────────────────────────────────────┘
```

### Excel Template Schema

**Sheet "Dishes":**
| Column | Field | Required | Format |
|:---|:---|:---|:---|
| A | dish_id (internal reference) | Yes | Text (để link với sheets khác) |
| B | name | Yes | Text |
| C | meal_type | Yes | breakfast / lunch / dinner / breakfast,lunch |
| D | diet_goals | Yes | weight_loss,eat_clean (comma separated) |
| E | difficulty | Yes | easy / medium / hard |
| F | prep_time | Yes | Integer (minutes) |
| G | cook_time | Yes | Integer (minutes) |
| H | calories | Yes | Integer |
| I | protein | Yes | Decimal |
| J | carb | Yes | Decimal |
| K | fat | Yes | Decimal |
| L | fiber | No | Decimal |
| M | tags | No | Comma separated |
| N | description | No | Text (max 200) |
| O | tips | No | Text (Markdown) |

**Sheet "Ingredients":**
| Column | Field | Required |
|:---|:---|:---|
| A | dish_id (reference) | Yes |
| B | name | Yes |
| C | amount | Yes |
| D | unit | Yes |
| E | category | Yes |
| F | substitute_name | No |
| G | substitute_amount | No |
| H | substitute_unit | No |
| I | substitute_note | No |

**Sheet "Steps":**
| Column | Field | Required |
|:---|:---|:---|
| A | dish_id (reference) | Yes |
| B | order | Yes |
| C | title | Yes |
| D | description | Yes |
| E | timer_seconds | No |

### Import Process

```
Backend processing:
1. Parse Excel (xlsx library)
2. Validate each row (required fields, data types, ranges)
3. Check duplicates (by name, case-insensitive)
4. Generate preview report
5. User confirms → batch insert:
   - All dishes → status: 'draft' (không phải pending_review)
   - Content Editor phải gửi từng món cho review
   OR
   - Super Admin có thể chọn: import as 'pending_review'
     → Auto-notify Nutritionist

Import rate: max 200 rows/batch
Timeout: 60 seconds
Progress bar: real-time (Server-Sent Events)
```

---

## ADM-005: NUTRITIONIST REVIEW

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | ADM-005 |
| **Tên màn hình** | Review Thông Tin Dinh Dưỡng |
| **Quyền truy cập** | Nutritionist, Super Admin |

### Mục Đích
Giao diện cho Nutritionist review và approve/reject từng món ăn về tính chính xác dinh dưỡng trước khi publish.

### Layout

```
┌────────────────────────────────────────────────────────────┐
│  Review dinh dưỡng — 12 món đang chờ                     │
│  [Lọc: Tất cả ▼] [Sắp xếp: Gửi sớm nhất ▼]            │
├────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────────────────────────────────┐  │
│  │ PENDING: Cháo yến mạch chuối mật ong               │  │
│  │ Gửi bởi: Lan Anh — 19/03/2025 10:30               │  │
│  │                                                    │  │
│  │ [Xem đầy đủ ▼]                                     │  │
│  │ ─────────────────────────────────────────────────   │  │
│  │ Thông tin dinh dưỡng cần review:                   │  │
│  │ ┌──────────────────────────────────────────────┐   │  │
│  │ │ Calo: 320 kcal  Protein: 9g  Carb: 52g      │   │  │
│  │ │ Fat: 7g  Fiber: 4g                           │   │  │
│  │ └──────────────────────────────────────────────┘   │  │
│  │ Chế độ ăn được gán: Giảm cân, Eat Clean, Tiểu đường│  │
│  │                                                    │  │
│  │ [📋 Ghi chú của Content Editor]                    │  │
│  │                                                    │  │
│  │ Nhận xét của Nutritionist (bắt buộc khi Từ chối): │  │
│  │ [Textarea                                        ] │  │
│  │                                                    │  │
│  │ Checklist:                                         │  │
│  │ ☐ Calo hợp lý với thành phần nguyên liệu          │  │
│  │ ☐ Macro (P/C/F) đã cân đối                        │  │
│  │ ☐ Phù hợp với các chế độ ăn được gán              │  │
│  │ ☐ Không có nguyên liệu nguy hiểm cho chế độ ăn   │  │
│  │   (VD: đường cao cho tiểu đường)                  │  │
│  │                                                    │  │
│  │ [❌ Từ chối & Yêu cầu sửa]  [✅ Approve & Publish]│  │
│  └──────────────────────────────────────────────────────┘  │
│                                                            │
│  [Món tiếp theo →]                                        │
└────────────────────────────────────────────────────────────┘
```

### Approve Flow

```
Tap [✅ Approve & Publish]:
→ Confirm: "Publish [Tên món] ngay?"
  [Hủy] [Publish]
→ dish.status = 'published'
→ dish.nutritionistApprovedAt = now()
→ dish.approvedBy = nutritionistId
→ Notify Content Editor: "[Tên món] đã được approve và publish"
→ Next item (auto-advance)
→ Dish xuất hiện trong suggestion engine ≤ 5 phút (cache invalidation)
```

### Reject Flow

```
Tap [❌ Từ chối & Yêu cầu sửa]:
→ Validation: nhận xét không được rỗng
→ dish.status = 'rejected'
→ dish.rejectionReason = textarea content
→ Notify Content Editor:
  Email + in-app: "[Tên món] bị từ chối bởi Nutritionist.
  Lý do: [reason].
  Vui lòng sửa và gửi lại."
→ Content Editor mở lại form → sửa → gửi review lại
   status: draft → pending_review
```

### Escalation cho Chế Độ Tiểu Đường

```
Khi món có dietGoal = 'diabetes':
├── Checklist bổ sung thêm:
│   ☐ Không có đường tinh luyện cao
│   ☐ GI (Glycemic Index) thấp/vừa nếu biết
│   ☐ Carb total phù hợp cho người tiểu đường (< 60g/meal)
└── Warning banner: "⚠️ Đây là món cho người tiểu đường.
    Cần kiểm tra đặc biệt kỹ về hàm lượng đường và GI."
```

---

## ADM-006: BÁO CÁO & THỐNG KÊ

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | ADM-006 |
| **Tên màn hình** | Báo Cáo & Thống Kê |
| **Quyền truy cập** | Super Admin |

### Các Báo Cáo Có Sẵn

```
1. Báo cáo Món ăn phổ biến (7/30/90 ngày)
   ├── Top 20 món được chọn nhiều nhất
   ├── Tỉ lệ đổi món theo bữa (sáng/trưa/tối)
   └── Heatmap ngày × bữa → tỉ lệ đổi món

2. Báo cáo Chế độ ăn
   ├── Phân bổ user theo chế độ ăn
   ├── Chế độ nào có tỉ lệ đổi món cao nhất
   └── Coverage gap (chế độ nào thiếu món)

3. Báo cáo Nội dung
   ├── Món mới được thêm theo tuần
   ├── Thời gian trung bình từ draft → publish
   └── Tỉ lệ reject của Nutritionist

4. Báo cáo Người dùng (high-level, no PII)
   ├── MAU (Monthly Active Users)
   ├── DAU
   ├── Retention rate 7/30 ngày
   └── Free vs Premium conversion

Export: CSV cho tất cả reports
Date range picker: last 7/30/90 days, custom range
```

---

## PHÂN QUYỀN ADMIN

### Role-Based Access Control (RBAC)

```
Super Admin:
└── Tất cả quyền + quản lý roles

Content Editor:
├── ADM-001: Read only (giới hạn — không xem user metrics)
├── ADM-002: Create, Read, Update own dishes; Delete = Archive
├── ADM-003: Create, Edit (draft + pending_review)
├── ADM-004: Import (creates drafts)
├── ADM-005: No access
└── ADM-006: No access

Nutritionist:
├── ADM-001: Read only (chỉ thấy pending review count)
├── ADM-002: Read only
├── ADM-003: Read only (không edit)
├── ADM-004: No access
├── ADM-005: Full access (review + approve/reject)
└── ADM-006: No access
```

### Session & Security

```
Admin auth:
├── Separate login from user app (admin.nutriday.vn)
├── Same Firebase Auth backend
├── Role check on every API request (server-side)
└── Session timeout: 8 hours inactivity

Audit log:
├── Every create/update/delete logged:
│   { userId, action, entityType, entityId, timestamp, ipAddress }
└── Logs accessible to Super Admin only
```

---

*SDD-ND-2025-001_08 | Module 08: Admin CMS | v1.0 | NỘI BỘ*
