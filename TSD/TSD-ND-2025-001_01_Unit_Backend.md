# TSD-ND-2025-001_01 — Unit Tests — Backend
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 01 — Unit Tests Backend |
| **Tool** | Jest + NestJS Testing Module |
| **Trigger** | Pre-commit + CI (mỗi PR) |
| **Coverage Target** | ≥ 80% services, ≥ 70% controllers |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. SETUP

```typescript
// jest.config.ts
export default {
  moduleFileExtensions: ['js', 'json', 'ts'],
  rootDir: 'src',
  testRegex: '.*\\.spec\\.ts$',
  transform: { '^.+\\.(t|j)s$': 'ts-jest' },
  collectCoverageFrom: ['**/*.(t|j)s'],
  coverageDirectory: '../coverage',
  testEnvironment: 'node',
  coverageThreshold: {
    global: { branches: 70, functions: 80, lines: 80, statements: 80 },
  },
};
```

---

## 2. AuthService Tests

```typescript
// src/modules/auth/auth.service.spec.ts
describe('AuthService', () => {
  let service: AuthService;
  let usersRepo: MockType<Repository<User>>;
  let otpRepo: MockType<Repository<OtpVerification>>;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        AuthService,
        { provide: getRepositoryToken(User), useFactory: mockRepository },
        { provide: getRepositoryToken(OtpVerification), useFactory: mockRepository },
        { provide: JwtService, useValue: mockJwtService },
        { provide: SmsService, useValue: mockSmsService },
      ],
    }).compile();
    service = module.get(AuthService);
  });

  // ─── register() ─────────────────────────────────
  describe('register()', () => {
    it('UT-AUTH-001: tạo user và gửi OTP thành công', async () => {
      usersRepo.findOne.mockResolvedValue(null);
      usersRepo.save.mockResolvedValue({ id: 'usr_001', email: 'test@test.vn' });
      const result = await service.register({ fullName: 'Test', email: 'test@test.vn', phone: '0901234567', password: 'P@ss1234' });
      expect(result.userId).toBeDefined();
      expect(mockSmsService.sendOtp).toHaveBeenCalledTimes(1);
    });

    it('UT-AUTH-002: throw ConflictException khi email đã tồn tại', async () => {
      usersRepo.findOne.mockResolvedValue({ email: 'test@test.vn' });
      await expect(service.register({ ...dto, email: 'test@test.vn' }))
        .rejects.toThrow(new ConflictException('USER_001'));
    });

    it('UT-AUTH-003: throw ConflictException khi phone đã tồn tại', async () => { ... });

    it('UT-AUTH-004: password phải được hash trước khi lưu', async () => {
      await service.register(dto);
      const savedUser = usersRepo.save.mock.calls[0][0];
      expect(savedUser.passwordHash).not.toBe(dto.password);
      expect(savedUser.passwordHash).toMatch(/^\$2b\$/); // bcrypt
    });
  });

  // ─── verifyOtp() ────────────────────────────────
  describe('verifyOtp()', () => {
    it('UT-AUTH-005: OTP đúng → is_verified = true', async () => { ... });
    it('UT-AUTH-006: OTP sai → throw BadRequestException AUTH_004', async () => { ... });
    it('UT-AUTH-007: OTP hết hạn → throw BadRequestException AUTH_005', async () => { ... });
    it('UT-AUTH-008: attempt_count tăng sau mỗi lần sai', async () => { ... });
    it('UT-AUTH-009: attempt_count ≥ 5 → throw TooManyRequestsException', async () => { ... });
  });

  // ─── login() ────────────────────────────────────
  describe('login()', () => {
    it('UT-AUTH-010: credentials hợp lệ → trả về accessToken + refreshToken', async () => {
      const result = await service.login({ emailOrPhone: 'test@test.vn', password: 'P@ss1234' });
      expect(result).toHaveProperty('accessToken');
      expect(result).toHaveProperty('refreshToken');
      expect(result).toHaveProperty('user');
    });
    it('UT-AUTH-011: sai password → throw UnauthorizedException', async () => { ... });
    it('UT-AUTH-012: is_verified = false → throw ForbiddenException AUTH_003', async () => { ... });
    it('UT-AUTH-013: is_active = false → throw ForbiddenException AUTH_006', async () => { ... });
    it('UT-AUTH-014: cập nhật last_login_at sau khi login thành công', async () => { ... });
  });

  // ─── refreshToken() ─────────────────────────────
  describe('refreshToken()', () => {
    it('UT-AUTH-015: refresh token hợp lệ → new access token', async () => { ... });
    it('UT-AUTH-016: refresh token hết hạn → UnauthorizedException', async () => { ... });
    it('UT-AUTH-017: refresh token đã revoked → UnauthorizedException', async () => { ... });
  });

  // ─── logout() ───────────────────────────────────
  describe('logout()', () => {
    it('UT-AUTH-018: revoke refresh token', async () => { ... });
  });
});
```

