# [FT-07] Đánh giá phòng trọ

## 1. Thông tin phân công
- **Sprint:** Sprint 2
- **Độ khó:** 4.5 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người đã hoặc đang thuê trọ
- **Tôi muốn:** Viết bài đánh giá chấm điểm số sao (1-5 sao) kèm hình ảnh thực tế và nhận xét trung thực về chất lượng phòng, thái độ chủ nhà
- **Để:** Giúp cộng đồng người thuê khác có cái nhìn khách quan và giúp chủ trọ cải thiện dịch vụ.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `RoomReviews`, `ReviewMedias`, `TenantVerifications`
  - Cần Custom Authorization Handler: Chỉ những người dùng đã được xác thực từng thuê phòng này (`Verified Tenant`) mới được phép gửi đánh giá.
  - Tự động tính toán lại điểm trung bình `AverageRating` của phòng (`Room`) sau mỗi đánh giá mới.
- **Endpoints cần xây dựng:**
  - `GET /api/v1/rooms/{roomId}/reviews`: Lấy danh sách đánh giá kèm ảnh và phân tích phân bổ sao.
  - `POST /api/v1/rooms/{roomId}/reviews`: Gửi đánh giá mới (số sao từ 1 đến 5, nhận xét, ảnh đính kèm).

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/reviews/`
  - `RatingSummary`: Hiển thị số sao trung bình và thanh phần trăm phân bổ 1-5 sao.
  - `ReviewList`: Danh sách bình luận kèm huy hiệu "Người thuê thực tế" (Verified Tenant).
  - `CreateReviewModal`: Form đánh giá sao, nhập nhận xét và tải ảnh thực tế.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Đánh giá từ người thuê đã xác thực):** Given người thuê đã được xác thực, When gửi đánh giá 5 sao kèm bình luận hợp lệ, Then hệ thống lưu đánh giá và cập nhật `AverageRating` của phòng.
- [ ] **AC-02 (Chặn đánh giá ảo):** Given người dùng chưa từng thuê phòng này, When gọi `POST /api/v1/rooms/{roomId}/reviews`, Then hệ thống trả về HTTP `403 Forbidden` (Chỉ người thuê thực tế mới được đánh giá).
- [ ] **AC-03 (Bắt lỗi số sao ngoài phạm vi):** Given số sao `< 1` hoặc `> 5`, When gửi request, Then trả về HTTP `400 Bad Request`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra tính toán lại `AverageRating` và phân quyền người thuê.
