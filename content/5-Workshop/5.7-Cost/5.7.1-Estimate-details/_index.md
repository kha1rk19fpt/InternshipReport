---
title: "Appendix: estimate details"
date: 2026-11-25
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---
This page lists every assumption and all 34 lines of the cost estimate Long prepared on 24/11/2026 (summarised in section [5.7](../)). The estimate covers one `cpaws-dev-*` environment in Singapore; the November observations include development, testing and demo activity, and no full production month is available yet. These are modeled costs, not a bill or a spending limit.

The Pricing Calculator export is dated 24/11/2026 23:57 (UTC+7), rate timestamp 23/11/2026 23:33, mode `BEFORE_DISCOUNTS`, 34 lines, all in `ap-southeast-1`. Bedrock and Scheduler were estimated with the public Pricing Calculator (links in section 5.7); SSM Parameter Store follows the Standard tier pricing. The CSV exported from the Console contains the full Account ID, so it is not published.

#### 1. Method and workload

Separate observed usage, deployed configuration, projected assumptions and calculated cost. The historical-cost field of new estimate lines is not an actual bill. Some account/region-level Cost Explorer quantities are used as proxies rather than fully tag-attributed project usage. For cumulative usage over d days, extrapolate `U_month = U_observed × 30 / d`, rounding appropriately. Use the current inventory and schedules for persistent resources, MAU and cron jobs rather than mechanically extrapolating. Annual cost assumes 12 comparable months, excluding taxes and price changes.

##### 1.1. Baseline application workload

| Parameter | Basis | Monthly baseline |
|---|---|---:|
| Cognito Essentials MAU | 3 in October; 4 during Nov 1–23; assumption of 5 active users | 5 |
| Forecast API | Assumed 60 requests/day | 1800 |
| Accuracy API | Assumed 20 requests/day | 600 |
| Chat API | Assumed 10 chats/day | 300 |
| API Gateway | Sum of three APIs; health/preflight/retries not separately included | 2700 |
| Scheduler | Two schedules × five days/week; round up to 22 each | 44 |
| Lambda jobs | 22 ingest, 22 predict, 22 accuracy; one predict per ingest | 66 |
| Total Lambda | 2700 API + 66 jobs | 2766 |
| Bedrock | 13 calls over 7 successful chats, Nov 22–23 | ~557 calls |
| Amplify | 679 requests over 2 days × 15; hosting traffic indicator only | 10185 |

The chatbot has source-level limits of 20 chats per user per day and 100 globally per day. The baseline is below the design limit of 3000 chats per 30 days; quotas are not a monetary or token cap.

##### 1.2. Lambda compute

Source: Lambda/CloudWatch, Nov 17–23. x86_64, 512 MB ephemeral storage. Compute = memory in GB × duration in seconds × invocations. Input duration matches the saved estimate: chat uses billed duration; other functions use runtime proxies, with a billed-duration cross-check for predict.

| Function | RAM MB | Observed average runtime ms | Observed invocations | Modeled duration s | Monthly invocations | GB-s/month |
|---|---:|---:|---:|---:|---:|---:|
| ingest | 1024 | 12610 | 5 | 12.610 | 22 | 277.420 |
| predict | 1024 | 5392 | 5 | 5.392 | 22 | 118.624 |
| accuracy-job | 1024 | 49621 | 5 | 49.621 | 22 | 1091.662 |
| forecasts-api | 256 | 1299 | 71 | 1.299 | 1800 | 584.550 |
| accuracy-api | 256 | 314 | 48 | 0.314 | 600 | 47.100 |
| chat-api | 512 | 4596 | 6 | 4.652 | 300 | 697.800 |
| **Total** | | | | | **2766** | **2817.156** |

Predict billed-duration logs average approximately 5.393 seconds; the estimate uses 5.392 seconds. Runtime is not necessarily billed duration. Additional retries and variable cold starts are excluded. Source sets Scheduler retries to zero and ingest Lambda asynchronous retries to one; the live Console displayed a dash for retry policy, so that field did not verify the source setting. One successful execution per scheduled task is assumed.

