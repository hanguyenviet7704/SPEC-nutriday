# FED-ND-2025-001_07 — Screens — Auth & Onboarding
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 07 — Screens Auth & Onboarding |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## SCR-001 — Splash Screen

```
┌──────────────────────┐
│                      │
│                      │
│     🍽️ NutriDay     │
│    "Ăn ngon mỗi ngày"│
│                      │
│     ◌ Loading...     │
│                      │
└──────────────────────┘
```

**Logic:**
- Check AsyncStorage cho auth token
- Nếu có token → verify → navigate MainTab hoặc Onboarding
- Nếu không → navigate Welcome
- Timeout 3 giây max

---

## SCR-002 — Welcome Screen

```
┌──────────────────────┐
│                      │
│   [Illustration]     │
│   3 slides carousel  │
│                      │
│   ● ○ ○              │
│                      │
│  [  Đăng ký  ]       │  ← primary button
│  [  Đăng nhập  ]     │  ← outline button
│                      │
└──────────────────────┘
```

**Slides:**
1. "Thực đơn theo mục tiêu" — Diet goal illustration
2. "Đa dạng mỗi ngày" — No repeat 7 days
3. "Danh sách mua sắm thông minh" — Shopping list

---

## SCR-003 — Register Screen

```
┌──────────────────────┐
│ ← Đăng ký            │
│                      │
│ Họ tên    [________] │
│ Email     [________] │
│ SĐT       [________] │
│ Mật khẩu  [________] │
│                      │
│ ☐ Đồng ý Điều khoản │
│                      │
│ [    Đăng ký    ]    │
│                      │
│ Đã có tài khoản?     │
│ Đăng nhập            │
└──────────────────────┘
```

**Validation (Zod):**
- fullName: 2-100 chars
- email: valid email format
- phone: 10 digits, starts with 0
- password: min 8, 1 uppercase, 1 digit, 1 special

**API:** `POST /v1/auth/register`
**Success:** Navigate → OtpVerify (userId, phone)
**Errors:** 409 email trùng, 400 validation

---

## SCR-004 — OTP Verify Screen

```
┌──────────────────────┐
│ ← Xác minh OTP       │
│                      │
│ Nhập mã gửi đến     │
│ 0901***567           │
│                      │
│ [ _ ][ _ ][ _ ][ _ ][ _ ][ _ ] │
│                      │
│ Gửi lại sau 59s     │
│                      │
│ [   Xác minh   ]    │
└──────────────────────┘
```

**Logic:**
- 6-digit OTP input (auto-focus next)
- Auto-submit khi nhập đủ 6 số
- Countdown 60s cho resend
- Max 5 attempts → lock 30 phút
- **API:** `POST /v1/auth/verify-otp`

---

## SCR-005 — Login Screen

```
┌──────────────────────┐
│ ← Đăng nhập          │
│                      │
│ Email/SĐT [________] │
│ Mật khẩu  [________] │
│                      │
│        Quên mật khẩu?│
│                      │
│ [   Đăng nhập   ]   │
│                      │
│ Chưa có tài khoản?  │
│ Đăng ký              │
└──────────────────────┘
```

**API:** `POST /v1/auth/login`
**Success:**
- onboardingCompleted=false → OnboardingNavigator
- onboardingCompleted=true → MainTabNavigator

---

## SCR-006 — Forgot Password Screen

```
┌──────────────────────┐
│ ← Quên mật khẩu      │
│                      │
│ Nhập email để nhận   │
│ link đặt lại mật khẩu│
│                      │
│ Email    [________]  │
│                      │
│ [    Gửi link    ]   │
│                      │
│ ✓ Đã gửi email!     │  ← success state
│ Kiểm tra hộp thư    │
└──────────────────────┘
```

**API:** `POST /v1/auth/forgot-password`

---

## SCR-007 — Goal Selection Screen (Onboarding 1/3)

```
┌──────────────────────┐
│ Bước 1/3             │
│ ████████░░░░░░░░░░░░ │
│                      │
│ Mục tiêu ăn uống    │
│ của bạn là gì?       │
│                      │
│ ┌──────┐ ┌──────┐   │
│ │Giảm  │ │Tăng  │   │
│ │ cân  │ │ cân  │   │
│ └──────┘ └──────┘   │
│ ┌──────┐ ┌──────┐   │
│ │Tiểu  │ │Eat   │   │
│ │đường │ │Clean │   │
│ └──────┘ └──────┘   │
│ ┌──────┐ ┌──────┐   │
│ │Chay  │ │Healthy│  │
│ └──────┘ └──────┘   │
│ ┌──────┐ ┌──────┐   │
│ │Low   │ │High  │   │
│ │Carb  │ │Protein│  │
│ └──────┘ └──────┘   │
│                      │
│ [    Tiếp tục    ]   │
└──────────────────────┘
```

**8 diet goals:** weight_loss, weight_gain, diabetes, eat_clean, vegetarian, healthy, low_carb, high_protein
**Store:** `onboardingStore.setDietGoal(goal)`

---

## SCR-008 — Servings Screen (Onboarding 2/3)

```
┌──────────────────────┐
│ Bước 2/3             │
│ ████████████░░░░░░░░ │
│                      │
│ Bạn nấu cho         │
│ bao nhiêu người?     │
│                      │
│      [ - ]  2  [ + ] │
│                      │
│ Số lượng ảnh hưởng   │
│ đến danh sách mua sắm│
│                      │
│ [    Tiếp tục    ]   │
└──────────────────────┘
```

**Range:** 1-10 servings
**Store:** `onboardingStore.setServings(n)`

---

## SCR-009 — Allergies Screen (Onboarding 3/3)

```
┌──────────────────────┐
│ Bước 3/3             │
│ ████████████████████ │
│                      │
│ Bạn dị ứng           │
│ thực phẩm nào?       │
│                      │
│ [Tôm/Cua] [Đậu phộng]│
│ [Trứng]  [Sữa]      │
│ [Đậu nành] [Gluten] │
│ [Cá]    [Hạt cây]   │
│                      │
│ Bạn có thể bỏ qua   │
│ nếu không có dị ứng  │
│                      │
│ [   Hoàn thành   ]  │
└──────────────────────┘
```

**Allergens:** shellfish, peanut, egg, dairy, soy, gluten, fish, tree_nut
**Multi-select** toggle chips
**API:** `POST /v1/users/onboarding` with { dietGoal, servings, allergies }
**Success:** Navigate → HomeScreen, set onboardingCompleted=true

---

**Trang trước:** [06 — Organisms & Admin ←](FED-ND-2025-001_06_Components_Organisms_Admin.md)
**Trang tiếp theo:** [08 — Screens Core →](FED-ND-2025-001_08_Screens_Core.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
