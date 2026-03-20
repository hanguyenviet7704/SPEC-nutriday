# TSD-ND-2025-001_02 — Unit Tests — Frontend
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 02 — Unit Tests Frontend |
| **Tool** | Jest + @testing-library/react-native (Mobile) · @testing-library/react (Admin) |
| **Coverage Target** | ≥ 70% components, ≥ 80% hooks/stores |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. SETUP

```typescript
// jest.config.js (Mobile)
module.exports = {
  preset: 'jest-expo',
  setupFilesAfterFramework: ['@testing-library/jest-native/extend-expect'],
  transformIgnorePatterns: [
    'node_modules/(?!((jest-)?react-native|@react-native|expo|@expo|phosphor-react-native)/)',
  ],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
};

// Mock setup
jest.mock('@react-native-async-storage/async-storage', () =>
  require('@react-native-async-storage/async-storage/jest/async-storage-mock')
);
jest.mock('expo-notifications', () => ({ requestPermissionsAsync: jest.fn() }));
```

---

## 2. COMPONENT TESTS

### 2.1 Button Component

```typescript
// src/components/atoms/Button/Button.test.tsx
describe('Button', () => {
  it('UT-FE-001: render label text đúng', () => {
    const { getByText } = render(<Button label="Đăng nhập" onPress={() => {}} />);
    expect(getByText('Đăng nhập')).toBeTruthy();
  });

  it('UT-FE-002: gọi onPress khi nhấn', () => {
    const onPress = jest.fn();
    const { getByText } = render(<Button label="OK" onPress={onPress} />);
    fireEvent.press(getByText('OK'));
    expect(onPress).toHaveBeenCalledTimes(1);
  });

  it('UT-FE-003: không gọi onPress khi disabled=true', () => {
    const onPress = jest.fn();
    const { getByText } = render(<Button label="OK" onPress={onPress} disabled />);
    fireEvent.press(getByText('OK'));
    expect(onPress).not.toHaveBeenCalled();
  });

  it('UT-FE-004: hiện ActivityIndicator khi loading=true', () => {
    const { getByTestId } = render(<Button label="OK" onPress={() => {}} loading />);
    expect(getByTestId('loading-indicator')).toBeTruthy();
  });

  it('UT-FE-005: apply đúng style cho variant=primary', () => {
    const { getByTestId } = render(<Button label="OK" onPress={() => {}} variant="primary" />);
    expect(getByTestId('button-container')).toHaveStyle({ backgroundColor: '#2E7D52' });
  });
});
```

### 2.2 MealCard Component

```typescript
describe('MealCard', () => {
  const mockDish = {
    dishId: 'dish_001', name: 'Phở Bò',
    image: 'https://cdn.nutriday.vn/pho_bo.jpg',
    calories: 450, cookingTime: 45,
    isNutritionistVerified: true, isFavorited: false,
  };

  it('UT-FE-006: hiện tên món, calories, cooking time', () => {
    const { getByText } = render(<MealCard dish={mockDish} mealType="lunch" onPress={() => {}} onFavorite={() => {}} onSwap={() => {}} />);
    expect(getByText('Phở Bò')).toBeTruthy();
    expect(getByText('450 kcal')).toBeTruthy();
    expect(getByText('45 phút')).toBeTruthy();
  });

  it('UT-FE-007: hiện verified badge khi isNutritionistVerified=true', () => {
    const { getByTestId } = render(<MealCard dish={{ ...mockDish, isNutritionistVerified: true }} mealType="lunch" onPress={() => {}} onFavorite={() => {}} onSwap={() => {}} />);
    expect(getByTestId('verified-badge')).toBeTruthy();
  });

  it('UT-FE-008: ẩn verified badge khi isNutritionistVerified=false', () => {
    const { queryByTestId } = render(<MealCard dish={{ ...mockDish, isNutritionistVerified: false }} mealType="lunch" onPress={() => {}} onFavorite={() => {}} onSwap={() => {}} />);
    expect(queryByTestId('verified-badge')).toBeNull();
  });

  it('UT-FE-009: icon tim filled khi isFavorited=true', () => { ... });
  it('UT-FE-010: gọi onFavorite khi nhấn tim', () => { ... });
  it('UT-FE-011: gọi onSwap khi nhấn nút Đổi', () => { ... });
  it('UT-FE-012: gọi onPress khi nhấn card', () => { ... });
});
```

### 2.3 NutritionBar Component

```typescript
describe('NutritionBar', () => {
  it('UT-FE-013: hiển thị đúng tổng calories', () => { ... });
  it('UT-FE-014: tính đúng % protein = protein/calories*4*100', () => { ... });
  it('UT-FE-015: bar width không vượt 100%', () => { ... });
  it('UT-FE-016: màu đúng cho từng macro', () => { ... });
});
```

