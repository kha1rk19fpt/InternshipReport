---
title: "Worklog Tuần 12"
date: 2026-11-30
weight: 12
chapter: false
pre: " <b> 1.12 </b> "
---

### MỤC TIÊU TRONG WEEK 12:
- Duy trì Production CPAWS chạy ổn định tới khi chấm xong; theo dõi alarm và job độ tin cậy hằng ngày.
- Chốt chi phí AWS của kỳ cùng nhóm và tổng kết bài học dự án.
- Làm chủ kỹ năng quản trị chi phí và tuân thủ (Governance) thông qua IAM Policy nâng cao (giới hạn Region, Instance Type).
- Thiết lập hệ thống giám sát toàn diện: Từ tầng mạng (VPC Flow Logs), tầng ứng dụng (Grafana) đến tầng dịch vụ AWS (CloudWatch).

### CÁC ĐẦU VIỆC CỦA WEEK 12:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|:---:|---|:---:|:---:|---|
| **2** | **- Thực hành Lab 30: Quản trị Chi phí và Sử dụng tài nguyên với IAM trên AWS**<br>+ Chuẩn bị<br>* Tạo IAM Group<br>* Tạo IAM user<br>+ Giới hạn theo Region<br>* Tạo policy giới hạn<br>* Gán policy vào group<br>* Kiểm tra hiệu quả policy<br>+ Giới hạn theo EC2 family<br>* Tạo policy giới hạn<br>* Gán policy vào group<br>* Kiểm tra hiệu quả policy<br>+ Giới hạn theo instance size<br>* Cập nhật policy giới hạn<br>* Kiểm tra hiệu quả policy<br>+ Giới hạn theo EBS volume<br>* Tạo policy giới hạn<br>* Gán policy vào group<br>* Kiểm tra hiệu quả policy<br>+ Dọn dẹp tài nguyên | 30/11/2026 | 30/11/2026 | [Lab 30 Intro](https://000064.awsfcaj.com/vi/1-preparation/)<br>[Limit Region](https://000064.awsfcaj.com/vi/2-limit-region/)<br>[Limit Family](https://000064.awsfcaj.com/vi/3-limit-family/)<br>[Limit Size](https://000064.awsfcaj.com/vi/4-limit-size/) |
| **3** | **- Thực hành Lab 31: Giám sát hạ tầng mạng với VPC Flow Log**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>+ Kích hoạt VPC Flow Logs<br>+ Giám sát hạ tầng mạng<br>+ Dọn dẹp tài nguyên | 01/12/2026 | 01/12/2026 | [Lab 31 Intro](https://000074.awsfcaj.com/vi/1-introduce/)<br>[VPC Flow Logs](https://000074.awsfcaj.com/vi/4-enablevpcflowlogs/) |
| **4** | **- Thực hành Lab 32: Bắt đầu với Grafana trên AWS**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>* Tạo VPC và subnet<br>* Tạo Security Group<br>* Tạo EC2 Instance<br>* Tạo IAM User<br>* Tạo IAM Role<br>* Gán IAM Role<br>+ Cài đặt Grafana<br>+ Giám sát với Grafana<br>+ Dọn dẹp tài nguyên | 02/12/2026 | 02/12/2026 | [Lab 32 Intro](https://000029.awsfcaj.com/vi/1-introduce/)<br>[Install Grafana](https://000029.awsfcaj.com/vi/3-installgrafana/) |
| **5** | **- (Kế hoạch) Dự án CPAWS: tổng kết**<br>+ Kiểm tra Production và alarm vẫn hoạt động<br>+ Cập nhật trang workshop theo chi phí chốt kỳ | 03/12/2026 | 03/12/2026 | [AWS Cost Explorer](https://aws.amazon.com/aws-cost-management/aws-cost-explorer/) |
| **6** | **- Thực hành Lab 33: AWS CloudWatch Workshop**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>+ CloudWatch Metric<br>* Viewing Metrics<br>* Search expressions<br>* Math expressions<br>* Dynamic Labels<br>+ CloudWatch Logs<br>* CloudWatch Logs<br>* CloudWatch Logs Insights<br>* CloudWatch Metric Filter<br>+ CloudWatch Alarms<br>+ CloudWatch Dashboards<br>+ Dọn dẹp tài nguyên | 04/12/2026 | 04/12/2026 | [Lab 33 Intro](https://000036.awsfcaj.com/vi/1-introduce/)<br>[Metrics](https://000036.awsfcaj.com/vi/3-cloudwatchmetric/)<br>[Logs](https://000036.awsfcaj.com/vi/4-cloudwatchlogs/)<br>[Alarms](https://000036.awsfcaj.com/vi/5-cloudwatchalarm/) |

### THÀNH TỰU ĐẠT ĐƯỢC TRONG WEEK 12:
1. (Kế hoạch) Production CPAWS chạy liên tục tới khi chấm xong, có số chi phí chốt kỳ.
2. (Kế hoạch) Tổng kết bài học dự án và cập nhật trang workshop.
3. Thiết lập các chính sách IAM chặt chẽ để kiểm soát việc tạo tài nguyên, ngăn chặn vượt ngân sách (Cost Control) và đảm bảo an ninh.
4. Xây dựng hệ thống giám sát đa tầng: Theo dõi lưu lượng mạng với VPC Flow Logs và trực quan hóa Metric ứng dụng trên Grafana/CloudWatch Dashboard.
