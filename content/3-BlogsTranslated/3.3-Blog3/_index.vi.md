---
title: "Blog 3"
date: 2026-11-10
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Dự đoán theo lô serverless trên AWS: EventBridge Scheduler, Lambda container và DynamoDB

*Trịnh Tiến Khải · Nhóm CPAWS · Blog tự viết từ quá trình làm dự án*

Khi nghĩ đến "đưa mô hình học máy lên cloud", nhiều bạn sinh viên (trong đó có tôi lúc đầu) nghĩ ngay đến một endpoint suy luận chạy liên tục. Nhưng với bài toán của CPAWS, dự đoán giá vàng, bạc và dầu cho 1–7 ngày tới, dữ liệu chỉ thay đổi **mỗi ngày một lần** sau khi phiên giao dịch đóng cửa. Một máy chủ chạy 24/7 để trả lời cùng một kết quả suốt cả ngày là lãng phí. Bài này chia sẻ cách nhóm tôi xây luồng **dự đoán theo lô** hoàn toàn serverless với chi phí rất thấp, cùng những lựa chọn thiết kế phía sau.

## 1. Bài toán và yêu cầu

- 4 tài sản (vàng, bạc, Brent, WTI), mỗi tài sản 7 tầm dự báo × 7 phân vị
- Chạy một lần sau mỗi phiên giao dịch từ thứ Hai đến thứ Sáu
- Một nguồn dữ liệu lỗi không được làm hỏng toàn bộ luồng
- Web và chatbot đọc kết quả mới nhất với độ trễ thấp
- Tổng chi phí phải nằm trong khoảng 200 USD credit cho cả dự án

## 2. Kiến trúc

```text
EventBridge Scheduler ──► Lambda ingest ──► S3 (raw/, clean/)
                               │
                   Lambda Destinations (OnSuccess + OnFailure)
                               ▼
                         Lambda predict (container, NumPy) ──► DynamoDB Forecasts
                                                                   ▲
                                         API Gateway + Cognito ────┘ (web, chatbot)
```

### EventBridge Scheduler thay vì cron trên máy chủ

Lịch `cron(15 12 ? * TUE-SAT *)` (UTC) chạy vào sáng sớm giờ New York, sau khi phiên của ngày hôm trước đã đóng cửa. Chạy từ thứ Ba đến thứ Bảy theo UTC tương ứng với các phiên thứ Hai đến thứ Sáu. Không cần máy chủ nào thức để chờ đến giờ.

### Nối hai Lambda bằng Lambda Destinations

Thay vì để Lambda ingest gọi trực tiếp Lambda predict trong code, chúng tôi cấu hình **Lambda Destinations**: khi ingest kết thúc, dù thành công hay lỗi, AWS tự gọi predict. Lý do gọi cả khi lỗi: ingest có thể lỗi ở một nguồn (ví dụ dầu Brent) nhưng các tài sản còn lại vẫn có dữ liệu tốt. Predict tự kiểm tra từng tài sản: tài sản nào **thiếu phiên mới nhất thì bỏ qua**, không lùi về dự đoán ngày cũ, để không bao giờ hiển thị một dự đoán cũ như thể nó mới.

Predict được đặt `retry_attempts=0`, vì nếu dữ liệu đầu vào sai thì chạy lại cũng không có ích; lỗi sẽ kích hoạt CloudWatch Alarm để người phụ trách vào xem.

### Lambda container image và suy luận bằng NumPy

Huấn luyện mô hình (DLinear) dùng PyTorch, nhưng thư viện này quá nặng cho Lambda. Nhóm chuyển trọng số mô hình sang NumPy (`weights.npz` kèm `config.json`, `scaler.json`, `manifest.json`), và viết lớp suy luận thuần NumPy. Image được đóng gói dạng **container image trên ECR**:
- Tag image bằng **commit SHA** để biết chính xác code nào đang chạy
- **Ghim digest** của base image để build lặp lại được
- Build bằng Docker BuildKit cho nhanh và gọn

