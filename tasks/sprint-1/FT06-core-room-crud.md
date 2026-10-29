# [FT-06] Thông tin phòng trọ và cho thuê (Core CRUD)

## 1. Thông tin phân công
- **Sprint:** Sprint 1
- **Độ khó:** 4.0 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Chủ trọ hoặc người thuê trọ
- **Tôi muốn:** Đăng tin cho thuê phòng chi tiết (giá thuê, tiền điện/nước, diện tích, tiện nghi kèm theo, hình ảnh) và xem danh sách chi tiết các phòng trọ đang cho thuê
- **Để:** Chủ trọ dễ dàng quản lý phòng và người thuê tìm được căn phòng ưng ý với đầy đủ thông tin minh bạch.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `BoardingHouses`, `Rooms`, `Amenities`, `RoomAmenities`, `RoomMedias`
  - Quan hệ N-N giữa `Room` và `Amenity` thông qua `RoomAmenity`.
  - Hỗ trợ tải nhiều ảnh phòng qua `RoomMedias`.
- **Validation (FluentValidation):**
  - `AreaM2 > 0`, `MonthlyRentPrice > 0`, `ElectricityPricePerKwh >= 0`, `WaterPricePerM3 >= 0`.
- **Endpoints cần xây dựng:**
  - `GET /api/v1/rooms`: Lấy danh sách phòng trọ có phân trang và bộ lọc cơ bản.
  - `GET /api/v1/rooms/{id}`: Xem chi tiết phòng trọ kèm danh sách tiện nghi và an ninh.
  - `POST /api/v1/rooms`: Đăng tin phòng trọ mới (chủ trọ).
  - `PUT /api/v1/rooms/{id}`: Chỉnh sửa thông tin phòng.
  - `DELETE /api/v1/rooms/{id}`: Xóa mềm (Soft Delete) phòng trọ.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/rooms/`
  - `RoomCard`: Thẻ hiển thị phòng ngoài trang chủ/danh sách.
  - `RoomDetailPage`: Trang chi tiết phòng trọ với thư viện ảnh Slider, tiện nghi, thông tin chủ nhà.
  - `CreateRoomForm`: Form đa bước (Thông tin cơ bản -> Tiện nghi & An ninh -> Tải ảnh).

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Đăng phòng hợp lệ):** Given thông tin phòng hợp lệ và danh sách mã tiện nghi `amenityCodes`, When gọi `POST /api/v1/rooms`, Then phòng được lưu thành công kèm liên kết tiện nghi, trả về HTTP `201 Created`.
- [ ] **AC-02 (Bắt lỗi validation):** Given diện tích `areaM2 <= 0` hoặc giá thuê `monthlyRentPrice <= 0`, When gửi request, Then FluentValidation chặn lại và trả về HTTP `400 Bad Request`.
- [ ] **AC-03 (Lấy chi tiết phòng):** When người dùng gọi `GET /api/v1/rooms/{id}`, Then trả về đầy đủ tiện nghi, thông tin an ninh, giá điện nước và mảng URL hình ảnh.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 4 Unit Tests (xUnit) cho RoomService (Create, GetById, Update, Delete).
