---
title: Cost of Quality
parent: Organizing Quality
nav_order: 1
layout: default
has_children: true
---

# Cost of Quality

**Cost of quality** expresses quality in the unit managers decide with, money or effort: what a project spends to prevent and find defects, and what it loses when defects escape. Software spends far more on quality than manufacturing does, and most of it goes on rework that earlier prevention could avoid {% cite houston1999cost %}. The pages in this section answer three questions in order: **how big** the cost is and how it splits, **where defects come from and where they are caught**, and **which modern practices** move the cost.

| Page | What it answers | Main sources |
|---|---|---|
| [Cost of Software Quality](01-cosq-foundations.md) | the four cost categories, their size, and how process maturity shifts them | Knox; Houston and Keats |
| [Defect Injection and Removal](02-defect-injection-removal.md) | how defects flow through phases, and how to compare improvement options | Miranda; COQUALMO |
| [Defect Origin and Detection](03-defect-classification.md) | where defects were made and found; removal efficiency per activity | Kan |
| [Defect Classification Schemes](04-classification-schemes.md) | ODC, HP and LiDeC for richer process feedback | Butcher; Huber; Mellegård |
| [Quality Costs in Continuous Delivery](04-quality-economics-devops.md) | shift-left and shift-right in cost terms; the price of flaky tests | Leinen; Gottam |
| [Value of Code Review](05-code-review-roi.md) | which review comments pay off | Turzo; Yu; Fu |
| [Technical Debt as a Quality Cost](06-technical-debt.md) | which tool to believe, where debt accumulates | Amanatidis; Bhatia |
| [Defect Prediction with Machine Learning](07-defect-prediction-ml.md) | why accuracy misleads, and what to measure instead | Khleel; Bhutamapuram |

[Worked Case: Measuring a Team's Cost of Quality](worked-case.md) applies the section to one team: time reports and tracker fields, the CoQ table over three releases, DRE and a recommendation.

The study notes and revision questions for this section are [SN A01: Cost of Quality](../../material/qam/SN-A1-CoSQ.md) and [RQ A01: Cost of Quality](../../material/qam/RQ-A1-CoSQ.md). Quality planning, which turns these costs into decisions for one project, is the next section, [Quality Planning](../02-plan/index.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
