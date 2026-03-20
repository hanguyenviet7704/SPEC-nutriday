# FED-ND-2025-001_06 — Components — Organisms & Admin
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 06 — Organisms & Admin Components |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## 1. MOBILE ORGANISMS

### 1.1 Bottom Tab Bar

```typescript
// src/components/organisms/BottomTabBar/BottomTabBar.tsx
interface BottomTabBarProps extends BottomTabBarProps {
  // Custom bottom tab bar with:
  // - 4 tabs: Home, Weekly, Search, Profile
  // - Icons: House, CalendarBlank, MagnifyingGlass, UserCircle (Phosphor)
  // - Active: icon filled + primary color + label
  // - Inactive: icon regular + gray
  // - Badge on Profile tab (notification count)
  // - Safe area padding (bottom inset)
  // - Height: 60 + safeArea
  // - Shadow: elevation 8
}
```

### 1.2 Page Header

```typescript
// src/components/organisms/PageHeader/PageHeader.tsx
interface PageHeaderProps {
  title: string;
  showBack?: boolean;
  onBack?: () => void;
  rightAction?: React.ReactNode;    // IconButton or menu
  subtitle?: string;
  transparent?: boolean;             // Overlay on image
}

// Variants:
// - Default: white bg, title center, back arrow left
// - Transparent: for DishDetail (overlays on hero image)
// - With subtitle: smaller text below title
```

### 1.3 Meal Section

```typescript
// src/components/organisms/MealSection/MealSection.tsx
interface MealSectionProps {
  mealType: 'breakfast' | 'lunch' | 'dinner';
  dish: DishSummary;
  wasSwapped: boolean;
  onPress: () => void;
  onFavorite: () => void;
  onSwap: () => void;
}

// Layout:
// ┌─────────────────────────────────────┐
// │  ☀️ Bữa trưa              11:30    │
// │  ┌───────────────────────────────┐  │
// │  │         MealCard              │  │
// │  └───────────────────────────────┘  │
// │  Calories: 450 kcal                 │
// └─────────────────────────────────────┘

// wasSwapped=true → hiện icon "Đã đổi" badge
```

### 1.4 Weekly Calendar Strip

```typescript
// src/components/organisms/WeeklyCalendar/WeeklyCalendar.tsx
interface WeeklyCalendarProps {
  selectedDate: string;
  onSelectDate: (date: string) => void;
  weekStart: string;            // ISO date
  markedDates?: string[];       // Dates with meal plans
}

// Layout:
// ◀  T2   T3   T4   T5   T6   T7   CN  ▶
//         [16] (17)  18   19   20   21
//        today selected

// Selected: circle bg primary
// Today: dot below
// Has plan: small green dot
```

### 1.5 Shopping Category Section

```typescript
// src/components/organisms/ShoppingCategory/ShoppingCategory.tsx
interface ShoppingCategoryProps {
  category: string;             // "Thịt & Hải sản", "Rau củ", etc.
  items: ShoppingItem[];
  onCheckItem: (itemId: string) => void;
  collapsible?: boolean;
}

// Layout:
// ▼ Thịt & Hải sản (2/5)
//   ☐ Thịt bò    200g
//   ☑ Tôm        300g   ← strikethrough
//   ☐ Cá hồi     150g
```

---

## 2. ADMIN WEB COMPONENTS

### 2.1 Admin Layout

```typescript
// src/components/layout/AdminLayout.tsx
// ┌──────┬─────────────────────────────────────┐
// │      │  Header: breadcrumb + admin avatar   │
// │ Side │───────────────────────────────────────│
// │ bar  │                                       │
// │      │  Main Content Area                    │
// │ 240px│  (Outlet)                             │
// │      │                                       │
// │      │                                       │
// └──────┴─────────────────────────────────────┘

// Sidebar items:
// - Dashboard (ChartBar)
// - Quản lý món ăn (ForkKnife)
//   - Danh sách
//   - Thêm mới
//   - Import
// - Duyệt món (ClipboardCheck)
// - Báo cáo (ChartLine)
// - Người dùng (Users) — super_admin only
// - Cài đặt (Gear) — super_admin only

// Responsive: sidebar collapse → hamburger menu < 1024px
```

