---
title: "Lesson 7: Choose Thresholds and Evaluate Events"
permalink: /time-series-anomaly-detection/lessons/07-thresholds-and-evaluation/
layout: math-page
---

# Lesson 7: Choose Thresholds and Evaluate Events

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/lessons/07-thresholds-and-evaluation/' | relative_url }})

## A score is not yet an alert

The same detector can serve two teams differently:

```text
score       /\       /\
threshold 1 -----------        catches more, alerts more
threshold 2 -----^-----        catches less, alerts less
time  ---------------------------------------------->
```

The model produces an **anomaly score**. A decision rule combines that score with a
threshold, duration rule, suppression policy, and often business context. Changing
the threshold does not retrain the model.

## Calibrate on validation data

Choose a threshold using a later validation period, then freeze it before opening
the test results. Useful objectives include:

- maximize event recall while keeping false alerts below five per week;
- minimize a cost where a missed outage costs more than an investigation;
- meet a required event precision or detection delay;
- use a high score quantile when labels are unavailable, then review sampled alerts.

Do not choose a contamination percentage merely because a library requires one.
It encodes an assumption about anomaly prevalence and should be validated.

## Turn adjacent points into events

Suppose one outage lasts five minutes:

```text
truth:       ......[========== one event ==========]......
alerts:      ..........^..^..^............................
point view:             three detected points
event view:             one detected event, three alerts to merge
```

Document an event-matching policy:

1. Merge alerts separated by less than a chosen gap.
2. Match a predicted event to truth when intervals overlap, optionally with an
   early/late tolerance.
3. Match each true event at most once.
4. Count unmatched predicted events as false alerts and unmatched true events as
   misses.

This policy must be fixed before comparing models.

## Measure what operators experience

For event counts, let true positives ($$TP$$) be detected true events, false
positives ($$FP$$) be false alert events, and false negatives ($$FN$$) be missed
events:

$$
\operatorname{precision} = \frac{TP}{TP + FP}
$$

$$
\operatorname{recall} = \frac{TP}{TP + FN}
$$

- **Precision** asks: when the system alerts, how often is an event real?
- **Recall** asks: of all real events, how many did it detect?
- **Detection delay** measures time from event start to the first accepted alert.
- **Alert volume** measures the review burden per day or week.

Point-wise accuracy is usually misleading: predicting “normal” everywhere can be
99.9% accurate when incidents are rare, while detecting none of them.

## Evaluate fairly

- Use a test period later than training and validation.
- Include quiet periods, busy periods, seasonal changes, and known incidents.
- Compare every candidate at the same alert budget or operational cost.
- Report uncertainty when only a few events exist; one event can move recall greatly.
- Inspect false alerts and misses by anomaly type instead of trusting one aggregate.

## Lesson checklist

- [ ] Define score direction and the complete decision rule.
- [ ] Select the threshold on validation data, not test data.
- [ ] Define event merging, tolerance, and one-to-one matching.
- [ ] Report event precision, recall, delay, and alert volume.
- [ ] Compare methods under the same time periods and alert budget.

[← Previous]({{ '/time-series-anomaly-detection/lessons/06-deep-time-series-models/' | relative_url }}) ·
[Course index]({{ '/time-series-anomaly-detection/' | relative_url }}) ·
[Next: Production and language models →]({{ '/time-series-anomaly-detection/lessons/08-production-and-llms/' | relative_url }})
