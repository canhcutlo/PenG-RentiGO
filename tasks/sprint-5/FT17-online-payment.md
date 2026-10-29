# [FT-17] Thanh toán online (VNPay / VietQR)

## 1. Thông tin phân công
- **Sprint:** Sprint 5
- **Độ khó:** 9.0 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người thuê trọ
- **Tôi muốn:** Thanh toán tiền cọc giữ phòng, tiền thuê phòng hàng tháng, tiền thuê đồ thiết yếu hoặc dịch vụ chuyển trọ qua cổng thanh toán VNPay hoặc quét mã VietQR động
- **Để:** Giao dịch an toàn, tức thì, có hóa đơn điện tử rõ ràng và không cần mang tiền mặt.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `Invoices`, `PaymentTransactions`
  - Tích hợp cổng thanh toán: VNPay / PayOS SDK.
  - Xác thực bảo mật: Kiểm tra mã băm chữ ký số HMAC-SHA512 trên Webhook Callback từ cổng thanh toán.
  - Nguyên tắc Idempotency: Đảm bảo một mã giao dịch không bị xử lý lặp lại (xử lý an toàn trong Database Transaction).
- **Endpoints cần xây dựng:**
  - `POST /api/v1/payments/create-url`: Khởi tạo liên kết thanh toán VNPay hoặc sinh mã VietQR theo hóa đơn.
  - `GET /api/v1/payments/vnpay-return`: URL chuyển hướng người dùng sau khi thanh toán.
  - `POST /api/v1/payments/webhook`: Nhận IPN Callback ngầm từ cổng thanh toán, xác thực chữ ký và cập nhật trạng thái hóa đơn (`Paid`).

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/payments/`
  - `CheckoutPage`: Màn hình thanh toán hiển thị chi tiết hóa đơn (mã hóa đơn, số tiền, nội dung chuyển khoản).
  - `VietQrModal`: Hộp thoại hiển thị mã QR động có sẵn số tiền và nội dung chuyển khoản chính xác.
  - `PaymentResultPage`: Màn hình thông báo kết quả giao dịch (Thành công / Thất bại) kèm mã tham chiếu và nút tải biên lai.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Tạo URL thanh toán):** Given hóa đơn hợp lệ chưa thanh toán, When gọi `POST /api/v1/payments/create-url`, Then sinh URL thanh toán VNPay có gắn checksum bảo mật HMAC-SHA512.
- [ ] **AC-02 (Xử lý Webhook an toàn):** Given cổng thanh toán gửi IPN Webhook hợp lệ, When hệ thống xác thực chữ ký số thành công, Then trạng thái hóa đơn đổi sang `Paid` và ghi nhận lịch sử giao dịch.
- [ ] **AC-03 (Chặn giả mạo Webhook):** Given Webhook gửi chữ ký băm không khớp hoặc bị thay đổi số tiền, When hệ thống nhận request, Then từ chối và trả về HTTP `400 Bad Request` kèm log cảnh báo bảo mật.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 4 Unit Tests (xUnit) kiểm tra tính toán chữ ký HMAC-SHA512, Idempotency và xử lý cập nhật hóa đơn.
