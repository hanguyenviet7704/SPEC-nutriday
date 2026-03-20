# TSD-ND-2025-001_08 — Test Case Tables
# NutriDay Test Design

---

| | |
|---|---|
| **Module** | 08 — Detailed Test Cases |
| **Index** | [← Quay lại INDEX](TSD-ND-2025-001_INDEX.md) |

---

## 1. AUTH TEST CASES

| ID | Mô tả | Input | Expected | Priority |
|---|---|---|---|---|
| TC-AUTH-001 | Đăng ký thành công | fullName, email, phone, password hợp lệ | 201, userId + otpExpiry | P0 |
| TC-AUTH-002 | Đăng ký email trùng | email đã tồn tại | 409, USER_001 | P0 |
| TC-AUTH-003 | Đăng ký phone trùng | phone đã tồn tại | 409, USER_002 | P0 |
| TC-AUTH-004 | Đăng ký password yếu | password="123456" | 400, VALIDATION_ERROR | P1 |
| TC-AUTH-005 | Đăng ký thiếu field | bỏ email | 400, VALIDATION_ERROR | P1 |
| TC-AUTH-006 | Verify OTP đúng | OTP 6 số hợp lệ | 200, verified=true | P0 |
| TC-AUTH-007 | Verify OTP sai | OTP sai | 400, AUTH_003 | P0 |
| TC-AUTH-008 | Verify OTP hết hạn | OTP > 10 phút | 400, AUTH_004 | P1 |
| TC-AUTH-009 | Verify OTP quá 5 lần | 6th attempt | 429, AUTH_005 | P1 |
| TC-AUTH-010 | Login thành công | email + password đúng | 200, tokens + user | P0 |
| TC-AUTH-011 | Login bằng phone | phone + password đúng | 200, tokens + user | P0 |
| TC-AUTH-012 | Login sai password | password sai | 401, AUTH_001 | P0 |
| TC-AUTH-013 | Login tài khoản chưa verify | unverified account | 403, AUTH_002 | P1 |
| TC-AUTH-014 | Login rate limit | > 5 lần/phút | 429 | P1 |
| TC-AUTH-015 | Refresh token | refreshToken hợp lệ | 200, new accessToken | P0 |
| TC-AUTH-016 | Refresh token hết hạn | expired refreshToken | 401 | P1 |
| TC-AUTH-017 | Logout | accessToken hợp lệ | 200, token revoked | P0 |
| TC-AUTH-018 | Forgot password | email tồn tại | 200, email sent | P1 |
| TC-AUTH-019 | Reset password | valid token + new password | 200 | P1 |
| TC-AUTH-020 | Reset token hết hạn | expired token | 400 | P2 |

---

## 2. MEAL PLAN TEST CASES

| ID | Mô tả | Input | Expected | Priority |
|---|---|---|---|---|
| TC-PLAN-001 | Tạo plan single day | date, mode=single | 201, 3 meals | P0 |
| TC-PLAN-002 | Tạo plan weekly | date, mode=weekly | 201, 7 days × 3 meals | P0 |
| TC-PLAN-003 | Không trùng 7 ngày | user có meal history 7 ngày | Tất cả dish mới ≠ history | P0 |
| TC-PLAN-004 | Loại allergen | user dị ứng shellfish | Không có món chứa shellfish | P0 |
| TC-PLAN-005 | Đúng diet goal | user dietGoal=weight_loss | Tất cả dish thuộc weight_loss | P0 |
| TC-PLAN-006 | Đúng meal type | breakfast slot | Dish có mealType=breakfast | P1 |
| TC-PLAN-007 | Plan đã tồn tại | date có plan rồi | 409, PLAN_002 | P1 |
| TC-PLAN-008 | Không đủ món | DB ít hơn 3 eligible dishes | 422, PLAN_001 | P1 |
| TC-PLAN-009 | Get today plan | Authenticated user | 200, today's meals | P0 |
| TC-PLAN-010 | Get weekly plan | weekStart parameter | 200, 7 days data | P0 |
| TC-PLAN-011 | Swap meal | planId, mealType, newDishId | 200, was_swapped=true | P0 |
| TC-PLAN-012 | Swap - plan khác user | planId thuộc user khác | 403 | P1 |
| TC-PLAN-013 | Swap suggestions | planId, mealType | 200, list alternatives | P0 |
| TC-PLAN-014 | Không swap sang allergen dish | newDish có allergen | 422 | P1 |
| TC-PLAN-015 | Record meal history | Sau generate | meal_history records created | P1 |

---

## 3. SHOPPING LIST TEST CASES