##### 1.3. Storage, requests and network traffic

| Item | Evidence / basis | Monthly assumption |
| --- | --- | --- |
| S3 storage | ~89.25 MB lake + ~1.06 MB CDK assets; snapshot inventory | 0.10 GB-Mo |
| S3 Tier 1 / Tier 2 | 218 / 3381 requests, 17–23/11 → ×30/7 | 934 / 14490 requests |
| DynamoDB on-demand | 4 bảng / tables; final Calculator inputs; historical measurement detail not retained here | 6381 RRU; 11310 WRU; 0.00158 GB-Mo |
| Amplify storage | 0.0038870168 GB usage 01–23/11; 20 active days (04–23/11) → ×30/20 | 0.006 GB |
| Amplify outbound | 5,547,694 bytes / 2 days (22–23/11) → 83,215,410 bytes / 30 days | 0.084 GB |
| ECR storage | 0.7476365831 GB-Mo / 23 days → ×30/23; 8 visible images in 2 repos | 0.98 GB-Mo |
| ECR outbound | 0.1287299758 GB / 23 days → ×30/23 | 0.168 GB |
| CloudWatch storage | 0.0000616697 GB-Mo / 23 days; 23/11 daily 0.0000047902 ×30 | 0.00015 GB-Mo |
| CloudWatch vended logs | 0.0002119546 GB / 23 days → ×30/23 | 0.00028 GB |
| CloudWatch alarms | 4.0626139866 alarm-month usage / 23 days; 6 active alarms | 6 standard alarms |
| CloudWatch custom metrics | 2 configured metric filters; no confirmed actual billable metric usage | 2 metrics (reserve) |
| CloudWatch GMD / API / query | 8 metrics / 56 requests / 0.0001027301 GB scanned, 23 days → ×30/23 | 11 metrics / 74 requests / 0.00014 GB |
| SNS | 9 API requests / 23 days; 1 confirmed email subscription | 12 requests / 12 emails (assumption) |
| KMS | 107 requests / 23 days; 0 customer-managed keys, 3 AWS-managed keys | 140 symmetric requests (assumption) |
| SSM | 2 Standard parameters; Standard throughput 40 TPS | 2 parameters; $0 SSM fee |

Explicitly modeled egress is 0.084 GB Amplify and 0.168 GB ECR, totaling 0.252 GB/month. This is data volume, not network throughput. No S3 transfer was recorded in the Nov 17–23 observation; API/SNS egress was not separately measured. Amplify build/SSR/WAF usage is assumed zero for static hosting with no recorded billable build minutes. ECR billed storage is used instead of summing image sizes, which may share layers. S3 is modeled as a storage snapshot, without future version growth. CloudWatch IncomingBytes (227588 bytes across six log groups) is a cross-check only; adding it to vended logs would double count. The seventh legacy log group did not appear; non-vended ingestion is provisionally zero.

##### 1.4. Bedrock token-based estimate

The source router defaults to the complex branch, using `global.anthropic.claude-haiku-4-5-20251001-v1:0`, three maximum tool rounds and 1500 output tokens per complex model call. Nova 2 Lite exists in source but has no confirmed usage in the observed window. Caching, image inputs and Guardrails are not included.

| Parameter | Value |
|---|---:|
| CloudWatch Nov 17–23 | 15 calls; 82198 input / 3708 output tokens |
| Selected Nov 22–23 log sample | 7 successful chats; 13 calls; 71904 input / 3191 output |
| Calls per chat | 1.857 |
| Input / output tokens per chat | 10272 / 455.857 |
| 300 chats/month | ~557 calls; 3081600 input / ~136757 output tokens |
| Calculator Global On-Demand Standard rates | $1/1M input; $5/1M output |
| Public Calculator inputs | 1 request/min, 1 hour/day, 1712 input / 76 output tokens per request |
| Equivalent monthly tokens | 3081600 input / 136800 output |
| **Monthly cost** | **$3.765600** |

