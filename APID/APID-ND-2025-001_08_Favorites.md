# APID-ND-2025-001_08 — Favorites API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 08 — Favorites |
| **Base Path** | `/favorites` |
| **Auth Required** | Bearer Token (tất cả) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | `/favorites` | Lấy danh sách yêu thích |
| POST | `/favorites` | Thêm món vào yêu thích |
| DELETE | `/favorites/:dishId` | Xóa món khỏi yêu thích |

---

## 8.1 Lấy danh sách yêu thích

**GET** `/favorites`

**Query Parameters:**
| Param | Type | Mô tả |
|---|---|---|
| `page` | number | Trang (default: 1) |
| `limit` | number | Số bản ghi (default: 20) |
| `sortBy` | string | `savedAt` \| `name` \| `calories` (default: `savedAt`) |
| `sortOrder` | string | `asc` \| `desc` (default: `desc`) |

**Response 200:**
```json
{
  "success": true,
  "data": [
    {
      "favoriteId": "fav_001",
      "dishId": "dish_001",
      "name": "Phở Bò",
      "image": "https://cdn.nutriday.vn/dishes/pho_bo.jpg",
      "calories": 450,
      "cookingTime": 45,
      "dietGoals": ["healthy", "high_protein"],
      "isNutritionistVerified": true,
      "savedAt": "2025-03-15T08:00:00+07:00"
    },
    {
      "favoriteId": "fav_002",
      "dishId": "dish_010",
      "name": "Cháo Gà",
      "image": "https://cdn.nutriday.vn/dishes/chao_ga.jpg",
      "calories": 380,
      "cookingTime": 30,
      "dietGoals": ["weight_loss", "diabetes"],
      "isNutritionistVerified": true,
      "savedAt": "2025-03-10T14:00:00+07:00"
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 8,
    "totalPages": 1
  }
}
```

---

## 8.2 Thêm vào yêu thích

**POST** `/favorites`

**Request Body:**
```json
{
  "dishId": "dish_001"
}
```

**Response 201:**
```json
{
  "success": true,
  "message": "Đã lưu vào danh sách yêu thích",
  "data": {
    "favoriteId": "fav_001",
    "dishId": "dish_001",
    "savedAt": "2025-04-01T10:00:00+07:00"
  }
}
```

**Error Cases:**
| Status | Mô tả |
|---|---|
| 404 | dishId không tồn tại |
| 409 | Món đã có trong danh sách yêu thích |

---

## 8.3 Xóa khỏi yêu thích

**DELETE** `/favorites/:dishId`

**Response 204:** No Content

**Error Cases:**
| Status | Mô tả |
|---|---|
| 404 | dishId không có trong danh sách yêu thích của user |

---

**Trang trước:** [07 — Search API ←](APID-ND-2025-001_07_Search.md)
**Trang tiếp theo:** [09 — Admin API →](APID-ND-2025-001_09_Admin.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
