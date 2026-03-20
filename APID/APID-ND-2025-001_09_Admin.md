# APID-ND-2025-001_09 — Admin API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 09 — Admin |
| **Base Path** | `/admin` |
| **Auth Required** | Bearer Token — Admin account |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Phân quyền

| Role | Quyền |
|---|---|
| `super_admin` | Toàn quyền |
| `content_editor` | Tạo/sửa/xóa món · Không duyệt · Không xóa published |
| `nutritionist` | Chỉ xem · Duyệt/từ chối món |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả | Role |
|---|---|---|---|
| GET | `/admin/dashboard` | Tổng quan KPIs | super_admin |
| GET | `/admin/dishes` | Danh sách món (admin) | Tất cả |
| POST | `/admin/dishes` | Tạo món mới | super_admin, content_editor |
| PUT | `/admin/dishes/:dishId` | Cập nhật món | super_admin, content_editor |
| DELETE | `/admin/dishes/:dishId` | Xóa món | super_admin |
| POST | `/admin/dishes/:dishId/review` | Duyệt/từ chối | nutritionist |
| POST | `/admin/dishes/import` | Import Excel | super_admin, content_editor |
| GET | `/admin/dishes/export` | Export Excel | super_admin |
| GET | `/admin/users` | Danh sách users | super_admin |
| GET | `/admin/users/:userId` | Chi tiết user | super_admin |
| PATCH | `/admin/users/:userId/status` | Khóa/mở khóa user | super_admin |
| GET | `/admin/reports/dish-coverage` | Báo cáo độ phủ món | super_admin |
| GET | `/admin/reports/user-activity` | Báo cáo hoạt động | super_admin |
| GET | `/admin/reports/popular-dishes` | Món phổ biến | super_admin |
| POST | `/admin/auth/login` | Đăng nhập admin | — |

---

## 9.1 Đăng nhập Admin

**POST** `/admin/auth/login`

**Request Body:**
```json
{
  "email": "superadmin@nutriday.vn",
  "password": "Admin@2025!"
}
```

