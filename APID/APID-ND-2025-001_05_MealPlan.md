# APID-ND-2025-001_05 — Meal Plan API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 05 — Meal Plan |
| **Base Path** | `/meal-plans` |
| **Auth Required** | Bearer Token (tất cả) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | `/meal-plans/today` | Lấy thực đơn hôm nay |
| GET | `/meal-plans/weekly` | Lấy thực đơn cả tuần |
| POST | `/meal-plans/generate` | Tạo thực đơn tự động |
| GET | `/meal-plans/:planId/swap-suggestions` | Lấy gợi ý đổi bữa |
| PATCH | `/meal-plans/:planId/swap` | Xác nhận đổi bữa |
| POST | `/meal-plans/:planId/randomize` | Tạo ngẫu nhiên toàn bộ |

---

## Business Rules

> - Không trùng lặp cùng món trong vòng **7 ngày** gần nhất.
> - Chỉ chọn món có `status = published`.
> - Lọc theo `dietGoal` của user tại thời điểm generate.
> - Loại bỏ món có allergen của user.

---

## 5.1 Lấy thực đơn hôm nay

**GET** `/meal-plans/today`

**Response 200:**
```json
{
  "success": true,
  "data": {
    "date": "2025-04-01",
    "planId": "plan_20250401_usr01",
    "dietGoal": "weight_loss",
    "totalNutrition": {
      "calories": 1480,
      "protein": 85,
      "carbs": 175,
      "fat": 42
    },
    "meals": [
      {
        "mealType": "breakfast",
        "dish": {
          "dishId": "dish_010",
          "name": "Cháo Gà",
          "image": "https://cdn.nutriday.vn/dishes/chao_ga.jpg",
          "calories": 380,
          "cookingTime": 30,
          "isNutritionistVerified": true
        }
      },
      {
        "mealType": "lunch",
        "dish": {
          "dishId": "dish_025",
          "name": "Cơm Gà Hấp Gừng",
          "image": "https://cdn.nutriday.vn/dishes/com_ga.jpg",
          "calories": 580,
          "cookingTime": 40,
          "isNutritionistVerified": true
        }
      },
      {
        "mealType": "dinner",
        "dish": {
          "dishId": "dish_040",
          "name": "Canh Rau Củ",
          "image": "https://cdn.nutriday.vn/dishes/canh_rau.jpg",
          "calories": 520,
          "cookingTime": 25,
          "isNutritionistVerified": false
        }
      }
    ]
  }
}
```

**Note:** Nếu chưa có thực đơn hôm nay, hệ thống tự động generate trước khi trả về.

---

## 5.2 Lấy thực đơn cả tuần

**GET** `/meal-plans/weekly?weekStart=2025-03-31`

**Query Parameters:**
| Param | Type | Mô tả |
|---|---|---|
| `weekStart` | string | ISO date — ngày bắt đầu tuần (default: Thứ 2 của tuần hiện tại) |

**Response 200:**
```json
{
  "success": true,
  "data": {
    "weekStart": "2025-03-31",
    "weekEnd": "2025-04-06",
    "days": [
      {
        "date": "2025-03-31",
        "dayOfWeek": "Monday",
        "hasPlan": true,
        "meals": {
          "breakfast": {
            "dishId": "dish_010",
            "name": "Cháo Gà",
            "image": "https://cdn.nutriday.vn/dishes/chao_ga.jpg",
            "calories": 380
          },
          "lunch": {
            "dishId": "dish_025",
            "name": "Cơm Gà Hấp Gừng",
            "image": "https://cdn.nutriday.vn/dishes/com_ga.jpg",
            "calories": 580
          },
          "dinner": {
            "dishId": "dish_040",
            "name": "Canh Rau Củ",
            "image": "https://cdn.nutriday.vn/dishes/canh_rau.jpg",
            "calories": 520
          }
        },
        "totalCalories": 1480
      },
      {
        "date": "2025-04-01",
        "dayOfWeek": "Tuesday",
        "hasPlan": false,
        "meals": null,
        "totalCalories": 0
      }
    ]
  }
}
```

> `hasPlan: false` nghĩa là ngày đó chưa có thực đơn. `meals = null`.

---

## 5.3 Tạo thực đơn tự động

**POST** `/meal-plans/generate`

**Request Body:**
```json
{
  "date": "2025-04-01",
  "mode": "single"
}
```

**`mode` Values:**
| Value | Mô tả |
|---|---|
| `single` | Tạo cho 1 ngày chỉ định |
| `week` | Tạo cho 7 ngày từ `date` |

