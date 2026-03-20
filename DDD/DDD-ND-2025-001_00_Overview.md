# DDD-ND-2025-001_00 — Tổng Quan & Quy Ước
# NutriDay Database Design

---

| | |
|---|---|
| **Module** | 00 — Overview |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](DDD-ND-2025-001_INDEX.md) |

---

## 1. CÔNG NGHỆ & CẤU HÌNH

| Thành phần | Giá trị |
|---|---|
| **Database** | MySQL 8.0+ |
| **Hosting** | AWS RDS (Multi-AZ) |
| **ORM** | TypeORM (NestJS) |
| **Migration** | TypeORM Migrations |
| **Charset** | utf8mb4 |
| **Collation** | utf8mb4_unicode_ci |
| **Backup** | RDS Automated — daily, 7 ngày retention |
| **Read Replica** | 1 Read Replica (cho reporting queries) |

**MySQL init config:**
```sql
SET GLOBAL max_connections = 500;
SET GLOBAL innodb_buffer_pool_size = 4G;
SET GLOBAL slow_query_log = 1;
SET GLOBAL long_query_time = 1;
```

---

## 2. SƠ ĐỒ QUAN HỆ (ERD — Tổng quát)

```
┌──────────────┐      ┌────────────────────┐
│    users     │─1:1──│ user_health_profile│
│              │─1:N──│ user_allergies      │
│              │─1:N──│ refresh_tokens      │
│              │─1:N──│ device_tokens       │
│              │─1:1──│ notification_settings│
│              │─1:N──│ meal_plans          │
│              │─1:N──│ shopping_lists      │
│              │─1:N──│ favorites           │
│              │─1:N──│ meal_history        │
└──────────────┘      └────────────────────┘

┌──────────────┐      ┌────────────────────┐
│    dishes    │─1:N──│ dish_images         │
│              │─1:N──│ dish_diet_goals     │
│              │─1:N──│ dish_meal_types     │
│              │─1:N──│ dish_allergens      │
│              │─1:1──│ dish_nutrition      │
│              │─1:N──│ recipe_steps        │
│              │─1:N──│ dish_ingredients    │
│              │─1:N──│ dish_reviews        │
└──────────────┘      └────────────────────┘

┌──────────────┐      ┌────────────────────┐
│ ingredients  │─1:N──│ dish_ingredients    │
│              │─1:N──│ ingredient_substitutions (original)
│              │─1:N──│ ingredient_substitutions (substitute)
│              │─1:N──│ shopping_list_items │
└──────────────┘      └────────────────────┘

meal_plans ──1:N──> meal_plan_items ──N:1──> dishes
shopping_lists ──1:N──> shopping_list_items ──N:1──> ingredients
```

---

## 3. QUY ƯỚC ĐẶT TÊN

### 3.1 Bảng & Cột

| Loại | Quy ước | Ví dụ |
|---|---|---|
| **Tên bảng** | `snake_case`, số nhiều | `dish_ingredients` |
| **Tên cột** | `snake_case` | `cooking_time_minutes` |
| **Primary Key** | `id` (ULID — VARCHAR 26) | `id` |
| **Foreign Key** | `{bảng_singular}_id` | `dish_id`, `user_id` |
| **Flag boolean** | `is_` + mô tả | `is_active`, `is_verified` |
| **Timestamp** | Luôn dùng `_at` suffix | `created_at`, `deleted_at` |

### 3.2 Constraint Naming

| Loại | Pattern | Ví dụ |
|---|---|---|
| **Index** | `idx_{table}_{col}` | `idx_dishes_status` |
| **Unique Key** | `uq_{table}_{col}` | `uq_user_email` |
| **Full-text** | `ft_{table}_{col}` | `ft_dishes_name` |
| **Foreign Key** | `fk_{table}_{ref_table}` | `fk_dishes_admin_users` |

### 3.3 ULID vs UUID

Sử dụng **ULID** (Universally Unique Lexicographically Sortable Identifier):

```
01HX3Z9N8KPQRST12345UVWXYZ
│└──────────────┘└─────────┘
│   Timestamp (48 bits)    Random (80 bits)
└── Sortable, 26 chars, B-tree friendly
```

**Lý do chọn ULID thay UUID v4:**
- Sortable theo thời gian → B-tree index hiệu quả hơn
- Không có dấu gạch ngang → VARCHAR(26) thay vì VARCHAR(36)
- Tạo ở application layer (NestJS) → không cần DB function

---

## 4. ENUM MASTER LIST

```sql
-- Diet Goals
('weight_loss','weight_gain','diabetes','eat_clean','vegetarian','healthy','low_carb','high_protein')

-- Meal Types
('breakfast','lunch','dinner')

-- Dish Status
('draft','pending_review','published','rejected')

-- Ingredient Categories
('meat','seafood','vegetable','fruit','grain','dairy','spice','sauce','other')

-- Allergen Codes
('shellfish','peanuts','gluten','dairy','eggs','soy','tree_nuts','fish')

-- Gender
('male','female','other')

-- Admin Roles
('super_admin','content_editor','nutritionist')

-- Platforms
('ios','android')

-- Difficulty
('easy','medium','hard')
```

---

## 5. SOFT DELETE STRATEGY

Các bảng hỗ trợ soft delete (có cột `deleted_at`):

| Bảng | Lý do |
|---|---|
| `users` | Dữ liệu liên quan (meal plans, favorites) cần giữ lại |
| `dishes` | Không muốn ảnh hưởng meal_plans đang active |
| `ingredients` | Nguyên liệu có thể được khôi phục |

**Query convention — luôn thêm WHERE clause:**
```sql
WHERE deleted_at IS NULL
```

**TypeORM config:**
```typescript
@Entity()
export class Dish {
  @DeleteDateColumn()
  deletedAt: Date;
}
// Tự động thêm WHERE deleted_at IS NULL khi query
```

---

**Trang tiếp theo:** [01 — User & Auth →](DDD-ND-2025-001_01_User_Auth.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
