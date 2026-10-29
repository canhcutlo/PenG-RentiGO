# [FT-16] Tổng đài CSKH bằng BOT (AI RAG)

## 1. Thông tin phân công
- **Sprint:** Sprint 5
- **Độ khó:** 8.5 / 10
- **Backend phụ trách:** BE-02
- **Frontend phụ trách:** FE-02
- **Tài liệu tham chiếu bắt buộc:**
  - Quy chuẩn chung: `/.agent/AGENTS.md`
  - Cơ sở dữ liệu: `/docs/DATABASE_SCHEMA.md`
  - Hồ sơ kỹ thuật: `/tasks/BACKEND_DEV2_TASKS.md`
  - Hồ sơ giao diện: `/tasks/FRONTEND_DEV2_TASKS.md`

## 2. Mô tả nghiệp vụ (User Story)
- **Là một:** Người dùng ứng dụng (người thuê hoặc chủ trọ)
- **Tôi muốn:** Trò chuyện trực tiếp với AI Chatbot thông minh 24/7 để giải đáp thắc mắc về nội quy, chính sách thuê, thủ tục tạm trú hoặc yêu cầu chuyển tiếp sang nhân viên hỗ trợ khi cần thiết
- **Để:** Nhận phản hồi ngay lập tức mà không phải chờ đợi tổng đài viên.

## 3. Yêu cầu kỹ thuật Backend (C# / .NET 8)
- **Entities liên quan:** `ChatSessions`, `ChatMessages`
  - Công nghệ AI: `Microsoft.SemanticKernel` triển khai kỹ thuật Retrieval-Augmented Generation (RAG) trên tài liệu nội bộ và Function Calling (tra cứu trạng thái phòng, đơn hàng, hóa đơn).
  - Giao tiếp thời gian thực: `SignalR` Streaming câu trả lời của Bot từng ký tự (Token streaming) để tăng trải nghiệm người dùng.
  - Cơ chế chuyển tiếp người thật: Cập nhật cờ `IsEscalatedToHuman = true` khi người dùng yêu cầu gặp nhân viên hoặc khi Bot phát hiện tình huống khẩn cấp.
- **Endpoints & Hubs:**
  - Hub: `/hubs/chat` (sự kiện `SendMessage`, `ReceiveBotChunk`, `BotResponseFinished`, `EscalateToAgent`).
  - `GET /api/v1/chat/history/{sessionId}`: Lấy lịch sử hội thoại trước đó.

## 4. Yêu cầu kỹ thuật Frontend (React TypeScript)
- **Màn hình / Component cần tạo:** `client/src/features/chatbot-cskh/`
  - `ChatWidget`: Nút nổi (Floating Button) mở cửa sổ chat ở góc dưới màn hình.
  - `MessageList`: Danh sách tin nhắn hỗ trợ hiển thị Markdown và hiệu ứng Typing/Streaming câu trả lời.
  - `EscalateButton`: Nút yêu cầu chuyển sang hỗ trợ viên người thật.

## 5. Tiêu chí nghiệm thu (Acceptance Criteria - Chuẩn Gherkin)
- [ ] **AC-01 (Trả lời theo ngữ cảnh RAG):** Given câu hỏi về thủ tục đăng ký tạm trú, When người dùng gửi tin nhắn, Then Bot sử dụng Semantic Kernel trích xuất đúng nội dung từ tài liệu pháp lý và trả lời chính xác, mạch lạc.
- [ ] **AC-02 (Streaming phản hồi):** Then tin nhắn của Bot được stream mượt mà qua SignalR từng đoạn văn bản đến giao diện người dùng.
- [ ] **AC-03 (Chuyển tiếp hỗ trợ viên):** When người dùng gõ "Tôi muốn gặp nhân viên hỗ trợ", Then Bot kích hoạt Function Escalation, đánh dấu `IsEscalatedToHuman = true` và gửi thông báo đến hàng đợi của Support Agent.
- [ ] **AC-04 (Kiểm thử):** Có tối thiểu 3 Unit Tests (xUnit) kiểm tra Semantic Kernel Plugin và quản lý phiên trò chuyện `ChatSession`.
