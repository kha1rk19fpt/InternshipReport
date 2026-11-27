---
title: "Cleanup"
date: 2026-11-20
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---
#### Summary

In this workshop, you have:
- Collected prices by trading calendar and stored versioned snapshots on S3
- Built a leakage-free quantile forecasting baseline, evaluated with pinball loss and a test set run only once
- Deployed container Lambdas, DynamoDB, EventBridge Scheduler, API Gateway and Cognito with CDK
- Chained ingest to predict with Lambda Destinations and written each forecast exactly once
- Built a Bedrock chatbot that uses tools to answer with real data, with question limits
- Monitored with CloudWatch alarms and tracked cost with Budgets

{{% notice info %}}
The CPAWS team keeps the system running until final grading is over, and cleans up afterwards. The steps below are for the end of the project, or for when you rebuild this workshop in your own account.
{{% /notice %}}

#### Prepare and stop new work

1. **Confirm the scope and back up.** Check the account (compare only the last 4 digits), the Region and the stack names. Save what must be kept for reference: evaluation results, model artifacts, screenshots and cost figures.
2. **Disable the two EventBridge Scheduler schedules** (`cpaws-dev-ingest-daily`, `cpaws-dev-accuracy-daily`) before deleting any stack, then wait for running Lambda invocations to finish. This stops new work and avoids an ingest run in the middle of the deletion.

```bash
aws sts get-caller-identity --profile cpaws-dev
aws cloudformation list-stacks --region ap-southeast-1 --profile cpaws-dev \
  --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --query "StackSummaries[].StackName"
```

Do not paste output that contains the Account ID or full ARNs into public documents.

#### Delete the stacks

Delete in the reverse order of creation. `CpawsApi` uses the tables and User Pool of two other stacks, so it goes first:

```bash
cd infra
cdk destroy CpawsApi --profile cpaws-dev
cdk destroy CpawsOps --profile cpaws-dev
cdk destroy CpawsData --profile cpaws-dev
cdk destroy CpawsServing --profile cpaws-dev
cdk destroy CpawsAuth --profile cpaws-dev
```

Delete one stack at a time, without `--all` or `--force`, and wait for `DELETE_COMPLETE` (or read the `DELETE_FAILED` reason) before the next one. In the `dev` environment, both ECR repositories are set to `DESTROY` and are emptied automatically when the stack is deleted. That means the images kept for rollback are deleted too.

#### `RETAIN` resources to delete by hand

Some resources are kept when their stack is deleted, to avoid losing data by mistake:

| Resource | How to delete |
|---|---|
| S3 `cpaws-dev-lake` | The bucket is versioned, so every object version must be deleted before the bucket can be (Console: **Empty**, then **Delete**). Before that, an administrator must remove the explicit deny statements that block deletion under `eval/` |
| DynamoDB `cpaws-dev-forecasts`, `cpaws-dev-news-labels`, `cpaws-dev-accuracy` | `aws dynamodb delete-table --table-name <TABLE_NAME> --profile cpaws-dev` |
| Cognito User Pool `cpaws-dev-users` | `aws cognito-idp delete-user-pool --user-pool-id <USER_POOL_ID> --profile cpaws-dev` |
| Log groups of the API Lambdas | Delete in CloudWatch > Log Management once the logs are no longer needed |

`aws s3 rb --force` does not remove old versions and delete markers in a versioned bucket, so use **Empty** in the Console. If an explicit deny or Object Lock blocks deletion, stop and contact the data owner; do not remove the protection just to finish faster.

#### Resources created outside CDK

```bash
aws ssm delete-parameter --name /cpaws/dev/eia-api-key --profile cpaws-dev
```

- Delete app `cpaws-dev-web` in the Amplify Console.
- Keep the CDK bootstrap bucket, repository and parameter, and the AWS managed KMS keys, if the account is shared.
- Keep AWS Budgets for a few more days to see whether any cost is still incurred, then delete it.

#### Check after cleanup

- CloudFormation has no `Cpaws*` stacks left. Delete the bootstrap stack `CDKToolkit` only if CDK is no longer used in the account.
- SNS topic `cpaws-dev-alarms` belongs to stack `CpawsOps`, so it is gone once that stack is deleted; confirm in the SNS Console.
- No bucket, DynamoDB table, User Pool or log group of the project remains.
- Check AWS Billing and Cost Explorer the next day and after the next billing period: billing data lags, so do not expect the cost to drop to zero immediately.
- Record the resources kept on purpose, why, and who owns them.
