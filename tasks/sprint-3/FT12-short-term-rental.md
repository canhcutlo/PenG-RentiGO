# [FT-12] Loại phòng trọ cho thuê ngắn hạn

## 1. Thông tin phân công
- **Sprint:** Sprint 3
- **Độ khó:** 7.0 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người đi công tác, du lịch hoặc phụ huynh đưa con đi thi
- **Tôi muốn:** Xem lịch còn trống và đặt phòng trọ theo ngày (Check-in / Check-out) với cơ chế giữ chỗ tức thời
- **Để:** Thuê được chỗ ở linh hoạt trong vài ngày với chi phí rẻ hơn khách sạn mà không lo bị trùng phòng (Overbooking).

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `Rooms`, `ShortTermBookings`, `RoomCalendarLocks`
  - Khóa lạc quan chống trùng phòng: `RowVersion` (`byte[]` / `[Timestamp]`) trên `Room` và kiểm tra khóa lịch `RoomCalendarLocks` trong Transaction.
- **Endpoints cần xây dựng:**
  - `GET /api/v1/rooms/{id}/availability?from={startDate}&to={endDate}`: Kiểm tra lịch trống của phòng theo khoảng ngày.
  - `POST /api/v1/rooms/{id}/short-term-booking`: Đặt phòng ngắn hạn theo ngày với cơ chế khóa lịch.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/short-term-booking/`
  - `DateRangePickerCalendar`: Lịch chọn ngày Check-in / Check-out, tự động vô hiệu hóa (disabled) các ngày đã có người đặt.
  - `BookingSummaryBox`: Hộp tính toán số đêm x giá thuê theo ngày + phí vệ sinh.
  - `ConfirmationModal`: Hộp thoại xác nhận đặt phòng và chuyển hướng thanh toán.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Kiểm tra ngày trống):** When người dùng chọn khoảng ngày không có ai đặt, Then hiển thị trạng thái "Còn phòng" kèm tổng giá tiền theo số đêm.
- [ ] **AC-02 (Chống đặt trùng phòng Concurrency):** Given hai người dùng cùng bấm đặt 1 phòng trong cùng khoảng ngày, When giao dịch thứ hai hoàn tất, Then giao dịch thứ hai bị từ chối với lỗi xung đột lịch đặt (HTTP `409 Conflict`) và không bị Overbooking.
- [ ] **AC-03 (Bắt lỗi ngày không hợp lệ):** Given ngày Check-out trước hoặc trùng ngày Check-in, When gửi request, Then trả về HTTP `400 Bad Request`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra việc khóa lịch và xử lý xung đột `DbUpdateConcurrencyException`.
