---
title: "Lesson 2: Build a Trustworthy Timeline"
permalink: /time-series-anomaly-detection/lessons/02-prepare-time-series-data/
---

# Lesson 2: Build a Trustworthy Timeline

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/lessons/02-prepare-time-series-data/' | relative_url }})

## The detector may only be finding broken data

Our service exports one reading per minute. This fragment looks alarming:

| Timestamp | Requests | What actually happened? |
| --- | ---: | --- |
| 10:00 | 98 | Valid reading |
| 10:01 | 102 | Valid reading |
| 10:01 | 102 | Duplicate export |
| 10:03 | 0 | 10:02 is missing; zero is a collector default |

Without a data contract, a model will confidently detect exporter behavior rather
than service behavior.

## First, define the time axis

Check these questions before calculating any score:

1. Are timestamps unique, sorted, and in one documented time zone?
2. Is sampling event-driven or expected every second, minute, or hour?
3. Are missing readings absent, `null`, zero, or a sentinel such as `-1`?
4. Can readings arrive late and revise the recent past?
5. Did daylight-saving or clock changes create duplicate or missing local times?

Keep an **observed/not-observed mask** when filling gaps. An imputed value is an
estimate, not a measurement. A long gap can be an operational event of its own and
should not automatically be interpolated into a smooth, apparently normal line.

## See the components before removing them

A useful mental model is:

```text
observed series = level/trend + repeating seasonality + remainder

requests
  ^       lunch       lunch       lunch
  |      /\          /\          /\       ← daily seasonality
  | ____/  \________/  \________/  \____
  +--------------------------------------> time
        gradual growth →                    trend
```

The remainder is often called a **residual**. It is not automatically noise: an
incident, a calendar effect, or a model misspecification can all remain there.

Plot the raw series at several scales. One hour reveals spikes; several weeks reveal
daily and weekly patterns; months may reveal growth or a product migration.

## Split by time, never by shuffling

```text
past                                                        future
|---------- training ----------|---- validation ----|---- test ----|
 fit baseline and transforms       choose threshold       report once
```

- **Training** estimates normal behavior and model parameters.
- **Validation** chooses windows, hyperparameters, and alert thresholds.
- **Test** represents a later, untouched period used for the final comparison.

Use rolling backtests when one split is too dependent on a particular week. Always
train on earlier data and evaluate on later data in each fold.

## What future leakage looks like

At 10:00, a production detector cannot use 10:01. Leakage occurs when you:

- randomly shuffle timestamps across train and test;
- normalize all data using a mean computed from the full timeline;
- center a rolling average so that its window includes future values;
- fill a gap using the next known observation;
- tune a threshold repeatedly on the final test period.

Build every scaler, imputer, seasonal baseline, feature, and model using information
available at decision time. Simulate the same delay and late-data policy that
production will use.

## Lesson checklist

- [ ] Document timestamp, time-zone, sampling, lateness, and missing-value rules.
- [ ] Resolve or explicitly mark duplicates and gaps.
- [ ] Inspect trend and seasonality at several time scales.
- [ ] Create ordered training, validation, and test periods.
- [ ] Verify each feature uses only information available at scoring time.

[← Previous]({{ '/time-series-anomaly-detection/lessons/01-what-is-an-anomaly/' | relative_url }}) ·
[Course index]({{ '/time-series-anomaly-detection/' | relative_url }}) ·
[Next: Statistical baselines →]({{ '/time-series-anomaly-detection/lessons/03-statistical-baselines/' | relative_url }})
