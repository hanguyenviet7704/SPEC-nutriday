# DDD-ND-2025-001_01 — User & Authentication
# NutriDay Database Design

---

| | |
|---|---|
| **Module** | 01 — User & Auth |
| **Bảng** | `users` · `user_health_profiles` · `user_allergies` · `admin_users` · `refresh_tokens` · `otp_verifications` · `password_reset_tokens` |
| **Index** | [← Quay lại INDEX](DDD-ND-2025-001_INDEX.md) |

---

## Quan hệ trong module

```
users ──1:1──► user_health_profiles
users ──1:N──► user_allergies
users ──1:N──► refresh_tokens
users ──1:N──► otp_verifications
users ──1:N──► password_reset_tokens
admin_users ──1:N──► refresh_tokens (admin_id)
```

---

## Bảng 1: `users`

**Mô tả:** Tài khoản người dùng mobile app.

```sql
CREATE TABLE users (
    id            VARCHAR(26)  NOT NULL                        COMMENT 'ULID',
    full_name     VARCHAR(100) NOT NULL,
    email         VARCHAR(255) NOT NULL,
    phone         VARCHAR(15)  NOT NULL,
    password_hash VARCHAR(255) NOT NULL                        COMMENT 'bcrypt hash, cost=12',
    avatar_url    VARCHAR(500) NULL,
    is_verified   TINYINT(1)   NOT NULL DEFAULT 0              COMMENT '0=chưa xác thực OTP, 1=đã xác thực',
    is_active     TINYINT(1)   NOT NULL DEFAULT 1              COMMENT '0=suspended, 1=active',
    last_login_at DATETIME     NULL,
    created_at    DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at    DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at    DATETIME     NULL                            COMMENT 'Soft delete',
    PRIMARY KEY (id),
    UNIQUE KEY uq_users_email (email),
    UNIQUE KEY uq_users_phone (phone)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

| Cột | Type | Null | Default | Ghi chú |
|---|---|---|---|---|
| `id` | VARCHAR(26) | NO | — | ULID, PK |
| `full_name` | VARCHAR(100) | NO | — | Họ và tên đầy đủ |
| `email` | VARCHAR(255) | NO | — | UNIQUE |
| `phone` | VARCHAR(15) | NO | — | UNIQUE, 10 số VN |
| `password_hash` | VARCHAR(255) | NO | — | bcrypt, cost factor 12 |
| `avatar_url` | VARCHAR(500) | YES | NULL | CDN URL |
| `is_verified` | TINYINT(1) | NO | 0 | OTP verification status |
| `is_active` | TINYINT(1) | NO | 1 | Account status |
| `last_login_at` | DATETIME | YES | NULL | UTC |
| `deleted_at` | DATETIME | YES | NULL | Soft delete marker |

---

## Bảng 2: `user_health_profiles`

**Mô tả:** Hồ sơ sức khỏe và mục tiêu dinh dưỡng — tách riêng để mở rộng dễ dàng.

```sql
CREATE TABLE user_health_profiles (
    id                   VARCHAR(26) NOT NULL,
    user_id              VARCHAR(26) NOT NULL,
    date_of_birth        DATE        NULL,
    gender               ENUM('male','female','other') NULL,
    height_cm            DECIMAL(5,1) NULL                     COMMENT 'Chiều cao (cm), VD: 170.0',
    weight_kg            DECIMAL(5,1) NULL                     COMMENT 'Cân nặng (kg), VD: 65.0',
    diet_goal            ENUM(
                             'weight_loss','weight_gain','diabetes',
                             'eat_clean','vegetarian','healthy',
                             'low_carb','high_protein'
                         ) NULL,
    servings             TINYINT UNSIGNED NOT NULL DEFAULT 1   COMMENT 'Số người ăn (1-10)',
    onboarding_completed TINYINT(1) NOT NULL DEFAULT 0,
    created_at           DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at           DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_uhp_user (user_id),
    CONSTRAINT fk_uhp_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Notes:**
- `servings` ảnh hưởng trực tiếp đến `shopping_list_items.quantity` (nhân theo servings)
- `diet_goal` = NULL khi onboarding chưa hoàn thành
- `onboarding_completed = 1` mới được dùng full app

---

## Bảng 3: `user_allergies`

**Mô tả:** Danh sách dị ứng — dùng để loại món trong meal plan generation.

```sql
CREATE TABLE user_allergies (
    id            VARCHAR(26) NOT NULL,
    user_id       VARCHAR(26) NOT NULL,
    allergen_code VARCHAR(50) NOT NULL COMMENT 'shellfish|peanuts|gluten|dairy|eggs|soy|tree_nuts|fish',
    created_at    DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_ua_user_allergen (user_id, allergen_code),
    CONSTRAINT fk_ua_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Allergen codes:**
| Code | Tên tiếng Việt |
|---|---|
| `shellfish` | Hải sản có vỏ |
| `peanuts` | Đậu phộng |
| `gluten` | Gluten (lúa mì) |
| `dairy` | Sữa & chế phẩm |
| `eggs` | Trứng |
| `soy` | Đậu nành |
| `tree_nuts` | Hạt cây (hạnh nhân, óc chó...) |
| `fish` | Cá |

---

## Bảng 4: `admin_users`

**Mô tả:** Tài khoản quản trị (tách hoàn toàn khỏi `users`).

```sql
CREATE TABLE admin_users (
    id            VARCHAR(26) NOT NULL,
    full_name     VARCHAR(100) NOT NULL,
    email         VARCHAR(255) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role          ENUM('super_admin','content_editor','nutritionist') NOT NULL,
    is_active     TINYINT(1)   NOT NULL DEFAULT 1,
    last_login_at DATETIME     NULL,
    created_at    DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at    DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_admin_email (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Phân quyền:**
| Role | Tạo dish | Sửa dish | Xóa dish | Duyệt dish | Xem báo cáo | Quản lý users |
|---|---|---|---|---|---|---|
| `super_admin` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `content_editor` | ✅ | ✅ (draft/rejected) | ❌ | ❌ | ❌ | ❌ |
| `nutritionist` | ❌ | ❌ | ❌ | ✅ | ❌ | ❌ |

---

## Bảng 5: `refresh_tokens`

**Mô tả:** JWT refresh tokens — lưu hash để blacklist khi logout.

```sql
CREATE TABLE refresh_tokens (
    id           VARCHAR(26) NOT NULL,
    user_id      VARCHAR(26) NULL                              COMMENT 'NULL nếu là admin token',
    admin_id     VARCHAR(26) NULL                              COMMENT 'NULL nếu là user token',
    token_hash   VARCHAR(255) NOT NULL                         COMMENT 'SHA-256 hash của token',
    expires_at   DATETIME    NOT NULL,
    revoked_at   DATETIME    NULL                              COMMENT 'NULL = còn hiệu lực',
    user_agent   VARCHAR(500) NULL,
    ip_address   VARCHAR(45)  NULL                             COMMENT 'IPv4 hoặc IPv6',
    created_at   DATETIME    NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_rt_token_hash (token_hash),
    CONSTRAINT fk_rt_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    CONSTRAINT fk_rt_admin_users FOREIGN KEY (admin_id) REFERENCES admin_users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Lưu ý:**
- Xóa token đã revoke sau 7 ngày (cron job)
- Xóa token đã expire sau 30 ngày (cron job)

---

## Bảng 6: `otp_verifications`

**Mô tả:** OTP cho đăng ký và quên mật khẩu.

```sql
CREATE TABLE otp_verifications (
    id            VARCHAR(26)  NOT NULL,
    user_id       VARCHAR(26)  NOT NULL,
    otp_code      VARCHAR(6)   NOT NULL,
    purpose       ENUM('register','forgot_password') NOT NULL DEFAULT 'register',
    attempt_count TINYINT      NOT NULL DEFAULT 0             COMMENT 'Max 5 attempts',
    expires_at    DATETIME     NOT NULL                       COMMENT 'Hết hạn sau 5 phút',
    verified_at   DATETIME     NULL,
    created_at    DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    CONSTRAINT fk_otp_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Business rules:**
- `attempt_count ≥ 5` → trả về lỗi RATE_LIMIT
- `expires_at < NOW()` → trả về lỗi AUTH_005
- Xóa tự động sau 24h (cron job)

---

## Bảng 7: `password_reset_tokens`

**Mô tả:** Token đặt lại mật khẩu (gửi qua email/SMS).

```sql
CREATE TABLE password_reset_tokens (
    id           VARCHAR(26)  NOT NULL,
    user_id      VARCHAR(26)  NOT NULL,
    token_hash   VARCHAR(255) NOT NULL COMMENT 'SHA-256 của token gửi cho user',
    expires_at   DATETIME     NOT NULL COMMENT 'Hết hạn sau 1 giờ',
    used_at      DATETIME     NULL     COMMENT 'Đã dùng — không dùng lại được',
    created_at   DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id),
    UNIQUE KEY uq_prt_token_hash (token_hash),
    CONSTRAINT fk_prt_users FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

---

**Trang trước:** [00 — Overview ←](DDD-ND-2025-001_00_Overview.md)
**Trang tiếp theo:** [02 — Dish Catalog →](DDD-ND-2025-001_02_Dish.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
