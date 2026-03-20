# FED-ND-2025-001_00 — Architecture
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 00 — Architecture |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## 1. TECH STACK

### 1.1 Mobile App

| Thành phần | Package | Version |
|---|---|---|
| Framework | React Native (Expo bare) | 0.73+ |
| Language | TypeScript | 5.0+ |
| Build tool | Expo SDK | 50+ |
| Navigation | React Navigation | v6 |
| State (global) | Zustand | 4+ |
| State (server) | TanStack React Query | v5 |
| Forms | React Hook Form + Zod | — |
| HTTP client | Axios | 1.6+ |
| Animation | React Native Reanimated | v3 |
| List | @shopify/flash-list | 1.6+ |
| Image | react-native-fast-image | 8+ |
| Bottom sheet | @gorhom/bottom-sheet | 4+ |
| Safe area | react-native-safe-area-context | 4+ |
| Icons | phosphor-react-native | 2+ |
| Push notifications | expo-notifications | — |
| Storage | @react-native-async-storage | 1+ |
| Testing | Jest + @testing-library/react-native | — |
| E2E | Detox | 20+ |

### 1.2 Admin Web

| Thành phần | Package | Version |
|---|---|---|
| Framework | React | 18+ |
| Language | TypeScript | 5.0+ |
| Build tool | Vite | 5+ |
| Routing | React Router DOM | v6 |
| State (global) | Zustand | 4+ |
| State (server) | TanStack React Query | v5 |
| Forms | React Hook Form + Zod | — |
| HTTP | Axios | 1.6+ |
| UI Library | shadcn/ui (Radix + Tailwind) | — |
| Table | TanStack Table | v8 |
| Charts | Recharts | 2+ |
| Excel | xlsx (SheetJS) | — |
| Icons | phosphor-react | 2+ |
| Testing | Jest + @testing-library/react | — |
| E2E | Playwright | 1.40+ |

---

## 2. ENVIRONMENTS

| Env | Mobile | Admin Web | API |
|---|---|---|---|
| **Development** | Expo Dev Client | `localhost:5173` | `localhost:3000` |
| **Staging** | TestFlight / Firebase Dist | `admin-staging.nutriday.vn` | `api-staging.nutriday.vn` |
| **Production** | App Store / Google Play | `admin.nutriday.vn` | `api.nutriday.vn` |

**Environment variables (Mobile — `.env`):**
```
EXPO_PUBLIC_API_URL=https://api.nutriday.vn/v1
EXPO_PUBLIC_CDN_URL=https://cdn.nutriday.vn
EXPO_PUBLIC_ENV=production
```

**Environment variables (Admin — `.env`):**
```
VITE_API_URL=https://api.nutriday.vn/v1
VITE_CDN_URL=https://cdn.nutriday.vn
VITE_ENV=production
```

---

## 3. CẤU TRÚC THƯ MỤC

### 3.1 Mobile App

```
nutriday-mobile/
├── app.json
├── App.tsx                     ← Entry point
├── babel.config.js
├── tsconfig.json
├── .env / .env.staging / .env.production
│
├── assets/
│   ├── fonts/                  ← SVN-Gilroy, BeVietnamPro
│   ├── images/                 ← App images
│   └── icons/                  ← Custom SVG icons
│
└── src/
    ├── api/                    ← Axios instance + module files
    │   ├── client.ts
    │   ├── auth.api.ts
    │   ├── user.api.ts
    │   ├── dish.api.ts
    │   ├── mealPlan.api.ts
    │   ├── shoppingList.api.ts
    │   ├── search.api.ts
    │   ├── favorites.api.ts
    │   └── notification.api.ts
    │
    ├── components/
    │   ├── atoms/              ← Button, Input, Badge, Chip...
    │   ├── molecules/          ← MealCard, IngredientRow...
    │   └── organisms/          ← BottomTabBar, PageHeader...
    │
    ├── screens/
    │   ├── auth/               ← SCR-001 to SCR-006
    │   ├── onboarding/         ← SCR-007 to SCR-009
    │   ├── home/               ← SCR-010 to SCR-011
    │   ├── dish/               ← SCR-012 to SCR-014
    │   ├── weekly/             ← SCR-015 to SCR-017
    │   ├── search/             ← SCR-018 to SCR-020
    │   └── profile/            ← SCR-021 to SCR-026
    │
    ├── navigation/
    │   ├── RootNavigator.tsx
    │   ├── AuthNavigator.tsx
    │   ├── OnboardingNavigator.tsx
    │   └── MainTabNavigator.tsx
    │
    ├── stores/                 ← Zustand stores
    │   ├── auth.store.ts
    │   ├── onboarding.store.ts
    │   └── settings.store.ts
    │
    ├── hooks/                  ← React Query custom hooks
    │   ├── useAuth.ts
    │   ├── useDishes.ts
    │   ├── useMealPlan.ts
    │   ├── useShoppingList.ts
    │   └── useFavorites.ts
    │
    ├── utils/
    │   ├── date.utils.ts       ← date-fns formatters
    │   ├── number.utils.ts     ← VND formatter
    │   └── nutrition.utils.ts  ← Macro calculators
    │
    ├── constants/
    │   ├── colors.ts
    │   ├── typography.ts
    │   ├── spacing.ts
    │   └── queryKeys.ts
    │
    ├── types/
    │   ├── api.types.ts        ← API response interfaces
    │   ├── dish.types.ts
    │   ├── user.types.ts
    │   └── mealPlan.types.ts
    │
    └── i18n/
        ├── index.ts
        └── vi.json             ← Vietnamese strings
```

### 3.2 Admin Web

```
nutriday-admin/
├── index.html
├── vite.config.ts
├── tsconfig.json
├── tailwind.config.ts
│
└── src/
    ├── api/
    │   ├── client.ts
    │   └── admin/
    │       ├── dashboard.api.ts
    │       ├── dish.api.ts
    │       ├── review.api.ts
    │       ├── user.api.ts
    │       └── report.api.ts
    │
    ├── components/
    │   ├── common/             ← Shared UI components
    │   │   ├── DataTable/
    │   │   ├── Modal/
    │   │   ├── Pagination/
    │   │   └── StatusBadge/
    │   ├── layout/
    │   │   ├── AdminLayout.tsx
    │   │   ├── Sidebar.tsx
    │   │   └── Header.tsx
    │   └── features/
    │       ├── DishForm/
    │       ├── ImportPanel/
    │       └── ReviewPanel/
    │
    ├── pages/
    │   ├── dashboard/
    │   ├── dishes/
    │   ├── reviews/
    │   ├── reports/
    │   └── users/
    │
    ├── router/
    │   └── index.tsx
    │
    ├── stores/
    │   └── auth.store.ts
    │
    ├── hooks/
    └── utils/
```

---

## 4. BUILD CONFIG

### 4.1 Mobile — Expo Build

```json
// eas.json
{
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "staging": {
      "distribution": "internal",
      "ios": { "simulator": false },
      "env": { "EXPO_PUBLIC_ENV": "staging" }
    },
    "production": {
      "autoIncrement": true
    }
  }
}
```

### 4.2 Admin Web — Vite

```typescript
// vite.config.ts
export default defineConfig({
  plugins: [react()],
  build: {
    target: 'es2020',
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
          ui: ['@radix-ui/react-dialog', '@radix-ui/react-dropdown-menu'],
          charts: ['recharts'],
        }
      }
    }
  }
})
```

---

**Trang tiếp theo:** [01 — Design System →](FED-ND-2025-001_01_DesignSystem.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
