# SAD-ND-2025-001_02 — Backend Architecture
# NutriDay System Architecture

---

| | |
|---|---|
| **Module** | 02 — Backend Architecture |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](SAD-ND-2025-001_INDEX.md) |

---

## 1. NESTJS MODULE STRUCTURE

```
src/
├── main.ts
├── app.module.ts
│
├── common/
│   ├── decorators/          # @CurrentUser, @Roles, @Public
│   ├── filters/             # HttpExceptionFilter, TypeOrmExceptionFilter
│   ├── guards/              # JwtAuthGuard, RolesGuard, ThrottlerGuard
│   ├── interceptors/        # TransformInterceptor, LoggingInterceptor
│   ├── pipes/               # ParseUlidPipe
│   ├── dto/                 # PaginationDto, ApiResponseDto
│   └── constants/           # error-codes.ts, enums.ts
│
├── config/
│   ├── app.config.ts        # Validated env schema (Joi)
│   ├── database.config.ts   # TypeORM config
│   ├── redis.config.ts      # Redis connection
│   └── jwt.config.ts        # JWT options
│
├── modules/
│   ├── auth/
│   │   ├── auth.module.ts
│   │   ├── auth.controller.ts
│   │   ├── auth.service.ts
│   │   ├── strategies/      # JwtStrategy, JwtRefreshStrategy
│   │   ├── guards/          # LocalAuthGuard
│   │   └── dto/             # RegisterDto, LoginDto, RefreshDto
│   │
│   ├── users/
│   │   ├── users.module.ts
│   │   ├── users.controller.ts
│   │   ├── users.service.ts
│   │   ├── entities/        # User, UserHealthProfile, UserAllergy
│   │   └── dto/
│   │
│   ├── dishes/
│   │   ├── dishes.module.ts
│   │   ├── dishes.controller.ts
│   │   ├── dishes.service.ts
│   │   ├── entities/        # Dish, DishImage, DishNutrition, DishAllergen, DishDietGoal, DishMealType
│   │   └── dto/
│   │
│   ├── ingredients/
│   │   ├── ingredients.module.ts
│   │   ├── ingredients.controller.ts
│   │   ├── ingredients.service.ts
│   │   ├── entities/        # Ingredient, DishIngredient, RecipeStep, IngredientSubstitution
│   │   └── dto/
│   │
│   ├── meal-plans/
│   │   ├── meal-plans.module.ts
│   │   ├── meal-plans.controller.ts
│   │   ├── meal-plans.service.ts
│   │   ├── meal-plan-generator.service.ts    # Core algorithm
│   │   ├── entities/        # MealPlan, MealPlanItem
│   │   └── dto/
│   │
│   ├── shopping-lists/
│   │   ├── shopping-lists.module.ts
│   │   ├── shopping-lists.controller.ts
│   │   ├── shopping-lists.service.ts
│   │   ├── entities/        # ShoppingList, ShoppingListItem
│   │   └── dto/
│   │
│   ├── search/
│   │   ├── search.module.ts
│   │   ├── search.controller.ts
│   │   └── search.service.ts    # FULLTEXT MySQL search
│   │
│   ├── favorites/
│   │   ├── favorites.module.ts
│   │   ├── favorites.controller.ts
│   │   ├── favorites.service.ts
│   │   └── entities/        # Favorite
│   │
│   ├── notifications/
│   │   ├── notifications.module.ts
│   │   ├── notifications.controller.ts
│   │   ├── notifications.service.ts
│   │   └── entities/        # NotificationSettings, DeviceToken
│   │
│   └── admin/
│       ├── admin.module.ts
│       ├── admin-dishes.controller.ts
│       ├── admin-users.controller.ts
│       ├── admin-reports.controller.ts
│       ├── admin-import.service.ts       # Excel import (xlsx)
│       ├── admin-review.service.ts       # Nutritionist review workflow
│       ├── entities/        # AdminUser, DishReview, AuditLog
│       └── dto/
│
├── shared/
│   ├── cache/               # RedisCacheService
│   ├── email/               # SES email service
│   ├── upload/              # S3 upload service
│   └── health/              # HealthController
│
└── database/
    ├── migrations/
    └── seeds/               # Test seed data
```

---

## 2. SERVICE LAYER PATTERN

### 2.1 Base Service

```typescript
// common/base.service.ts
export abstract class BaseService<T> {
  constructor(
    protected readonly repository: Repository<T>,
    protected readonly cacheService: RedisCacheService,
  ) {}

  async findById(id: string, cacheKey?: string): Promise<T> {
    if (cacheKey) {
      const cached = await this.cacheService.get<T>(cacheKey);
      if (cached) return cached;
    }
    const entity = await this.repository.findOne({ where: { id } as any });
    if (!entity) throw new NotFoundException();
    if (cacheKey) await this.cacheService.set(cacheKey, entity);
    return entity;
  }

  async paginate(options: PaginationDto, where?: FindOptionsWhere<T>): Promise<PaginatedResult<T>> {
    const [data, total] = await this.repository.findAndCount({
      where,
      skip: (options.page - 1) * options.limit,
      take: options.limit,
    });
    return { data, meta: { total, page: options.page, limit: options.limit, totalPages: Math.ceil(total / options.limit) } };
  }
}
```

