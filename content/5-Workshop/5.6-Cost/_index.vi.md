---
title: "Ước tính và tối ưu chi phí"
date: 2026-11-20
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Giả định để ước tính

| Giả định | Giá trị |
|---|---|
| Region | ap-southeast-1 (Singapore) |
| Luồng hằng ngày | khoảng 22 ngày giao dịch/tháng, mỗi ngày 1 lần ingest + 1 lần predict |
| Người dùng thử nghiệm | khoảng 15 người, khoảng 10.000 lượt gọi API/tháng |
| Chatbot | khoảng 2.000 câu hỏi/tháng, trung bình 3.000 token vào và 500 token ra mỗi câu |
| Huấn luyện lại | 1 Training Job/tuần, khoảng 1 giờ trên instance CPU nhỏ, tự tắt sau khi chạy |

Đơn giá là giá tham khảo tại thời điểm viết; hãy kiểm tra lại bằng [AWS Pricing Calculator](https://calculator.aws/#/) trước khi dùng con số này cho dự án khác.

#### Ước tính chi phí hằng tháng

| Dịch vụ | Mức dùng | Ước tính (USD/tháng) |
|---|---|---|
| AWS Lambda (ingest, predict, API, chat) | khoảng 10.000 lượt gọi, khoảng 10.000 GB-giây | 0 – 0,3 (phần lớn nằm trong mức miễn phí) |
| Amazon EventBridge Scheduler | khoảng 22 lượt gọi | 0 |
| Amazon S3 | khoảng 1 GB Parquet + request | khoảng 0,1 |
| Amazon DynamoDB (on-demand) | vài trăm lượt ghi, vài chục nghìn lượt đọc | khoảng 0,1 |
| Amazon ECR | 2 repository, khoảng 2 GB image | khoảng 0,2 |
| Amazon API Gateway (REST) | khoảng 10.000 request | khoảng 0,1 |
| Amazon Cognito | khoảng 15 người dùng hoạt động | 0 |
| AWS Amplify Hosting | build và phục vụ web tĩnh | khoảng 1 |
| Amazon CloudWatch (Logs, Alarm) | khoảng 1 GB log, vài alarm | khoảng 1 |
| Amazon SageMaker AI (Training Job) | khoảng 4 giờ/tháng | khoảng 1 |
| Amazon Bedrock (chatbot, gắn nhãn tin) | khoảng 6 triệu token vào, 1 triệu token ra | khoảng 10 – 12 |
| **Tổng** | | **khoảng 15 USD/tháng; tối đa khoảng 35 USD/tháng nếu chatbot được dùng nhiều gấp đôi** |

Với 3 tháng phát triển và vận hành, tổng chi phí dự kiến khoảng 45–105 USD, nằm trong khoản credit khoảng 200 USD của nhóm.

#### Biện pháp tối ưu chi phí

| Biện pháp | Tác dụng |
|---|---|
| Dự đoán theo lô bằng Lambda, không dùng endpoint suy luận chạy 24/7 | Chỉ trả tiền cho vài phút chạy mỗi ngày thay vì 730 giờ/tháng |
| Không dùng NAT Gateway; Lambda gọi API bên ngoài đặt ngoài VPC | Tránh phí giờ và phí xử lý dữ liệu của NAT Gateway |
| Kết quả dự đoán tính sẵn, lưu DynamoDB on-demand, truy vấn `Limit=1` | Mỗi lượt xem chỉ tốn một lần đọc nhỏ |
| Suy luận bằng NumPy thay vì PyTorch trong Lambda | Image nhỏ hơn, khởi động nhanh hơn, tốn ít GB-giây hơn |
| Định tuyến chatbot theo luật: câu đơn giản dùng Amazon Nova, câu phức tạp dùng Claude Haiku 4.5 | Giảm chi phí token cho phần lớn câu hỏi |
| Giới hạn `max_tokens` theo loại câu (512 / 1500) | Chặn câu trả lời dài bất thường |
| Bắt buộc đăng nhập Cognito, không cho tự đăng ký | Tránh bị lạm dụng làm tăng chi phí Bedrock |
| Training Job theo lịch, tự tắt sau khi chạy | Không có máy huấn luyện chạy không |
| CloudWatch Logs đặt thời gian lưu giới hạn | Không để log tích lũy vô thời hạn |
| AWS Budgets cảnh báo ở 50 USD/tháng | Phát hiện sớm khi chi phí tăng bất thường |

#### Theo dõi chi phí thực tế

- Xem chi phí theo dịch vụ trong **AWS Cost Explorer** mỗi tuần
- Gắn tag `project=cpaws` cho tài nguyên trong CDK để lọc chi phí theo dự án
- Kiểm tra cảnh báo của **AWS Budgets** trong email của người phụ trách chi phí

**Ảnh minh họa:** ...
