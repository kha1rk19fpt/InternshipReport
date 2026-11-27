---
title: "Worklog Tuần 11"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### MỤC TIÊU TRONG WEEK 11:
- Đóng băng tính năng dự án CPAWS (22/11), quay video demo và hoàn thiện hồ sơ nộp (README repo, trang workshop).
- Làm chủ quy trình phát triển ứng dụng Serverless phức tạp (Frontend, Backend, Xử lý ảnh) sử dụng AWS SAM.
- Thực hành quy trình Machine Learning chuyên nghiệp trên Amazon SageMaker: Từ Feature Engineering đến Deploy Model.
- Tối ưu hóa hiệu suất lập trình với bộ công cụ AI của AWS (Amazon Q, CodeWhisperer) trên VS Code.
- Học thêm kỹ năng quản trị chi phí và tuân thủ (Governance) thông qua IAM Policy nâng cao (giới hạn Region, Instance Type).

### CÁC ĐẦU VIỆC CỦA WEEK 11:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|:---:|---|:---:|:---:|---|
| **2** | **- Dự án CPAWS: release production, hoàn thiện hồ sơ nộp và quay video demo**<br>**- Thực hành Lab 26: Serverless - Hướng dẫn viết Front-end gọi API Gateway**<br>+ Triển Khai Front-end<br>+ Thiết Lập API Gateway<br>+ Kiểm Tra API Với Postman<br>+ Kiểm Tra API Với Front-end<br>+ Dọn Dẹp Tài Nguyên | 23/11/2026 | 25/11/2026 | [Lab 26 Intro](https://000135.awsfcaj.com/vi/1-front-end-deployment/)<br>[API Gateway](https://000135.awsfcaj.com/vi/2-config-api-gw/) |
| **3** | **- Thực hành Lab 27: Serverless với Lambda, API Gateway và SAM**<br>+ Giới thiệu<br>+ Chuẩn bị<br>* Tạo Cloud9 instance<br>* Tạo CodeCommit Repository<br>+ Triển khai ứng dụng mẫu<br>* Triển khai Frontend<br>* Triển khai Backend<br>Triển khai hạ tầng<br>Điền thông tin chuyến đi<br>Kiểm tra cấu hình<br>* Cập nhật Frontend<br>+ Triển khai ride times system<br>* Triển khai Backend<br>* Triển khai Frontend<br>+ Xử lý ảnh trên xe<br>* Triển khai Backend<br>Tạo Chroma Key Lambda function<br>Tạo Photo-Compositing Lambda function<br>Post-processing<br>* Kiểm tra tính năng<br>+ Dọn dẹp tài nguyên | 24/11/2026 | 24/11/2026 | [Lab 27 Intro](https://000066.awsfcaj.com/vi/1-introduce/)<br>[Deploy App](https://000066.awsfcaj.com/vi/3-deployapp/)<br>[Image Processing](https://000066.awsfcaj.com/vi/5-on-ridesphotoprocessing/) |
| **4** | **- Thực hành Lab 28: Workshop khởi đầu Immersion Day của Sagemaker**<br>+ Chuẩn bị<br>* Tạo SageMaker Studio<br>* Tải GitHub repo<br>+ Feature Engineering<br>* Chuẩn bị Dataset<br>* Cài đặt Data Wrangler<br>* Phân tích Dataset<br>* Phân tích tương quan<br>* Chuyển đổi dữ liệu<br>* Feature Store<br>* Export Data tới S3<br>+ Train/Tune/Deploy XGBoost<br>* Train & Tune model<br>* Deploy model<br>* Đánh giá hiệu suất model<br>* Tune model tự động<br>+ Dọn dẹp tài nguyên | 25/11/2026 | 25/11/2026 | [Lab 28 Prepare](https://000200.awsfcaj.com/vi/1-prepare/)<br>[Feature Engineering](https://000200.awsfcaj.com/vi/2-feature-engineering/)<br>[Train Deploy](https://000200.awsfcaj.com/vi/3-train-tune-deploy/) |
| **5** | **- Thực hành Lab 29: AWS Toolkit for VS Code: Amazon Q & CodeWhisperer**<br>+ Giới thiệu<br>+ AWS Toolkit Cho Visual Studio Code<br>+ Kết nối tài khoản AWS<br>+ Thay đổi AWS Region<br>+ Xác thực<br>* IAM Identity Center<br>* AWS IAM credentials<br>* Tạo AWS Builder ID<br>+ Tương tác với các dịch vụ<br>* AWS Explorer<br>* Amazon Q<br>* Amazon CodeWhisperer | 26/11/2026 | 26/11/2026 | [Lab 29 Intro](https://000087.awsfcaj.com/vi/1-introduce/)<br>[Install Toolkit](https://000087.awsfcaj.com/vi/2-install/)<br>[Authentication](https://000087.awsfcaj.com/vi/5-authentication-and-access-for-the-aws-toolkit-for-visual-studio-code/)<br>[Amazon Q](https://000087.awsfcaj.com/vi/6-working-with-aws-services/) |
| **6** | **- Thực hành Lab 30: Quản trị Chi phí và Sử dụng tài nguyên với IAM trên AWS**<br>+ Chuẩn bị<br>* Tạo IAM Group<br>* Tạo IAM user<br>+ Giới hạn theo Region<br>* Tạo policy giới hạn<br>* Gán policy vào group<br>* Kiểm tra hiệu quả policy<br>+ Giới hạn theo EC2 family<br>* Tạo policy giới hạn<br>* Gán policy vào group<br>* Kiểm tra hiệu quả policy<br>+ Giới hạn theo instance size<br>* Cập nhật policy giới hạn<br>* Kiểm tra hiệu quả policy<br>+ Giới hạn theo EBS volume<br>* Tạo policy giới hạn<br>* Gán policy vào group<br>* Kiểm tra hiệu quả policy<br>+ Dọn dẹp tài nguyên | 27/11/2026 | 27/11/2026 | [Lab 30 Intro](https://000064.awsfcaj.com/vi/1-preparation/)<br>[Limit Region](https://000064.awsfcaj.com/vi/2-limit-region/)<br>[Limit Family](https://000064.awsfcaj.com/vi/3-limit-family/)<br>[Limit Size](https://000064.awsfcaj.com/vi/4-limit-size/) |

### THÀNH TỰU ĐẠT ĐƯỢC TRONG WEEK 11:
1. Đóng băng tính năng dự án CPAWS; release production, hoàn thiện và nộp báo cáo workshop và internship report.
2. Triển khai hoàn chỉnh ứng dụng Serverless đa chức năng (bao gồm xử lý ảnh thời gian thực) sử dụng AWS SAM (Serverless Application Model).
3. Hoàn thành pipeline Machine Learning trên SageMaker: Từ khâu làm sạch dữ liệu (Data Wrangler) đến huấn luyện và triển khai model XGBoost.
4. Cấu hình thành công môi trường lập trình tích hợp AI (Amazon Q, CodeWhisperer) trên VS Code, giúp tăng tốc độ viết code và debug.
5. Thiết lập các chính sách IAM chặt chẽ để kiểm soát việc tạo tài nguyên, ngăn chặn vượt ngân sách (Cost Control) và đảm bảo an ninh.
