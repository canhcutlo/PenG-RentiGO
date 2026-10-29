# Hồ Sơ Giao Diện & Task Markdown Dành Cho Frontend Dev 2 (FRONTEND_DEV2_TASKS.md)

Tài liệu này dành cho **Frontend Dev 2 (FE-02)** cùng Coding Agent của bạn. Bạn phụ trách giao diện cho toàn bộ hệ sinh thái **Đời Sống Khu Trọ, Thủ Tục Hành Chính, Bảng Tin Cộng Đồng, Quản Lý Vệ Sinh, Đánh Giá, Báo Cáo Sự Cố, Chợ Đồ Thiết Yếu, Dịch Vụ Chuyển Trọ và Cửa Sổ Chatbot CSKH**.

## 1. Danh Sách 9 Tính Năng Giao Diện Do FE-02 Phụ Trách

| Thứ tự độ khó | Mã TN | Tên tính năng trên UI | Sprint | Thư mục Component trong `client/src/features/` | Phối hợp API với |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 / 18 | **FT-01** | Trang Cẩm nang thủ tục hành chính & Tải hợp đồng mẫu | Sprint 1 | `features/legal-guides/` | BE-02 |
| 3 / 18 | **FT-03** | Bảng tin Thông báo tìm đồ bị mất (Lost & Found) | Sprint 1 | `features/lost-and-found/` | BE-02 |
| 4 / 18 | **FT-04** | Bảng tin Tuyển việc vặt quanh khu trọ | Sprint 1 | `features/odd-jobs/` | BE-02 |
| 5 / 18 | **FT-05** | Bảng phân công & Checklist Quản lý vệ sinh khu trọ | Sprint 2 | `features/cleaning-schedule/` | BE-02 |
| 7 / 18 | **FT-07** | Khu vực Đánh giá sao & Nhận xét phòng trọ | Sprint 2 | `features/reviews/` | BE-01 |
| 8 / 18 | **FT-08** | Trang Báo cáo hỏng thiết bị & Báo tin sai lệch | Sprint 2 | `features/issue-reports/` | BE-02 |
| 9 / 18 | **FT-09** | Chợ Chia sẻ, Thuê & Bán đồ thiết yếu (quạt, đệm, chăn, màn...) | Sprint 2 | `features/essential-marketplace/` | BE-02 |
| 11 / 18 | **FT-11** | Màn hình Đặt dịch vụ chuyển trọ & Ước tính chi phí | Sprint 3 | `features/moving-service/` | BE-02 |
| 16 / 18 | **FT-16** | Cửa sổ nổi (Floating Widget) Tổng đài Chatbot CSKH | Sprint 5 | `features/chatbot-cskh/` | BE-02 |

## 2. Nguyên Tắc Dành Cho Agent Của FE-02

1. **Kết nối Real-time với SignalR (`@microsoft/signalr`):**
   * Đối với tính năng **Báo cáo hỏng thiết bị (`FT-08`)** và **Tổng đài Chatbot CSKH (`FT-16`)**, Agent của FE-02 sử dụng một custom hook chung tại `client/src/hooks/useSignalRHub.ts` để lắng nghe sự kiện thời gian thực từ Server C#.
2. **Tái sử dụng Layout Bảng tin (Feed Layout):**
   * Các tính năng `FT-03` (Tìm đồ mất), `FT-04` (Việc vặt) và `FT-09` (Chợ đồ thiết yếu) có thể dùng chung các Base Component như `FilterBar`, `StatusBadge`, `ImageUploadPreview` để giảm thời gian viết code và giữ giao diện đồng nhất.

## 3. File Task Mẫu Thực Chiến Cho FE-02 Agent

### Mẫu 1: `tasks/sprint-2/FE2-FT08-issue-report-ui.md` (Sprint 2)
* **Mục tiêu:** Xây dựng giao diện gửi báo cáo sự cố (báo hỏng bình nóng lạnh, máy giặt, rò nước cho Chủ trọ; hoặc báo tin đăng sai sự thật cho Admin) và bảng theo dõi tiến độ xử lý.
* **Yêu cầu UI/UX:**
  - Form tạo báo cáo nhanh gồm: Loại báo cáo (`Báo hỏng thiết bị trong phòng` / `Báo cáo tin đăng sai lệch`), Mức độ khẩn cấp (`Thấp`, `Trung bình`, `Cao`, `Khẩn cấp`), Mô tả chi tiết và khung tải ảnh chụp hiện trạng.
  - Hiển thị thanh trạng thái trực quan (Stepper): `Mới gửi` -> `Chủ trọ đã tiếp nhận` -> `Đang sửa chữa` -> `Đã hoàn tất`.
  - Khi Chủ trọ đổi trạng thái trên hệ thống, màn hình của Người thuê tự động nhảy thông báo Toast qua SignalR mà không cần tải lại trang.
* **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - [ ] Phân biệt rõ giao diện của Người thuê (gửi & xem phiếu của mình) và Chủ trọ (xem danh sách phiếu báo hỏng của các phòng và bấm cập nhật trạng thái).
  - [ ] Hiển thị thông báo lỗi rõ ràng nếu người dùng gửi phiếu trống hoặc tải file không phải định dạng ảnh.

### Mẫu 2: `tasks/sprint-2/FE2-FT09-essential-marketplace-ui.md` (Sprint 2)
* **Mục tiêu:** Xây dựng giao diện Chợ đồ thiết yếu cho phép người thuê chia sẻ miễn phí, bán thanh lý hoặc cho thuê theo tháng các vật dụng (quạt, đệm, chăn, màn, bàn học, ấm đun nước...).
* **Yêu cầu UI/UX:**
  - Có 3 tab lọc rõ ràng: `Tất cả`, `Thuê theo tháng (Đệm, Quạt, Chăn màn)`, `Mua thanh lý` và `Tặng miễn phí (0đ)`.
  - Trên mỗi thẻ món đồ cho thuê, hiển thị rõ: **Giá thuê/tháng**, **Tiền cọc**, **Tình trạng đồ (ví dụ: Mới 95%)** và **Khu trọ đang có đồ**.
  - Khi người dùng bấm **"Đặt thuê đồ"**, mở hộp thoại chọn `Ngày nhận đồ` và `Ngày dự kiến trả` -> Tự động tính tổng số tiền cần thanh toán = `(Giá thuê x Số tháng) + Tiền cọc`.
* **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - [ ] Tính đúng tiền thuê và tiền cọc ngay trên giao diện khi người dùng thay đổi ngày bắt đầu/kết thúc.
  - [ ] Không cho phép đặt thuê vượt quá số lượng tồn kho (`availableQuantity`) của món đồ.
