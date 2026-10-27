---
title: Reading Metric Data
parent: Measurement
nav_order: 4
layout: default
---

# Reading Metric Data

**Reading metric data** means summarising measurements with statistics that fit their scale and spread, and judging what a relationship between two metrics shows. Software data make both hard: they are skewed, and almost every code metric grows with size {% cite rosenberg2008statistical %}.

## 1. Mean or median?

The **mean** is the sum divided by the count; the **median** is the middle value once the data are sorted; the **mode** is the most frequent value. On symmetric data they agree. On skewed data they do not.

**Example.** Ten bugs were fixed last sprint: nine took 1 hour each and one took 100 hours.

- Mean: (9 × 1 + 100) / 10 = **10.9 hours**.
- Median: sorted, the 5th and 6th values are both 1, so the median is **1 hour**.

The mean describes no bug that actually happened. Fix times, response times and effort are skewed like this: report the **median** and a high **percentile** instead of the mean: the 95th percentile is the value 95% of observations fall below.

## 2. Correlation is not causation

Two metrics are **correlated** when they tend to rise and fall together. A third variable, a **confounder**, may drive both. Coffee drinkers once showed more lung cancer: smokers drink more coffee.

In software the usual confounder is **size**. Large classes have more methods, more coupling and more complexity, and they also have more faults, simply because there is more code to be wrong. Coupling here means **CBO** (coupling between objects), the number of other classes a class uses or is used by, one of the six class-level **CK metrics** in [Object-Oriented Metrics](oo-metrics.md).

![Class size points to both an OO metric and to fault-proneness; the metric-to-fault arrow is the hypothesis that size may explain away](confounding-size.svg)

_The confounding pattern, after El Emam et al. Without controlling for size, the dashed link looks real._ {% cite elemam2001confounding %}

**Controlling for size** means comparing classes of similar size. **Example** (constructed numbers, to show the idea): 60 classes are split by size and by coupling, and we count how many had a fault.

| | CBO ≤ 5 | CBO > 5 |
|---|---|---|
| Small (under 300 LOC) | 2 of 20 faulty (10%) | 1 of 10 faulty (10%) |
| Large (300 LOC or more) | 6 of 10 faulty (60%) | 12 of 20 faulty (60%) |
| **All classes** | **8 of 30 (27%)** | **13 of 30 (43%)** |

Across all classes, highly coupled ones look riskier: 43% against 27%. Within each size band the fault rate is the same whatever the coupling, 10% for small classes and 60% for large ones. The overall difference exists only because most highly coupled classes are large. Studies do the same with a **regression model**, an equation that predicts faults from one or more metrics: fit it on size alone, then on size plus the metric, and keep the metric only if the second predicts clearly better.

El Emam and colleagues did this on a large C++ system: before controlling for size, the CK metrics were associated with which classes had field faults (found after release); **after controlling for size, none of them was** {% cite elemam2001confounding %}. On a second C++ system, 4 of 24 object-oriented metrics stayed associated with faults, and 2 of those improved prediction {% cite elemam1999validation %}.

## 3. Significant is not the same as large

A **statistically significant** result is one unlikely to appear by chance if there were no real difference. The **p-value** is the probability of seeing a difference at least this large when there is none; by convention p < 0.05 counts as significant. An **effect size** says how large the difference is; ε² (epsilon squared) is the share of the variation that the grouping explains, from 0 to 1. With enough data, tiny differences become significant. A study of 2,405 Python files generated with different prompt styles found a significant difference in lines of code (p = 0.003), yet prompt style explained less than 1% of the variation, the spread of values around their mean (ε² = 0.008); the largest contrast, chain-of-thought against few-shot prompts, was a median of 7 against 11 source lines {% cite dellaporta2025llm %}.

## When to use it — and when not

- **Use** the median and percentiles for times and effort; the mean only for roughly symmetric interval or ratio data.
- **Use** size as a control variable whenever a code metric is compared with defects or effort.
- **Do not** read a correlation as a lever: reducing a metric that only tracks size will not reduce faults.
- **Do not** report a p-value without the effect size.

## How solid is this?

- **Where it comes from.** The statistical rules are textbook {% cite rosenberg2008statistical %}; the size confounding is from El Emam et al.; Gil and Lalouche (2017, not held here) report the same.
- **What is contested.** El Emam's two reports differ (none of the metrics in one system, 4 of 24 in another): whether a metric adds anything beyond size depends on the system and must be checked. See [Do Code Metrics Predict Quality?](evidence.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
