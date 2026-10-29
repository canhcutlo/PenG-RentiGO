# Bộ Tài Liệu Điều Phối Dành Cho Scrum Master (SCRUM_MASTER_KIT.md)

Tài liệu này dành riêng cho **Scrum Master (SM-01)** và Agent hỗ trợ quản lý dự án của bạn. Nhiệm vụ của Scrum Master trong mô hình Multi-Agent không phải là ngồi gõ từng dòng code tính năng, mà là **Kiến trúc sư Ngữ cảnh (Context Architect)**: biến yêu cầu thành các file Markdown rõ ràng để 4 Agent của 4 bạn Dev (2 BE, 2 FE) chạy chính xác, không lệch hướng và không đụng độ file.

## 1. Bảng Phân Bổ 18 Tính Năng Cho 4 Dev (Từ Dễ Đến Khó)

| Thứ tự | Mã TN | Tên tính năng | Độ khó (1-10) | Sprint | Backend phụ trách | Frontend phụ trách | Đường dẫn file Task Markdown cần tạo |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | **FT-01** | Hỗ trợ thủ tục hành chính và hợp đồng | 1.5 | Sprint 1 | BE-02 | FE-02 | `tasks/sprint-1/FT01-legal-guides.md` |
| 2 | **FT-02** | Thông tin an ninh khu trọ | 2.0 | Sprint 1 | BE-01 | FE-01 | `tasks/sprint-1/FT02-security-info.md` |
| 3 | **FT-03** | Thông báo tìm đồ bị mất | 2.5 | Sprint 1 | BE-02 | FE-02 | `tasks/sprint-1/FT03-lost-and-found.md` |
| 4 | **FT-04** | Thông tin tuyển việc vặt | 3.0 | Sprint 1 | BE-02 | FE-02 | `tasks/sprint-1/FT04-odd-jobs.md` |
| 5 | **FT-05** | Quản lý vệ sinh khu vực trọ | 3.5 | Sprint 2 | BE-02 | FE-02 | `tasks/sprint-2/FT05-cleaning-schedule.md` |
| 6 | **FT-06** | Thông tin phòng trọ và cho thuê (Core) | 4.0 | Sprint 1 | BE-01 | FE-01 | `tasks/sprint-1/FT06-core-room-crud.md` |
| 7 | **FT-07** | Đánh giá phòng trọ | 4.5 | Sprint 2 | BE-01 | FE-02 | `tasks/sprint-2/FT07-room-reviews.md` |
| 8 | **FT-08** | Trang báo cáo (hỏng thiết bị, tin sai...) | 5.0 | Sprint 2 | BE-02 | FE-02 | `tasks/sprint-2/FT08-issue-reporting.md` |
| 9 | **FT-09** | Chia sẻ, thuê, bán đồ thiết yếu | 5.5 | Sprint 2 | BE-02 | FE-02 | `tasks/sprint-2/FT09-essential-items.md` |
| 10 | **FT-10** | Ghép phòng trọ & Đi chung xe | 6.0 | Sprint 3 | BE-02 | FE-01 | `tasks/sprint-3/FT10-roommate-carpool.md` |
| 11 | **FT-11** | Dịch vụ chuyển trọ | 6.5 | Sprint 3 | BE-02 | FE-02 | `tasks/sprint-3/FT11-moving-service.md` |
| 12 | **FT-12** | Loại phòng trọ cho thuê ngắn hạn | 7.0 | Sprint 3 | BE-01 | FE-01 | `tasks/sprint-3/FT12-short-term-rental.md` |
| 13 | **FT-13** | Tìm kiếm phòng trọ đa tiêu chí | 7.5 | Sprint 3 | BE-01 | FE-01 | `tasks/sprint-3/FT13-advanced-search.md` |
| 14 | **FT-14** | Hiển thị khu vực tiện ích ở gần (GIS) | 8.0 | Sprint 4 | BE-01 | FE-01 | `tasks/sprint-4/FT14-nearby-utilities-map.md` |
| 15 | **FT-15** | Dự đoán số điện nước tiêu thụ (ML.NET) | 8.5 | Sprint 4 | BE-01 | FE-01 | `tasks/sprint-4/FT15-utility-prediction.md` |
| 16 | **FT-16** | Tổng đài CSKH bằng BOT (AI RAG) | 8.5 | Sprint 5 | BE-02 | FE-02 | `tasks/sprint-5/FT16-ai-chatbot-cskh.md` |
| 17 | **FT-17** | Thanh toán online (VNPay / VietQR) | 9.0 | Sprint 5 | BE-01 | FE-01 | `tasks/sprint-5/FT17-online-payment.md` |
| 18 | **FT-18** | AI Quét Hợp đồng tìm "Điều khoản bẫy" | 9.5 | Sprint 6 | BE-01 | FE-01 | `tasks/sprint-6/FT18-ai-contract-scanner.md` |

