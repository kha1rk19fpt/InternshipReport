---
title: "Price Ingestion"
date: 2026-11-20
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---
#### Data Sources

| Asset | Series | Trading calendar |
|---|---|---|
| Gold | GLD | NYSE |
| Silver | SLV | NYSE |
| Brent crude | BZ=F | CME |
| WTI crude | CL=F | CME |
| USD/VND | VND=X | display conversion only |

Brent and WTI prices are cross-checked against the EIA Open Data API.

#### Local Run

```bash
python -m src.data.ingest_prices --start 2012-01-01 --dest local
```

The ingest Lambda calls the same `run()` function with `--dest s3`.

#### Cleaning Rules

- Use `pandas_market_calendars` to know each asset's valid trading sessions
- **Drop the current day's bar** by New York time (session not yet closed)
- Each run re-downloads the **last 5 sessions** and overwrites by key, fixing late provider revisions
- The clean table is in long format with a `flag` column; rejected rows go to `rejected.parquet`
- **No forward-filling of the target**; prices ≤ 0 (e.g. WTI on 20/4/2020) are kept but their return is NaN

#### S3 Layout

```text
s3://cpaws-dev-lake/
├── raw/
├── clean/prices/dt=<date>/
├── features/
├── models/
└── eval/
```

#### Test

```bash
pytest tests/test_ingest_prices.py -q
```

**Screenshots:** ...
