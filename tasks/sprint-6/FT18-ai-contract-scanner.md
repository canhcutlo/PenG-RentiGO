# [FT-18] AI Quét Hợp đồng tìm "Điều khoản bẫy"

## 1. Thông tin phân công
- **Sprint:** Sprint 6
- **Độ khó:** 9.5 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người đi thuê trọ chuẩn bị ký kết hợp đồng
- **Tôi muốn:** Tải lên file ảnh chụp hoặc file PDF hợp đồng thuê trọ để hệ thống AI tự động đọc văn bản và cảnh báo các "điều khoản bẫy" rủi ro pháp lý (tự ý tăng giá vô lý, mất trắng tiền cọc, phạt vi phạm bất thường, tính giá điện nước sai quy định...)
- **Để:** Bảo vệ quyền lợi hợp pháp của bản thân, tự tin đàm phán sửa đổi điều khoản trước khi đặt bút ký hợp đồng.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `ContractScanJobs`, `DetectedTrapClauses`
  - `ContractScanJob`: `Id`, `UserId`, `FileUrl`, `FileType`, `ExtractedOcrText`, `Status` (`Processing`, `Completed`, `Failed`), `CreatedAt`
  - `DetectedTrapClause`: `Id`, `JobId`, `OriginalClauseText`, `RiskLevel` (`HIGH`, `MEDIUM`, `LOW`), `Explanation`, `SuggestedRevision`
- **Công nghệ bóc tách & AI:**
  - Bóc tách văn bản: `PdfPig` cho file tài liệu số và `Tesseract OCR` xử lý file ảnh chụp tiếng Việt.
  - Phân tích rủi ro: `Microsoft.SemanticKernel` kết hợp Large Language Model xuất kết quả chuẩn JSON theo Schema định sẵn.
- **Endpoints cần xây dựng:**
  - `POST /api/v1/ai-contract/scan`: Tiếp nhận file hợp đồng (PDF, PNG, JPG), kích hoạt quy trình OCR và phân tích AI.
  - `GET /api/v1/ai-contract/jobs/{jobId}`: Lấy kết quả phân tích chi tiết các điều khoản bẫy phát hiện được.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/contract-scanner/`
  - `ContractUploadDropzone`: Khu vực kéo thả file hợp đồng (hỗ trợ xem trước trang PDF hoặc ảnh).
  - `ScanProgressIndicator`: Thanh hiển thị tiến trình quét (Đang đọc OCR -> Đang phân tích rủi ro -> Hoàn tất).
  - `TrapClauseAlertCard`: Thẻ cảnh báo phân loại theo màu sắc (Đỏ: Rủi ro cao, Vàng: Rủi ro vừa) kèm văn bản gốc, giải thích lý do và gợi ý câu chữ thay thế an toàn.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Quét văn bản và phát hiện điều khoản bẫy):** Given file hợp đồng chứa điều khoản "Chủ nhà có quyền tăng giá thuê bất kỳ lúc nào", When gọi `POST /api/v1/ai-contract/scan`, Then hệ thống trả về kết quả phân tích có chứa cảnh báo mức `HIGH` kèm lý do vi phạm và văn bản đề xuất sửa đổi.
- [ ] **AC-02 (Cấu trúc dữ liệu JSON chuẩn xác):** Then mảng `detectedTraps` trả về đúng định dạng có đầy đủ các trường `originalClauseText`, `riskLevel`, `explanation` và `suggestedRevision`.
- [ ] **AC-03 (Bắt lỗi định dạng file):** Given file tải lên không phải là `.pdf`, `.png` hoặc `.jpg`, When gửi request, Then hệ thống từ chối và trả về HTTP `400 Bad Request` kèm thông báo định dạng không được hỗ trợ.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 4 Unit Tests (xUnit) kiểm tra Service phân tích văn bản hợp đồng và Mock AI Engine.
