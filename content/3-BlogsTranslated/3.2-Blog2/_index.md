---
title: "Blog 2"
date: 2026-11-07
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Five Look-Ahead Leakage Traps in Financial Time-Series Forecasting

*Trịnh Tiến Khải · CPAWS team · Self-written from my project work*

In time-series forecasting, **look-ahead leakage** happens when a model accidentally sees information that would not be available at real forecast time. The result is a suspiciously good backtest followed by a collapse in production. While building the model for CPAWS, I ran into, or nearly ran into, all five traps below. This post collects them so others working on similar projects can avoid them.

The whole rule fits in one sentence: **at forecast time t, use only information truly known at t.** The difficulty is that future information usually sneaks in very quietly.

## Trap 1: Decomposing the Whole Series

Our main model uses **CEEMDAN** to split the price series into oscillating components (IMFs) and a trend. A common approach in papers is to run CEEMDAN once on the entire series and only then split train/test.

The problem: CEEMDAN is a global decomposition. The IMF value at day t depends on days after t. Using it as a feature means indirectly seeing the future, and the backtest looks artificially good.

**Fix:** run CEEMDAN on a **rolling window**: at each t, decompose only the price segment ending at t and take only the last value of each IMF as a feature. This is far more expensive, so the team caches IMF results on S3. The years 2012–2013 are reserved to "warm up" the window.

## Trap 2: Scaling with Whole-Dataset Statistics

`StandardScaler().fit(X)` on all data before splitting train/validation is a classic mistake: the mean and standard deviation already contain future information.

**Fix:** fit the scaler only on the training set (2014–2018), store its parameters in the model artifact (`scaler.json`), and reuse exactly those parameters for validation, test and real prediction in Lambda.

## Trap 3: Dating News and Indices by Event Date Instead of Publication Date

This is the sneakiest trap. For example:
- FOMC minutes are published about three weeks after the meeting. Attaching them to the meeting date lets the model read them three weeks early.
- The Geopolitical Risk (GPR) index is updated periodically; a day's value only exists after its publication.
- A release published after the market close only affects the next trading session.

**Fix:** every signal source has a **publication-time** rule (in code, `PUBLISHED_AT_RULES`). A signal is used only from the first trading session after publication. More importantly, training and prediction share **the same feature builder** with the same rules, so the backtest and the product never run different logic.

## Trap 4: Trading Calendars and Forward-Filling

Assets on different exchanges have different holidays. A real case we hit: on 5/12/2018 NYSE was closed (national day of mourning) while CME energy was still trading — yet our data source was missing that session for Brent and WTI.

The natural reflex is to forward-fill. But forward-filling the **target** teaches the model fake "return = 0" days, followed by a return that spans two sessions. Both distort the distribution.

**Fix:**
- Use `pandas_market_calendars` to define each asset's valid sessions; t + h follows that asset's own calendar
- **Never forward-fill the target**; missing rows are flagged and excluded from evaluation, accepting some lost samples (the 5/12/2018 gap flags about 301 oil rows in the 2019 validation set as having an invalid input window)
- Drop the current day's bar (New York time) because the session has not closed

## Trap 5: Using the Test Set More Than Once

Leakage lives not only in code but also in **process**. If we tweak parameters after each look at the test results, the test set becomes a second validation set and the reported number is no longer an honest estimate.

**Our process:**
- A fixed split: 2014–2018 train, 2019 validation, 2020 → present test
- Every choice (window, architecture, signals kept) is based only on validation
- The test set is **run only once** after parameters are locked, with the configuration and commit recorded beforehand
- The same principle applies to the chatbot test set: never tweak the prompt and re-score on the same questions

## Checking with Automated Tests

"I was careful" is not enough. A simple but effective test I wrote for the feature builder:

```python
def test_features_do_not_use_future(prices):
    t = prices.index[-30]
    full = build_features(prices).loc[t]
    # replace all data after t with garbage
    tampered = prices.copy()
    tampered.loc[tampered.index > t] = 1e9
    cut = build_features(tampered).loc[t]
    # features at t must not change
    assert np.allclose(full, cut, equal_nan=True)
```

If features at t change when future data changes, there is definitely leakage.

## Conclusion

Look-ahead leakage rarely comes from an obviously wrong line of code; it comes from hidden assumptions about time. The three habits that helped me most: always ask "when is this information available?", share one function between training and prediction, and write tests that deliberately corrupt future data.

## References

- Torres, M. E. et al. (2011). [A complete ensemble empirical mode decomposition with adaptive noise](https://doi.org/10.1109/ICASSP.2011.5947265). *ICASSP*.
- [PyEMD documentation (CEEMDAN)](https://pyemd.readthedocs.io/)
- [pandas_market_calendars](https://github.com/rsheftel/pandas_market_calendars)
- [Federal Reserve – FOMC meeting calendars, statements and minutes](https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm)
- [Geopolitical Risk (GPR) Index](https://www.matteoiacoviello.com/gpr.htm)
- [scikit-learn – Common pitfalls: data leakage](https://scikit-learn.org/stable/common_pitfalls.html)
