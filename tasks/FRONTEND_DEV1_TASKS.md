# Hồ Sơ Giao Diện & Task Markdown Dành Cho Frontend Dev 1 (FRONTEND_DEV1_TASKS.md)

Tài liệu này dành cho **Frontend Dev 1 (FE-01)** cùng Coding Agent của bạn (Cursor, Windsurf, Claude Code...). Bạn phụ trách giao diện cho nhóm tính năng **Phòng Trọ Cốt Lõi, Tìm Kiếm & Bản Đồ Tiện Ích (GIS), Đặt Phòng Ngắn Hạn, Ghép Phòng/Chung Xe, Thanh Toán Online, Dự Đoán Điện Nước và AI Quét Hợp Đồng**.

## 1. Danh Sách 9 Tính Năng Giao Diện Do FE-01 Phụ Trách

| Thứ tự độ khó | Mã TN | Tên tính năng trên UI | Sprint | Thư mục Component trong `client/src/features/` | Phối hợp API với |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 2 / 18 | **FT-02** | Huy hiệu & biểu mẫu An ninh khu trọ | Sprint 1 | `features/security-badges/` | BE-01 |
| 6 / 18 | **FT-06** | Trang chi tiết phòng trọ, Tiện nghi & Đăng tin phòng | Sprint 1 | `features/rooms/` | BE-01 |
| 10 / 18 | **FT-10** | Giao diện Tìm bạn ở ghép (% hợp nhau) & Đi chung xe | Sprint 3 | `features/matching/` | BE-02 |
| 12 / 18 | **FT-12** | Lịch đặt phòng ngắn hạn theo ngày (Check-in / Check-out) | Sprint 3 | `features/short-term-booking/` | BE-01 |
| 13 / 18 | **FT-13** | Trang Tìm kiếm & Bộ lọc phòng trọ đa tiêu chí | Sprint 3 | `features/search/` | BE-01 |
| 14 / 18 | **FT-14** | Bản đồ tương tác hiển thị tiện ích ở gần (Nhà ăn, Siêu thị, Công viên...) | Sprint 4 | `features/nearby-map/` | BE-01 |
| 15 / 18 | **FT-15** | Công cụ tính & dự báo tiền điện nước hàng tháng | Sprint 4 | `features/utility-predictor/` | BE-01 |
| 17 / 18 | **FT-17** | Cổng thanh toán trực tuyến VietQR / VNPay & Hóa đơn | Sprint 5 | `features/payments/` | BE-01 |
| 18 / 18 | **FT-18** | Màn hình AI Quét Hợp đồng thuê trọ phát hiện "Điều khoản bẫy" | Sprint 6 | `features/contract-scanner/` | BE-01 |

## 2. Nguyên Tắc Dành Cho Agent Của FE-01 (Làm Việc Không Cần Chờ Backend)

1. **Khởi tạo Mock Service từ ngày đầu Sprint:**
   * Agent của FE-01 đọc file `docs/api/core-rooms-api.md` (trong `BACKEND_DEV1_TASKS.md`) để sinh file `client/src/api/roomApi.ts` cùng dữ liệu giả lập `mockRooms.ts`.
   * Sử dụng biến môi trường `VITE_USE_MOCK_API=true` trong lúc BE-01 đang viết C#, và chuyển sang `false` ngay khi BE-01 đẩy nhánh API lên Git.
2. **Chuẩn hóa TypeScript Interfaces:**
   ```typescript
   export type AmenityCode =
     | 'WASHING_MACHINE'   // Máy giặt
     | 'WATER_PURIFIER'    // Máy lọc nước
     | 'WATER_HEATER'      // Bình nóng lạnh
     | 'HIGH_SPEED_WIFI'   // Internet tốc độ cao
     | 'AIR_CONDITIONER'   // Điều hòa
     | 'FRIDGE'            // Tủ lạnh
     | 'PRIVATE_BALCONY';  // Ban công riêng

   export interface SecurityInfoDto {
     hasCamera24x7: boolean;
     hasFingerprintLock: boolean;
     hasSecurityGuard: boolean;
     hasFireSafetyCert: boolean;
     curfewHours: string;
   }
   ```

## 3. File Task Mẫu Thực Chiến Cho FE-01 Agent

### Mẫu 1: `tasks/sprint-1/FE1-FT06-room-detail-and-post.md` (Sprint 1)
* **Mục tiêu:** Xây dựng trang Chi tiết phòng trọ, hiển thị trực quan danh sách tiện nghi (máy giặt, lọc nước, nóng lạnh, internet), khối chứng nhận An ninh khu trọ (`FT-02`) và Form đăng phòng trọ đa bước cho chủ trọ.
* **Yêu cầu UI/UX:**
  - Hiển thị lưới Icon + Nhãn tiếng Việt cho từng tiện nghi có trong phòng (những tiện nghi không có thì làm mờ kèm gạch ngang để người thuê nhìn rõ ngay lập tức).
  - Hiển thị khối **"An ninh khu trọ"** nổi bật với các huy hiệu xanh lá: Camera 24/7, Khóa vân tay, PCCC đạt chuẩn và Giờ giấc đóng mở cửa.
  - Form đăng phòng chia làm 3 bước: (1) Địa chỉ & Diện tích/Giá -> (2) Tích chọn tiện nghi & An ninh -> (3) Kéo thả tải ảnh phòng.
* **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - [ ] Giao diện Responsive hoàn chỉnh trên cả Mobile và Desktop.
  - [ ] Kiểm tra hợp lệ dữ liệu trên Form trước khi gửi (không cho nhập giá âm, bắt buộc chọn ít nhất 1 ảnh).
  - [ ] Hoạt động mượt với cả Mock Data và API thật của BE-01.

### Mẫu 2: `tasks/sprint-4/FE1-FT14-FT15-map-and-utility-ui.md` (Sprint 4)
* **Mục tiêu:** Tích hợp bản đồ Leaflet hiển thị tiện ích xung quanh (`FT-14`) và bảng điều khiển dự đoán số điện nước tiêu thụ hàng tháng (`FT-15`).
* **Yêu cầu UI/UX:**
  - **Bản đồ tiện ích (`FT-14`):** Hiển thị ghim (Marker) phòng trọ ở chính giữa, vẽ vòng tròn bán kính `500m` và `1km`. Cho phép bật/tắt các lớp tiện ích: 🍱 Nhà ăn/Quán cơm, 🛒 Siêu thị/Chợ, 🌳 Công viên, 🚌 Trạm xe buýt. Khi bấm vào 1 điểm tiện ích, hiển thị khoảng cách (m) và số phút đi bộ.
  - **Dự đoán điện nước (`FT-15`):** Cho phép người thuê tích chọn các thiết bị điện định mang vào phòng (Điều hòa, Bình nóng lạnh, Máy giặt, Ấm siêu tốc, Tủ lạnh, Bếp từ...), điều chỉnh thanh trượt (Slider) số giờ dùng mỗi ngày -> Hiển thị ngay biểu đồ tròn phân bổ kWh và tổng số tiền điện nước dự kiến mỗi tháng.
* **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - [ ] Bản đồ không bị giật lag khi hiển thị 20+ điểm tiện ích cùng lúc.
  - [ ] Khi thay đổi số giờ sử dụng thiết bị trên thanh trượt, biểu đồ và số tiền dự báo cập nhật mượt mà kèm lời khuyên tiết kiệm điện.
