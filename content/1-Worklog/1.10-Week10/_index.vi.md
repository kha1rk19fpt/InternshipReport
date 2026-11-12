---
title: "Worklog Tuần 10"
date: 2026-11-08
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### MỤC TIÊU TRONG WEEK 10:
- Xây dựng đặc trưng tín hiệu (CEEMDAN, tin tức Fed/EIA, GPR, OVX/GVZ) không rò rỉ dữ liệu tương lai cho mô hình CPAWS.
- Làm chủ kiến trúc Serverless toàn diện: Từ xử lý sự kiện (S3 triggers), lưu trữ (DynamoDB) đến xây dựng API (API Gateway).
- Xây dựng quy trình CI/CD tự động cho ứng dụng Serverless bằng AWS CodePipeline.
- Thực hành giám sát (Monitoring) và truy vết (Tracing) ứng dụng Serverless chuyên sâu với CloudWatch và X-Ray.

### CÁC ĐẦU VIỆC CỦA WEEK 10:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|:---:|---|:---:|:---:|---|
| **2** | **- Họp team project: Phát triển đặc trưng tín hiệu cho mô hình:**<br>- Đặc trưng CEEMDAN theo cửa sổ trượt trên chuỗi giá, dùng cache IMF<br>- Tín hiệu tin tức (thông cáo Fed, báo cáo EIA), GPR, OVX/GVZ căn theo thời điểm công bố<br>- Viết test chống rò rỉ dữ liệu tương lai cho hàm tạo đặc trưng | 09/11/2026 | 09/11/2026 | [PyEMD (CEEMDAN)](https://pyemd.readthedocs.io/)<br>[FOMC Statements](https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm)<br>[GPR Index](https://www.matteoiacoviello.com/gpr.htm) |
| **3** | **- Thực hành Lab 22: Serverless - Tương tác giữa Lambda với S3 và DynamoDB**<br>+ Giới thiệu<br>+ Xử lý và Tối ưu Kích thước Ảnh trên AWS<br>* Tạo Lambda Function Xử Lý Ảnh<br>* Tạo S3 Bucket<br>* Tạo IAM Policy cho Lambda Function<br>* Kiểm tra hoạt động của Lambda Function<br>+ Ghi dữ liệu vào Amazon DynamoDB<br>* Tạo và Quản lý Bảng trong AWS DynamoDB<br>* Tạo Lambda Function để Ghi dữ liệu<br>+ Dọn dẹp tài nguyên | 10/11/2026 | 10/11/2026 | [Lab 22 Intro](https://000078.awsstudygroup.com/vi/1-introduce/)<br>[Resize Function](https://000078.awsstudygroup.com/vi/2-resize-image-function/)<br>[DynamoDB Write](https://000078.awsstudygroup.com/vi/3-write-data-to-dynaomodb/) |
| **4** | **- Thực hành Lab 23: Serverless - Hướng dẫn viết Front-end gọi API Gateway**<br>+ Giới thiệu API Gateway / DynamoDB<br>+ Triển khai front-end<br>+ Triển khai Lambda function<br>* Tạo bảng trong DynamoDB<br>* Triển khai Lambda function<br>Lambda function ghi dữ liệu<br>Lambda function đọc dữ liệu<br>Lambda function xoá dữ liệu<br>+ Thiết lập API Gateway<br>* Tạo các method<br>* Cài đặt và kích hoạt CORS<br>+ Kiểm tra API với Postman<br>+ Kiểm tra API với front-end<br>+ Dọn dẹp tài nguyên | 11/11/2026 | 11/11/2026 | [Lab 23 Intro](https://000079.awsstudygroup.com/vi/1-introduction/)<br>[Deploy Frontend](https://000079.awsstudygroup.com/vi/2-front-end-deployment/)<br>[API Gateway](https://000079.awsstudygroup.com/vi/4-config-api-gw/)<br>[Postman Test](https://000079.awsstudygroup.com/vi/5-test-api-by-postman/) |
| **5** | ... | 12/11/2026 | 12/11/2026 | ... |
| **6** | **- Thực hành Lab 24: Serverless - CI/CD với CodePipeline**<br>+ Chuẩn bị<br>+ Xây dựng pipeline SAM<br>* Tạo kho Git<br>* Tạo SAM pipeline<br>+ Xây dựng pipeline cho front-end<br>* Tạo kho Git<br>* Tạo pipeline<br>+ Kiểm tra hoạt động web<br>+ Dọn dẹp<br>**- Thực hành Lab 25: Serverless - Giám sát ứng dụng Serverless với CloudWatch và X-Ray**<br>+ Chuẩn bị<br>+ Giám sát với CloudWatch<br>* Gỡ lỗi với nhật ký CloudWatch<br>* Tạo số liệu tùy chỉnh<br>* Tạo cảnh báo với CloudWatch Alarm<br>+ Theo dõi với X-ray<br>+ Dọn dẹp | 13/11/2026 | 13/11/2026 | [Lab 24 Intro](https://000084.awsstudygroup.com/vi/1-preparation/)<br>[SAM Pipeline](https://000084.awsstudygroup.com/vi/2-build-sam-pipeline/)<br>[Lab 25 Intro](https://000085.awsstudygroup.com/vi/1-preparation/)<br>[CloudWatch](https://000085.awsstudygroup.com/vi/2-cloudwatch-monitor/)<br>[X-Ray](https://000085.awsstudygroup.com/vi/3-x-ray-trace/) |

### THÀNH TỰU ĐẠT ĐƯỢC TRONG WEEK 10:
1. Hoàn thành hàm tạo đặc trưng dùng chung cho train và predict, có test chống rò rỉ dữ liệu tương lai.
2. Xây dựng hoàn chỉnh luồng xử lý Serverless Backend: Tự động hóa xử lý ảnh với S3 Trigger và quản lý dữ liệu NoSQL với DynamoDB.
3. Triển khai thành công Full-stack Serverless App: Kết nối Frontend với Backend thông qua API Gateway và Lambda, xử lý tốt vấn đề CORS.
4. Thiết lập quy trình DevOps tự động (CI/CD) cho ứng dụng Serverless, giúp việc deploy Code và Frontend trở nên nhanh chóng và ít lỗi.
5. ...
