---
title: Time Series Anomaly Detection
permalink: /time-series-anomaly-detection/
---

# Time Series Anomaly Detection

Notes on finding unusual observations and patterns in time-dependent data.

## Topics to explore

- Point anomalies, contextual anomalies, and collective anomalies.
- Trend and seasonality decomposition.
- Rolling statistics and robust baselines such as median absolute deviation.
- Evaluation with time-based splits and event-level precision and recall.

## Example workflow

1. Inspect timestamps, missing values, and sampling frequency.
2. Build a seasonal or rolling baseline using past observations only.
3. Score deviations and choose a threshold on a validation period.
4. Evaluate on a later held-out period to avoid future-data leakage.

[Back to home]({{ '/' | relative_url }})
