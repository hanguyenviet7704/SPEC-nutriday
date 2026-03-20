# TSD-ND-2025-001_09 — Test Data & Acceptance Criteria
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 09 — Test Data & Acceptance |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. SEED DATA

### 1.1 Test Users

| User | Email | Diet Goal | Servings | Allergies | Status |
|---|---|---|---|---|---|
| user_weightloss | user_weightloss@test.vn | weight_loss | 1 | shellfish | active, verified |
| user_diabetes | user_diabetes@test.vn | diabetes | 2 | peanut, dairy | active, verified |
| user_vegetarian | user_vegetarian@test.vn | vegetarian | 1 | — | active, verified |
| user_unverified | user_unverified@test.vn | — | 1 | — | active, NOT verified |
| user_inactive | user_inactive@test.vn | healthy | 1 | — | INACTIVE |
| user_new | (created during test) | — | — | — | — |

**Password (all test users):** `TestP@ss1`

### 1.2 Admin Users

| User | Email | Role |
|---|---|---|
| admin_super | admin@nutriday.vn | super_admin |
| admin_editor | editor@nutriday.vn | content_editor |
| admin_nutri | nutri@nutriday.vn | nutritionist |

**Password (all admin):** `Admin@123`

### 1.3 Test Dishes (20 seed dishes)

| ID | Name | Diet Goals | Meal Types | Calories | Allergens | Status |
|---|---|---|---|---|---|---|
| dish_001 | Phở Bò | weight_loss, healthy | lunch, dinner | 450 | gluten | published |
| dish_002 | Cơm gà xối mỡ | weight_gain, healthy | lunch, dinner | 680 | — | published |
| dish_003 | Bánh mì trứng | weight_loss, eat_clean | breakfast | 320 | gluten, egg | published |
| dish_004 | Bún chả | weight_loss | lunch | 480 | — | published |
| dish_005 | Canh chua cá lóc | diabetes, healthy | lunch, dinner | 280 | fish | published |
| dish_006 | Gỏi cuốn tôm | eat_clean, low_carb | lunch | 220 | shellfish | published |
| dish_007 | Cháo gà | healthy | breakfast | 350 | — | published |
| dish_008 | Salad trứng | eat_clean, vegetarian | breakfast, lunch | 280 | egg | published |
| dish_009 | Cơm tấm sườn | weight_gain | lunch, dinner | 750 | — | published |
| dish_010 | Bún bò Huế | weight_loss, healthy | lunch | 420 | — | published |
| dish_011 | Mì Quảng | healthy | lunch | 380 | gluten | published |
| dish_012 | Rau xào thập cẩm | vegetarian, eat_clean | dinner | 180 | soy | published |
| dish_013 | Thịt kho tàu | high_protein | dinner | 550 | egg | published |
| dish_014 | Canh bí đỏ | diabetes, low_carb | dinner | 150 | — | published |
| dish_015 | Bánh cuốn | weight_loss | breakfast | 300 | — | published |
| dish_016 | Draft dish | weight_loss | lunch | 400 | — | draft |
| dish_017 | Pending dish | healthy | lunch | 420 | — | pending_review |
| dish_018 | Rejected dish | healthy | dinner | 380 | — | rejected |
| dish_019 | Tôm hấp sả | shellfish_test | dinner | 250 | shellfish | published |
| dish_020 | Đậu phụ chiên | vegetarian, low_carb | dinner | 320 | soy | published |

### 1.4 Test Ingredients (sampled)

```sql
-- 300 ingredients seeded (matching DDD migration plan)
-- Sampled for test:
INSERT INTO ingredients (ingredient_id, name, default_unit, category, calories_per_100g)
VALUES
  ('ing_001', 'Thịt bò', 'g', 'protein', 250),
  ('ing_002', 'Bánh phở', 'g', 'grain', 180),
  ('ing_003', 'Hành lá', 'cọng', 'vegetable', 25),
  ('ing_004', 'Tôm', 'g', 'protein', 100),
  ('ing_005', 'Bún tươi', 'g', 'grain', 150),
  ('ing_006', 'Rau muống', 'bó', 'vegetable', 20),
  ('ing_007', 'Cá lóc', 'g', 'protein', 120),
  ('ing_008', 'Đậu phụ', 'miếng', 'protein', 80),
  ('ing_009', 'Trứng gà', 'quả', 'protein', 155),
  ('ing_010', 'Gạo', 'g', 'grain', 130);
```

---

## 2. TEST ENVIRONMENT

### 2.1 Docker Compose (Test)

