# DDD-ND-2025-001_03 — Recipe & Ingredient
# NutriDay Database Design

---

| | |
|---|---|
| **Module** | 03 — Recipe & Ingredient |
| **Bảng** | `recipe_steps` · `ingredients` · `dish_ingredients` · `ingredient_substitutions` |
| **Index** | [← Quay lại INDEX](DDD-ND-2025-001_INDEX.md) |

---

## Quan hệ

```
dishes ──1:N──► recipe_steps
dishes ──1:N──► dish_ingredients ──N:1──► ingredients
ingredients ──1:N──► ingredient_substitutions (original_ingredient_id)
ingredients ──1:N──► ingredient_substitutions (substitute_id)
dish_ingredients ──N:1──► dishes
```

---

## Bảng 14: `recipe_steps`

**Mô tả:** Các bước nấu ăn theo thứ tự — dùng cho màn hình Recipe (SCR-013).

```sql
CREATE TABLE recipe_steps (
    id           VARCHAR(26)       NOT NULL,
    dish_id      VARCHAR(26)       NOT NULL,
    step_number  TINYINT UNSIGNED  NOT NULL            COMMENT 'Bắt đầu từ 1',
    instruction  TEXT              NOT NULL,
    duration_sec SMALLINT UNSIGNED NULL                COMMENT 'Thời gian bước (giây), NULL = không timer',
    image_url    VARCHAR(500)      NULL                COMMENT 'Ảnh minh họa bước (optional)',
    PRIMARY KEY (id),
    UNIQUE KEY uq_rs_dish_step (dish_id, step_number),
    CONSTRAINT fk_rs_dishes FOREIGN KEY (dish_id) REFERENCES dishes(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Notes:**
- `duration_sec` → hiển thị countdown timer trên SCR-013
- Thứ tự bước đảm bảo bởi UNIQUE(dish_id, step_number)
- Khi import Excel: parse text "2 phút" → 120 giây

---

## Bảng 15: `ingredients`

**Mô tả:** Danh mục nguyên liệu dùng chung — không phụ thuộc vào dish cụ thể.

```sql
CREATE TABLE ingredients (
    id           VARCHAR(26)  NOT NULL,
    name         VARCHAR(200) NOT NULL,
    name_search  VARCHAR(200) NOT NULL COMMENT 'Lowercase không dấu — full-text search',
    category     ENUM(
                     'meat','seafood','vegetable','fruit',
                     'grain','dairy','spice','sauce','other'
                 ) NOT NULL DEFAULT 'other',
    default_unit VARCHAR(20)  NOT NULL  COMMENT 'g, ml, quả, củ, lá, thìa, hộp, gói...',
    image_url    VARCHAR(500) NULL,
    is_allergen  TINYINT(1)   NOT NULL DEFAULT 0   COMMENT '1 = bản thân nguyên liệu là allergen',
    created_at   DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at   DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at   DATETIME     NULL,
    PRIMARY KEY (id),
    FULLTEXT KEY ft_ingredients_name (name_search)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**`name_search` generation:**
```typescript
// Trong application layer (NestJS)
import { removeVietnameseTones } from './utils';
ingredient.nameSearch = removeVietnameseTones(ingredient.name).toLowerCase();
// VD: "Thịt Bò" → "thit bo"
```

**Ví dụ data:**
| name | category | default_unit |
|---|---|---|
| Thịt bò | meat | g |
| Cá hồi | seafood | g |
| Hành tây | vegetable | củ |
| Bánh phở | grain | g |
| Nước mắm | sauce | thìa |

---

## Bảng 16: `dish_ingredients`

**Mô tả:** Nguyên liệu và số lượng của từng món ăn — junction table với thêm data.

```sql
CREATE TABLE dish_ingredients (
    id            VARCHAR(26)  NOT NULL,
    dish_id       VARCHAR(26)  NOT NULL,
    ingredient_id VARCHAR(26)  NOT NULL,
    quantity      DECIMAL(8,2) NOT NULL          COMMENT 'Số lượng cho 1 khẩu phần chuẩn',
    unit          VARCHAR(20)  NOT NULL          COMMENT 'Đơn vị (có thể khác default_unit của ingredient)',
    note          VARCHAR(200) NULL              COMMENT 'VD: "thái mỏng", "phi vàng", "bỏ hạt"',
    sort_order    TINYINT UNSIGNED NOT NULL DEFAULT 0,
    PRIMARY KEY (id),
    UNIQUE KEY uq_di_dish_ingredient (dish_id, ingredient_id),
    CONSTRAINT fk_di_dishes FOREIGN KEY (dish_id) REFERENCES dishes(id) ON DELETE CASCADE,
    CONSTRAINT fk_di_ingredients FOREIGN KEY (ingredient_id) REFERENCES ingredients(id) ON DELETE RESTRICT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Query tạo shopping list từ meal plan:**
```sql
SELECT
    di.ingredient_id,
    i.name,
    i.category,
    SUM(di.quantity * :servings) AS total_quantity,
    di.unit
FROM meal_plan_items mpi
INNER JOIN dish_ingredients di ON mpi.dish_id = di.dish_id
INNER JOIN ingredients i ON di.ingredient_id = i.id
WHERE mpi.meal_plan_id IN (:planIds)
GROUP BY di.ingredient_id, i.name, i.category, di.unit
ORDER BY i.category, i.name
```

---

## Bảng 17: `ingredient_substitutions`

**Mô tả:** Gợi ý thay thế nguyên liệu trong context của một món cụ thể.

```sql
CREATE TABLE ingredient_substitutions (
    id                     VARCHAR(26)  NOT NULL,
    dish_id                VARCHAR(26)  NOT NULL,
    original_ingredient_id VARCHAR(26)  NOT NULL,
    substitute_id          VARCHAR(26)  NOT NULL,
    quantity               DECIMAL(8,2) NULL  COMMENT 'NULL = giữ nguyên số lượng bản gốc',
    unit                   VARCHAR(20)  NULL  COMMENT 'NULL = giữ nguyên đơn vị bản gốc',
    note                   VARCHAR(500) NULL  COMMENT 'VD: "Thích hợp cho chế độ eat clean"',
    nutrition_impact       VARCHAR(200) NULL  COMMENT 'VD: "Ít calories hơn 15%"',
    created_at             DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_is_dish_original_sub (dish_id, original_ingredient_id, substitute_id),
    CONSTRAINT fk_is_dishes FOREIGN KEY (dish_id) REFERENCES dishes(id) ON DELETE CASCADE,
    CONSTRAINT fk_is_original FOREIGN KEY (original_ingredient_id) REFERENCES ingredients(id) ON DELETE CASCADE,
    CONSTRAINT fk_is_substitute FOREIGN KEY (substitute_id) REFERENCES ingredients(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Notes:**
- Substitution là context-specific: nguyên liệu A thay cho B trong món X có thể khác trong món Y
- `quantity = NULL` nghĩa là dùng cùng số lượng với nguyên liệu gốc

---

**Trang trước:** [02 — Dish ←](DDD-ND-2025-001_02_Dish.md)
**Trang tiếp theo:** [04 — Meal Plan →](DDD-ND-2025-001_04_MealPlan.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
