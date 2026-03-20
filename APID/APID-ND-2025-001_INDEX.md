# APID-ND-2025-001 — INDEX
# NutriDay API Design — Mục Lục Tài Liệu

---

| Thuộc tính | Giá trị |
|---|---|
| **Mã tài liệu** | APID-ND-2025-001 |
| **Phiên bản** | v1.0 DRAFT |
| **Ngày tạo** | 20/03/2025 |
| **Tham chiếu** | BRD-ND-2025-001 · SDD-ND-2025-001 · DDD-ND-2025-001 |

---

## Danh sách Module

| # | File | Module | Endpoints |
|---|---|---|---|
| 00 | [APID-ND-2025-001_00_Overview.md](APID-ND-2025-001_00_Overview.md) | Tổng quan & Quy ước chung | Base URL · Request/Response format · Pagination · Security |
| 01 | [APID-ND-2025-001_01_Auth.md](APID-ND-2025-001_01_Auth.md) | Authentication | POST register · verify-otp · resend-otp · login · refresh · logout · forgot-password · reset-password |
| 02 | [APID-ND-2025-001_02_User.md](APID-ND-2025-001_02_User.md) | User | GET/PATCH me · POST onboarding · PATCH diet-goal · POST avatar · GET meal-history |
| 03 | [APID-ND-2025-001_03_Dish.md](APID-ND-2025-001_03_Dish.md) | Dish | GET dishes · GET dishes/:id · GET substitutions |
| 04 | [APID-ND-2025-001_04_Ingredient.md](APID-ND-2025-001_04_Ingredient.md) | Ingredient | GET ingredients · POST search-dishes |
| 05 | [APID-ND-2025-001_05_MealPlan.md](APID-ND-2025-001_05_MealPlan.md) | Meal Plan | GET today · GET weekly · POST generate · GET swap-suggestions · PATCH swap · POST randomize |
| 06 | [APID-ND-2025-001_06_ShoppingList.md](APID-ND-2025-001_06_ShoppingList.md) | Shopping List | POST generate · GET list · PATCH item · PATCH check-all · GET export |
| 07 | [APID-ND-2025-001_07_Search.md](APID-ND-2025-001_07_Search.md) | Search | GET search/dishes · GET autocomplete · GET explore |
| 08 | [APID-ND-2025-001_08_Favorites.md](APID-ND-2025-001_08_Favorites.md) | Favorites | GET favorites · POST favorites · DELETE favorites/:dishId |
| 09 | [APID-ND-2025-001_09_Admin.md](APID-ND-2025-001_09_Admin.md) | Admin | Dashboard · Dish CRUD · Review · Import/Export · User Management · Reports |
| 10 | [APID-ND-2025-001_10_Notification.md](APID-ND-2025-001_10_Notification.md) | Notification | GET/PATCH settings · POST device-token |
| 11 | [APID-ND-2025-001_11_ErrorCodes.md](APID-ND-2025-001_11_ErrorCodes.md) | Error Codes | Danh sách mã lỗi toàn hệ thống |

---

## Base URLs

| Môi trường | URL |
|---|---|
| Development | `https://api-dev.nutriday.vn/v1` |
| Staging | `https://api-staging.nutriday.vn/v1` |
| Production | `https://api.nutriday.vn/v1` |

---

## Tóm tắt Endpoints

| Module | GET | POST | PATCH | PUT | DELETE | Tổng |
|---|---|---|---|---|---|---|
| Auth | — | 8 | — | — | — | 8 |
| User | 2 | 2 | 2 | — | — | 6 |
| Dish | 3 | — | — | — | — | 3 |
| Ingredient | 1 | 1 | — | — | — | 2 |
| Meal Plan | 3 | 2 | 1 | — | — | 6 |
| Shopping List | 2 | 1 | 2 | — | — | 5 |
| Search | 3 | — | — | — | — | 3 |
| Favorites | 1 | 1 | — | — | 1 | 3 |
| Admin | 7 | 4 | 2 | 1 | 1 | 15 |
| Notification | 1 | 1 | 1 | — | — | 3 |
| **Total** | **23** | **20** | **8** | **1** | **2** | **54** |

---

*Phiên bản: v1.0 DRAFT — 20/03/2025*
