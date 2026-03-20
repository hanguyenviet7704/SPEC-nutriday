# APID-ND-2025-001_10 — Notification API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 10 — Notification |
| **Base Path** | `/notifications` |
| **Auth Required** | Bearer Token (tất cả) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | `/notifications/settings` | Lấy cài đặt thông báo |
| PATCH | `/notifications/settings` | Cập nhật cài đặt thông báo |
| POST | `/notifications/device-token` | Đăng ký push token thiết bị |

---

## 10.1 Lấy cài đặt thông báo

**GET** `/notifications/settings`

**Response 200:**
```json
{
  "success": true,
  "data": {
    "breakfastReminder": true,
    "breakfastTime": "07:00",
    "lunchReminder": true,
    "lunchTime": "11:30",
    "dinnerReminder": true,
    "dinnerTime": "18:00",
    "weeklyPlanReminder": true,
    "weeklyPlanDay": "Sunday",
    "weeklyPlanTime": "20:00",
    "shoppingListReminder": false
  }
}
```

**Field Descriptions:**
| Field | Type | Mô tả |
|---|---|---|
| `breakfastReminder` | boolean | Nhắc nhở bữa sáng |
| `breakfastTime` | string | Giờ nhắc `HH:mm` |
| `lunchReminder` | boolean | Nhắc nhở bữa trưa |
| `lunchTime` | string | Giờ nhắc `HH:mm` |
| `dinnerReminder` | boolean | Nhắc nhở bữa tối |
| `dinnerTime` | string | Giờ nhắc `HH:mm` |
| `weeklyPlanReminder` | boolean | Nhắc lập thực đơn tuần |
| `weeklyPlanDay` | string | Ngày nhắc (Monday–Sunday) |
| `weeklyPlanTime` | string | Giờ nhắc `HH:mm` |
| `shoppingListReminder` | boolean | Nhắc mua sắm trước ngày dùng |

---

## 10.2 Cập nhật cài đặt thông báo

**PATCH** `/notifications/settings`

> Chỉ gửi các fields muốn thay đổi.

**Request Body (ví dụ):**
```json
{
  "breakfastReminder": false,
  "dinnerTime": "19:00",
  "weeklyPlanDay": "Saturday"
}
```

**Validation:**
| Field | Rule |
|---|---|
| `*Time` | Định dạng `HH:mm`, giờ 00–23, phút 00–59 |
| `weeklyPlanDay` | Một trong 7 ngày: `Monday` đến `Sunday` |

**Response 200:**
```json
{
  "success": true,
  "message": "Đã cập nhật cài đặt thông báo",
  "data": {
    "breakfastReminder": false,
    "breakfastTime": "07:00",
    "lunchReminder": true,
    "lunchTime": "11:30",
    "dinnerReminder": true,
    "dinnerTime": "19:00",
    "weeklyPlanReminder": true,
    "weeklyPlanDay": "Saturday",
    "weeklyPlanTime": "20:00",
    "shoppingListReminder": false
  }
}
```

---

## 10.3 Đăng ký Push Token

**POST** `/notifications/device-token`

**Request Body:**
```json
{
  "token": "ExponentPushToken[xxxxxxxxxxxxxxxxxxxxxx]",
  "platform": "ios"
}
```

**`platform` Values:** `ios` | `android`

**Response 200:**
```json
{
  "success": true,
  "message": "Đã đăng ký thiết bị nhận thông báo"
}
```

> Nếu token đã tồn tại → upsert (cập nhật `updatedAt`). Mỗi user có thể có nhiều device tokens (nhiều thiết bị).

---

**Trang trước:** [09 — Admin API ←](APID-ND-2025-001_09_Admin.md)
**Trang tiếp theo:** [11 — Error Codes →](APID-ND-2025-001_11_ErrorCodes.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
