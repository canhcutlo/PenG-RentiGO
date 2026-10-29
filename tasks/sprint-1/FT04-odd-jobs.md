# [FT-04] Thông tin tuyển việc vặt quanh khu trọ

## 1. Thông tin phân công
- **Sprint:** Sprint 1
- **Độ khó:** 3.0 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người bận rộn hoặc sinh viên muốn kiếm thêm thu nhập tại khu trọ
- **Tôi muốn:** Đăng tin nhờ làm việc vặt (nhận hàng giúp, dọn phòng, khiêng đồ, mua hộ đồ ăn...) kèm mức thù lao, hoặc nhận làm các việc vặt xung quanh
- **Để:** Tiết kiệm thời gian cá nhân hoặc có thêm thu nhập trang trải cuộc sống.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `OddJobs`, `OddJobApplications`
  - `OddJob`: `Id`, `PosterId`, `Title`, `Description`, `WageAmount`, `LocationText`, `Status` (`Open`, `Assigned`, `Completed`)
  - `OddJobApplication`: `Id`, `JobId`, `ApplicantId`, `Note`, `AppliedAt`, `IsAccepted` (bool?)
- **Endpoints cần xây dựng:**
  - `GET /api/v1/odd-jobs`: Lấy danh sách việc vặt đang mở (`status=Open`).
  - `POST /api/v1/odd-jobs`: Đăng tin việc vặt kèm thù lao dự kiến.
  - `POST /api/v1/odd-jobs/{id}/apply`: Ứng tuyển nhanh việc vặt.
  - `PATCH /api/v1/odd-jobs/{id}/status`: Cập nhật trạng thái công việc (`Open` -> `Assigned` -> `Completed`).

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/odd-jobs/`
  - `JobBoardPage`: Bảng tin việc vặt hiển thị thẻ việc làm kèm huy hiệu giá tiền (VNĐ).
  - `CreateJobModal`: Biểu mẫu đăng việc vặt với các trường tiêu đề, thù lao, hạn chót.
  - `ApplyJobDialog`: Hộp thoại ứng tuyển nhanh và xem danh sách người nhận việc.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Đăng việc vặt):** Given thông tin việc vặt hợp lệ và thù lao `wageAmount > 0`, When gọi `POST /api/v1/odd-jobs`, Then trả về HTTP `201 Created`.
- [ ] **AC-02 (Ứng tuyển không trùng lặp):** Given người dùng đã ứng tuyển một công việc, When tiếp tục gọi `POST /api/v1/odd-jobs/{id}/apply` cho cùng công việc đó, Then trả về HTTP `400 Bad Request` thông báo đã ứng tuyển.
- [ ] **AC-03 (Chuyển trạng thái hoàn thành):** When người đăng việc chọn người làm và xác nhận hoàn thành, Then `Status` cập nhật thành `Completed`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) cho tầng Application Service của Odd Jobs.