**Response 201:**
```json
{
  "success": true,
  "message": "Thực đơn đã được tạo thành công",
  "data": {
    "generatedDays": 1,
    "plans": [
      {
        "date": "2025-04-01",
        "planId": "plan_20250401_usr01",
        "meals": { ... }
      }
    ]
  }
}
```

**Error Cases:**
| Status | Error Code | Mô tả |
|---|---|---|
| 409 | `PLAN_002` | Thực đơn ngày đã tồn tại (dùng PATCH swap để thay đổi) |
| 422 | `PLAN_001` | Không đủ món phù hợp để generate |

---

## 5.4 Lấy gợi ý đổi bữa

**GET** `/meal-plans/:planId/swap-suggestions?mealType=lunch`

**Query Parameters:**
| Param | Required | Mô tả |
|---|---|---|
| `mealType` | Có | `breakfast` \| `lunch` \| `dinner` |

**Response 200:**
```json
{
  "success": true,
  "data": {
    "mealType": "lunch",
    "currentDish": {
      "dishId": "dish_025",
      "name": "Cơm Gà Hấp Gừng",
      "calories": 580
    },
    "suggestions": [
      {
        "dishId": "dish_030",
        "name": "Bún Bò Huế",
        "image": "https://cdn.nutriday.vn/dishes/bun_bo.jpg",
        "calories": 560,
        "cookingTime": 35
      },
      {
        "dishId": "dish_031",
        "name": "Mì Xào Bò",
        "image": "https://cdn.nutriday.vn/dishes/mi_xao.jpg",
        "calories": 540,
        "cookingTime": 20
      },
      {
        "dishId": "dish_032",
        "name": "Cơm Tấm",
        "image": "https://cdn.nutriday.vn/dishes/com_tam.jpg",
        "calories": 620,
        "cookingTime": 30
      },
      {
        "dishId": "dish_033",
        "name": "Bánh Mì Thịt",
        "image": "https://cdn.nutriday.vn/dishes/banh_mi.jpg",
        "calories": 480,
        "cookingTime": 10
      },
      {
        "dishId": "dish_034",
        "name": "Hủ Tiếu Nam Vang",
        "image": "https://cdn.nutriday.vn/dishes/hu_tieu.jpg",
        "calories": 510,
        "cookingTime": 20
      }
    ]
  }
}
```

> Luôn trả về đúng **5 suggestions**. Không bao gồm món hiện tại và các món đã ăn trong 7 ngày.

---

## 5.5 Xác nhận đổi bữa

**PATCH** `/meal-plans/:planId/swap`

**Request Body:**
```json
{
  "mealType": "lunch",
  "newDishId": "dish_030"
}
```

**Response 200:**
```json
{
  "success": true,
  "message": "Đã đổi bữa trưa thành công",
  "data": {
    "mealType": "lunch",
    "previousDish": {
      "dishId": "dish_025",
      "name": "Cơm Gà Hấp Gừng"
    },
    "newDish": {
      "dishId": "dish_030",
      "name": "Bún Bò Huế",
      "calories": 560
    },
    "updatedTotalCalories": 1460
  }
}
```

**Error Cases:**
| Status | Mô tả |
|---|---|
| 404 | planId không tồn tại |
| 422 | newDishId không phù hợp với mealType |
| 403 | planId không thuộc về user hiện tại |

---

## 5.6 Tạo ngẫu nhiên toàn bộ

**POST** `/meal-plans/:planId/randomize`

**Request Body:**
```json
{
  "mealTypes": ["breakfast", "lunch", "dinner"]
}
```

> `mealTypes` có thể chứa 1, 2 hoặc 3 bữa. Chỉ bữa được liệt kê mới bị thay thế.

**Response 200:**
```json
{
  "success": true,
  "message": "Đã tạo thực đơn ngẫu nhiên",
  "data": {
    "updatedMeals": ["breakfast", "lunch", "dinner"],
    "meals": {
      "breakfast": { "dishId": "dish_012", "name": "Bánh Cuốn", "calories": 340 },
      "lunch": { "dishId": "dish_038", "name": "Bún Thịt Nướng", "calories": 590 },
      "dinner": { "dishId": "dish_047", "name": "Cá Kho Tộ", "calories": 480 }
    }
  }
}
```

---

**Trang trước:** [04 — Ingredient API ←](APID-ND-2025-001_04_Ingredient.md)
**Trang tiếp theo:** [06 — Shopping List API →](APID-ND-2025-001_06_ShoppingList.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
