---
title: "Bootstrap và stack dữ liệu"
date: 2026-11-20
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---
#### Bootstrap CDK

Chỉ chạy một lần cho mỗi tài khoản và region:

```bash
cd infra
pip install -r requirements.txt
cdk bootstrap aws://<ACCOUNT_ID>/ap-southeast-1 --profile cpaws-dev
```

#### Build và đẩy container image

Image được tag bằng commit SHA, base image ghim theo digest để build lặp lại được.

```bash
TAG=$(git rev-parse --short HEAD)
REPO=<ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com
aws ecr get-login-password --profile cpaws-dev | docker login --username AWS --password-stdin $REPO

DOCKER_BUILDKIT=1 docker build -f docker/predict.Dockerfile -t $REPO/cpaws-dev-predict:$TAG .
docker push $REPO/cpaws-dev-predict:$TAG
```

Làm tương tự cho image `cpaws-dev-ingest`.

#### Deploy `CpawsData`

Luôn truyền đủ context đang dùng, nếu không stack sẽ quay về giá trị mặc định:

```bash
cdk deploy CpawsData --profile cpaws-dev \
  -c ingest_image_tag=$TAG \
  -c predict_image_tag=$TAG
```

#### Kiểm tra

- S3 bucket `cpaws-dev-lake` đã được tạo
- Hai repository ECR có image với tag vừa đẩy
- Gọi thử Lambda ingest trên Console và xem log trong CloudWatch Logs

**Ảnh minh họa:** ...
