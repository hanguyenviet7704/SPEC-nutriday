# APID-ND-2025-001_07 — Search API
# NutriDay API Design

---

| | |
|---|---|
| **Module** | 07 — Search |
| **Base Path** | `/search` · `/explore` |
| **Auth Required** | Bearer Token (tất cả) |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](APID-ND-2025-001_INDEX.md) |

---

## Danh sách Endpoints

| Method | Endpoint | Mô tả |
|---|---|---|
| GET | `/search/dishes` | Tìm kiếm món ăn theo tên |
| GET | `/search/autocomplete` | Gợi ý tự động khi gõ |
| GET | `/explore` | Khám phá / duyệt theo danh mục |

---

## 7.1 Tìm kiếm theo tên món

**GET** `/search/dishes`

**Query Parameters:**
| Param | Type | Mô tả |
|---|---|---|
| `q` | string | Từ khóa tìm kiếm (tối thiểu 1 ký tự) |
| `dietGoal` | string | Lọc theo mục tiêu dinh dưỡng |
| `mealType` | string | `breakfast` \| `lunch` \| `dinner` |
| `cookingTime` | number | Thời gian nấu tối đa (phút) |
| `page` | number | Trang (default: 1) |
| `limit` | number | Số bản ghi (default: 20) |

**Ví dụ:** `GET /search/dishes?q=phở&dietGoal=healthy&mealType=breakfast`

**Response 200:**
```json
{
  "success": true,
  "data": {
    "query": "phở",
    "totalResults": 5,
    "dishes": [
      {
        "dishId": "dish_001",
        "name": "Phở Bò",
        "image": "https://cdn.nutriday.vn/dishes/pho_bo.jpg",
        "calories": 450,
        "cookingTime": 45,
        "dietGoals": ["healthy", "high_protein"],
        "isNutritionistVerified": true,
        "isFavorited": true,
        "highlight": "**Phở** Bò truyền thống"
      },
      {
        "dishId": "dish_002",
        "name": "Phở Gà",
        "image": "https://cdn.nutriday.vn/dishes/pho_ga.jpg",
        "calories": 390,
        "cookingTime": 40,
        "dietGoals": ["healthy", "low_carb"],
        "isNutritionistVerified": true,
        "isFavorited": false,
        "highlight": "**Phở** Gà thanh nhẹ"
      }
    ],
    "suggestions": ["Phở Gà", "Phở Hải Sản", "Phở Chay"]
  },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 5,
    "totalPages": 1
  }
}
```

**Field Notes:**
| Field | Mô tả |
|---|---|
| `highlight` | Tên món với từ khóa được **bold** bằng Markdown |
| `suggestions` | Gợi ý từ khóa liên quan (tối đa 5) |

---

## 7.2 Gợi ý autocomplete

**GET** `/search/autocomplete`

**Query Parameters:**
| Param | Type | Mô tả |
|---|---|---|
| `q` | string | Tiền tố tìm kiếm (tối thiểu 1 ký tự) |
| `type` | string | `dish` (default) \| `ingredient` |
| `limit` | number | Số gợi ý (default: 5, max: 10) |

**Ví dụ:** `GET /search/autocomplete?q=ph&type=dish`

**Response 200:**
```json
{
  "success": true,
  "data": [
    "Phở Bò",
    "Phở Gà",
    "Phở Hải Sản",
    "Phở Chay",
    "Phở Sate"
  ]
}
```

**Ví dụ với type=ingredient:** `GET /search/autocomplete?q=thị&type=ingredient`

```json
{
  "success": true,
  "data": [
    "Thịt bò",
    "Thịt gà",
    "Thịt heo",
    "Thịt vịt"
  ]
}
```

> Dùng FULLTEXT index MySQL cho tốc độ nhanh. Kết quả trả về < 50ms.

---

## 7.3 Khám phá / Duyệt theo danh mục

**GET** `/explore`

**Query Parameters:**
| Param | Type | Mô tả |
|---|---|---|
| `dietGoal` | string | Lọc theo mục tiêu |
| `mealType` | string | `breakfast` \| `lunch` \| `dinner` |
| `sortBy` | string | `popular` \| `newest` \| `cookingTime` \| `calories` |
| `cookingTime` | number | Thời gian nấu tối đa (phút) |
| `page` | number | Trang (default: 1) |
| `limit` | number | Số bản ghi (default: 20) |

**Ví dụ:** `GET /explore?dietGoal=weight_loss&mealType=lunch&sortBy=popular&page=1`

**Response 200:**
```json
{
  "success": true,
  "data": [
    {
      "dishId": "dish_010",
      "name": "Cháo Gà",
      "slug": "chao-ga",
      "image": "https://cdn.nutriday.vn/dishes/chao_ga.jpg",
      "cookingTime": 30,
      "calories": 380,
      "dietGoals": ["weight_loss", "diabetes"],
      "mealTypes": ["breakfast", "lunch"],
      "isNutritionistVerified": true,
      "isFavorited": false,
      "rank": 1,
      "viewCount": 15420
    },
    {
      "dishId": "dish_025",
      "name": "Cơm Gà Hấp Gừng",
      "slug": "com-ga-hap-gung",
      "image": "https://cdn.nutriday.vn/dishes/com_ga.jpg",
      "cookingTime": 40,
      "calories": 580,
      "dietGoals": ["healthy", "weight_loss"],
      "mealTypes": ["lunch", "dinner"],
      "isNutritionistVerified": true,
      "isFavorited": true,
      "rank": 2,
      "viewCount": 12300
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 120,
    "totalPages": 6
  }
}
```

**`sortBy` Logic:**
| Value | Sắp xếp |
|---|---|
| `popular` | `viewCount DESC` |
| `newest` | `publishedAt DESC` |
| `cookingTime` | `cookingTime ASC` |
| `calories` | `calories ASC` |

---

**Trang trước:** [06 — Shopping List API ←](APID-ND-2025-001_06_ShoppingList.md)
**Trang tiếp theo:** [08 — Favorites API →](APID-ND-2025-001_08_Favorites.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
