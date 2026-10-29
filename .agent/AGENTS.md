# Quy Chuẩn Dự Án & Hướng Dẫn Dành Cho Coding Agents (AGENTS.md)

Tài liệu này đóng vai trò là **System Prompt / Ngữ cảnh gốc (Root Context)** cho mọi Coding Agent (Claude Code, Cursor, Codex, Windsurf, Antigravity...) tham gia vào dự án **PenG-RentiGO** — Ứng dụng Đăng, Tìm và Quản lý Phòng Trọ Thông Minh.

Mọi Agent của tất cả thành viên (1 Scrum Master, 2 Backend Devs, 2 Frontend Devs) **bắt buộc phải đọc tệp này trước khi sinh mã nguồn hoặc tạo tài liệu**.

## 1. Kiến Trúc Tổng Quan & Công Nghệ Sử Dụng (Tech Stack)

| Tầng (Layer) | Công nghệ chuẩn | Phiên bản & Thư viện bắt buộc |
| :--- | :--- | :--- |
| **Backend Core** | C# 12 / ASP.NET Core Web API | `.NET 8.0 LTS`, Clean Architecture |
| **Database & ORM** | SQL Server hoặc PostgreSQL | `Entity Framework Core 8`, Code-First Migrations, `NetTopologySuite` (GIS) |
| **Validation & Mapping** | Kiểm tra dữ liệu & ánh xạ DTO | `FluentValidation`, `Mapperly` hoặc `AutoMapper` |
| **Real-time & Jobs** | Thông báo tức thời & tác vụ định kỳ | `ASP.NET Core SignalR`, `Hangfire` |
| **AI & Machine Learning** | Xử lý hợp đồng, Chatbot & Dự báo điện nước | `Microsoft.SemanticKernel`, `ML.NET`, `PdfPig` / `Tesseract OCR` |
| **Frontend** | Giao diện người dùng | `React 18 + TypeScript + Vite + TailwindCSS` (hoặc `Blazor Web App`) |
| **Giao thức phối hợp** | Quản lý Task & API Contract | Markdown (`.md`) lưu trữ trực tiếp trong Git Repository |

## 2. Cấu Trúc Thư Mục Chuẩn Của Repository (PenG-RentiGO)

Toàn bộ nhóm thống nhất sử dụng cấu trúc thư mục dưới đây để các Agent không tạo tệp sai vị trí:

```text
PenG-RentiGO/
├── .agent/                                     # Trung tâm điều phối Multi-Agent
│   ├── AGENTS.md                               # Quy chuẩn chung cho toàn bộ Agent (File này)
│   ├── SCRUM_MASTER_KIT.md                     # Hướng dẫn & Template dành cho Scrum Master
│   └── templates/
│       ├── task-template.md                    # Mẫu giao việc chuẩn Gherkin
│       └── pr-template.md                      # Mẫu kiểm tra Pull Request
├── docs/                                       # Tài liệu kỹ thuật & Hợp đồng giao tiếp (Contracts)
│   ├── architecture.md                         # Sơ đồ kiến trúc Clean Architecture
│   ├── database-schema.md                      # Thiết kế bảng dữ liệu cho 18 tính năng
│   ├── ai/
│   │   └── contract-trap-rules.md              # Bộ quy tắc nhận diện "điều khoản bẫy" trong hợp đồng
│   └── api/                                    # Hợp đồng API giữa Backend và Frontend
│       ├── core-rooms-api.md                   # API Phòng trọ, An ninh, Tìm kiếm, Thuê ngắn hạn
│       ├── community-ops-api.md                # API Cẩm nang, Đồ thất lạc, Việc vặt, Vệ sinh, Báo cáo
│       ├── marketplace-services-api.md         # API Chợ đồ thiết yếu, Ghép phòng/xe, Chuyển trọ
│       └── smart-features-api.md               # API Bản đồ GIS, Dự báo điện nước, Chatbot, Thanh toán, AI
├── tasks/                                      # Hồ sơ phân công theo vai trò & từng Sprint
│   ├── BACKEND_DEV1_TASKS.md                   # Đặc tả & Task dành riêng cho Backend Dev 1
│   ├── BACKEND_DEV2_TASKS.md                   # Đặc tả & Task dành riêng cho Backend Dev 2
│   ├── FRONTEND_DEV1_TASKS.md                  # Đặc tả & Task dành riêng cho Frontend Dev 1
│   ├── FRONTEND_DEV2_TASKS.md                  # Đặc tả & Task dành riêng cho Frontend Dev 2
│   ├── sprint-1/                               # Các file task đơn lẻ của Sprint 1 (FT-01..FT-04, FT-06)
│   ├── sprint-2/                               # Các file task đơn lẻ của Sprint 2 (FT-05, FT-07..FT-09)
│   ├── sprint-3/                               # Các file task đơn lẻ của Sprint 3 (FT-10..FT-13)
│   ├── sprint-4/                               # Các file task đơn lẻ của Sprint 4 (FT-14, FT-15)
│   ├── sprint-5/                               # Các file task đơn lẻ của Sprint 5 (FT-16, FT-17)
│   └── sprint-6/                               # Các file task đơn lẻ của Sprint 6 (FT-18 & E2E Test)
├── src/                                        # Mã nguồn Backend C# (.NET 8)
│   ├── PenG.RentiGO.Domain/                    # Entities, ValueObjects, Enums, Domain Exceptions
│   ├── PenG.RentiGO.Application/               # Interfaces, Services, DTOs, Validators
│   ├── PenG.RentiGO.Infrastructure/            # AppDbContext, Migrations, Repositories, AI/Payment Adapters
│   └── PenG.RentiGO.WebApi/                    # Controllers, SignalR Hubs, Middlewares, Program.cs
├── tests/                                      # Kiểm thử tự động C# (xUnit + Moq)
│   ├── PenG.RentiGO.UnitTests/
│   └── PenG.RentiGO.IntegrationTests/
├── client/                                     # Mã nguồn Frontend
│   ├── src/
│   │   ├── api/                                # Axios/Fetch clients & Mock Services theo docs/api/*.md
│   │   ├── components/                         # UI Components dùng chung
│   │   ├── features/                           # Chia theo 18 module tính năng (ft01..ft18)
│   │   ├── hooks/                              # Custom hooks & SignalR connection hooks
│   │   └── types/                              # TypeScript Interfaces khớp 100% với C# DTOs
├── .gitignore
└── README.md
```

