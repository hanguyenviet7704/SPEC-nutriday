# FED-ND-2025-001_08 — Screens — Core Features
# NutriDay Front End Design

---

| | |
|---|---|
| **Module** | 08 — Screens Core Features |
| **Phiên bản** | v1.0 DRAFT |
| **Index** | [← Quay lại INDEX](FED-ND-2025-001_INDEX.md) |

---

## SCR-010 — Home Screen

```
┌──────────────────────┐
│ Xin chào, Minh! 🔔   │
│ Hôm nay: T3, 01/06  │
│──────────────────────│
│ ┌─ Tổng hôm nay ───┐│
│ │ 1,450 / 2,000 kcal││
│ │ P: 65g C: 180g F: 48g││
│ └────────────────────┘│
│                      │
│ ☀️ Bữa sáng          │
│ ┌────────────────────┐│
│ │ Bánh mì trứng     ││
│ │ 320 kcal · 15 phút││
│ │ ✓ Verified  ♡ [Đổi]││
│ └────────────────────┘│
│                      │
│ 🌤️ Bữa trưa          │
│ ┌────────────────────┐│
│ │ Phở Bò            ││
│ │ 450 kcal · 45 phút││
│ │ ✓ Verified  ♡ [Đổi]││
│ └────────────────────┘│
│                      │
│ 🌙 Bữa tối           │
│ ┌────────────────────┐│
│ │ Cơm gà xối mỡ    ││
│ │ 680 kcal · 30 phút││
│ │ ✓ Verified  ♡ [Đổi]││
│ └────────────────────┘│
│                      │
│ [Home][Weekly][🔍][👤] │
└──────────────────────┘
```

**Data:** `useTodayMealPlan()` hook
**Features:**
- Auto-generate plan nếu chưa có (PLAN_NOT_FOUND)
- Pull-to-refresh
- NutritionBar tổng 3 bữa
- MealCard cho mỗi bữa → tap → DishDetail
- Swap button → SubstitutionScreen

---

## SCR-012 — Dish Detail Screen

```
┌──────────────────────┐
│ ← [Hero Image]   ♡  │
│                      │
│ Phở Bò              │
│ ✓ Chuyên gia xác minh│
│                      │
│ 450 kcal · 45 phút  │
│ Giảm cân · Bữa trưa │
│──────────────────────│
│ Dinh dưỡng           │
│ P: 35g  C: 45g  F: 12g│
│ ████  ████  ███      │
│──────────────────────│
│ Nguyên liệu (2 người)│
│ · Bánh phở    150g   │
│ · Thịt bò    200g   │
│ · Hành lá    2 cọng  │
│ · ...                │
│──────────────────────│
│ [Xem công thức →]    │
│──────────────────────│
│ Dị ứng: Gluten       │
└──────────────────────┘
```

**API:** `GET /v1/dishes/:id`
**Features:** Hero image parallax, nutrition bars, ingredient list scaled by servings, allergen warnings

---

## SCR-013 — Recipe Screen

```
┌──────────────────────┐
│ ← Công thức Phở Bò   │
│                      │
│ ⏱ Tổng: 45 phút     │
│                      │
│ ①  Luộc bánh phở... │
│    ⏱ 2 phút         │
│                      │
│ ②  Xào thịt bò...   │
│    ⏱ 5 phút         │
│                      │
│ ③  Nấu nước dùng... │
│    ⏱ 30 phút        │
│                      │
│ ④  Trình bày...     │
│    ⏱ 5 phút         │
└──────────────────────┘
```

---

## SCR-014 — Substitution Screen

```
┌──────────────────────┐
│ ← Đổi món bữa trưa  │
│                      │
│ Món hiện tại:        │
│ Phở Bò — 450 kcal   │
│──────────────────────│
│ Gợi ý thay thế:     │
│                      │
│ ┌────────────────────┐│
│ │ Bún bò Huế        ││
│ │ 420 kcal · 40 phút││
│ │ 92% phù hợp       ││
│ │      [Chọn món này]││
│ └────────────────────┘│
│ ┌────────────────────┐│
│ │ Mì Quảng           ││
│ │ 380 kcal · 35 phút││
│ │ 87% phù hợp       ││
│ │      [Chọn món này]││
│ └────────────────────┘│
└──────────────────────┘
```

