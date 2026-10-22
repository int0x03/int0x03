---
title: "Lesson 6: Understand Deep Time-Series Models"
permalink: /time-series-anomaly-detection/lessons/06-deep-time-series-models/
---

# Lesson 6: Understand Deep Time-Series Models

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/lessons/06-deep-time-series-models/' | relative_url }})

## Begin with the relationship you need to learn

Imagine three service signals: traffic, Central Processing Unit (CPU) use, and
latency. A latency of 200 milliseconds may be normal under heavy traffic but
suspicious under almost no traffic. Each value alone looks plausible; their
relationship is wrong.

```text
time ───────────────→
traffic:    low low LOW
CPU:        low low LOW       values: plausible
latency:    low low HIGH      relationship: suspicious
```

Deep models become useful when long temporal dependencies, nonlinear cross-sensor
relationships, large datasets, or missing observations defeat simpler features.
“Deep” is not itself an anomaly-scoring mechanism.

## Four ways to produce a score

| Mechanism | Training task | Anomaly score | Representative models |
| --- | --- | --- | --- |
| Forecasting | Predict the next point or window. | Forecast error or low predictive likelihood. | Recurrent Neural Network (RNN), Long Short-Term Memory network (LSTM), Temporal Convolutional Network (TCN), Transformer |
| Reconstruction | Compress and rebuild a normal window. | Difference between input and reconstruction. | Autoencoder, variational or sequence autoencoder |
| Probabilistic | Learn a distribution over normal sequences or states. | Low likelihood or surprising state transition. | State-space model, hidden Markov model, normalizing flow |
| Representation / graph | Learn embeddings and variable relations. | Distance from normal representations or broken graph relations. | Contrastive model, temporal Transformer, graph neural network |

```text
forecast:       past window → model → expected next value → error
reconstruction: window → encoder → compact code → decoder → rebuild → error
graph:          sensor nodes + relations → expected joint state → relation error
```

The learning setup is a separate dimension. Predicting a hidden or future segment
can be a **self-supervised** task because the series supplies its own target. A model
can also train on known-normal history for semi-supervised novelty detection, or use
incident labels for supervised classification.

## Multivariate data: relation failures matter

Before choosing a graph or attention model, ask whether variables truly interact
and whether those relationships are stable. Sensor names, units, and topology can
be as important as values. A model can otherwise learn accidental correlations
that disappear after a deployment.

Preserve feature identity and availability masks. Evaluate both whole-event
detection and the model's ability to identify affected variables if operators need
root-cause clues.

## Missing values are observations about availability

```text
value:  [12, 13,  ?,  ?, 15]
mask:   [ 1,  1,  0,  0,  1]
```

Do not silently turn missing values into zeros. Some models consume values, masks,
and time gaps together. [PyPOTS](https://github.com/WenjieDu/PyPOTS), the Python
Toolbox for Data Mining on Partially-Observed Time Series, provides interfaces for
multiple neural models and tasks on partially observed data. It is a **toolbox**,
not one anomaly-detection algorithm; identify the specific model and scoring
mechanism used.

## The cost of added capacity

Deep models need more data, tuning, compute, threshold calibration, and drift
monitoring. Their scores can vary across retraining runs and may be difficult to
explain. Compare them with the same time splits, event definitions, and alert budget
used for the simple baseline.

Use one only when it wins on an operational outcome: earlier detection, more true
events, fewer false alerts, or useful multivariate localization.

## Lesson checklist

- [ ] State the temporal or cross-variable relation a simpler model misses.
- [ ] Name the scoring mechanism, not only the architecture.
- [ ] Preserve missingness masks and variable identity.
- [ ] Prevent overlap and preprocessing leakage across time splits.
- [ ] Justify operational gains against added compute and maintenance.

[← Previous]({{ '/time-series-anomaly-detection/lessons/05-classical-machine-learning/' | relative_url }}) ·
[Course index]({{ '/time-series-anomaly-detection/' | relative_url }}) ·
[Next: Thresholds and evaluation →]({{ '/time-series-anomaly-detection/lessons/07-thresholds-and-evaluation/' | relative_url }})