## 2. Mẫu File Giao Việc Chuẩn (`.agent/templates/task-template.md`)

Khi bắt đầu mỗi Sprint, Scrum Master yêu cầu Agent của mình dùng mẫu dưới đây để sinh ra các file task cho từng Dev:

```markdown
# [MÃ TÍNH NĂNG] Tên Tính Năng Cụ Thể

## 1. Thông tin phân công
- **Sprint:** Sprint X
- **Độ khó:** X.X / 10
- **Backend phụ trách:** BE-01 hoặc BE-02
- **Frontend phụ trách:** FE-01 hoặc FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Hợp đồng API: `/docs/api/<ten-file-api>.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** [Người thuê trọ / Chủ trọ / Quản trị viên]
- **Tôi muốn:** [Hành động cụ thể]
- **Để:** [Giá trị nhận được]

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** [Tên các bảng trong PenG.RentiGO.Domain]
- **Business Logic & Validation:**
  - [Quy tắc kiểm tra dữ liệu 1]
  - [Quy tắc xử lý nghiệp vụ 2]
- **Endpoints cần xây dựng:**
  - `GET /api/v1/...`
  - `POST /api/v1/...`

## 4. Yêu cầu kỹ thuật Frontend
- **Màn hình / Component cần tạo:** `client/src/features/<ten-module>/`
- **Luồng tương tác người dùng:**
  - [Trạng thái Loading, Empty, Error, Success]
  - [Các trường trên Form và bộ lọc]

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Luồng hợp lệ):** Given [ngữ cảnh], When [hành động], Then [kết quả mong đợi].
- [ ] **AC-02 (Luồng bắt lỗi):** Given [dữ liệu thiếu/sai], When [gửi request], Then trả về mã `400 Bad Request` kèm thông báo lỗi rõ ràng.
- [ ] **AC-03 (Phân quyền):** Given [người dùng không có quyền], When [gọi API], Then trả về `403 Forbidden`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) cho tầng Application Service.

## 6. Câu lệnh Prompt gợi ý cho Dev nạp vào Agent
> "Hãy đọc kỹ @.agent/AGENTS.md và file task hiện tại. Nếu bạn là Backend Agent, hãy triển khai đầy đủ Entity, DTO, Validator, Service, Controller và Unit Test. Nếu bạn là Frontend Agent, hãy tạo Mock Service theo đúng API Spec và xây dựng giao diện hoàn chỉnh."
```

## 3. Mẫu Kiểm Tra Pull Request (`.agent/templates/pr-template.md`)

Lưu nội dung này vào `.github/PULL_REQUEST_TEMPLATE.md` để mỗi khi Dev đẩy code lên, Agent của Scrum Master có thể review tự động:

```markdown
## Thông tin Pull Request
- **Mã tính năng & Task:** [Ví dụ: FT-06 - Thông tin phòng trọ]
- **File Task Markdown gốc:** `tasks/sprint-1/FT06-core-room-crud.md`
- **Vai trò thực hiện:** [ ] BE-01  [ ] BE-02  [ ] FE-01  [ ] FE-02

## Checklist Tự Kiểm Tra Bằng Agent Trước Khi Merge
- [ ] Code tuân thủ đúng cấu trúc thư mục trong `.agent/AGENTS.md`.
- [ ] JSON Request/Response khớp 100% với hợp đồng trong `docs/api/`.
- [ ] Đã hoàn thành toàn bộ các mục Acceptance Criteria (`AC-01` -> `AC-04`) trong file Task Markdown.
- [ ] Đã chạy `dotnet build` và `dotnet test` thành công (đối với Backend) hoặc `npm run build` không lỗi (đối với Frontend).
```

## 4. Quy Trình 15 Phút Đầu Mỗi Sprint Dành Cho Scrum Master

1. **Ngày đầu Sprint:** Mở Agent của SM, nạp file `SCRUM_MASTER_KIT.md` này và yêu cầu Agent sinh ra các file task chi tiết vào thư mục `tasks/sprint-X/`.
2. **Họp chốt API Contract (15 phút):** Cùng 2 BE và 2 FE duyệt nhanh các đường dẫn API và tên trường JSON trong `docs/api/`, sau đó commit lên nhánh `main`.
3. **Phát lệnh chạy song song:** 4 Dev pull nhánh `main` về máy, mỗi người mở nhánh riêng (`feat/BE1-FT06`, `feat/FE1-FT06`...) và nạp file Markdown vào Agent cá nhân để code độc lập mà không ai phải chờ ai.
