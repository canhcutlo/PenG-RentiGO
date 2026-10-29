# [FT-02] Thông tin an ninh khu trọ

## 1. Thông tin phân công
- **Sprint:** Sprint 1
- **Độ khó:** 2.0 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người tìm phòng trọ (đặc biệt là sinh viên, nữ giới)
- **Tôi muốn:** Xem rõ các thông tin an ninh của khu trọ (Camera 24/7, khóa vân tay, bảo vệ, chứng nhận PCCC, giờ giới nghiêm)
- **Để:** An tâm về an toàn tài sản và tính mạng khi chuyển đến ở.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `BoardingHouses`
  - Cột an ninh: `HasCamera24x7` (bool), `HasFingerprintLock` (bool), `HasSecurityGuard` (bool), `HasFireSafetyCert` (bool), `CurfewHours` (string), `SecurityNotes` (string?), `SecurityRating` (double)
- **Endpoints cần xây dựng:**
  - `GET /api/v1/boarding-houses/{id}/security`: Lấy thông tin chi tiết các tiêu chuẩn an ninh và badge an toàn của khu trọ.
  - `PUT /api/v1/boarding-houses/{id}/security`: Cập nhật thông số an ninh (chỉ Chủ trọ sở hữu khu trọ).

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/security-badges/`
  - `SecurityBadges`: Hiển thị danh sách huy hiệu trực quan (Camera 24/7, Khóa vân tay, Bảo vệ, PCCC) với màu sắc nổi bật.
  - `CurfewNotice`: Thẻ thông báo giờ giấc ra vào (ví dụ: "Tự do 24/24" hoặc "Đóng cửa 23:30").
  - `SecurityFormSection`: Form tích chọn các chỉ số an ninh khi đăng bài hoặc cập nhật nhà trọ.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Hiển thị huy hiệu an ninh):** Given nhà trọ có đầy đủ Camera và Khóa vân tay, When người dùng xem thông tin qua `GET /api/v1/boarding-houses/{id}/security`, Then trả về HTTP `200 OK` kèm cờ an ninh và danh sách huy hiệu tương ứng.
- [ ] **AC-02 (Cập nhật thông tin an ninh):** Given chủ trọ gửi thông tin an ninh hợp lệ, When gọi `PUT /api/v1/boarding-houses/{id}/security`, Then dữ liệu an ninh được cập nhật vào bảng `BoardingHouses` và trả về HTTP `200 OK`.
- [ ] **AC-03 (Phân quyền):** Given người dùng không phải là chủ sở hữu nhà trọ, When gọi cập nhật an ninh, Then trả về HTTP `403 Forbidden`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra việc lấy và cập nhật thông tin an ninh.