**API:** `GET /v1/meal-plans/:planId/swap-suggestions?mealType=lunch`
**Action:** `PATCH /v1/meal-plans/:planId/swap` → optimistic update → back to Home

---

## SCR-015 — Weekly Plan Screen

```
┌──────────────────────┐
│ Thực đơn tuần        │
│ ◀ T2 T3 T4 T5 T6 T7 CN ▶│
│      ●              │
│──────────────────────│
│ Thứ 4, 04/06        │
│                      │
│ Sáng: Bánh cuốn     │
│ Trưa: Cơm tấm       │
│ Tối: Canh chua       │
│                      │
│ 1,380 kcal tổng     │
│──────────────────────│
│ [Tạo danh sách mua →]│
└──────────────────────┘
```

**API:** `GET /v1/meal-plans/weekly?weekStart=2025-06-02`

---

## SCR-016/017 — Shopping List Screen

```
┌──────────────────────┐
│ ← Danh sách mua sắm │
│ [Hôm nay] [Tuần này] │
│──────────────────────│
│ 3/12 đã mua          │
│ ████░░░░░░░░ 25%     │
│──────────────────────│
│ ▼ Thịt & Hải sản (1/3)│
│   ☐ Thịt bò    200g │
│   ☑ Tôm        300g │
│   ☐ Cá hồi    150g  │
│                      │
│ ▼ Rau củ (2/4)       │
│   ☑ Hành lá    2 bó │
│   ☑ Giá đỗ    200g  │
│   ☐ Rau muống  1 bó │
│   ☐ Cà chua   3 quả │
│──────────────────────│
│ [  Chia sẻ danh sách ]│
└──────────────────────┘
```

**API:** `GET /v1/shopping-lists/:id`, `PATCH /v1/shopping-lists/:id/items/:itemId`
**Features:** Optimistic check/uncheck, progress bar, grouped by category, share via native share

---

## SCR-018 — Search Screen

```
┌──────────────────────┐
│ [🔍 Tìm món ăn...  ] │
│──────────────────────│
│ Gợi ý: phở, bún, cơm│
│──────────────────────│
│ Lọc: [Giảm cân ✕]   │
│  [Bữa trưa ✕] [< 30p]│
│──────────────────────│
│ ┌────────────────────┐│
│ │ Phở Bò · 450 kcal ││
│ └────────────────────┘│
│ ┌────────────────────┐│
│ │ Phở Gà · 380 kcal ││
│ └────────────────────┘│
│ ...                   │
└──────────────────────┘
```

**API:** `GET /v1/search/dishes?q=...`, `GET /v1/search/autocomplete?q=...`
**Debounce:** 300ms

---

## SCR-021 — Favorites Screen

**API:** `GET /v1/favorites`
**Grid layout:** 2 columns, MealCard compact variant

---

## SCR-022 — Profile Screen

```
┌──────────────────────┐
│ ┌────┐               │
│ │ AV │ Nguyễn Minh   │
│ └────┘ Giảm cân · 2ng│
│──────────────────────│
│ › Chỉnh sửa hồ sơ   │
│ › Món yêu thích (12) │
│ › Lịch sử bữa ăn    │
│ › Cài đặt            │
│ › Thông báo          │
│──────────────────────│
│ [  Đăng xuất  ]     │
└──────────────────────┘
```

---

## SCR-025 — Settings Screen

- Đổi mật khẩu
- Thay đổi mục tiêu ăn uống
- Cập nhật dị ứng
- Thay đổi số người ăn
- Xóa tài khoản

---

## SCR-026 — Notification Settings Screen

- Push notifications toggle
- Nhắc bữa sáng (giờ)
- Nhắc bữa trưa (giờ)
- Nhắc bữa tối (giờ)
- Nhắc mua sắm

---

**Trang trước:** [07 — Screens Auth & Onboarding ←](FED-ND-2025-001_07_Screens_Auth_Onboarding.md)
**Trang tiếp theo:** [09 — Non-Functional →](FED-ND-2025-001_09_NonFunctional.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
