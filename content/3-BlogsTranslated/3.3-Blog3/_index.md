---
title: "Blog 3"
date: 2026-11-10
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Serverless Batch Prediction on AWS: EventBridge Scheduler, Lambda Container Images and DynamoDB

*Trịnh Tiến Khải · CPAWS team · Self-written from my project work*

When thinking about "putting an ML model on the cloud", many students (me included, at first) immediately picture an always-on inference endpoint. But for CPAWS — forecasting gold, silver and oil prices 1–7 days ahead — the data changes only **once a day**, after the trading session closes. A 24/7 server returning the same result all day is waste. This post shares how my team built a fully serverless **batch prediction** flow at very low cost, and the design choices behind it.

## 1. Problem and Requirements

- 4 assets (gold, silver, Brent, WTI), each with 7 horizons × 7 quantiles
- Run once after each Monday–Friday trading session
- One failing data source must not break the whole flow
- The web and chatbot read the latest result with low latency
- Total cost must fit within about 200 USD of credit for the whole project

## 2. Architecture

```text
EventBridge Scheduler ──► Lambda ingest ──► S3 (raw/, clean/)
                               │
                   Lambda Destinations (OnSuccess + OnFailure)
                               ▼
                         Lambda predict (container, NumPy) ──► DynamoDB Forecasts
                                                                   ▲
                                         API Gateway + Cognito ────┘ (web, chatbot)
```

### EventBridge Scheduler Instead of Cron on a Server

The schedule `cron(15 12 ? * TUE-SAT *)` (UTC) runs early morning New York time, after the previous day's session has closed. Tuesday–Saturday in UTC corresponds to the Monday–Friday sessions. No server needs to stay awake waiting.

### Chaining Two Lambdas with Lambda Destinations

Instead of having the ingest Lambda call the predict Lambda in code, we configured **Lambda Destinations**: when ingest finishes, whether it succeeds or fails, AWS invokes predict. Why also on failure? Ingest may fail for one source (say Brent) while other assets still have good data. Predict checks each asset: if an asset is **missing the latest session, it is skipped** rather than falling back to an older date, so an old forecast is never shown as if it were new.

Predict uses `retry_attempts=0`, because if the input data is wrong, retrying does not help; failures trigger a CloudWatch Alarm for the owner to investigate.

### Lambda Container Images and NumPy Inference

Training (DLinear) uses PyTorch, which is too heavy for Lambda. The team converts model weights to NumPy (`weights.npz` plus `config.json`, `scaler.json`, `manifest.json`) and wrote a pure NumPy inference class. The image is packaged as a **container image on ECR**:
- Images are tagged with the **commit SHA** so we know exactly which code is running
- The base image is **pinned by digest** for reproducible builds
- Built with Docker BuildKit for speed

The product currently uses the 252-session statistical baseline (DLinear did not beat it on the validation set), so inference is even lighter. The architecture stays the same, so a better model only requires swapping the artifact.

### Designing the DynamoDB Table for the Query

The only query the web and chatbot need is: *"the latest forecast for asset X"*. So the `Forecasts` table has:
- **Partition key** `asset` (gold, silver, brent, wti)
- **Sort key** `base_date` (the reference trading date)

Fetching the latest item is a single `Query` on `asset`, sorted descending by `base_date`, with `Limit=1`. No `Scan`, no secondary index. Results are precomputed, so the API only reads and formats, with very low latency. The API Lambda and the chatbot share **one formatting function**, so numbers on the web and in chatbot answers always match.

## 3. Cost: Why Only a Few USD per Month

A simple estimate for the prediction path (reference prices; check the AWS pricing pages):

| Component | Monthly usage | Note |
|---|---|---|
| EventBridge Scheduler | about 22 invocations | within the free tier |
| Lambda ingest + predict | about 44 runs, each under 1 minute | hundreds to a few thousand GB-seconds |
| DynamoDB on-demand | hundreds of writes, thousands of reads | a few cents |
| S3 | a few hundred MB of Parquet | a few cents |

For comparison, an always-on inference endpoint, even the smallest size, is billed for all 730 hours of the month — many times the cost of the whole flow above. The most expensive part of the system is actually **Amazon Bedrock** when the chatbot is heavily used, so we control it with Cognito sign-in, routing simple questions to a smaller model, and limiting `max_tokens` by question type. AWS Budgets alerts at 50 USD/month.

## 4. What I Learned

1. **Ask "how often does the data change?" before choosing an architecture.** If the answer is once a day, batch prediction is almost always cheaper and simpler than real-time inference.
2. **Design for failure from the start.** Decoupling ingest and predict, running predict even when ingest fails, never falling back a day: three small decisions that keep the system from silently showing wrong data.
3. **Design tables around queries.** With DynamoDB, think about the query first, then choose the keys.
4. **Make everything traceable.** Images tagged by commit, model artifacts with a manifest, date-partitioned data on S3: when a number on the web looks odd, we know exactly where it came from.

## References

- [Amazon EventBridge Scheduler – User Guide](https://docs.aws.amazon.com/scheduler/latest/UserGuide/what-is-scheduler.html)
- [AWS Lambda – Capturing records of asynchronous invocations (Destinations)](https://docs.aws.amazon.com/lambda/latest/dg/invocation-async-retain-records.html)
- [AWS Lambda – Create a Lambda function using a container image](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html)
- [Amazon DynamoDB – Querying tables](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)
- [AWS Lambda Pricing](https://aws.amazon.com/lambda/pricing/)
- [AWS Well-Architected Framework – Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html)
