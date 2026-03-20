# TSD-ND-2025-001_00 — Chiến Lược & Môi Trường
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 00 — Strategy & Environment |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. CHIẾN LƯỢC KIỂM THỬ

### 1.1 Test Pyramid

```
            ┌──────────────┐
            │   E2E Tests  │  ~25 flows — Detox + Playwright
            │   (Chậm)     │  Nightly build
            ├──────────────┤
            │ Integration  │  ~90 test cases — Supertest
            │  Tests       │  Mỗi PR
            ├──────────────┤
            │  Unit Tests  │  ~200 test cases — Jest
            │  (Nhanh)     │  Mỗi commit
            └──────────────┘
```

### 1.2 Phân loại & Tools

| Loại | Tool | Trigger | Thời gian |
|---|---|---|---|
| Unit — Backend | Jest + NestJS Testing | Pre-commit | < 2 phút |
| Unit — Frontend | Jest + RNTL / RTL | Pre-commit | < 2 phút |
| Integration | Supertest + Docker MySQL | PR merge | < 10 phút |
| E2E Mobile | Detox | Nightly | ~30 phút |
| E2E Admin Web | Playwright | Nightly | ~20 phút |
| Performance | k6 | Pre-release | ~60 phút |
| Security | OWASP ZAP + Manual | Pre-release | ~120 phút |

### 1.3 Coverage Targets

| Layer | Minimum | Target |
|---|---|---|
| Backend — Services | 80% | 90% |
| Backend — Controllers | 70% | 80% |
| Frontend — Components | 70% | 80% |
| Frontend — Hooks/Stores | 80% | 90% |
| API Endpoints | 100% (happy path) | 100% (+ error paths) |

---

## 2. TEST ENVIRONMENTS

### 2.1 Cấu hình

| Env | Mục đích | Database | API |
|---|---|---|---|
| **Local** | Dev testing | Docker MySQL | `localhost:3000` |
| **CI** | Automated gates | MySQL in GitHub Actions | Internal |
| **Staging** | UAT · E2E · Perf | AWS RDS Staging | `api-staging.nutriday.vn` |

### 2.2 Docker Compose — CI

```yaml
# docker-compose.test.yml
services:
  mysql-test:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: testroot
      MYSQL_DATABASE: nutriday_test
      MYSQL_USER: test_user
      MYSQL_PASSWORD: testpass
    ports:
      - "3307:3306"
    options: >-
      --health-cmd="mysqladmin ping"
      --health-interval=10s
      --health-timeout=5s
      --health-retries=3

  api-test:
    build: .
    environment:
      NODE_ENV: test
      DATABASE_URL: mysql://test_user:testpass@mysql-test:3306/nutriday_test
    depends_on:
      mysql-test:
        condition: service_healthy
```

### 2.3 Database Seeding

```typescript
// test/seeds/index.ts
export async function seedTestDatabase(dataSource: DataSource) {
  await seedAdminUsers(dataSource);    // 3 admin users
  await seedIngredients(dataSource);   // 300 ingredients
  await seedDishes(dataSource);        // 60+ dishes, all status=published
  await seedUsers(dataSource);         // 5 test users với các dietGoal khác nhau
}

// Reset sau mỗi test suite
afterEach(async () => {
  await dataSource.query('SET FOREIGN_KEY_CHECKS=0');
  await clearTables(['meal_plans', 'shopping_lists', 'favorites', 'meal_history']);
  await dataSource.query('SET FOREIGN_KEY_CHECKS=1');
});
```

---

## 3. DEVICE MATRIX — MOBILE

### 3.1 Physical Devices

| Thiết bị | OS | Priority | Test Type |
|---|---|---|---|
| iPhone 15 Pro | iOS 17.4 | P1 — Primary | Unit · E2E |
| iPhone SE 3rd gen | iOS 16.0 | P2 — Secondary | E2E |
| Samsung Galaxy S23 | Android 13 | P1 — Primary | Unit · E2E |
| Google Pixel 7a | Android 13 | P2 — Secondary | E2E |
| Samsung Galaxy A32 | Android 12 | P3 — Budget | Smoke |
| iPad Air 5th gen | iPadOS 16 | P3 — Tablet | Layout |

### 3.2 Simulators/Emulators (CI)

| Thiết bị | OS | Dùng cho |
|---|---|---|
| iPhone 15 (Sim) | iOS 17 | Unit + Integration |
| Pixel API 33 (Emu) | Android 13 | Unit + Integration |

---

## 4. TEST DATA CONVENTIONS

### 4.1 OTP

- **Môi trường test/staging:** OTP cố định = `123456`
- **Production:** OTP ngẫu nhiên qua SMS

### 4.2 Test Users cố định

| userId | email | dietGoal | allergies |
|---|---|---|---|
| `test_u001` | `user_weightloss@test.vn` | `weight_loss` | `shellfish` |
| `test_u002` | `user_vegetarian@test.vn` | `vegetarian` | — |
| `test_u003` | `user_diabetes@test.vn` | `diabetes` | `gluten`, `peanuts` |
| `test_u004` | `user_eatclean@test.vn` | `eat_clean` | — |
| `test_u005` | `user_highpro@test.vn` | `high_protein` | `dairy` |

### 4.3 Số lượng dish tối thiểu cho test

| dietGoal | breakfast | lunch | dinner | Total |
|---|---|---|---|---|
| weight_loss | 7 | 7 | 7 | 21 |
| weight_gain | 7 | 7 | 7 | 21 |
| diabetes | 7 | 7 | 7 | 21 |
| eat_clean | 7 | 7 | 7 | 21 |
| vegetarian | 7 | 7 | 7 | 21 |
| healthy | 7 | 7 | 7 | 21 |
| low_carb | 7 | 7 | 7 | 21 |
| high_protein | 7 | 7 | 7 | 21 |
| **Total** | | | | **168** |

> Cần 7+ món/mealType/dietGoal để test no-repeat 7 ngày.

---

## 5. DEFINITION OF DONE

### 5.1 Per Feature

```
□ Unit tests viết và pass (coverage ≥ target)
□ Integration tests viết và pass
□ E2E test case tương ứng pass (nếu có)
□ Không có console error / warning
□ API response time ≤ 300ms
□ Code review approved (≥ 1 reviewer)
□ No critical security issues
□ Accessibility: touch targets ≥ 44pt
```

### 5.2 Per Sprint Release

```
□ Tất cả unit + integration tests pass
□ Coverage không giảm so với sprint trước
□ E2E smoke tests pass trên staging
□ Không có P0 bugs open
□ P1 bugs ≤ 5 open
```

### 5.3 MVP Go-live

```
□ 100% P0 test cases pass
□ 0 critical (P0) bugs open
□ ≤ 3 high (P1) bugs open
□ Backend coverage ≥ 80%
□ Frontend coverage ≥ 70%
□ p95 API latency ≤ 300ms @ 100 concurrent users
□ Mobile crash rate < 0.1% (TestFlight 500+ sessions)
□ 0 critical OWASP vulnerabilities
□ Stakeholder UAT sign-off trên staging
```

---

**Trang tiếp theo:** [01 — Unit Tests Backend →](TSD-ND-2025-001_01_Unit_Backend.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
