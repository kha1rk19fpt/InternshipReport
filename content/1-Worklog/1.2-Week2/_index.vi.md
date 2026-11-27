---
title: "Worklog Tuần 2"
date: 2026-09-21
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### MỤC TIÊU TRONG WEEK 2:
- Hiểu và thực hành quản lý truy cập người dùng an toàn với AWS IAM.
- Nắm vững kiến trúc mạng Amazon VPC và các lớp bảo mật mạng (Security Groups, NACL).
- Triển khai kết nối Hybrid Cloud thông qua AWS Site-to-Site VPN.
- Hệ thống hóa kiến thức mạng đã học và khởi động dự án nhóm CPAWS.

### CÁC ĐẦU VIỆC CỦA WEEK 2:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|:---:|---|:---:|:---:|---|
| **2** | **- Thực hành Lab 4: Quản trị quyền truy cập với AWS Identity and Access Management (IAM)**<br>+ IAM User và IAM Group<br>+ IAM Role<br>+ Chuyển đổi Role<br>**- Thực hành Lab 5: Bắt đầu với Amazon Virtual Private Cloud (VPC) và AWS Site-to-Site VPN**<br>+ Giới thiệu Amazon VPC:<br>* Subnets<br>* Route Table<br>* Internet Gateway<br>* NAT Gateway<br>+ Network Security với Security Groups và Network ACLs:<br>* Security groups<br>* Network ACLs<br>* VPC Resource Map | 21/09/2026 | 21/09/2026 | [Lab 4 Guide](https://000002.awsfcaj.com/vi/)<br>[Lab 5 Intro](https://000003.awsfcaj.com/vi/1-introduce/)<br>[Lab 5 Firewall](https://000003.awsfcaj.com/vi/2-firewallinvpc/) |
| **3** | - Lên văn phòng lần đầu tiên và được trao đổi với cả mentor.<br>- Gặp gỡ, làm quen và lập team với các thành viên tham gia chương trình FCAJ.<br>**- Tiếp tục thực hành Lab 5:**<br>+ Chuẩn bị Môi trường:<br>* Tạo VPC<br>* Tạo Subnet<br>* Tạo Internet Gateway<br>* Tạo Route Table<br>* Tạo Security Group<br>* Kích hoạt VPC Flow Logs<br>+ Triển khai Amazon EC2 Instance<br>* Tạo EC2 Instances<br>* Kiểm thử Phương thức Kết nối<br>* Tạo Multi-AZ NAT Gateway<br>* Sử dụng Reachability Analyzer<br>* Cấu hình EIC Endpoint<br>* AWS Systems Manager Session Manager<br>* CloudWatch Monitoring & Alerting | 22/09/2026 | 22/09/2026 | [Lab 5 Prerequisite](https://000003.awsfcaj.com/vi/3-prerequisite/)<br>[Lab 5 Create EC2](https://000003.awsfcaj.com/vi/4-createec2server/) |
| **4** | **- Tiếp tục thực hành Lab 5:**<br>+ Thiết lập AWS Site-to-Site VPN:<br>* Tạo môi trường VPN<br>Tạo VPC cho VPN<br>Tạo EC2 Instance<br>* Cấu hình kết nối VPN<br>Tạo Virtual Private Gateway<br>Tạo Customer Gateway<br>Tạo kết nối VPN<br>Cấu hình Customer Gateway<br>Tùy chỉnh AWS VPN Tunnel<br>Cấu hình VPN Nâng cao | 23/09/2026 | 23/09/2026 | [Lab 5 VPN](https://000003.awsfcaj.com/vi/5-vpnsitetosite/) |
| **5** | **- Tổng hợp ghi chú Lab 4 và Lab 5**<br>+ Vẽ lại sơ đồ VPC (Subnet, Route Table, IGW, NAT Gateway) và luồng Site-to-Site VPN trên draw.io<br>+ Ôn lại khác biệt giữa Security Group và Network ACL | 24/09/2026 | 24/09/2026 | [Lab 5 VPN](https://000003.awsfcaj.com/vi/5-vpnsitetosite/) |
| **6** | **- Họp team project: lên ý tưởng, phân tích, nghiên cứu để chọn lọc idea phù hợp**<br>+ Nhóm CPAWS (5 thành viên) thống nhất cách làm việc và kênh trao đổi<br>+ Đề xuất hướng dự báo giá hàng hóa giao dịch chung giữa thế giới và Việt Nam (vàng, bạc, dầu thô) | 25/09/2026 | 25/09/2026 | [World Bank Commodity Markets](https://www.worldbank.org/en/research/commodity-markets) |

### THÀNH TỰU ĐẠT ĐƯỢC TRONG WEEK 2:
1. Đã được lên văn phòng AWS lần đầu tiên.
2. Hoàn thành Lab 4, thành thạo việc tạo IAM User, Group và Role để phân quyền truy cập hợp lý.
3. Xây dựng thành công hạ tầng mạng VPC với đầy đủ các thành phần: Subnet, Route Table, IGW, NAT Gateway.
4. Cấu hình bảo mật mạng nhiều lớp sử dụng Security Groups và Network ACLs.
5. Triển khai và giám sát EC2 Instance, cấu hình truy cập an toàn qua Session Manager và EIC Endpoint.
6. Thiết lập thành công kết nối AWS Site-to-Site VPN để kết nối giữa AWS và môi trường giả lập On-premise.
7. Thành lập nhóm CPAWS và đưa ra các hướng ý tưởng ban đầu cho dự án, trong đó có hướng dự báo giá vàng, bạc, dầu thô.