### 2.4 TextInput Component

```typescript
describe('NutriTextInput', () => {
  it('UT-FE-017: hiện error message khi error prop có giá trị', () => {
    const { getByText } = render(<NutriTextInput label="Email" error="Email không hợp lệ" value="" onChangeText={() => {}} />);
    expect(getByText('Email không hợp lệ')).toBeTruthy();
  });

  it('UT-FE-018: border chuyển sang màu đỏ khi có error', () => { ... });
  it('UT-FE-019: toggle password visibility khi nhấn eye icon', () => { ... });
  it('UT-FE-020: gọi onChangeText với value đúng', () => { ... });
});
```

---

## 3. CUSTOM HOOK TESTS

### 3.1 useTodayMealPlan

```typescript
// src/hooks/__tests__/useTodayMealPlan.test.ts
const wrapper = createQueryWrapper();

describe('useTodayMealPlan', () => {
  it('UT-FE-021: trả về loading state ban đầu', () => {
    server.use(http.get('*/meal-plans/today', () => new HttpResponse(null, { status: 200 })));
    const { result } = renderHook(() => useTodayMealPlan(), { wrapper });
    expect(result.current.isLoading).toBe(true);
  });

  it('UT-FE-022: trả về data sau khi fetch thành công', async () => {
    server.use(http.get('*/meal-plans/today', () => HttpResponse.json(mockTodayPlan)));
    const { result } = renderHook(() => useTodayMealPlan(), { wrapper });
    await waitFor(() => expect(result.current.isSuccess).toBe(true));
    expect(result.current.data?.meals).toHaveLength(3);
  });

  it('UT-FE-023: auto-generate plan khi API trả về PLAN_NOT_FOUND', async () => { ... });
});
```

### 3.2 useSwapMeal

```typescript
describe('useSwapMeal', () => {
  it('UT-FE-024: optimistic update — hiển thị món mới ngay lập tức', async () => { ... });
  it('UT-FE-025: rollback khi API thất bại', async () => { ... });
  it('UT-FE-026: invalidate todayPlan cache sau khi thành công', async () => { ... });
});
```

### 3.3 useToggleFavorite

```typescript
describe('useToggleFavorite', () => {
  it('UT-FE-027: gọi API add khi isFavorited=false', async () => { ... });
  it('UT-FE-028: gọi API remove khi isFavorited=true', async () => { ... });
  it('UT-FE-029: cập nhật dish cache sau thành công', async () => { ... });
});
```

---

## 4. ZUSTAND STORE TESTS

```typescript
describe('authStore', () => {
  beforeEach(() => useAuthStore.setState({ accessToken: null, user: null, isAuthenticated: false }));

  it('UT-FE-030: login — set token và user', async () => {
    server.use(http.post('*/auth/login', () => HttpResponse.json(mockLoginResponse)));
    await useAuthStore.getState().login({ emailOrPhone: 'test@test.vn', password: 'P@ss1234' });
    expect(useAuthStore.getState().isAuthenticated).toBe(true);
    expect(useAuthStore.getState().accessToken).toBeDefined();
  });

  it('UT-FE-031: logout — clear state và revoke token', async () => { ... });
  it('UT-FE-032: refreshAccessToken — cập nhật accessToken', async () => { ... });
});

describe('onboardingStore', () => {
  it('UT-FE-033: setDietGoal — cập nhật dietGoal và bước tiếp theo', () => { ... });
  it('UT-FE-034: toggleAllergen — thêm allergen nếu chưa có', () => { ... });
  it('UT-FE-035: toggleAllergen — xóa allergen nếu đã có', () => { ... });
});
```

---

## 5. FORM VALIDATION TESTS

```typescript
describe('RegisterForm validation (Zod schema)', () => {
  const schema = registerSchema;

  it('UT-FE-036: email không hợp lệ → error', () => {
    const result = schema.safeParse({ email: 'notanemail', phone: '0901234567', password: 'P@ss1234', fullName: 'Test' });
    expect(result.success).toBe(false);
    expect(result.error?.issues[0].path).toContain('email');
  });

  it('UT-FE-037: password < 8 ký tự → error', () => { ... });
  it('UT-FE-038: phone không đúng 10 số → error', () => { ... });
  it('UT-FE-039: tất cả hợp lệ → pass', () => { ... });
});
```

---

**Trang trước:** [01 — Unit Tests Backend ←](TSD-ND-2025-001_01_Unit_Backend.md)
**Trang tiếp theo:** [03 — Integration API →](TSD-ND-2025-001_03_Integration_API.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
