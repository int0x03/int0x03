---
title: Time Series Anomaly Detection
permalink: /time-series-anomaly-detection/
layout: math-page
---

# Time Series Anomaly Detection: A Beginner's Course

Learn to find unusual behavior in time-dependent data without starting from a wall
of algorithm names. Every lesson begins with a concrete monitoring story, adds one
idea at a time, and ends with a small checklist.

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/' | relative_url }})

## The story we will follow

Imagine a service that reports requests per minute. Most nights are quiet, every
lunchtime is busy, deployments sometimes change the baseline, and a broken sensor
occasionally repeats one value. “Unusual” therefore means more than “a large
number.” This course gradually builds a detector for that service.

```text
raw readings → trustworthy timeline → baseline → anomaly score → threshold
             → event evaluation → alert → explanation and feedback
```

## Learning path

Read the lessons in order if this is your first encounter with anomaly detection.
Lessons 1–4 form the essential statistical path; lessons 5–8 explain when and how
to go further.

| # | Lesson | Main question | You will produce |
| --- | --- | --- | --- |
| 1 | [What counts as an anomaly?]({{ '/time-series-anomaly-detection/lessons/01-what-is-an-anomaly/' | relative_url }}) | Is the problem a point, context, sequence, or change? | A precise anomaly definition |
| 2 | [Build a trustworthy timeline]({{ '/time-series-anomaly-detection/lessons/02-prepare-time-series-data/' | relative_url }}) | Can the data be trusted without seeing the future? | A time-ordered data split |
| 3 | [Build the first statistical baseline]({{ '/time-series-anomaly-detection/lessons/03-statistical-baselines/' | relative_url }}) | How far is this value from normal? | Z-score, Median Absolute Deviation (MAD), and Interquartile Range (IQR) baselines |
| 4 | [Handle seasonality and persistent change]({{ '/time-series-anomaly-detection/lessons/04-seasonality-and-change/' | relative_url }}) | Is it unusual for *this time*, or has normal changed? | Residual and change scores |
| 5 | [Turn windows into machine-learning features]({{ '/time-series-anomaly-detection/lessons/05-classical-machine-learning/' | relative_url }}) | Is the unusual object a multistep shape? | A leak-free feature pipeline |
| 6 | [Understand deep time-series models]({{ '/time-series-anomaly-detection/lessons/06-deep-time-series-models/' | relative_url }}) | When do complex temporal or cross-sensor relations matter? | A model-mechanism choice |
| 7 | [Choose thresholds and evaluate events]({{ '/time-series-anomaly-detection/lessons/07-thresholds-and-evaluation/' | relative_url }}) | Does a high score create a useful alert? | A calibrated, event-level evaluation |
| 8 | [Put the detector into production]({{ '/time-series-anomaly-detection/lessons/08-production-and-llms/' | relative_url }}) | How does a detector stay useful after launch? | A monitored detection workflow |

## Keep three questions separate

An algorithm name alone never describes a complete solution:

1. **What is the anomaly unit?** A point, window, event, or lasting regime change.
2. **How is a score produced?** A rule, distance, forecast error, reconstruction
   error, probability, or learned representation.
3. **How is the model taught?** Rules, unsupervised data, known-normal history,
   labeled examples, or a self-supervised task.

For example, a forecasting model can be self-supervised during training, produce
a residual score, and ultimately detect an event made of many adjacent points.

## Quick starting-point map

| Situation | Start here | Do not forget |
| --- | --- | --- |
| Stable single metric | MAD or IQR | Handle a zero spread explicitly. |
| Daily or weekly pattern | Seasonal residual plus MAD | Compare like times with like times. |
| Small persistent shift | Exponentially Weighted Moving Average (EWMA) or Cumulative Sum (CUSUM) | Validate the expected detection delay. |
| Unusual window shape | Features plus Isolation Forest or Local Outlier Factor (LOF) | Fit every transform on past data only. |
| Correlated sensors or long dependencies | Forecasting, reconstruction, or graph model | Prove improvement over a simple baseline. |
| Rich logs around numeric alerts | Numeric detector, then a Large Language Model (LLM) for triage | Do not use fluent text as a calibrated score. |

## A practical rule

Start with the simplest detector that represents trend and seasonality. Add
complexity only when a later, untouched test period shows a meaningful improvement
in event recall, false alerts, or detection delay—not merely lower training loss.

[Start lesson 1 →]({{ '/time-series-anomaly-detection/lessons/01-what-is-an-anomaly/' | relative_url }})

[Back to home]({{ '/' | relative_url }})
