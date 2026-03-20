# SDD-ND-2025-001 | INDEX — TỔNG HỢP TÀI LIỆU SDD
## NutriDay — Screen Design Detail

| Mã tài liệu: | SDD-ND-2025-001_INDEX |
|:---|:---|
| **Sản phẩm:** | NutriDay — Ứng dụng gợi ý thực đơn thông minh hằng ngày |
| **Phiên bản SDD:** | v1.0 |
| **Ngày tạo:** | 19/03/2025 |
| **Tham chiếu BRD:** | BRD-ND-2025-001 v1.0 |
| **BA phụ trách:** | Trần Lan Anh (BA Lead), Phạm Đức Huy (BA) |
| **Nền tảng:** | Mobile App iOS & Android (React Native) + Web Admin (ReactJS) |
| **Ngôn ngữ giao diện:** | Tiếng Việt |
| **Phân loại:** | NỘI BỘ — KHÔNG PHÂN PHỐI BÊN NGOÀI |

---

## CẤU TRÚC TÀI LIỆU SDD

Tài liệu SDD được chia thành **10 module file** tương ứng với các nhóm tính năng:

| File | Module | Nội dung | Màn hình |
|:---|:---|:---|:---|
| `SDD-ND-2025-001_00_DesignSystem.md` | 00 — Design System | Bảng màu, typography, spacing, component library | — |
| `SDD-ND-2025-001_01_Onboarding_Authentication.md` | 01 — Onboarding & Auth | Splash, Welcome, Đăng ký, OTP, Đăng nhập, Onboarding 3 bước | SCR-001 → SCR-009 |
| `SDD-ND-2025-001_02_Home_DailyMenu.md` | 02 — Trang Chủ & Gợi Ý Ngày | Trang chủ, Bottom sheet đổi món | SCR-010, SCR-011 |
| `SDD-ND-2025-001_03_DishDetail_Recipe.md` | 03 — Chi Tiết Món & Công Thức | Chi tiết món, Bước nấu + Timer, Thay thế nguyên liệu | SCR-012, SCR-013, SCR-014 |
| `SDD-ND-2025-001_04_WeeklyPlan.md` | 04 — Kế Hoạch Tuần | Lưới thực đơn 7×3, Tự động điền tuần, DS mua sắm tuần | SCR-015, SCR-017 |
| `SDD-ND-2025-001_05_ShoppingList.md` | 05 — Mua Sắm | Danh sách nguyên liệu ngày, Gộp & chia sẻ | SCR-016 |
| `SDD-ND-2025-001_06_Search_Explore.md` | 06 — Tìm Kiếm & Khám Phá | Explore, Tìm theo tên, Tìm theo nguyên liệu | SCR-018, SCR-019, SCR-020 |
| `SDD-ND-2025-001_07_Favorites_Profile.md` | 07 — Yêu Thích & Hồ Sơ | Danh sách yêu thích, Trang cá nhân, Chỉnh sửa, Lịch sử | SCR-021, SCR-022, SCR-023, SCR-024 |
| `SDD-ND-2025-001_08_AdminCMS.md` | 08 — Admin CMS | Dashboard, Quản lý món, Form thêm/sửa, Import Excel, Review, Báo cáo | ADM-001 → ADM-006 |
| `SDD-ND-2025-001_09_Settings_Notifications.md` | 09 — Cài Đặt & Thông Báo | Cài đặt app, Notification settings | SCR-025, SCR-026 |

---

## DANH SÁCH ĐẦY ĐỦ CÁC MÀN HÌNH

### Mobile App (iOS & Android)

