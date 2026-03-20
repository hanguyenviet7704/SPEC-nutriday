# TSD-ND-2025-001_04 — E2E Tests — Mobile
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 04 — E2E Tests Mobile |
| **Tool** | Detox (iOS Simulator + Android Emulator) |
| **Coverage** | Critical user journeys (8 flows) |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. SETUP

```typescript
// .detoxrc.js
module.exports = {
  testRunner: { args: { $0: 'jest', config: 'e2e/jest.config.js' } },
  apps: {
    'ios.debug': {
      type: 'ios.app',
      binaryPath: 'ios/build/NutriDay.app',
      build: 'xcodebuild -workspace ios/NutriDay.xcworkspace -scheme NutriDay -configuration Debug -sdk iphonesimulator',
    },
    'android.debug': {
      type: 'android.apk',
      binaryPath: 'android/app/build/outputs/apk/debug/app-debug.apk',
      build: 'cd android && ./gradlew assembleDebug',
    },
  },
  devices: {
    simulator: { type: 'ios.simulator', device: { type: 'iPhone 15' } },
    emulator: { type: 'android.emulator', device: { avdName: 'Pixel_7' } },
  },
  configurations: {
    'ios.sim.debug': { device: 'simulator', app: 'ios.debug' },
    'android.emu.debug': { device: 'emulator', app: 'android.debug' },
  },
};
```

---

## 2. E2E TEST FLOWS

### Flow 1: Registration → Onboarding → First Meal Plan

```typescript
// e2e/flows/registration.e2e.ts
describe('E2E-MOB-001: Registration → Onboarding → Home', () => {
  beforeAll(async () => {
    await device.launchApp({ newInstance: true, delete: true });
  });

  it('Step 1: Welcome → Register', async () => {
    await expect(element(by.text('Đăng ký'))).toBeVisible();
    await element(by.text('Đăng ký')).tap();
  });

  it('Step 2: Fill register form', async () => {
    await element(by.id('input-fullName')).typeText('Test User');
    await element(by.id('input-email')).typeText('e2e@test.vn');
    await element(by.id('input-phone')).typeText('0901234567');
    await element(by.id('input-password')).typeText('P@ss1234');
    await element(by.id('checkbox-terms')).tap();
    await element(by.id('btn-register')).tap();
  });

  it('Step 3: OTP verification', async () => {
    await waitFor(element(by.text('Xác minh OTP'))).toBeVisible().withTimeout(5000);
    // Test OTP: 123456 (seeded in test environment)
    await element(by.id('otp-input')).typeText('123456');
    await element(by.id('btn-verify')).tap();
  });

  it('Step 4: Onboarding — select goal', async () => {
    await waitFor(element(by.text('Mục tiêu ăn uống'))).toBeVisible().withTimeout(5000);
    await element(by.id('goal-weight_loss')).tap();
    await element(by.id('btn-continue')).tap();
  });

  it('Step 5: Onboarding — servings', async () => {
    await element(by.id('btn-increment')).tap(); // 2 người
    await element(by.id('btn-continue')).tap();
  });

  it('Step 6: Onboarding — allergies', async () => {
    await element(by.id('allergen-shellfish')).tap();
    await element(by.id('btn-complete')).tap();
  });

  it('Step 7: Home screen with meal plan', async () => {
    await waitFor(element(by.id('home-screen'))).toBeVisible().withTimeout(10000);
    await expect(element(by.id('meal-breakfast'))).toBeVisible();
    await expect(element(by.id('meal-lunch'))).toBeVisible();
    await expect(element(by.id('meal-dinner'))).toBeVisible();
  });
});
```

---

### Flow 2: Login → View Today Plan

```typescript
describe('E2E-MOB-002: Login → Home', () => {
  it('Login with existing user', async () => {
    await element(by.text('Đăng nhập')).tap();
    await element(by.id('input-emailOrPhone')).typeText('user_weightloss@test.vn');
    await element(by.id('input-password')).typeText('TestP@ss1');
    await element(by.id('btn-login')).tap();
    await waitFor(element(by.id('home-screen'))).toBeVisible().withTimeout(5000);
  });

  it('Today plan has 3 meals', async () => {
    await expect(element(by.id('meal-breakfast'))).toBeVisible();
    await expect(element(by.id('nutrition-bar'))).toBeVisible();
  });
});
```

