# FED-ND-2025-001_09 — Non-Functional Requirements
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 09 — Non-Functional Requirements |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## 1. PERFORMANCE

### 1.1 Mobile Targets

| Metric | Target | Đo bằng |
|---|---|---|
| Cold start (splash → home) | < 3 giây | Flipper / Performance Monitor |
| Time to Interactive (TTI) | < 2 giây | React DevTools |
| Screen transition | < 300ms | react-native-reanimated |
| API response render | < 500ms | React Query devtools |
| JS bundle size | < 5 MB | Metro bundler |
| Image load (CDN) | < 1 giây | CloudFront metrics |
| Memory usage | < 200 MB | Xcode Instruments / Android Profiler |
| FPS (scroll/animation) | ≥ 55 FPS | Flipper |

### 1.2 Admin Web Targets

| Metric | Target | Đo bằng |
|---|---|---|
| First Contentful Paint | < 1.5s | Lighthouse |
| Largest Contentful Paint | < 2.5s | Lighthouse |
| Cumulative Layout Shift | < 0.1 | Lighthouse |
| Time to Interactive | < 3s | Lighthouse |
| Bundle size (gzipped) | < 300 KB | Vite build |

### 1.3 Optimization Strategies

```typescript
// Mobile:
// 1. Lazy load screens với React.lazy + Suspense
// 2. FlatList với getItemLayout, windowSize=5
// 3. Image caching: react-native-fast-image
// 4. Memoize: React.memo, useMemo, useCallback
// 5. Hermes engine (enabled by default in Expo SDK 50)

// Admin Web:
// 1. Route-based code splitting (React.lazy)
// 2. Tree shaking (Vite)
// 3. Virtual scrolling cho tables lớn (TanStack Virtual)
// 4. Image lazy loading (native loading="lazy")
```

---

## 2. ACCESSIBILITY

### 2.1 Standards

| Tiêu chuẩn | Level | Phạm vi |
|---|---|---|
| WCAG 2.1 | AA | Admin Web |
| iOS Accessibility | VoiceOver | Mobile |
| Android Accessibility | TalkBack | Mobile |

### 2.2 Implementation

```typescript
// Mobile — React Native:
// Mỗi interactive element phải có:
<TouchableOpacity
  accessible={true}
  accessibilityLabel="Đổi món bữa trưa"
  accessibilityRole="button"
  accessibilityHint="Nhấn để xem gợi ý món thay thế"
  accessibilityState={{ disabled: false }}
>

// Image:
<Image
  source={{ uri: dish.image }}
  accessibilityLabel={`Ảnh món ${dish.name}`}
/>

// Admin Web — HTML semantics:
// - Proper heading hierarchy (h1 > h2 > h3)
// - aria-label cho icon-only buttons
// - Focus management cho modals
// - Keyboard navigation cho all interactive elements
// - Color contrast ratio ≥ 4.5:1
```

### 2.3 Color Contrast

| Element | Foreground | Background | Ratio | Pass? |
|---|---|---|---|---|
| Primary text | #1A1A1A | #FAFAF5 | 15.2:1 | ✓ AA |
| Secondary text | #757575 | #FAFAF5 | 4.8:1 | ✓ AA |
| Primary button | #FFFFFF | #2E7D52 | 5.1:1 | ✓ AA |
| Error text | #D32F2F | #FFFFFF | 5.6:1 | ✓ AA |
| Link text | #2E7D52 | #FAFAF5 | 5.3:1 | ✓ AA |

---

## 3. INTERNATIONALIZATION (i18n)

### 3.1 Phase 1 — Vietnamese Only

```typescript
// Tất cả UI text hardcoded tiếng Việt
// Chuẩn bị cho i18n bằng cách:
// 1. Không hardcode text trong JSX — dùng constants
// 2. Date format: dd/MM/yyyy (Vietnamese standard)
// 3. Number format: 1.450 kcal (dấu chấm ngàn)
// 4. Currency: không dùng (Phase 1)

// src/constants/strings.ts
export const strings = {
  home: {
    greeting: 'Xin chào',
    todayPlan: 'Thực đơn hôm nay',
    totalCalories: 'Tổng calories',
  },
  meals: {
    breakfast: 'Bữa sáng',
    lunch: 'Bữa trưa',
    dinner: 'Bữa tối',
  },
  // ... grouped by screen/feature
};
```

### 3.2 Phase 2 — i18n Ready

```typescript
// Chuyển sang react-i18next khi cần:
// - vi.json (default)
// - en.json
// Locale detection: device language
```

---

## 4. ERROR HANDLING (Frontend)

### 4.1 Error Boundary

```typescript
// src/components/ErrorBoundary.tsx
// Catch unexpected React errors
// Show: "Đã có lỗi xảy ra" + "Thử lại" button
// Log to Sentry
```

### 4.2 Network Error Handling

| Tình huống | UI Response |
|---|---|
| No internet | Banner "Không có kết nối mạng" (top, persistent) |
| API timeout (15s) | Toast "Hết thời gian chờ. Thử lại!" |
| 401 Expired | Auto-refresh token, retry silently |
| 403 Forbidden | Toast "Bạn không có quyền" |
| 404 Not Found | Navigate back + Toast |
| 422 Business error | Toast with server error message |
| 429 Rate limited | Toast "Vui lòng thử lại sau" |
| 500 Server error | Toast "Lỗi hệ thống. Vui lòng thử lại!" + Sentry |

### 4.3 Empty States

| Screen | Empty State |
|---|---|
| Home (no plan) | Illustration + "Tạo thực đơn đầu tiên" button |
| Favorites (empty) | Illustration + "Khám phá món ăn" button |
| Search (no results) | "Không tìm thấy" + suggestion chips |
| Shopping (no list) | "Tạo thực đơn trước" message |
| Weekly (no plan) | "Tạo thực đơn tuần" button |

---

## 5. SECURITY (Frontend)

| Biện pháp | Thực hiện |
|---|---|
| Token storage | SecureStore (mobile), httpOnly cookie (admin Phase 2) |
| Input sanitization | Zod validation trước khi submit |
| Deep link validation | Check auth state trước khi navigate |
| Screen capture | Không block (Phase 1) |
| Jailbreak detection | Không (Phase 1) |
| Certificate pinning | Không (Phase 1), có thể thêm Phase 2 |

---

**Trang trước:** [08 — Screens Core ←](FED-ND-2025-001_08_Screens_Core.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
