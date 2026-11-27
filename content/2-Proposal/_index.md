---
title: "Proposal"
date: "2026-10-21"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# CPAWS - Gold, Silver and Crude Oil Price Forecasting with an Explanatory Chatbot on AWS
*(See next week's price forecast in USD and VND, and ask AI why prices may rise or fall)*

---

## 1. Executive Summary

We are **CPAWS**, a team of 5 interns in the AWS First Cloud AI Journey (AWS-FCAJ) program. We are building a web platform that forecasts **gold, silver, Brent and WTI crude oil** prices **1-7 days ahead**, shown in two currencies, **USD and VND**, with a **chatbot** that explains why prices may rise or fall based on model outputs and cited news.

| Member | Role |
|---|---|
| Trịnh Tiến Khải | Research Lead - main forecasting model, feature builder, predict Lambda code |
| Nguyễn Tiến Long | Modeling & Evaluation - baselines, DLinear, accuracy evaluation |
| Phan Tấn Phước | News & Chatbot - news labeling, chatbot |
| Châu Trần Mạnh Tiến | Platform - CDK, Lambda, Step Functions, web |
| Huỳnh Ngọc Tuấn | Data & Security Lead - IAM, API Gateway, Cognito, DynamoDB, cost |

The system runs entirely on AWS with a **scheduled serverless architecture**: daily data collection, batch prediction and pre-computed results, keeping costs low and within the team's **roughly 200 USD of AWS credit**. The goal is a **working product**, not a research paper.

---

## 2. The Problem We Address

### What's the Problem?

Gold, silver and oil prices swing sharply around major events such as armed conflicts, central bank rate decisions or OPEC+ output decisions. In Vietnam these commodities trade in line with world markets: domestic gold and silver prices follow international prices, and crude oil is the main basis for retail fuel prices. Ordinary users can easily see current prices but face three problems:

- **Hard to know what comes next:** press forecasts are usually qualitative, with no numbers and no track record.
- **Hard to understand why:** news is plentiful, scattered and not tied to concrete numbers.
- **Hard to relate to domestic prices:** world prices are quoted in USD/ounce or USD/barrel, which is hard to translate into local impact.

### The Solution

CPAWS forecasts the **distribution of log returns 1-7 sessions ahead** as 7 quantiles (5%, 10%, 25%, 50%, 75%, 90%, 95%), then converts them into price ranges and a probability of an increase for display. We do not forecast price levels directly: tomorrow's price is almost today's price, so a model that just repeats today's price looks "99% accurate". Forecasting returns forces the model to learn real changes and allows honest accuracy measurement.

The Amazon Bedrock chatbot takes numbers only from the system through **tool use**, cites a source for every reason, and gives no investment advice.

| User group | Main need |
|---|---|
| Gold savers | Next week's trend and expected price range |
| Transport and import businesses, energy price watchers | World oil price trend, the main basis for domestic fuel prices |
| Students and people interested in finance | Understanding which factors are moving prices |

### Benefits and ROI

- **Verifiable numbers:** forecast ranges and historical accuracy are public, instead of qualitative opinions.
- **Explanations with sources:** the chatbot ties every reason to news or an official release.
- **Built for Vietnamese users:** conversion to VND at the current exchange rate.
- **Low cost:** no 24/7 resources, estimated at 15-35 USD/month.

---

## 3. Solution Architecture

### Overview

The system runs on a schedule: daily collection, batch prediction and stored results. Each component runs independently, so one failing data source does not stop the whole system.

![CPAWS architecture](/images/2-Proposal/CPAWS-architecture.jpg)

### AWS Services Used

| Layer | AWS services | Role |
|---|---|---|
| Collection | Amazon EventBridge Scheduler, AWS Lambda | Call data sources on a daily schedule |
| Storage | Amazon S3 (data lake), Amazon DynamoDB | Raw, clean, feature and model data; forecasts and accuracy |
| Training | AWS Step Functions, Amazon SageMaker AI | Retraining, backtesting, champion/challenger comparison |
| Prediction | AWS Lambda (container image on Amazon ECR) | Run the frozen model every day |
| Conversational AI | Amazon Bedrock (Converse API, tool use, Guardrails) | Explain forecasts, label news |
| Interface | AWS Amplify Hosting, Amazon API Gateway, Amazon Cognito | Per-asset web (USD/VND), sign-in, rate limiting |
| Operations | Amazon CloudWatch, AWS Budgets, AWS CDK, GitHub Actions | Monitoring, cost alerts, infrastructure as code, CI/CD |

### Workflow

- **Daily:** EventBridge Scheduler triggers the ingest Lambda to load prices and exchange rates into S3; when it finishes, the predict Lambda reads clean data, runs the champion model and writes 7 quantiles for h = 1-7 to DynamoDB.
- **Weekly:** train a challenger model, backtest on recent weeks and replace the champion only when the challenger is better.
- **When a user asks:** the chatbot calls tools to get forecasts, accuracy and related news, then answers with citations.

---

## 4. Technical Implementation

### Forecasting Models

| Model | Role |
|---|---|
| Naive (return = 0), 252-session empirical quantiles | Baselines |
| DLinear | First machine learning model of the MVP, light and CPU-friendly |
| CEEMDAN + DLinear/LSTM | Main model: rolling-window CEEMDAN splits the price series into trend and short-term components used as inputs |

External signals include the Geopolitical Risk index (GPR), OVX/GVZ volatility indices, an event calendar (FOMC, EIA inventory reports) and official Fed and EIA releases. All signals are aligned to **publication time** to avoid look-ahead leakage.

### Data Split and Evaluation

| Period | Dates |
|---|---|
| CEEMDAN warm-up | 2012-2013 |
| Training | 2014-2018 |
| Validation (parameter selection) | 2019 |
| Test (run once after parameters are locked) | 2020 -> present |

- **Primary metric:** pinball skill against the 252-session quantile baseline.
- **Interval calibration:** share of actual prices inside the 80% interval, interval width, interval score.
- **Probability of an increase:** Brier skill.
- **Model comparison:** Diebold-Mariano test.
- **Not used:** MAPE or price-level errors, which create illusory accuracy.

### Technical Requirements

- **Languages and libraries:** Python (pandas, NumPy, PyTorch for training, NumPy for inference), React + TypeScript + Vite for the web.
- **Infrastructure:** AWS CDK (Python), region ap-southeast-1.
- **Process:** Code and reports are developed and stored on GitHub; changes to IAM/infrastructure, the chatbot or the forecasting model need review by the member responsible for that part.

---

## 5. Architecture Build Timeline

| Phase | Activities |
|---|---|
| Pre-build (09-10/2026) | AWS fundamentals, topic selection, proposal, price data since 2012 |
| Weeks 1-2 (12-25/10) | Architecture, clean data, target and evaluation, ingestion pipeline |
| Weeks 3-4 (26/10-08/11) | Baselines, DLinear MVP, predict Lambda, first API and chatbot |
| Weeks 5-6 (09-22/11) | Automated daily flow, news signals, evaluation; feature freeze on 22/11 |
| Week 7 (23-29/11) | System stabilization, final report, end-to-end system review and evaluation |
| Late 11 - early 12/2026 | Submit the system architecture and send the demo video to FCAJ |

---

## 6. Budget Estimation

### AWS Costs

| Service | Estimate/month |
|---|---|
| Lambda, EventBridge Scheduler, Step Functions | < 2 USD |
| S3, DynamoDB (on-demand), ECR | 1-3 USD |
| SageMaker AI Training Jobs (scheduled, stopped after each run) | 3-10 USD |
| Amazon Bedrock (chatbot, news labeling) | 5-15 USD |
| API Gateway, Cognito, Amplify Hosting, CloudWatch | 2-5 USD |
| **Total** | **about 15-35 USD/month** |

### Cost Optimization

- No 24/7 endpoints, NAT Gateway or OpenSearch Serverless.
- Batch prediction with Lambda instead of an always-on inference endpoint.
- Rule-based chatbot routing: simple questions go to a smaller model, complex ones to a larger model; `max_tokens` limited by question type.
- AWS Budgets alert at 50 USD/month.

### Budget Control

- All costs stay within the team's AWS credit (about 200 USD).
- Every new resource is cost-estimated before being proposed.

---

## 7. Risk Assessment

| Risk | Level | Mitigation |
|---|---|---|
| Look-ahead leakage (full-series CEEMDAN, wrong scaling, misaligned news timestamps) | High | Rolling-window CEEMDAN, walk-forward backtest, publication-time alignment, automated tests |
| Model fails to beat the statistical baseline | High | Honest reporting; the product uses the baseline until an ML model proves better |
| Data sources change or become restricted | Medium | One primary source per series with a cross-check source (EIA); terms recorded in ADRs |
| Chatbot fabricates numbers or is seen as investment advice | Medium | Tool use, citations, Guardrails, mandatory disclaimer |
| Prompt injection through news content | Medium | Mark news as untrusted data, Guardrails |
| Bedrock cost spikes from abuse | Medium | Cognito sign-in, rate limiting, Budgets alerts |
| Scope creep | Medium | Must/Should/Could priorities, feature freeze on 22/11 |

---

## 8. Expected Outcomes

### Success Criteria

- A working web product on AWS that automatically collects data and forecasts all 4 assets every trading day.
- The web shows forecast ranges, probability of an increase, USD/VND conversion and the last 8 weeks of accuracy.
- The chatbot answers in Vietnamese, with numbers matching the database, cited reasons, and refusals for out-of-scope questions.
- Costs stay within the AWS credit.

### Long-term Value

- An honest evaluation framework (statistical baseline, champion/challenger) reusable for other assets.
- A data pipeline and infrastructure as code that make new features easy to add.

### Note on Changes After the Proposal

During the build, several decisions were adjusted and recorded in ADRs: price data moved to one consistent source per series (GLD, SLV, BZ=F, CL=F, VND=X) cross-checked with EIA; news uses only official Fed and EIA releases; API keys are stored in SSM Parameter Store; the MVP does not use Knowledge Bases yet; and the product currently uses the 252-session statistical baseline because DLinear did not beat it on the validation set.
