# APID-ND-2025-001_01 — Auth API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 01 — Authentication |
| **Base Path** | `/auth` |
| **Auth Required** | Không (trừ logout) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả | Auth |
|---|---|---|---|
| POST | `/auth/register` | Đăng ký tài khoản | Không |
| POST | `/auth/verify-otp` | Xác thực OTP | Không |
| POST | `/auth/resend-otp` | Gửi lại OTP | Không |
| POST | `/auth/login` | Đăng nhập | Không |
| POST | `/auth/refresh` | Làm mới access token | Không |
| POST | `/auth/logout` | Đăng xuất | Bearer |
| POST | `/auth/forgot-password` | Quên mật khẩu | Không |
| POST | `/auth/reset-password` | Đặt lại mật khẩu | Không |

---

## 1.1 Đăng ký

**POST** `/auth/register`

**Request Body:**
```json
{
  "fullName": "Nguyễn Văn An",
  "email": "an.nguyen@gmail.com",
  "phone": "0901234567",
  "password": "P@ssw0rd123"
}
```

**Validation:**
| Field | Rule |
|---|---|
| `fullName` | 2–100 ký tự, không rỗng |
| `email` | Định dạng email hợp lệ, unique |
| `phone` | 10 chữ số Việt Nam, unique |
| `password` | ≥ 8 ký tự, có chữ hoa, chữ thường, số, ký tự đặc biệt |

**Response 201:**
```json
{
  "success": true,
  "statusCode": 201,
  "message": "Đăng ký thành công. OTP đã được gửi đến số điện thoại của bạn.",
  "data": {
    "userId": "usr_01HX3Z9N8K",
    "phone": "090****567",
    "otpExpiry": "2025-04-01T10:05:00+07:00"
  }
}
```

**Error Cases:**
| Status | Error Code | Mô tả |
|---|---|---|
| 400 | `VALIDATION_ERROR` | Dữ liệu không hợp lệ |
| 409 | `USER_001` | Email đã tồn tại |
| 409 | `USER_002` | Số điện thoại đã tồn tại |
| 429 | `RATE_LIMIT` | Vượt 10 requests/phút/IP |

---

## 1.2 Xác thực OTP

**POST** `/auth/verify-otp`

**Request Body:**
```json
{
  "userId": "usr_01HX3Z9N8K",
  "otp": "123456"
}
```

**Response 200:**
```json
{
  "success": true,
  "data": {
    "userId": "usr_01HX3Z9N8K",
    "isVerified": true,
    "onboardingRequired": true
  }
}
```

**Error Cases:**
| Status | Error Code | Mô tả |
|---|---|---|
| 400 | `AUTH_004` | OTP không đúng |
| 400 | `AUTH_005` | OTP hết hạn (sau 5 phút) |
| 429 | `RATE_LIMIT` | Vượt 5 lần thử |

---

## 1.3 Gửi lại OTP

**POST** `/auth/resend-otp`

**Request Body:**
```json
{
  "userId": "usr_01HX3Z9N8K"
}
```

**Response 200:**
```json
{
  "success": true,
  "message": "OTP đã được gửi lại.",
  "data": {
    "otpExpiry": "2025-04-01T10:10:00+07:00"
  }
}
```

**Giới hạn:** Tối đa 5 lần/ngày/user

---

## 1.4 Đăng nhập

**POST** `/auth/login`

**Request Body:**
```json
{
  "emailOrPhone": "an.nguyen@gmail.com",
  "password": "P@ssw0rd123"
}
```

**Response 200:**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGci...",
    "refreshToken": "eyJhbGci...",
    "expiresIn": 900,
    "user": {
      "userId": "usr_01HX3Z9N8K",
      "fullName": "Nguyễn Văn An",
      "email": "an.nguyen@gmail.com",
      "avatar": "https://cdn.nutriday.vn/avatars/default.png",
      "onboardingCompleted": true,
      "dietGoal": "weight_loss"
    }
  }
}
```

**Error Cases:**
| Status | Error Code | Mô tả |
|---|---|---|
| 401 | — | Sai mật khẩu |
| 403 | `AUTH_003` | Tài khoản chưa xác thực OTP |
| 403 | `AUTH_006` | Tài khoản bị khóa |
| 429 | `RATE_LIMIT` | Vượt 10 requests/phút/IP |

---

## 1.5 Làm mới Token

**POST** `/auth/refresh`

**Request Body:**
```json
{
  "refreshToken": "eyJhbGci..."
}
```

**Response 200:**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGci...",
    "expiresIn": 900
  }
}
```

**Error Cases:**
| Status | Error Code | Mô tả |
|---|---|---|
| 401 | `AUTH_001` | Refresh token hết hạn |
| 401 | `AUTH_002` | Refresh token đã bị thu hồi |

---

## 1.6 Đăng xuất

**POST** `/auth/logout`

**Headers:** `Authorization: Bearer <access_token>`

**Response 200:**
```json
{
  "success": true,
  "message": "Đăng xuất thành công"
}
```

> Thu hồi `refresh_token` hiện tại, xóa `device_token` khỏi push notification.

---

## 1.7 Quên mật khẩu

**POST** `/auth/forgot-password`

**Request Body:**
```json
{
  "emailOrPhone": "an.nguyen@gmail.com"
}
```

**Response 200:**
```json
{
  "success": true,
  "message": "Link đặt lại mật khẩu đã được gửi đến email / SĐT của bạn."
}
```

> Để bảo mật, luôn trả về `200` dù email/phone không tồn tại.

---

## 1.8 Đặt lại mật khẩu

**POST** `/auth/reset-password`

**Request Body:**
```json
{
  "resetToken": "abc123xyz",
  "newPassword": "NewP@ss456"
}
```

**Response 200:**
```json
{
  "success": true,
  "message": "Mật khẩu đã được cập nhật thành công."
}
```

**Error Cases:**
| Status | Mô tả |
|---|---|
| 400 | Token không hợp lệ hoặc đã dùng |
| 400 | Token hết hạn (sau 1 giờ) |
| 400 | Mật khẩu mới không đủ mạnh |

---

**Trang trước:** [00 — Overview ←](APID-ND-2025-001_00_Overview.md)
**Trang tiếp theo:** [02 — User API →](APID-ND-2025-001_02_User.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
