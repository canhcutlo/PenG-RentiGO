# Thiết Kế Cơ Sở Dữ Liệu Tổng Thể Cho 18 Tính Năng (DATABASE_SCHEMA.md)

Tài liệu này mô tả toàn bộ mô hình dữ liệu (`Entity Framework Core 8`) của dự án **PenG-RentiGO**. Cả **Backend Dev 1** và **Backend Dev 2** nạp file này cho Agent khi tạo `AppDbContext` và chạy lệnh `dotnet ef migrations add InitialCreate` để đảm bảo toàn bộ 18 tính năng liên kết khóa ngoại chính xác.

## 1. Sơ Đồ Phân Nhóm Bảng Dữ Liệu Theo 18 Tính Năng

| Nhóm nghiệp vụ | Tính năng phục vụ | Các bảng (Tables / Entities) trong C# | Dev phụ trách | 
| ----- | ----- | ----- | ----- | 
| **1. Người dùng & Phân quyền** | Toàn hệ thống | `Users`, `Roles`, `TenantVerifications` | BE-01 & BE-02 | 
| **2. Phòng trọ, Tiện nghi & An ninh** | `FT-02`, `FT-06`, `FT-13` | `BoardingHouses`, `Rooms`, `Amenities`, `RoomAmenities`, `RoomMedias` | BE-01 | 
| **3. Thủ tục hành chính & Hợp đồng mẫu** | `FT-01` | `LegalGuides`, `ContractTemplates` | BE-02 | 
| **4. Đời sống & Vận hành nội khu** | `FT-03`, `FT-04`, `FT-05`, `FT-08` | `LostFoundPosts`, `OddJobs`, `OddJobApplications`, `CleaningSchedules`, `IssueReports` | BE-02 | 
| **5. Đánh giá & Uy tín phòng trọ** | `FT-07` | `RoomReviews`, `ReviewMedias` | BE-01 | 
| **6. Chợ đồ thiết yếu & Dịch vụ chuyển trọ** | `FT-09`, `FT-11` | `EssentialItems`, `ItemRentalOrders`, `MovingBookings` | BE-02 | 
| **7. Ghép phòng & Đi chung xe** | `FT-10` | `RoommateProfiles`, `CarpoolRoutes`, `MatchRequests` | BE-02 | 
| **8. Thuê ngắn hạn & Thanh toán Online** | `FT-12`, `FT-17` | `ShortTermBookings`, `RoomCalendarLocks`, `Invoices`, `PaymentTransactions` | BE-01 | 
| **9. Bản đồ GIS, Điện nước ML & AI** | `FT-14`, `FT-15`, `FT-16`, `FT-18` | `NearbyPois`, `ApplianceCatalogs`, `UtilityPredictions`, `ChatSessions`, `ContractScanJobs` | BE-01 & BE-02 | 

## 2. Chi Tiết Cấu Trúc Bảng & Quan Hệ Khóa Ngoại (ERD Specification)

### 2.1. Nhóm Core Phòng Trọ, Tiện Nghi & An Ninh (`FT-02`, `FT-06`, `FT-12`, `FT-13`)

* **`BoardingHouses` (Khu nhà trọ):**

  * `Id` (Guid, PK), `LandlordId` (Guid, FK -> `Users.Id`), `Name` (nvarchar(200)), `Address` (nvarchar(500)), `Ward`, `District`, `City`.

  * `Location` (`Point` - NetTopologySuite SRID 4326 để truy vấn bán kính cho `FT-13` và `FT-14`).

  * Các cột an ninh (`FT-02`): `HasCamera24x7` (bit), `HasFingerprintLock` (bit), `HasSecurityGuard` (bit), `HasFireSafetyCert` (bit), `CurfewHours` (nvarchar(100)), `SecurityRating` (float).

* **`Rooms` (Phòng trọ):**

  * `Id` (Guid, PK), `BoardingHouseId` (Guid, FK), `RoomNumber` (nvarchar(50)), `Title` (nvarchar(250)), `AreaM2` (decimal(6,2)).

  * `MonthlyRentPrice` (decimal(18,2)), `DailyRentPrice` (decimal(18,2), nullable cho `FT-12`), `AllowsShortTerm` (bit).

  * `ElectricityPricePerKwh` (decimal(18,2)), `WaterPricePerM3` (decimal(18,2)), `Status` (int: `Available`, `Occupied`, `Maintenance`).

  * `RowVersion` (rowversion/timestamp - chống đặt trùng phòng ngắn hạn ở `FT-12`).

* **`Amenities` & `RoomAmenities` (`FT-06`):**

  * Lưu danh mục tiện nghi chuẩn: Máy giặt (`WASHING_MACHINE`), Máy lọc nước (`WATER_PURIFIER`), Bình nóng lạnh (`WATER_HEATER`), Internet (`HIGH_SPEED_WIFI`), Điều hòa (`AIR_CONDITIONER`), Tủ lạnh (`FRIDGE`).

