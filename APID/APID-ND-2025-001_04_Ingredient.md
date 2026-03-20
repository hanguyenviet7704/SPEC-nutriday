# APID-ND-2025-001_04 — Ingredient API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 04 — Ingredient |
| **Base Path** | `/ingredients` |
| **Auth Required** | Bearer Token (tất cả) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | `/ingredients` | Danh sách nguyên liệu (có filter) |
| POST | `/ingredients/search-dishes` | Tìm món ăn theo nguyên liệu có sẵn |

---

## 4.1 Danh sách nguyên liệu

**GET** `/ingredients`

**Query Parameters:**
| Param | Type | Mô tả |
|---|---|---|
| `keyword` | string | Tìm theo tên nguyên liệu |
| `category` | string | Lọc theo nhóm (xem bảng dưới) |
| `page` | number | Trang (default: 1) |
| `limit` | number | Số bản ghi (default: 20, max: 100) |

**Category Values:**
| Value | Mô tả |
|---|---|
| `meat` | Thịt |
| `seafood` | Hải sản |
| `vegetable` | Rau củ |
| `fruit` | Trái cây |
| `grain` | Ngũ cốc, tinh bột |
| `dairy` | Sữa & chế phẩm |
| `spice` | Gia vị khô |
| `sauce` | Nước chấm, sốt |
| `other` | Khác |

**Ví dụ:** `GET /ingredients?keyword=thịt&category=meat&page=1&limit=20`

**Response 200:**
```json
{
  "success": true,
  "data": [
    {
      "ingredientId": "ing_002",
      "name": "Thịt bò",
      "category": "meat",
      "unit": "g",
      "image": "https://cdn.nutriday.vn/ingredients/thit_bo.jpg",
      "allergenFlag": false
    },
    {
      "ingredientId": "ing_003",
      "name": "Thịt gà",
      "category": "meat",
      "unit": "g",
      "image": "https://cdn.nutriday.vn/ingredients/thit_ga.jpg",
      "allergenFlag": false
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 85,
    "totalPages": 5
  }
}
```

> Endpoint này được dùng chủ yếu cho tính năng **Search by Ingredient** (SCR-019) để user chọn nguyên liệu có sẵn.

---

## 4.2 Tìm món ăn theo nguyên liệu

**POST** `/ingredients/search-dishes`

**Request Body:**
```json
{
  "ingredientIds": ["ing_001", "ing_002", "ing_010"],
  "matchType": "any",
  "dietGoal": "healthy",
  "page": 1,
  "limit": 20
}
```

**Field Descriptions:**
| Field | Required | Mô tả |
|---|---|---|
| `ingredientIds` | Có | Danh sách ID nguyên liệu (1–20 items) |
| `matchType` | Có | `any` = có ít nhất 1 · `all` = có đủ tất cả |
| `dietGoal` | Không | Lọc thêm theo mục tiêu dinh dưỡng |
| `page` | Không | Trang (default: 1) |
| `limit` | Không | Số bản ghi (default: 20) |

**Response 200:**
```json
{
  "success": true,
  "data": [
    {
      "dishId": "dish_001",
      "name": "Phở Bò",
      "image": "https://cdn.nutriday.vn/dishes/pho_bo.jpg",
      "calories": 450,
      "cookingTime": 45,
      "dietGoals": ["healthy", "high_protein"],
      "matchedIngredients": 3,
      "totalIngredients": 8,
      "matchPercentage": 37.5
    },
    {
      "dishId": "dish_055",
      "name": "Bún Bò",
      "image": "https://cdn.nutriday.vn/dishes/bun_bo.jpg",
      "calories": 520,
      "cookingTime": 40,
      "dietGoals": ["healthy"],
      "matchedIngredients": 2,
      "totalIngredients": 6,
      "matchPercentage": 33.3
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 12,
    "totalPages": 1
  }
}
```

**Sorting:** Kết quả sắp xếp theo `matchPercentage` giảm dần.

**Error Cases:**
| Status | Mô tả |
|---|---|
| 400 | `ingredientIds` rỗng hoặc > 20 items |
| 400 | `matchType` không hợp lệ |

---

**Trang trước:** [03 — Dish API ←](APID-ND-2025-001_03_Dish.md)
**Trang tiếp theo:** [05 — Meal Plan API →](APID-ND-2025-001_05_MealPlan.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