## 3. Quy Tắc Viết Code C# (.NET 8) Bắt Buộc Cho Backend Agents

1. **Tuân thủ Clean Architecture:**
   * `Domain` không được phụ thuộc vào bất kỳ thư viện ngoài nào.
   * `Application` chỉ chứa logic nghiệp vụ, DTOs, Interfaces (`IRoomService`, `IPaymentService`) và chỉ phụ thuộc vào `Domain`.
   * `Infrastructure` triển khai `DbContext`, gọi API bên thứ ba (VNPay, Google Places, Semantic Kernel) và đăng ký Dependency Injection.
   * `WebApi` chỉ nhận HTTP Request, gọi `Application Service` và trả về kết quả chuẩn. Không viết logic truy vấn `DbContext` trực tiếp trong Controller.
2. **Chuẩn hóa cấu trúc trả về API (`ApiResponse<T>`):**
   Mọi API Endpoint đều phải trả về đúng khuôn mẫu JSON sau để Frontend Agent dễ dàng xử lý:
   ```csharp
   public class ApiResponse<T>
   {
       public bool Success { get; set; }
       public string Message { get; set; } = string.Empty;
       public T? Data { get; set; }
       public List<string>? Errors { get; set; }
       public PaginationMeta? Meta { get; set; }
   }
   ```
3. **Bất đồng bộ & Hiệu năng (Async/Await):**
   * Tất cả các hàm I/O, Database, gọi AI/HTTP đều phải dùng `async/await`, đặt hậu tố `Async` (ví dụ: `CreateRoomAsync`) và truyền `CancellationToken`.
   * Các truy vấn chỉ đọc (`GET`) bắt buộc thêm `.AsNoTracking()` trong Entity Framework Core.
4. **Đặt tên & Kiểu dữ liệu:**
   * Dùng `PascalCase` cho Class, Method, Property; dùng `_camelCase` cho private readonly fields.
   * Khóa chính dùng `Guid` (`Id`), mọi Entity kế thừa từ `BaseEntity` gồm: `Id`, `CreatedAt`, `UpdatedAt`, `CreatedBy`, `IsDeleted` (Soft Delete).

## 4. Quy Tắc Cho Frontend Agents

1. **Làm việc độc lập qua Mock Service (Contract-First):**
   * Khi Backend chưa code xong API, Frontend Agent phải đọc file `docs/api/*.md` để tạo TypeScript Interface và Mock Data tương ứng trong `client/src/api/mocks/`.
   * Tuyệt đối không tự ý bịa tên trường JSON khác với tài liệu trong `docs/api/`.
2. **Cấu trúc Module theo tính năng:**
   * Mỗi tính năng trong 18 tính năng được đặt trong thư mục riêng (ví dụ: `client/src/features/rooms/`, `client/src/features/contract-scanner/`) để `FE-01` và `FE-02` không bị xung đột code (Merge Conflict) khi làm việc song song.

## 5. Quy Trình Phối Hợp Multi-Agent Bằng Markdown

1. **Bước 1 (Giao việc):** Scrum Master tạo hoặc cập nhật file task trong `tasks/sprint-X/FTxx-*.md`.
2. **Bước 2 (Ký hợp đồng API):** Backend Dev và Frontend Dev cùng thống nhất cấu trúc Request/Response trong `docs/api/*.md`.
3. **Bước 3 (Thực thi bằng Agent):**
   * Mỗi Dev mở Coding Agent cá nhân và truyền ngữ cảnh theo cú pháp:
     `Hãy đọc @.agent/AGENTS.md, @docs/api/<ten-file>.md và thực thi yêu cầu trong @tasks/sprint-X/<ten-task>.md`
4. **Bước 4 (Kiểm tra nghiệm thu):** Trước khi tạo Pull Request, Dev yêu cầu Agent tự chạy `dotnet test` (hoặc `npm run build`) và tích vào danh sách Acceptance Criteria trong PR.
