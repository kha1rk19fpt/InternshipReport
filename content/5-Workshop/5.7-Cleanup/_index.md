---
title: "Cleanup"
date: 2026-11-20
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---
#### Overview

Congratulations on completing the CPAWS workshop! You have:
- Collected price data by trading calendar and stored it in S3
- Built a leakage-free quantile baseline
- Deployed Lambda container images, DynamoDB, Scheduler, API Gateway and Cognito with CDK
- Connected an Amazon Bedrock chatbot to real data through tool use

#### Clean Up Resources

Destroy stacks in reverse creation order:

```bash
cd infra
cdk destroy CpawsApi CpawsAuth CpawsOps CpawsServing --profile cpaws-dev
```

Before destroying `CpawsData`, empty the bucket and delete ECR images:

```bash
aws s3 rm s3://cpaws-dev-lake --recursive --profile cpaws-dev
aws ecr batch-delete-image --profile cpaws-dev --repository-name cpaws-dev-predict \
  --image-ids "$(aws ecr list-images --profile cpaws-dev --repository-name cpaws-dev-predict --query 'imageIds' --output json)"
cdk destroy CpawsData --profile cpaws-dev
```

Repeat for the `cpaws-dev-ingest` repository. Delete the SSM parameter:

```bash
aws ssm delete-parameter --name /cpaws/dev/eia-api-key --profile cpaws-dev
```

#### Verify After Cleanup

- No `Cpaws*` stacks remain in CloudFormation
- No leftover buckets, DynamoDB tables or log groups with `RemovalPolicy.RETAIN`
- Check AWS Billing and Budgets a few days later to make sure no charges remain

**Screenshots:** ...
