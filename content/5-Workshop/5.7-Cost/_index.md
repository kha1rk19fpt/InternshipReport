---
title: "Cost and Well-Architected"
date: 2026-11-20
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---
#### Actual cost

All costs are paid with the team's AWS credits (about 200 USD). The figures below come from Cost Explorer, 01/10–24/11/2026, Charge type = Usage, grouped by Service.

| Item | Amount | Notes |
|---|---:|---|
| **Total** | **about 6.35 USD** | October: 3.00 USD; November (to 24/11): 3.35 USD. Includes experiments and benchmarks |
| Claude Haiku 4.5 (Bedrock) | 5.56 USD | Chatbot, benchmarks and test questions |
| Bedrock (other models) | 0.50 USD | October only, while Nova 2 Lite was being tested (news labelling, benchmark) |
| Amazon ECR | 0.11 USD | Container image storage |
| Cost Explorer | 0.07 USD | Cost Explorer queries |
| Amazon S3 | 0.01 USD | Data lake |
| Lambda, DynamoDB, API Gateway, Amplify, CloudWatch, Cognito, KMS, SNS, CloudFormation | under 0.01 USD each | Rounded to 0.00 by Cost Explorer |

Cost Explorer also lists Relational Database Service (0.10 USD) and Secrets Manager. Both come from a personal lab on the same account and are not part of the CPAWS architecture (CPAWS uses no RDS and keeps its API key in SSM Parameter Store).

![Cost Explorer](/images/5-Workshop/5.7-cost-explorer.png)

*Cost Explorer, 01/10–24/11/2026, grouped by Service: total 6.35 USD, average 3.18 USD per month, mostly Claude Haiku 4.5.*

![Cost Explorer by service](/images/5-Workshop/5.7-cost-breakdown.png)

*Cost by service: Claude Haiku 4.5 5.56 USD, Bedrock 0.50 USD, ECR 0.11 USD, RDS 0.10 USD (lab, outside CPAWS), Cost Explorer 0.07 USD; the remaining services round to 0.00 USD.*

#### Estimate with AWS Pricing Calculator

Long prepared the estimate on 24/11/2026 for **one** running environment (`cpaws-dev-*`, Singapore Region), treated as a low-traffic production baseline. The `production` branch in the repository does not mean a second AWS environment, so the cost is not doubled.

**Result: about 5.26 USD/month, 63.12 USD/year** (using the subtotals the Calculator displays; the unrounded figure is 5.2539 USD/month). It has three parts:

