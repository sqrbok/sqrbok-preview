---
title: Measurement
parent: Quality
nav_order: 3
layout: default
has_children: true
---

# Measurement

**Software measurement** assigns numbers to attributes of software products, processes and people by a stated rule, so that the numbers can support a decision: is this release ready, is this module getting harder to change, did the new review process work? A quality model says *which* attributes matter; measurement says how to put a number on them, and when that number can be trusted {% cite park1996goal %}.

The pages follow the order in which the questions come up when a team introduces a metric: what a measurement is, how it misleads, which arithmetic its scale allows, how to read the data, how to design a metric from a goal, and finally the classic code metrics and the evidence for them.

| Page | Question it answers | Key idea |
|---|---|---|
| [Measurement Definitions](definitions.md) | What counts as a measurement, and when is it a good one? | Reduction of uncertainty; accuracy, precision, resolution |
| [Measurement Pitfalls](pitfalls.md) | How do metrics mislead? | Relative vs absolute numbers, McNamara fallacy, gaming |
| [Measurement Scales](types.md) | Which arithmetic is allowed on these numbers? | Nominal, ordinal, interval, ratio, absolute |
| [Reading Metric Data](statistics.md) | What does this column of numbers say? | Median vs mean, correlation, confounding by size |
| [Developing Metrics with GQM](development.md) | Which metric should we collect at all? | Goal → Question → Metric; operational definitions |
| [Size and Complexity Metrics](examples.md) | How are LOC, cyclomatic complexity and Halstead's metrics computed? | One worked function, three metrics |
| [Maintainability Index](maintainability-index.md) | How do the three combine into one score? | A regression formula; per file; the comment term |
| [Object-Oriented Metrics](oo-metrics.md) | What do the class-level CK metrics measure? | Six metrics, and why fixed thresholds fail |
| [Do Code Metrics Predict Quality?](evidence.md) | How far can these numbers be trusted? | Size confounding, accuracy on rare defects |
| [Defect Density Benchmarks](defect-density-benchmarks.md) | How many post-release defects per KLOC is typical? | Median 4.3 across 109 projects; open source, language and size move it |

Metrics for specific quality attributes live with those attributes: [Measuring Maintainability](../../attributes/maintainability/measurement.md), [Reliability Measurement](../../attributes/reliability/measurement.md) and [Performance Measurement](../../attributes/performance/measurement.md). Test coverage, the most common test metric, has its own section: [Coverage](../../verif/coverage/index.md).

---

### References

{% bibliography --cited %}

### Acknowledgments

The lectures behind this area took early inspiration from the *Analysis of Software Artifacts* course of Eduardo Miranda and Claire Le Goues (Carnegie Mellon University). The metrics case study in the original lectures was credited to Christian Kästner. The pages here are original, written from the literature review; any errors are ours.

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
