---
title: "Introduction"
date: 2026-11-20
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---
#### About CPAWS

Gold, silver and crude oil prices swing sharply around major events, but ordinary users struggle to know where prices are heading and why. CPAWS forecasts the **distribution of h-session log returns** (h = 1–7) as 7 quantiles, converts them into price ranges and a probability of an increase, and lets a chatbot explain them in Vietnamese with citations.

#### The Real Problem

- **Hard to know what comes next:** press forecasts are usually qualitative, with no numbers and no track record
- **Hard to understand why prices move:** news is plentiful, scattered and not tied to concrete numbers
- **Hard to relate to domestic prices:** world prices are quoted in USD/ounce or USD/barrel, hard to picture in VND

#### The Solution

- Forecast price ranges and the probability of an increase for the next 1–7 sessions, in USD/VND
- A Vietnamese chatbot explains forecasts, taking every number from the system and citing every reason
- The system runs automatically every trading day for about 15 USD/month

#### Architecture Overview

1. **Ingestion:** EventBridge Scheduler triggers the ingest Lambda every trading day, writing data to S3 (`raw/`, `clean/`)
2. **Prediction:** when ingestion finishes, Lambda Destinations invoke the predict Lambda (container image, NumPy inference), which writes results to DynamoDB
3. **API:** API Gateway + Cognito authorizer serve forecasts to the web
4. **Chatbot:** a Lambda calls the Amazon Bedrock Converse API and gets numbers through the `get_forecast` tool
5. **Operations:** CloudWatch Alarms, AWS Budgets, infrastructure written in AWS CDK

![overview](/images/5-Workshop/CPAWS-architecture.jpg)

#### CDK Stacks

| Stack | Main resources |
|---|---|
| `CpawsData` | S3 data lake, ECR (ingest, predict), ingest Lambda, predict Lambda |
| `CpawsServing` | DynamoDB `Forecasts` (PK `asset`, SK `base_date`) |
| `CpawsOps` | EventBridge Scheduler, CloudWatch Alarms |
| `CpawsAuth` | Cognito User Pool (no self sign-up) |
| `CpawsApi` | REST API + Cognito authorizer, API Lambda, chat Lambda |

#### Cost Estimate

With one run per trading day, Lambda, Scheduler, S3 and DynamoDB cost only a few USD per month. The largest cost is Amazon Bedrock when the chatbot is heavily used, which is why the API requires sign-in and rate limiting.

#### Product Demo

- Demo video: ...
- Production link: ...