---

## 3. MealPlanService Tests

```typescript
// src/modules/meal-plans/meal-plan.service.spec.ts
describe('MealPlanService', () => {

  describe('generateDailyPlan()', () => {
    it('UT-PLAN-001: tạo đúng 3 bữa (breakfast, lunch, dinner)', async () => {
      const plan = await service.generateDailyPlan(userId, date);
      const mealTypes = plan.items.map(i => i.mealType);
      expect(mealTypes).toContain('breakfast');
      expect(mealTypes).toContain('lunch');
      expect(mealTypes).toContain('dinner');
    });

    it('UT-PLAN-002: không lặp món trong 7 ngày gần nhất', async () => {
      // Seed 7 ngày với các món đã dùng
      const recentDishIds = ['d1', 'd2', 'd3', 'd4', 'd5', 'd6', 'd7'];
      mockMealHistoryRepo.find.mockResolvedValue(recentDishIds.map(id => ({ dishId: id })));
      const plan = await service.generateDailyPlan(userId, date);
      const newDishIds = plan.items.map(i => i.dishId);
      newDishIds.forEach(id => expect(recentDishIds).not.toContain(id));
    });

    it('UT-PLAN-003: chỉ chọn món có status = published', async () => {
      const dishes = await mockDishRepo.findPublished.mock.results[0].value;
      dishes.forEach(d => expect(d.status).toBe('published'));
    });

    it('UT-PLAN-004: lọc theo diet_goal của user', async () => { ... });
    it('UT-PLAN-005: loại bỏ món có allergen của user', async () => { ... });

    it('UT-PLAN-006: throw PLAN_001 khi không đủ món (< 3 available)', async () => {
      mockDishRepo.findAvailable.mockResolvedValue([{ dishId: 'd1' }, { dishId: 'd2' }]);
      await expect(service.generateDailyPlan(userId, date))
        .rejects.toThrow(new UnprocessableEntityException('PLAN_001'));
    });

    it('UT-PLAN-007: throw PLAN_002 khi thực đơn ngày đã tồn tại', async () => { ... });
  });

  describe('generateWeeklyPlan()', () => {
    it('UT-PLAN-008: tạo đúng 7 ngày × 3 bữa = 21 meal_plan_items', async () => { ... });
    it('UT-PLAN-009: không trùng món trong toàn bộ 7 ngày + 7 ngày lịch sử', async () => { ... });
  });

  describe('getSwapSuggestions()', () => {
    it('UT-PLAN-010: trả về đúng 5 suggestions', async () => {
      const suggestions = await service.getSwapSuggestions(planId, 'lunch');
      expect(suggestions).toHaveLength(5);
    });
    it('UT-PLAN-011: suggestions không bao gồm món hiện tại', async () => { ... });
    it('UT-PLAN-012: suggestions không bao gồm món trong 7 ngày lịch sử', async () => { ... });
    it('UT-PLAN-013: lọc theo meal_type đúng', async () => { ... });
  });

  describe('swapMeal()', () => {
    it('UT-PLAN-014: cập nhật dish_id và was_swapped = true', async () => { ... });
    it('UT-PLAN-015: lưu original_dish_id', async () => { ... });
    it('UT-PLAN-016: throw 422 nếu món mới không hỗ trợ meal_type', async () => { ... });
    it('UT-PLAN-017: throw 403 nếu plan không thuộc user', async () => { ... });
  });
});
```

---

## 4. DishService Tests

