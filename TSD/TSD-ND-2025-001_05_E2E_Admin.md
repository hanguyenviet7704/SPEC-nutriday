# TSD-ND-2025-001_05 — E2E Tests — Admin Web
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 05 — E2E Tests Admin Web |
| **Tool** | Playwright (Chromium + Firefox) |
| **Coverage** | All admin workflows |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. SETUP

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  timeout: 30000,
  retries: 1,
  use: {
    baseURL: 'http://localhost:5173',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
    trace: 'retain-on-failure',
  },
  projects: [
    { name: 'chromium', use: { browserName: 'chromium' } },
    { name: 'firefox', use: { browserName: 'firefox' } },
  ],
});

// e2e/helpers/auth.ts
export const loginAsAdmin = async (page: Page, role: 'super_admin' | 'content_editor' | 'nutritionist') => {
  const credentials = {
    super_admin: { email: 'admin@nutriday.vn', password: 'Admin@123' },
    content_editor: { email: 'editor@nutriday.vn', password: 'Editor@123' },
    nutritionist: { email: 'nutri@nutriday.vn', password: 'Nutri@123' },
  };
  await page.goto('/admin/login');
  await page.fill('[data-testid="input-email"]', credentials[role].email);
  await page.fill('[data-testid="input-password"]', credentials[role].password);
  await page.click('[data-testid="btn-login"]');
  await page.waitForURL('/admin/dashboard');
};
```

---

## 2. E2E TEST FLOWS

### Flow 1: Admin Login

```typescript
describe('E2E-ADM-001: Admin Login', () => {
  test('Login as content_editor → dashboard', async ({ page }) => {
    await loginAsAdmin(page, 'content_editor');
    await expect(page.locator('h1')).toHaveText('Dashboard');
  });

  test('Wrong password → error message', async ({ page }) => {
    await page.goto('/admin/login');
    await page.fill('[data-testid="input-email"]', 'admin@nutriday.vn');
    await page.fill('[data-testid="input-password"]', 'wrong');
    await page.click('[data-testid="btn-login"]');
    await expect(page.locator('[data-testid="error-message"]')).toBeVisible();
  });

  test('Unauthorized role redirect', async ({ page }) => {
    await loginAsAdmin(page, 'nutritionist');
    await page.goto('/admin/users');
    await expect(page).toHaveURL('/admin/dashboard'); // Redirected
  });
});
```

---

### Flow 2: Create Dish

```typescript
describe('E2E-ADM-002: Create Dish', () => {
  test('Editor creates dish → status=draft', async ({ page }) => {
    await loginAsAdmin(page, 'content_editor');
    await page.click('text=Thêm mới');
    await page.waitForURL('/admin/dishes/new');

    // Tab 1: Basic info
    await page.fill('[name="name"]', 'Bún chả Hà Nội');
    await page.fill('[name="description"]', 'Món bún chả truyền thống');
    await page.click('[data-testid="chip-weight_loss"]');
    await page.click('[data-testid="chip-lunch"]');
    await page.fill('[name="cookingTimeMinutes"]', '40');

    // Tab 2: Nutrition
    await page.click('text=Dinh dưỡng');
    await page.fill('[name="calories"]', '520');
    await page.fill('[name="protein"]', '28');
    await page.fill('[name="carbs"]', '55');
    await page.fill('[name="fat"]', '18');

    // Tab 3: Ingredients
    await page.click('text=Nguyên liệu');
    await page.click('[data-testid="add-ingredient"]');
    await page.fill('[data-testid="ingredient-0-name"]', 'Bún tươi');
    await page.fill('[data-testid="ingredient-0-quantity"]', '200');
    await page.selectOption('[data-testid="ingredient-0-unit"]', 'g');

    // Submit
    await page.click('[data-testid="btn-save"]');
    await expect(page.locator('[data-testid="toast-success"]')).toBeVisible();
    await expect(page).toHaveURL(/\/admin\/dishes$/);
  });
});
```

---

### Flow 3: Review Dish (Nutritionist)

```typescript
describe('E2E-ADM-003: Review Dish', () => {
  test('Nutritionist approves dish → published', async ({ page }) => {
    await loginAsAdmin(page, 'nutritionist');
    await page.click('text=Duyệt món');
    await page.waitForURL('/admin/reviews');

    // Click first pending dish
    await page.click('[data-testid="review-item-0"]');
    await expect(page.locator('[data-testid="dish-detail"]')).toBeVisible();

    // Approve
    await page.click('[data-testid="btn-approve"]');
    await expect(page.locator('[data-testid="toast-success"]')).toContainText('Đã duyệt');
  });

  test('Nutritionist rejects dish with comment', async ({ page }) => {
    await loginAsAdmin(page, 'nutritionist');
    await page.click('text=Duyệt món');
    await page.click('[data-testid="review-item-0"]');

    await page.click('[data-testid="btn-reject"]');
    await page.fill('[data-testid="reject-comment"]', 'Thiếu thông tin dinh dưỡng chi tiết');
    await page.click('[data-testid="btn-confirm-reject"]');
    await expect(page.locator('[data-testid="toast-success"]')).toContainText('Đã từ chối');
  });
});
```

---

### Flow 4: Import Dishes (Excel)

```typescript
describe('E2E-ADM-004: Import Excel', () => {
  test('Upload valid xlsx → import success', async ({ page }) => {
    await loginAsAdmin(page, 'content_editor');
    await page.goto('/admin/dishes/import');

    // Upload file
    await page.setInputFiles('[data-testid="file-input"]', 'e2e/fixtures/dishes_sample.xlsx');
    await page.click('[data-testid="btn-validate"]');

    // Validation results
    await expect(page.locator('[data-testid="valid-count"]')).toHaveText('47');
    await expect(page.locator('[data-testid="error-count"]')).toHaveText('3');

    // Confirm import
    await page.click('[data-testid="btn-import"]');
    await expect(page.locator('[data-testid="toast-success"]')).toContainText('47 món');
  });
});
```

---

### Flow 5: Dashboard Reports

```typescript
describe('E2E-ADM-005: Dashboard', () => {
  test('Dashboard shows stats and charts', async ({ page }) => {
    await loginAsAdmin(page, 'super_admin');
    await expect(page.locator('[data-testid="stat-total-dishes"]')).toBeVisible();
    await expect(page.locator('[data-testid="stat-total-users"]')).toBeVisible();
    await expect(page.locator('[data-testid="stat-pending-reviews"]')).toBeVisible();
    await expect(page.locator('[data-testid="chart-registrations"]')).toBeVisible();
  });
});
```

---

### Flow 6: User Management (Super Admin)

```typescript
describe('E2E-ADM-006: User Management', () => {
  test('Super admin can deactivate user', async ({ page }) => {
    await loginAsAdmin(page, 'super_admin');
    await page.click('text=Người dùng');
    await page.click('[data-testid="user-row-0"] [data-testid="btn-actions"]');
    await page.click('text=Vô hiệu hóa');
    await page.click('[data-testid="btn-confirm"]');
    await expect(page.locator('[data-testid="toast-success"]')).toBeVisible();
  });
});
```

---

**Trang trước:** [04 — E2E Mobile ←](TSD-ND-2025-001_04_E2E_Mobile.md)
**Trang tiếp theo:** [06 — Performance Tests →](TSD-ND-2025-001_06_Performance.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