### 2.2 Meal Plan Generator (Core Algorithm)

```typescript
// modules/meal-plans/meal-plan-generator.service.ts
@Injectable()
export class MealPlanGeneratorService {
  async generate(userId: string, date: string, mode: 'single' | 'weekly'): Promise<MealPlan[]> {
    // 1. Load user profile (dietGoal, servings, allergies)
    const profile = await this.usersService.getProfileWithAllergies(userId);

    // 2. Get dishes NOT in last 7 days history
    const recentDishIds = await this.mealHistoryRepo.getRecentDishIds(userId, 7);

    // 3. Query eligible dishes
    const eligibleDishes = await this.dishesService.findEligible({
      dietGoal: profile.dietGoal,
      excludeDishIds: recentDishIds,
      excludeAllergens: profile.allergies,
      status: 'published',
    });

    // 4. Allocate per meal type (breakfast, lunch, dinner)
    const days = mode === 'single' ? 1 : 7;
    const plans: MealPlan[] = [];

    for (let d = 0; d < days; d++) {
      const planDate = addDays(date, d);
      const usedInPlan: string[] = [];

      const meals = {};
      for (const mealType of ['breakfast', 'lunch', 'dinner']) {
        const candidates = eligibleDishes.filter(
          dish => dish.mealTypes.includes(mealType) && !usedInPlan.includes(dish.id)
        );

        if (candidates.length === 0) {
          throw new BusinessException('PLAN_001', 'Không đủ món cho ' + mealType);
        }

        // Weighted random: prefer higher-rated, nutritionist-verified
        const selected = this.weightedRandom(candidates);
        usedInPlan.push(selected.id);
        meals[mealType] = selected;
      }

      plans.push({ date: planDate, meals, userId });
    }

    // 5. Save to DB + record meal_history
    return this.savePlans(plans);
  }
}
```

---

## 3. RESPONSE FORMAT

### Standard Success

```typescript
// common/interceptors/transform.interceptor.ts
@Injectable()
export class TransformInterceptor<T> implements NestInterceptor<T, ApiResponse<T>> {
  intercept(context: ExecutionContext, next: CallHandler): Observable<ApiResponse<T>> {
    return next.handle().pipe(
      map(data => ({
        success: true,
        data,
        timestamp: new Date().toISOString(),
      })),
    );
  }
}
```

### Standard Error

```typescript
// common/filters/http-exception.filter.ts
@Catch()
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse();

    const { status, code, message } = this.extractError(exception);

    response.status(status).json({
      success: false,
      error: { code, message },
      timestamp: new Date().toISOString(),
    });
  }
}
```

---

## 4. VALIDATION & DTO

```typescript
// Tất cả DTO dùng class-validator + class-transformer
// Global ValidationPipe trong main.ts

// main.ts
app.useGlobalPipes(new ValidationPipe({
  transform: true,
  whitelist: true,             // Strip unknown properties
  forbidNonWhitelisted: true,  // Throw if unknown properties
  transformOptions: { enableImplicitConversion: true },
}));

// Ví dụ DTO:
export class RegisterDto {
  @IsString() @MinLength(2) @MaxLength(100)
  fullName: string;

  @IsEmail()
  email: string;

  @Matches(/^0[0-9]{9}$/, { message: 'Phone phải là 10 số bắt đầu bằng 0' })
  phone: string;

  @IsString() @MinLength(8) @Matches(/^(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])/)
  password: string;
}
```

---

## 5. ERROR HANDLING STRATEGY

| Layer | Xử lý |
|---|---|
| Controller | Không try-catch, để exception filter xử lý |
| Service | Throw `BusinessException(code, message)` cho business errors |
| Repository | TypeORM exceptions tự động catch bởi `TypeOrmExceptionFilter` |
| External (Redis, S3) | Wrap trong try-catch, log error, fallback graceful |

```typescript
// common/exceptions/business.exception.ts
export class BusinessException extends HttpException {
  constructor(
    public readonly code: string,
    message: string,
    status: HttpStatus = HttpStatus.UNPROCESSABLE_ENTITY,
  ) {
    super({ code, message }, status);
  }
}
```

---

## 6. LOGGING

```typescript
// common/interceptors/logging.interceptor.ts
// Log format: JSON structured
{
  "timestamp": "2025-06-01T10:00:00.000Z",
  "level": "info",
  "method": "POST",
  "url": "/v1/meal-plans/generate",
  "userId": "user_01HXYZ...",
  "statusCode": 201,
  "duration": 245,           // ms
  "userAgent": "NutriDay/1.0 (iOS)"
}

// Sensitive fields masked: password, token, refreshToken
```

---

**Trang trước:** [01 — Infrastructure ←](SAD-ND-2025-001_01_Infrastructure.md)
**Trang tiếp theo:** [03 — Frontend Architecture →](SAD-ND-2025-001_03_Frontend.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