```typescript
describe('DishService', () => {

  describe('findAll()', () => {
    it('UT-DISH-001: chỉ trả về status = published cho user endpoint', async () => { ... });
    it('UT-DISH-002: filter by dietGoal', async () => { ... });
    it('UT-DISH-003: filter by mealType', async () => { ... });
    it('UT-DISH-004: filter by cookingTime ≤ maxTime', async () => { ... });
    it('UT-DISH-005: pagination đúng (page, limit, total)', async () => { ... });
    it('UT-DISH-006: deleted_at IS NULL (soft delete)', async () => { ... });
  });

  describe('findOne()', () => {
    it('UT-DISH-007: trả về đầy đủ nutrition, ingredients, recipeSteps', async () => { ... });
    it('UT-DISH-008: throw 404 DISH_001 nếu dishId không tồn tại', async () => { ... });
    it('UT-DISH-009: throw 404 DISH_002 nếu status ≠ published', async () => { ... });
  });

  describe('search()', () => {
    it('UT-DISH-010: tìm kiếm không phân biệt hoa thường', async () => { ... });
    it('UT-DISH-011: tìm kiếm không phân biệt có dấu (phở = pho)', async () => { ... });
    it('UT-DISH-012: keyword rỗng trả về tất cả (giống findAll)', async () => { ... });
  });

  describe('findByIngredients()', () => {
    it('UT-DISH-013: matchType=any — có ít nhất 1 ingredient khớp', async () => { ... });
    it('UT-DISH-014: matchType=all — có đủ tất cả ingredient', async () => { ... });
    it('UT-DISH-015: sắp xếp theo matchPercentage giảm dần', async () => { ... });
    it('UT-DISH-016: tính matchPercentage = matchedCount/totalIngredients*100', async () => { ... });
  });
});
```

---

## 5. ShoppingListService Tests

```typescript
describe('ShoppingListService', () => {

  describe('generateDailyList()', () => {
    it('UT-SHOP-001: gộp nguyên liệu trùng (cùng ingredientId)', async () => {
      // Bữa sáng: hành tây 50g, Bữa trưa: hành tây 30g
      // → Shopping list: hành tây 80g (1 dòng)
      const list = await service.generateDailyList(userId, '2025-04-01');
      const hanhTay = list.items.filter(i => i.ingredientId === 'ing_015');
      expect(hanhTay).toHaveLength(1);
      expect(hanhTay[0].quantity).toBe(80);
    });

    it('UT-SHOP-002: nhân số lượng với servings=2', async () => { ... });
    it('UT-SHOP-003: phân loại đúng theo ingredient.category', async () => { ... });
    it('UT-SHOP-004: throw SHOP_001 nếu chưa có meal_plan ngày đó', async () => { ... });
  });

  describe('generateWeeklyList()', () => {
    it('UT-SHOP-005: tổng hợp đúng 7 ngày', async () => { ... });
    it('UT-SHOP-006: gộp nguyên liệu trùng từ các ngày khác nhau', async () => { ... });
  });

  describe('checkItem()', () => {
    it('UT-SHOP-007: cập nhật is_checked = true và checked_at', async () => { ... });
    it('UT-SHOP-008: tăng shopping_lists.checked_items counter', async () => { ... });
    it('UT-SHOP-009: throw 403 nếu item không thuộc user', async () => { ... });
  });
});
```

---

## 6. FavoritesService Tests

```typescript
describe('FavoritesService', () => {
  it('UT-FAV-001: addFavorite — tạo favorite record', async () => { ... });
  it('UT-FAV-002: addFavorite — throw 409 nếu đã tồn tại', async () => { ... });
  it('UT-FAV-003: removeFavorite — xóa record', async () => { ... });
  it('UT-FAV-004: removeFavorite — throw 404 nếu không tồn tại', async () => { ... });
  it('UT-FAV-005: addFavorite tăng dish.favorite_count', async () => { ... });
  it('UT-FAV-006: removeFavorite giảm dish.favorite_count', async () => { ... });
});
```

---

## 7. Admin Review Tests

```typescript
describe('AdminDishService - review()', () => {
  it('UT-ADM-001: approve → status = published, is_nutritionist_verified = true', async () => { ... });
  it('UT-ADM-002: reject → status = rejected, note được lưu', async () => { ... });
  it('UT-ADM-003: ghi lịch sử vào dish_reviews', async () => { ... });
  it('UT-ADM-004: throw 403 nếu reviewer không phải nutritionist', async () => { ... });
  it('UT-ADM-005: throw 422 nếu dish không ở trạng thái pending_review', async () => { ... });
});
```

---

**Trang trước:** [00 — Strategy ←](TSD-ND-2025-001_00_Strategy.md)
**Trang tiếp theo:** [02 — Unit Tests Frontend →](TSD-ND-2025-001_02_Unit_Frontend.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
