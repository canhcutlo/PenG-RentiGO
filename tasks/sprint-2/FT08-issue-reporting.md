# [FT-08] Trang báo cáo sự cố (hỏng thiết bị, tin sai...)

## 1. Thông tin phân công
- **Sprint:** Sprint 2
- **Độ khó:** 5.0 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người thuê trọ hoặc người tìm phòng
- **Tôi muốn:** Gửi phiếu báo hỏng thiết bị (bình nóng lạnh, điều hòa, ống nước...) đến chủ trọ hoặc báo cáo tin đăng giả mạo đến ban quản trị
- **Để:** Sự cố kỹ thuật được sửa chữa nhanh chóng và tin rác/lừa đảo bị xử lý kịp thời.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `IssueReports`
  - `Id`, `ReporterId`, `RoomId` (nullable), `TargetType` (`EquipmentDamage`, `FakeListing`), `Title`, `Description`, `Priority` (`Low`, `Medium`, `High`, `Urgent`), `Status` (`Open`, `InProgress`, `Resolved`, `Rejected`), `ResolutionNote`
- **Thông báo thời gian thực (SignalR):**
  - Hub: `/hubs/notifications`
  - Sự kiện `ReportCreated`: Bắn thông báo ngay lập tức tới nhóm chủ trọ (`Landlord_{id}`) hoặc nhóm `Admins`.
- **Endpoints cần xây dựng:**
  - `GET /api/v1/reports`: Lấy danh sách báo cáo theo vai trò (Người thuê / Chủ trọ / Admin).
  - `POST /api/v1/reports`: Tạo mới báo cáo sự cố kèm ảnh chụp.
  - `PATCH /api/v1/reports/{id}/status`: Cập nhật trạng thái xử lý (`InProgress`, `Resolved`).

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/issue-reports/`
  - `ReportListPage`: Bảng quản lý sự cố phân biệt theo trạng thái (Tabs).
  - `CreateReportForm`: Biểu mẫu chọn loại sự cố, mức độ khẩn cấp và đính kèm ảnh lỗi.
  - `ReportDetailModal`: Xem tiến độ xử lý dạng Stepper (Mới gửi -> Đã tiếp nhận -> Đang sửa -> Hoàn tất).

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Gửi báo cáo sự cố):** Given người thuê nhập đủ tiêu đề, mô tả và tải ảnh hỏng hóc, When gọi `POST /api/v1/reports`, Then lưu thành công và bắn thông báo SignalR `ReportCreated` đến chủ trọ.
- [ ] **AC-02 (Cập nhật tiến độ):** When chủ trọ đổi trạng thái sang `InProgress` kèm ghi chú sửa chữa, Then cập nhật cơ sở dữ liệu và người thuê nhận được thông báo trạng thái mới.
- [ ] **AC-03 (Bắt lỗi):** Given gửi yêu cầu thiếu tiêu đề hoặc mô tả, When gọi API, Then trả về HTTP `400 Bad Request`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra luồng chuyển đổi trạng thái và gửi thông báo SignalR.
