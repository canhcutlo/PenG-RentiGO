# Hồ Sơ Kỹ Thuật & Task Markdown Dành Cho Backend Dev 2 (BACKEND_DEV2_TASKS.md)

Tài liệu này dành cho **Backend Dev 2 (BE-02)** cùng Coding Agent của bạn. Bạn phụ trách toàn bộ mảng **Vận Hành Nội Khu, Tiện Ích Cộng Đồng, Sàn Giao Dịch Đồ Thiết Yếu, Thuật Toán Ghép Đôi (Phòng/Xe), Dịch Vụ Chuyển Trọ và Tổng Đài Chatbot CSKH**.

## 1. Danh Sách 9 Tính Năng Do BE-02 Phụ Trách (Xếp Từ Dễ Đến Khó)

| Thứ tự độ khó | Mã TN | Tên tính năng | Điểm khó | Sprint | Công nghệ C# (.NET 8) trọng tâm |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 / 18 | **FT-01** | Cung cấp thông tin hỗ trợ thủ tục hành chính và hợp đồng | 1.5 | Sprint 1 | Markdown Article CRUD, Static File Download (`.docx`/`.pdf`) |
| 3 / 18 | **FT-03** | Thông báo tìm đồ bị mất | 2.5 | Sprint 1 | CRUD Bảng tin Lost & Found, Upload ảnh, Bộ lọc khu trọ |
| 4 / 18 | **FT-04** | Thông tin tuyển việc vặt | 3.0 | Sprint 1 | CRUD Việc vặt, Ứng tuyển nhanh (`OddJobApplication`), Unique Constraint |
| 5 / 18 | **FT-05** | Quản lý vệ sinh khu vực trọ | 3.5 | Sprint 2 | `Hangfire` Recurring Job chia lịch trực nhật xoay vòng hàng tuần |
| 8 / 18 | **FT-08** | Trang báo cáo (báo hỏng thiết bị, báo thông tin sai lệch...) | 5.0 | Sprint 2 | State Workflow (`Open` -> `InProgress` -> `Resolved`) + `SignalR` Push |
| 9 / 18 | **FT-09** | Chia sẻ, thuê, bán đồ thiết yếu (quạt, đệm, chăn, màn...) | 5.5 | Sprint 2 | Quản lý tồn kho, tính tiền cọc thuê đồ theo kỳ, nhắc hạn trả đồ |
| 10 / 18 | **FT-10** | Chia sẻ phòng trọ (tìm người ở cùng) / đi chung xe | 6.0 | Sprint 3 | Compatibility Scoring bằng LINQ (khớp ngân sách, giờ giấc, lộ trình) |
| 11 / 18 | **FT-11** | Dịch vụ chuyển trọ | 6.5 | Sprint 3 | `MovingPriceCalculator` tính phí động theo km, loại xe, số tầng lầu |
| 16 / 18 | **FT-16** | Tổng đài CSKH bằng BOT | 8.5 | Sprint 5 | `Semantic Kernel` RAG + Function Calling + `SignalR` Streaming Chat |

## 2. Thiết Kế Database Entities (C# EF Core) Của BE-02

Dưới đây là các Entity trọng tâm trong `PenG.RentiGO.Domain` do BE-02 quản lý để không xung đột với BE-01:

```csharp
namespace PenG.RentiGO.Domain.Entities;

// FT-08: Báo cáo hỏng thiết bị & thông tin sai lệch
public class IssueReport : BaseEntity
{
    public Guid ReporterId { get; set; }
    public Guid? RoomId { get; set; }
    public ReportTargetType TargetType { get; set; } // EquipmentDamage (gửi Chủ trọ) | FakeListing (gửi Admin)
    public string Title { get; set; } = string.Empty;
    public string Description { get; set; } = string.Empty;
    public ReportPriority Priority { get; set; } // Low, Medium, High, Urgent
    public ReportStatus Status { get; set; } // Open, InProgress, Resolved, Rejected
    public string? ResolutionNote { get; set; }
    public DateTime? ResolvedAt { get; set; }
    public ICollection<string> AttachmentUrls { get; set; } = new List<string>();
}

// FT-09: Chia sẻ, thuê, bán đồ thiết yếu (quạt, đệm, chăn, màn...)
public class EssentialItem : BaseEntity
{
    public Guid OwnerId { get; set; }
    public Guid? BoardingHouseId { get; set; }
    public string ItemName { get; set; } = string.Empty; // Quạt cây, Đệm bông ép, Chăn đông, Màn khung...
    public ItemTransactionType TransactionType { get; set; } // FreeShare, Sell, Rent
    public decimal SalePrice { get; set; }
    public decimal RentalPricePerMonth { get; set; }
    public decimal DepositAmount { get; set; }
    public int AvailableQuantity { get; set; }
    public string Condition { get; set; } = "Mới 90%";
}

// FT-10: Tìm người ở ghép & Đi chung xe
public class RoommateProfile : BaseEntity
{
    public Guid UserId { get; set; }
    public Guid? CurrentRoomId { get; set; }
    public string GenderPreference { get; set; } = "Any";
    public decimal BudgetMin { get; set; }
    public decimal BudgetMax { get; set; }
    public bool IsNonSmoker { get; set; }
    public bool AllowsPets { get; set; }
    public string SleepSchedule { get; set; } = "Before23h"; // Before23h, NightOwl, Flexible
}
```

