# FED-ND-2025-001_05 — Components — Molecules
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 05 — Components Molecules |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## 1. MEAL CARD

```typescript
// src/components/molecules/MealCard/MealCard.tsx
interface MealCardProps {
  dish: {
    dishId: string;
    name: string;
    image: string;
    calories: number;
    cookingTime: number;             // phút
    isNutritionistVerified: boolean;
    isFavorited: boolean;
  };
  mealType: 'breakfast' | 'lunch' | 'dinner';
  onPress: () => void;
  onFavorite: () => void;
  onSwap: () => void;
}
```

### Layout

```
┌─────────────────────────────────────────────┐
│  ┌──────────┐  Phở Bò                 ♡    │
│  │          │  ┌────────┐ ┌──────────┐      │
│  │  Image   │  │450 kcal│ │ 45 phút  │      │
│  │  120×120 │  └────────┘ └──────────┘      │
│  │          │  ✓ Chuyên gia xác minh        │
│  └──────────┘                    [Đổi món]  │
└─────────────────────────────────────────────┘
```

### Features
- Image: 120×120, borderRadius 12, placeholder shimmer
- Verified badge: `<Badge label="Đã xác minh" variant="success" />` khi `isNutritionistVerified=true`
- Heart icon: filled (red) khi `isFavorited=true`, outline khi false
- Swap button: outline variant, icon `ArrowsClockwise`
- Meal type header: "Bữa sáng" / "Bữa trưa" / "Bữa tối" với icon tương ứng
- testID: `meal-card`, `verified-badge`, `favorite-btn`, `swap-btn`

---

## 2. NUTRITION BAR

```typescript
// src/components/molecules/NutritionBar/NutritionBar.tsx
interface NutritionBarProps {
  calories: number;
  protein: number;      // gam
  carbs: number;        // gam
  fat: number;          // gam
  target?: {            // Mục tiêu (nếu có)
    calories: number;
    protein: number;
    carbs: number;
    fat: number;
  };
  compact?: boolean;    // Compact mode cho MealCard
}
```

### Layout (Full)

```
Tổng: 1,450 / 2,000 kcal
──────────────────────── 100%

Protein    65g          ████████████░░░░  72%
Carbs     180g          ██████████████░░  85%
Fat        48g          █████████░░░░░░░  60%
```

### Colors
- Protein: `#4CAF50` (green)
- Carbs: `#FF9800` (orange)
- Fat: `#2196F3` (blue)
- Background track: `#F0F0F0`

### Calculation
```typescript
// % = (macro_grams * calories_per_gram) / total_calories * 100
// protein: 4 kcal/g, carbs: 4 kcal/g, fat: 9 kcal/g
// Bar width capped at 100%
```

---

## 3. INGREDIENT ROW

```typescript
// src/components/molecules/IngredientRow/IngredientRow.tsx
interface IngredientRowProps {
  name: string;
  quantity: number;
  unit: string;
  category: 'protein' | 'vegetable' | 'grain' | 'spice' | 'dairy' | 'other';
  isChecked?: boolean;       // Shopping list mode
  onCheck?: () => void;
  substitution?: string;     // Nguyên liệu thay thế
  onSubstitute?: () => void;
}
```

### Layout

```
☐  Thịt bò          200g     [Đổi]
☑  Bánh phở          150g
☐  Hành lá           2 cọng
```

### Features
- Checkbox animation (scale spring)
- Strikethrough text khi checked
- Category icon/color coding
- Substitution button hiện khi có `substitution` prop

---

## 4. RECIPE STEP

```typescript
// src/components/molecules/RecipeStep/RecipeStep.tsx
interface RecipeStepProps {
  stepNumber: number;
  instruction: string;
  duration?: number;         // phút (optional)
  image?: string;            // Step image (optional)
}
```

### Layout

```
┌──┐
│ 1│  Luộc bánh phở trong nước sôi 2 phút,
└──┘  vớt ra ngâm nước lạnh.          ⏱ 2 phút
```

---

## 5. SEARCH BAR

```typescript
// src/components/molecules/SearchBar/SearchBar.tsx
interface SearchBarProps {
  value: string;
  onChangeText: (text: string) => void;
  placeholder?: string;
  onSubmit?: () => void;
  onClear?: () => void;
  autoFocus?: boolean;
  showFilter?: boolean;
  onFilterPress?: () => void;
}

// Features:
// - MagnifyingGlass icon left
// - Clear (X) button khi có text
// - Filter icon right (optional)
// - Debounce 300ms built-in cho autocomplete
```

---

## 6. MEAL TYPE SELECTOR

```typescript
// src/components/molecules/MealTypeSelector/MealTypeSelector.tsx
interface MealTypeSelectorProps {
  selected: 'breakfast' | 'lunch' | 'dinner';
  onChange: (type: MealType) => void;
}

// Layout: 3 horizontal chips
// ┌──────────┐  ┌──────────┐  ┌──────────┐
// │ 🌅 Sáng  │  │ ☀️ Trưa  │  │ 🌙 Tối  │
// └──────────┘  └──────────┘  └──────────┘
//   (selected)
```

---

## 7. EMPTY STATE

```typescript
// src/components/molecules/EmptyState/EmptyState.tsx
interface EmptyStateProps {
  illustration: 'no-plan' | 'no-favorites' | 'no-results' | 'no-shopping' | 'error';
  title: string;
  description?: string;
  actionLabel?: string;
  onAction?: () => void;
}

// Usage:
// <EmptyState
//   illustration="no-plan"
//   title="Chưa có thực đơn"
//   description="Tạo thực đơn để bắt đầu"
//   actionLabel="Tạo thực đơn"
//   onAction={onGenerate}
// />
```

---

## 8. STAT CARD

```typescript
// src/components/molecules/StatCard/StatCard.tsx
interface StatCardProps {
  label: string;
  value: string | number;
  icon: React.ReactNode;
  color: string;
  trend?: { value: number; direction: 'up' | 'down' };  // Admin dashboard
}

// Usage: Admin dashboard stat cards
// <StatCard label="Tổng món" value={347} icon={<ForkKnife />} color="#2E7D52" />
```

---

**Trang trước:** [04 — Components Atoms ←](FED-ND-2025-001_04_Components_Atoms.md)
**Trang tiếp theo:** [06 — Organisms & Admin Components →](FED-ND-2025-001_06_Components_Organisms_Admin.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
