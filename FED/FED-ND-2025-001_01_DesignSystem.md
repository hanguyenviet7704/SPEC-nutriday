# FED-ND-2025-001_01 — Design System
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 01 — Design System |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## 1. COLOR PALETTE

```typescript
// src/constants/colors.ts
export const colors = {
  // ─── Primary Green ───────────────────────────────
  primary: {
    50:  '#E8F5EE',
    100: '#C6E6D3',
    200: '#9FD5B5',
    300: '#77C496',
    400: '#58B87E',
    500: '#3AAB66',
    600: '#2E7D52',   // ← Main brand — buttons, active states
    700: '#256640',
    800: '#1C4F30',
    900: '#123820',
  },
  // ─── Accent Orange ───────────────────────────────
  accent: {
    50:  '#FDF0E8',
    100: '#FAD9C4',
    200: '#F6BF9B',
    300: '#F2A472',
    400: '#EF8F53',
    500: '#E8651A',   // ← Main accent — highlights, badges
    600: '#D4560F',
    700: '#B8490D',
    800: '#9C3D0B',
    900: '#7A2F08',
  },
  // ─── Neutrals ────────────────────────────────────
  neutral: {
    white:  '#FFFFFF',
    cream:  '#FBF8F4',   // ← App background
    50:  '#F9FAFB',
    100: '#F3F4F6',
    200: '#E5E7EB',      // ← Dividers, borders
    300: '#D1D5DB',
    400: '#9CA3AF',      // ← Placeholder text
    500: '#6B7280',      // ← Secondary text
    600: '#4B5563',
    700: '#374151',
    800: '#1F2937',      // ← Primary text
    900: '#111827',
  },
  // ─── Semantic ────────────────────────────────────
  success: '#16A34A',
  warning: '#D97706',
  error:   '#DC2626',
  info:    '#2563EB',
  // ─── Nutrition Macros ────────────────────────────
  macro: {
    protein: '#2E7D52',   // Green
    carbs:   '#E8651A',   // Orange
    fat:     '#F59E0B',   // Amber
    fiber:   '#8B5CF6',   // Purple
  },
};
```

**Usage rules:**
| Element | Color |
|---|---|
| Primary button | `primary.600` |
| Active tab | `primary.600` |
| Secondary button border | `primary.600` |
| Alert / badge | `accent.500` |
| Background | `neutral.cream` |
| Card background | `neutral.white` |
| Body text | `neutral.800` |
| Placeholder | `neutral.400` |
| Divider | `neutral.200` |
| Error | `error` |

---

## 2. TYPOGRAPHY

```typescript
// src/constants/typography.ts
export const typography = {
  fontFamily: {
    // Headings — display, H1, H2, H3
    bold:       'SVN-Gilroy-Bold',
    semiBold:   'SVN-Gilroy-SemiBold',
    // Body — paragraphs, labels, captions
    regular:    'BeVietnamPro-Regular',
    medium:     'BeVietnamPro-Medium',
    bodyBold:   'BeVietnamPro-Bold',
  },
  fontSize: {
    display: 32,    // Hero text, splash
    h1:      24,    // Screen title
    h2:      20,    // Section header
    h3:      18,    // Card title
    bodyLg:  16,    // Primary body
    body:    14,    // Standard body (minimum readable)
    caption: 12,    // Secondary info
    overline: 10,   // Tiny labels
  },
  lineHeight: {
    tight:   1.2,
    normal:  1.5,
    relaxed: 1.75,
  },
  letterSpacing: {
    tight:  -0.5,
    normal: 0,
    wide:   0.5,
    wider:  1.0,
  },
};
```

**Predefined text styles:**
```typescript
export const textStyles = {
  display:     { fontFamily: typography.fontFamily.bold,     fontSize: 32, lineHeight: 38 },
  h1:          { fontFamily: typography.fontFamily.bold,     fontSize: 24, lineHeight: 29 },
  h2:          { fontFamily: typography.fontFamily.semiBold, fontSize: 20, lineHeight: 24 },
  h3:          { fontFamily: typography.fontFamily.semiBold, fontSize: 18, lineHeight: 22 },
  bodyLg:      { fontFamily: typography.fontFamily.regular,  fontSize: 16, lineHeight: 24 },
  body:        { fontFamily: typography.fontFamily.regular,  fontSize: 14, lineHeight: 21 },
  bodyMedium:  { fontFamily: typography.fontFamily.medium,   fontSize: 14, lineHeight: 21 },
  caption:     { fontFamily: typography.fontFamily.regular,  fontSize: 12, lineHeight: 18 },
  overline:    { fontFamily: typography.fontFamily.medium,   fontSize: 10, lineHeight: 16, letterSpacing: 1.0 },
  buttonLg:    { fontFamily: typography.fontFamily.semiBold, fontSize: 16, lineHeight: 20 },
  button:      { fontFamily: typography.fontFamily.semiBold, fontSize: 14, lineHeight: 18 },
};
```

