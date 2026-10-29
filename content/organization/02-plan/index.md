---
title: Quality Planning
parent: Organizing Quality
nav_order: 2
layout: default
has_children: true
---

# Quality Planning

**Quality planning** decides, before the work starts, which quality activities a project will run, on what, when, by whom and at what cost, and writes those decisions down so that they survive schedule pressure {% cite miranda2015asa %}. The pages follow the order in which a plan is built: first the decisions, then the activities per phase, how strict each gate must be, how much effort it takes, how it is documented, how progress is reported, and how domains change the answers.

| Page | What it answers | Main sources |
|---|---|---|
| [The Quality Plan](01-quality-management-planning.md) | what a plan is and the eleven decisions it records | Miranda; Koopman |
| [V&V Activities by Phase](02-vv-activities-process-models.md) | the minimum V&V tasks per phase and test-process maturity | Wallace and Fujii; Hrabovská |
| [Verification Methods per Quality Characteristic](02b-methods-per-characteristic.md) | which methods give evidence for which ISO/IEC 25010 characteristic | NASA; ISO/IEC 25010 |
| [Criticality and Quality Gates](03-quality-gates-criticality.md) | how criticality sets entry and exit criteria | DO-178B; IEEE 829; Uzunova |
| [Estimating Test Effort](04-test-effort-estimation.md) | how many tests and hours, and how to sanity-check them | Bluemke; Miranda |
| [Test Documentation (IEEE 829)](05-test-documentation-ieee829.md) | master and level test plans, scaled to integrity | IEEE 829 |
| [Test Status Reporting](06-metrics-dashboards-reporting.md) | what "30% done" means, and what pipelines need to show | Kaner; Ahmad |
| [Domain-Specific V&V](07-domain-specific-vv.md) | how robotics, IoT, automotive and model-based projects differ | four systematic reviews |

[Worked Case: A Quality Plan for a Booking Service](worked-case.md) applies the section end to end: criticality classes, gate criteria with Python tool commands, triage rules, roles, an effort estimate and a one-page plan to copy.

The study notes and revision questions are [SN A04: Quality Planning](../../material/qam/SN-A4-QualityPlan.md) and [RQ A04: Quality Planning](../../material/qam/RQ-A4-QualityPlan.md). The costs that a plan balances are in [Cost of Quality](../01-coq/index.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
