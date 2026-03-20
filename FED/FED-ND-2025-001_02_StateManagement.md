# FED-ND-2025-001_02 — State Management & API Layer
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 02 — State Management & API Layer |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## 1. ZUSTAND STORES (Global State)

### 1.1 Auth Store

```typescript
// src/stores/auth.store.ts
interface AuthState {
  accessToken: string | null;
  refreshToken: string | null;
  user: AuthUser | null;
  isAuthenticated: boolean;
  isLoading: boolean;
}

interface AuthActions {
  login: (credentials: LoginDto) => Promise<void>;
  logout: () => Promise<void>;
  refreshAccessToken: () => Promise<string>;
  setUser: (user: AuthUser) => void;
  clearAuth: () => void;
}

export const useAuthStore = create<AuthState & AuthActions>()(
  persist(
    (set, get) => ({
      accessToken: null,
      refreshToken: null,
      user: null,
      isAuthenticated: false,
      isLoading: false,

      login: async (credentials) => {
        set({ isLoading: true });
        const res = await authApi.login(credentials);
        await SecureStore.setItemAsync('refreshToken', res.refreshToken);
        set({ accessToken: res.accessToken, user: res.user, isAuthenticated: true, isLoading: false });
      },

      logout: async () => {
        await authApi.logout();
        await SecureStore.deleteItemAsync('refreshToken');
        set({ accessToken: null, refreshToken: null, user: null, isAuthenticated: false });
      },

      refreshAccessToken: async () => {
        const refreshToken = await SecureStore.getItemAsync('refreshToken');
        const res = await authApi.refresh({ refreshToken });
        set({ accessToken: res.accessToken });
        return res.accessToken;
      },
    }),
    {
      name: 'auth-storage',
      storage: createJSONStorage(() => AsyncStorage),
      partialize: (state) => ({ user: state.user }),  // Chỉ persist user info
    }
  )
);
```

### 1.2 Onboarding Store

```typescript
// src/stores/onboarding.store.ts
interface OnboardingState {
  dietGoal: DietGoal | null;
  servings: number;
  allergies: AllergenCode[];
  currentStep: 'goal' | 'servings' | 'allergies';
}

export const useOnboardingStore = create<OnboardingState & OnboardingActions>()((set) => ({
  dietGoal: null,
  servings: 1,
  allergies: [],
  currentStep: 'goal',

  setDietGoal: (goal) => set({ dietGoal: goal, currentStep: 'servings' }),
  setServings: (n) => set({ servings: n, currentStep: 'allergies' }),
  toggleAllergen: (code) => set((state) => ({
    allergies: state.allergies.includes(code)
      ? state.allergies.filter(a => a !== code)
      : [...state.allergies, code],
  })),
  reset: () => set({ dietGoal: null, servings: 1, allergies: [], currentStep: 'goal' }),
}));
```

### 1.3 Settings Store

```typescript
// src/stores/settings.store.ts
interface SettingsState {
  notificationSettings: NotificationSettings | null;
}

export const useSettingsStore = create<SettingsState & SettingsActions>()(
  persist((set) => ({
    notificationSettings: null,
    setNotificationSettings: (settings) => set({ notificationSettings: settings }),
  }), { name: 'settings-storage' })
);
```

---

## 2. REACT QUERY CONFIG

```typescript
// src/api/queryClient.ts
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 5 * 60 * 1000,     // 5 phút
      gcTime: 10 * 60 * 1000,       // 10 phút (formerly cacheTime)
      retry: 2,
      retryDelay: (attempt) => Math.min(1000 * 2 ** attempt, 10000),
      refetchOnWindowFocus: false,
      refetchOnMount: true,
    },
    mutations: {
      retry: 0,
      onError: (error) => {
        Toast.show({ type: 'error', text1: getErrorMessage(error) });
      },
    },
  },
});
```

---

## 3. QUERY KEYS

```typescript
// src/constants/queryKeys.ts
export const queryKeys = {
  // User
  user:           ()                    => ['user', 'me'] as const,
  mealHistory:    (filters)             => ['user', 'meal-history', filters] as const,

  // Dishes
  dishes:         (filters)             => ['dishes', filters] as const,
  dish:           (id: string)          => ['dishes', id] as const,
  substitutions:  (dishId: string)      => ['dishes', dishId, 'substitutions'] as const,

  // Meal Plans
  todayPlan:      ()                    => ['meal-plans', 'today'] as const,
  weeklyPlan:     (weekStart: string)   => ['meal-plans', 'weekly', weekStart] as const,
  swapSuggestions:(planId, mealType)    => ['meal-plans', planId, 'swap', mealType] as const,

  // Shopping
  shoppingList:   (id: string)          => ['shopping-lists', id] as const,

  // Search
  search:         (q: string, filters)  => ['search', q, filters] as const,
  autocomplete:   (q: string, type)     => ['autocomplete', q, type] as const,
  explore:        (filters)             => ['explore', filters] as const,

  // Favorites
  favorites:      (filters)             => ['favorites', filters] as const,

  // Notifications
  notifSettings:  ()                    => ['notifications', 'settings'] as const,

  // Ingredients
  ingredients:    (filters)             => ['ingredients', filters] as const,
};
```

