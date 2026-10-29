---
title: Six Sigma
parent: Process Improvement
nav_order: 7
layout: default
---

# Six Sigma

**Six Sigma** is a data-driven improvement method, started at Motorola in the 1980s, that improves a process by measuring its variation and removing the causes of defects; in statistical terms, a "six sigma" process produces no more than **3.4 defects per million opportunities** (DPMO) {% cite motorola1985sixsigma antony2002sixsigma %}.

## 1. DMAIC

A Six Sigma improvement project follows five phases, **DMAIC** {% cite antony2002sixsigma %}:

| Phase | Question | Typical output |
|---|---|---|
| **Define** | What is the problem, and what is critical to quality (CTQ) for the customer? | a project charter with one measurable goal |
| **Measure** | How does the process perform today? | a baseline, for example a defect rate |
| **Analyze** | What causes the defects or the variation? | causes confirmed with data, not opinion |
| **Improve** | Which change removes the cause? | a tested change and its measured effect |
| **Control** | How do we keep the gain? | monitoring, a control chart, an updated standard |

Defining an *opportunity* matters: it is each place where a unit can fail to meet a requirement, so DPMO compares processes of different complexity.

## 2. Worked example

A team runs 500 CI builds in a month; each build has four checks that can fail (compile, unit tests, linter, packaging), so there are 500 × 4 = 2,000 opportunities. Thirty checks fail for reasons that are not in the code under test: a flaky test, a missing cache, a full disk (a constructed example).

In *Define*, the team states the problem and its CTQ: developers need a red build to mean a code defect, so the goal is to halve build failures not caused by the code under test. *Measure* gives DPMO = 30 / 2,000 × 1,000,000 = **15,000**, far from the six-sigma level of 3.4. In *Analyze* it sorts the 30 failures by cause and finds, say, that 18 are the same flaky test; Improve fixes or quarantines it; Control adds the infrastructure-failure rate to the team's dashboard so that a rise is noticed.

## 3. What makes it work

In a survey of UK companies, Banuelas Coronado and Antony ranked twelve critical success factors; **management commitment and involvement** came first, followed by cultural change and linking Six Sigma to business strategy, and incentive programs came last {% cite antony2002sixsigma %}. A survey of 101 companies using **Lean Six Sigma**, which combines Six Sigma's variation reduction with Lean's removal of waste, also put management commitment first and organizational culture second; 46% of the companies used it mainly for cost savings or cost avoidance {% cite laureani2012leansixsigma %}.

## When to use it — and when not

Use Six Sigma for processes that run often enough to measure, such as builds, deployments, incident handling or support tickets, where variation itself is the problem. It suits creative design work poorly: a feature is designed once, so there is no population of repetitions to analyze. It also needs people trained in its statistics, which is why companies appoint dedicated roles.

## Limitations

- **3.4 DPMO is a target, not a description.** Few software processes have the volume or stable definitions to reach or even measure it.
- **The opportunity count can be gamed.** Counting more opportunities per unit lowers DPMO without any real improvement.
- **The success-factor rankings are perceptions,** from practitioners in surveys, not measured outcomes; the Lean Six Sigma survey itself notes that 101 responses are too few to generalize {% cite laureani2012leansixsigma %}.

## How solid is this?

- **Where it comes from.** Motorola's program, a survey of Six Sigma's critical success factors, and a survey of Lean Six Sigma practitioners {% cite motorola1985sixsigma antony2002sixsigma laureani2012leansixsigma %}.
- **What is contested.** Banuelas Coronado and Antony argue that Six Sigma must be run as an overall approach to managing an organization, not as a collection of statistical tools {% cite antony2002sixsigma %}; their survey measures what practitioners believe matters, not what changed results.
- **What we do not hold.** The derivation of the 3.4 DPMO figure; the sources state it without deriving it.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
