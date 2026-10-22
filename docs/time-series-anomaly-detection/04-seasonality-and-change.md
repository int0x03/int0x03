---
title: "Lesson 4: Handle Seasonality and Persistent Change"
permalink: /time-series-anomaly-detection/lessons/04-seasonality-and-change/
layout: math-page
---

# Lesson 4: Handle Seasonality and Persistent Change

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/lessons/04-seasonality-and-change/' | relative_url }})

## A large value can be normal

Two readings of 200 requests per minute have different meanings:

```text
03:00 baseline:  18  22  20  [200]  ← unusual for this hour
12:00 baseline: 190 205 198  [200]  ← ordinary for this hour
```

A global threshold forgets time context. A seasonal baseline compares Monday 12:00
with earlier Mondays around 12:00, not with every minute ever observed.

## Remove what you expect, then score what remains

One decomposition writes observation $$x_t$$ as trend $$T_t$$, seasonality $$S_t$$,
and residual $$r_t$$:

$$
x_t = T_t + S_t + r_t
$$

```text
observed:  repeating hills + a one-time spike
expected:  repeating hills
residual:  nearly flat      + a clear spike
```

Estimate expected behavior using only past data, calculate
$$r_t = x_t - T_t - S_t$$, then apply a robust score such as Median Absolute
Deviation (MAD) to residuals. Calendar effects such as holidays may need their own
context; they are not automatically captured by “day of week.”

## Forecast intervals express changing uncertainty

A forecast gives an expected value $$\hat{x}_t$$. The residual is:

$$
e_t = x_t - \hat{x}_t
$$

Instead of one fixed error limit, a prediction interval can widen when the future is
less certain:

```text
upper  · · · · · · · · · · ·       × observed outside interval
forecast ────────────────
lower  · · · · · · · · · · ·
```

Exponential smoothing, state-space models, or Autoregressive Integrated Moving
Average (ARIMA) models can provide forecasts. Always check interval coverage on
later data: a theoretical 95% interval need not cover 95% after model mismatch.

## Find small but persistent shifts

A one-point detector may ignore a repeated change from 20 to 23. Two classic
process-monitoring tools emphasize persistence:

- **Exponentially Weighted Moving Average (EWMA)** smooths observations while giving
  recent values more weight. It reveals gradual movement without following every
  noise spike.
- **Cumulative Sum (CUSUM)** accumulates deviations from a target. Small deviations
  in the same direction eventually create a large score.

```text
deviation:  +1  -1  +1  +1  +1  +1
CUSUM idea:  1   0   1   2   3   4  → persistent evidence grows
```

A **change point** marks the boundary between regimes. After confirming a legitimate
change, retraining or resetting the baseline may be correct. Resetting immediately
after every alert, however, can teach the detector to accept an ongoing incident.

## Which tool fits the story?

| Story | Starting method |
| --- | --- |
| Wrong value for this hour or weekday | Seasonal residual plus MAD |
| Observation exceeds a changing expected range | Forecast interval |
| Small sustained drift from a controlled target | EWMA or CUSUM |
| Need to locate a lasting regime boundary | Change-point method |

## Lesson checklist

- [ ] Name the seasonal periods and known calendar effects.
- [ ] Estimate trend, seasonality, forecasts, and intervals from past data only.
- [ ] Validate residual behavior and interval coverage on a later period.
- [ ] Decide whether to alert on deviation, persistence, or a regime boundary.
- [ ] Define when a new regime may replace the old baseline.

[← Previous]({{ '/time-series-anomaly-detection/lessons/03-statistical-baselines/' | relative_url }}) ·
[Course index]({{ '/time-series-anomaly-detection/' | relative_url }}) ·
[Next: Classical machine learning →]({{ '/time-series-anomaly-detection/lessons/05-classical-machine-learning/' | relative_url }})
