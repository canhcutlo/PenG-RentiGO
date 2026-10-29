# [FT-14] Hiển thị khu vực tiện ích ở gần (GIS)

## 1. Thông tin phân công
- **Sprint:** Sprint 4
- **Độ khó:** 8.0 / 10
- **Backend phụ trách:** BE-01
- **Frontend phụ trách:** FE-01
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV1_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV1_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người tìm phòng trọ
- **Tôi muốn:** Xem bản đồ tương tác hiển thị vị trí phòng trọ cùng các tiện ích lân cận (siêu thị, quán ăn, trạm xe buýt, công viên, trường đại học) trong bán kính 500m - 1km kèm khoảng cách và thời gian đi bộ
- **Để:** Đánh giá môi trường sống xung quanh và sự thuận tiện trong sinh hoạt hàng ngày.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `BoardingHouses`, `NearbyPois`
  - Sử dụng `NetTopologySuite` với kiểu dữ liệu `Point` (SRID 4326) để tính toán khoảng cách địa lý chính xác (ST_Distance).
  - Tích hợp Overpass API (OpenStreetMap) hoặc Google Places API Client để đồng bộ danh sách POI xung quanh tọa độ khu trọ.
- **Endpoints cần xây dựng:**
  - `GET /api/v1/boarding-houses/{id}/nearby-pois?radiusMeters=1000&type={poiType}`: Lấy danh sách điểm tiện ích quanh nhà trọ kèm khoảng cách (mét) và thời gian đi bộ ước tính.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/nearby-map/`
  - `InteractiveMap`: Bản đồ tương tác (sử dụng Leaflet / Mapbox) hiển thị Marker trung tâm phòng trọ và vẽ các vòng tròn bán kính 500m / 1000m.
  - `PoiFilterChips`: Các nút bật/tắt hiển thị từng loại tiện ích (🍱 Quán ăn, 🛒 Siêu thị, 🌳 Công viên, 🚌 Xe buýt, 🎓 Đại học).
  - `PoiDetailPopup`: Popup hiển thị tên tiện ích, khoảng cách (m) và thời gian đi bộ khi click vào Marker.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Truy vấn theo bán kính GIS):** Given tọa độ nhà trọ, When gọi API với bán kính `radiusMeters=1000`, Then trả về danh sách POI nằm trong vòng tròn bán kính 1km và được sắp xếp theo khoảng cách tăng dần.
- [ ] **AC-02 (Tính thời gian đi bộ):** Then mỗi POI trả về phải chứa số mét khoảng cách chính xác và thời gian đi bộ ước tính (tính theo tốc độ trung bình 4.5 km/h).
- [ ] **AC-03 (Bắt lỗi):** Given bán kính yêu cầu `radiusMeters > 5000` (vượt quá 5km), When gọi API, Then trả về HTTP `400 Bad Request`.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra tính toán khoảng cách không gian bằng NetTopologySuite.
