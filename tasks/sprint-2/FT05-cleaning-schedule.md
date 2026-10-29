# [FT-05] Quản lý vệ sinh khu vực trọ

## 1. Thông tin phân công
- **Sprint:** Sprint 2
- **Độ khó:** 3.5 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Cư dân hoặc chủ khu trọ
- **Tôi muốn:** Xem lịch trực nhật vệ sinh xoay vòng hàng tuần cho các khu vực chung (hành lang, sân phơi, khu rác) và gửi ảnh chụp nghiệm thu dọn dẹp
- **Để:** Duy trì môi trường sống sạch sẽ, công bằng và gắn kết giữa các thành viên trong khu trọ.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `CleaningSchedules`
  - `Id`, `BoardingHouseId`, `AssignedRoomId`, `WeekStartDate`, `AreaDescription`, `IsCompleted`, `ProofImageUrl`, `CompletedAt`
- **Tác vụ ngầm (Hangfire):**
  - Recurring Job chạy 07:00 sáng Thứ Hai hàng tuần: tự động phân công phòng trực nhật xoay vòng không trùng tuần liền trước.
- **Endpoints cần xây dựng:**
  - `GET /api/v1/cleaning/schedules?boardingHouseId={id}`: Lấy lịch trực nhật theo khu trọ.
  - `PATCH /api/v1/cleaning/{scheduleId}/complete`: Tải ảnh chụp nghiệm thu và đánh dấu hoàn tất nhiệm vụ trực nhật.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/cleaning-schedule/`
  - `CleaningCalendarView`: Giao diện lịch trực nhật tuần này & tuần tới.
  - `SubmitCleaningProofModal`: Hộp thoại chụp/tải ảnh xác nhận đã dọn dẹp sạch sẽ.
  - `CleaningStatusBadge`: Nhãn hiển thị trạng thái (Chưa dọn / Đã hoàn tất).

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Tự động xoay vòng lịch):** Given khu trọ có danh sách các phòng đang có người ở, When Hangfire Job chạy đầu tuần, Then phân công phòng trực nhật khu vực chung không trùng phòng tuần trước.
- [ ] **AC-02 (Nghiệm thu dọn dẹp):** Given phòng được phân công gửi ảnh xác nhận qua `PATCH /api/v1/cleaning/{scheduleId}/complete`, Then trạng thái chuyển thành `Completed` và ghi nhận thời gian hoàn thành.
- [ ] **AC-03 (Bắt lỗi):** Given người dùng không thuộc phòng được phân công trực nhật trong tuần, When cố tình gửi xác nhận, Then trả về HTTP `403 Forbidden`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra thuật toán phân công xoay vòng và nghiệm thu.
