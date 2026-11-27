---
title: "Sự Kiện 5"
date: 2026-11-14
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Sự kiện: "Cloud Mastery 1"

### Tổng Quan Sự Kiện

- **Tên Sự Kiện:** Cloud Mastery 1  
- **Ngày:** 14/11/2026  
- **Vai Trò:** Người tham dự  
- **Hình Thức:** Buổi chia sẻ kỹ thuật  
- **Địa Điểm:** Tầng 26, Tòa nhà Bitexco Financial Tower, Quận 1, TP.HCM  
- **Chủ Đề Chính:** Đưa AI từ prototype lên production, Amazon Bedrock AgentCore

### Nội Dung Hoạt Động

#### Từ prototype đến production

- Diễn giả so sánh AI trong môi trường học thuật với AI trong doanh nghiệp bằng hình ảnh căn bếp gia đình và chuỗi nhà hàng. AI doanh nghiệp được đánh giá bằng giá trị kinh doanh chứ không chỉ độ chính xác, phải làm việc với dữ liệu thay đổi liên tục và phục vụ rất nhiều người dùng 24/7.
- Model chỉ là một phần của hệ thống production. Bên cạnh model còn cần data pipeline, giám sát, bảo mật, khả năng mở rộng, độ tin cậy và kiểm soát chi phí.

#### Bốn trụ cột trên AWS

- **Bảo mật:** AWS KMS bảo vệ dữ liệu, AWS IAM kiểm soát truy cập, guardrails xử lý các rủi ro riêng của AI, ví dụ người dùng cố khiến model trả lời sai.
- **Khả năng mở rộng và độ tin cậy:** AWS Lambda, Auto Scaling và Elastic Load Balancing đáp ứng lưu lượng thay đổi, kết hợp health check, retry, failover và cảnh báo.
- **Tối ưu chi phí:** trả theo mức dùng, chọn đúng cấu hình, dùng cache. Với AI, một model lớn điều phối còn các model nhỏ, rẻ hơn xử lý tác vụ đơn giản; câu hỏi lặp lại thì trả từ cache.

#### Case study: Cox Automotive và Mobileye

- **Cox Automotive** xác định 57 cơ hội dùng agentic AI nhưng vướng hai vấn đề: agent cần quyền tối thiểu với dữ liệu nhạy cảm, và chưa có môi trường chuẩn để chạy agent. Sau khi chuẩn hóa trên AgentCore Runtime, AgentCore Identity và Amazon Bedrock Guardrails, công ty đưa được 17 giải pháp agent vào production.
- **Mobileye** có 66% ticket hỗ trợ là câu hỏi trạng thái thông thường. Một support agent chạy trên AgentCore Runtime nối với hệ thống ticket on-premises, dùng AWS Secrets Manager và MCP để gọi dịch vụ nội bộ, rút thời gian phản hồi từ vài giờ xuống khoảng một phút.
- Ba bài học của phần này: đưa bảo mật và khả năng mở rộng vào nền tảng ngay từ đầu; agent chỉ có giá trị khi nối được với hệ thống và dữ liệu thật; không thể tin một agent nếu không đo lường được nó.

#### Amazon Bedrock AgentCore

- Case study Duolingo cho thấy lợi ích của một nền tảng agent dùng chung, thay vì mỗi nhóm tự dựng lại cùng một bộ hạ tầng.
- Demo một agent tra cứu đơn hàng viết bằng Strands Agents SDK, chạy trên AgentCore Runtime (tài nguyên tính toán được quản lý, session tách biệt, tự mở rộng). Dù runtime được quản lý, đội phát triển vẫn chịu trách nhiệm về business rule, dữ liệu, tool và kiểm thử.
- **Identity** tách xác thực chiều vào (người dùng là ai, có được gọi agent không) với chiều ra (agent được truy cập gì thay mặt người dùng).
- **Gateway** cho agent một điểm truy cập thống nhất, an toàn tới nhiều tool, API và MCP server.
- **Memory** gồm bộ nhớ ngắn hạn cho cuộc hội thoại hiện tại và bộ nhớ dài hạn cho sở thích người dùng, bản tóm tắt.
- **Observability** tích hợp Amazon CloudWatch, theo dõi từng bước của agent: lời gọi model, lời gọi tool, độ trễ, số token.

#### Hỏi đáp và kết thúc

- Người tham dự đặt câu hỏi cho diễn giả, làm mini quiz, điền form phản hồi và chụp ảnh lưu niệm.

### Những Điều Tôi Học Được

#### AI sẵn sàng cho production

- Một model chạy được chỉ là điểm khởi đầu; bảo mật, khả năng mở rộng, độ tin cậy và chi phí phải được thiết kế từ đầu.

#### Kiến trúc agent

- Hiểu cách một agent framework như Strands kết hợp với nền tảng được quản lý như AgentCore, và vì sao identity, quyền truy cập tool, bộ nhớ nên dùng thành phần chung thay vì tự xây lại ở từng dự án.

#### Bảo mật và giám sát agent

- Tách quyền chiều vào và chiều ra, chỉ cấp cho mỗi agent đúng quyền nó cần.
- Agent thường trả lời sai mà không báo lỗi, nên mỗi lần chạy cần được ghi log, theo dõi và đo theo mục tiêu đặt trước.

### Ảnh Sự Kiện

![Cloud Mastery 1](/images/4-Event/cloud_mastery_1.jpg)
