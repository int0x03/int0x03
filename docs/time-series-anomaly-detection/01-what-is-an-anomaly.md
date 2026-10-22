---
title: "Lesson 1: What Counts as an Anomaly?"
permalink: /time-series-anomaly-detection/lessons/01-what-is-an-anomaly/
---

# Lesson 1: What Counts as an Anomaly?

[简体中文]({{ '/time-series-anomaly-detection/zh-cn/lessons/01-what-is-an-anomaly/' | relative_url }})

## Start with a story, not an algorithm

Suppose a service normally receives 20 requests per minute at 03:00 and 200 at
12:00. Consider these four incidents:

```text
point          context         collective       change
20 21 200 22   19 200 21       20 20 20 20      20 21 | 60 61 59
      ^            ^              └─ flat ─┘           └─ new normal
```

All four can be anomalies, but for different reasons. A detector looking only for
large values catches the first and may miss the other three.

## Four useful anomaly types

| Type | Plain-language test | Service example |
| --- | --- | --- |
| Point anomaly | Is this value strange by itself? | 2,000 requests per minute when values rarely exceed 300. |
| Contextual anomaly | Is it strange *at this time*? | 200 requests at 03:00, although 200 is normal at noon. |
| Collective anomaly | Is this sequence shape strange? | A sensor repeats exactly 20 for an hour. |
| Change point | Did the process move to a new normal? | Traffic stays three times higher after a deployment. |

A change point is not simply a long point anomaly. It asks where the data-generating
process changed; after that boundary, using the old baseline forever may create an
endless alert.

## Choose the object you will detect

The **anomaly unit** determines labels, scores, and evaluation:

```text
timestamp:  10:00 10:01 10:02 10:03 10:04 10:05
reading:       20    21    80    90    75    22
point labels:   .     .     A     A     A     .
event label:              [ one incident ]
window:             [10:01 ........ 10:05]
```

- A **point** detector decides at every timestamp.
- A **window** detector scores a fixed-length sequence.
- An **event** groups adjacent alerts into one operational incident.
- A **regime** detector finds a boundary and a lasting state.

For an operator, three red points caused by one outage usually mean one event, not
three independent successes.

## Write the problem before choosing a method

Replace “detect anomalies in traffic” with an observable statement:

> Detect traffic drops that last at least five minutes, relative to the same time
> of day, within two minutes of the event starting. Planned maintenance is not an
> anomaly.

This sentence specifies direction, duration, context, latency, and exclusions. It
also exposes unanswered questions: How large must a drop be? Who marks maintenance?
What happens during missing data?

## Common beginner mistakes

1. **“Anomaly means rare.”** A rare product launch may be expected; a common-looking
   value at the wrong hour may be harmful.
2. **“Every spike is bad.”** Direction and business context matter.
3. **“The model will define normal.”** People responsible for the system must still
   define useful incidents and exclusions.
4. **“One label per point is enough.”** Duration and event grouping affect evaluation.

## Lesson checklist

- [ ] Name the anomaly type or types.
- [ ] Choose point, window, event, or regime as the unit.
- [ ] State direction, minimum duration, temporal context, and exclusions.
- [ ] Decide how quickly an alert must arrive.
- [ ] Describe one normal case that looks unusual and one harmful case that looks ordinary.

[Course index]({{ '/time-series-anomaly-detection/' | relative_url }}) ·
[Next: Build a trustworthy timeline →]({{ '/time-series-anomaly-detection/lessons/02-prepare-time-series-data/' | relative_url }})
