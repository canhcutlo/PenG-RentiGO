# [FT-10] Ghép phòng trọ & Đi chung xe

## 1. Thông tin phân công
- **Sprint:** Sprint 3
- **Độ khó:** 6.0 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người tìm bạn cùng phòng hoặc người đi làm/sinh viên có xe muốn chia sẻ lộ trình
- **Tôi muốn:** Tìm người ở ghép phù hợp về lối sống/ngân sách và tìm bạn đi chung tuyến đường hàng ngày để chia sẻ tiền xăng
- **Để:** Giảm gánh nặng chi phí tiền nhà, tiền xăng và di chuyển an toàn, tiện lợi.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `RoommateProfiles`, `CarpoolRoutes`, `MatchRequests`
  - `RoommateProfile`: `UserId`, `GenderPreference`, `BudgetMin`, `BudgetMax`, `IsNonSmoker`, `AllowsPets`, `SleepSchedule`
  - `CarpoolRoute`: `OriginPoint`, `DestinationPoint`, `DepartureTime`, `AvailableSeats`, `CostSharePerTrip`
- **Thuật toán đối sánh (Matching Algorithm):**
  - Tính điểm tương thích Compatibility Score (0 - 100%) dựa trên: Khoảng ngân sách giao thoa, thói quen ngủ (trước 23h / cú đêm), hút thuốc, nuôi thú cưng.
- **Endpoints cần xây dựng:**
  - `GET /api/v1/roommates/recommendations`: Lấy danh sách bạn ở ghép kèm điểm tương thích %.
  - `POST /api/v1/roommates/profile`: Cập nhật hồ sơ lối sống cá nhân.
  - `GET /api/v1/carpool/routes`: Tìm tuyến đường đi chung xe theo điểm đầu/điểm cuối.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/matching/`
  - `RoommateCard`: Hiển thị hồ sơ người tìm ở ghép kèm huy hiệu tỷ lệ hợp nhau (ví dụ: "92% Phù hợp").
  - `CarpoolBoard`: Bảng hiển thị các tuyến xe đi chung theo giờ xuất phát và số chỗ trống.
  - `LifestylePreferenceForm`: Form thiết lập thói quen sinh hoạt cá nhân.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Tính điểm tương thích):** Given hai hồ sơ có cùng khoảng ngân sách, không hút thuốc và cùng giờ đi ngủ, When hệ thống so khớp, Then trả về Compatibility Score >= 85%.
- [ ] **AC-02 (Lọc tuyến đi chung xe):** When tìm kiếm theo điểm đi và điểm đến hợp lệ, Then trả về danh sách tài xế còn chỗ trống (`AvailableSeats > 0`).
- [ ] **AC-03 (Bắt lỗi):** Given hồ sơ nhập `BudgetMin > BudgetMax`, When lưu hồ sơ, Then trả về HTTP `400 Bad Request`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) cho thuật toán tính điểm tương thích bạn ở cùng.
