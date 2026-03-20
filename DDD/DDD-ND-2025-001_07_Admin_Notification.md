# DDD-ND-2025-001_07 — Admin & Notification
# NutriDay Database Design

---

| | |
|---|---|
| **Module** | 07 — Admin & Notification |
| **Bảng** | `dish_reviews` · `audit_logs` · `notification_settings` · `device_tokens` |
| **Index** | [← Quay lại INDEX](DDD-ND-2025-001_INDEX.md) |

---

## Bảng 24: `dish_reviews`

**Mô tả:** Lịch sử duyệt/từ chối món ăn của nutritionist.

```sql
CREATE TABLE dish_reviews (
    id            VARCHAR(26) NOT NULL,
    dish_id       VARCHAR(26) NOT NULL,
    reviewer_id   VARCHAR(26) NOT NULL  COMMENT 'admin_users.id (nutritionist)',
    action        ENUM('approve','reject') NOT NULL,
    note          TEXT        NULL,
    status_before ENUM('draft','pending_review','published','rejected') NOT NULL,
    status_after  ENUM('draft','pending_review','published','rejected') NOT NULL,
    created_at    DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    INDEX idx_dr_dish (dish_id),
    CONSTRAINT fk_dr_dishes      FOREIGN KEY (dish_id)     REFERENCES dishes(id)       ON DELETE CASCADE,
    CONSTRAINT fk_dr_admin_users FOREIGN KEY (reviewer_id) REFERENCES admin_users(id)  ON DELETE RESTRICT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

## Bảng 25: `audit_logs`

**Mô tả:** Nhật ký toàn bộ hành động admin — phục vụ compliance và debug.

```sql
CREATE TABLE audit_logs (
    id          VARCHAR(26)  NOT NULL,
    actor_id    VARCHAR(26)  NOT NULL  COMMENT 'admin_users.id',
    actor_role  ENUM('super_admin','content_editor','nutritionist') NOT NULL,
    action      VARCHAR(100) NOT NULL  COMMENT 'dish.create | dish.approve | user.suspend...',
    entity_type VARCHAR(50)  NOT NULL  COMMENT 'dish | user | ingredient',
    entity_id   VARCHAR(26)  NOT NULL,
    old_value   JSON         NULL,
    new_value   JSON         NULL,
    ip_address  VARCHAR(45)  NULL,
    user_agent  VARCHAR(500) NULL,
    created_at  DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    INDEX idx_al_actor    (actor_id, created_at),
    INDEX idx_al_entity   (entity_type, entity_id),
    INDEX idx_al_created  (created_at),
    CONSTRAINT fk_al_admin_users FOREIGN KEY (actor_id) REFERENCES admin_users(id) ON DELETE RESTRICT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Action naming convention:** `{entity}.{verb}` — VD: `dish.create`, `dish.approve`, `dish.reject`, `user.suspend`, `import.upload`

---

## Bảng 26: `notification_settings`

```sql
CREATE TABLE notification_settings (
    id                     VARCHAR(26) NOT NULL,
    user_id                VARCHAR(26) NOT NULL,
    breakfast_reminder     TINYINT(1)  NOT NULL DEFAULT 1,
    breakfast_time         TIME        NOT NULL DEFAULT '07:00:00',
    lunch_reminder         TINYINT(1)  NOT NULL DEFAULT 1,
    lunch_time             TIME        NOT NULL DEFAULT '11:30:00',
    dinner_reminder        TINYINT(1)  NOT NULL DEFAULT 1,
    dinner_time            TIME        NOT NULL DEFAULT '18:00:00',
    weekly_plan_reminder   TINYINT(1)  NOT NULL DEFAULT 1,
    weekly_plan_day        ENUM('Monday','Tuesday','Wednesday','Thursday','Friday','Saturday','Sunday')
                           NOT NULL DEFAULT 'Sunday',
    weekly_plan_time       TIME        NOT NULL DEFAULT '20:00:00',
    shopping_list_reminder TINYINT(1)  NOT NULL DEFAULT 0,
    updated_at             DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_ns_user (user_id),
    CONSTRAINT fk_ns_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Auto-create:** Khi user hoàn thành onboarding, tự động INSERT với default values.

---

## Bảng 27: `device_tokens`

**Mô tả:** Expo push notification tokens của các thiết bị.

```sql
CREATE TABLE device_tokens (
    id         VARCHAR(26)  NOT NULL,
    user_id    VARCHAR(26)  NOT NULL,
    token      VARCHAR(500) NOT NULL   COMMENT 'ExponentPushToken[xxx]',
    platform   ENUM('ios','android')   NOT NULL,
    is_active  TINYINT(1)   NOT NULL DEFAULT 1,
    created_at DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_dt_token (token),
    INDEX idx_dt_user (user_id),
    CONSTRAINT fk_dt_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Upsert logic:** `INSERT INTO device_tokens ... ON DUPLICATE KEY UPDATE updated_at = NOW(), is_active = 1`

---

**Trang trước:** [06 — Favorites & History ←](DDD-ND-2025-001_06_Favorites_History.md)
**Trang tiếp theo:** [08 — Indexes & Strategy →](DDD-ND-2025-001_08_Indexes_Strategy.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
