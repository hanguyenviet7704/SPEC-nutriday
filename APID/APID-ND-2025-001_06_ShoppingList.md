# APID-ND-2025-001_06 — Shopping List API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 06 — Shopping List |
| **Base Path** | `/shopping-lists` |
| **Auth Required** | Bearer Token (tất cả) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| POST | `/shopping-lists/generate` | Tạo danh sách mua sắm |
| GET | `/shopping-lists/:listId` | Lấy danh sách mua sắm |
| PATCH | `/shopping-lists/:listId/items/:itemId` | Đánh dấu đã mua 1 item |
| PATCH | `/shopping-lists/:listId/check-all` | Đánh dấu tất cả đã mua |
| GET | `/shopping-lists/:listId/export` | Xuất danh sách |

---

## Business Rules

> - Nguyên liệu trùng nhau (cùng `ingredientId`) được **gộp lại** — số lượng cộng dồn.
> - Số lượng nhân với `servings` của user.
> - Nguyên liệu phân nhóm theo `category`.
> - Yêu cầu thực đơn ngày (hoặc tuần) phải tồn tại trước.

---

## 6.1 Tạo danh sách mua sắm

**POST** `/shopping-lists/generate`

**Request Body:**
```json
{
  "type": "daily",
  "date": "2025-04-01"
}
```

**`type` Values:**
| Value | Mô tả |
|---|---|
| `daily` | Nguyên liệu cho 1 ngày (3 bữa) |
| `weekly` | Nguyên liệu tổng hợp cả tuần (7 ngày) |

> Với `type = weekly`, field `date` là ngày bắt đầu tuần.

**Response 201:**
```json
{
  "success": true,
  "data": {
    "listId": "shop_20250401_usr01",
    "type": "daily",
    "date": "2025-04-01",
    "totalItems": 12,
    "checkedItems": 0,
    "estimatedCost": 150000,
    "categories": [
      {
        "category": "Thịt & Cá",
        "categoryKey": "meat",
        "items": [
          {
            "itemId": "sitem_001",
            "ingredientId": "ing_002",
            "name": "Thịt bò",
            "quantity": 200,
            "unit": "g",
            "isChecked": false,
            "estimatedPrice": 60000
          }
        ]
      },
      {
        "category": "Rau củ",
        "categoryKey": "vegetable",
        "items": [
          {
            "itemId": "sitem_002",
            "ingredientId": "ing_015",
            "name": "Hành tây",
            "quantity": 1,
            "unit": "củ",
            "isChecked": false,
            "estimatedPrice": 5000
          },
          {
            "itemId": "sitem_003",
            "ingredientId": "ing_020",
            "name": "Cà rốt",
            "quantity": 2,
            "unit": "củ",
            "isChecked": false,
            "estimatedPrice": 8000
          }
        ]
      }
    ]
  }
}
```

**Error Cases:**
| Status | Error Code | Mô tả |
|---|---|---|
| 422 | `SHOP_001` | Chưa có thực đơn ngày/tuần tương ứng |

---

## 6.2 Lấy danh sách mua sắm

**GET** `/shopping-lists/:listId`

**Response 200:** Cấu trúc giống như response của **6.1** (không có `201`).

**Error Cases:**
| Status | Mô tả |
|---|---|
| 404 | listId không tồn tại |
| 403 | list không thuộc về user hiện tại |

---

## 6.3 Đánh dấu đã mua (1 item)

**PATCH** `/shopping-lists/:listId/items/:itemId`

**Request Body:**
```json
{
  "isChecked": true
}
```

**Response 200:**
```json
{
  "success": true,
  "data": {
    "itemId": "sitem_001",
    "isChecked": true,
    "checkedAt": "2025-04-01T08:30:00+07:00",
    "listProgress": {
      "checkedItems": 5,
      "totalItems": 12,
      "percentage": 41.7
    }
  }
}
```

---

## 6.4 Đánh dấu tất cả đã mua

**PATCH** `/shopping-lists/:listId/check-all`

**Request Body:**
```json
{
  "isChecked": true
}
```

> `isChecked: false` — bỏ chọn tất cả (reset danh sách).

**Response 200:**
```json
{
  "success": true,
  "message": "Đã cập nhật tất cả 12 nguyên liệu",
  "data": {
    "checkedItems": 12,
    "totalItems": 12,
    "percentage": 100
  }
}
```

---

## 6.5 Xuất danh sách mua sắm

**GET** `/shopping-lists/:listId/export?format=text`

**Query Parameters:**
| Param | Values | Default |
|---|---|---|
| `format` | `text` \| `json` | `text` |

**Response — format=text:**

```
Content-Type: text/plain; charset=utf-8

DANH SÁCH MUA SẮM - 01/04/2025
================================
THỊT & CÁ:
□ Thịt bò - 200g
□ Cá hồi - 150g

RAU CỦ:
□ Hành tây - 1 củ
□ Cà rốt - 2 củ
□ Bắp cải - 300g

NGŨ CỐC:
□ Gạo tẻ - 400g

GIA VỊ:
□ Nước mắm - 2 thìa
□ Muối - 1 thìa

Tổng ước tính: 150.000₫
================================
```

**Response — format=json:** Cấu trúc giống response `GET /shopping-lists/:listId`.

---

**Trang trước:** [05 — Meal Plan API ←](APID-ND-2025-001_05_MealPlan.md)
**Trang tiếp theo:** [07 — Search API →](APID-ND-2025-001_07_Search.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
