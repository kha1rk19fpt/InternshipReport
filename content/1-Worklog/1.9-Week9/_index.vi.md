---
title: "Worklog Tuần 9"
date: 2026-11-09
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:
- Tiếp cận và làm chủ AWS Cloud Development Kit (CDK) để định nghĩa hạ tầng đám mây bằng ngôn ngữ lập trình.
- Thực hành triển khai các kiến trúc phức tạp (ECS, ALB, API Gateway, Serverless) sử dụng CDK và Nested Stacks.
- Củng cố tư duy Infrastructure as Code (IaC) thông qua việc triển khai kiến trúc 3 tầng (Three-Tier Architecture) hoàn chỉnh.
- Đánh giá mô hình dự báo CPAWS trên tập validation 2019 và viết code Lambda predict.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|:---:|---|:---:|:---:|---|
| **2** | **- Họp team project: Xây dựng mốc so sánh và đánh giá mô hình**<br>+ Mốc thống kê: phân vị thực nghiệm 252 phiên nhân quả (`baseline-q252`)<br>+ Cùng Long so DLinear MVP (chỉ dùng giá, cửa sổ 60, đầu ra 7h × 7q) với mốc trên validation 2019 bằng `pinball_skill` | 09/11/2026 | 09/11/2026 | |
| **3** | **- Thực hành Lab 19: CDK cơ bản**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>* Tạo IAM Role<br>+ CDK cơ bản<br>* Tạo workspace<br>* Cấu hình Cloud9<br>+ Tạo CDK Template<br>+ Cập nhật CDK Template<br>+ Dọn dẹp tài nguyên | 10/11/2026 | 10/11/2026 | [Lab 19 Intro](https://000038.awsfcaj.com/vi/1-introduce/)<br>[CDK Basic](https://000038.awsfcaj.com/vi/3-cdkbasic/)<br>[Create Template](https://000038.awsfcaj.com/vi/4-createcdktemplate/) |
| **4** | **- Thực hành Lab 20: CDK Cơ bản - 2**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>* Tạo IAM Role<br>* Chạy một máy chủ EC2<br>* Cấu hình môi trường VSCode<br>+ ECS, ALB và API Gateway<br>+ Lambda và S3<br>+ Nested stack<br>* Tạo các stack lồng với CDK<br>+ Dọn dẹp tài nguyên | 11/11/2026 | 11/11/2026 | [Lab 20 Intro](https://000076.awsfcaj.com/vi/1-introduction/)<br>[ECS & ALB](https://000076.awsfcaj.com/vi/3-ecs-alb-and-api-gateway/)<br>[Nested Stack](https://000076.awsfcaj.com/vi/5-nested-stack/) |
| **5** | **- Họp team project: Chốt mô hình chạy production và viết code Lambda predict**<br>+ DLinear thua mốc ở 28/28 nhóm validation → sản phẩm dùng `baseline-q252`, DLinear giữ làm challenger<br>+ Lambda predict: đọc dữ liệu clean, chạy mô hình, `normalize_quantiles()`, ghi kết quả vào DynamoDB | 12/11/2026 | 12/11/2026 | |
| **6** | **- Thực hành Lab 21: Giới Thiệu về Infrastructure as Code**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>+ Tạo Lambda function<br>+ Tạo VPC và EC2<br>+ Triển khai Three-Tier Architecture<br>* Triển khai CloudFormation Stack<br>* Kiểm tra truy cập Web Tier<br>* Kiểm tra truy cập Application Tier<br>* Kiểm tra truy cập Database Tier<br>+ Dọn dẹp tài nguyên | 13/11/2026 | 13/11/2026 | [Lab 21 Intro](https://000102.awsfcaj.com/vi/1-introduction/)<br>[Create Lambda](https://000102.awsfcaj.com/vi/3-createlambda/)<br>[Three Tier](https://000102.awsfcaj.com/vi/5-threetierweb/) |

### Kết quả đạt được tuần 9:
1. Thiết lập thành công môi trường làm việc CDK (Cloud9, VSCode) và triển khai các Template hạ tầng cơ bản.
2. Ứng dụng CDK để xây dựng các tài nguyên nâng cao: ECS Cluster, Application Load Balancer, API Gateway và cấu trúc Nested Stack.
3. Triển khai thành công kiến trúc Three-Tier chuẩn mực (Web, App, Database layers) hoàn toàn bằng mã nguồn (IaC).
4. Hoàn thành đánh giá trên validation 2019, chọn mô hình production dựa trên số liệu và có code Lambda predict ghi dự đoán vào DynamoDB.
