# SDD-ND-2025-001 | MODULE 09: CÀI ĐẶT & THÔNG BÁO
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_09 |
|:---|:---|
| **Module:** | 09 — Cài Đặt & Thông Báo |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | BR-012; NFR-06; §6.1 UC-U03; §10.1 FCM |
| **Màn hình trong module:** | SCR-025, SCR-026 |

---

## MỤC LỤC MODULE 09

- [SCR-025: Màn Hình Cài Đặt](#scr-025-màn-hình-cài-đặt)
- [SCR-026: Cài Đặt Thông Báo](#scr-026-cài-đặt-thông-báo)

---

## SCR-025: MÀN HÌNH CÀI ĐẶT

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-025 |
| **Tên màn hình** | Cài Đặt |
| **Navigation From** | SCR-022 (Profile → Cài đặt), Header [⚙️] button |
| **Navigation To** | SCR-023 (Edit Profile), SCR-026 (Notifications), SCR-005 (Logout) |

### Layout

```
┌────────────────────────────────────────────┐
│  ← Cài đặt                                 │
├────────────────────────────────────────────┤
│                                            │
│  TÀI KHOẢN                                │  ← Section header
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │ 👤  Chỉnh sửa hồ sơ           →  │   │  ← → SCR-023
│  │ 🎯  Mục tiêu & chế độ ăn      →  │   │  ← → SCR-023 (anchor: goals)
│  │ 🔐  Đổi mật khẩu              →  │   │  ← Modal/screen
│  └────────────────────────────────────┘   │
│                                            │
│  THÔNG BÁO                                │
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │ 🔔  Cài đặt thông báo          →  │   │  ← → SCR-026
│  └────────────────────────────────────┘   │
│                                            │
│  ỨNG DỤNG                                 │
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │ 🌐  Ngôn ngữ                      │   │
│  │     Tiếng Việt              [›]   │   │  ← Hiện chỉ có TV (Phase 2: EN)
│  │ ─────────────────────────────────   │   │
│  │ 📱  Phiên bản                     │   │
│  │     NutriDay v1.0.0               │   │  ← Read-only
│  │ ─────────────────────────────────   │   │
│  │ 💾  Bộ nhớ cache                  │   │
│  │     Đang dùng: 45 MB   [Xóa cache]│   │
│  └────────────────────────────────────┘   │
│                                            │
│  HỖ TRỢ                                   │
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │ ❓  Trợ giúp & FAQ            →  │   │  ← Webview FAQ page
│  │ 💬  Gửi phản hồi              →  │   │  ← Feedback form
│  │ ⭐  Đánh giá ứng dụng         →  │   │  ← App Store / Play Store deeplink
│  │ 📋  Điều khoản dịch vụ        →  │   │  ← Webview
│  │ 🔒  Chính sách bảo mật        →  │   │  ← Webview
│  └────────────────────────────────────┘   │
│                                            │
│  GÓI DỊCH VỤ                             │
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │ 🆓  Đang dùng gói Free            │   │  ← Free tier
│  │     [Nâng cấp Premium →]          │   │  ← CTA to upgrade
│  └────────────────────────────────────┘   │
│  (Ẩn section này nếu đã là Premium)       │
│                                            │
│  ┌────────────────────────────────────┐   │
│  │ 🚪  Đăng xuất                     │   │  ← Destructive action
│  └────────────────────────────────────┘   │
│                                            │
│  ┌────────────────────────────────────┐   │
│  │ 🗑️  Xóa tài khoản               →│   │  ← Danger zone, color-error-500
│  └────────────────────────────────────┘   │
│                                            │
└────────────────────────────────────────────┘
```

### Đổi Mật Khẩu

```
Modal (không navigate ra screen):
┌────────────────────────────────────────────┐
│  Đổi mật khẩu                     [×]     │
│  ─────────────────────────────────────    │
│  Mật khẩu hiện tại:                       │
│  [●●●●●●●●                          ]     │
│                                            │
│  Mật khẩu mới:                            │
│  [●●●●●●●●●●●●                      ]     │
│  [Strength indicator]                     │
│                                            │
│  Xác nhận mật khẩu mới:                  │
│  [●●●●●●●●●●●●                      ]     │
│                                            │
│  [Hủy]          [Cập nhật mật khẩu]      │
└────────────────────────────────────────────┘

Validation:
├── Mật khẩu hiện tại phải đúng
├── Mật khẩu mới ≥ 8 chars, 1 uppercase, 1 number
└── Mật khẩu mới ≠ mật khẩu cũ
```

### Xóa Cache

```
Tap [Xóa cache]:
→ Alert: "Xóa bộ nhớ cache?
   Ảnh và dữ liệu đã tải sẽ bị xóa.
   Ứng dụng sẽ tải lại dữ liệu khi cần."
  [Hủy] [Xóa cache]
→ Clear AsyncStorage image cache + MMKV cache
→ Recalculate storage: "Đã giải phóng 45 MB"
→ Toast: "Cache đã được xóa thành công"
```

### Đăng Xuất

```
Tap [Đăng xuất]:
→ Alert: "Đăng xuất khỏi NutriDay?"
  [Hủy] [Đăng xuất]
→ Delete JWT tokens from Keychain/SecureStorage
→ Clear user session state
→ Navigate → SCR-002 (Welcome Screen)
→ (Local DayPlan cache bị xóa — cần login lại để sync)
```

---

## SCR-026: CÀI ĐẶT THÔNG BÁO

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-026 |
| **Tên màn hình** | Cài Đặt Thông Báo |
| **Use Case** | BR-012 (Nhắc nấu ăn) |
| **Navigation From** | SCR-025 (Cài đặt) |

### Mục Đích
Cho phép người dùng tùy chỉnh các loại thông báo push notification và lịch nhắc nhở nấu ăn.

### Layout

```
┌────────────────────────────────────────────┐
│  ← Cài đặt thông báo                       │
├────────────────────────────────────────────┤
│                                            │
│  QUYỀN THÔNG BÁO HỆ THỐNG                 │
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │  ✅ Thông báo đã được bật          │   │  ← Status display
│  │  Thay đổi trong Cài đặt hệ thống  │   │  ← iOS/Android settings deeplink
│  └────────────────────────────────────┘   │
│  (Hiện "Bật thông báo →" nếu chưa cho phép)│
│                                            │
│  NHẮC NHỞ NẤU ĂN                         │
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │  🌅 Nhắc bữa sáng   [Toggle ON ]  │   │
│  │     Giờ nhắc: 07:00         [›]   │   │  ← Time picker
│  └────────────────────────────────────┘   │
│  ┌────────────────────────────────────┐   │
│  │  ☀️ Nhắc bữa trưa   [Toggle ON ]  │   │
│  │     Giờ nhắc: 11:30         [›]   │   │
│  └────────────────────────────────────┘   │
│  ┌────────────────────────────────────┐   │
│  │  🌙 Nhắc bữa tối   [Toggle OFF]   │   │
│  │     (đang tắt)                     │   │
│  └────────────────────────────────────┘   │
│                                            │
│  NGÀY NHẮC NHỞ                           │
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │ [T2 ✓] [T3 ✓] [T4 ✓] [T5 ✓]    │   │
│  │ [T6 ✓] [T7 ✓] [CN  ]             │   │  ← Day selector chips
│  └────────────────────────────────────┘   │
│                                            │
│  THÔNG BÁO HỆ THỐNG                      │
│  ─────────────────────────────────────    │
│  ┌────────────────────────────────────┐   │
│  │  📢 Thực đơn mới sẵn sàng [Toggle]│   │
│  │  🔔 Cập nhật từ NutriDay  [Toggle]│   │
│  └────────────────────────────────────┘   │
│                                            │
│  Nhắc nhở: Tối đa 3 thông báo/ngày        │  ← Rate limit info
│  (theo cài đặt của ứng dụng)              │
│                                            │
└────────────────────────────────────────────┘
```

### Toggle Behavior

```
Toggle ON → OFF:
→ Tắt notification type đó
→ FCM unsubscribe từ topic/schedule

Toggle OFF → ON:
→ Check OS permission
├── Granted → Enable, schedule notification
└── Not granted → Deep-link to OS notification settings
    Toast: "Vui lòng bật thông báo trong Cài đặt hệ thống
    để sử dụng tính năng này"
```

### Time Picker

```
Tap giờ nhắc (VD: "07:00 [›]"):
→ Native time picker (iOS: wheel picker, Android: clock dialog)
→ User chọn giờ → Save
→ Schedule FCM local notification cho giờ đó mỗi ngày active

Notification content (nhắc bữa sáng lúc 7:00):
Title: "🌅 Bữa sáng hôm nay: Cháo yến mạch chuối mật ong"
Body: "⏱ 15 phút | 🔥 320 kcal — Bấm để xem công thức"
Tap notification → deep-link mở SCR-012 với dishId của bữa sáng hôm nay
```

### iOS Permission Request Flow

```
First time toggle ON:
1. Check current permission status (iOS)
2. If 'not determined': Show pre-permission modal:
   ┌──────────────────────────────────────────┐
   │ [Bell illustration]                      │
   │ Nhận nhắc nhở nấu ăn                    │
   │ mỗi ngày!                               │
   │                                          │
   │ NutriDay sẽ nhắc bạn chuẩn bị           │
   │ các bữa ăn theo lịch của bạn.           │
   │                                          │
   │ [Không, cảm ơn]    [Bật thông báo]      │
   └──────────────────────────────────────────┘
3. User taps "Bật thông báo" → request iOS permission
4. If granted: Enable toggles
5. If denied: Show instructions to enable manually
```

### FCM Integration

```
Backend (FCM Admin SDK):
├── Rate limit: max 3 push/day/user (NFR từ §10.1)
├── Types:
│   • meal_reminder: meal-time reminders (scheduled)
│   • system: admin announcements (low frequency)
│   └── new_menu: "your daily menu is ready" (once/day)

User can disable each type independently
FCM token refresh: handled automatically by SDK

Notification tracking:
└── Analytics: track open rate, dismiss rate per notification type
```

---

## OFFLINE BEHAVIOR SUMMARY (NFR-07)

Tổng hợp hành vi offline toàn ứng dụng:

| Màn hình | Offline behavior |
|:---|:---|
| SCR-010 (Home) | Hiển thị DayPlan đã cache. Banner "Ngoại tuyến". Disable "Đổi món" |
| SCR-012 (Dish Detail) | Hiển thị từ cache nếu đã xem. "Chưa tải" nếu chưa cache |
| SCR-015 (Week Plan) | Hiển thị WeekPlan đã cache. Disable "Tự động điền" |
| SCR-016/017 (Shopping) | Hiển thị từ cache DayPlan/WeekPlan. Checkbox state local only |
| SCR-018/019/020 (Search) | Disable search. "Tìm kiếm cần kết nối" |
| SCR-021 (Favorites) | Hiển thị từ cache. Disable remove (sync khi có mạng) |
| SCR-022 (Profile) | Hiển thị thông tin đã cache. Disable edit |
| Khi có mạng lại | Sync outstanding operations (save favorite, confirm eaten) |

---

*SDD-ND-2025-001_09 | Module 09: Cài Đặt & Thông Báo | v1.0 | NỘI BỘ*
