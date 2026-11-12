---
title: "Triển khai hạ tầng với CDK"
date: 2026-11-20
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---
#### Tổng quan

Toàn bộ hạ tầng được viết bằng **AWS CDK (Python)** trong thư mục `infra/`, chia thành nhiều stack nhỏ để deploy và review độc lập.

1. [Bootstrap và stack dữ liệu](5.4.1-Data-stack/)
2. [Luồng chạy hằng ngày](5.4.2-Daily-flow/)
3. [API và xác thực](5.4.3-API-Cognito/)

#### Nguyên tắc chi phí và bảo mật

- Không dùng endpoint chạy 24/7, NAT Gateway hay OpenSearch Serverless
- Lambda gọi API bên ngoài đặt **ngoài VPC**
- Mỗi Lambda có IAM role quyền tối thiểu
- Ước tính chi phí trước khi thêm tài nguyên mới