---

## 3. SPACING SYSTEM

```typescript
// src/constants/spacing.ts
// Base unit: 4px
export const spacing = {
  xs:  4,   // 1 unit
  sm:  8,   // 2 units
  md:  12,  // 3 units
  base: 16, // 4 units — standard padding
  lg:  20,  // 5 units
  xl:  24,  // 6 units
  '2xl': 32,  // 8 units
  '3xl': 40,  // 10 units
  '4xl': 48,  // 12 units
  '5xl': 64,  // 16 units
};

export const layout = {
  screenHPadding: 16,   // Horizontal padding cho tất cả screens
  cardPadding:    16,
  sectionGap:     24,
  itemGap:        12,
};
```

---

## 4. BORDER RADIUS

```typescript
export const borderRadius = {
  xs:   4,
  sm:   8,
  md:   12,
  lg:   16,
  xl:   20,
  '2xl': 24,
  full: 9999,
};
```

---

## 5. SHADOWS (React Native)

```typescript
export const shadows = {
  none: {},
  sm: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 1 },
    shadowOpacity: 0.05,
    shadowRadius: 4,
    elevation: 1,
  },
  card: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.08,
    shadowRadius: 8,
    elevation: 3,
  },
  modal: {
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 8 },
    shadowOpacity: 0.15,
    shadowRadius: 24,
    elevation: 10,
  },
  button: {
    shadowColor: colors.primary[600],
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.3,
    shadowRadius: 8,
    elevation: 5,
  },
};
```

---

## 6. ANIMATIONS

### 6.1 Timing Config

```typescript
import { Easing } from 'react-native-reanimated';

export const animations = {
  duration: {
    fast:   150,
    normal: 250,
    slow:   400,
  },
  easing: {
    standard: Easing.bezier(0.4, 0, 0.2, 1),   // Material standard
    enter:    Easing.bezier(0, 0, 0.2, 1),      // Decelerate
    exit:     Easing.bezier(0.4, 0, 1, 1),      // Accelerate
    spring:   { damping: 18, stiffness: 200 },
  },
};
```

### 6.2 Animation Catalog

| Tên | Trigger | Mô tả |
|---|---|---|
| `favoritePulse` | Nhấn ♡ | Scale 1→1.3→1, fill màu primary |
| `mealSwap` | Đổi bữa | Slide out cũ + slide in mới (300ms) |
| `slotMachine` | Nút random | Roll effect, 3 vòng → dừng lại |
| `confetti` | Hoàn thành tuần | Confetti burst từ giữa màn hình |
| `checkBounce` | Check shopping item | Scale 1→1.2→1 (150ms) |
| `timerEnd` | Timer recipe step | Vibration + bounce scale |
| `cardEnter` | Load MealCard list | Staggered fade-in mỗi 50ms |
| `skeletonShimmer` | Loading state | Left-to-right shimmer gradient |
| `pageTransition` | Navigation | Slide right (stack) / fade (tab) |

---

## 7. ICON SYSTEM

```typescript
// Sử dụng Phosphor Icons
import { House, CalendarBlank, MagnifyingGlass, UserCircle } from 'phosphor-react-native';

// Standard sizes
export const iconSizes = {
  xs: 16,
  sm: 20,
  md: 24,   // Default
  lg: 32,
  xl: 40,
};

// Usage
<House size={24} color={colors.primary[600]} weight="fill" />
```

**Custom NutriDay icons** (SVG, trong `assets/icons/`):
| File | Dùng cho |
|---|---|
| `chopsticks.svg` | App logo mark |
| `bowl.svg` | Meal placeholder |
| `nutrition-ring.svg` | Daily nutrition summary |
| `diet-goal-*.svg` | 8 diet goal icons |
| `timer-cooking.svg` | Recipe timer |

---

## 8. COMPONENT TOKENS

```typescript
export const componentTokens = {
  button: {
    height: { sm: 36, md: 48, lg: 56 },
    paddingH: { sm: 16, md: 24, lg: 32 },
    borderRadius: 12,
    borderWidth: 1.5,
  },
  input: {
    height: 52,
    paddingH: 16,
    borderRadius: 12,
    borderWidth: 1.5,
    borderColor: {
      default: colors.neutral[200],
      focused: colors.primary[600],
      error:   colors.error,
    },
  },
  card: {
    borderRadius: 16,
    padding: 16,
    backgroundColor: colors.neutral.white,
  },
  bottomSheet: {
    borderTopLeftRadius: 24,
    borderTopRightRadius: 24,
    backgroundColor: colors.neutral.white,
  },
  bottomTabBar: {
    height: 64,
    backgroundColor: colors.neutral.white,
    borderTopColor: colors.neutral[200],
    borderTopWidth: 1,
  },
};
```

---

**Trang trước:** [00 — Architecture ←](FED-ND-2025-001_00_Architecture.md)
**Trang tiếp theo:** [02 — State Management →](FED-ND-2025-001_02_StateManagement.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
