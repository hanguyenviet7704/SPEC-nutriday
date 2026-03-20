# SDD-ND-2025-001 | MODULE 01: ONBOARDING & AUTHENTICATION
## NutriDay — Tài Liệu Thiết Kế Màn Hình Chi Tiết

| Mã tài liệu: | SDD-ND-2025-001_01 |
|:---|:---|
| **Module:** | 01 — Onboarding & Authentication |
| **Phiên bản:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | §6.1 UC-U01 → UC-U04 |
| **Màn hình trong module:** | SCR-001 → SCR-009 |

---

## MỤC LỤC MODULE 01

- [SCR-001: Splash Screen](#scr-001-splash-screen)
- [SCR-002: Welcome Screen](#scr-002-welcome-screen)
- [SCR-003: Đăng Ký Tài Khoản](#scr-003-đăng-ký-tài-khoản)
- [SCR-004: Xác Thực OTP](#scr-004-xác-thực-otp)
- [SCR-005: Đăng Nhập](#scr-005-đăng-nhập)
- [SCR-006: Quên Mật Khẩu](#scr-006-quên-mật-khẩu)
- [SCR-007: Onboarding — Chọn Mục Tiêu Sức Khỏe](#scr-007-onboarding--chọn-mục-tiêu-sức-khỏe)
- [SCR-008: Onboarding — Số Người Ăn](#scr-008-onboarding--số-người-ăn)
- [SCR-009: Onboarding — Dị Ứng Thực Phẩm](#scr-009-onboarding--dị-ứng-thực-phẩm)

---

## SCR-001: SPLASH SCREEN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-001 |
| **Tên màn hình** | Splash Screen |
| **Use Case** | — (System) |
| **Nền tảng** | iOS & Android |
| **Thời gian hiển thị** | 2–3 giây (auto-advance) |

### Mục Đích
Màn hình đầu tiên khi khởi động ứng dụng. Hiển thị brand identity, thực hiện kiểm tra authentication token và preload dữ liệu cần thiết.

### Layout & Thiết Kế

```
┌────────────────────────────────────┐
│                                    │
│                                    │
│                                    │
│          [Logo NutriDay]           │  ← Logo 120×120px, center
│                                    │
│           NutriDay                 │  ← text-display-xl, color-white
│     Thực đơn thông minh            │  ← text-body-lg, color-white 80%
│          hằng ngày                 │
│                                    │
│                                    │
│    ════════════════                │  ← Loading bar nhẹ (optional)
│                                    │
└────────────────────────────────────┘
Background: Linear gradient 135° từ #2E7D52 → #1A5C3A
Status bar: Light content (trắng)
```

### Vùng màn hình

| Vùng | Nội dung | Vị trí |
|:---|:---|:---|
| Logo area | App icon + wordmark "NutriDay" | Vertical center, hơi cao hơn center 10% |
| Tagline | "Thực đơn thông minh hằng ngày" | Dưới logo, margin-top 16px |
| Loading indicator | Spinner nhỏ màu trắng (opacity 60%) | Bottom 20% màn hình |
| Copyright | "© 2025 NutriDay Vietnam" | Bottom safe area |

### Logic Xử Lý Background

```
1. App launch
2. Kiểm tra JWT token trong AsyncStorage/Keychain
   ├── Token hợp lệ + chưa hết hạn
   │   ├── User đã hoàn tất onboarding → Navigate: HomeScreen (SCR-010)
   │   └── User chưa hoàn tất onboarding → Navigate: Onboarding Stack (SCR-007)
   ├── Token hết hạn → Thử refresh token
   │   ├── Refresh thành công → Navigate: HomeScreen
   │   └── Refresh thất bại → Navigate: WelcomeScreen (SCR-002)
   └── Không có token (lần đầu / đăng xuất)
       → Navigate: WelcomeScreen (SCR-002)
3. Minimum display time: 1.5 giây (brand impression)
```

### Trạng Thái

| State | Mô tả |
|:---|:---|
| Loading | Animation spinner dưới logo |
| Network Error | Toast ngắn "Không có kết nối", vẫn navigate như offline |

### Animation

```
Entrance:
- Logo: fade in + scale 0.8 → 1.0 (500ms ease-out)
- Tagline: fade in (300ms, delay 300ms)

Exit:
- Fade out toàn bộ màn hình (300ms)
- Cross-fade sang màn hình tiếp theo
```

### Accessibility
- Status bar: light content (trắng trên nền xanh)
- VoiceOver: announce "NutriDay đang tải"
- Không có interactive elements

---

## SCR-002: WELCOME SCREEN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-002 |
| **Tên màn hình** | Màn hình Chào / Welcome |
| **Use Case** | UC-U01 (Guest Mode), UC-U02 (Register), UC-U03 (Login) |
| **Navigation From** | SCR-001 (Splash) |
| **Navigation To** | SCR-003 (Register), SCR-005 (Login), SCR-007 (Guest/Onboarding) |

### Mục Đích
Giới thiệu ứng dụng, đề xuất người dùng đăng ký/đăng nhập hoặc tiếp tục với chế độ khách (Guest).

### Layout & Thiết Kế

```
┌────────────────────────────────────┐
│  [Skip / Bỏ qua]          →       │  ← Top right, chỉ hiện ở slide ≠ cuối
│                                    │
│  ┌────────────────────────────┐    │
│  │                            │    │
│  │   [Illustration/Hero]      │    │  ← 280×280px SVG illustration
│  │   Food photography style   │    │     Slide 1: Thực đơn đa dạng
│  │                            │    │     Slide 2: Cá nhân hóa mục tiêu
│  └────────────────────────────┘    │     Slide 3: Mua sắm thông minh
│                                    │
│  ● ● ●  (page indicator)           │  ← 3 dots, active = filled green
│                                    │
│  Tiêu đề slide                     │  ← text-heading-1, center
│  (text-display-lg)                 │
│                                    │
│  Mô tả ngắn về tính năng          │  ← text-body-md, color-gray-600, center
│  của NutriDay...                   │
│                                    │
│  ┌──────────────────────────────┐  │
│  │   Bắt đầu ngay (miễn phí)   │  │  ← Primary button
│  └──────────────────────────────┘  │
│  ┌──────────────────────────────┐  │
│  │   Đăng nhập                 │  │  ← Secondary button (outline)
│  └──────────────────────────────┘  │
│                                    │
│  Dùng thử không cần đăng ký →     │  ← Ghost text link, bottom
└────────────────────────────────────┘
```

### Carousel Slides

| Slide | Illustration | Tiêu đề | Mô tả |
|:---|:---|:---|:---|
| 1 | Bàn ăn với 3 món đa dạng | "Thực đơn hằng ngày, không cần nghĩ" | "Gợi ý 3 bữa mỗi ngày, phù hợp mục tiêu sức khỏe của bạn" |
| 2 | Nhân vật chọn biểu tượng mục tiêu | "Ăn đúng cách, đạt mục tiêu" | "8 chế độ ăn từ giảm cân đến ăn chay. Thực đơn được chuyên gia dinh dưỡng kiểm duyệt" |
| 3 | Danh sách mua sắm + túi chợ | "Đi chợ thông minh, không lãng phí" | "Tự động tổng hợp nguyên liệu, chia sẻ danh sách mua sắm trong 1 nút" |

### Interaction

| Hành động | Kết quả |
|:---|:---|
| Swipe ngang trên illustration area | Chuyển slide |
| Tap vùng ngoài illustration | Chuyển slide tiếp theo |
| Tap "Bắt đầu ngay" | Navigate → SCR-003 (Register) |
| Tap "Đăng nhập" | Navigate → SCR-005 (Login) |
| Tap "Dùng thử không cần đăng ký" | Navigate → SCR-007 (Onboarding, Guest mode) |
| Tap "Bỏ qua" (slides 1,2) | Jump to slide 3 |
| Auto-advance | Không có (manual swipe only) |

### Edge Cases

| Tình huống | Xử lý |
|:---|:---|
| Người dùng đã đăng nhập nhưng logout → vào Welcome | Hiển thị bình thường |
| Sau 3 ngày guest mode | Banner nhắc đăng ký ở top HomeScreen |

---

## SCR-003: ĐĂNG KÝ TÀI KHOẢN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-003 |
| **Tên màn hình** | Đăng Ký Tài Khoản |
| **Use Case** | UC-U02 |
| **Navigation From** | SCR-002 (Welcome) |
| **Navigation To** | SCR-004 (OTP) hoặc SCR-007 (Onboarding — sau verify thành công) |

### Mục Đích
Cho phép người dùng tạo tài khoản mới bằng email/mật khẩu hoặc thông qua Google/Apple OAuth.

### Layout & Thiết Kế

```
┌────────────────────────────────────┐
│  ←  Đăng ký tài khoản              │  ← Header với back button
│                                    │
│  [Logo nhỏ 48px] NutriDay          │
│                                    │
│  Tạo tài khoản miễn phí            │  ← text-heading-1
│  và bắt đầu ăn uống lành mạnh 🌿  │  ← text-body-md, color-gray-600
│                                    │
│  ┌──────────────────────────────┐  │
│  │ 👤  Họ và tên               │  │  ← Input: displayName (optional but recommended)
│  └──────────────────────────────┘  │
│                                    │
│  ┌──────────────────────────────┐  │
│  │ ✉️  Email                   │  │  ← Input: email (required)
│  └──────────────────────────────┘  │
│                                    │
│  ┌──────────────────────────────┐  │
│  │ 🔒  Mật khẩu           [👁] │  │  ← Input: password, toggle visibility
│  └──────────────────────────────┘  │
│  Ít nhất 8 ký tự, 1 chữ hoa, 1 số │  ← Helper text
│  [Strength indicator: ●●●●○]       │  ← Password strength bar
│                                    │
│  ☐  Tôi đồng ý với Điều khoản     │  ← Checkbox required
│     Dịch vụ và Chính sách BM      │
│                                    │
│  ┌──────────────────────────────┐  │
│  │      Đăng ký ngay            │  │  ← Primary button (disabled until valid)
│  └──────────────────────────────┘  │
│                                    │
│  ─────────── hoặc ───────────      │
│                                    │
│  ┌──────────────────────────────┐  │
│  │ [G]  Đăng ký với Google     │  │  ← Google OAuth button
│  └──────────────────────────────┘  │
│  ┌──────────────────────────────┐  │
│  │ [🍎]  Đăng ký với Apple     │  │  ← Apple Sign-In (iOS only)
│  └──────────────────────────────┘  │
│                                    │
│  Đã có tài khoản? **Đăng nhập**   │  ← Link to SCR-005
└────────────────────────────────────┘
```

### Form Validation Rules

| Field | Validation | Error Message |
|:---|:---|:---|
| Họ và tên | Optional, max 100 chars | — |
| Email | Required, valid email format, unique | "Email không hợp lệ" / "Email đã được sử dụng" |
| Mật khẩu | Min 8 chars, 1 uppercase, 1 number | "Mật khẩu phải có ít nhất 8 ký tự, 1 chữ hoa và 1 số" |
| Terms checkbox | Required = true | Nút "Đăng ký" disabled |

### Password Strength Indicator

| Mức | Điều kiện | Màu | Label |
|:---|:---|:---|:---|
| Rất yếu | < 6 chars | color-error-500 | "Rất yếu" |
| Yếu | 6-7 chars hoặc thiếu điều kiện | color-warning-500 | "Yếu" |
| Trung bình | 8+ chars, đủ điều kiện cơ bản | color-warning-400 | "Trung bình" |
| Mạnh | 10+ chars + ký tự đặc biệt | color-success-500 | "Mạnh" |

### Interaction Flow

```
User nhập email + password + tick terms
→ Tap "Đăng ký ngay"
→ Validate client-side
  ├── Lỗi → Hiện error messages inline, focus vào field lỗi đầu tiên
  └── Hợp lệ → Loading spinner trên button
    → API POST /auth/register
      ├── 201 Created → Navigate SCR-004 (OTP), pass email
      ├── 409 Conflict (email tồn tại) → Error: "Email này đã có tài khoản"
      │   + Link "Đăng nhập thay"
      └── 500/Network → Toast error, allow retry

Google OAuth:
→ Tap "Đăng ký với Google"
→ Google sign-in flow (native sheet)
  ├── Success → API POST /auth/google (exchange token)
  │   ├── User mới → Navigate SCR-007 (Onboarding)
  │   └── User cũ → Navigate SCR-010 (Home)
  └── Cancelled/Error → Back to SCR-003, show error toast
```

### States

| State | Button | Form |
|:---|:---|:---|
| Initial | Disabled (gray) | Empty |
| Filling | Disabled | Validation as-you-type |
| Valid & Terms accepted | Enabled (green) | No errors |
| Loading | Spinner, disabled | Locked |
| Error | Re-enabled | Error messages shown |

### Accessibility
- `accessibilityLabel` cho mọi input: "Nhập họ và tên", "Nhập địa chỉ email", "Nhập mật khẩu"
- Password toggle: `accessibilityLabel="Hiển thị mật khẩu"` / `"Ẩn mật khẩu"`
- Error messages đọc tự động bởi screen reader khi focus vào field
- Return key behavior: họ tên → email → mật khẩu → submit

---

## SCR-004: XÁC THỰC OTP

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-004 |
| **Tên màn hình** | Xác Thực OTP Email |
| **Use Case** | UC-U02 |
| **Navigation From** | SCR-003 (sau đăng ký thành công) |
| **Navigation To** | SCR-007 (Onboarding) |
| **Timeout OTP** | 10 phút |

### Mục Đích
Xác minh email của người dùng vừa đăng ký bằng mã OTP 6 chữ số gửi qua email.

### Layout & Thiết Kế

```
┌────────────────────────────────────┐
│  ←                                 │
│                                    │
│      [✉️ Illustration — 120px]     │
│                                    │
│      Kiểm tra email của bạn        │  ← text-heading-1, center
│                                    │
│  Mã xác thực đã gửi đến            │  ← text-body-md, center
│  **nguyen@example.com**            │  ← email bold, truncate nếu dài
│  Nhập mã 6 chữ số để xác nhận     │
│                                    │
│  ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐  │
│  │    │ │    │ │    │ │    │ │    │ │    │  │  ← 6 OTP input boxes
│  └────┘ └────┘ └────┘ └────┘ └────┘ └────┘  │
│                                    │
│  Hết hạn sau: 09:45                │  ← Countdown timer, color-error if < 60s
│                                    │
│  ┌──────────────────────────────┐  │
│  │         Xác nhận             │  │  ← Primary button (disabled until 6 digits)
│  └──────────────────────────────┘  │
│                                    │
│  Không nhận được mã?               │
│  Gửi lại (sau 60 giây)            │  ← Countdown trước khi enable resend
│                                    │
│  Đổi email đăng ký?                │  ← Ghost link
└────────────────────────────────────┘
```

### OTP Input Component

```
6 ô input riêng biệt:
- Mỗi ô: 48×56px, border-radius: radius-md
- Border default: color-gray-200
- Border focused: color-primary-500 + shadow nhẹ
- Border error: color-error-500
- Border success: color-success-500
- Font: text-heading-2 (20pt, bold), center-aligned
- Type: number only, max 1 char per box
- Auto-advance: nhập 1 số → tự jump sang ô tiếp theo
- Auto-submit: khi ô cuối được điền → tự gọi verify API
- Paste support: paste 6 số → auto-fill toàn bộ
- Backspace: xóa ô hiện tại → focus ô trước (nếu empty)
```

### Countdown Timer

```
Đếm ngược từ 10:00 (600 giây)
Format: MM:SS
Color: color-gray-600 khi > 60 giây
Color: color-error-500 khi ≤ 60 giây (blinking khi < 30s)
Khi hết: "Mã đã hết hạn" + disable input + enable "Gửi lại" ngay
```

### Logic Xử Lý

```
Auto-submit khi đủ 6 số:
→ API POST /auth/verify-otp { email, otp }
  ├── 200 OK → Animate success (all boxes turn green)
  │   → Navigate SCR-007 (Onboarding) sau 500ms
  ├── 400 Wrong OTP → Shake animation, clear input, focus ô 1
  │   Error: "Mã không đúng. Còn [N] lần thử."
  │   Sau 5 lần sai → Block 15 phút
  └── 410 Expired → "Mã đã hết hạn, vui lòng gửi lại"

Gửi lại OTP:
→ Enabled sau 60 giây từ lần gửi trước
→ API POST /auth/resend-otp { email }
→ Toast: "Mã mới đã gửi đến [email]"
→ Reset countdown 10:00
→ Clear input, focus ô 1
```

### Edge Cases

| Tình huống | Xử lý |
|:---|:---|
| Người dùng quay lại trang này sau khi đã verify | Redirect về SCR-010 nếu token hợp lệ |
| Nhập sai 5 lần | Block 15 phút, hiện countdown unlock |
| Email sai → tap "Đổi email" | Quay về SCR-003, pre-fill form |
| App bị tắt, mở lại với OTP chưa dùng | Tiếp tục từ SCR-004, countdown tiếp |

---

## SCR-005: ĐĂNG NHẬP

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-005 |
| **Tên màn hình** | Đăng Nhập |
| **Use Case** | UC-U03 |
| **Navigation From** | SCR-002 (Welcome), SCR-003 (link) |
| **Navigation To** | SCR-010 (Home) hoặc SCR-007 (Onboarding nếu chưa done) |

### Mục Đích
Cho phép người dùng đã có tài khoản đăng nhập.

### Layout & Thiết Kế

```
┌────────────────────────────────────┐
│  ←  Đăng nhập                      │
│                                    │
│  [Logo 64px] NutriDay              │
│                                    │
│  Chào mừng trở lại! 👋             │  ← text-heading-1
│  Đăng nhập để tiếp tục             │  ← text-body-md, color-gray-600
│                                    │
│  ┌──────────────────────────────┐  │
│  │ ✉️  Email                   │  │
│  └──────────────────────────────┘  │
│                                    │
│  ┌──────────────────────────────┐  │
│  │ 🔒  Mật khẩu           [👁] │  │
│  └──────────────────────────────┘  │
│                                    │
│  ☐  Ghi nhớ đăng nhập      [Quên mật khẩu?] │
│                                    │
│  ┌──────────────────────────────┐  │
│  │          Đăng nhập           │  │  ← Primary button
│  └──────────────────────────────┘  │
│                                    │
│  ─────────── hoặc ───────────      │
│                                    │
│  [G] Đăng nhập với Google          │
│  [🍎] Đăng nhập với Apple         │  ← iOS only
│                                    │
│  Chưa có tài khoản? **Đăng ký**   │
└────────────────────────────────────┘
```

### Logic Đăng Nhập

```
Tap "Đăng nhập":
→ Validate: email format, password not empty
→ API POST /auth/login { email, password }
  ├── 200 OK { accessToken, refreshToken, user }
  │   ├── Lưu token vào Keychain (iOS) / EncryptedSharedPrefs (Android)
  │   ├── "Ghi nhớ" = true → session 30 ngày
  │   ├── "Ghi nhớ" = false → session 24 giờ
  │   ├── user.onboardingDone = true → Navigate SCR-010 (Home)
  │   └── user.onboardingDone = false → Navigate SCR-007 (Onboarding)
  ├── 401 Wrong password → "Mật khẩu không đúng" (inline error)
  │   Counter tăng. Nếu >= 5 → Toast: "Tài khoản tạm khóa 15 phút.
  │   Email cảnh báo đã gửi đến [email]"
  ├── 404 Email not found → "Email này chưa có tài khoản"
  │   + Link "Đăng ký ngay"
  └── Network error → "Không thể kết nối. Thử lại?"
```

### Lockout Behavior

```
Sau 5 lần sai mật khẩu:
- Khóa input form
- Hiện countdown: "Tài khoản tạm khóa. Thử lại sau: 14:59"
- Gửi email cảnh báo bảo mật đến địa chỉ email
- Sau 15 phút: tự mở khóa, reset counter
```

---

## SCR-006: QUÊN MẬT KHẨU

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-006 |
| **Tên màn hình** | Quên Mật Khẩu |
| **Use Case** | UC-U03 |
| **Navigation From** | SCR-005 (link "Quên mật khẩu?") |
| **Navigation To** | SCR-005 (sau khi reset thành công) |

### Layout

```
Step 1 — Nhập email:
┌────────────────────────────────────┐
│  ←  Quên mật khẩu                  │
│  [Lock illustration 100px]         │
│  Nhập email để đặt lại mật khẩu   │
│  [Email input]                     │
│  [Gửi hướng dẫn đặt lại]          │
└────────────────────────────────────┘

Step 2 — Nhập OTP (tương tự SCR-004):
→ OTP 6 số gửi qua email (subject: "Đặt lại mật khẩu NutriDay")

Step 3 — Đặt mật khẩu mới:
┌────────────────────────────────────┐
│  ←  Mật khẩu mới                   │
│  [Password input + strength bar]   │
│  [Xác nhận mật khẩu input]         │
│  [Cập nhật mật khẩu]               │
└────────────────────────────────────┘
```

**Validation Step 3:**
- Mật khẩu mới ≥ 8 chars, 1 uppercase, 1 number
- Confirm password khớp với mật khẩu mới
- Không được trùng với mật khẩu cũ (API validate)

**Success Flow:**
→ Toast "Mật khẩu đã được đặt lại thành công"
→ Navigate SCR-005 (đăng nhập với mật khẩu mới)

---

## SCR-007: ONBOARDING — CHỌN MỤC TIÊU SỨC KHỎE

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-007 |
| **Tên màn hình** | Onboarding: Chọn Mục Tiêu |
| **Use Case** | UC-U04 |
| **Navigation From** | SCR-004 (sau verify OTP), SCR-002 (Guest), SCR-005 (onboarding chưa done) |
| **Navigation To** | SCR-008 (Onboarding: Số người ăn) |
| **Bước** | 1/3 |

### Mục Đích
Cho phép người dùng chọn mục tiêu sức khỏe để hệ thống cá nhân hóa gợi ý thực đơn.

### Layout & Thiết Kế

```
┌────────────────────────────────────┐
│  ●──○──○  (Step indicator)         │  ← 3 dots: 1 active, 2 inactive
│  Bước 1/3                          │
│                                    │
│  Mục tiêu sức khỏe của bạn        │  ← text-heading-1
│  là gì?                            │
│                                    │
│  Chọn một hoặc nhiều mục tiêu     │  ← text-body-md, color-gray-600
│  phù hợp với bạn.                  │
│                                    │
│  ┌──────────────┐  ┌──────────────┐│
│  │ 🏃 Giảm cân │  │ 💪 Tăng cân  ││  ← Diet Goal Chip (2×4 grid)
│  └──────────────┘  └──────────────┘│
│  ┌──────────────┐  ┌──────────────┐│
│  │ 🩺 Tiểu đường│  │ 🥗 Eat Clean ││
│  └──────────────┘  └──────────────┘│
│  ┌──────────────┐  ┌──────────────┐│
│  │ 🌿 Ăn chay  │  │ 💚 Healthy   ││
│  └──────────────┘  └──────────────┘│
│  ┌──────────────┐  ┌──────────────┐│
│  │🍚 Ít tinh bột│  │🥩 Nhiều protein││
│  └──────────────┘  └──────────────┘│
│                                    │
│  💡 Bạn có thể thay đổi bất cứ    │  ← Info note
│     lúc nào trong Cài đặt         │
│                                    │
│  ┌──────────────────────────────┐  │
│  │   Tiếp tục (1 mục tiêu đã chọn) │  ← Dynamic count
│  └──────────────────────────────┘  │
│  Bỏ qua, chọn sau                 │  ← Ghost link (skip option)
└────────────────────────────────────┘
```

### Diet Goal Chip Design

```
Unselected:
- Background: color-white
- Border: 1.5px solid color-gray-200
- Icon: 24px, grayscale
- Text: text-label-lg, color-gray-700

Selected:
- Background: color-primary-100
- Border: 1.5px solid color-primary-500
- Icon: 24px, color tự nhiên
- Text: text-label-lg, color-primary-700
- Checkmark: icon-sm (✓) xuất hiện ở top-right

Animation khi tap:
- Scale: 1.0 → 0.95 → 1.02 → 1.0 (200ms spring)
- Haptic: Light
```

### Logic

```
Multi-select:
- Tối thiểu 1 chip phải được chọn → button "Tiếp tục" mới enabled
- Không giới hạn số lượng chọn
- Button text: "Tiếp tục" khi 0 chọn; "Tiếp tục (N mục tiêu đã chọn)" khi N ≥ 1

Lưu:
- Guest: lưu vào AsyncStorage { dietGoals: [...] }
- Authenticated: lưu local, sync lên server ở bước cuối hoặc background

Skip:
- Lưu dietGoals = ["healthy"] (default)
- Navigate thẳng SCR-008
```

### Validation & Edge Cases

| Tình huống | Xử lý |
|:---|:---|
| Người dùng không chọn gì, tap "Tiếp tục" | Button disabled, không thể tap |
| Người dùng tap "Bỏ qua" | dietGoals = ["healthy"], next screen |
| Người dùng đã từng onboard, vào lại từ Settings | Pre-select những goals đã chọn |
| Chọn "Tiểu đường" | Hiện note nhỏ: "Thực đơn dành cho người tiểu đường được chuyên gia dinh dưỡng kiểm duyệt đặc biệt" |

---

## SCR-008: ONBOARDING — SỐ NGƯỜI ĂN

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-008 |
| **Tên màn hình** | Onboarding: Số Người Ăn |
| **Use Case** | UC-U04 |
| **Navigation From** | SCR-007 |
| **Navigation To** | SCR-009 |
| **Bước** | 2/3 |

### Layout & Thiết Kế

```
┌────────────────────────────────────┐
│  ○──●──○  (Step 2/3)               │
│                                    │
│  Bạn nấu ăn cho                    │  ← text-heading-1
│  mấy người?                        │
│                                    │
│  Chúng tôi sẽ điều chỉnh khẩu     │  ← text-body-md, color-gray-600
│  phần phù hợp với gia đình bạn    │
│                                    │
│  ┌──────────────────────────────┐  │
│  │                              │  │
│  │   [   -   ]  [  2  ]  [  + ]│  │  ← Number stepper: 1–10+
│  │          người ăn            │  │
│  │                              │  │
│  └──────────────────────────────┘  │
│                                    │
│  [1 người]  [2 người]  [3 người]   │
│  [4 người]  [Hơn 4]               │  ← Quick select chips
│                                    │
│  [Illustration: bàn ăn với N ghế  │  ← Dynamic illustration thay đổi
│   thay đổi theo số người]          │    khi N thay đổi
│                                    │
│  💡 Bạn có thể thay đổi trước      │
│     từng bữa ăn                   │
│                                    │
│  [Tiếp tục]                        │
│  Bỏ qua, chọn sau                 │
└────────────────────────────────────┘
```

### Number Stepper

```
[-] button: giảm 1 (min = 1, disabled khi = 1)
[+] button: tăng 1 (max = 10, nếu > 10 hiện "10+" và disable +)
Value display: text-display-xl, color-primary-500
Quick chips: 1, 2, 3, 4, "Hơn 4" (= 5 default)
Tap chip → stepper jumps to value + haptic
```

### Default Value
- Mặc định: 2 người (giá trị phổ biến nhất từ user survey)

---

## SCR-009: ONBOARDING — DỊ ỨNG THỰC PHẨM

| Thuộc tính | Giá trị |
|:---|:---|
| **Screen ID** | SCR-009 |
| **Tên màn hình** | Onboarding: Dị Ứng & Thực Phẩm Không Ăn |
| **Use Case** | UC-U04 |
| **Navigation From** | SCR-008 |
| **Navigation To** | SCR-010 (Home) — hoàn tất onboarding |
| **Bước** | 3/3 |

### Mục Đích
Thu thập danh sách thực phẩm người dùng dị ứng hoặc không muốn ăn để loại trừ khỏi gợi ý.

### Layout & Thiết Kế

```
┌────────────────────────────────────┐
│  ○──○──●  (Step 3/3)               │
│                                    │
│  Có thực phẩm nào bạn              │  ← text-heading-1
│  không ăn được không?              │
│                                    │
│  Chúng tôi sẽ tránh các           │  ← text-body-md, color-gray-600
│  món có thành phần này            │
│                                    │
│  Phổ biến:                         │  ← text-label-md, color-gray-500
│  [Hải sản] [Sữa] [Gluten]         │
│  [Đậu phộng] [Trứng] [Đậu nành]  │  ← Pre-defined chips
│                                    │
│  ┌──────────────────────────────┐  │
│  │ 🔍 Thêm nguyên liệu khác... │  │  ← Search + add custom input
│  └──────────────────────────────┘  │
│                                    │
│  Đã thêm:                          │  ← Section này chỉ hiện khi có item
│  [Hải sản ×] [Sữa ×]             │  ← Chips with delete (×)
│                                    │
│  💡 Bạn có thể chỉnh lại          │
│     bất cứ lúc nào                │
│                                    │
│  ┌──────────────────────────────┐  │
│  │   Hoàn tất — Xem thực đơn!  │  │  ← Primary button
│  └──────────────────────────────┘  │
│  Bỏ qua, tôi không có dị ứng      │  ← Ghost link (bỏ qua step này)
└────────────────────────────────────┘
```

### Pre-defined Allergens

| Chip | Icon | Tên hiển thị |
|:---|:---|:---|
| `seafood` | 🦐 | Hải sản |
| `dairy` | 🥛 | Sữa & sản phẩm sữa |
| `gluten` | 🌾 | Gluten (lúa mì) |
| `peanut` | 🥜 | Đậu phộng |
| `egg` | 🥚 | Trứng |
| `soy` | 🫘 | Đậu nành |
| `pork` | 🐷 | Thịt heo |
| `beef` | 🐄 | Thịt bò |

### Custom Input

```
Khi user gõ từ 2 ký tự:
- Auto-suggest từ danh sách nguyên liệu trong DB
- Hiển thị dropdown (max 5 gợi ý)
- Chọn gợi ý hoặc nhập tự do → thêm vào chips
- Max 20 items trong danh sách allergens
```

### Hoàn Tất Onboarding

```
Tap "Hoàn tất — Xem thực đơn!":
→ Lưu toàn bộ preferences:
  { dietGoals, servings, allergies }
→ Authenticated: API PATCH /user/profile
→ Guest: AsyncStorage
→ Run suggestion engine lần đầu (trigger background)
→ Navigate SCR-010 (Home) với animation đặc biệt
→ Show confetti nhỏ + Toast: "Chào mừng bạn đến NutriDay! 🎉"
```

### Luồng Guest Mode Onboarding

```
Nếu là Guest:
- Tất cả 3 bước vẫn chạy bình thường
- Dữ liệu lưu AsyncStorage với key 'guest_preferences'
- Sau 3 ngày: Banner ở HomeScreen nhắc "Đăng ký để lưu thực đơn của bạn"
  Nội dung: "Bạn đang dùng chế độ khách. Tạo tài khoản để lưu lịch sử và
  thực đơn yêu thích không bị mất!"
  Button: [Đăng ký ngay] [Để sau]
```

---

## LUỒNG ĐIỀU HƯỚNG TỔNG THỂ MODULE 01

```
App Launch
    │
    ▼
SCR-001: Splash (1.5-3s)
    │
    ├── Token hợp lệ + onboarding done ──────────────────────► SCR-010 (Home)
    ├── Token hợp lệ + onboarding incomplete ────────────────► SCR-007 (Onboarding)
    │
    └── Không có token / expired
            │
            ▼
        SCR-002: Welcome
            │
            ├── [Bắt đầu ngay] ──► SCR-003: Register
            │                           │
            │                           ▼ (success)
            │                       SCR-004: OTP Verify
            │                           │
            │                           ▼ (verified)
            │                       SCR-007 ──► SCR-008 ──► SCR-009 ──► SCR-010
            │
            ├── [Đăng nhập] ──────► SCR-005: Login
            │                           │
            │                           ├── (onboarding done) ──► SCR-010 (Home)
            │                           ├── (onboarding not done) ──► SCR-007
            │                           └── [Quên mật khẩu] ──► SCR-006
            │
            └── [Dùng thử] ────────► SCR-007 (Guest) ──► SCR-008 ──► SCR-009 ──► SCR-010
```

---

*SDD-ND-2025-001_01 | Module 01: Onboarding & Authentication | v1.0 | NỘI BỘ*
