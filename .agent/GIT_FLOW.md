# Quy Chuẩn Git Flow Cho Nhóm 5 Người & Multi-Agent (GIT_FLOW.md)

Tài liệu này quy định chiến lược phân nhánh, quy ước commit và luồng review Pull Request (PR) cho dự án **PenG-RentiGO**. Toàn bộ 5 thành viên (1 Scrum Master, 2 Backend Devs, 2 Frontend Devs) và các Coding Agent (Claude Code, Cursor, Codex...) bắt buộc phải tuân thủ để tránh xung đột mã nguồn.

## 1. Chiến Lược Phân Nhánh (Branching Strategy)

Hệ thống nhánh được chia làm 3 tầng:

| Tầng nhánh | Tên nhánh | Mục đích & Quy tắc |
| :--- | :--- | :--- |
| **Production / Demo** | `main` | Nhánh ổn định cao nhất dùng để demo cho thầy giáo và bảo vệ đồ án. Nghiêm cấm push trực tiếp (`Protected Branch`). Chỉ merge từ `develop` khi kết thúc mỗi Sprint. |
| **Integration** | `develop` | Nhánh tích hợp chính của toàn bộ nhóm trong suốt Sprint. Mọi nhánh tính năng đều rẽ nhánh từ `develop` và merge ngược lại vào `develop` qua PR. |
| **Feature Branches** | `feature/<role>-<feature_code>-<short_desc>` | Nhánh làm việc độc lập của từng Dev cho từng tính năng. Sau khi hoàn thành và test pass thì tạo PR vào `develop`. |
| **Bugfix Branches** | `bugfix/<role>-<issue_desc>` | Nhánh sửa lỗi phát sinh trong quá trình tích hợp trên `develop`. |
| **Hotfix Branches** | `hotfix/<issue_desc>` | Nhánh sửa lỗi nghiêm trọng trực tiếp từ `main`. |

### Quy tắc đặt tên nhánh tính năng:
* **Backend Dev 1:** `feature/BE1-FT06-room-crud`, `feature/BE1-FT13-advanced-search`, `feature/BE1-FT17-online-payment`
* **Backend Dev 2:** `feature/BE2-FT01-legal-guides`, `feature/BE2-FT05-cleaning-schedule`, `feature/BE2-FT08-issue-reporting`
* **Frontend Dev 1:** `feature/FE1-FT06-room-detail-ui`, `feature/FE1-FT14-nearby-map-ui`, `feature/FE1-FT18-contract-scanner-ui`
* **Frontend Dev 2:** `feature/FE2-FT03-lost-found-ui`, `feature/FE2-FT08-issue-report-ui`, `feature/FE2-FT09-marketplace-ui`

## 2. Quy Ước Commit (Conventional Commits)

Mỗi commit của người hoặc của Coding Agent đều phải theo định dạng:

```text
<type>(<scope>): <mô tả ngắn gọn bằng tiếng Anh hoặc tiếng Việt>
```

### Các tiền tố `<type>` được phép dùng:
* `feat`: Thêm tính năng mới (ví dụ: `feat(BE1-FT06): implement Room and RoomAmenity EF Core entities`)
* `fix`: Sửa lỗi (ví dụ: `fix(FE2-FT08): fix SignalR reconnect loop on network drop`)
* `docs`: Cập nhật tài liệu, Markdown specs, API contracts (ví dụ: `docs(api): update core-rooms request schema`)
* `refactor`: Tái cấu trúc code nhưng không thay đổi hành vi nghiệp vụ (ví dụ: `refactor(BE1): optimize LINQ query with AsNoTracking`)
* `test`: Thêm hoặc sửa Unit/Integration tests (ví dụ: `test(BE2-FT05): add unit test for cleaning schedule rotation`)
* `chore`: Cấu hình build, dependencies, gitignore (ví dụ: `chore: add NetTopologySuite package to Infrastructure`)

## 3. Quy Trình Pull Request & Review Chéo (PR Flow)