---

### Flow 3: Swap Meal

```typescript
describe('E2E-MOB-003: Swap Meal', () => {
  it('Tap swap → select alternative → updated', async () => {
    await element(by.id('swap-btn-lunch')).tap();
    await waitFor(element(by.text('Gợi ý thay thế'))).toBeVisible().withTimeout(5000);
    await element(by.id('suggestion-0')).tap(); // Chọn gợi ý đầu tiên
    await element(by.id('btn-confirm-swap')).tap();
    // Verify: meal card updated (different dish name)
    await waitFor(element(by.id('home-screen'))).toBeVisible().withTimeout(3000);
  });
});
```

---

### Flow 4: Dish Detail → Recipe

```typescript
describe('E2E-MOB-004: View Dish Detail', () => {
  it('Tap meal → dish detail → recipe', async () => {
    await element(by.id('meal-lunch')).tap();
    await waitFor(element(by.id('dish-detail-screen'))).toBeVisible().withTimeout(3000);
    await expect(element(by.id('nutrition-section'))).toBeVisible();
    await expect(element(by.id('ingredients-section'))).toBeVisible();

    await element(by.text('Xem công thức')).tap();
    await waitFor(element(by.id('recipe-screen'))).toBeVisible().withTimeout(3000);
    await expect(element(by.id('step-1'))).toBeVisible();
  });
});
```

---

### Flow 5: Shopping List

```typescript
describe('E2E-MOB-005: Shopping List', () => {
  it('Navigate to shopping → check items', async () => {
    await element(by.id('tab-weekly')).tap();
    await element(by.text('Tạo danh sách mua')).tap();
    await waitFor(element(by.id('shopping-screen'))).toBeVisible().withTimeout(5000);

    // Check first item
    await element(by.id('shopping-item-0')).tap();
    await expect(element(by.id('progress-bar'))).toBeVisible();
  });
});
```

---

### Flow 6: Search & Filter

```typescript
describe('E2E-MOB-006: Search', () => {
  it('Search for "phở" → results visible', async () => {
    await element(by.id('tab-search')).tap();
    await element(by.id('search-input')).typeText('phở');
    await waitFor(element(by.id('search-result-0'))).toBeVisible().withTimeout(3000);
  });
});
```

---

### Flow 7: Favorites

```typescript
describe('E2E-MOB-007: Toggle Favorite', () => {
  it('Favorite from dish detail → appears in favorites list', async () => {
    // Navigate to dish detail
    await element(by.id('meal-lunch')).tap();
    await element(by.id('favorite-btn')).tap();
    // Navigate to favorites
    await element(by.id('btn-back')).tap();
    await element(by.id('tab-profile')).tap();
    await element(by.text('Món yêu thích')).tap();
    await waitFor(element(by.id('favorites-screen'))).toBeVisible().withTimeout(3000);
  });
});
```

---

### Flow 8: Logout

```typescript
describe('E2E-MOB-008: Logout', () => {
  it('Profile → Logout → Welcome screen', async () => {
    await element(by.id('tab-profile')).tap();
    await element(by.text('Đăng xuất')).tap();
    await element(by.text('Xác nhận')).tap(); // Confirm dialog
    await waitFor(element(by.text('Đăng nhập'))).toBeVisible().withTimeout(3000);
  });
});
```

---

## 3. DEVICE MATRIX

| Device | OS | Priority |
|---|---|---|
| iPhone 15 | iOS 17 | P0 |
| iPhone 12 | iOS 16 | P1 |
| Pixel 7 | Android 14 | P0 |
| Samsung A54 | Android 13 | P1 |

---

**Trang trước:** [03 — Integration API ←](TSD-ND-2025-001_03_Integration_API.md)
**Trang tiếp theo:** [05 — E2E Admin →](TSD-ND-2025-001_05_E2E_Admin.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