### 2.2 Data Table (TanStack Table)

```typescript
// src/components/dishes/DishTable.tsx
// Features:
// - Server-side pagination, sorting, filtering
// - Column: Image, Tên món, Diet goals, Status, Calories, Verified, Actions
// - Status filter: draft, pending_review, published, rejected
// - Bulk actions: publish, delete (super_admin)
// - Row click → navigate to edit page
// - Export to CSV

const columns: ColumnDef<DishRow>[] = [
  { accessorKey: 'image', header: '', cell: ImageCell, size: 60 },
  { accessorKey: 'name', header: 'Tên món', enableSorting: true },
  { accessorKey: 'dietGoals', header: 'Chế độ', cell: ChipListCell },
  { accessorKey: 'status', header: 'Trạng thái', cell: StatusBadgeCell, enableColumnFilter: true },
  { accessorKey: 'calories', header: 'Kcal', enableSorting: true },
  { accessorKey: 'isVerified', header: 'Xác minh', cell: VerifiedCell },
  { id: 'actions', header: '', cell: ActionsCell },
];
```

### 2.3 Dish Form

```typescript
// src/components/dishes/DishForm.tsx
// Tabs: Thông tin cơ bản | Dinh dưỡng | Nguyên liệu | Công thức | Ảnh

// Tab 1 — Thông tin cơ bản:
// - name (required), description (Tiptap rich text)
// - diet_goals (multi-select chips), meal_types (multi-select)
// - cooking_time_minutes, difficulty_level, servings
// - allergens (multi-select)

// Tab 2 — Dinh dưỡng:
// - calories, protein, carbs, fat, fiber, sodium
// - Auto-calculate from ingredients (optional)

// Tab 3 — Nguyên liệu:
// - Dynamic list: ingredient (autocomplete) + quantity + unit
// - Add/remove rows
// - Substitution suggestions

// Tab 4 — Công thức:
// - Sortable step list (drag & drop)
// - Each step: instruction (textarea) + duration (optional) + image (optional)

// Tab 5 — Ảnh:
// - Hero image upload (required)
// - Gallery images (up to 5)
// - Drag to reorder, crop preview

// Form validation: Zod schema, real-time errors
// Auto-save draft every 30 seconds
```

### 2.4 Review Queue

```typescript
// src/components/reviews/ReviewQueue.tsx
// Layout:
// ┌─────────────────────────────────────────────┐
// │  Pending Reviews (12)          Filter ▼     │
// │───────────────────────────────────────────── │
// │  ┌─────────────────────────────────────────┐│
// │  │ Phở Bò  │ by Editor A │ 2 ngày trước   ││
// │  │ weight_loss, healthy │ 450 kcal         ││
// │  │ [Xem chi tiết]  [Duyệt ✓]  [Từ chối ✗]││
// │  └─────────────────────────────────────────┘│
// │  ...                                        │
// └─────────────────────────────────────────────┘

// Review detail modal:
// - Full dish info, nutrition, ingredients
// - Nutrition comparison with recommended ranges
// - Comment textarea (required for rejection)
// - Approve → status=published, is_nutritionist_verified=true
// - Reject → status=rejected, review comment saved
```

### 2.5 Import Wizard

```typescript
// src/components/import/ImportWizard.tsx
// Step 1: Upload .xlsx file
// Step 2: Column mapping (auto-detect + manual override)
// Step 3: Validation results
//   - ✓ 47 rows valid
//   - ✗ 3 rows with errors (show details)
// Step 4: Confirm import
// Step 5: Results summary

// Validation rules:
// - Required fields: name, calories, protein, carbs, fat
// - Range checks: calories 50-2000, cooking_time 5-180
// - Duplicate check: name + diet_goal combination
// - Allergen codes must match ENUM list
```

---

**Trang trước:** [05 — Components Molecules ←](FED-ND-2025-001_05_Components_Molecules.md)
**Trang tiếp theo:** [07 — Screens Auth & Onboarding →](FED-ND-2025-001_07_Screens_Auth_Onboarding.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
