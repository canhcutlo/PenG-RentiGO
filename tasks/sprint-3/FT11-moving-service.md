# [FT-11] Dịch vụ chuyển trọ

## 1. Thông tin phân công
- **Sprint:** Sprint 3
- **Độ khó:** 6.5 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người thuê chuyển sang phòng trọ mới
- **Tôi muốn:** Nhập địa chỉ đi/đến, chọn loại phương tiện (xe ba gác, xe tải 500kg, xe tải 1 tấn), số tầng lầu (không có thang máy) để ước tính chi phí và đặt dịch vụ chuyển trọ
- **Để:** Tiết kiệm công sức dọn đồ với chi phí minh bạch, không bị ép giá.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `MovingBookings`
  - `Id`, `TenantId`, `PickupAddress`, `DropoffAddress`, `DistanceKm`, `VehicleType` (`BaGac`, `Truck500Kg`, `Truck1Ton`), `FloorCountNoElevator`, `NeedHelper`, `EstimatedTotalFee`, `Status` (`Draft`, `Booked`, `InTransit`, `Completed`, `Cancelled`)
- **Công cụ tính giá (`MovingPriceCalculator`):**
  - Công thức: `BaseFee(VehicleType) + (DistanceKm * RatePerKm) + (FloorCount * FloorRate) + (NeedHelper ? HelperFee : 0)`
- **Endpoints cần xây dựng:**
  - `POST /api/v1/moving/estimate`: Tính nhanh chi phí dự kiến trước khi đặt.
  - `POST /api/v1/moving/book`: Xác nhận đặt chuyến chuyển trọ.
  - `GET /api/v1/moving/my-bookings`: Xem lịch sử và trạng thái chuyến xe.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/moving-service/`
  - `MovingEstimatorForm`: Form chọn xe trực quan (hình minh họa kích thước thùng xe, tải trọng).
  - `CostBreakdownCard`: Thẻ chi tiết từng khoản phụ phí (khoảng cách, tầng lầu thang bộ, người phụ bốc vác).
  - `BookingTimeline`: Theo dõi tiến độ chuyển dọn.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Ước tính phí chính xác):** Given quãng đường 10km, xe tải 500kg và 2 tầng lầu thang bộ, When gọi `POST /api/v1/moving/estimate`, Then trả về chi tiết từng khoản phí và tổng số tiền chuẩn xác.
- [ ] **AC-02 (Đặt chuyến):** Given thông tin người đặt và thời gian hợp lệ, When gọi `POST /api/v1/moving/book`, Then lưu booking và tạo trạng thái `Booked`.
- [ ] **AC-03 (Bắt lỗi):** Given quãng đường `DistanceKm <= 0`, When gọi tính phí, Then trả về HTTP `400 Bad Request`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra các trường hợp tính giá của `MovingPriceCalculator`.