| Screen ID | Tên Màn Hình | Module | Use Case BRD |
|:---|:---|:---|:---|
| SCR-001 | Splash Screen | 01 | — |
| SCR-002 | Welcome / Giới thiệu | 01 | UC-U01 |
| SCR-003 | Đăng ký tài khoản | 01 | UC-U02 |
| SCR-004 | Xác thực OTP | 01 | UC-U02 |
| SCR-005 | Đăng nhập | 01 | UC-U03 |
| SCR-006 | Quên mật khẩu | 01 | UC-U03 |
| SCR-007 | Onboarding: Chọn mục tiêu sức khỏe | 01 | UC-U04 |
| SCR-008 | Onboarding: Số người ăn | 01 | UC-U04 |
| SCR-009 | Onboarding: Dị ứng thực phẩm | 01 | UC-U04 |
| SCR-010 | Trang chủ — Thực đơn hôm nay | 02 | UC-M01, M02, M03, M04 |
| SCR-011 | Bottom Sheet — Đổi món | 02 | UC-M02 |
| SCR-012 | Chi tiết món ăn | 03 | UC-R01, R02, R05 |
| SCR-013 | Công thức nấu & Timer | 03 | UC-R01, R03 |
| SCR-014 | Gợi ý thay thế nguyên liệu | 03 | UC-R04 |
| SCR-015 | Kế hoạch thực đơn tuần | 04 | UC-W01, W02, W03 |
| SCR-016 | Danh sách nguyên liệu ngày | 05 | UC-S01, S02, S03 |
| SCR-017 | Danh sách mua sắm tuần | 04 | UC-W03, S01-S03 |
| SCR-018 | Tìm kiếm theo tên món | 06 | UC-F01 |
| SCR-019 | Tìm kiếm theo nguyên liệu | 06 | UC-F02 |
| SCR-020 | Khám phá món ăn (Explore) | 06 | UC-F03 |
| SCR-021 | Danh sách món yêu thích | 07 | UC-P01, R05 |
| SCR-022 | Trang cá nhân & thống kê | 07 | UC-P02 |
| SCR-023 | Chỉnh sửa hồ sơ & mục tiêu | 07 | UC-U05 |
| SCR-024 | Lịch sử thực đơn | 07 | UC-U06 |
| SCR-025 | Cài đặt | 09 | — |
| SCR-026 | Cài đặt thông báo | 09 | BR-012 |

**Tổng: 26 màn hình Mobile**

### Admin Web App (ReactJS)

| Screen ID | Tên Màn Hình | Module | BRD Reference |
|:---|:---|:---|:---|
| ADM-001 | Dashboard tổng quan | 08 | §6.8 |
| ADM-002 | Danh sách món ăn | 08 | §6.8 |
| ADM-003 | Thêm/Sửa món ăn | 08 | BR-008 |
| ADM-004 | Import Excel hàng loạt | 08 | BR-008 |
| ADM-005 | Nutritionist Review | 08 | BR-004 |
| ADM-006 | Báo cáo & thống kê | 08 | §6.8 |

**Tổng: 6 màn hình Admin**

---

## MA TRẬN TRUY XUẤT: USE CASE → MÀN HÌNH

| Use Case | Màn hình chính | Màn hình liên quan |
|:---|:---|:---|
| UC-U01 (Guest Mode) | SCR-002 | SCR-007, SCR-008, SCR-009 |
| UC-U02 (Đăng ký) | SCR-003 | SCR-004 |
| UC-U03 (Đăng nhập) | SCR-005 | SCR-006 |
| UC-U04 (Onboarding) | SCR-007, SCR-008, SCR-009 | — |
| UC-U05 (Cập nhật hồ sơ) | SCR-023 | SCR-022 |
| UC-U06 (Lịch sử) | SCR-024 | SCR-022 |
| UC-M01 (Thực đơn ngày) | SCR-010 | — |
| UC-M02 (Đổi món) | SCR-011 | SCR-010 |
| UC-M03 (Gợi ý ngẫu nhiên) | SCR-010 | — |
| UC-M04 (Bộ lọc nhanh) | SCR-010 | — |
| UC-R01 (Chi tiết món) | SCR-012 | SCR-013 |
| UC-R02 (Điều chỉnh khẩu phần) | SCR-012 | — |
| UC-R03 (Timer nấu ăn) | SCR-013 | — |
| UC-R04 (Thay thế nguyên liệu) | SCR-014 | SCR-012 |
| UC-R05 (Yêu thích) | SCR-012 | SCR-021 |
| UC-S01 (DS nguyên liệu) | SCR-016, SCR-017 | — |
| UC-S02 (Tích chọn đã có) | SCR-016, SCR-017 | — |
| UC-S03 (Chia sẻ DS mua) | SCR-016, SCR-017 | — |
| UC-W01 (Xem & sửa tuần) | SCR-015 | SCR-011 |
| UC-W02 (Tự động điền tuần) | SCR-015 | — |
| UC-W03 (DS mua sắm tuần) | SCR-017 | SCR-015 |
| UC-F01 (Tìm theo tên) | SCR-018 | SCR-020 |
| UC-F02 (Tìm theo nguyên liệu) | SCR-019 | SCR-020 |
| UC-F03 (Duyệt & lọc) | SCR-020 | — |
| UC-P01 (Danh sách yêu thích) | SCR-021 | — |
| UC-P02 (Trang cá nhân) | SCR-022 | — |

