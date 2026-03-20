# TSD-ND-2025-001 — INDEX
# NutriDay Test Design — Mục Lục

---

| Thuộc tính | Giá trị |
|---|---|
| **Mã tài liệu** | TSD-ND-2025-001 |
| **Phiên bản** | v1.0 DRAFT |
| **Ngày tạo** | 20/03/2025 |
| **Tham chiếu** | BRD-ND-2025-001 · APID-ND-2025-001 · DDD-ND-2025-001 |

---

## Danh sách Module

| # | File | Module | Nội dung |
|---|---|---|---|
| 00 | [TSD-ND-2025-001_00_Strategy.md](TSD-ND-2025-001_00_Strategy.md) | Chiến lược & Môi trường | Test pyramid · Tools · Environments · Device matrix · Quality gates |
| 01 | [TSD-ND-2025-001_01_Unit_Backend.md](TSD-ND-2025-001_01_Unit_Backend.md) | Unit Tests — Backend | AuthService · UserService · DishService · MealPlanService · ShoppingListService |
| 02 | [TSD-ND-2025-001_02_Unit_Frontend.md](TSD-ND-2025-001_02_Unit_Frontend.md) | Unit Tests — Frontend | Component tests · Custom hooks · Zustand stores · Form validation |
| 03 | [TSD-ND-2025-001_03_Integration_API.md](TSD-ND-2025-001_03_Integration_API.md) | Integration Tests — API | Supertest · Auth · User · Dish · MealPlan · ShoppingList · Admin |
| 04 | [TSD-ND-2025-001_04_E2E_Mobile.md](TSD-ND-2025-001_04_E2E_Mobile.md) | E2E Tests — Mobile (Detox) | Đăng ký · Login · Thực đơn · Đổi bữa · Nấu ăn · Shopping · Search |
| 05 | [TSD-ND-2025-001_05_E2E_Admin.md](TSD-ND-2025-001_05_E2E_Admin.md) | E2E Tests — Admin (Playwright) | Tạo món · Duyệt · Import · Export · Báo cáo |
| 06 | [TSD-ND-2025-001_06_Performance.md](TSD-ND-2025-001_06_Performance.md) | Performance Tests (k6) | Load test · Stress test · Spike test · Soak test · Targets |
| 07 | [TSD-ND-2025-001_07_Security.md](TSD-ND-2025-001_07_Security.md) | Security Tests | Auth/AuthZ · Input validation · OWASP Top 10 · API security |
| 08 | [TSD-ND-2025-001_08_TestCases.md](TSD-ND-2025-001_08_TestCases.md) | Test Cases Chi Tiết | TC-AUTH · TC-USER · TC-DISH · TC-PLAN · TC-SHOP · TC-FAV · TC-ADM |
| 09 | [TSD-ND-2025-001_09_TestData_Acceptance.md](TSD-ND-2025-001_09_TestData_Acceptance.md) | Test Data & Acceptance | Seed data · DoD · Bug report template · Defect SLA |

---

## Tổng hợp số lượng test

| Loại | Tool | Số lượng (ước tính) | Coverage Target |
|---|---|---|---|
| Unit — Backend | Jest (NestJS) | ~120 test cases | ≥ 80% |
| Unit — Frontend | Jest + RNTL | ~80 test cases | ≥ 70% |
| Integration — API | Supertest | ~90 test cases | 100% endpoints |
| E2E — Mobile | Detox | ~15 flows | Happy path + Error |
| E2E — Admin | Playwright | ~10 flows | Happy path + Error |
| Performance | k6 | 4 scenarios | p95 ≤ 300ms |
| Security | OWASP ZAP + Manual | ~30 test cases | OWASP Top 10 |
| **Total** | | **~345** | |

---

## Quality Gates — MVP Release

| Gate | Tiêu chí |
|---|---|
| **P0 test cases** | 100% pass |
| **Backend coverage** | ≥ 80% |
| **Frontend coverage** | ≥ 70% |
| **API performance** | p95 ≤ 300ms @ 100 users |
| **Mobile stability** | Crash rate < 0.1% |
| **Critical bugs** | 0 open |
| **High bugs** | ≤ 3 open |

---

*Phiên bản: v1.0 DRAFT — 20/03/2025*
