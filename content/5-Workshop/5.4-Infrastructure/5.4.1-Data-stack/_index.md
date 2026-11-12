---
title: "Bootstrap and Data Stack"
date: 2026-11-20
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---
#### CDK Bootstrap

Run once per account and region:

```bash
cd infra
pip install -r requirements.txt
cdk bootstrap aws://<ACCOUNT_ID>/ap-southeast-1 --profile cpaws-dev
```

#### Build and Push Container Images

Images are tagged with the commit SHA, and the base image is pinned by digest for reproducible builds.

```bash
TAG=$(git rev-parse --short HEAD)
REPO=<ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com
aws ecr get-login-password --profile cpaws-dev | docker login --username AWS --password-stdin $REPO

DOCKER_BUILDKIT=1 docker build -f docker/predict.Dockerfile -t $REPO/cpaws-dev-predict:$TAG .
docker push $REPO/cpaws-dev-predict:$TAG
```

Do the same for the `cpaws-dev-ingest` image.

#### Deploy `CpawsData`

Always pass every context value in use, otherwise the stack falls back to defaults:

```bash
cdk deploy CpawsData --profile cpaws-dev \
  -c ingest_image_tag=$TAG \
  -c predict_image_tag=$TAG
```

#### Verify

- The `cpaws-dev-lake` S3 bucket exists
- Both ECR repositories contain the image with the pushed tag
- Invoke the ingest Lambda from the Console and check CloudWatch Logs

**Screenshots:** ...
