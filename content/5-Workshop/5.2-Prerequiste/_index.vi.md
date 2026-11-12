---
title: "Yêu cầu tiên quyết"
date: 2026-11-20
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---
#### Công cụ cần cài

| Công cụ | Phiên bản gợi ý | Dùng để |
|---|---|---|
| Python | 3.12 | Code dữ liệu, mô hình, Lambda, CDK |
| Node.js | 20 LTS | AWS CDK CLI và web (React + Vite) |
| AWS CLI | v2 mới nhất | Đăng nhập và thao tác với AWS |
| AWS CDK CLI | v2 | `npm install -g aws-cdk` |
| Docker (bật BuildKit) | mới nhất | Build container image cho Lambda |
| Git | mới nhất | Quản lý mã nguồn |

#### Tài khoản và quyền

- Tài khoản AWS dùng **IAM user trong group**, đăng nhập bằng phiên ngắn hạn, không tạo access key dài hạn
- Region **ap-southeast-1 (Singapore)**
- Bật quyền dùng model trên Amazon Bedrock (Claude Haiku 4.5, Amazon Nova) qua cross-region inference
- Tạo **AWS Budgets** cảnh báo trước khi bắt đầu

```bash
# đăng nhập bằng profile riêng cho dự án
aws login --profile cpaws-dev
aws sts get-caller-identity --profile cpaws-dev
```

#### Bí mật

- File `.env` nằm trong `.gitignore`; chỉ commit `.env.example` (tên biến, không có giá trị)
- API key của EIA lưu trong **SSM Parameter Store** dạng `SecureString`

```bash
aws ssm put-parameter --profile cpaws-dev \
  --name /cpaws/dev/eia-api-key --type SecureString --value "<EIA_API_KEY>"
```

{{% notice warning %}}
Không commit API key, mật khẩu hay Account ID đầy đủ lên repo public.
{{% /notice %}}

**Ảnh minh họa:** ...
