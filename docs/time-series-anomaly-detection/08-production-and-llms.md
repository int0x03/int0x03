---
title: "Lesson 8: Put the Detector into Production"
permalink: /time-series-anomaly-detection/lessons/08-production-and-llms/
---

# Lesson 8: Put the Detector into Production

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/lessons/08-production-and-llms/' | relative_url }})

## The model is one box in a larger system

```text
metrics → quality checks → features → numeric detector → score → decision rule
   │                                                        │
   └── availability monitoring                        event + evidence
                                                            ↓
logs + deployments + runbooks → context retrieval → explanation → operator
                                                            │
                                                     feedback and labels
```

The system needs ownership, fallback behavior, and observability around every arrow.
A detector that is accurate in a notebook but silent when data stops arriving is
not production-ready.

## Monitor the monitor

Track at least four layers:

| Layer | Example signals |
| --- | --- |
| Input | Delay, missing rate, duplicates, range, schema and unit changes |
| Score | Distribution, seasonal pattern, fraction above threshold |
| Alert | Events per day, suppression rate, affected services, investigation load |
| Outcome | Confirmed incidents, false alerts, misses, detection delay |

A sudden fall to zero alerts can mean a healthy service—or a broken pipeline. Keep
heartbeats and data-quality alerts independent from the anomaly model.

## Drift needs a response policy

**Data drift** means input or score distributions changed. **Concept drift** means
the relation between observations and real incidents changed. Neither automatically
means “retrain now.” First ask whether the cause is expected growth, a sensor or
unit change, a deployment, an incident, or seasonality omitted from the model.

Define who may retrain, what history is eligible, which evaluations must pass, how
thresholds are recalibrated, and how to roll back. Keep model, feature, data, and
threshold versions together.

## Foundation models and language models have different jobs

A **time-series foundation model** is pretrained on many numeric series. It may
provide a forecast, prediction interval, or embedding used by a numeric detector.
It can help with cold starts, but still needs domain evaluation, latency checks, and
threshold calibration.

A general-purpose Large Language Model (LLM) is better placed *after* a calibrated
numeric detector. It can combine the event with authorized logs, deployment records,
metric descriptions, and runbooks to:

- summarize what changed and when;
- correlate related alerts;
- retrieve likely runbook steps;
- draft a triage note with links to evidence.

It should not invent measurements, silently change thresholds, or claim a root cause
without evidence.

## Make assisted triage auditable

Pass structured context rather than an unexplained screenshot:

```text
event: start/end, score, threshold, affected variables
evidence: source, timestamp, excerpt or aggregate
changes: deployments and configuration changes
output: summary, hypotheses, cited evidence, uncertainty, next checks
```

Protect credentials and personal data, restrict retrieval to the operator's access,
label generated hypotheses, and retain links to source evidence. High-impact actions
such as rollback or paging should remain behind deterministic rules or human approval.

## A staged rollout

1. **Replay:** run on historical data with no operational effect.
2. **Shadow:** score live data but do not notify responders.
3. **Limited:** notify one team or service with conservative thresholds.
4. **General:** expand only after alert load and event outcomes are acceptable.
5. **Review:** inspect drift, misses, false alerts, and operator feedback regularly.

Keep the simple baseline running during rollout. It is both a fallback and an early
warning that a complex model no longer earns its cost.

## Final checklist

- [ ] Assign owners for data, model, threshold, and incident response.
- [ ] Monitor input, score, alert, and outcome health separately.
- [ ] Version and test retraining and rollback procedures.
- [ ] Use an LLM for evidence-linked assistance, not numeric score calibration.
- [ ] Roll out in stages and keep a validated fallback.

[← Previous]({{ '/time-series-anomaly-detection/lessons/07-thresholds-and-evaluation/' | relative_url }}) ·
[Course index]({{ '/time-series-anomaly-detection/' | relative_url }})
