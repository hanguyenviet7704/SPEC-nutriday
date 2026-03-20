# DDD-ND-2025-001 — INDEX
# NutriDay Database Design — Mục Lục

---

| Thuộc tính | Giá trị |
|---|---|
| **Mã tài liệu** | DDD-ND-2025-001 |
| **Phiên bản** | v1.0 DRAFT |
| **Ngày tạo** | 20/03/2025 |
| **RDBMS** | MySQL 8.0+ (AWS RDS) |
| **Tham chiếu** | BRD-ND-2025-001 · APID-ND-2025-001 |

---

## Danh sách Module

| # | File | Module | Bảng |
|---|---|---|---|
| 00 | [DDD-ND-2025-001_00_Overview.md](DDD-ND-2025-001_00_Overview.md) | Tổng quan & Quy ước | ERD tổng quát · naming convention · MySQL config |
| 01 | [DDD-ND-2025-001_01_User_Auth.md](DDD-ND-2025-001_01_User_Auth.md) | User & Authentication | `users` · `user_health_profiles` · `user_allergies` · `admin_users` · `refresh_tokens` · `otp_verifications` · `password_reset_tokens` |
| 02 | [DDD-ND-2025-001_02_Dish.md](DDD-ND-2025-001_02_Dish.md) | Dish Catalog | `dishes` · `dish_images` · `dish_diet_goals` · `dish_meal_types` · `dish_allergens` · `dish_nutrition` |
| 03 | [DDD-ND-2025-001_03_Recipe_Ingredient.md](DDD-ND-2025-001_03_Recipe_Ingredient.md) | Recipe & Ingredient | `recipe_steps` · `ingredients` · `dish_ingredients` · `ingredient_substitutions` |
| 04 | [DDD-ND-2025-001_04_MealPlan.md](DDD-ND-2025-001_04_MealPlan.md) | Meal Plan | `meal_plans` · `meal_plan_items` |
| 05 | [DDD-ND-2025-001_05_ShoppingList.md](DDD-ND-2025-001_05_ShoppingList.md) | Shopping List | `shopping_lists` · `shopping_list_items` |
| 06 | [DDD-ND-2025-001_06_Favorites_History.md](DDD-ND-2025-001_06_Favorites_History.md) | Favorites & History | `favorites` · `meal_history` |
| 07 | [DDD-ND-2025-001_07_Admin_Notification.md](DDD-ND-2025-001_07_Admin_Notification.md) | Admin & Notification | `dish_reviews` · `audit_logs` · `notification_settings` · `device_tokens` |
| 08 | [DDD-ND-2025-001_08_Indexes_Strategy.md](DDD-ND-2025-001_08_Indexes_Strategy.md) | Indexes & Strategy | Index definitions · Partitioning · Retention · Migrations |

---

## Tổng hợp bảng (27 bảng)

| # | Tên bảng | Nhóm | Rows ước tính (12 tháng) |
|---|---|---|---|
| 1 | `users` | Auth | 50,000 |
| 2 | `user_health_profiles` | Auth | 50,000 |
| 3 | `user_allergies` | Auth | 80,000 |
| 4 | `admin_users` | Auth | 20 |
| 5 | `refresh_tokens` | Auth | 200,000 |
| 6 | `otp_verifications` | Auth | Xóa tự động |
| 7 | `password_reset_tokens` | Auth | Xóa tự động |
| 8 | `dishes` | Dish | 500 |
| 9 | `dish_images` | Dish | 1,500 |
| 10 | `dish_diet_goals` | Dish | 2,000 |
| 11 | `dish_meal_types` | Dish | 1,000 |
| 12 | `dish_allergens` | Dish | 1,500 |
| 13 | `dish_nutrition` | Dish | 500 |
| 14 | `recipe_steps` | Recipe | 3,000 |
| 15 | `ingredients` | Ingredient | 300 |
| 16 | `dish_ingredients` | Ingredient | 5,000 |
| 17 | `ingredient_substitutions` | Ingredient | 2,000 |
| 18 | `meal_plans` | Meal Plan | 18,250,000 |
| 19 | `meal_plan_items` | Meal Plan | 54,750,000 |
| 20 | `shopping_lists` | Shopping | 5,475,000 |
| 21 | `shopping_list_items` | Shopping | 65,700,000 |
| 22 | `favorites` | User Activity | 500,000 |
| 23 | `meal_history` | User Activity | 54,750,000 |
| 24 | `dish_reviews` | Admin | 2,000 |
| 25 | `audit_logs` | Admin | 100,000 |
| 26 | `notification_settings` | Notification | 50,000 |
| 27 | `device_tokens` | Notification | 75,000 |

---

*Phiên bản: v1.0 DRAFT — 20/03/2025*
