---
title: DevOps Teams & Adoption
parent: Industry Practices
nav_order: 2
layout: default
---

# DevOps Teams & Adoption

**DevOps adoption** is the reorganization of teams and responsibilities that puts [DevOps](01-devops-foundations.md) into practice; studies find that *how* development and operations people are grouped predicts delivery performance more than which tools they use {% cite lopez2021devops macarthy2020devops %}.

## 1. Four team patterns

López-Fernández and colleagues interviewed 46 people in 31 companies and found four recurring structures, from least to most integrated {% cite lopez2021devops %}:

| Pattern | Structure |
|---|---|
| A. Interdepartmental collaboration | Dev and Ops stay in separate departments and collaborate occasionally; several managers, no shared ownership |
| B. Interdepartmental Dev-Ops team | a stable product team whose members still report to different departments |
| C. Boosted cross-functional team | a development team temporarily "boosted" by DevOps experts who coach it until it can build and run its own product |
| D. Full cross-functional team | one autonomous team with one leader runs the whole lifecycle ("you build it, you run it") |

The more mature the pattern, the better the delivery: pattern correlated negatively with lead time (Spearman ρ = −0.505, p = 0.004) and with MTTR (ρ = −0.382, p = 0.037), but not significantly with deployment frequency. Mean lead time for pattern D teams was about a quarter of pattern A's (83.75 against 306.43 in the study's units), though the spread within each pattern was large and only 30 teams had full data.

Macarthy and Bass, interviewing 11 practitioners in nine organizations, found four *implementation modes* driven by the infrastructure: developers working with a classic operations team, developers alone in a pure cloud with no operations team, a DevOps team running the cloud and pipelines, and, the most common, a **DevOps bridge team** between developers and IT operations {% cite macarthy2020devops %}. They found DevOps to be both a culture and a job title.

## 2. A maturity path

Sethupathy's Lean-Agile DevOps Maturity Framework, built from 78 papers, rates six domains (deployment automation, telemetry, testing, build and release, security, architecture) on five levels from Novice to Expert {% cite sethupathy2025devops %}. In its literature base the most cited capabilities were continuous integration (79% of papers) and automated testing (74%).

**Example.** One of the three enterprises studied, a bank ("F-Bank"), moved from Intermediate to Advanced {% cite sethupathy2025devops %}:

| Measure | Before | After | Change |
|---|---|---|---|
| Deployments per week | 2.1 | 12.6 | 12.6 / 2.1 = 6.0× |
| Lead time (hours) | 23 | 4.1 | 82% shorter |
| MTTR (hours) | 4.8 | 3.0 | (4.8 − 3.0) / 4.8 = 37.5% shorter |
| Change failure rate | 9.1% | 5.3% | 3.8 points lower |

## When to use this — and when not

Move toward pattern C or D when the team owns a service it can deploy independently; the boosted pattern C is a practical way to get there without hiring a full team of specialists. A bridge team suits organizations with legacy infrastructure that an operations department must still run. Do not copy a pattern without the matching responsibility: a team that "runs" its service but cannot deploy it is pattern A with a new name.

## Limitations

- **Small samples.** 30 teams with full data in the pattern study, 11 interviewees in the taxonomy, three enterprises in the maturity framework.
- **Before-and-after, no control.** The bank's gains came with other changes over the same period; the study triangulates with interviews but has no comparison group.

## How solid is this?

- **Where it comes from.** A mixed-methods study of team structures, a grounded-theory taxonomy, and a maturity framework validated in three enterprises {% cite lopez2021devops macarthy2020devops sethupathy2025devops %}.
- **What is contested.** Whether a separate "DevOps team" is a step toward DevOps or a new silo; the studies above found it common and workable.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
