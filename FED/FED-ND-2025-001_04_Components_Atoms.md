# FED-ND-2025-001_04 — Components — Atoms
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 04 — Components Atoms |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## 1. BUTTON

```typescript
// src/components/atoms/Button/Button.tsx
interface ButtonProps {
  label: string;
  onPress: () => void;
  variant?: 'primary' | 'secondary' | 'outline' | 'ghost' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  loading?: boolean;
  disabled?: boolean;
  icon?: React.ReactNode;
  iconPosition?: 'left' | 'right';
  fullWidth?: boolean;
}

// Style mapping:
const variants = {
  primary:   { bg: '#2E7D52', text: '#FFFFFF', border: 'transparent' },
  secondary: { bg: '#FF6B35', text: '#FFFFFF', border: 'transparent' },
  outline:   { bg: 'transparent', text: '#2E7D52', border: '#2E7D52' },
  ghost:     { bg: 'transparent', text: '#2E7D52', border: 'transparent' },
  danger:    { bg: '#D32F2F', text: '#FFFFFF', border: 'transparent' },
};

const sizes = {
  sm: { height: 32, paddingH: 12, fontSize: 13 },
  md: { height: 44, paddingH: 20, fontSize: 15 },
  lg: { height: 52, paddingH: 24, fontSize: 17 },
};

// testID: 'button-container', 'loading-indicator'
```

---

## 2. TEXT INPUT

```typescript
// src/components/atoms/TextInput/NutriTextInput.tsx
interface NutriTextInputProps {
  label: string;
  value: string;
  onChangeText: (text: string) => void;
  placeholder?: string;
  error?: string;
  secureTextEntry?: boolean;    // Password toggle
  keyboardType?: KeyboardTypeOptions;
  autoCapitalize?: 'none' | 'sentences' | 'words';
  maxLength?: number;
  leftIcon?: React.ReactNode;
  rightIcon?: React.ReactNode;
  multiline?: boolean;
  numberOfLines?: number;
}

// States:
// - Default: border #E0E0E0, label #757575
// - Focused: border #2E7D52, label #2E7D52
// - Error: border #D32F2F, label #D32F2F, error text below
// - Disabled: bg #F5F5F5, text #BDBDBD

// Password: eye icon toggle secureTextEntry
// testID: 'text-input', 'error-message', 'toggle-password'
```

---

## 3. BADGE

```typescript
// src/components/atoms/Badge/Badge.tsx
interface BadgeProps {
  label: string;
  variant?: 'success' | 'warning' | 'error' | 'info' | 'neutral';
  size?: 'sm' | 'md';
  icon?: React.ReactNode;
}

const badgeColors = {
  success: { bg: '#E8F5E9', text: '#2E7D32' },   // Verified, Published
  warning: { bg: '#FFF3E0', text: '#E65100' },   // Pending review
  error:   { bg: '#FFEBEE', text: '#C62828' },   // Rejected, Allergen
  info:    { bg: '#E3F2FD', text: '#1565C0' },   // Info badge
  neutral: { bg: '#F5F5F5', text: '#616161' },   // Default
};

// Usage: <Badge label="Đã xác minh" variant="success" icon={<CheckCircle />} />
```

---

## 4. CHIP

```typescript
// src/components/atoms/Chip/Chip.tsx
interface ChipProps {
  label: string;
  selected?: boolean;
  onPress?: () => void;
  icon?: React.ReactNode;
  removable?: boolean;
  onRemove?: () => void;
}

// Selected: bg #2E7D52, text white, border #2E7D52
// Unselected: bg transparent, text #424242, border #E0E0E0

// Usage: Diet goal selector, allergen picker, filter chips
// <Chip label="Giảm cân" selected={goal === 'weight_loss'} onPress={() => setGoal('weight_loss')} />
```

---

## 5. AVATAR

```typescript
// src/components/atoms/Avatar/Avatar.tsx
interface AvatarProps {
  source?: string;         // Image URL
  name?: string;           // Fallback: initials
  size?: 'xs' | 'sm' | 'md' | 'lg' | 'xl';
  badge?: 'online' | 'verified';
}

const avatarSizes = {
  xs: 24,
  sm: 32,
  md: 40,
  lg: 56,
  xl: 80,
};

// Fallback: 2 chữ cái đầu, background random từ palette
// <Avatar source={user.avatar} name={user.fullName} size="md" badge="verified" />
```

---

## 6. PROGRESS BAR

```typescript
// src/components/atoms/ProgressBar/ProgressBar.tsx
interface ProgressBarProps {
  progress: number;        // 0-100
  color?: string;          // Default: primary
  height?: number;         // Default: 8
  showLabel?: boolean;     // Hiển thị "65%"
  animated?: boolean;      // Reanimated animation
}

// Usage: Nutrition macro bars, onboarding progress
// <ProgressBar progress={65} color={colors.protein} showLabel />
```

---

## 7. ICON BUTTON

```typescript
// src/components/atoms/IconButton/IconButton.tsx
interface IconButtonProps {
  icon: React.ReactNode;   // Phosphor icon
  onPress: () => void;
  size?: 'sm' | 'md' | 'lg';
  variant?: 'filled' | 'outline' | 'ghost';
  color?: string;
  badge?: number;          // Notification count
  disabled?: boolean;
}

// Usage: Heart toggle, swap button, back button
// <IconButton icon={<Heart weight={isFav ? 'fill' : 'regular'} />} onPress={onFavorite} />
```

---

## 8. DIVIDER

```typescript
// src/components/atoms/Divider/Divider.tsx
interface DividerProps {
  orientation?: 'horizontal' | 'vertical';
  color?: string;          // Default: #E0E0E0
  spacing?: number;        // marginVertical (default: 16)
  label?: string;          // Center label: "── hoặc ──"
}
```

---

## 9. SKELETON

```typescript
// src/components/atoms/Skeleton/Skeleton.tsx
interface SkeletonProps {
  width: number | string;
  height: number;
  borderRadius?: number;
  variant?: 'text' | 'circular' | 'rectangular';
}

// Shimmer animation dùng react-native-reanimated
// Usage: Loading states cho MealCard, DishDetail, etc.
```

---

**Trang trước:** [03 — Navigation ←](FED-ND-2025-001_03_Navigation.md)
**Trang tiếp theo:** [05 — Components Molecules →](FED-ND-2025-001_05_Components_Molecules.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
