---
title: "Sự Kiện 5"
date: 2026-11-14
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Báo Cáo Tóm Tắt: "Cloud Mastery 1"

### Tổng Quan Sự Kiện

- **Tên Sự Kiện:** Cloud Mastery 1  
- **Ngày:** 14/11/2026  
- **Vai Trò:** Người tham dự  
- **Hình Thức:** Buổi chia sẻ kỹ thuật  
- **Địa Điểm:** Tầng 26, Tòa nhà Bitexco Financial Tower, Quận 1, TP.HCM  
- **Chủ Đề Chính:** Amazon Bedrock AgentCore

Cloud Mastery 1 là buổi chia sẻ kỹ thuật với chủ đề chính là Amazon Bedrock AgentCore. Phần mở đầu "AI in the Enterprise" đặt AI vào bên trong một sản phẩm thay vì coi AI là sản phẩm: dữ liệu là nguyên liệu, AI model được xây trên dữ liệu, tính năng được xây trên model, và người dùng chỉ gặp AI thông qua tính năng đó.

### Tóm Tắt Về Amazon Bedrock AgentCore

*Tóm tắt từ tài liệu công khai của AWS.*

- Amazon Bedrock cung cấp các foundation model; AgentCore là bộ dịch vụ được quản lý để chạy các agent xây trên những model đó ở môi trường thật
- Các thành phần chính gồm **Runtime** (nơi chạy code của agent), **Gateway** (biến API và Lambda function thành tool cho agent), **Memory** (ngữ cảnh ngắn hạn và dài hạn), **Identity** (xác thực cho agent và người dùng), **Observability**, cùng các tool có sẵn như Code Interpreter và Browser
- Có thể dùng từng thành phần riêng, không bắt buộc dùng cả bộ

### Liên Hệ Với Dự Án CPAWS

- Sự kiện diễn ra khoảng một tuần trước khi CPAWS đóng băng tính năng (22/11), lúc chatbot của nhóm đã chạy
- Chatbot CPAWS là một phiên bản nhỏ, tự dựng của chính các phần này: Lambda function `chat-api` đóng vai trò runtime, hai tool Python (`get_forecast`, `get_news`) được gọi qua Bedrock Converse API, người dùng đăng nhập bằng Amazon Cognito, giới hạn lượt dùng mỗi ngày lưu trong DynamoDB. Chatbot chưa có bộ nhớ hội thoại dài hạn
- Nếu chatbot mở rộng (thêm tool, nhớ ngữ cảnh giữa các phiên), AgentCore Gateway, Memory và Identity là những phần nên cân nhắc thay vì tự dựng thêm hạ tầng

### Ảnh Sự Kiện

![Cloud Mastery 1](/images/4-Event/cloud_mastery_1.jpg)

### Tài Liệu Tham Khảo

- [Amazon Bedrock AgentCore](https://aws.amazon.com/bedrock/agentcore/)
- [What is Amazon Bedrock AgentCore? (Developer Guide)](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html)
