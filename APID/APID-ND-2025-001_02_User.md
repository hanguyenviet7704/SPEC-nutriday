# APID-ND-2025-001_02 — User API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 02 — User |
| **Base Path** | `/users` |
| **Auth Required** | Bearer Token (tất cả) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | `/users/me` | Lấy thông tin cá nhân |
| PATCH | `/users/me` | Cập nhật hồ sơ |
| POST | `/users/me/onboarding` | Hoàn thành onboarding |
| PATCH | `/users/me/diet-goal` | Cập nhật mục tiêu dinh dưỡng |
| POST | `/users/me/avatar` | Upload ảnh đại diện |
| GET | `/users/me/meal-history` | Lịch sử bữa ăn |

---

## 2.1 Lấy thông tin cá nhân

**GET** `/users/me`

**Response 200:**
```json
{
  "success": true,
  "data": {
    "userId": "usr_01HX3Z9N8K",
    "fullName": "Nguyễn Văn An",
    "email": "an.nguyen@gmail.com",
    "phone": "0901234567",
    "avatar": "https://cdn.nutriday.vn/avatars/usr_01HX3Z9N8K.jpg",
    "dateOfBirth": "1995-06-15",
    "gender": "male",
    "height": 170,
    "weight": 65,
    "dietGoal": "weight_loss",
    "servings": 2,
    "allergies": ["shellfish", "peanuts"],
    "onboardingCompleted": true,
    "createdAt": "2025-04-01T10:00:00+07:00"
  }
}
```

---

## 2.2 Cập nhật hồ sơ

**PATCH** `/users/me`

> Tất cả fields đều tùy chọn — chỉ gửi fields muốn cập nhật.

**Request Body:**
```json
{
  "fullName": "Nguyễn Văn An",
  "dateOfBirth": "1995-06-15",
  "gender": "male",
  "height": 172,
  "weight": 63
}
```

**Field Constraints:**
| Field | Type | Rule |
|---|---|---|
| `fullName` | string | 2–100 ký tự |
| `dateOfBirth` | string | ISO date `YYYY-MM-DD`, không tương lai |
| `gender` | string | `male` \| `female` \| `other` |
| `height` | number | 50–250 (cm) |
| `weight` | number | 10–300 (kg) |

**Response 200:**
```json
{
  "success": true,
  "message": "Cập nhật hồ sơ thành công",
  "data": { ...updatedUser }
}
```

---

## 2.3 Hoàn thành Onboarding

**POST** `/users/me/onboarding`

> Chỉ gọi được 1 lần. Sau khi `onboardingCompleted = true` dùng endpoint `PATCH /diet-goal`.

**Request Body:**
```json
{
  "dietGoal": "weight_loss",
  "servings": 2,
  "allergies": ["shellfish", "peanuts"]
}
```

**Diet Goal Values:**
| Value | Mô tả |
|---|---|
| `weight_loss` | Giảm cân |
| `weight_gain` | Tăng cân |
| `diabetes` | Tiểu đường |
| `eat_clean` | Eat Clean |
| `vegetarian` | Ăn chay |
| `healthy` | Healthy |
| `low_carb` | Ít tinh bột |
| `high_protein` | Nhiều protein |

**Allergen Values:** `shellfish` · `peanuts` · `gluten` · `dairy` · `eggs` · `soy` · `tree_nuts` · `fish`

**Response 200:**
```json
{
  "success": true,
  "message": "Cài đặt mục tiêu dinh dưỡng thành công",
  "data": {
    "dietGoal": "weight_loss",
    "servings": 2,
    "allergies": ["shellfish", "peanuts"],
    "onboardingCompleted": true
  }
}
```

**Error Cases:**
| Status | Mô tả |
|---|---|
| 409 | Onboarding đã hoàn thành trước đó |
| 400 | `dietGoal` không hợp lệ |

---

## 2.4 Cập nhật mục tiêu dinh dưỡng

**PATCH** `/users/me/diet-goal`

> Dùng sau khi onboarding đã hoàn thành. Thay đổi goal sẽ reset thực đơn ngày tiếp theo.

**Request Body:**
```json
{
  "dietGoal": "eat_clean",
  "servings": 3,
  "allergies": []
}
```

**Response 200:**
```json
{
  "success": true,
  "message": "Đã cập nhật mục tiêu dinh dưỡng",
  "data": {
    "dietGoal": "eat_clean",
    "servings": 3,
    "allergies": []
  }
}
```

---

## 2.5 Upload ảnh đại diện

**POST** `/users/me/avatar`

**Content-Type:** `multipart/form-data`

| Field | Type | Giới hạn |
|---|---|---|
| `avatar` | file | JPG/PNG/WebP, tối đa 5MB |

**Response 200:**
```json
{
  "success": true,
  "data": {
    "avatarUrl": "https://cdn.nutriday.vn/avatars/usr_01HX3Z9N8K.jpg"
  }
}
```

**Error Cases:**
| Status | Mô tả |
|---|---|
| 400 | File không phải ảnh |
| 400 | File vượt 5MB |

---

## 2.6 Lịch sử bữa ăn

**GET** `/users/me/meal-history`

**Query Parameters:**
| Param | Type | Default | Mô tả |
|---|---|---|---|
| `page` | number | 1 | Số trang |
| `limit` | number | 20 | Số bản ghi |
| `fromDate` | string | — | ISO date `YYYY-MM-DD` |
| `toDate` | string | — | ISO date `YYYY-MM-DD` |

**Ví dụ:** `GET /users/me/meal-history?page=1&limit=20&fromDate=2025-03-01&toDate=2025-03-31`

**Response 200:**
```json
{
  "success": true,
  "data": [
    {
      "date": "2025-03-20",
      "meals": [
        {
          "mealType": "breakfast",
          "dish": {
            "dishId": "dish_001",
            "name": "Phở Bò",
            "image": "https://cdn.nutriday.vn/dishes/pho_bo.jpg",
            "calories": 450
          }
        },
        {
          "mealType": "lunch",
          "dish": {
            "dishId": "dish_025",
            "name": "Cơm Gà Hấp Gừng",
            "image": "https://cdn.nutriday.vn/dishes/com_ga.jpg",
            "calories": 580
          }
        },
        {
          "mealType": "dinner",
          "dish": {
            "dishId": "dish_040",
            "name": "Canh Rau Củ",
            "image": "https://cdn.nutriday.vn/dishes/canh_rau.jpg",
            "calories": 520
          }
        }
      ]
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 90,
    "totalPages": 5
  }
}
```

---

**Trang trước:** [01 — Auth API ←](APID-ND-2025-001_01_Auth.md)
**Trang tiếp theo:** [03 — Dish API →](APID-ND-2025-001_03_Dish.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