The Calculator's 1800 equivalent requests encode the token volume, not expected real calls. Integer input rounding explains the output-token difference. Six of seven sampled chats used tools. This small sample is not a full production distribution; longer questions or additional tool calls can increase cost.

##### 1.5. EventBridge Scheduler

Both schedules are enabled Tuesday–Saturday; `InvocationAttemptCount` for group `default` totaled 23 during Nov 1–23. The accuracy schedule was created Nov 11, so the current schedule rather than a direct extrapolation gives 44 invocations/month. The exported Public estimate shows 44 invocations, 1 KB payload and $0.00 after the 14 million monthly invocation allowance. The gross reference charge is $0.000044 at $1 per million, before allowance. Extra retries are excluded.

#### 2. All 34 estimate lines

Quantities and costs come directly from the Pricing Calculator export, without intermediate rounding; Lambda groups are shortened to the function name.

| Service code | Group / function | Usage type | Operation | Monthly quantity | USD/month |
| --- | --- | --- | --- | --- | --- |
| AWSLambda | ingest | APS1-Lambda-GB-Second | — | 277.42 Lambda-GB-Second | 0.004623676 |
| AWSLambda | ingest | APS1-Request | — | 22 Request | 0.0000044 |
| AWSLambda | accuracy-api | APS1-Lambda-GB-Second | — | 47.1 Lambda-GB-Second | 0.000785002 |
| AWSLambda | accuracy-api | APS1-Request | — | 600 Request | 0.00012 |
| AWSLambda | accuracy-job | APS1-Lambda-GB-Second | — | 1091.662 Lambda-GB-Second | 0.018194403 |
| AWSLambda | accuracy-job | APS1-Request | — | 22 Request | 0.0000044 |
| AWSLambda | chat-api | APS1-Lambda-GB-Second | — | 697.8 Lambda-GB-Second | 0.011630023 |
| AWSLambda | chat-api | APS1-Request | — | 300 Request | 0.00006 |
| AWSLambda | forecasts-api | APS1-Lambda-GB-Second | — | 584.55 Lambda-GB-Second | 0.009742519 |
| AWSLambda | forecasts-api | APS1-Request | — | 1800 Request | 0.00036 |
| AWSLambda | predict | APS1-Lambda-GB-Second | — | 118.624 Lambda-GB-Second | 0.001977071 |
| AWSLambda | predict | APS1-Request | — | 22 Request | 0.0000044 |
| AmazonApiGateway | CPAWS-API-Gateway | APS1-ApiGatewayRequest | ApiGatewayRequest | 2700 APS1-ApiGatewayRequest | 0.011475 |
| AmazonDynamoDB | CPAWS-DynamoDB | APS1-ReadRequestUnits | PayPerRequestThroughput | 6381 ReadRequestUnits | 0.000909292 |
| AmazonDynamoDB | CPAWS-DynamoDB | APS1-TimedStorage-ByteHrs | — | 0.00158 GB-Mo | 0 |
| AmazonDynamoDB | CPAWS-DynamoDB | APS1-WriteRequestUnits | PayPerRequestThroughput | 11310 WriteRequestUnits | 0.0080301 |
| AmazonS3 | CPAWS-S3 | APS1-Requests-Tier1 | — | 934 Requests | 0.00467 |
| AmazonS3 | CPAWS-S3 | APS1-Requests-Tier2 | — | 14490 Requests | 0.005796 |
| AmazonS3 | CPAWS-S3 | APS1-TimedStorage-ByteHrs | — | 0.1 GB-Mo | 0.0025 |
| AWSAmplify | CPAWS-Amplify | APS1-DataStorage | — | 0.006 GigaBytes | 0.000138 |
| AWSAmplify | CPAWS-Amplify | APS1-DataTransferOut | — | 0.084 GigaBytes | 0.0126 |
| AmazonCognito | CPAWS-Cognito | APS1-CognitoEssentialsMAU | CognitoEssentialsOperation | 5 CognitoUserPoolsMAU | 0.075 |
| AmazonECR | CPAWS-ECR | APS1-TimedStorage-ByteHrs | — | 0.98 GB-Mo | 0.098 |
| AWSDataTransfer | CPAWS-ECR | APS1-DataTransfer-Out-Bytes | — | 0.168 GB | 0.02016 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-CW:AlarmMonitorUsage | — | 6 Alarms | 0.6 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-CW:GMD-Metrics | GetMetricData | 11 Metrics | 0.00011 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-CW:MetricMonitorUsage | — | 2 Metrics | 0.6 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-CW:Requests | — | 74 Requests | 0.00074 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-DataScanned-Bytes | StartQuery | 0.00014 GB | 0.00000098 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-TimedStorage-ByteHrs | — | 0.00015 GB-Mo | 0.0000045 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-VendedLog-Bytes | PutLogEvents | 0.00028 GB | 0.000196 |
| AmazonSNS | CPAWS-SNS | APS1-DeliveryAttempts-SMTP | — | 12 Notifications | 0 |
| AmazonSNS | CPAWS-SNS | APS1-Requests-Tier1 | — | 12 Requests | 0 |
| awskms | CPAWS-KMS | ap-southeast-1-KMS-Requests | — | 140 Requests | 0.00042 |
| **Console subtotal** |  |  |  | 34 lines | 1.488255766 |

