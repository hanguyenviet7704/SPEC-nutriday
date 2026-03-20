# TSD-ND-2025-001_03 — Integration Tests — API
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 03 — Integration Tests API |
| **Tool** | Supertest + NestJS E2E + Docker MySQL |
| **Coverage** | 100% endpoints (happy path + key error paths) |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. SETUP

```typescript
// test/setup/app.setup.ts
let app: INestApplication;
let dataSource: DataSource;

beforeAll(async () => {
  const moduleRef = await Test.createTestingModule({
    imports: [AppModule],
  }).compile();

  app = moduleRef.createNestApplication();
  app.useGlobalPipes(new ValidationPipe({ transform: true }));
  app.useGlobalFilters(new HttpExceptionFilter());
  await app.init();

  dataSource = moduleRef.get(DataSource);
  await dataSource.runMigrations();
  await seedTestDatabase(dataSource);
});

afterAll(async () => {
  await dataSource.dropDatabase();
  await app.close();
});

// Helper: login và lấy token
const loginAs = async (user: TestUser): Promise<string> => {
  const res = await request(app.getHttpServer())
    .post('/v1/auth/login')
    .send({ emailOrPhone: user.email, password: user.password });
  return res.body.data.accessToken;
};
```

---

## 2. AUTH INTEGRATION TESTS

```typescript
describe('POST /v1/auth/register', () => {
  it('INT-AUTH-001: 201 — đăng ký thành công', async () => {
    const res = await request(app.getHttpServer())
      .post('/v1/auth/register')
      .send({ fullName: 'New User', email: 'new@test.vn', phone: '0909999999', password: 'P@ss1234' });
    expect(res.status).toBe(201);
    expect(res.body.data).toHaveProperty('userId');
    expect(res.body.data).toHaveProperty('otpExpiry');
  });

  it('INT-AUTH-002: 409 — email trùng', async () => {
    const res = await request(app.getHttpServer())
      .post('/v1/auth/register')
      .send({ fullName: 'Dup', email: 'user_weightloss@test.vn', phone: '0908888888', password: 'P@ss1234' });
    expect(res.status).toBe(409);
    expect(res.body.error.code).toBe('USER_001');
  });

  it('INT-AUTH-003: 400 — password yếu', async () => {
    const res = await request(app.getHttpServer())
      .post('/v1/auth/register')
      .send({ fullName: 'Test', email: 'x@test.vn', phone: '0907777777', password: '123456' });
    expect(res.status).toBe(400);
    expect(res.body.error.code).toBe('VALIDATION_ERROR');
  });

  it('INT-AUTH-004: 429 — rate limit sau 10 requests', async () => { ... });
});

describe('POST /v1/auth/login', () => {
  it('INT-AUTH-005: 200 — credentials hợp lệ', async () => {
    const res = await request(app.getHttpServer())
      .post('/v1/auth/login')
      .send({ emailOrPhone: 'user_weightloss@test.vn', password: 'TestP@ss1' });
    expect(res.status).toBe(200);
    expect(res.body.data).toHaveProperty('accessToken');
    expect(res.body.data).toHaveProperty('refreshToken');
    expect(res.body.data.user.dietGoal).toBe('weight_loss');
  });

  it('INT-AUTH-006: 401 — sai mật khẩu', async () => { ... });
  it('INT-AUTH-007: 403 — tài khoản chưa verify', async () => { ... });
});
```

---

## 3. DISH INTEGRATION TESTS

```typescript
describe('GET /v1/dishes', () => {
  let token: string;
  beforeAll(async () => { token = await loginAs(testUsers.weightLoss); });

  it('INT-DISH-001: 200 — trả về paginated list', async () => {
    const res = await request(app.getHttpServer())
      .get('/v1/dishes?page=1&limit=10')
      .set('Authorization', `Bearer ${token}`);
    expect(res.status).toBe(200);
    expect(res.body.data).toHaveLength(10);
    expect(res.body.meta.total).toBeGreaterThan(0);
  });

  it('INT-DISH-002: 200 — filter by dietGoal', async () => {
    const res = await request(app.getHttpServer())
      .get('/v1/dishes?dietGoal=weight_loss')
      .set('Authorization', `Bearer ${token}`);
    // Tất cả dishes phải có diet_goal=weight_loss
    const dishIds = res.body.data.map(d => d.dishId);
    for (const id of dishIds) {
      const check = await dataSource.query(
        `SELECT 1 FROM dish_diet_goals WHERE dish_id = ? AND diet_goal = 'weight_loss'`, [id]
      );
      expect(check).toHaveLength(1);
    }
  });

  it('INT-DISH-003: 200 — chỉ trả về status=published', async () => {
    const statuses = res.body.data.map(d => d.status);
    statuses.forEach(s => expect(s).toBe('published'));
  });

  it('INT-DISH-004: 401 — thiếu token', async () => { ... });
});

describe('GET /v1/dishes/:id', () => {
  it('INT-DISH-005: 200 — đủ nutrition, ingredients, recipeSteps', async () => {
    const res = await request(app.getHttpServer())
      .get(`/v1/dishes/${testDishIds.phoBoId}`)
      .set('Authorization', `Bearer ${token}`);
    expect(res.body.data.nutrition).toBeDefined();
    expect(res.body.data.ingredients.length).toBeGreaterThan(0);
    expect(res.body.data.recipeSteps.length).toBeGreaterThan(0);
  });
  it('INT-DISH-006: 404 — dish không tồn tại', async () => { ... });
});
```

---

## 4. MEAL PLAN INTEGRATION TESTS