Để phát huy tối đa mô hình 5 người kết hợp Multi-Agent:

```
[Dev tạo nhánh feature] ──> [Agent code & chạy test] ──> [Tạo PR vào develop]
                                                               │
                                       ┌───────────────────────┴───────────────────────┐
                                       ▼                                               ▼
                         [Review chéo đồng cấp]                           [Agent chạy kiểm tra tự động]
                      - BE1 review BE2 (và ngược lại)                  - Đối chiếu Acceptance Criteria
                      - FE1 review FE2 (và ngược lại)                  - Kiểm tra định dạng JSON
                                       │                                               │
                                       └───────────────────────┬───────────────────────┘
                                                               ▼
                                                  [Scrum Master duyệt & Merge]
                                                               │
                                                               ▼
                                                      [Merge vào develop]
```

### Các bước cụ thể:
1. **Trước khi tạo PR:** Dev yêu cầu Agent của mình chạy kiểm tra cục bộ:
   * Backend: Chạy `dotnet test` đạt 100% pass.
   * Frontend: Chạy `npm run build` hoặc `npm run test` không có lỗi lint/compile.
2. **Nội dung PR:** Bắt buộc áp dụng `.agent/templates/pr-template.md` (có đánh dấu checklist các mục Acceptance Criteria từ file task).
3. **Review chéo:**
   * **BE-01** và **BE-02** đọc và review code logic C# của nhau.
   * **FE-01** và **FE-02** review cấu trúc component và state của nhau.
   * Người review có thể copy PR diff nạp vào Agent của mình để hỏi: *"Đoạn code này có vi phạm Clean Architecture hoặc thiếu validation trường hợp nào không?"*.
4. **Scrum Master Merge:** Khi PR có ít nhất 1 phê duyệt (Approved) từ bạn đồng cấp và không bị conflict, Scrum Master sẽ bấm `Squash and Merge` vào `develop`.

## 4. Xử Lý Xung Đột Code (Merge Conflict Resolution)

Vì dự án có 2 Backend và 2 Frontend làm việc đồng thời, các điểm dễ xung đột nhất là:
1. **Tệp `AppDbContext.cs` & `Migrations` (Backend):**
   * *Quy tắc:* Mỗi Dev tạo file cấu hình riêng dạng `IEntityTypeConfiguration<T>` trong thư mục `Infrastructure/Configurations/` (ví dụ: `RoomConfiguration.cs`, `IssueReportConfiguration.cs`).
   * Trong `AppDbContext.cs`, chỉ dùng duy nhất 1 dòng `modelBuilder.ApplyConfigurationsFromAssembly(typeof(AppDbContext).Assembly);`. Nhờ vậy, 2 BE thêm Entity mới hoàn toàn không đụng vào cùng 1 file!
2. **Tệp `routes.tsx` hoặc `App.razor` (Frontend):**
   * *Quy tắc:* Chia route theo từng feature module (ví dụ `roomRoutes.ts`, `communityRoutes.ts`) và nạp vào router chính dạng mảng spread (`...roomRoutes, ...communityRoutes`).
3. **Cập nhật nhánh thường xuyên:** Trước khi bắt đầu code mỗi ngày, Dev phải gõ:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout feature/ten-nhanh-cua-minh
   git merge develop
   ```

## 5. Chuỗi Lệnh Git Mẫu Cho Thành Viên

```bash
# 1. Bắt đầu làm tính năng mới từ develop
git checkout develop
git pull origin develop
git checkout -b feature/BE1-FT06-room-crud

# 2. Commit công việc theo chuẩn
git add .
git commit -m "feat(BE1-FT06): create room entity and api controller"

# 3. Đồng bộ code mới nhất từ develop trước khi đẩy lên
git fetch origin
git merge origin/develop

# 4. Đẩy nhánh lên GitHub
git push origin feature/BE1-FT06-room-crud

# 5. Mở trình duyệt vào GitHub tạo Pull Request vào nhánh develop!
```
