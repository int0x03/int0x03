---
title: "Lesson 5: Turn Windows into Machine-Learning Features"
permalink: /time-series-anomaly-detection/lessons/05-classical-machine-learning/
---

# Lesson 5: Turn Windows into Machine-Learning Features

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/lessons/05-classical-machine-learning/' | relative_url }})

## A point can look normal while its shape is not

The readings `20, 24, 20, 16, 20` are all within a valid range, but repeated rapid
oscillation may indicate an unstable controller. Represent several adjacent points
as one sample:

```text
series:  20 21 22 21 20 19
window 1 [20 21 22]
window 2    [21 22 21]
window 3       [22 21 20]
```

With a window length of three and a stride of one, neighboring samples overlap.
Keep all windows from the same incident on one side of a time split.

## Turn each window into a useful description

Two common representations are:

1. **Raw window:** use every ordered value, such as `[20, 21, 22]`.
2. **Engineered features:** summarize level, spread, and shape.

| Feature | Question it answers |
| --- | --- |
| Lags | What happened one or several steps ago? |
| Mean, standard deviation, minimum, maximum | What is the window's level and spread? |
| Difference or slope | Is it moving, and how quickly? |
| Longest repeated run | Is a sensor stuck? |
| Seasonal residual statistics | Is it unusual after expected context is removed? |
| Frequency-domain energy | Is an unusual oscillation present? |

Choose features that correspond to an incident story. Hundreds of unexplained
features make debugging and drift monitoring harder.

## Match a detector to the geometry

| Method | Intuition | Important caution |
| --- | --- | --- |
| Isolation Forest | Unusual samples are isolated with fewer random splits. | Scores relative rarity, not business harm. |
| Local Outlier Factor (LOF) | A point is suspicious when its neighborhood is less dense than nearby neighborhoods. | Use novelty mode to score future samples. |
| One-Class Support Vector Machine (SVM) | Draw a flexible boundary around mostly normal examples. | Scaling and hyperparameters strongly affect the boundary. |
| Elliptic Envelope | Fit a robust Gaussian-shaped cloud. | Poor fit when normal data is multimodal or non-elliptical. |
| Clustering / nearest neighbors | Measure distance from normal groups or neighbors. | Distance loses meaning with many noisy dimensions. |
| Principal Component Analysis (PCA) | Learn a low-dimensional normal subspace; score reconstruction error. | Linear reconstruction misses nonlinear relations. |

[scikit-learn's outlier-detection guide](https://scikit-learn.org/stable/modules/outlier_detection.html)
distinguishes **outlier detection**, where training data may contain outliers, from
**novelty detection**, where the model learns from mostly clean normal history and
scores new observations. This choice changes both assumptions and application
interfaces.

## Build one chronological pipeline

```text
past raw data → windows → features → imputer → scaler → detector → score
                  all fitted using the training period only
future data   → same frozen transforms ----------------------→ score
```

Scaling is essential for distance- and boundary-based models. Fit the scaler only
on training features. Window construction must also be causal: a window scored at
10:00 cannot end at 10:05.

Associate a window score with a documented time, usually its final timestamp. Then
merge adjacent high-scoring windows so one incident does not flood operators.

## When not to use machine learning

If a seasonal residual plus a robust threshold already meets the alert budget and
detection-delay requirement, a learned detector may add maintenance cost without
adding value. Use a more complex model only for a specific missing capability, such
as nonlinear feature interactions or unusual shapes.

## Lesson checklist

- [ ] Set window length and stride from expected incident duration.
- [ ] Explain what each feature captures.
- [ ] Choose outlier or novelty detection deliberately.
- [ ] Fit windows, imputers, scalers, and models on past training data only.
- [ ] Compare event-level results with the statistical baseline.

[← Previous]({{ '/time-series-anomaly-detection/lessons/04-seasonality-and-change/' | relative_url }}) ·
[Course index]({{ '/time-series-anomaly-detection/' | relative_url }}) ·
[Next: Deep time-series models →]({{ '/time-series-anomaly-detection/lessons/06-deep-time-series-models/' | relative_url }})
