# [FT-09] Chia sẻ, thuê, bán đồ thiết yếu

## 1. Thông tin phân công
- **Sprint:** Sprint 2
- **Độ khó:** 5.5 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người thuê trọ cần vật dụng ngắn hạn hoặc muốn thanh lý đồ đạc
- **Tôi muốn:** Tìm mua thanh lý, nhận đồ chia sẻ miễn phí hoặc đặt thuê theo tháng các vật dụng sinh hoạt (quạt cây, đệm gấp, ấm đun nước, bàn học...)
- **Để:** Tiết kiệm chi phí mua sắm đồ mới và tránh lãng phí khi chuyển phòng trọ.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `EssentialItems`, `ItemRentalOrders`
  - `EssentialItem`: `Id`, `OwnerId`, `BoardingHouseId`, `ItemName`, `TransactionType` (`FreeShare`, `Sell`, `Rent`), `SalePrice`, `RentalPricePerMonth`, `DepositAmount`, `AvailableQuantity`, `Condition`
  - `ItemRentalOrder`: `Id`, `ItemId`, `RenterId`, `RentalMonths`, `TotalRentalFee`, `DepositAmount`, `Status` (`PendingHandover`, `Active`, `Returned`, `Cancelled`), `IsDepositRefunded`
- **Endpoints cần xây dựng:**
  - `GET /api/v1/marketplace/items`: Tìm kiếm và lọc danh mục đồ thiết yếu theo loại giao dịch (Thuê/Mua/Tặng).
  - `POST /api/v1/marketplace/items`: Đăng vật phẩm mới lên chợ.
  - `POST /api/v1/marketplace/orders`: Tạo đơn đặt thuê hoặc mua đồ (tính toán tự động tiền thuê + tiền cọc).

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/essential-marketplace/`
  - `MarketplaceFeed`: Bộ lọc theo Tab (Tất cả / Cho thuê / Mua thanh lý / Tặng miễn phí 0đ).
  - `ItemCard`: Thẻ hiển thị giá thuê/tháng, tiền cọc, độ mới của vật dụng.
  - `RentOrderModal`: Hộp thoại chọn ngày bắt đầu & kết thúc thuê -> tự động tính tổng tiền thanh toán.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Đăng món đồ hợp lệ):** Given thông tin vật dụng đầy đủ, When người dùng đăng món đồ, Then lưu thành công với `TransactionType` tương ứng và trả về HTTP `201 Created`.
- [ ] **AC-02 (Tính tiền thuê và cọc chính xác):** Given món đồ có giá thuê 50.000đ/tháng và cọc 200.000đ, When người dùng đặt thuê 3 tháng, Then đơn hàng tính tổng tiền = (50.000 * 3) + 200.000 = 350.000đ.
- [ ] **AC-03 (Chặn đặt vượt số lượng):** Given `AvailableQuantity = 1`, When có yêu cầu đặt thuê số lượng 2, Then hệ thống từ chối và trả về HTTP `400 Bad Request`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra việc đăng bán/cho thuê và công thức tính tiền cọc đơn hàng.