| ID | Mô tả | Input | Expected | Priority |
|---|---|---|---|---|
| TC-SHOP-001 | Tạo list daily | date (has plan) | 201, grouped by category | P0 |
| TC-SHOP-002 | Tạo list weekly | weekStart (has plans) | 201, aggregated | P0 |
| TC-SHOP-003 | Nguyên liệu gộp | 2 món có "thịt bò" | quantity = sum | P0 |
| TC-SHOP-004 | Scale by servings | user servings=2 | quantity × 2 | P0 |
| TC-SHOP-005 | Chưa có meal plan | date không có plan | 422, SHOP_001 | P1 |
| TC-SHOP-006 | Check item | itemId | is_checked=true, checked_at set | P0 |
| TC-SHOP-007 | Uncheck item | checked itemId | is_checked=false | P1 |
| TC-SHOP-008 | Progress update | Check 3/10 items | checked_items=3, total=10 | P1 |
| TC-SHOP-009 | Category grouping | Mixed ingredients | Đúng category cho mỗi item | P1 |
| TC-SHOP-010 | Share list | listId | 200, shareable format | P2 |

---

## 4. ADMIN TEST CASES

| ID | Mô tả | Input | Expected | Priority |
|---|---|---|---|---|
| TC-ADM-001 | Editor tạo dish | Valid dish payload | 201, status=draft | P0 |
| TC-ADM-002 | Editor update dish | dishId + changes | 200, updated | P0 |
| TC-ADM-003 | Nutritionist approve | dishId, action=approve | status=published, verified=true | P0 |
| TC-ADM-004 | Nutritionist reject | dishId, action=reject, comment | status=rejected | P0 |
| TC-ADM-005 | Review record created | After approve/reject | dish_reviews record exists | P1 |
| TC-ADM-006 | Audit log created | Any admin action | audit_logs record exists | P1 |
| TC-ADM-007 | Import valid xlsx | 50 rows valid | importedCount=50 | P0 |
| TC-ADM-008 | Import with errors | 47 valid + 3 invalid | importedCount=47, errors=3 | P1 |
| TC-ADM-009 | Import invalid file | .csv file | 400 | P1 |
| TC-ADM-010 | Nutritionist cannot create | POST /admin/dishes | 403 | P0 |
| TC-ADM-011 | Editor cannot manage users | GET /admin/users | 403 | P0 |
| TC-ADM-012 | Super admin full access | Any admin endpoint | 200 | P1 |
| TC-ADM-013 | Soft delete dish | dishId | deleted_at set, not in public queries | P1 |
| TC-ADM-014 | Dashboard stats | GET /admin/reports/stats | Correct counts | P2 |
| TC-ADM-015 | Diet goal report | GET /admin/reports/diet-goals | Distribution data | P2 |

---

## 5. SEARCH TEST CASES

| ID | Mô tả | Input | Expected | Priority |
|---|---|---|---|---|
| TC-SEARCH-001 | Tìm theo tên | q="phở" | Dishes containing "phở" | P0 |
| TC-SEARCH-002 | Tìm theo nguyên liệu | q="thịt bò" | Dishes with thịt bò | P1 |
| TC-SEARCH-003 | Filter diet goal | dietGoal=weight_loss | Only weight_loss dishes | P0 |
| TC-SEARCH-004 | Filter meal type | mealType=breakfast | Only breakfast dishes | P1 |
| TC-SEARCH-005 | Filter cooking time | maxCookingTime=30 | Only ≤ 30 min dishes | P1 |
| TC-SEARCH-006 | Autocomplete | q="ph" (2 chars) | Suggestions: phở, phở gà... | P1 |
| TC-SEARCH-007 | No results | q="xyznotfound" | Empty array, 200 | P1 |
| TC-SEARCH-008 | Pagination | page=2, limit=10 | Correct offset | P1 |

---

## 6. FAVORITES TEST CASES

| ID | Mô tả | Input | Expected | Priority |
|---|---|---|---|---|
| TC-FAV-001 | Add favorite | dishId | 201, favorite created | P0 |
| TC-FAV-002 | Remove favorite | dishId | 200, favorite removed | P0 |
| TC-FAV-003 | Duplicate add | dishId đã favorite | 409 | P1 |
| TC-FAV-004 | List favorites | GET /favorites | Paginated list | P0 |
| TC-FAV-005 | Filter by diet goal | dietGoal=weight_loss | Only matching | P2 |

---

## 7. TEST SUMMARY

| Module | P0 | P1 | P2 | Total |
|---|---|---|---|---|
| Auth | 9 | 8 | 3 | 20 |
| Meal Plan | 6 | 7 | 2 | 15 |
| Shopping List | 3 | 5 | 2 | 10 |
| Admin | 4 | 7 | 4 | 15 |
| Search | 2 | 5 | 1 | 8 |
| Favorites | 2 | 2 | 1 | 5 |
| **Total** | **26** | **34** | **13** | **73** |

---

**Trang trước:** [07 — Security Tests ←](TSD-ND-2025-001_07_Security.md)
**Trang tiếp theo:** [09 — Test Data & Acceptance →](TSD-ND-2025-001_09_TestData_Acceptance.md)

*Phiên bản: v1.0 DRAFT — 20/03/2025*