#### References

- **R1.** [FCAJ Workshop standards](https://hcm-rules.awsfcaj.com/3-project/) — sections 4.3 (COST), 4.5 (VI/EN).
- **R2.** [FCAJ Workshop scoring criteria](https://hcm-rules.awsfcaj.com/5-scoring/).
- **R3.** [AWS Pricing Calculator](https://calculator.aws/) and [AWS Well-Architected — Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html).
- **R4.** [Amazon Bedrock Pricing](https://aws.amazon.com/bedrock/pricing/) and [Claude Haiku 4.5 model card](https://docs.aws.amazon.com/bedrock/latest/userguide/model-card-anthropic-claude-haiku-4-5.html). Model unit rates in this report come from the dated Calculator setup.
- **R5.** [Amazon EventBridge Pricing](https://aws.amazon.com/eventbridge/pricing/).
- **R6.** [AWS Systems Manager Pricing](https://aws.amazon.com/systems-manager/pricing/) and [AWS KMS Pricing](https://aws.amazon.com/kms/pricing/).
- **R7.** [AWS CDK destroy](https://docs.aws.amazon.com/cdk/v2/guide/ref-cli-cmd-destroy.html) and [CDK retained-resource troubleshooting](https://docs.aws.amazon.com/cdk/v2/guide/troubleshooting.html).
- **R8.** [Emptying a general purpose S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/empty-bucket.html) and [Deleting a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/delete-bucket.html).
- **R9.** Additional pricing references: [Lambda](https://aws.amazon.com/lambda/pricing/), [API Gateway](https://aws.amazon.com/api-gateway/pricing/), [DynamoDB](https://aws.amazon.com/dynamodb/pricing/), [S3](https://aws.amazon.com/s3/pricing/), [Amplify](https://aws.amazon.com/amplify/pricing/), [Cognito](https://aws.amazon.com/cognito/pricing/), [ECR](https://aws.amazon.com/ecr/pricing/), [CloudWatch](https://aws.amazon.com/cloudwatch/pricing/), [SNS](https://aws.amazon.com/sns/pricing/).
- **R10.** [AWS Budgets](https://aws.amazon.com/aws-cost-management/aws-budgets/) and [AWS Cost Anomaly Detection](https://aws.amazon.com/aws-cost-management/aws-cost-anomaly-detection/).

*Pricing snapshot is dated Nov 23–24, 2026; linked prices and benefits can change. / Mốc giá 23–24/11/2026; bảng giá và ưu đãi có thể thay đổi.*