---

## 4. AXIOS CLIENT

```typescript
// src/api/client.ts
const apiClient = axios.create({
  baseURL: process.env.EXPO_PUBLIC_API_URL,
  timeout: 15000,
  headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
});

// ─── Request interceptor ─────────────────────────────
apiClient.interceptors.request.use((config) => {
  const token = useAuthStore.getState().accessToken;
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

// ─── Response interceptor (auto-refresh) ─────────────
let isRefreshing = false;
let failedQueue: Array<{ resolve: Function; reject: Function }> = [];

apiClient.interceptors.response.use(
  (response) => response.data,  // Unwrap response.data
  async (error) => {
    const original = error.config;

    if (error.response?.status === 401 && !original._retry) {
      if (isRefreshing) {
        return new Promise((resolve, reject) => {
          failedQueue.push({ resolve, reject });
        }).then((token) => {
          original.headers.Authorization = `Bearer ${token}`;
          return apiClient(original);
        });
      }

      original._retry = true;
      isRefreshing = true;

      try {
        const newToken = await useAuthStore.getState().refreshAccessToken();
        failedQueue.forEach(({ resolve }) => resolve(newToken));
        failedQueue = [];
        original.headers.Authorization = `Bearer ${newToken}`;
        return apiClient(original);
      } catch (refreshError) {
        failedQueue.forEach(({ reject }) => reject(refreshError));
        failedQueue = [];
        useAuthStore.getState().clearAuth();
        // Navigate to Login
        navigationRef.current?.reset({ index: 0, routes: [{ name: 'Login' }] });
        return Promise.reject(refreshError);
      } finally {
        isRefreshing = false;
      }
    }

    return Promise.reject(transformApiError(error));
  }
);
```

---

## 5. CUSTOM HOOKS

### 5.1 useTodayMealPlan

```typescript
export const useTodayMealPlan = () => {
  const generatePlan = useGenerateMealPlan();

  return useQuery({
    queryKey: queryKeys.todayPlan(),
    queryFn: async () => {
      try {
        return await mealPlanApi.getToday();
      } catch (error) {
        if (error.code === 'PLAN_NOT_FOUND') {
          // Auto-generate nếu chưa có
          await generatePlan.mutateAsync({ date: today(), mode: 'single' });
          return mealPlanApi.getToday();
        }
        throw error;
      }
    },
    staleTime: 2 * 60 * 1000,
  });
};
```

### 5.2 useSwapMeal

```typescript
export const useSwapMeal = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: ({ planId, mealType, newDishId }: SwapMealDto) =>
      mealPlanApi.swap(planId, mealType, newDishId),

    onMutate: async ({ planId, mealType, newDishId }) => {
      // Optimistic update
      await queryClient.cancelQueries({ queryKey: queryKeys.todayPlan() });
      const previous = queryClient.getQueryData(queryKeys.todayPlan());
      queryClient.setQueryData(queryKeys.todayPlan(), (old: TodayPlan) => ({
        ...old,
        meals: old.meals.map(m => m.mealType === mealType ? { ...m, dish: { dishId: newDishId } } : m),
      }));
      return { previous };
    },

    onError: (_, __, context) => {
      queryClient.setQueryData(queryKeys.todayPlan(), context?.previous);
      Toast.show({ type: 'error', text1: 'Không thể đổi bữa. Thử lại!' });
    },

    onSettled: () => {
      queryClient.invalidateQueries({ queryKey: queryKeys.todayPlan() });
    },
  });
};
```

### 5.3 useToggleFavorite

```typescript
export const useToggleFavorite = () => {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: ({ dishId, isFavorited }: { dishId: string; isFavorited: boolean }) =>
      isFavorited ? favoritesApi.remove(dishId) : favoritesApi.add(dishId),

    onSuccess: (_, { dishId, isFavorited }) => {
      // Invalidate favorites list
      queryClient.invalidateQueries({ queryKey: queryKeys.favorites({}) });
      // Update dish detail cache
      queryClient.setQueryData(queryKeys.dish(dishId), (old: DishDetail) =>
        old ? { ...old, isFavorited: !isFavorited } : old
      );
    },
  });
};
```

---

**Trang trước:** [01 — Design System ←](FED-ND-2025-001_01_DesignSystem.md)
**Trang tiếp theo:** [03 — Navigation →](FED-ND-2025-001_03_Navigation.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
