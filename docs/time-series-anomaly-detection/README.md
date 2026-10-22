---
title: Time Series Anomaly Detection
permalink: /time-series-anomaly-detection/
---

# Time Series Anomaly Detection

Notes on finding unusual observations and patterns in time-dependent data.

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/' | relative_url }})

There is no single, mutually exclusive taxonomy for time-series anomaly detection.
A method can be statistical, unsupervised, and forecasting-based at the same time.
It is useful to classify a problem along several dimensions: what constitutes an
anomaly, how the detector learns, and how it produces an anomaly score.

## What is anomalous?

| Type | Description | Example |
| --- | --- | --- |
| Point anomaly | One observation is unusual by itself. | A temperature reading of 200°C. |
| Contextual anomaly | A value is unusual only in its temporal context. | High traffic at 03:00 that is normal at 12:00. |
| Collective anomaly | A sequence is anomalous even if each point looks plausible. | A long flatline or an unusual oscillation. |
| Change point | The data-generating process changes. | A persistent shift in mean, variance, or trend. |

The distinction matters: a global threshold may detect point anomalies but miss
an unusual shape, seasonal deviation, or persistent regime change.

## Main families of methods

### 1. Statistical and rule-based methods

These methods estimate an expected range or distribution and flag sufficiently
large deviations. They are fast, interpretable, and strong baselines.

| Method | Basic idea | Notes |
| --- | --- | --- |
| Z-score | Flag values several standard deviations from the mean. | Best for roughly stationary, light-tailed data; use rolling or seasonal statistics when the baseline changes. |
| Modified Z-score / MAD | Compare values with the median using the median absolute deviation. | More robust than mean and standard deviation when the history already contains outliers. |
| IQR or quantile rules | Flag values outside robust quantile bounds. | Simple and distribution-free, but does not model temporal order by itself. |
| EWMA / CUSUM / control charts | Accumulate small deviations from a controlled baseline. | Useful for detecting persistent level shifts in monitored processes. |
| Seasonal decomposition | Remove trend and seasonality, then score the residual. | Useful when an observation should be compared with the same seasonal context. |
| Forecast interval | Fit a statistical forecast and flag observations outside its prediction interval. | Examples include exponential smoothing and ARIMA-family models. |

For observations \(x_t\), the ordinary Z-score uses the mean and standard
deviation:

\[
z_t = \frac{x_t - \mu}{\sigma}
\]

A common robust alternative uses the median and MAD:

\[
\operatorname{MAD} = \operatorname{median}(|x_t - \operatorname{median}(x)|)
\]

Thresholds such as `abs(z) > 3` are heuristics, not universal constants. Estimate
them on representative validation data, and define what to do when variance or
MAD is zero.

### 2. Classical machine learning

Classical models usually receive a feature vector rather than a raw timestamped
series. Features may include the current value, lags, rolling statistics,
differences, seasonal residuals, frequency-domain features, or a complete sliding
window.

