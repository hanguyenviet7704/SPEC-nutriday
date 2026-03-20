# SAD-ND-2025-001_03 — Frontend Architecture
# NutriDay System Architecture

---

| | |
|---|---|
| **Module** | 03 — Frontend Architecture |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](SAD-ND-2025-001_INDEX.md) |

---

## 1. MOBILE — REACT NATIVE (EXPO)

### 1.1 Tech Stack

| Layer | Công nghệ | Phiên bản |
|---|---|---|
| Framework | React Native (Expo bare workflow) | SDK 50 |
| Language | TypeScript | 5.3+ |
| Navigation | React Navigation v6 | 6.x |
| State (global) | Zustand | 4.x |
| State (server) | TanStack React Query | 5.x |
| HTTP Client | Axios | 1.6+ |
| Forms | React Hook Form + Zod | 7.x + 3.x |
| Icons | Phosphor React Native | 1.x |
| Storage | AsyncStorage + expo-secure-store | — |
| Animations | react-native-reanimated | 3.x |
| Charts | react-native-svg + victory-native | — |
| Push Notifications | expo-notifications + FCM | — |

### 1.2 Directory Structure

```
mobile/
├── app.json
├── babel.config.js
├── tsconfig.json
├── src/
│   ├── api/                  # Axios client, API functions
│   │   ├── client.ts         # Axios instance + interceptors
│   │   ├── auth.api.ts
│   │   ├── dishes.api.ts
│   │   ├── meal-plans.api.ts
│   │   ├── shopping.api.ts
│   │   ├── search.api.ts
│   │   ├── favorites.api.ts
│   │   └── notifications.api.ts
│   │
│   ├── components/
│   │   ├── atoms/            # Button, TextInput, Badge, Chip, Avatar
│   │   ├── molecules/        # MealCard, NutritionBar, IngredientRow
│   │   └── organisms/        # BottomTabBar, PageHeader, MealSection
│   │
│   ├── constants/
│   │   ├── colors.ts         # Design system colors
│   │   ├── typography.ts     # Font sizes, weights
│   │   ├── spacing.ts        # 4px grid
│   │   ├── queryKeys.ts      # React Query keys
│   │   └── enums.ts          # DietGoal, MealType, AllergenCode
│   │
│   ├── hooks/                # Custom hooks (useTodayMealPlan, useSwapMeal, etc.)
│   ├── navigation/           # RootNavigator, AuthStack, MainTabs
│   ├── screens/              # Screen components grouped by flow
│   ├── stores/               # Zustand stores
│   ├── types/                # TypeScript interfaces
│   └── utils/                # Helpers, formatters, validators
│
├── assets/                   # Fonts, static images
└── test/                     # Test setup, fixtures
```

### 1.3 Rendering Strategy

| Loại dữ liệu | Strategy | Lý do |
|---|---|---|
| Meal plan hôm nay | React Query + staleTime 2min | Cần fresh, nhưng không cần realtime |
| Dish list (paginated) | React Query + infinite scroll | Lazy load khi scroll |
| User profile | Zustand (persisted) + React Query | Offline-first, sync khi online |
| Shopping list | React Query + optimistic update | Check/uncheck cần instant feedback |
| Search results | React Query + debounce 300ms | Tránh spam API |
| Favorites | React Query + optimistic toggle | Heart icon cần instant feedback |

---

## 2. ADMIN WEB — REACTJS

### 2.1 Tech Stack

| Layer | Công nghệ | Phiên bản |
|---|---|---|
| Framework | React | 18.x |
| Build tool | Vite | 5.x |
| Language | TypeScript | 5.3+ |
| Routing | React Router DOM v6 | 6.x |
| State (global) | Zustand | 4.x |
| State (server) | TanStack React Query | 5.x |
| UI Components | shadcn/ui (Radix + Tailwind) | — |
| Table | TanStack Table | 8.x |
| Forms | React Hook Form + Zod | 7.x + 3.x |
| Charts | Recharts | 2.x |
| Excel | SheetJS (xlsx) | — |
| Rich Text | Tiptap | 2.x |

### 2.2 Directory Structure

```
admin/
├── index.html
├── vite.config.ts
├── tailwind.config.ts
├── src/
│   ├── api/                  # Axios client, API functions
│   ├── components/
│   │   ├── ui/               # shadcn/ui components
│   │   ├── layout/           # AdminLayout, Sidebar, Header
│   │   ├── dishes/           # DishForm, DishTable, NutritionForm
│   │   ├── reviews/          # ReviewQueue, ReviewDetail
│   │   ├── import/           # ImportWizard, ValidationResults
│   │   └── reports/          # Charts, StatCards
│   │
│   ├── hooks/                # Custom hooks
│   ├── pages/                # Route-level components
│   ├── router/               # Route definitions, guards
│   ├── stores/               # Zustand (adminAuth, ui)
│   ├── types/                # TypeScript interfaces
│   └── utils/
└── test/
```

---

## 3. SHARED PATTERNS

### 3.1 API Response Handling

```typescript
// Cả mobile và admin đều dùng cùng pattern:
// 1. Axios interceptor unwrap response.data
// 2. React Query handle loading/error states
// 3. Error boundary cho unexpected errors
// 4. Toast notifications cho mutation errors

// Type-safe API response:
interface ApiResponse<T> {
  success: boolean;
  data: T;
  timestamp: string;
}

interface ApiError {
  success: false;
  error: { code: string; message: string };
  timestamp: string;
}

interface PaginatedResponse<T> extends ApiResponse<T[]> {
  meta: { total: number; page: number; limit: number; totalPages: number };
}
```

### 3.2 Offline Support (Mobile)

```
┌──────────────┐    Online     ┌──────────┐
│  React Query │ ────────────→ │   API    │
│  Cache       │ ←──────────── │  Server  │
└──────┬───────┘               └──────────┘
       │
       │ Offline
       ▼
┌──────────────┐
│ AsyncStorage │  ← Persisted query cache
│ SecureStore  │  ← Auth tokens
└──────────────┘

// NetInfo listener toggles online mode
// Mutations queue offline → replay on reconnect
```

---

**Trang trước:** [02 — Backend Architecture ←](SAD-ND-2025-001_02_Backend.md)
**Trang tiếp theo:** [04 — Security →](SAD-ND-2025-001_04_Security.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
