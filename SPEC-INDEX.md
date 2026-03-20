# SPEC-INDEX — Bộ Tài Liệu Đặc Tả NutriDay
# Mục Lục Tổng Hợp Toàn Bộ Specification

---

| Thuộc tính | Giá trị |
|---|---|
| **Dự án** | NutriDay — Ứng Dụng Gợi Ý Thực Đơn Thông Minh Hằng Ngày |
| **Công ty** | CÔNG TY TNHH MEALTECH VIETNAM |
| **Phiên bản** | v1.0 DRAFT |
| **Ngày tạo** | 20/03/2025 |
| **Phân loại** | Nội bộ — Không phân phối bên ngoài |

---

## Cấu trúc tài liệu

```
nutriday/
├── SPEC-INDEX.md                          ← Bạn đang ở đây
│
├── BRD-ND-2025-001_NutriDay.md            ← Business Requirements
│
├── SAD/                                   ← System Architecture
│   ├── SAD-ND-2025-001_INDEX.md
│   ├── SAD-ND-2025-001_00_Overview.md
│   ├── SAD-ND-2025-001_01_Infrastructure.md
│   ├── SAD-ND-2025-001_02_Backend.md
│   ├── SAD-ND-2025-001_03_Frontend.md
│   ├── SAD-ND-2025-001_04_Security.md
│   └── SAD-ND-2025-001_05_DevOps.md
│
├── SDD/                                   ← Screen Design (10 modules)
│   ├── SDD-ND-2025-001_INDEX.md
│   ├── SDD-ND-2025-001_00_DesignSystem.md
│   ├── SDD-ND-2025-001_01_Onboarding_Authentication.md
│   ├── SDD-ND-2025-001_02_Home_DailyMenu.md
│   ├── SDD-ND-2025-001_03_DishDetail_Recipe.md
│   ├── SDD-ND-2025-001_04_WeeklyPlan.md
│   ├── SDD-ND-2025-001_05_ShoppingList.md
│   ├── SDD-ND-2025-001_06_Search_Explore.md
│   ├── SDD-ND-2025-001_07_Favorites_Profile.md
│   ├── SDD-ND-2025-001_08_AdminCMS.md
│   └── SDD-ND-2025-001_09_Settings_Notifications.md
│
├── APID/                                  ← API Design (12 modules)
│   ├── APID-ND-2025-001_INDEX.md
│   ├── APID-ND-2025-001_00_Overview.md
│   ├── APID-ND-2025-001_01_Auth.md
│   ├── APID-ND-2025-001_02_User.md
│   ├── APID-ND-2025-001_03_Dish.md
│   ├── APID-ND-2025-001_04_Ingredient.md
│   ├── APID-ND-2025-001_05_MealPlan.md
│   ├── APID-ND-2025-001_06_ShoppingList.md
│   ├── APID-ND-2025-001_07_Search.md
│   ├── APID-ND-2025-001_08_Favorites.md
│   ├── APID-ND-2025-001_09_Admin.md
│   ├── APID-ND-2025-001_10_Notification.md
│   └── APID-ND-2025-001_11_ErrorCodes.md
│
├── DDD/                                   ← Database Design (9 modules)
│   ├── DDD-ND-2025-001_INDEX.md
│   ├── DDD-ND-2025-001_00_Overview.md
│   ├── DDD-ND-2025-001_01_User_Auth.md
│   ├── DDD-ND-2025-001_02_Dish.md
│   ├── DDD-ND-2025-001_03_Recipe_Ingredient.md
│   ├── DDD-ND-2025-001_04_MealPlan.md
│   ├── DDD-ND-2025-001_05_ShoppingList.md
│   ├── DDD-ND-2025-001_06_Favorites_History.md
│   ├── DDD-ND-2025-001_07_Admin_Notification.md
│   └── DDD-ND-2025-001_08_Indexes_Strategy.md
│
├── FED/                                   ← Front End Design (10 modules)
│   ├── FED-ND-2025-001_INDEX.md
│   ├── FED-ND-2025-001_00_Architecture.md
│   ├── FED-ND-2025-001_01_DesignSystem.md
│   ├── FED-ND-2025-001_02_StateManagement.md
│   ├── FED-ND-2025-001_03_Navigation.md
│   ├── FED-ND-2025-001_04_Components_Atoms.md
│   ├── FED-ND-2025-001_05_Components_Molecules.md
│   ├── FED-ND-2025-001_06_Components_Organisms_Admin.md
│   ├── FED-ND-2025-001_07_Screens_Auth_Onboarding.md
│   ├── FED-ND-2025-001_08_Screens_Core.md
│   └── FED-ND-2025-001_09_NonFunctional.md
│
└── TSD/                                   ← Test Design (10 modules)
    ├── TSD-ND-2025-001_INDEX.md
    ├── TSD-ND-2025-001_00_Strategy.md
    ├── TSD-ND-2025-001_01_Unit_Backend.md
    ├── TSD-ND-2025-001_02_Unit_Frontend.md
    ├── TSD-ND-2025-001_03_Integration_API.md
    ├── TSD-ND-2025-001_04_E2E_Mobile.md
    ├── TSD-ND-2025-001_05_E2E_Admin.md
    ├── TSD-ND-2025-001_06_Performance.md
    ├── TSD-ND-2025-001_07_Security.md
    ├── TSD-ND-2025-001_08_TestCases.md
    └── TSD-ND-2025-001_09_TestData_Acceptance.md
```

---

## Danh sách tài liệu

| # | Mã tài liệu | Tên | Trạng thái |
|---|---|---|---|
| 1 | BRD-ND-2025-001 | Business Requirements Document | ✅ DRAFT |
| 2 | SAD-ND-2025-001 | System Architecture Document | ✅ DRAFT |
| 3 | SDD-ND-2025-001 | Screen Design Document | ✅ DRAFT |
| 4 | APID-ND-2025-001 | API Design Document | ✅ DRAFT |
| 5 | DDD-ND-2025-001 | Database Design Document | ✅ DRAFT |
| 6 | FED-ND-2025-001 | Front End Design Document | ✅ DRAFT |
| 7 | TSD-ND-2025-001 | Test Design Document | ✅ DRAFT |

---

## Mốc phê duyệt

| Tài liệu | BA Review | Dev Lead Review | PO Approve |
|---|---|---|---|
| BRD | 25/03/2025 | 28/03/2025 | 31/03/2025 |
| SAD | 25/03/2025 | 28/03/2025 | 31/03/2025 |
| SDD | 25/03/2025 | 28/03/2025 | 31/03/2025 |
| APID | 25/03/2025 | 28/03/2025 | 31/03/2025 |
| DDD | 25/03/2025 | 28/03/2025 | 31/03/2025 |
| FED | 25/03/2025 | 28/03/2025 | 31/03/2025 |
| TSD | 25/03/2025 | 28/03/2025 | 31/03/2025 |

---

*Phiên bản: v1.0 DRAFT — 20/03/2025*