**Response 200:**
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGci...",
    "refreshToken": "eyJhbGci...",
    "admin": {
      "adminId": "admin_001",
      "fullName": "Nguyễn Admin",
      "email": "superadmin@nutriday.vn",
      "role": "super_admin"
    }
  }
}
```

---

## 9.2 Dashboard Stats

**GET** `/admin/dashboard`

**Response 200:**
```json
{
  "success": true,
  "data": {
    "totalDishes": 380,
    "publishedDishes": 310,
    "draftDishes": 55,
    "pendingReview": 15,
    "rejectedDishes": 0,
    "totalUsers": 12500,
    "activeUsers": 8200,
    "newUsersToday": 45,
    "coverageByDietGoal": [
      { "dietGoal": "weight_loss", "label": "Giảm cân", "count": 52, "required": 30, "status": "ok" },
      { "dietGoal": "weight_gain", "label": "Tăng cân", "count": 38, "required": 30, "status": "ok" },
      { "dietGoal": "diabetes", "label": "Tiểu đường", "count": 31, "required": 30, "status": "ok" },
      { "dietGoal": "eat_clean", "label": "Eat Clean", "count": 45, "required": 30, "status": "ok" },
      { "dietGoal": "vegetarian", "label": "Ăn chay", "count": 28, "required": 30, "status": "warning" },
      { "dietGoal": "healthy", "label": "Healthy", "count": 60, "required": 30, "status": "ok" },
      { "dietGoal": "low_carb", "label": "Ít tinh bột", "count": 35, "required": 30, "status": "ok" },
      { "dietGoal": "high_protein", "label": "Nhiều protein", "count": 41, "required": 30, "status": "ok" }
    ]
  }
}
```

---

## 9.3 Danh sách món (Admin view)

**GET** `/admin/dishes`

**Query Parameters:**
| Param | Type | Mô tả |
|---|---|---|
| `status` | string | `draft` \| `pending_review` \| `published` \| `rejected` |
| `dietGoal` | string | Lọc theo mục tiêu |
| `keyword` | string | Tìm theo tên |
| `createdBy` | string | adminId của người tạo |
| `page` | number | Trang (default: 1) |
| `limit` | number | Số bản ghi (default: 20) |

**Response 200:**
```json
{
  "success": true,
  "data": [
    {
      "dishId": "dish_001",
      "name": "Phở Bò",
      "status": "published",
      "dietGoals": ["healthy", "high_protein"],
      "mealTypes": ["breakfast", "lunch"],
      "isNutritionistVerified": true,
      "createdBy": { "adminId": "admin_002", "fullName": "Editor A" },
      "reviewedBy": { "adminId": "admin_003", "fullName": "Dr. Linh" },
      "publishedAt": "2025-04-05T10:00:00+07:00",
      "createdAt": "2025-04-01T08:00:00+07:00"
    }
  ],
  "meta": { "page": 1, "limit": 20, "total": 310 }
}
```

---

## 9.4 Tạo món mới

**POST** `/admin/dishes`

**Request Body:**
```json
{
  "name": "Cơm Chiên Trứng",
  "description": "Cơm chiên với trứng và rau củ đơn giản...",
  "cookingTime": 15,
  "prepTime": 5,
  "servings": 1,
  "difficulty": "easy",
  "dietGoals": ["healthy", "eat_clean"],
  "mealTypes": ["lunch", "dinner"],
  "allergens": ["eggs"],
  "nutrition": {
    "calories": 420,
    "protein": 15,
    "carbs": 58,
    "fat": 14,
    "fiber": 2,
    "sodium": 450
  },
  "ingredients": [
    { "ingredientId": "ing_003", "quantity": 200, "unit": "g", "note": "Cơm nguội" },
    { "ingredientId": "ing_008", "quantity": 2, "unit": "quả" }
  ],
  "recipeSteps": [
    { "stepNumber": 1, "instruction": "Đánh trứng với muối và tiêu.", "duration": 120 },
    { "stepNumber": 2, "instruction": "Phi hành với dầu ăn cho thơm.", "duration": 60 },
    { "stepNumber": 3, "instruction": "Cho cơm vào xào với lửa lớn.", "duration": 180 }
  ]
}
```

**Validation:**
| Field | Rule |
|---|---|
| `name` | 2–200 ký tự, unique |
| `cookingTime` | 1–480 phút |
| `dietGoals` | ≥ 1 giá trị hợp lệ |
| `mealTypes` | ≥ 1 giá trị hợp lệ |
| `nutrition.calories` | > 0 |
| `ingredients` | ≥ 1 item |
| `recipeSteps` | ≥ 1 bước |

**Response 201:**
```json
{
  "success": true,
  "data": {
    "dishId": "dish_new_001",
    "name": "Cơm Chiên Trứng",
    "status": "draft",
    "createdAt": "2025-04-01T10:00:00+07:00"
  }
}
```

---

## 9.5 Cập nhật món

**PUT** `/admin/dishes/:dishId`

> Body giống **9.4**. Trả về toàn bộ dish object sau khi cập nhật.

> Nếu dish đang `published`, cập nhật sẽ đưa về `pending_review`.

---

## 9.6 Xóa món

**DELETE** `/admin/dishes/:dishId`

**Response 204:** No Content

**Error Cases:**
| Status | Mô tả |
|---|---|
| 403 | Không phải `super_admin` |
| 422 | Không thể xóa món đang được dùng trong active meal_plans |

---

## 9.7 Duyệt / Từ chối món

**POST** `/admin/dishes/:dishId/review`

**Request Body:**
```json
{
  "action": "approve",
  "note": "Thông tin dinh dưỡng chính xác. Phù hợp chế độ giảm cân."
}
```

**`action` Values:**
| Value | Status sau | Mô tả |
|---|---|---|
| `approve` | `published` | Duyệt — món hiển thị cho users ngay |
| `reject` | `rejected` | Từ chối — cần sửa và gửi lại |

**Response 200:**
```json
{
  "success": true,
  "message": "Đã duyệt và phát hành món ăn",
  "data": {
    "dishId": "dish_001",
    "status": "published",
    "isNutritionistVerified": true,
    "publishedAt": "2025-04-05T10:00:00+07:00"
  }
}
```

---

## 9.8 Import Excel

**POST** `/admin/dishes/import`

**Content-Type:** `multipart/form-data`

| Field | Type | Giới hạn |
|---|---|---|
| `file` | file | File Excel `.xlsx`, tối đa 10MB |
| `mode` | string | `validate_only` \| `import` |

**`mode` Values:**
| Value | Mô tả |
|---|---|
| `validate_only` | Chỉ kiểm tra, không import |
| `import` | Kiểm tra và import các rows hợp lệ |

**Response 200:**
```json
{
  "success": true,
  "data": {
    "mode": "import",
    "totalRows": 50,
    "validRows": 47,
    "errorRows": 3,
    "importedCount": 47,
    "errors": [
      { "row": 5, "field": "calories", "message": "Giá trị không hợp lệ (phải > 0)" },
      { "row": 18, "field": "dietGoals", "message": "Giá trị 'diet' không hợp lệ" },
      { "row": 33, "field": "name", "message": "Tên món đã tồn tại" }
    ]
  }
}
```

**Excel Template Columns:**
`name` · `description` · `cookingTime` · `prepTime` · `difficulty` · `dietGoals` · `mealTypes` · `allergens` · `calories` · `protein` · `carbs` · `fat` · `fiber` · `sodium`

---

## 9.9 Export Excel

**GET** `/admin/dishes/export?status=published&dietGoal=weight_loss`

**Response:** File `.xlsx` download

**Headers:**
```
Content-Type: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="nutriday_dishes_20250401.xlsx"
```

---

## 9.10 Quản lý người dùng

**GET** `/admin/users?page=1&limit=20&status=active`

**GET** `/admin/users/:userId`

**PATCH** `/admin/users/:userId/status`
```json
{ "status": "suspended", "reason": "Vi phạm điều khoản sử dụng" }
```
**`status`**: `active` | `suspended`

---

## 9.11 Báo cáo & Thống kê

### Báo cáo độ phủ món

**GET** `/admin/reports/dish-coverage`

```json
{
  "data": {
    "byDietGoal": [ { "dietGoal": "weight_loss", "total": 52, "breakfast": 18, "lunch": 22, "dinner": 12 } ],
    "byMealType": { "breakfast": 95, "lunch": 120, "dinner": 95 }
  }
}
```

---

### Báo cáo hoạt động user

**GET** `/admin/reports/user-activity?fromDate=2025-03-01&toDate=2025-03-31`

```json
{
  "data": {
    "newUsers": 850,
    "activeUsers": 6200,
    "totalMealPlansGenerated": 45000,
    "totalSwaps": 12000,
    "topDietGoal": "weight_loss"
  }
}
```

---

### Món phổ biến

**GET** `/admin/reports/popular-dishes?period=30d&limit=10`

| Param | Values |
|---|---|
| `period` | `7d` \| `30d` \| `90d` |
| `limit` | 1–50 (default: 10) |

```json
{
  "data": [
    { "rank": 1, "dishId": "dish_010", "name": "Cháo Gà", "viewCount": 15420, "favoriteCount": 3200 }
  ]
}
```

---

**Trang trước:** [08 — Favorites API ←](APID-ND-2025-001_08_Favorites.md)
**Trang tiếp theo:** [10 — Notification API →](APID-ND-2025-001_10_Notification.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
