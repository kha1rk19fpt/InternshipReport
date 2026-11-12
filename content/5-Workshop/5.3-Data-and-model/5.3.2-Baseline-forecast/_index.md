---
title: "Quantile Baseline"
date: 2026-11-20
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---
#### Target: h-session log return

```python
import numpy as np
import pandas as pd

QUANTILES = np.array([0.05, 0.10, 0.25, 0.50, 0.75, 0.90, 0.95])

def log_return(close: pd.Series, h: int) -> pd.Series:
    # return from session t to t+h on the trading calendar; NaN when a price is missing or <= 0
    p = close.where(close > 0)
    return np.log(p.shift(-h) / p)
```

#### Causal 252-session Quantile Baseline

An h-session return starting at session s is only known at session s + h, so at time t we only use returns that have already ended.

```python
def baseline_q252(r_h: pd.Series, t: pd.Timestamp, h: int) -> np.ndarray:
    # drop the last h values (not finished at t), take the latest 252
    known = r_h.loc[:t].iloc[:-h].dropna()
    return np.quantile(known.iloc[-252:], QUANTILES)
```

#### Quantile Normalization and Probability of an Increase

```python
def normalize_quantiles(q: np.ndarray) -> np.ndarray:
    # sort exactly once after the model outputs, preventing crossing quantiles
    return np.sort(q, axis=-1)

def prob_up(q: np.ndarray) -> float:
    # linearly interpolate the CDF at 0, clamp to [0.05, 0.95]
    f0 = np.interp(0.0, q, QUANTILES, left=0.0, right=1.0)
    return float(np.clip(1.0 - f0, 0.05, 0.95))
```

#### Converting to Price Ranges

`price_q = last_price × exp(q)`. The web shows q10, q50, q90 and labels the source "252-session statistical baseline".

#### Evaluation with Pinball Loss

```python
def pinball_loss(y: np.ndarray, q_pred: np.ndarray) -> float:
    # y: (n,), q_pred: (n, 7)
    diff = y[:, None] - q_pred
    return float(np.mean(np.maximum(QUANTILES * diff, (QUANTILES - 1) * diff)))
```

`pinball_skill = 1 - loss_model / loss_baseline`. A machine learning model replaces the baseline only when the lower bound of the 95% confidence interval of pinball skill is above 0.

**Screenshots:** ...
