---
title: "Worklog Tuần 8"
date: 2026-10-25
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### MỤC TIÊU TRONG WEEK 8:
- Hoàn thiện kỹ năng Networking nâng cao: VPC Endpoints (PrivateLink), VPC Peering và quản lý mạng tập trung với Transit Gateway Network Manager.
- Thực hành triển khai hệ thống Web Application thực tế (WordPress) với kiến trúc High Availability (ASG, ALB, RDS, CloudFront).
- Làm chủ tư duy "Infrastructure as Code" (IaC) thông qua AWS CloudFormation để tự động hóa việc khởi tạo hạ tầng.
- Thống nhất cách hiển thị dự đoán, thiết kế bảng DynamoDB và xây dựng pipeline thu thập giá cho CPAWS.
- Tham gia hoạt động thiện nguyện làm thú nhồi bông STEM cùng AWS HCM.

### CÁC ĐẦU VIỆC CỦA WEEK 8:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|:---:|---|:---:|:---:|---|
| **2** | **- Tham gia event: Thú nhồi bông làm STEM cùng AWS HCM (hoạt động thiện nguyện, tầng 36)**<br>**- Họp team project: Định hướng cách hiển thị và thiết kế dữ liệu**<br>+ Web hiển thị q10/q50/q90 quy đổi thành khoảng giá, xác suất tăng, chuyển USD/VND<br>+ Thiết kế bảng DynamoDB `Forecasts` (PK `asset`, SK `base_date`)<br>+ Chốt nguồn giá: GLD, SLV, BZ=F, CL=F, VND=X; đối chiếu với EIA | 26/10/2026 | 26/10/2026 | [yfinance](https://github.com/ranaroussi/yfinance)<br>[EIA Open Data API](https://www.eia.gov/opendata/) |
| **3** | **- Tiếp tục Thực hành Lab 17:**<br>+ VPC Endpoints cho AWS Services<br>* Triển khai VPC Endpoint<br>* Kiểm tra VPC Endpoint<br>+ VPC Endpoint Services<br>* Triển khai VPC Endpoint Services<br>+ VPC Peering<br>* VPC Peering Request<br>* Routing Configuration<br>* Kiểm tra VPC Peering<br>+ Transit Gateway Network Manager<br>* Cài đặt Network Manager<br>* Cấu hình Site<br>* Network Insights<br>+ Dọn dẹp tài nguyên | 27/10/2026 | 27/10/2026 | [VPC Endpoint](https://000092.awsstudygroup.com/vi/6-vpcendpointaws/)<br>[PrivateLink](https://000092.awsstudygroup.com/vi/7-vpcendpointonpremise/)<br>[Peering](https://000092.awsstudygroup.com/vi/8-vpcpeering/)<br>[Network Manager](https://000092.awsstudygroup.com/vi/9-transitgatewaynetworkmanager/) |
| **4** | **- Thực hành Lab 18: Triển khai Wordpress trên AWS Cloud**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>* Chuẩn bị VPC và Subnet<br>* Tạo Security Group cho EC2<br>* Tạo Security Group cho Database Instance<br>* Khởi tạo EC2 Instance<br>* Khởi tạo Database Instance<br>+ Cài đặt wordpress trên EC2<br>+ Thực hiện tạo Autoscaling cho wordpress Instance<br>* Khởi tạo AMI từ Webserver Instance<br>* Khởi tạo Launch Template<br>* Khởi tạo Target Group<br>* Khởi tạo Load Balancer<br>* Khởi tạo Auto Scaling Group<br>+ Sao lưu và phục hồi database<br>* Tạo DB snapshot<br>* Phục hồi bằng DB snapshot<br>+ Khởi tạo Cloudfront cho Web Server<br>+ Dọn dẹp tài nguyên | 28/10/2026 | 28/10/2026 | [Lab 18 Intro](https://000101.awsstudygroup.com/vi/1-introduce/)<br>[Install WP](https://000101.awsstudygroup.com/vi/3-installwordpressonec2/)<br>[ASG Setup](https://000101.awsstudygroup.com/vi/4-asgforec2/)<br>[CloudFront](https://000101.awsstudygroup.com/vi/6-createcloudfront/) |
| **5** | **- Họp team project: Xây dựng pipeline thu thập giá theo thiết kế dữ liệu**<br>+ CLI `ingest_prices` ghi dữ liệu vào S3 (`raw/`, `clean/prices/`), dùng lịch giao dịch NYSE/CME<br>+ Bỏ thanh của ngày hiện tại theo giờ New York, gắn cờ dòng bất thường, không forward-fill target<br>+ Đóng gói Lambda ingest dạng container image, ghim digest base image | 29/10/2026 | 29/10/2026 | [pandas_market_calendars](https://github.com/rsheftel/pandas_market_calendars)<br>[Lambda Container Image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html) |
| **6** | **- Thực hành Lab 19: AWS CloudFormation**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>* Tạo IAM User<br>* Tạo IAM Role<br>+ CloudFormation cơ bản<br>* Tạo Workspace<br>* Tạo CloudFormation Template<br>+ CloudFormation nâng cao<br>* Các tài nguyên tùy chỉnh<br>Tạo Lambda Function<br>Tạo Stack<br>Kết nối EC2 Instance<br>* Ánh xạ và Stacksets<br>* Drift Detection<br>+ Dọn dẹp tài nguyên | 30/10/2026 | 30/10/2026 | [Lab 19 Intro](https://000037.awsstudygroup.com/vi/1-introduce/)<br>[CF Basic](https://000037.awsstudygroup.com/vi/3-cloudformationbasic/)<br>[CF Advanced](https://000037.awsstudygroup.com/vi/4-cloudformationadvance/) |

### THÀNH TỰU ĐẠT ĐƯỢC TRONG WEEK 8:
1. Triển khai thành công các kết nối mạng bảo mật: VPC Endpoints (kết nối nội bộ đến dịch vụ AWS) và VPC Peering (kết nối giữa các VPC).
2. Xây dựng hoàn chỉnh hệ thống WordPress có khả năng tự động mở rộng (Auto Scaling), cân bằng tải (ALB) và phân phối nội dung toàn cầu (CloudFront).
3. Nắm vững quy trình Backup & Restore cơ sở dữ liệu RDS thông qua Snapshot trong bài toán thực tế.
4. Sử dụng thành thạo AWS CloudFormation để viết Template (Infrastructure as Code), quản lý Stacks và phát hiện sai lệch cấu hình (Drift Detection).
5. Hoàn thành pipeline thu thập giá hằng ngày cho 4 tài sản và tỷ giá, có lịch giao dịch, gắn cờ dữ liệu và chạy được trên Lambda.
6. Góp sức vào hoạt động thiện nguyện làm thú nhồi bông STEM cho các em nhỏ cùng AWS HCM.