- 34 lines in the Billing Console Pricing Calculator: 1.49 USD/month. Every line is listed in [Appendix 5.7.1](5.7.1-estimate-details/).
- Amazon Bedrock, public estimate: [https://calculator.aws/#/estimate?id=f81e944af4c143c007493dbfad5bfa39c934efbe](https://calculator.aws/#/estimate?id=f81e944af4c143c007493dbfad5bfa39c934efbe) (3.77 USD/month).
- EventBridge Scheduler, public estimate: [https://calculator.aws/#/estimate?id=1530e02ff22ccf47d5f43f5a203d3d3bc392f421](https://calculator.aws/#/estimate?id=1530e02ff22ccf47d5f43f5a203d3d3bc392f421) (0.00 USD/month, within the 14-million-invocation Free Tier).

| Service | Role | USD/month |
|---|---|---:|
| Amazon Bedrock (Claude Haiku 4.5) | Chatbot | 3.766 |
| Amazon CloudWatch | Logs, metrics, 6 alarms | 1.201 |
| Amazon ECR + Data Transfer | Container images, egress | 0.118 |
| Amazon Cognito | Sign-in (5 MAU) | 0.075 |
| AWS Lambda | 6 functions | 0.048 |
| AWS Amplify | Static web app | 0.013 |
| Amazon S3 | Data lake | 0.013 |
| Amazon API Gateway | REST API | 0.011 |
| Amazon DynamoDB | Forecast, accuracy and chat-quota tables | 0.009 |
| AWS KMS | Encryption API | 0.0004 |
| Amazon SNS, EventBridge Scheduler, SSM Parameter Store | Alerts, schedules, parameters | 0 |
| **Total** | | **5.254** |

**Main monthly workload assumptions** (see the appendix for all of them):

| Parameter | Value | Basis |
|---|---:|---|
| Cognito users | 5 MAU | 3 MAU in October, 4 MAU during 01–23/11 |
| API requests | 2,700 | Assumed 60 forecast views, 20 accuracy views and 10 chat questions per day |
| Job runs | 66 | 22 days × (ingest, predict, accuracy-job) |
| Lambda | 2,766 invocations, 2,817 GB-seconds | Run times measured 17–23/11/2026 |
| Bedrock | 300 chat questions, about 3.08 million input and 0.14 million output tokens | Sample of 7 real chats on 22–23/11: on average 1.86 model calls and 10,272 input tokens per question; 1 USD per million input tokens, 5 USD per million output tokens |

**Largest items:** Bedrock is 71.7% and CloudWatch 22.9%. CloudWatch includes 0.60 USD for 6 alarms and a 0.60 USD reserve for 2 custom metrics; the estimate uses pre-discount rates for most services, so small items such as alarms are higher than the actual bill.

**Sensitivity to chat volume** (only Bedrock tokens are scaled, other items stay fixed):

| Scenario | Chats/month | Bedrock (USD/month) | Total (USD/month) |
|---|---:|---:|---:|
| Baseline | 300 | 3.77 | 5.25 |
| Five times | 1,500 | 18.83 | 20.32 |
| Full quota of 100 questions/day | 3,000 | 37.66 | 39.14 |

The question limit is not a spending limit: longer questions or more tool calls still raise the cost per question.

**Cognito while self sign-up is open:** during grading, if all 5 members and up to 40 invited users are active in the same month (about 45 MAU), Cognito costs about 0.68 USD/month instead of 0.075 USD.

#### Cost optimization measures

| Measure | Effect |
|---|---|
| Batch forecasting in Lambda instead of a 24/7 inference endpoint | Pay for a few seconds of predict per day instead of 730 hours per month |
| Drop Step Functions and SageMaker AI (ADR-0011) | No orchestration or periodic training cost |
| No Lambda in a VPC, no NAT Gateway | Avoid NAT Gateway hourly and data processing charges |
| Forecasts precomputed, stored in on-demand DynamoDB, read with `Query` and `Limit=1` | Each page view costs one small read |
| NumPy inference in the predict Lambda instead of PyTorch | Small image, fast start, fewer GB-seconds |
| Chatbot question limits and `maxTokens` 1500 | Caps unexpected Bedrock cost |
| ECR keeps 5 images, logs kept 1 month, old S3 versions kept 30 days | Nothing accumulates forever |
| AWS Budgets at 50 USD/month and Cost Anomaly Detection | Early warning of unusual cost |
| `Project`, `Owner`, `Env` tags on every resource | Filter cost by project in Cost Explorer |

The proposals below are **not done yet**; each needs a measurement before and after the change, and no saving is claimed until it is measured:

- Reduce chatbot input tokens (less repeated context, tools return only the fields needed). Cutting input tokens by 20% saves about 0.62 USD/month, or 12% of the estimate.
- Try routing simple questions to Nova 2 Lite again, only if the router passes on a new question set.
- Re-measure memory for each Lambda (256/512/1,024 MB) and consider ARM if the images and libraries support it.
- One older log group still keeps logs for 2 years; align its retention with the 1-month setting.
- Add 50/80/100% alert thresholds to the budget, suited to the current low spend.

#### Review against AWS Well-Architected

| Pillar | Done | Weak points |
|---|---|---|
| **Operational Excellence** | Infrastructure as code (CDK, 5 stacks), `cdk diff` before deploying, CI on every PR, rollback with one deploy command using an old image tag, an ADR for every architecture decision | Manual deployment from one machine, no CD; Amplify by manual zip upload |
| **Security** | IAM users + groups, MFA, short-lived sign-in sessions; least-privilege IAM role per Lambda; S3 public access blocked, SSL enforced; API key in SSM `SecureString`; Cognito authorizer, throttling, single-origin CORS; deletion of evaluation results blocked by explicit deny | Bedrock Guardrails not enabled; the read-only group uses `ReadOnlyAccess`, broader than needed |
| **Reliability** | A failed source still lets the other assets publish; conditional writes make re-runs safe; versioned bucket; forecasts table and User Pool are `RETAIN`; 6 alarms send email | One region, one environment; no alert when the schedule does not run |
| **Performance Efficiency** | Lambda container images chosen for large libraries; predict runs in about 3.3 seconds with 1,024 MB; forecasts are precomputed so the API reads one record | Chat Lambda cold starts add to answer time |
| **Cost Optimization** | No resources billed by the hour; actual cost about 6.35 USD from 01/10 to 24/11/2026, estimate 5.26 USD/month; Budgets and Cost Anomaly Detection | Bedrock cost not yet broken down by feature |
| **Sustainability** | Runs only when there is a new trading session (Tuesday–Saturday); no idle machines | — |

#### Clean-up

The steps to disable the schedules, delete the stacks and `RETAIN` resources, and then check billing are in section [5.8](../5.8-cleanup/).
