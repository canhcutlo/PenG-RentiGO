# Hồ Sơ Kỹ Thuật & Task Markdown Dành Cho Backend Dev 1 (BACKEND_DEV1_TASKS.md)

Tài liệu này dành cho **Backend Dev 1 (BE-01)** cùng Coding Agent của bạn (Claude Code, Cursor, Copilot, Codex...). Bạn chịu trách nhiệm chính về **Core Phòng Trọ, Tìm Kiếm Không Gian (GIS), Đặt Phòng Ngắn Hạn, Thanh Toán Online và AI/ML**.

## 1. Danh Sách 9 Tính Năng Do BE-01 Phụ Trách (Xếp Từ Dễ Đến Khó)

| Thứ tự độ khó | Mã TN | Tên tính năng | Điểm khó | Sprint | Công nghệ C# (.NET 8) trọng tâm |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 2 / 18 | **FT-02** | Thông tin an ninh khu trọ | 2.0 | Sprint 1 | EF Core 1-N, Enum Flags, Badge DTOs |
| 6 / 18 | **FT-06** | Thông tin phòng trọ và cho thuê (Core CRUD) | 4.0 | Sprint 1 | EF Core N-N (`RoomAmenity`), Multi-image Upload, FluentValidation |
| 7 / 18 | **FT-07** | Đánh giá phòng trọ | 4.5 | Sprint 2 | Custom Authorization Handler (Verified Tenant), Rating Recalculation |
| 12 / 18 | **FT-12** | Loại phòng trọ cho thuê ngắn hạn | 7.0 | Sprint 3 | Availability Calendar, Optimistic Concurrency (`[Timestamp]` / `RowVersion`) |
| 13 / 18 | **FT-13** | Tìm kiếm phòng trọ đa tiêu chí | 7.5 | Sprint 3 | Specification Pattern, Dynamic LINQ, SQL Indexing, Redis Cache |
| 14 / 18 | **FT-14** | Hiển thị khu vực tiện ích ở gần (nhà ăn, siêu thị, công viên...) | 8.0 | Sprint 4 | `NetTopologySuite` (`Point` SRID 4326), Overpass/Google Places API Client |
| 15 / 18 | **FT-15** | Dự đoán số điện nước tiêu thụ hàng tháng dựa vào đồ dùng điện | 8.5 | Sprint 4 | `ML.NET` (`FastTree` Regression) + Công thức vật lý công suất thiết bị (W) |
| 17 / 18 | **FT-17** | Thanh toán online | 9.0 | Sprint 5 | VNPay / PayOS SDK, HMAC-SHA512 Webhook Verification, Idempotency Transaction |
| 18 / 18 | **FT-18** | AI Quét Hợp đồng thuê trọ tìm "Điều khoản bẫy" | 9.5 | Sprint 6 | `PdfPig` / `Tesseract OCR` + `Microsoft.SemanticKernel` Structured JSON Output |

## 2. Thiết Kế Database Entities (C# EF Core) Của BE-01

Dưới đây là cấu trúc Entity cốt lõi trong `PenG.RentiGO.Domain` mà Agent của BE-01 cần khởi tạo:

```csharp
namespace PenG.RentiGO.Domain.Entities;

public class BoardingHouse : BaseEntity
{
    public string Name { get; set; } = string.Empty;
    public string Address { get; set; } = string.Empty;
    public double Latitude { get; set; }
    public double Longitude { get; set; }
    public Guid LandlordId { get; set; }
    
    // FT-02: Thông tin an ninh khu trọ
    public bool HasCamera24x7 { get; set; }
    public bool HasFingerprintLock { get; set; }
    public bool HasSecurityGuard { get; set; }
    public bool HasFireSafetyCert { get; set; }
    public string? CurfewHours { get; set; } // Ví dụ: "Tự do 24/24" hoặc "Đóng cửa 23:30"
    public string? SecurityNotes { get; set; }

    public ICollection<Room> Rooms { get; set; } = new List<Room>();
    public ICollection<NearbyPoi> NearbyPois { get; set; } = new List<NearbyPoi>();
}

public class Room : BaseEntity
{
    public Guid BoardingHouseId { get; set; }
    public string Title { get; set; } = string.Empty;
    public string RoomNumber { get; set; } = string.Empty;
    public decimal AreaM2 { get; set; }
    public decimal MonthlyRentPrice { get; set; }
    public decimal? DailyRentPrice { get; set; } // Phục vụ FT-12: Thuê ngắn hạn
    public bool AllowsShortTerm { get; set; }
    public decimal ElectricityPricePerKwh { get; set; }
    public decimal WaterPricePerM3 { get; set; }
    public int MaxOccupants { get; set; }
    public RoomStatus Status { get; set; }
    public double AverageRating { get; set; }

    // Concurrency Token chống đặt trùng phòng (FT-12)
    public byte[] RowVersion { get; set; } = Array.Empty<byte>();

    // FT-06: Tiện nghi (máy giặt, lọc nước, nóng lạnh, internet, điều hòa...)
    public ICollection<RoomAmenity> RoomAmenities { get; set; } = new List<RoomAmenity>();
    public ICollection<RoomMedia> MediaFiles { get; set; } = new List<RoomMedia>();
    public ICollection<RoomReview> Reviews { get; set; } = new List<RoomReview>();
}
```

