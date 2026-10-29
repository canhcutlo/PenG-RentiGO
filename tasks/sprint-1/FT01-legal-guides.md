# [FT-01] Hỗ trợ thủ tục hành chính và hợp đồng

## 1. Thông tin phân công
- **Sprint:** Sprint 1
- **Độ khó:** 1.5 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người thuê trọ mới hoặc chủ trọ
- **Tôi muốn:** Đọc các bài viết hướng dẫn về thủ tục đăng ký tạm trú, nội quy phòng cháy chữa cháy (PCCC) và tải về các mẫu hợp đồng thuê trọ chuẩn pháp lý (`.docx`, `.pdf`)
- **Để:** Nắm rõ quy định pháp luật và thực hiện ký kết hợp đồng minh bạch, đúng quy chuẩn.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `LegalGuides`, `ContractTemplates`
  - `LegalGuide`: `Id`, `Title`, `Slug`, `Category` (`TamTru`, `HopDong`, `PCCC`, `NoiQuy`), `MarkdownContent`, `ReadingTimeMinutes`
  - `ContractTemplate`: `Id`, `Title`, `Description`, `FileDownloadUrl`, `FileType` (`Docx`, `Pdf`), `Version`
- **Endpoints cần xây dựng:**
  - `GET /api/v1/guides?category={category}`: Lấy danh sách bài viết hướng dẫn theo danh mục.
  - `GET /api/v1/guides/{slug}`: Lấy chi tiết nội dung bài viết hướng dẫn (Markdown).
  - `GET /api/v1/guides/templates`: Lấy danh sách mẫu hợp đồng có sẵn.
  - `GET /api/v1/guides/templates/{id}/download`: Tải file mẫu hợp đồng với `Content-Disposition: attachment`.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/legal-guides/`
  - `GuideListPage`: Danh sách cẩm nang phân loại theo Tab (Tạm trú, Hợp đồng, PCCC).
  - `GuideDetailPage`: Trình xem nội dung Markdown chi tiết.
  - `TemplateDownloadCard`: Card thông tin mẫu hợp đồng kèm nút tải về nhanh.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Lấy danh mục cẩm nang):** Given danh mục `category=TamTru`, When người dùng gọi `GET /api/v1/guides?category=TamTru`, Then trả về HTTP `200 OK` kèm danh sách bài viết Markdown và thời gian đọc ước tính.
- [ ] **AC-02 (Tải file hợp đồng mẫu):** When người dùng bấm tải mẫu hợp đồng qua `GET /api/v1/guides/templates/{id}/download`, Then trả về file `.docx` hoặc `.pdf` chuẩn kèm header `Content-Disposition: attachment`.
- [ ] **AC-03 (Bắt lỗi bài viết không tồn tại):** Given slug bài viết không tồn tại trong hệ thống, When gọi `GET /api/v1/guides/{slug}`, Then trả về HTTP `404 Not Found` kèm thông báo lỗi rõ ràng.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra Service lấy danh sách bài viết, xem chi tiết và tải template.
