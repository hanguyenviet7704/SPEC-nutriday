# APID-ND-2025-001_11 — Error Codes
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 11 — Error Codes |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Auth Errors

| Code | HTTP | Mô tả | Module |
|---|---|---|---|
| `AUTH_001` | 401 | Access token hết hạn | Auth |
| `AUTH_002` | 401 | Token không hợp lệ hoặc đã bị thu hồi | Auth |
| `AUTH_003` | 403 | Tài khoản chưa xác thực OTP | Auth |
| `AUTH_004` | 400 | OTP không đúng | Auth |
| `AUTH_005` | 400 | OTP hết hạn (sau 5 phút) | Auth |
| `AUTH_006` | 403 | Tài khoản bị khóa/suspended | Auth |

---

## User Errors

| Code | HTTP | Mô tả | Module |
|---|---|---|---|
| `USER_001` | 409 | Email đã tồn tại | User |
| `USER_002` | 409 | Số điện thoại đã tồn tại | User |
| `USER_003` | 422 | Onboarding chưa hoàn thành — không thể tiếp tục | User |

---

## Dish Errors

| Code | HTTP | Mô tả | Module |
|---|---|---|---|
| `DISH_001` | 404 | Món ăn không tồn tại | Dish |
| `DISH_002` | 404 | Món ăn chưa được duyệt (status ≠ published) | Dish |

---

## Meal Plan Errors

| Code | HTTP | Mô tả | Module |
|---|---|---|---|
| `PLAN_001` | 422 | Không đủ món phù hợp để tạo thực đơn | Meal Plan |
| `PLAN_002` | 409 | Thực đơn ngày đã tồn tại | Meal Plan |

---

## Shopping List Errors

| Code | HTTP | Mô tả | Module |
|---|---|---|---|
| `SHOP_001` | 422 | Chưa có thực đơn để tạo danh sách mua sắm | Shopping List |

---

## Admin / Import Errors

| Code | HTTP | Mô tả | Module |
|---|---|---|---|
| `IMPORT_001` | 400 | File không đúng định dạng (phải là .xlsx) | Admin |
| `IMPORT_002` | 422 | Dữ liệu trong file không hợp lệ | Admin |

---

## Generic Errors

| Code | HTTP | Mô tả | Áp dụng |
|---|---|---|---|
| `VALIDATION_ERROR` | 400 | Lỗi validation dữ liệu đầu vào | Toàn bộ |
| `FORBIDDEN` | 403 | Không có quyền thực hiện hành động | Toàn bộ |
| `NOT_FOUND` | 404 | Tài nguyên không tồn tại | Toàn bộ |
| `CONFLICT` | 409 | Dữ liệu đã tồn tại | Toàn bộ |
| `RATE_LIMIT` | 429 | Vượt quá giới hạn số request | Toàn bộ |
| `INTERNAL_ERROR` | 500 | Lỗi hệ thống nội bộ | Toàn bộ |

---

## Error Response Format

```json
{
  "success": false,
  "statusCode": 400,
  "message": "Mô tả lỗi thân thiện với người dùng",
  "error": {
    "code": "VALIDATION_ERROR",
    "details": [
      {
        "field": "email",
        "message": "Email không hợp lệ"
      },
      {
        "field": "password",
        "message": "Mật khẩu phải có ít nhất 8 ký tự"
      }
    ]
  },
  "timestamp": "2025-04-01T10:00:00+07:00"
}
```

> - `message`: Thông báo lỗi tổng quát, hiển thị cho user.
> - `error.code`: Mã lỗi dùng để xử lý logic phía client.
> - `error.details`: Danh sách lỗi chi tiết (chỉ có khi `VALIDATION_ERROR`).

---

**Trang trước:** [10 — Notification API ←](APID-ND-2025-001_10_Notification.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