---

## MA TRẬN TRUY XUẤT: BUSINESS REQUIREMENT → MÀN HÌNH

| BR-ID | Nội dung tóm tắt | Màn hình |
|:---|:---|:---|
| BR-001 | Gợi ý thực đơn ngay không cần đăng ký | SCR-002, SCR-007→009, SCR-010 |
| BR-002 | Không lặp món trong 7 ngày | SCR-010 (logic), SCR-011 |
| BR-003 | Đầy đủ thông tin: công thức, dinh dưỡng, ảnh | SCR-012, SCR-013 |
| BR-004 | Nutritionist xác nhận dinh dưỡng | ADM-005 |
| BR-005 | 8 chế độ ăn, ≥ 30 món/chế độ | SCR-007, SCR-020, ADM-001 |
| BR-006 | Tổng hợp DS nguyên liệu theo danh mục | SCR-016, SCR-017 |
| BR-007 | Kế hoạch thực đơn tuần | SCR-015, SCR-017 |
| BR-008 | Admin CMS dễ dùng, import Excel | ADM-002, ADM-003, ADM-004 |
| BR-009 | Tìm món theo nguyên liệu | SCR-019 |
| BR-010 | Điều chỉnh khẩu phần | SCR-012 |
| BR-011 | Gợi ý thay thế nguyên liệu | SCR-014 |
| BR-012 | Thông báo nhắc nấu ăn | SCR-026 |

---

## MA TRẬN TRUY XUẤT: CHANGE REQUEST → MÀN HÌNH

| CR-ID | Nội dung | Màn hình |
|:---|:---|:---|
| CR-001 | Nút "Gợi ý ngẫu nhiên" | SCR-010 |
| CR-002 | Điều chỉnh khẩu phần nhân/chia định lượng | SCR-012 |
| CR-003 | Tìm món theo nguyên liệu sẵn có | SCR-019 |
| CR-004 | Apple Health / Google Fit | Phase 2 — không có trong SDD này |
| CR-005 | Timer đếm ngược trong bước nấu | SCR-013 |

---

## LUỒNG ĐIỀU HƯỚNG CHÍNH

```
LUỒNG 1: NGƯỜI DÙNG MỚI (GUEST)
Splash → Welcome → Onboarding (3 bước) → Home → Explore → Dish Detail → Recipe

LUỒNG 2: NGƯỜI DÙNG MỚI (ĐĂNG KÝ)
Splash → Welcome → Register → OTP → Onboarding → Home

LUỒNG 3: NGƯỜI DÙNG QUAY LẠI (TOKEN VALID)
Splash → Home (< 2s)

LUỒNG 4: XEM VÀ NẤU ĂN (CORE JOURNEY)
Home → [Tap card] → Dish Detail → [Bắt đầu nấu] → Recipe + Timer

LUỒNG 5: LẬP KẾ HOẠCH TUẦN
Home → [Tab Tuần] → Week Plan → [Tự động điền] → [Tạo DS mua] → Shopping List Tuần

LUỒNG 6: MUA SẮM THÔNG MINH
Home → [Shopping CTA] → Shopping List → [Tick đã có] → [Chia sẻ] → Share Sheet

LUỒNG 7: TÌM KIẾM THEO NGUYÊN LIỆU
Explore → [Tìm theo NL] → Ingredient Search → [Nhập NL] → Results → Dish Detail

LUỒNG 8: ADMIN WORKFLOW (NỘI DUNG)
Admin Login → Dashboard → Add Dish → Submit Review → Nutritionist Review → Approve → Published
```