## 3. Hợp Đồng API Chuẩn Giữa BE-01 Và FE-01 (`docs/api/core-rooms-api.md`)

### 3.1. Tạo mới / Cập nhật thông tin phòng trọ & tiện nghi (`POST /api/v1/rooms`)
* **Request Body JSON:**
```json
{
  "boardingHouseId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "title": "Phòng Studio khép kín đủ đồ gần ĐH Bách Khoa",
  "roomNumber": "P.302",
  "areaM2": 28.5,
  "monthlyRentPrice": 3800000,
  "dailyRentPrice": 250000,
  "allowsShortTerm": true,
  "electricityPricePerKwh": 3500,
  "waterPricePerM3": 25000,
  "maxOccupants": 3,
  "amenityCodes": ["WASHING_MACHINE", "WATER_PURIFIER", "WATER_HEATER", "HIGH_SPEED_WIFI", "AIR_CONDITIONER"],
  "securityInfo": {
    "hasCamera24x7": true,
    "hasFingerprintLock": true,
    "hasSecurityGuard": false,
    "hasFireSafetyCert": true,
    "curfewHours": "Tự do 24/24"
  }
}
```

### 3.2. Dự đoán điện nước hàng tháng (`POST /api/v1/utilities/predict`)
* **Request Body JSON:**
```json
{
  "roomId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "occupantCount": 2,
  "season": "SUMMER",
  "appliances": [
    { "code": "AIR_CONDITIONER", "wattage": 900, "hoursPerDay": 7, "isInverter": true },
    { "code": "WATER_HEATER", "wattage": 2500, "hoursPerDay": 0.5, "isInverter": false },
    { "code": "FRIDGE", "wattage": 120, "hoursPerDay": 24, "isInverter": true },
    { "code": "WASHING_MACHINE", "wattage": 500, "hoursPerDay": 0.6, "isInverter": false }
  ]
}
```
* **Response Body JSON:**
```json
{
  "success": true,
  "data": {
    "estimatedMonthlyKwh": 198.4,
    "estimatedElectricityCost": 694400,
    "estimatedWaterM3": 8.0,
    "estimatedWaterCost": 200000,
    "totalEstimatedUtilityCost": 894400,
    "breakdownByAppliance": [
      { "code": "AIR_CONDITIONER", "kwh": 132.3, "percentage": 66.7, "savingTip": "Đặt nhiệt độ 26-27 độ C kết hợp quạt giúp giảm ~18% điện năng." }
    ]
  }
}
```

## 4. File Task Mẫu Thực Chiến Cho BE-01 Agent

### Mẫu 1: `tasks/sprint-1/FT06-core-room-crud.md` (Sprint 1)
* **Mục tiêu:** Xây dựng CRUD Phòng trọ, Danh mục tiện nghi (` máy giặt`, `lọc nước`, `nóng lạnh`, `internet`...) và Thông tin an ninh khu trọ (`FT-02` + `FT-06`).
* **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - [ ] `Given` chủ trọ gửi thông tin phòng hợp lệ kèm mảng `amenityCodes`, `When` gọi `POST /api/v1/rooms`, `Then` lưu thành công vào bảng `Rooms` và `RoomAmenities`, trả về HTTP `201 Created`.
  - [ ] `Given` diện tích `areaM2 <= 0` hoặc `monthlyRentPrice <= 0`, `When` gửi request, `Then` `FluentValidation` chặn lại và trả về HTTP `400 Bad Request`.
  - [ ] `When` gọi `GET /api/v1/rooms/{id}`, `Then` trả về chi tiết phòng kèm danh sách tiện nghi, huy hiệu an ninh và danh sách URL hình ảnh.

### Mẫu 2: `tasks/sprint-6/FT18-ai-contract-scanner.md` (Sprint 6)
* **Mục tiêu:** Xây dựng API nhận file ảnh/PDF hợp đồng thuê trọ, bóc tách văn bản tiếng Việt và dùng `Microsoft.SemanticKernel` tìm "Điều khoản bẫy".
* **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - [ ] `Given` người dùng tải lên file `.pdf` hoặc `.jpg/.png` hợp đồng, `When` gọi `POST /api/v1/ai-contract/scan`, `Then` trích xuất toàn bộ nội dung văn bản.
  - [ ] `Then` đối chiếu với danh sách rủi ro pháp lý (tự ý tăng giá không báo trước, phạt mất cọc 100% khi chấm dứt sớm vì lỗi chủ nhà, tính giá điện cao bất thường, không ghi rõ thời hạn hoàn trả tiền cọc) và trả về mảng JSON `detectedTraps` gồm: `originalClauseText`, `riskLevel` (`HIGH`/`MEDIUM`/`LOW`), `explanation` và `suggestedRevision`.