```typescript
describe('POST /v1/meal-plans/generate', () => {
  it('INT-PLAN-001: 201 — tạo 3 bữa thành công', async () => {
    const res = await request(app.getHttpServer())
      .post('/v1/meal-plans/generate')
      .set('Authorization', `Bearer ${token}`)
      .send({ date: '2025-06-01', mode: 'single' });
    expect(res.status).toBe(201);
    expect(res.body.data.plans[0].meals.breakfast).toBeDefined();
    expect(res.body.data.plans[0].meals.lunch).toBeDefined();
    expect(res.body.data.plans[0].meals.dinner).toBeDefined();
  });

  it('INT-PLAN-002: các món không trùng với 7 ngày lịch sử', async () => {
    // Seed 7 ngày lịch sử với dish_ids đã biết
    await seedMealHistory(dataSource, testUsers.weightLoss.id, last7Days, knownDishIds);
    const res = await request(app.getHttpServer())
      .post('/v1/meal-plans/generate')
      .set('Authorization', `Bearer ${token}`)
      .send({ date: '2025-06-08', mode: 'single' });
    const newDishIds = Object.values(res.body.data.plans[0].meals).map(m => m.dishId);
    newDishIds.forEach(id => expect(knownDishIds).not.toContain(id));
  });

  it('INT-PLAN-003: không chọn món có allergen của user', async () => {
    // user_weightloss dị ứng shellfish
    const newDishIds = Object.values(res.body.data.plans[0].meals).map(m => m.dishId);
    for (const dishId of newDishIds) {
      const allergens = await dataSource.query(
        `SELECT allergen_code FROM dish_allergens WHERE dish_id = ?`, [dishId]
      );
      const codes = allergens.map(a => a.allergen_code);
      expect(codes).not.toContain('shellfish');
    }
  });

  it('INT-PLAN-004: 409 — plan ngày đã tồn tại', async () => { ... });
  it('INT-PLAN-005: 422 PLAN_001 — không đủ món', async () => { ... });
});

describe('PATCH /v1/meal-plans/:planId/swap', () => {
  it('INT-PLAN-006: 200 — was_swapped=true, original_dish_id được lưu', async () => { ... });
  it('INT-PLAN-007: 403 — plan thuộc user khác', async () => { ... });
});
```

---

## 5. SHOPPING LIST INTEGRATION TESTS

```typescript
describe('POST /v1/shopping-lists/generate', () => {
  it('INT-SHOP-001: 201 — tạo list daily thành công', async () => { ... });

  it('INT-SHOP-002: nguyên liệu trùng được gộp lại', async () => {
    const list = res.body.data;
    const allItems = list.categories.flatMap(c => c.items);
    const ingredientIds = allItems.map(i => i.ingredientId);
    const uniqueIds = [...new Set(ingredientIds)];
    expect(ingredientIds.length).toBe(uniqueIds.length); // Không có duplicate
  });

  it('INT-SHOP-003: số lượng × servings=2', async () => {
    // Verify: single serving quantity * 2 = list quantity
  });

  it('INT-SHOP-004: 422 SHOP_001 — chưa có meal plan', async () => { ... });
});

describe('PATCH /v1/shopping-lists/:id/items/:itemId', () => {
  it('INT-SHOP-005: cập nhật is_checked và checked_at', async () => { ... });
  it('INT-SHOP-006: shopping_list.checked_items tăng', async () => { ... });
});
```

---

## 6. ADMIN INTEGRATION TESTS

```typescript
describe('POST /v1/admin/dishes', () => {
  let editorToken: string;
  let nutritionistToken: string;
  beforeAll(async () => {
    editorToken = await loginAsAdmin(adminUsers.editor);
    nutritionistToken = await loginAsAdmin(adminUsers.nutritionist);
  });

  it('INT-ADM-001: 201 — content_editor tạo dish thành công', async () => {
    const res = await request(app.getHttpServer())
      .post('/v1/admin/dishes')
      .set('Authorization', `Bearer ${editorToken}`)
      .send(validDishPayload);
    expect(res.status).toBe(201);
    expect(res.body.data.status).toBe('draft');
  });

  it('INT-ADM-002: 403 — nutritionist không có quyền tạo', async () => {
    const res = await request(app.getHttpServer())
      .post('/v1/admin/dishes')
      .set('Authorization', `Bearer ${nutritionistToken}`)
      .send(validDishPayload);
    expect(res.status).toBe(403);
  });
});

describe('POST /v1/admin/dishes/:id/review', () => {
  it('INT-ADM-003: approve → status=published, is_nutritionist_verified=true', async () => { ... });
  it('INT-ADM-004: reject → status=rejected', async () => { ... });
  it('INT-ADM-005: dish_reviews record được tạo', async () => { ... });
  it('INT-ADM-006: audit_log record được tạo', async () => { ... });
});

describe('POST /v1/admin/dishes/import', () => {
  it('INT-ADM-007: 200 — import 47 valid rows, 3 error rows', async () => {
    const res = await request(app.getHttpServer())
      .post('/v1/admin/dishes/import')
      .set('Authorization', `Bearer ${editorToken}`)
      .attach('file', 'test/fixtures/dishes_sample.xlsx')
      .field('mode', 'import');
    expect(res.body.data.importedCount).toBe(47);
    expect(res.body.data.errors).toHaveLength(3);
  });

  it('INT-ADM-008: 400 — file không phải xlsx', async () => { ... });
});
```

---

**Trang trước:** [02 — Unit Frontend ←](TSD-ND-2025-001_02_Unit_Frontend.md)
**Trang tiếp theo:** [04 — E2E Mobile →](TSD-ND-2025-001_04_E2E_Mobile.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
