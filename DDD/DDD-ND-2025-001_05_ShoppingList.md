# DDD-ND-2025-001_05 — Shopping List
# NutriDay Database Design

---

| | |
|---|---|
| **Module** | 05 — Shopping List |
| **Bảng** | `shopping_lists` · `shopping_list_items` |
| **Index** | [← Quay lại INDEX](DDD-ND-2025-001_INDEX.md) |

---

## Quan hệ

```
users ──1:N──► shopping_lists ──1:N──► shopping_list_items ──N:1──► ingredients
```

---

## Bảng 20: `shopping_lists`

```sql
CREATE TABLE shopping_lists (
    id             VARCHAR(26) NOT NULL,
    user_id        VARCHAR(26) NOT NULL,
    list_type      ENUM('daily','weekly') NOT NULL DEFAULT 'daily',
    reference_date DATE        NOT NULL     COMMENT 'Ngày (daily) hoặc ngày bắt đầu tuần (weekly)',
    total_items    SMALLINT UNSIGNED NOT NULL DEFAULT 0,
    checked_items  SMALLINT UNSIGNED NOT NULL DEFAULT 0,
    estimated_cost DECIMAL(10,2) NULL       COMMENT 'VNĐ — ước tính',
    created_at     DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at     DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    INDEX idx_sl_user_date (user_id, reference_date),
    CONSTRAINT fk_sl_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## Bảng 21: `shopping_list_items`

```sql
CREATE TABLE shopping_list_items (
    id               VARCHAR(26)  NOT NULL,
    shopping_list_id VARCHAR(26)  NOT NULL,
    ingredient_id    VARCHAR(26)  NOT NULL,
    name             VARCHAR(200) NOT NULL  COMMENT 'Snapshot tên tại thời điểm tạo list',
    quantity         DECIMAL(8,2) NOT NULL  COMMENT 'Đã nhân servings + gộp trùng',
    unit             VARCHAR(20)  NOT NULL,
    category         ENUM(
                         'meat','seafood','vegetable','fruit',
                         'grain','dairy','spice','sauce','other'
                     ) NOT NULL DEFAULT 'other',
    is_checked       TINYINT(1)   NOT NULL DEFAULT 0,
    estimated_price  DECIMAL(10,2) NULL,
    checked_at       DATETIME     NULL,
    created_at       DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    INDEX idx_sli_list (shopping_list_id),
    CONSTRAINT fk_sli_lists FOREIGN KEY (shopping_list_id) REFERENCES shopping_lists(id) ON DELETE CASCADE,
    CONSTRAINT fk_sli_ingredients FOREIGN KEY (ingredient_id) REFERENCES ingredients(id) ON DELETE RESTRICT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Notes:**
- `name` là snapshot — không JOIN lại ingredients khi đọc lại (ingredients có thể đổi tên)
- Khi `is_checked` thay đổi: trigger cập nhật `shopping_lists.checked_items`

---

## Query: Generate shopping list từ meal_plans

```sql
-- Bước 1: Gộp nguyên liệu từ các món trong ngày (nhân servings)
SELECT
    di.ingredient_id,
    i.name,
    i.category,
    SUM(di.quantity * mp.servings) AS total_quantity,
    di.unit
FROM meal_plans mp
INNER JOIN meal_plan_items mpi ON mp.id = mpi.meal_plan_id
INNER JOIN dish_ingredients di  ON mpi.dish_id = di.dish_id
INNER JOIN ingredients i        ON di.ingredient_id = i.id
WHERE mp.user_id = :userId
  AND mp.plan_date = :date
GROUP BY di.ingredient_id, i.name, i.category, di.unit
ORDER BY FIELD(i.category, 'meat','seafood','vegetable','fruit','grain','dairy','spice','sauce','other'),
         i.name;

-- Bước 2: INSERT vào shopping_list_items (từ application layer)
```

---

**Trang trước:** [04 — Meal Plan ←](DDD-ND-2025-001_04_MealPlan.md)
**Trang tiếp theo:** [06 — Favorites & History →](DDD-ND-2025-001_06_Favorites_History.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
