---
title: "Dọn dẹp"
date: 2026-11-20
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---
#### Tổng quan

Chúc mừng bạn đã hoàn thành workshop CPAWS! Bạn đã:
- Thu thập dữ liệu giá theo lịch giao dịch và lưu vào S3
- Xây mốc dự báo phân vị không rò rỉ dữ liệu
- Triển khai Lambda container, DynamoDB, Scheduler, API Gateway, Cognito bằng CDK
- Kết nối chatbot Amazon Bedrock với dữ liệu thật qua tool use

#### Dọn dẹp tài nguyên

Xóa các stack theo thứ tự ngược với lúc tạo:

```bash
cd infra
cdk destroy CpawsApi CpawsAuth CpawsOps CpawsServing --profile cpaws-dev
```

Trước khi xóa `CpawsData`, làm rỗng bucket và xóa image trong ECR:

```bash
aws s3 rm s3://cpaws-dev-lake --recursive --profile cpaws-dev
aws ecr batch-delete-image --profile cpaws-dev --repository-name cpaws-dev-predict \
  --image-ids "$(aws ecr list-images --profile cpaws-dev --repository-name cpaws-dev-predict --query 'imageIds' --output json)"
cdk destroy CpawsData --profile cpaws-dev
```

Làm tương tự với repository `cpaws-dev-ingest`. Xóa tham số trong SSM Parameter Store:

```bash
aws ssm delete-parameter --name /cpaws/dev/eia-api-key --profile cpaws-dev
```

#### Kiểm tra sau khi dọn

- CloudFormation không còn stack `Cpaws*`
- Không còn bucket, bảng DynamoDB hay log group có `RemovalPolicy.RETAIN` bị sót
- Xem AWS Billing và Budgets vài ngày sau để chắc chắn không còn chi phí phát sinh

**Ảnh minh họa:** ...
