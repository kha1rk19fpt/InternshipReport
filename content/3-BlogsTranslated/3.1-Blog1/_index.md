---
title: "Blog 1"
date: 2026-10-24
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Don't Be Fooled by "99% Accuracy": Forecasting Return Distributions with Pinball Loss

*Trịnh Tiến Khải · CPAWS team · Self-written from my project work*

When we started CPAWS (forecasting gold, silver, Brent and WTI prices 1–7 days ahead), the first question my team had to answer was not "which model", but **"what do we forecast and how do we measure it"**. This post shares the three most important decisions I learned from: forecast returns instead of prices, forecast quantiles instead of a single number, and always compare against a strong enough statistical baseline.

## 1. The Trap of Forecasting Price Levels

Suppose gold is 2,000 USD/ounce today. The laziest possible "model" says: *tomorrow's price = today's price*. Because gold rarely moves more than 1–2% a day, its mean absolute percentage error (MAPE) is only about 1%. The report would say "99% accurate" — impressive, yet the model knows nothing about the future.

Many price forecasting projects online fall into exactly this trap: the forecast line almost overlaps the actual price, but it is really just yesterday's price shifted one day to the right.

**How we handled it:** we forecast the **h-session log return**:

```text
r(t, h) = ln( P(t+h) / P(t) ),  h = 1..7
```

With this target, the "no change" model corresponds to predicting a return of 0, and every other model has to prove it learned real changes. For display we simply convert back: `forecast price = current price × exp(r)`.

A small but important detail: *h sessions* follow each asset's **trading calendar** (NYSE for the GLD/SLV funds, CME for oil), not calendar days. If a valid price is missing at t or t + h, or the price is ≤ 0 (like WTI on 20/4/2020), the return is NaN instead of being filled in.

## 2. One Number Is Not Enough: Forecasting Quantiles

Commodity markets are highly uncertain. Saying "gold will rise 0.3% next week" means almost nothing without "but it may range from −2% to +2.6%". So CPAWS forecasts **7 quantiles** of the return: 5%, 10%, 25%, 50%, 75%, 90%, 95%.

From these, the web shows:
- **q50**: the central scenario
- **q10 – q90**: the price range the model believes has about an 80% chance of containing the actual price
- **Probability of an increase**: linear interpolation of the distribution at return = 0, clamped to [5%, 95%] so we never claim absolute certainty

A common technical issue: ML models can output "crossing" quantiles (q10 above q50). We fix this with a `normalize_quantiles()` function that sorts the quantiles **exactly once**, right after the model outputs, inside the predict Lambda.

## 3. Scoring Quantiles with Pinball Loss

MAE or RMSE cannot score a quantile forecast. The right tool is **pinball loss** (quantile loss). For quantile τ, forecast q and actual value y:

```text
L_τ(y, q) = τ · (y − q)        if y ≥ q
          = (1 − τ) · (q − y)  if y < q
```

Intuitively, for τ = 0.9, an actual value above q90 is penalized heavily (factor 0.9), while one below q90 is penalized lightly (factor 0.1). The optimal q90 is therefore the level exceeded about 10% of the time. Pinball loss is a *proper scoring rule*: a model scores best only when it reports what it truly believes.

Alongside pinball loss we track calibration metrics:
- **coverage_80**: share of actual values inside q10–q90, ideally close to 80%
- **width_80**: interval width; an infinitely wide interval has 100% coverage but is useless
- **Brier skill** for the probability of an increase

## 4. Always Compare Against a Strong Baseline

The "return = 0" baseline is too weak for quantile forecasting because it says nothing about volatility. Our baseline is the **empirical quantiles of the last 252 returns** (about one trading year), computed causally: at time t we only use returns that ended before t.

The primary metric is:

```text
pinball_skill = 1 − loss_model / loss_baseline_252
```

`pinball_skill > 0` means the model beats the baseline. The team set a stricter rule: an ML model may replace the baseline only when the **lower bound of the 95% confidence interval** of pinball skill is above 0, and model comparisons also use the Diebold–Mariano test.

## 5. Real Results and Lessons

On the 2019 validation set, DLinear (our first ML model, price-only with a 60-session window) **lost to the 252-session baseline in all 28/28 groups** (4 assets × 7 horizons). Disappointing, but very valuable. Had we measured MAPE on prices, DLinear would still have looked "99% accurate" and might have shipped.

So the product currently uses the 252-session statistical baseline, clearly labeled "252-session statistical baseline" on the web and in chatbot answers; DLinear remains the challenger.

**Three things I take away from the project:**
1. Choosing the target and the evaluation metric matters more than choosing the model.
2. A simple, honestly evaluated baseline is more trustworthy than a complex model that cannot prove its value.
3. Showing uncertainty (forecast ranges) is a way of respecting users.

## References

- Gneiting, T. & Raftery, A. (2007). [Strictly Proper Scoring Rules, Prediction, and Estimation](https://doi.org/10.1198/016214506000001437). *JASA*.
- Hyndman, R. & Athanasopoulos, G. [Forecasting: Principles and Practice (3rd ed.)](https://otexts.com/fpp3/)
- Zeng, A. et al. (2022). [Are Transformers Effective for Time Series Forecasting? (DLinear)](https://arxiv.org/abs/2205.13504)
- Harvey, D., Leybourne, S. & Newbold, P. (1997). [Testing the equality of prediction mean squared errors](https://doi.org/10.1016/S0169-2070%2896%2900719-4). *IJF*.
