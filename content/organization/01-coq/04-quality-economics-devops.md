---
title: Quality Costs in Continuous Delivery
parent: Cost of Quality
nav_order: 5
layout: default
---

# Quality Costs in Continuous Delivery

In continuous delivery, **quality costs** are the same four categories as in any project, but they are spent in two places: before release (**shift-left**: reviews, test-driven development, static analysis, continuous-integration tests) and after release (**shift-right**: monitoring, canary releases, feature flags, chaos experiments) {% cite gottam2025convergent magori2024early %}. This page applies the [CoSQ categories](01-cosq-foundations.md) to both, and prices one of the commonest pipeline costs: flaky tests.

## 1. Shift-left and shift-right in CoSQ terms

**Shift-left** moves verification earlier. Magori's review of early-phase techniques (design review, code review, inspection, test-driven development) reports fewer defects, shorter development cycles and lower cost than testing after development {% cite magori2024early %}. **Shift-right** validates in production: telemetry, A/B tests, canary releases that expose new functionality to a few users first, and chaos engineering, which injects failures on purpose to test recovery {% cite gottam2025convergent %}. Gottam argues that the two must feed each other, with production findings shaping the next tests {% cite gottam2025convergent %}; AI-based observability adds anomaly detection and automated root-cause analysis on top of logs, metrics and traces {% cite keating2024ai %}.

Applying the categories to these practices gives:

| Practice | Category | Why |
|---|---|---|
| Code review, test-driven development, coding standards | prevention | keep defects out before the change is complete (reviews by the handbook's [booking convention](01-cosq-foundations.md#1-four-categories)) |
| Continuous-integration tests, static analysis, canary monitoring | appraisal | automated checks of the product's condition (a team may book static analysis with reviews if it always does) |
| Investigating and fixing a red pipeline, fixing a review finding | internal failure | spent because a defect exists, before any user meets it |
| Incident response, hot fixes, rolling back a canary, lost users | external failure | users met the failure, even if only a few |

A canary release caps the external failure without changing its category: the canary has shipped to real users, so the rollback and the harm to the 1% who met the defect are external failure, but the same defect no longer reaches all users.

## 2. Worked example: the price of flaky tests

A **flaky test** passes or fails on unchanged code. Leinen et al. measured the cost over five years of a project with about 30 developers and about one million lines of code {% cite leinen2024flaky %}:

| Measure | Value |
|---|---|
| Developer time spent on flaky tests | at least 2.5% of productive time |
| of which investigating suspicious failures | 1.1% |
| of which repairing flaky tests | 1.3% |
| of which building tools to monitor them | 0.1% |
| Manual investigation of one failed pipeline | USD 5.67 |
| Automatically rerunning the failed test | 0.02 cents (USD 0.000236) |
| Test-suite executions with at least one flaky failure, found by the new rerun strategy in five months | 4.8%, about one in 20 |

The source does not book the three flaky-test costs in CoSQ categories; this handbook does. **Investigating** suspicious failures is internal failure: the effort exists only because a test failed before release. **Repairing** flaky tests is internal failure too: it is rework of a defective test (and, in a quarter of the repairs, of the product code). **Building monitoring tools** is appraisal: the tools discover the condition of the test suite.

Investigating a failure costs about 24,000 times a rerun (5.67 / 0.000236), so the company shifted effort from developers to compute: it raised the number of automatic reruns before a pipeline is declared red. In CoSQ terms, cheap appraisal (reruns) replaced expensive internal failure (investigation).

## When to use it, and when not

- **Use it** when a team argues about adding a pipeline stage, reruns or monitoring: price each option in the four categories and compare.
- **Do not apply** the flaky-test figures directly elsewhere; the costs depend on the local pipeline, hourly rates and compute prices.

## Limitations

- **Reruns hide real defects.** A test that fails intermittently because of a race condition in the product is not flaky; rerunning it until green suppresses a true failure. Leinen et al.'s cost model counts time, not the defects that reruns mask.
- **The shift-right sources are argument, not measurement.** Gottam and Keating describe frameworks; neither reports a controlled cost comparison.

## How solid is this?

- **Where it comes from.** The flaky-test case from Leinen et al.; shift-left evidence from Magori's review; shift-right practices from Gottam and Keating {% cite leinen2024flaky magori2024early gottam2025convergent keating2024ai %}.
- **What is contested.** Leinen et al.'s abstract and calculation give a rerun as 0.02 cents (USD 236 × 10⁻⁶); the introduction says USD 0.02. With the rounded 0.02 cents the ratio is 5.67 / 0.0002 ≈ 28,000; with the introduction's dollar figure it would be 5.67 / 0.02 ≈ 280. The calculation supports the cents.
- **What we do not hold.** Measured cost data for chaos engineering or canary releases.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
