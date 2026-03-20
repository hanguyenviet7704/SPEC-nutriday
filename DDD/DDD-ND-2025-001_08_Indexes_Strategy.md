# DDD-ND-2025-001_08 — Indexes & Strategy
# NutriDay Database Design

---

| | |
|---|---|
| **Module** | 08 — Indexes & Storage Strategy |
| **Index** | [← Quay lại INDEX](DDD-ND-2025-001_INDEX.md) |

---

## 1. INDEX DEFINITIONS

```sql
-- ─── users ─────────────────────────────────────
CREATE INDEX idx_users_is_active  ON users(is_active);
CREATE INDEX idx_users_created_at ON users(created_at);

-- ─── dishes ────────────────────────────────────
CREATE INDEX idx_dishes_status       ON dishes(status);
CREATE INDEX idx_dishes_cooking_time ON dishes(cooking_time_minutes);
CREATE INDEX idx_dishes_published_at ON dishes(published_at);
CREATE INDEX idx_dishes_view_count   ON dishes(view_count DESC);
-- FULLTEXT đã khai báo trong CREATE TABLE

-- ─── dish_diet_goals ───────────────────────────
CREATE INDEX idx_ddg_diet_goal ON dish_diet_goals(diet_goal);

-- ─── dish_meal_types ───────────────────────────
CREATE INDEX idx_dmt_meal_type ON dish_meal_types(meal_type);

-- ─── dish_allergens ────────────────────────────
CREATE INDEX idx_da_allergen ON dish_allergens(allergen_code);

-- ─── meal_plans ────────────────────────────────
CREATE INDEX idx_mp_user_date  ON meal_plans(user_id, plan_date);
CREATE INDEX idx_mp_plan_date  ON meal_plans(plan_date);

-- ─── meal_plan_items ───────────────────────────
CREATE INDEX idx_mpi_dish ON meal_plan_items(dish_id);

-- ─── shopping_list_items ───────────────────────
CREATE INDEX idx_sli_category   ON shopping_list_items(category);
CREATE INDEX idx_sli_is_checked ON shopping_list_items(is_checked);

-- ─── meal_history ──────────────────────────────
CREATE INDEX idx_mh_dish_date   ON meal_history(user_id, dish_id, served_date);

-- ─── favorites ─────────────────────────────────
-- idx_fav_user đã khai báo trong CREATE TABLE

-- ─── audit_logs ────────────────────────────────
-- Indexes đã khai báo trong CREATE TABLE

-- ─── refresh_tokens ────────────────────────────
CREATE INDEX idx_rt_expires ON refresh_tokens(expires_at);
CREATE INDEX idx_rt_user    ON refresh_tokens(user_id);
```

---

## 2. COMPOSITE INDEX STRATEGY

### 2.1 Meal Plan Generation Query

```sql
-- Query: tìm món theo dietGoal + mealType, loại allergen
-- Cần composite index:
CREATE INDEX idx_dishes_status_deleted ON dishes(status, deleted_at);

-- Covering index cho dish list query:
CREATE INDEX idx_ddg_goal_dish ON dish_diet_goals(diet_goal, dish_id);
CREATE INDEX idx_dmt_type_dish ON dish_meal_types(meal_type, dish_id);
```

### 2.2 Shopping List Query

```sql
-- Query: GROUP BY ingredient_id từ meal_plan_items → dish_ingredients
CREATE INDEX idx_di_dish_ingredient ON dish_ingredients(dish_id, ingredient_id);
```

---

## 3. PARTITIONING

### 3.1 `meal_plans` — Range partition by year-month

```sql
ALTER TABLE meal_plans PARTITION BY RANGE (
    YEAR(plan_date) * 100 + MONTH(plan_date)
) (
    PARTITION p_2025_04 VALUES LESS THAN (202505),
    PARTITION p_2025_05 VALUES LESS THAN (202506),
    PARTITION p_2025_06 VALUES LESS THAN (202507),
    PARTITION p_2025_07 VALUES LESS THAN (202508),
    PARTITION p_2025_08 VALUES LESS THAN (202509),
    PARTITION p_2025_09 VALUES LESS THAN (202510),
    PARTITION p_future   VALUES LESS THAN MAXVALUE
);
```

### 3.2 `meal_history` — Range partition by year-month

```sql
-- Tương tự meal_plans, thêm mỗi tháng qua cron job
ALTER TABLE meal_history REORGANIZE PARTITION p_future INTO (
    PARTITION p_2025_10 VALUES LESS THAN (202511),
    PARTITION p_future   VALUES LESS THAN MAXVALUE
);
```

### 3.3 `audit_logs` — Range partition by quarter

```sql
ALTER TABLE audit_logs PARTITION BY RANGE (
    YEAR(created_at) * 10 + QUARTER(created_at)
) (
    PARTITION p_2025_q2 VALUES LESS THAN (20253),
    PARTITION p_2025_q3 VALUES LESS THAN (20254),
    PARTITION p_2025_q4 VALUES LESS THAN (20261),
    PARTITION p_future   VALUES LESS THAN MAXVALUE
);
```

---

## 4. DATA RETENTION

| Bảng | Retention | Hành động |
|---|---|---|
| `otp_verifications` | 24 giờ | Cron job: DELETE WHERE created_at < NOW() - INTERVAL 24 HOUR |
| `password_reset_tokens` | 2 giờ | Cron job: DELETE WHERE expires_at < NOW() |
| `refresh_tokens` (revoked/expired) | 7 ngày | Cron job |
| `meal_history` | 12 tháng | Archive → S3 (Parquet) → DROP partition |
| `audit_logs` | 24 tháng | Archive → S3 → DROP partition |
| `meal_plans` (old) | 13 tháng | Giữ lại (user có thể xem lịch sử) |

---

## 5. MIGRATIONS

```typescript
// TypeORM migration naming: {timestamp}-{description}.ts
// VD: 1711900000000-CreateUsersTables.ts

// Chạy migrations:
// npm run migration:run
// npm run migration:revert  (rollback 1 migration)

// Migration template:
export class CreateUsersTables1711900000000 implements MigrationInterface {
    async up(queryRunner: QueryRunner): Promise<void> {
        await queryRunner.query(`CREATE TABLE users (...)`);
    }
    async down(queryRunner: QueryRunner): Promise<void> {
        await queryRunner.query(`DROP TABLE users`);
    }
}
```

**Migration order (Phase 1):**
1. `CreateUsersTables` — users, user_health_profiles, user_allergies
2. `CreateAdminTables` — admin_users, refresh_tokens, otp_verifications, password_reset_tokens
3. `CreateDishTables` — dishes, dish_images, dish_diet_goals, dish_meal_types, dish_allergens, dish_nutrition
4. `CreateIngredientTables` — ingredients, recipe_steps, dish_ingredients, ingredient_substitutions
5. `CreateMealPlanTables` — meal_plans, meal_plan_items
6. `CreateShoppingTables` — shopping_lists, shopping_list_items
7. `CreateActivityTables` — favorites, meal_history
8. `CreateNotificationTables` — notification_settings, device_tokens, dish_reviews, audit_logs
9. `CreateIndexes` — Tất cả indexes
10. `SeedInitialData` — Admin users + 300 ingredients

---

**Trang trước:** [07 — Admin & Notification ←](DDD-ND-2025-001_07_Admin_Notification.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
