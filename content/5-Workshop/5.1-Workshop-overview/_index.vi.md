---
title: "Giới thiệu"
date: 2026-11-20
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---
#### Về CPAWS

Giá vàng, bạc và dầu thô biến động mạnh khi có sự kiện lớn, nhưng người dùng phổ thông khó biết giá sắp tới, khó hiểu vì sao giá thay đổi. CPAWS dự đoán **phân phối log return h phiên tới** (h = 1–7) dưới dạng 7 phân vị, quy đổi thành khoảng giá và xác suất tăng, rồi để chatbot giải thích bằng tiếng Việt có trích nguồn.

#### Vấn đề thực tế

- **Khó biết giá sắp tới:** dự báo trên báo chí thường là nhận định định tính, không có con số và không công bố độ chính xác trong quá khứ
- **Khó hiểu vì sao giá biến động:** tin tức nhiều, rời rạc, không gắn với con số cụ thể
- **Khó liên hệ với giá trong nước:** giá thế giới tính bằng USD/ounce, USD/thùng nên khó hình dung theo VND

#### Giải pháp

- Dự đoán khoảng giá và xác suất tăng cho 1–7 phiên tới, quy đổi USD/VND
- Chatbot tiếng Việt giải thích dự đoán, mọi con số lấy từ hệ thống, mọi lý do có trích nguồn
- Hệ thống tự chạy mỗi ngày giao dịch, chi phí khoảng 15 USD/tháng

#### Tổng quan kiến trúc

1. **Thu thập:** EventBridge Scheduler kích hoạt Lambda ingest mỗi ngày giao dịch, ghi dữ liệu vào S3 (`raw/`, `clean/`)
2. **Dự đoán:** khi ingest xong, Lambda Destinations gọi Lambda predict (container image, suy luận bằng NumPy), ghi kết quả vào DynamoDB
3. **API:** API Gateway + Cognito authorizer trả dự đoán cho web
4. **Chatbot:** Lambda gọi Amazon Bedrock Converse API, lấy số liệu qua tool `get_forecast`
5. **Vận hành:** CloudWatch Alarm, AWS Budgets, hạ tầng viết bằng AWS CDK

![overview](/images/5-Workshop/CPAWS-architecture.jpg)

#### Các stack CDK

| Stack | Tài nguyên chính |
|---|---|
| `CpawsData` | S3 data lake, ECR (ingest, predict), Lambda ingest, Lambda predict |
| `CpawsServing` | DynamoDB `Forecasts` (PK `asset`, SK `base_date`) |
| `CpawsOps` | EventBridge Scheduler, CloudWatch Alarm |
| `CpawsAuth` | Cognito User Pool (không cho tự đăng ký) |
| `CpawsApi` | REST API + Cognito authorizer, Lambda API, Lambda chat |

#### Ước tính chi phí

Với tần suất một lần chạy mỗi ngày giao dịch, chi phí Lambda, Scheduler, S3 và DynamoDB chỉ vài USD/tháng. Phần tốn nhất là Amazon Bedrock khi chatbot được dùng nhiều, vì vậy API cần đăng nhập và có giới hạn lượt gọi.

#### Demo sản phẩm

- Video demo: ...
- Link production: ...
