# [TEST-01] Kiểm thử tích hợp toàn diện (E2E & Integration Testing)

## 1. Thông tin phân công
- **Sprint:** Sprint 6
- **Độ khó:** 7.5 / 10
- **Phụ trách:** Toàn đội (BE-01, BE-02, FE-01, FE-02)
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hợp đồng API: `/docs/api/`

## 2. Mục tiêu kiểm thử tích hợp
Đảm bảo toàn bộ hệ thống PenG-RentiGO hoạt động đồng bộ, ổn định từ luồng tìm phòng, đặt cọc, thanh toán online, vận hành nội khu đến phân tích hợp đồng bằng AI.

## 3. Các kịch bản kiểm thử tích hợp trọng tâm (Integration Scenarios)

### Kịch bản 1: Chu trình đăng phòng & Tìm kiếm đa tiêu chí
- **Quy trình:**
  1. Chủ trọ đăng phòng trọ mới (`FT-06`) kèm các thuộc tính an ninh (`FT-02`).
  2. Người thuê tìm kiếm đa tiêu chí (`FT-13`) và xem tiện ích lân cận trên bản đồ GIS (`FT-14`).
- **Kỳ vọng:** Thông tin phòng hiển thị chính xác trên bản đồ và trong danh sách tìm kiếm với độ trễ thấp (< 500ms).

### Kịch bản 2: Đặt phòng ngắn hạn & Thanh toán trực tuyến
- **Quy trình:**
  1. Người thuê chọn lịch trống và đặt phòng ngắn hạn theo ngày (`FT-12`).
  2. Khởi tạo hóa đơn và thanh toán qua cổng VNPay/VietQR (`FT-17`).
  3. Cổng thanh toán gửi IPN Webhook về hệ thống.
- **Kỳ vọng:** Khóa lịch đặt thành công, không bị đặt trùng phòng (concurrency), hóa đơn đổi sang trạng thái `Paid`.

### Kịch bản 3: Báo cáo sự cố & Thông báo thời gian thực
- **Quy trình:**
  1. Người thuê gửi báo cáo hỏng bình nóng lạnh kèm ảnh chụp (`FT-08`).
  2. Hệ thống đẩy sự kiện SignalR tới chủ trọ.
  3. Chủ trọ cập nhật tiến độ sang `InProgress` và `Resolved`.
- **Kỳ vọng:** Người thuê nhận được thông báo cập nhật tức thời qua websocket mà không cần F5 trang web.

### Kịch bản 4: Quét hợp đồng thông minh & Chatbot CSKH
- **Quy trình:**
  1. Người thuê tải file ảnh hợp đồng để quét bẫy pháp lý (`FT-18`).
  2. Mở cửa sổ chat với AI Bot (`FT-16`) hỏi thêm chi tiết về điều khoản phát hiện được.
- **Kỳ vọng:** Trích xuất đúng chữ tiếng Việt, cảnh báo điều khoản bất lợi và Chatbot giải thích thông suốt dựa trên tri thức văn bản.

## 4. Tiêu chí nghiệm thu (Acceptance Criteria)
- [ ] Toàn bộ bộ test tích hợp trong `tests/PenG.RentiGO.IntegrationTests` chạy thành công (`dotnet test` đạt 100% pass).
- [ ] Toàn bộ giao diện frontend build production không có lỗi (`npm run build`).
- [ ] Không có lỗ hổng bảo mật nghiêm trọng trong quá trình xử lý webhook thanh toán và xác thực người dùng.
