# FED-ND-2025-001_03 — Navigation Architecture
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 03 — Navigation |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## 1. MOBILE — NAVIGATOR HIERARCHY

```
RootNavigator (NavigationContainer)
│
├── [isAuthenticated = false]
│   └── AuthNavigator (Stack)
│       ├── SplashScreen          — SCR-001
│       ├── WelcomeScreen         — SCR-002
│       ├── RegisterScreen        — SCR-003
│       ├── OtpVerifyScreen       — SCR-004
│       ├── LoginScreen           — SCR-005
│       └── ForgotPasswordScreen  — SCR-006
│
├── [isAuthenticated = true, onboardingCompleted = false]
│   └── OnboardingNavigator (Stack)
│       ├── GoalSelectionScreen   — SCR-007
│       ├── ServingsScreen        — SCR-008
│       └── AllergiesScreen       — SCR-009
│
└── [isAuthenticated = true, onboardingCompleted = true]
    └── MainTabNavigator (Bottom Tab)
        │
        ├── Tab: Home (House icon)
        │   └── HomeStack (Stack)
        │       ├── HomeScreen              — SCR-010
        │       ├── DishDetailScreen        — SCR-012
        │       ├── RecipeScreen            — SCR-013
        │       └── SubstitutionScreen      — SCR-014
        │
        ├── Tab: Weekly (CalendarBlank icon)
        │   └── WeeklyStack (Stack)
        │       ├── WeeklyPlanScreen        — SCR-015
        │       ├── DailyShoppingScreen     — SCR-016
        │       └── WeeklyShoppingScreen    — SCR-017
        │
        ├── Tab: Search (MagnifyingGlass icon)
        │   └── SearchStack (Stack)
        │       ├── SearchScreen            — SCR-018
        │       ├── IngredientSearchScreen  — SCR-019
        │       └── ExploreScreen           — SCR-020
        │
        └── Tab: Profile (UserCircle icon)
            └── ProfileStack (Stack)
                ├── FavoritesScreen         — SCR-021
                ├── ProfileScreen           — SCR-022
                ├── EditProfileScreen       — SCR-023
                ├── MealHistoryScreen       — SCR-024
                ├── SettingsScreen          — SCR-025
                └── NotificationScreen      — SCR-026
```

---

## 2. NAVIGATOR IMPLEMENTATIONS

### 2.1 RootNavigator

```typescript
// src/navigation/RootNavigator.tsx
export const RootNavigator = () => {
  const { isAuthenticated, user } = useAuthStore();
  const onboardingCompleted = user?.onboardingCompleted ?? false;

  return (
    <NavigationContainer ref={navigationRef} theme={NutriDayTheme}>
      {!isAuthenticated
        ? <AuthNavigator />
        : !onboardingCompleted
          ? <OnboardingNavigator />
          : <MainTabNavigator />
      }
    </NavigationContainer>
  );
};
```

### 2.2 Bottom Tab Config

```typescript
// src/navigation/MainTabNavigator.tsx
const Tab = createBottomTabNavigator<MainTabParamList>();

export const MainTabNavigator = () => (
  <Tab.Navigator
    screenOptions={{ headerShown: false }}
    tabBar={(props) => <BottomTabBar {...props} />}
  >
    <Tab.Screen name="Home"    component={HomeStack}    />
    <Tab.Screen name="Weekly"  component={WeeklyStack}  />
    <Tab.Screen name="Search"  component={SearchStack}  />
    <Tab.Screen name="Profile" component={ProfileStack} />
  </Tab.Navigator>
);
```

### 2.3 Stack Transition Config

```typescript
export const stackScreenOptions: StackNavigationOptions = {
  headerShown: false,
  animation: 'slide_from_right',
  gestureEnabled: true,
  gestureDirection: 'horizontal',
};

// Modal screens (bottom sheet style)
export const modalScreenOptions: StackNavigationOptions = {
  headerShown: false,
  animation: 'slide_from_bottom',
  presentation: 'modal',
};
```

---

## 3. TYPE-SAFE NAVIGATION

```typescript
// src/navigation/types.ts
export type AuthStackParamList = {
  Splash: undefined;
  Welcome: undefined;
  Register: undefined;
  OtpVerify: { userId: string; phone: string };
  Login: undefined;
  ForgotPassword: undefined;
};

export type MainTabParamList = {
  Home: undefined;
  Weekly: undefined;
  Search: undefined;
  Profile: undefined;
};

export type HomeStackParamList = {
  HomeMain: undefined;
  DishDetail: { dishId: string; fromMealType?: MealType };
  Recipe: { dishId: string; dishName: string };
  Substitution: { dishId: string };
};

// Usage in screen:
const navigation = useNavigation<NativeStackNavigationProp<HomeStackParamList>>();
navigation.navigate('DishDetail', { dishId: 'dish_001' });

// Usage with useRoute:
const { params } = useRoute<RouteProp<HomeStackParamList, 'DishDetail'>>();
```

---

## 4. DEEP LINKS

```typescript
// app.json
{
  "expo": {
    "scheme": "nutriday",
    "intentFilters": [{
      "action": "VIEW",
      "data": [{ "scheme": "https", "host": "nutriday.vn", "pathPrefix": "/dish" }]
    }]
  }
}

// Linking config
const linking: LinkingOptions<RootParamList> = {
  prefixes: ['nutriday://', 'https://nutriday.vn'],
  config: {
    screens: {
      Main: {
        screens: {
          Home: {
            screens: {
              DishDetail: 'dish/:dishId',
            }
          }
        }
      }
    }
  }
};
```

---

## 5. ADMIN WEB — ROUTES

```typescript
// src/router/index.tsx
export const router = createBrowserRouter([
  {
    path: '/admin',
    element: <AdminLayout />,
    loader: requireAdminAuth,
    children: [
      { index: true,         element: <Navigate to="/admin/dashboard" replace /> },
      { path: 'dashboard',   element: <DashboardPage /> },
      { path: 'dishes',      element: <DishListPage /> },
      { path: 'dishes/new',  element: <DishFormPage mode="create" /> },
      { path: 'dishes/:id/edit', element: <DishFormPage mode="edit" /> },
      { path: 'dishes/import',   element: <ImportPage /> },
      { path: 'reviews',     element: <ReviewQueuePage /> },
      { path: 'reports',     element: <ReportsPage /> },
      { path: 'users',       element: <UserListPage /> },
    ],
  },
  { path: '/admin/login', element: <AdminLoginPage /> },
  { path: '*',            element: <Navigate to="/admin/login" replace /> },
]);
```

### Route Guards

```typescript
// src/router/guards.ts
export const requireAdminAuth: LoaderFunction = async () => {
  const token = useAdminAuthStore.getState().accessToken;
  if (!token) throw redirect('/admin/login');
  return null;
};

export const requireRole = (roles: AdminRole[]): LoaderFunction => async () => {
  const { admin } = useAdminAuthStore.getState();
  if (!admin || !roles.includes(admin.role)) throw redirect('/admin/dashboard');
  return null;
};
```

---

**Trang trước:** [02 — State Management ←](FED-ND-2025-001_02_StateManagement.md)
**Trang tiếp theo:** [04 — Components Atoms →](FED-ND-2025-001_04_Components_Atoms.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
