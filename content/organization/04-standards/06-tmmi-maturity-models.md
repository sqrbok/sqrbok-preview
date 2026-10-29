---
title: TMMi & Maturity Models
parent: Quality Standards
nav_order: 6
layout: default
---

# TMMi & Maturity Models

The **Test Maturity Model integration (TMMi)** is a staged maturity model for the test process alone: five levels, 16 process areas, 50 specific goals and 173 specific practices, built like [CMMI](01-cmmi-overview.md) but with the testing detail that CMMI lacks {% cite vanveenendaal2022tmmi %}. The TMMi Foundation was founded in 2010 and published the first stable version in 2012; it uses ISTQB's testing terminology.

## 1. Levels and process areas

| Level | Name | Process areas |
|---|---|---|
| 1 | Initial | none: testing is ad hoc |
| 2 | Managed | test policy and strategy, test planning, test monitoring and control, test design and execution, test environment |
| 3 | Defined | test organization, test training program, test lifecycle and integration, non-functional testing, peer reviews |
| 4 | Measured | test measurement, product quality evaluation, advanced reviews |
| 5 | Optimization | defect prevention, quality control, test process optimization |

The counts add to 5 + 5 + 3 + 3 = 16. Each process area breaks down into goals and practices; test policy and strategy, for instance, has three specific goals: establish a test policy, establish a test strategy and establish test performance indicators {% cite vanveenendaal2022tmmi %}.

## 2. Example: why organizations adopt it

Van Veenendaal, Garousi and Felderer surveyed the 114 companies and teams formally assessed by the TMMi Foundation; 74 responded (64.9%) {% cite vanveenendaal2022tmmi %}. The most common motives were better software quality (70%), higher test productivity (68%), lower product risk and the certificate itself (62% each). 87% said TMMi met or exceeded their expectations, and 88% saw benefits for product quality.

Respondents gave concrete figures, each from one organization: regression test execution cut from 4 hours to 30 minutes, defect leakage to production down from 10% to 5%, and a **defect detection percentage (DDP)** in system testing up from 60% to 70%. DDP is the number of defects a test phase finds divided by the number found by that phase and by everything afterwards. If system test finds 60 defects and 40 more turn up later, DDP is 60 / (60 + 40) = 60%; finding 70 of the same 100 gives 70%. DDP measures a test phase; it is not IBM's Defect Prevention Process (DPP) on [Defect Prevention](../03-process/05-defect-prevention.md).

## 3. Other maturity models

TMMi is one of many: a review cited in the survey counts 58 models for test maturity and capability alone {% cite vanveenendaal2022tmmi %}. Maturity models also exist for usability, where most are derived from CMMI or ISO/IEC 15504 (the process-assessment standard known as SPICE) but few are validated {% cite lacerda2018usability %}, and for universities, where a review of 23 studies found that CMMI-style models dominate but cover isolated areas and say *what* to reach rather than *how* {% cite mikusova2020maturity %}.

| Need | Model |
|---|---|
| Overall development process | [CMMI](01-cmmi-overview.md) |
| Test process | TMMi |
| IT service management | [ITIL](03-itil-framework.md) |
| Certified quality management system | [ISO 9001](05-iso-9000-quality-systems.md) |

## When to use it — and when not

Use TMMi when testing is a large part of the cost and the organization wants a roadmap for it specifically, or alongside CMMI, which has verification and validation process areas but little testing detail. A team without a basic test plan should not start with an assessment; Level 2's five process areas are the practical checklist.

## Limitations

- **Survey of the converted.** All respondents had already paid for a formal assessment, so satisfaction rates are likely to be high.
- **Anecdotal metrics.** The before-and-after figures are single organizations' statements, not measured across the sample.
- **The foundation reports on its own model.** The survey was run by the TMMi Foundation, whose CEO is one of the authors.

## How solid is this?

- **Where it comes from.** The TMMi Foundation's user survey, and reviews of usability and university maturity models {% cite vanveenendaal2022tmmi lacerda2018usability mikusova2020maturity %}.
- **What we do not hold.** The TMMi specification; the process-area names above follow the published framework, which the survey cites but the review does not hold.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
