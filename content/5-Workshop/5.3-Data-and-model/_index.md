---
title: "Data and Model"
date: 2026-11-20
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---
#### Overview

This part builds the two logic blocks that run inside Lambda:

1. [Price ingestion](5.3.1-Ingest-prices/): download prices by trading calendar, clean, flag and write to S3
2. [Quantile baseline](5.3.2-Baseline-forecast/): compute the log-return target, the 252-session baseline, quantile normalization and probability of an increase

#### Folder Structure

```text
src/
├── data/        # ingest_prices.py: CLI + run() for the ingest Lambda
├── features/    # build.py: feature builder shared by training and prediction
├── models/      # baselines.py, dlinear_numpy.py
├── eval/        # pinball loss, coverage, DM test
└── serving/     # forecast_record.py: DynamoDB record format
infra/           # AWS CDK (Python)
web/             # React + TypeScript + Vite
tests/
```

#### Leakage Prevention Principles

- Use only information **known at forecast time t**
- Training and prediction use **the same** feature builder
- News signals are aligned to **publication time**, not the event date
- The 2020 → present test set is **run only once** after parameters are locked