## 3. Hợp Đồng API Chuẩn Giữa BE-02 Và FE-02 (`docs/api/community-ops-api.md`)

### 3.1. Gửi báo cáo hỏng thiết bị / báo tin sai (`POST /api/v1/reports`)
* **Request Body JSON:**
```json
{
  "roomId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "targetType": "EquipmentDamage",
  "title": "Bình nóng lạnh phòng 302 không lên nguồn",
  "description": "Đã bật aptomat 30 phút nhưng đèn báo không sáng và nước không nóng.",
  "priority": "High",
  "attachmentUrls": ["/uploads/reports/heater-error-01.jpg"]
}
```
* **Sự kiện SignalR đẩy tức thời (`ReportCreated`):**
  * Hub URL: `/hubs/notifications`
  * Gửi tới Group của Chủ trọ (`Landlord_{landlordId}`) hoặc `Admins` ngay sau khi lưu DB.

### 3.2. Đặt thuê/mua đồ thiết yếu (`POST /api/v1/marketplace/orders`)
* **Request Body JSON:**
```json
{
  "itemId": "8b215f64-1122-4562-b3fc-2c963f661111",
  "transactionType": "Rent",
  "quantity": 1,
  "rentalStartDate": "2026-11-01",
  "rentalEndDate": "2027-02-01"
}
```
* **Response Body JSON:**
```json
{
  "success": true,
  "data": {
    "orderId": "9c335f64-9988-4562-b3fc-2c963f662222",
    "rentalMonths": 3,
    "totalRentalFee": 150000,
    "depositAmount": 200000,
    "totalPayableNow": 350000,
    "status": "PendingHandover"
  }
}
```

## 4. File Task Mẫu Thực Chiến Cho BE-02 Agent

### Mẫu 1: `tasks/sprint-1/FT01-legal-guides.md` (Sprint 1 - Tính năng dễ nhất)
* **Mục tiêu:** Xây dựng module cung cấp thông tin hỗ trợ thủ tục hành chính (đăng ký tạm trú trực tuyến qua Cổng dịch vụ công, quy định PCCC) và tải mẫu hợp đồng thuê trọ chuẩn.
* **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - [ ] `When` gọi `GET /api/v1/guides?category=TamTru`, `Then` trả về danh sách bài viết hướng dẫn viết bằng Markdown kèm thời gian đọc ước tính.
  - [ ] `When` gọi `GET /api/v1/guides/templates/{id}/download`, `Then` trả về file mẫu hợp đồng `.docx` hoặc `.pdf` với `Content-Disposition: attachment`.

### Mẫu 2: `tasks/sprint-2/FT05-cleaning-schedule.md` (Sprint 2)
* **Mục tiêu:** Xây dựng hệ thống quản lý vệ sinh khu vực trọ và lập lịch trực nhật tự động hàng tuần bằng `Hangfire`.
* **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - [ ] `Given` khu trọ có danh sách các phòng đang có người ở, `When` Hangfire Job chạy vào 07:00 sáng Thứ Hai hàng tuần, `Then` tự động xoay vòng phân công phòng trực nhật khu vực chung (hành lang, nhà để xe, khu đổ rác) mà không lặp lại phòng của tuần liền trước.
  - [ ] `Given` người thuê đã dọn vệ sinh xong, `When` gọi `PATCH /api/v1/cleaning/{scheduleId}/complete` kèm ảnh chụp nghiệm thu, `Then` chuyển trạng thái sang `Completed` và ghi nhận điểm cộng vệ sinh cho phòng đó.
