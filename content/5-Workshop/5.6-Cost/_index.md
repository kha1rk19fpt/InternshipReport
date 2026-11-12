---
title: "Cost Estimation and Optimization"
date: 2026-11-20
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Assumptions

| Assumption | Value |
|---|---|
| Region | ap-southeast-1 (Singapore) |
| Daily flow | about 22 trading days/month, 1 ingest + 1 predict run per day |
| Test users | about 15 people, about 10,000 API calls/month |
| Chatbot | about 2,000 questions/month, on average 3,000 input and 500 output tokens each |
| Retraining | 1 Training Job/week, about 1 hour on a small CPU instance, stopped after each run |

Unit prices are reference prices at the time of writing; re-check them with the [AWS Pricing Calculator](https://calculator.aws/#/) before reusing these numbers.

#### Monthly Cost Estimate

| Service | Usage | Estimate (USD/month) |
|---|---|---|
| AWS Lambda (ingest, predict, API, chat) | about 10,000 invocations, about 10,000 GB-seconds | 0 – 0.3 (mostly within the free tier) |
| Amazon EventBridge Scheduler | about 22 invocations | 0 |
| Amazon S3 | about 1 GB of Parquet + requests | about 0.1 |
| Amazon DynamoDB (on-demand) | hundreds of writes, tens of thousands of reads | about 0.1 |
| Amazon ECR | 2 repositories, about 2 GB of images | about 0.2 |
| Amazon API Gateway (REST) | about 10,000 requests | about 0.1 |
| Amazon Cognito | about 15 active users | 0 |
| AWS Amplify Hosting | build and serve the static web app | about 1 |
| Amazon CloudWatch (Logs, Alarms) | about 1 GB of logs, a few alarms | about 1 |
| Amazon SageMaker AI (Training Jobs) | about 4 hours/month | about 1 |
| Amazon Bedrock (chatbot, news labeling) | about 6 million input and 1 million output tokens | about 10 – 12 |
| **Total** | | **about 15 USD/month; up to about 35 USD/month if chatbot usage doubles** |

Over 3 months of development and operation, the expected total is about 45–105 USD, within the team's roughly 200 USD of credit.

#### Cost Optimization Measures

| Measure | Effect |
|---|---|
| Batch prediction with Lambda, no 24/7 inference endpoint | Pay for a few minutes per day instead of 730 hours/month |
| No NAT Gateway; Lambdas calling external APIs run outside a VPC | Avoid NAT Gateway hourly and data processing charges |
| Precomputed forecasts in on-demand DynamoDB, queried with `Limit=1` | Each page view costs one small read |
| NumPy inference instead of PyTorch in Lambda | Smaller image, faster start, fewer GB-seconds |
| Rule-based chatbot routing: simple questions to Amazon Nova, complex ones to Claude Haiku 4.5 | Lower token cost for most questions |
| `max_tokens` limited by question type (512 / 1500) | Prevents unusually long answers |
| Mandatory Cognito sign-in, no self sign-up | Prevents abuse that would inflate Bedrock costs |
| Scheduled Training Jobs that stop after running | No idle training machines |
| Limited CloudWatch Logs retention | Logs do not accumulate forever |
| AWS Budgets alert at 50 USD/month | Early warning on unusual cost growth |

#### Tracking Actual Costs

- Review cost by service in **AWS Cost Explorer** every week
- Tag resources with `project=cpaws` in CDK to filter costs by project
- Check **AWS Budgets** alerts in the cost owner's email

**Screenshots:** ...
