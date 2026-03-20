# APID-ND-2025-001_03 — Dish API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 03 — Dish |
| **Base Path** | `/dishes` |
| **Auth Required** | Bearer Token (tất cả) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | `/dishes` | Danh sách món ăn (có filter) |
| GET | `/dishes/:dishId` | Chi tiết món ăn |
| GET | `/dishes/:dishId/substitutions` | Gợi ý thay thế nguyên liệu |

---

## 3.1 Danh sách món ăn

**GET** `/dishes`

**Query Parameters:**
| Param | Type | Mô tả |
|---|---|---|
| `dietGoal` | string | Lọc theo mục tiêu (`weight_loss`, `vegetarian`, ...) |
| `mealType` | string | `breakfast` \| `lunch` \| `dinner` |
| `cookingTime` | number | Thời gian nấu tối đa (phút) |
| `keyword` | string | Tìm theo tên món |
| `page` | number | Trang (default: 1) |
| `limit` | number | Số bản ghi (default: 20, max: 100) |
| `sortBy` | string | `name` \| `calories` \| `cookingTime` \| `createdAt` |
| `sortOrder` | string | `asc` \| `desc` (default: `desc`) |

**Ví dụ:** `GET /dishes?dietGoal=weight_loss&mealType=breakfast&cookingTime=30&page=1`

**Response 200:**
```json
{
  "success": true,
  "data": [
    {
      "dishId": "dish_001",
      "name": "Phở Bò",
      "slug": "pho-bo",
      "image": "https://cdn.nutriday.vn/dishes/pho_bo.jpg",
      "cookingTime": 45,
      "servings": 1,
      "calories": 450,
      "dietGoals": ["healthy", "high_protein"],
      "mealTypes": ["breakfast", "lunch"],
      "isNutritionistVerified": true,
      "isFavorited": false
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 240,
    "totalPages": 12
  }
}
```

> `isFavorited` phản ánh trạng thái yêu thích của user đang đăng nhập.

---

## 3.2 Chi tiết món ăn

**GET** `/dishes/:dishId`

**Response 200:**
```json
{
  "success": true,
  "data": {
    "dishId": "dish_001",
    "name": "Phở Bò",
    "slug": "pho-bo",
    "description": "Món phở truyền thống với nước dùng xương bò đậm đà...",
    "image": "https://cdn.nutriday.vn/dishes/pho_bo.jpg",
    "images": [
      "https://cdn.nutriday.vn/dishes/pho_bo_1.jpg",
      "https://cdn.nutriday.vn/dishes/pho_bo_2.jpg"
    ],
    "cookingTime": 45,
    "prepTime": 15,
    "servings": 1,
    "difficulty": "medium",
    "dietGoals": ["healthy", "high_protein"],
    "mealTypes": ["breakfast", "lunch"],
    "allergens": ["gluten"],
    "nutrition": {
      "calories": 450,
      "protein": 28,
      "carbs": 52,
      "fat": 12,
      "fiber": 3,
      "sodium": 920
    },
    "ingredients": [
      {
        "ingredientId": "ing_001",
        "name": "Bánh phở",
        "quantity": 200,
        "unit": "g",
        "substitutes": [
          {
            "ingredientId": "ing_045",
            "name": "Bún tươi",
            "note": "Thay thế hoàn toàn"
          }
        ]
      },
      {
        "ingredientId": "ing_002",
        "name": "Thịt bò",
        "quantity": 100,
        "unit": "g",
        "substitutes": []
      }
    ],
    "recipeSteps": [
      {
        "stepNumber": 1,
        "instruction": "Ninh xương bò trong 3-4 giờ để lấy nước dùng.",
        "duration": 240,
        "image": null
      },
      {
        "stepNumber": 2,
        "instruction": "Trần bánh phở qua nước sôi.",
        "duration": 2,
        "image": null
      }
    ],
    "tags": ["truyền-thống", "nóng", "nước"],
    "isNutritionistVerified": true,
    "nutritionistNote": "Món giàu protein, phù hợp bữa sáng.",
    "isFavorited": true,
    "createdAt": "2025-04-01T10:00:00+07:00",
    "updatedAt": "2025-04-10T08:00:00+07:00"
  }
}
```

**Field Notes:**
| Field | Mô tả |
|---|---|
| `difficulty` | `easy` \| `medium` \| `hard` |
| `nutrition.sodium` | đơn vị mg |
| `recipeSteps[].duration` | đơn vị giây |
| `isNutritionistVerified` | Có badge "Đã kiểm duyệt dinh dưỡng" |

**Error Cases:**
| Status | Error Code | Mô tả |
|---|---|---|
| 404 | `DISH_001` | dishId không tồn tại |
| 404 | `DISH_002` | Món chưa được duyệt (status ≠ published) |

---

## 3.3 Gợi ý thay thế nguyên liệu

**GET** `/dishes/:dishId/substitutions`

**Response 200:**
```json
{
  "success": true,
  "data": {
    "dishId": "dish_001",
    "dishName": "Phở Bò",
    "substitutions": [
      {
        "originalIngredient": {
          "ingredientId": "ing_001",
          "name": "Bánh phở",
          "quantity": 200,
          "unit": "g"
        },
        "substitutes": [
          {
            "ingredientId": "ing_045",
            "name": "Bún tươi",
            "quantity": 200,
            "unit": "g",
            "note": "Thay thế hoàn toàn — thích hợp cho chế độ eat clean",
            "nutritionImpact": "Ít calories hơn 15%"
          },
          {
            "ingredientId": "ing_046",
            "name": "Mì gạo",
            "quantity": 180,
            "unit": "g",
            "note": "Ít tinh bột hơn",
            "nutritionImpact": "Giảm carbs ~20%"
          }
        ]
      }
    ]
  }
}
```

> Chỉ trả về các nguyên liệu **có** substitutes. Nguyên liệu không có thay thế sẽ bị bỏ qua.

---

**Trang trước:** [02 — User API ←](APID-ND-2025-001_02_User.md)
**Trang tiếp theo:** [04 — Ingredient API →](APID-ND-2025-001_04_Ingredient.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