### 2.2. Nhóm Cộng Đồng, Vệ Sinh, Báo Cáo & Chợ Đồ Thiết Yếu (`FT-01`, `FT-03`..`FT-05`, `FT-08`..`FT-11`)

* **`LegalGuides` & `ContractTemplates` (`FT-01`):**

  * `Id` (Guid, PK), `Title`, `Slug`, `Category` (`TamTru`, `HopDong`, `PCCC`, `NoiQuy`), `MarkdownContent`, `FileDownloadUrl`.

* **`LostFoundPosts` (`FT-03`):**

  * `Id` (Guid, PK), `AuthorId` (FK), `BoardingHouseId` (FK, nullable), `PostType` (`Lost` | `Found`), `ItemName`, `Description`, `ImageUrl`, `ContactPhone`, `IsResolved` (bit).

* **`OddJobs` & `OddJobApplications` (`FT-04`):**

  * `Id` (Guid, PK), `PosterId` (FK), `Title`, `Description`, `WageAmount` (decimal(18,2)), `LocationText`, `Status` (`Open`, `Assigned`, `Completed`).

* **`CleaningSchedules` (`FT-05`):**

  * `Id` (Guid, PK), `BoardingHouseId` (FK), `AssignedRoomId` (FK -> `Rooms.Id`), `WeekStartDate` (date), `AreaDescription` (nvarchar(250)), `IsCompleted` (bit), `ProofImageUrl` (nvarchar(500)).

* **`IssueReports` (`FT-08`):**

  * `Id` (Guid, PK), `ReporterId` (FK), `RoomId` (FK, nullable), `ReportCategory` (`EquipmentDamage`, `FakeInformation`, `NoiseComplaint`), `Priority` (int), `Status` (int), `AttachmentUrlsJson` (nvarchar(max)).

* **`EssentialItems` & `ItemRentalOrders` (`FT-09`):**

  * Quản lý danh sách quạt, đệm, chăn, màn... kèm `TransactionType` (`Share`, `Sell`, `Rent`), `RentalPricePerMonth`, `DepositAmount`, `AvailableQuantity`, và đơn đặt thuê có `StartDate`, `EndDate`, `IsDepositRefunded`.

* **`RoommateProfiles` & `CarpoolRoutes` (`FT-10`):**

  * Lưu hồ sơ tìm người ở ghép (ngân sách, giới tính, giờ ngủ, hút thuốc) và tuyến đi chung xe hàng ngày (`OriginPoint`, `DestinationPoint`, `DepartureTime`, `AvailableSeats`, `CostSharePerTrip`).

* **`MovingBookings` (`FT-11`):**

  * `Id` (Guid, PK), `TenantId` (FK), `PickupAddress`, `DropoffAddress`, `DistanceKm` (double), `VehicleType` (`BaGac`, `Truck500Kg`, `Truck1Ton`), `FloorCountNoElevator` (int), `NeedHelper` (bit), `EstimatedTotalFee` (decimal(18,2)).

### 2.3. Nhóm Tiện Ích Gần Trọ, Dự Báo Điện Nước, Chatbot, Thanh Toán & AI Hợp Đồng (`FT-14`..`FT-18`)

* **`NearbyPois` (`FT-14`):**

  * `Id` (Guid, PK), `BoardingHouseId` (FK), `PoiName`, `PoiType` (`Canteen`, `Supermarket`, `Park`, `BusStop`, `University`), `DistanceMeters` (int), `WalkingMinutes` (int), `Latitude`, `Longitude`.

* **`ApplianceCatalogs` & `UtilityPredictions` (`FT-15`):**

  * Lưu công suất tiêu chuẩn (W) của từng loại đồ điện và lịch sử dự đoán số điện (kWh), số nước (m3) hàng tháng.

* **`ChatSessions` & `ChatMessages` (`FT-16`):**

  * Lưu hội thoại giữa người dùng và AI Bot CSKH, kèm cờ `IsEscalatedToHuman` (bit) khi cần nhân viên trực tổng đài hỗ trợ.

* **`Invoices` & `PaymentTransactions` (`FT-17`):**

  * `Id` (Guid, PK), `InvoiceCode` (Unique), `PayerId` (FK), `InvoiceType` (`RoomDeposit`, `MonthlyRent`, `ShortTermRent`, `EssentialItemRent`, `MovingService`), `Amount` (decimal(18,2)), `PaymentGateway` (`VNPay`, `PayOS`), `TransactionReference`, `WebhookSignatureVerified` (bit), `Status` (`Pending`, `Paid`, `Failed`, `Refunded`).

* **`ContractScanJobs` & `DetectedTrapClauses` (`FT-18`):**

  * Lưu file hợp đồng tải lên (`FileUrl`, `ExtractedOcrText`) và danh sách các điều khoản bẫy phát hiện được (`ClauseExcerpt`, `RiskLevel`, `LegalViolationNote`, `SuggestedSafeWording`).