# DDD-ND-2025-001_06 — Favorites & Meal History
# NutriDay Database Design

---

| | |
|---|---|
| **Module** | 06 — Favorites & History |
| **Bảng** | `favorites` · `meal_history` |
| **Index** | [← Quay lại INDEX](DDD-ND-2025-001_INDEX.md) |

---

## Bảng 22: `favorites`

```sql
CREATE TABLE favorites (
    id         VARCHAR(26) NOT NULL,
    user_id    VARCHAR(26) NOT NULL,
    dish_id    VARCHAR(26) NOT NULL,
    created_at DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_fav_user_dish (user_id, dish_id),
    INDEX idx_fav_user (user_id),
    CONSTRAINT fk_fav_users  FOREIGN KEY (user_id) REFERENCES users(id)  ON DELETE CASCADE,
    CONSTRAINT fk_fav_dishes FOREIGN KEY (dish_id) REFERENCES dishes(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Trigger cập nhật `dish.favorite_count`:**
```sql
-- Sau INSERT favorites
UPDATE dishes SET favorite_count = favorite_count + 1 WHERE id = NEW.dish_id;

-- Sau DELETE favorites
UPDATE dishes SET favorite_count = GREATEST(favorite_count - 1, 0) WHERE id = OLD.dish_id;
```

---

## Bảng 23: `meal_history`

**Mô tả:** Lịch sử bữa ăn — tự động ghi khi meal_plan được confirm/viewed. Dùng để:
1. Hiển thị lịch sử trên SCR-024
2. Tránh repeat món trong 7 ngày (query `meal_history`)

```sql
CREATE TABLE meal_history (
    id          VARCHAR(26) NOT NULL,
    user_id     VARCHAR(26) NOT NULL,
    dish_id     VARCHAR(26) NOT NULL,
    meal_type   ENUM('breakfast','lunch','dinner') NOT NULL,
    served_date DATE        NOT NULL  COMMENT 'Ngày ăn bữa này',
    diet_goal   ENUM(
                    'weight_loss','weight_gain','diabetes',
                    'eat_clean','vegetarian','healthy',
                    'low_carb','high_protein'
                ) NOT NULL             COMMENT 'dietGoal tại thời điểm ăn',
    created_at  DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    INDEX idx_mh_user_date (user_id, served_date),
    CONSTRAINT fk_mh_users  FOREIGN KEY (user_id) REFERENCES users(id)  ON DELETE CASCADE,
    CONSTRAINT fk_mh_dishes FOREIGN KEY (dish_id) REFERENCES dishes(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Ghi vào meal_history:** Khi user mở app vào ngày đó (auto-mark as served), hoặc khi ngày kết thúc (cron job nightly).

**Partition:** Theo tháng để tối ưu query (xem module 08).

---

**Trang trước:** [05 — Shopping List ←](DDD-ND-2025-001_05_ShoppingList.md)
**Trang tiếp theo:** [07 — Admin & Notification →](DDD-ND-2025-001_07_Admin_Notification.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