Common [scikit-learn outlier and novelty detectors](https://scikit-learn.org/stable/modules/outlier_detection.html)
include:

| Method | Typical use |
| --- | --- |
| Isolation Forest | Unsupervised scoring of tabular or window features; scales well and makes few distributional assumptions. |
| Local Outlier Factor (LOF) | Finds samples with unusually low density relative to their neighbors. Use novelty mode when scoring future data. |
| One-Class SVM | Learns a boundary around mostly normal training examples; can be sensitive to scaling and hyperparameters. |
| Elliptic Envelope | Fits a robust Gaussian covariance; appropriate only when that shape is a reasonable assumption. |
| Clustering or nearest-neighbor distance | Treats small clusters or samples far from normal clusters as suspicious. |
| PCA reconstruction error | Flags windows that cannot be represented well by a low-dimensional normal subspace. |
| Supervised classifiers | Predict anomalies directly when enough reliable labels exist. |

scikit-learn is a general machine-learning toolkit, not a time-series anomaly
detector by itself. The feature-building and train/test split must preserve time:
fit scalers, windows, and models using past data only to avoid leakage.

### 3. Time-series and deep learning models

Model-based detectors learn temporal or multivariate structure directly. They are
often grouped by the mechanism used to produce a score:

| Mechanism | Basic idea | Representative models |
| --- | --- | --- |
| Forecasting-based | Predict the next value or window and score the forecast residual or likelihood. | State-space models, RNN/LSTM/TCN, Transformer forecasters. |
| Reconstruction-based | Reconstruct a normal input window and score reconstruction error. | PCA, autoencoders, variational autoencoders, sequence autoencoders. |
| Density or probabilistic | Estimate how likely an observation or sequence is under the learned normal process. | Gaussian processes, hidden Markov/state-space models, normalizing flows. |
| Representation or graph-based | Learn temporal and cross-variable relationships before scoring unusual states. | Contrastive models, temporal Transformers, graph neural networks. |

[PyPOTS](https://github.com/WenjieDu/PyPOTS) belongs here as a **toolbox**, not as
one anomaly-detection algorithm. It provides a common interface for multiple
neural time-series models and tasks on partially observed data, including anomaly
detection and handling missing values. The specific PyPOTS model still needs to
be described as forecasting-, reconstruction-, or representation-based rather
than simply “PyPOTS-based.”

Deep models are most useful when temporal dependencies, many correlated
variables, missing observations, or large datasets make simpler assumptions too
restrictive. They also require more careful tuning, calibration, and monitoring.

### 4. Foundation models and LLM-assisted methods

This is an emerging family and should be split into two different ideas:

- **Time-series foundation models** are pretrained on large collections of numeric
  series. They can provide forecasts, prediction intervals, or embeddings; anomaly
  scores can then come from forecast errors or distances in embedding space.
- **General-purpose LLMs** are usually better used with contextual information such
  as alerts, logs, maintenance records, metric names, and detector output. They can
  help explain, correlate, or triage anomalies, but are not naturally calibrated
  numeric anomaly detectors.

Foundation models may help with transfer and cold-start problems, but they do not
remove the need for domain-specific thresholds, time-based evaluation, latency
checks, and false-alarm control. For many production signals, a seasonal baseline,
MAD, or Isolation Forest remains easier to validate and operate.

## Learning setup

The same algorithm family can be used under different supervision assumptions:

- **Rule-based:** domain experts define valid ranges, rate-of-change limits, or
  logical constraints.
- **Unsupervised:** training data is unlabeled and may contain some anomalies.
- **Semi-supervised / novelty detection:** train primarily on known-normal history,
  then detect departures from it.
- **Supervised:** train from labeled normal and anomalous examples.
- **Self-supervised:** learn representations using forecasting, masking, or
  reconstruction objectives before fitting an anomaly score or classifier.

## Choosing a starting point

| Situation | Good starting point |
| --- | --- |
| One stable univariate metric | Rolling Z-score, MAD, IQR, or a control chart. |
| Strong trend or seasonality | Decomposition plus residual scoring, or a forecast interval. |
| Several engineered features and few labels | Isolation Forest, LOF, or One-Class SVM. |
| Reliable anomaly labels | A supervised classifier with time-based validation. |
| Many correlated sensors or complex sequence anomalies | A reconstruction-, forecasting-, or graph-based temporal model. |
| Missing multivariate observations are central to the problem | A model/toolkit designed for partially observed time series, such as PyPOTS. |
| Rich logs and operational context accompany numeric alerts | A numeric detector followed by LLM-assisted explanation or triage. |

Start with the simplest method that captures the expected trend and seasonality.
A sophisticated model should demonstrate a meaningful event-level improvement
over statistical and classical baselines, not merely a lower training loss.

## Practical workflow

1. Define the anomaly unit: point, window, event, or regime change.
2. Inspect timestamps, duplicates, missing values, sampling frequency, trend, and
   seasonality.
3. Create time-ordered train, validation, and test periods; never randomly mix
   future observations into training features.
4. Build a statistical baseline such as seasonal residuals plus MAD.
5. Produce an anomaly score separately from the decision threshold.
6. Select and calibrate the threshold on validation data using operational costs
   or an alert-budget target.
7. Evaluate on a later held-out period and compare against the baseline.
8. Monitor drift, alert volume, missing-data behavior, and threshold stability in
   production.

Prefer event-level precision, recall, and detection delay when anomalies persist
across many timestamps. Point-wise accuracy can be misleading because normal
points usually dominate, and labeling every point in one incident as a separate
success overstates performance.

[Back to home]({{ '/' | relative_url }})