---

## NGUYÊN TẮC THIẾT KẾ CHUNG

### Warm & Fresh Design
- **Màu chính:** Xanh lá `#2E7D52` — sức khỏe, tươi mới
- **Màu nền:** Trắng `#FFFFFF` + Kem `#FFF8F0`
- **Màu highlight:** Cam `#E8651A`
- **Font:** SVN-Gilroy (heading) + Be Vietnam Pro (body)

### UX Principles
1. **Tốc độ:** TTI ≤ 2s, API ≤ 300ms — không để người dùng chờ
2. **Đơn giản:** 3 bước có thực đơn — không form dài
3. **Tin cậy:** Badge "Đã kiểm duyệt" trên mọi thông tin dinh dưỡng
4. **Cảm xúc:** Micro-animations, confetti, haptic — tạo delight

### Accessibility
- Font minimum: 14pt (body), 16pt (cooking mode)
- Contrast ratio: ≥ 4.5:1 (WCAG 2.1 AA)
- Touch targets: minimum 44×44pt
- Screen reader: VoiceOver/TalkBack support cơ bản

---

## TRẠNG THÁI TÀI LIỆU

| Module | File | Trạng thái | Ngày hoàn thành |
|:---|:---|:---|:---|
| 00 — Design System | SDD-ND-2025-001_00 | ✅ Draft v1.0 | 19/03/2025 |
| 01 — Onboarding & Auth | SDD-ND-2025-001_01 | ✅ Draft v1.0 | 19/03/2025 |
| 02 — Home & Daily Menu | SDD-ND-2025-001_02 | ✅ Draft v1.0 | 19/03/2025 |
| 03 — Dish Detail & Recipe | SDD-ND-2025-001_03 | ✅ Draft v1.0 | 19/03/2025 |
| 04 — Weekly Plan | SDD-ND-2025-001_04 | ✅ Draft v1.0 | 19/03/2025 |
| 05 — Shopping List | SDD-ND-2025-001_05 | ✅ Draft v1.0 | 19/03/2025 |
| 06 — Search & Explore | SDD-ND-2025-001_06 | ✅ Draft v1.0 | 19/03/2025 |
| 07 — Favorites & Profile | SDD-ND-2025-001_07 | ✅ Draft v1.0 | 19/03/2025 |
| 08 — Admin CMS | SDD-ND-2025-001_08 | ✅ Draft v1.0 | 19/03/2025 |
| 09 — Settings & Notifications | SDD-ND-2025-001_09 | ✅ Draft v1.0 | 19/03/2025 |

**Tổng trạng thái:** 10/10 modules hoàn thành — Chờ Review nội bộ

---

## BƯỚC TIẾP THEO

| # | Hành động | Người phụ trách | Hạn |
|:---|:---|:---|:---|
| 1 | Review SDD với PO (Nguyễn Minh Tú) | Trần Lan Anh | 25/03/2025 |
| 2 | Review SDD với Dev Lead | Lê Hoàng Nam | 28/03/2025 |
| 3 | Cập nhật dựa trên feedback | Phạm Đức Huy | 31/03/2025 |
| 4 | Bàn giao cho UI/UX team (Figma) | Trần Lan Anh | 01/04/2025 |
| 5 | Bàn giao cho Dev team | Lê Hoàng Nam | 01/04/2025 |
| 6 | SDD v1.1 sau khi có Figma wireframe | Phạm Đức Huy | 15/04/2025 |

---

*SDD-ND-2025-001_INDEX | NutriDay Screen Design Detail | v1.0 DRAFT | NỘI BỘ — KHÔNG PHÂN PHỐI*

*— HẾT TÀI LIỆU INDEX —*