Hiện tại sản phẩm dùng mốc thống kê 252 phiên (vì DLinear chưa thắng mốc trên tập validation), nên phần suy luận còn nhẹ hơn nữa. Kiến trúc vẫn giữ nguyên để khi có mô hình tốt hơn chỉ cần thay artifact.

### Thiết kế bảng DynamoDB cho đúng truy vấn

Truy vấn duy nhất web và chatbot cần là: *"dự đoán mới nhất của tài sản X"*. Vì vậy bảng `Forecasts` có:
- **Partition key** `asset` (gold, silver, brent, wti)
- **Sort key** `base_date` (ngày giao dịch làm mốc)

Lấy bản mới nhất chỉ là một lệnh `Query` theo `asset`, sắp xếp giảm dần theo `base_date`, `Limit=1`. Không cần `Scan`, không cần index phụ. Kết quả đã được tính sẵn, nên API chỉ đọc và định dạng, độ trễ rất thấp. Lambda API và chatbot dùng **chung một hàm định dạng** để số liệu trên web và trong câu trả lời của chatbot luôn khớp nhau.

## 3. Chi phí: vì sao chỉ vài USD mỗi tháng

Một ước lượng đơn giản cho phần dự đoán (giá tham khảo, cần kiểm tra lại trên trang giá AWS):

| Thành phần | Mức dùng mỗi tháng | Ghi chú |
|---|---|---|
| EventBridge Scheduler | khoảng 22 lần gọi | nằm trong mức miễn phí |
| Lambda ingest + predict | khoảng 44 lần chạy, mỗi lần dưới 1 phút | vài trăm đến vài nghìn GB-giây |
| DynamoDB on-demand | vài trăm lượt ghi, vài nghìn lượt đọc | vài cent |
| S3 | vài trăm MB dữ liệu Parquet | vài cent |

So sánh: một endpoint suy luận chạy liên tục, dù là loại nhỏ nhất, vẫn tính tiền cho đủ 730 giờ mỗi tháng, tức là cao hơn nhiều lần toàn bộ luồng trên. Phần tốn nhất của hệ thống thực ra nằm ở **Amazon Bedrock** khi chatbot được dùng nhiều, nên chúng tôi kiểm soát bằng đăng nhập Cognito, định tuyến câu đơn giản sang model nhỏ và giới hạn `max_tokens` theo loại câu. AWS Budgets đặt cảnh báo ở 50 USD/tháng.

## 4. Những điều tôi học được

1. **Hỏi "dữ liệu thay đổi bao lâu một lần?" trước khi chọn kiến trúc.** Nếu câu trả lời là mỗi ngày một lần, dự đoán theo lô gần như luôn rẻ và đơn giản hơn suy luận thời gian thực.
2. **Thiết kế cho lỗi từ đầu.** Tách ingest và predict, gọi predict cả khi ingest lỗi, không bao giờ lùi ngày: ba quyết định nhỏ giúp hệ thống không im lặng hiển thị dữ liệu sai.
3. **Thiết kế bảng theo truy vấn.** Với DynamoDB, nghĩ đến câu truy vấn trước rồi mới chọn khóa.
4. **Truy vết được mọi thứ.** Image tag theo commit, artifact mô hình có manifest, dữ liệu theo phân vùng ngày trên S3: khi một con số trên web trông lạ, chúng tôi biết chính xác nó đến từ đâu.

## Tài liệu tham khảo

- [Amazon EventBridge Scheduler – User Guide](https://docs.aws.amazon.com/scheduler/latest/UserGuide/what-is-scheduler.html)
- [AWS Lambda – Capturing records of asynchronous invocations (Destinations)](https://docs.aws.amazon.com/lambda/latest/dg/invocation-async-retain-records.html)
- [AWS Lambda – Create a Lambda function using a container image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html)
- [Amazon DynamoDB – Querying tables](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)
- [AWS Lambda Pricing](https://aws.amazon.com/lambda/pricing/)
- [AWS Well-Architected Framework – Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html)
