---
title: "Lesson 3: Build the First Statistical Baseline"
permalink: /time-series-anomaly-detection/lessons/03-statistical-baselines/
layout: math-page
---

# Lesson 3: Build the First Statistical Baseline

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/lessons/03-statistical-baselines/' | relative_url }})

## Example: is 35 unusual?

Recent nightly request counts are `19, 20, 20, 21, 20, 19, 35`. A statistical
baseline answers two questions:

1. Where is the center of normal history?
2. How spread out is that history?

An anomaly score measures distance from the center in units of normal spread. The
score ranks observations; a separate threshold turns scores into alerts.

## Z-score: familiar but easy to contaminate

For observation $$x_t$$, historical mean $$\mu$$, and standard deviation $$\sigma$$:

$$
z_t = \frac{x_t - \mu}{\sigma}
$$

A score of 3 means “three historical standard deviations above the mean.” For a
normal distribution, about 99.7% of observations fall between -3 and 3; this is a
coverage statement, not the probability that one point is anomalous. The common
rule `abs(z) > 3` is a starting heuristic, not a universal threshold.

One extreme historical incident can pull the mean upward and inflate the standard
deviation. The resulting Z-scores shrink, making later incidents harder to find.

## Median Absolute Deviation: a robust ruler

The median stays near the middle even when a few values are extreme. Median
Absolute Deviation (MAD) measures the typical absolute distance from that median:

$$
\operatorname{MAD} = \operatorname{median}(|x_i - \operatorname{median}(x)|)
$$

A commonly scaled modified Z-score is:

$$
m_t = \frac{0.6745(x_t - \operatorname{median}(x))}{\operatorname{MAD}}
$$

The factor `0.6745` makes the scale comparable to a standard deviation for normally
distributed data. Robust does not mean invulnerable: if most training data is
abnormal, its median also describes the wrong process.

## Interquartile Range: fences around the middle half

Let $$Q_1$$ and $$Q_3$$ be the 25th and 75th percentiles. The Interquartile Range
(IQR) is:

$$
\operatorname{IQR} = Q_3 - Q_1
$$

Typical box-plot fences are $$Q_1 - 1.5\operatorname{IQR}$$ and
$$Q_3 + 1.5\operatorname{IQR}$$.

```text
low fence       Q1        median        Q3       high fence
----|------------[==========|===========]------------|----
                  middle 50% of observations
```

The multiplier `1.5` is another convention to validate, not a law.

## Global, rolling, or seasonal history?

| Baseline | Compare the current point with | Useful when |
| --- | --- | --- |
| Global | All approved history | The process is stable. |
| Rolling | A trailing window | The level changes gradually. |
| Seasonal | Earlier matching hours/days | Daily or weekly patterns dominate. |

A rolling window must end before the point being scored. A very short window adapts
quickly but may absorb an incident; a long window is stable but slow to follow
legitimate change.

## Edge cases are part of the method

- If $$\sigma = 0$$ or $$\operatorname{MAD} = 0$$, do not divide by zero. A changed
  value can be flagged directly, or another scale estimate can be used.
- Decide whether to detect high, low, or two-sided deviations.
- Require enough history before scoring; mark warm-up points as unavailable.
- Exclude known maintenance only by a documented policy, not after seeing results.

## Lesson checklist

- [ ] Pick a center and spread suited to contamination and distribution shape.
- [ ] Pick global, trailing, or seasonal reference history.
- [ ] Keep the numeric score separate from the alert threshold.
- [ ] Define zero-spread, warm-up, missing-data, and one-sided behavior.
- [ ] Save this simple baseline for every later model comparison.

[← Previous]({{ '/time-series-anomaly-detection/lessons/02-prepare-time-series-data/' | relative_url }}) ·
[Course index]({{ '/time-series-anomaly-detection/' | relative_url }}) ·
[Next: Seasonality and change →]({{ '/time-series-anomaly-detection/lessons/04-seasonality-and-change/' | relative_url }})
