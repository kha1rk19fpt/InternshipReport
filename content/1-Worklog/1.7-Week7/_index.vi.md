---
title: "Worklog Tuần 7"
date: 2026-10-26
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### MỤC TIÊU TRONG WEEK 7:
- Hoàn thiện và tối ưu hóa kiến trúc dự án CPAWS dựa trên các tiêu chí Well-Architected.
- Nắm vững dịch vụ In-memory Caching với Amazon ElastiCache (Redis): Tạo cluster, kết nối qua CLI/SDK và thao tác dữ liệu.
- Hoàn tất và đẩy Proposal dự án CPAWS lên GitHub.
- Thực hành Networking nâng cao: Transit Gateway, Site-to-Site VPN (với Cisco CSR), ECMP Routing và Hybrid DNS với Route 53.

### CÁC ĐẦU VIỆC CỦA WEEK 7:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
|:---:|---|:---:|:---:|---|
| **2** | **- Họp team: đánh giá các tiêu chí well architected và sửa đổi kiến trúc dự án**<br>+ Dự đoán theo lô bằng Lambda container (NumPy) thay vì endpoint SageMaker chạy liên tục<br>+ Cùng Long chốt target: log return h phiên giao dịch (h = 1–7), 7 phân vị, pinball loss| 26/10/2026 | 26/10/2026 | [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected/)<br>[Proper Scoring Rules](https://doi.org/10.1198/016214506000001437) |
| **3** | **- Thực hành Lab 16: Amazon ElastiCache - Redis**<br>+ Giới thiệu<br>+ Chuẩn bị<br>* Tạo AWS Access Key<br>* Cài đặt AWS CLI<br>* Cấu hình AWS CLI<br>+ Tạo ElastiCache cluster<br>* Tạo subnet group (Console/AWS CLI)<br>* Tạo cluster (cluster mode disabled) (Console/AWS CLI)<br>* Tạo cluster(cluster mode enabled)<br>* Cấp quyền truy cập vào cluster<br>* Kết nối với node của cluster<br>Cluster Mode Disabled<br>Cluster Mode Enabled<br>Endpoints use AWS CLI<br>* Xóa cluster (Console/AWS CLI) | 27/10/2026 | 27/10/2026 | [Lab 16 Intro](https://000061.awsfcaj.com/vi/1-introduce/)<br>[Prereq](https://000061.awsfcaj.com/vi/2-prerequiste/)<br>[Redis Lab](https://000061.awsfcaj.com/vi/3-amazonelasticacheforredis/) |
| **4** | **- Tiếp tục Thực hành Lab 16: Amazon ElastiCache - Redis**<br>+ Sử dụng AWS SDK để ghi và đọc dữ liệu trên ElastiCache<br>* Tạo Elasticache cluster<br>Tạo một cluster (mode disabled cluster)<br>Tạo một cluster (mode enabled cluster)<br>Kiểm tra nhóm người dùng<br>* Kết nối với Elasticache<br>Kết nối tới một cluster (mode disabled cluster)<br>Kết nối tới một cluster (mode enabled cluster)<br>* Set and Get strings<br>* Set and Get a hash with multiple items<br>* Publish (write) and subscribe (read)<br>* Write and read from a stream<br>+ Dọn dẹp tài nguyên | 28/10/2026 | 28/10/2026 | [SDK Redis](https://000061.awsfcaj.com/vi/4-sdkelasticache/)<br>[Cleanup](https://000061.awsfcaj.com/vi/5.cleanupresource/) |
| **5** | **- Hoàn thiện toàn bộ proposal CPAWS**<br>+ Viết tài liệu API (`contract.md`): bảng `Forecasts` và các endpoint `/health`, `/forecasts`, `/forecasts/{asset}` | 29/10/2026 | 29/10/2026 | [API Gateway REST API](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-rest-api.html) |
| **6** | **- Thực hành Lab 17: AWS Networking and Content Delivery**<br>+ Giới thiệu<br>+ Các bước chuẩn bị<br>* Triển khai VPC<br>* Cisco CSR Agreement<br>+ VPC Components Deep Dive<br>+ Transit Gateway và Site-to-Site VPNs<br>* Transit Gateway và Site-to-Site VPNs<br>* Truy cập Cisco CSR Router với Cloud9<br>* Cài đặt Site-to-site VPN<br>* Thêm ECMP Path<br>* Transit Gateway Routing<br>+ Route53 DNS Endpoints và Internal Hosted Zones<br>* Triển khai DNS<br>* Kiểm tra DNS | 30/10/2026 | 30/10/2026 | [Lab 17 Intro](http://000092.awsfcaj.com/vi/1-introduce/)<br>[VPC Deep Dive](https://000092.awsfcaj.com/vi/3-vpcs/)<br>[TGW & VPN](https://000092.awsfcaj.com/vi/4-transitgatewayandvpn/)<br>[Route53](https://000092.awsfcaj.com/vi/5-route53/) |

### THÀNH TỰU ĐẠT ĐƯỢC TRONG WEEK 7:
1. Kiến trúc dự án CPAWS được hoàn thiện.
2. Hoàn thành Lab 16: Nắm được kiến thức về Amazon ElastiCache (Redis), thực hành tạo Cluster, kết nối và thao tác dữ liệu nâng cao (Pub/Sub, Stream) thông qua AWS SDK.
3. Hoàn tất tài liệu Proposal và tài liệu API của dự án, nộp thành công lên GitHub Repository.
4. Hoàn thành Lab 17: Triển khai mô hình mạng phức tạp sử dụng Transit Gateway kết nối VPN với Router Cisco CSR, cấu hình định tuyến ECMP và phân giải tên miền Hybrid với Route 53.
