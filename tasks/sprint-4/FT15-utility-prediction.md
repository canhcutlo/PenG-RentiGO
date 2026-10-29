# [FT-15] Dự đoán số điện nước tiêu thụ (ML.NET)

## 1. Thông tin phân công
- **Sprint:** Sprint 4
- **Độ khó:** 8.5 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người chuẩn bị thuê phòng trọ
- **Tôi muốn:** Nhập danh sách các thiết bị điện cá nhân mang theo (điều hòa Inverter, bình nóng lạnh, tủ lạnh, máy tính, nồi cơm điện...) và số người ở
- **Để:** Hệ thống AI/ML dự báo chính xác số điện (kWh), số nước ($m^3$) và tổng số tiền điện nước phải trả hàng tháng, đồng thời đưa ra lời khuyên tiết kiệm điện.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `ApplianceCatalogs`, `UtilityPredictions`
  - Mô hình máy học: `ML.NET` huấn luyện thuật toán hồi quy (`FastTree` Regression) kết hợp công thức vật lý tiêu thụ điện năng: $P(kWh) = \frac{W \times \text{hours}}{1000}$.
  - Xét thêm các biến số môi trường: Mùa trong năm (`SUMMER` / `WINTER`), cờ Inverter tiết kiệm điện (hệ số 0.65 - 0.75).
- **Endpoints cần xây dựng:**
  - `GET /api/v1/utilities/appliances`: Lấy danh mục thiết bị điện mẫu kèm công suất tiêu chuẩn (W).
  - `POST /api/v1/utilities/predict`: Dự đoán lượng điện nước tiêu thụ và phân tích tỷ trọng theo từng thiết bị.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/utility-predictor/`
  - `ApplianceSelector`: Danh sách checkbox các đồ dùng điện gia đình kèm công suất (W).
  - `UsageHoursSlider`: Thanh trượt điều chỉnh số giờ sử dụng trung bình mỗi ngày cho từng thiết bị.
  - `PredictionPieChart`: Biểu đồ tròn thể hiện tỷ trọng điện năng (% tiêu thụ của điều hòa, tủ lạnh...) và hộp hiển thị tổng số tiền dự kiến kèm lời khuyên tiết kiệm điện.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Dự đoán chính xác theo công suất):** Given danh sách đồ dùng điện và số giờ dùng mỗi ngày, When gọi `POST /api/v1/utilities/predict`, Then trả về kết quả dự báo gồm `estimatedMonthlyKwh`, `estimatedElectricityCost`, `estimatedWaterM3` và `breakdownByAppliance`.
- [ ] **AC-02 (Lời khuyên tiết kiệm):** Given điều hòa chiếm trên 60% điện năng tiêu thụ, Then trường `savingTip` đưa ra lời khuyên tối ưu nhiệt độ điều hòa phù hợp.
- [ ] **AC-03 (Bắt lỗi):** Given số người ở `occupantCount <= 0` hoặc số giờ dùng mỗi ngày `> 24`, When gửi request, Then trả về HTTP `400 Bad Request`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra tính toán tiêu thụ điện và mô hình ML.NET.
