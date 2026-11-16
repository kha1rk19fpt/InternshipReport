---
title: "Các bài blog đã đăng"
date: 2026-09-08
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Ba bài blog dưới đây do tôi tự viết từ những gì đã học về các dịch vụ AWS trong quá trình làm dự án CPAWS, và đăng lên nhóm cộng đồng [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj). Mỗi bài dùng một ví dụ riêng và một sơ đồ tự vẽ bằng draw.io.

### [Blog 1 - Mô hình chỉ chạy vài giây mỗi ngày: có cần đến SageMaker endpoint không?](3.1-Blog1/)

So sánh bốn cách chạy inference trên SageMaker với AWS Lambda container image cho một job dự báo chạy mỗi đêm: giới hạn gói zip và container, 7 luồng của pipeline (ECR, EventBridge Scheduler, Lambda destinations, S3, DynamoDB, CloudWatch), retry khi gọi bất đồng bộ và chi phí.

### [Blog 2 - Amazon S3 Versioning: tái lập kết quả xử lý dữ liệu khi file bị ghi đè](3.2-Blog2/)

Tổ chức data lake theo tầng, cách S3 Versioning giữ phiên bản cũ, ghim dữ liệu đầu vào bằng VersionId và SHA-256 trước một lần đánh giá, quyền `s3:GetObjectVersion` và lifecycle cho phiên bản cũ.

### [Blog 3 - Thiết kế bảng DynamoDB: bắt đầu từ câu hỏi, không phải từ dữ liệu](3.3-Blog3/)

Chọn partition key và sort key từ các câu hỏi của ứng dụng, đọc bản mới nhất bằng Query `Limit=1`, gom một lần dự báo vào một item, và ghi có điều kiện để job chạy lại không ghi đè dữ liệu.
