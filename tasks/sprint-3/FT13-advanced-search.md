# [FT-13] Tìm kiếm phòng trọ đa tiêu chí

## 1. Thông tin phân công
- **Sprint:** Sprint 3
- **Độ khó:** 7.5 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người có nhu cầu tìm phòng cụ thể
- **Tôi muốn:** Lọc phòng theo nhiều tiêu chí đồng thời (khoảng giá, khu vực quận/huyện, bán kính km, danh sách tiện nghi bắt buộc, tiêu chuẩn an ninh, cho phép nuôi thú cưng)
- **Để:** Tìm được đúng căn phòng ưng ý nhanh nhất mà không phải xem qua hàng trăm tin không phù hợp.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `Rooms`, `BoardingHouses`, `RoomAmenities`, `Amenities`
  - Áp dụng Specification Pattern hoặc Dynamic LINQ để ghép nối linh hoạt các điều kiện truy vấn.
  - Tối ưu hóa SQL Indexes (Index trên `MonthlyRentPrice`, `City`, `District`, `Status`).
  - Cache kết quả danh mục và các bộ lọc phổ biến vào Memory Cache / Redis.
- **Endpoints cần xây dựng:**
  - `GET /api/v1/rooms/search`: Tìm kiếm nâng cao với các Query Parameters: `minPrice`, `maxPrice`, `district`, `amenities`, `hasCamera`, `curfew`, `sortBy`, `pageNumber`, `pageSize`.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/search/`
  - `SearchFilterDrawer`: Thanh/Ngăn kéo bộ lọc đa tiêu chí (Dual Slider chọn giá, Checkbox tiện nghi & an ninh).
  - `ActiveFilterChips`: Danh sách các Tag tiêu chí đang chọn có nút (X) để xóa nhanh.
  - `SearchResultList`: Danh sách kết quả kèm phân trang hoặc Infinite Scroll.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Tìm kiếm kết hợp):** Given người dùng chọn giá `2.000.000 - 3.500.000` và tiện nghi có `AIR_CONDITIONER` + `WASHING_MACHINE`, When gọi API tìm kiếm, Then chỉ trả về các phòng thỏa mãn đồng thời tất cả các điều kiện.
- [ ] **AC-02 (Sắp xếp kết quả):** When chọn sắp xếp `sortBy=price_asc`, Then kết quả trả về được sắp xếp theo giá thuê tăng dần.
- [ ] **AC-03 (Phân trang chuẩn):** Given kết quả có 50 phòng, When yêu cầu `pageSize=10&pageNumber=1`, Then trả về đúng 10 phòng và metadata tổng số trang `totalPages=5`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 4 Unit Tests (xUnit) kiểm tra Specification ghép nối điều kiện lọc và phân trang.
