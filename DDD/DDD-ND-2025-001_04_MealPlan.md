# DDD-ND-2025-001_04 — Meal Plan
# NutriDay Database Design

---

| | |
|---|---|
| **Module** | 04 — Meal Plan |
| **Bảng** | `meal_plans` · `meal_plan_items` |
| **Index** | [← Quay lại INDEX](DDD-ND-2025-001_INDEX.md) |

---

## Quan hệ

```
users ──1:N──► meal_plans ──1:N──► meal_plan_items ──N:1──► dishes
                                   meal_plan_items.original_dish_id ──N:1──► dishes
```

---

## Bảng 18: `meal_plans`

**Mô tả:** Thực đơn của một user cho một ngày cụ thể — mỗi user chỉ có 1 plan/ngày.

```sql
CREATE TABLE meal_plans (
    id                VARCHAR(26)  NOT NULL,
    user_id           VARCHAR(26)  NOT NULL,
    plan_date         DATE         NOT NULL   COMMENT 'Ngày áp dụng thực đơn',
    diet_goal         ENUM(
                          'weight_loss','weight_gain','diabetes',
                          'eat_clean','vegetarian','healthy',
                          'low_carb','high_protein'
                      ) NOT NULL              COMMENT 'dietGoal tại thời điểm generate',
    servings          TINYINT UNSIGNED NOT NULL DEFAULT 1,
    total_calories    DECIMAL(7,2) NULL       COMMENT 'Tổng calories 3 bữa (denormalized)',
    is_auto_generated TINYINT(1)   NOT NULL DEFAULT 1   COMMENT '0 = user tự tạo thủ công',
    created_at        DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at        DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_mp_user_date (user_id, plan_date),
    INDEX idx_mp_plan_date (plan_date),
    CONSTRAINT fk_mp_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Notes:**
- UNIQUE(user_id, plan_date) → enforce 1 plan/user/day
- `diet_goal` snapshot tại thời điểm generate (user có thể đổi goal sau)
- `total_calories` được cập nhật sau khi INSERT/UPDATE meal_plan_items
- Partition theo tháng để tối ưu query (xem module 08)

---

## Bảng 19: `meal_plan_items`

**Mô tả:** 3 dòng cho mỗi meal_plan (breakfast/lunch/dinner).

```sql
CREATE TABLE meal_plan_items (
    id               VARCHAR(26) NOT NULL,
    meal_plan_id     VARCHAR(26) NOT NULL,
    meal_type        ENUM('breakfast','lunch','dinner') NOT NULL,
    dish_id          VARCHAR(26) NOT NULL,
    was_swapped      TINYINT(1)  NOT NULL DEFAULT 0   COMMENT '1 = user đã đổi bữa này',
    original_dish_id VARCHAR(26) NULL                 COMMENT 'Món ban đầu trước khi swap',
    created_at       DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at       DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_mpi_plan_mealtype (meal_plan_id, meal_type),
    CONSTRAINT fk_mpi_meal_plans FOREIGN KEY (meal_plan_id) REFERENCES meal_plans(id) ON DELETE CASCADE,
    CONSTRAINT fk_mpi_dishes FOREIGN KEY (dish_id) REFERENCES dishes(id) ON DELETE RESTRICT,
    CONSTRAINT fk_mpi_original FOREIGN KEY (original_dish_id) REFERENCES dishes(id) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Swap flow:**
```sql
-- Khi user đổi bữa
UPDATE meal_plan_items
SET
    original_dish_id = dish_id,   -- Lưu món cũ
    dish_id          = :newDishId,
    was_swapped      = 1,
    updated_at       = NOW()
WHERE meal_plan_id = :planId AND meal_type = :mealType;
```

---

## Queries quan trọng

### Query 1: Lấy thực đơn hôm nay với đầy đủ thông tin

```sql
SELECT
    mp.id AS plan_id,
    mp.plan_date,
    mp.diet_goal,
    mp.total_calories,
    mpi.meal_type,
    mpi.was_swapped,
    d.id AS dish_id,
    d.name AS dish_name,
    d.main_image_url,
    d.cooking_time_minutes,
    dn.calories
FROM meal_plans mp
INNER JOIN meal_plan_items mpi ON mp.id = mpi.meal_plan_id
INNER JOIN dishes d ON mpi.dish_id = d.id
LEFT JOIN dish_nutrition dn ON d.id = dn.dish_id
WHERE mp.user_id = :userId
  AND mp.plan_date = CURDATE()
ORDER BY FIELD(mpi.meal_type, 'breakfast', 'lunch', 'dinner');
```

### Query 2: Lấy danh sách món đã ăn trong 7 ngày (để avoid repeat)

```sql
SELECT DISTINCT mpi.dish_id
FROM meal_plans mp
INNER JOIN meal_plan_items mpi ON mp.id = mpi.meal_plan_id
WHERE mp.user_id = :userId
  AND mp.plan_date >= DATE_SUB(CURDATE(), INTERVAL 7 DAY)
  AND mp.plan_date < CURDATE();
```

### Query 3: Generate — chọn món random theo dietGoal, tránh allergens và 7 ngày gần nhất

```sql
SELECT d.id, d.name, dmt.meal_type
FROM dishes d
INNER JOIN dish_diet_goals ddg ON d.id = ddg.dish_id
INNER JOIN dish_meal_types dmt ON d.id = dmt.dish_id
WHERE d.status = 'published'
  AND d.deleted_at IS NULL
  AND ddg.diet_goal = :dietGoal
  AND dmt.meal_type = :mealType
  AND d.id NOT IN (:recentDishIds)
  AND d.id NOT IN (
      SELECT da.dish_id FROM dish_allergens da
      INNER JOIN user_allergies ua ON da.allergen_code = ua.allergen_code
      WHERE ua.user_id = :userId
  )
ORDER BY RAND()
LIMIT 1;
```

---

**Trang trước:** [03 — Recipe & Ingredient ←](DDD-ND-2025-001_03_Recipe_Ingredient.md)
**Trang tiếp theo:** [05 — Shopping List →](DDD-ND-2025-001_05_ShoppingList.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
