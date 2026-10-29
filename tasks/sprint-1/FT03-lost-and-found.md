# [FT-03] Thông báo tìm đồ bị mất (Lost & Found)

## 1. Thông tin phân công
- **Sprint:** Sprint 1
- **Độ khó:** 2.5 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Cư dân khu trọ
- **Tôi muốn:** Đăng tin tìm đồ bị mất hoặc thông báo nhặt được đồ thất lạc kèm hình ảnh và số điện thoại liên hệ
- **Để:** Nhanh chóng nhận lại hoặc bàn giao lại tài sản bị thất lạc trong khu vực trọ.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `LostFoundPosts`
  - `Id`, `AuthorId`, `BoardingHouseId` (nullable), `PostType` (`Lost` | `Found`), `ItemName`, `Description`, `ImageUrl`, `ContactPhone`, `IsResolved` (bool)
- **Endpoints cần xây dựng:**
  - `GET /api/v1/lost-found`: Lấy danh sách tin tìm/nhặt được đồ theo bộ lọc `type` và `boardingHouseId`.
  - `POST /api/v1/lost-found`: Tạo tin báo mất hoặc nhặt được đồ mới (kèm ảnh).
  - `PATCH /api/v1/lost-found/{id}/resolve`: Đánh dấu đã tìm thấy/đã bàn giao đồ (chỉ người tạo tin).

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/lost-and-found/`
  - `LostFoundFeed`: Bảng tin hiển thị danh sách dạng Card (phân biệt tag màu đỏ "Tìm đồ mất" và màu xanh "Nhặt được đồ").
  - `CreateLostFoundModal`: Modal đăng bài với upload ảnh xem trước và nhập số điện thoại.
  - `ResolveButton`: Nút đánh dấu đã giải quyết xong để chuyển trạng thái bài viết.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Đăng tin thành công):** Given thông tin hợp lệ (tên món đồ, mô tả, số điện thoại), When người dùng gọi `POST /api/v1/lost-found`, Then bài đăng được lưu và trả về HTTP `201 Created`.
- [ ] **AC-02 (Lọc theo loại tin):** When người dùng lọc theo `postType=Lost`, Then danh sách chỉ hiển thị các tin tìm đồ bị mất chưa giải quyết.
- [ ] **AC-03 (Đánh dấu đã giải quyết):** When tác giả nhấn hoàn tất và gọi `PATCH /api/v1/lost-found/{id}/resolve`, Then `IsResolved` chuyển thành `true` và bài viết hiển thị nhãn "Đã tìm thấy".
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra CRUD bảng tin Lost & Found.