```yaml
# docker-compose.test.yml
version: '3.8'
services:
  mysql-test:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: test
      MYSQL_DATABASE: nutriday_test
    ports: ['3307:3306']
    tmpfs: /var/lib/mysql    # RAM disk for speed

  redis-test:
    image: redis:7-alpine
    ports: ['6380:6379']

  api-test:
    build: ./backend
    environment:
      NODE_ENV: test
      DB_HOST: mysql-test
      DB_PORT: 3306
      DB_NAME: nutriday_test
      REDIS_URL: redis://redis-test:6379
    depends_on: [mysql-test, redis-test]
    command: npm run test:e2e
```

### 2.2 Database Reset Strategy

```typescript
// test/setup/database.ts
export const resetTestDatabase = async (dataSource: DataSource) => {
  // Truncate all tables (order matters due to FK)
  const tables = [
    'shopping_list_items', 'shopping_lists',
    'meal_plan_items', 'meal_plans',
    'meal_history', 'favorites',
    'dish_reviews', 'audit_logs',
    'dish_ingredients', 'recipe_steps',
    'dish_allergens', 'dish_diet_goals', 'dish_meal_types',
    'dish_nutrition', 'dish_images', 'dishes',
    'user_allergies', 'user_health_profiles',
    'otp_verifications', 'password_reset_tokens', 'refresh_tokens',
    'device_tokens', 'notification_settings',
    'users', 'admin_users', 'ingredients',
  ];
  await dataSource.query('SET FOREIGN_KEY_CHECKS = 0');
  for (const table of tables) {
    await dataSource.query(`TRUNCATE TABLE ${table}`);
  }
  await dataSource.query('SET FOREIGN_KEY_CHECKS = 1');
  await seedTestDatabase(dataSource);
};
```

---

## 3. ACCEPTANCE CRITERIA

### 3.1 Phase 1 Release Criteria

| Criteria | Target | Blocking? |
|---|---|---|
| Unit test pass rate | 100% | Yes |
| Unit test coverage (backend) | ≥ 80% | Yes |
| Unit test coverage (frontend) | ≥ 70% | Yes |
| Integration test pass rate | 100% | Yes |
| E2E critical flows pass | 100% (8 mobile + 6 admin) | Yes |
| Performance p95 | < 500ms | Yes |
| Performance p99 | < 1000ms | No (warning) |
| Security (OWASP ZAP) | No HIGH/CRITICAL | Yes |
| Security (npm audit) | No CRITICAL | Yes |
| Accessibility (admin) | WCAG 2.1 AA | No (P2) |

### 3.2 Definition of Done (DoD)

Mỗi feature được coi là "Done" khi:
1. Code đã được review và approve (≥ 1 reviewer)
2. Unit tests pass (coverage ≥ threshold)
3. Integration tests pass (nếu có API changes)
4. E2E test cho affected flow pass
5. No lint errors
6. No TypeScript errors
7. Tested on iOS Simulator + Android Emulator
8. UI matches SDD designs
9. API matches APID spec

---

## 4. DEFECT MANAGEMENT

### 4.1 Severity Levels

| Severity | Định nghĩa | SLA |
|---|---|---|
| S1 — Critical | App crash, data loss, security breach | Fix trong 4 giờ |
| S2 — Major | Feature không hoạt động, incorrect data | Fix trong 24 giờ |
| S3 — Minor | UI issue, edge case, workaround available | Fix trong sprint |
| S4 — Trivial | Cosmetic, typo, enhancement | Backlog |

### 4.2 Bug Report Template

```markdown
**Title:** [Module] Brief description
**Severity:** S1/S2/S3/S4
**Environment:** Staging / iOS 17 / iPhone 15
**Steps to Reproduce:**
1. Login as user_weightloss
2. Navigate to Home
3. Tap "Đổi" on lunch meal
4. Select first suggestion

**Expected:** Meal updated, back to Home
**Actual:** App crashes
**Screenshots/Logs:** [Attach]
**Test Case:** TC-PLAN-011
```

---

## 5. CI/CD QUALITY GATES

```yaml
# Quality gates trong CI pipeline:
# Gate 1: Lint (0 errors)
# Gate 2: Type check (0 errors)
# Gate 3: Unit tests (100% pass, coverage ≥ threshold)
# Gate 4: Integration tests (100% pass)
# Gate 5: Security scan (no HIGH/CRITICAL)
# Gate 6: Build success

# Nếu bất kỳ gate nào fail → block merge/deploy
```

---

**Trang trước:** [08 — Test Cases ←](TSD-ND-2025-001_08_TestCases.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
