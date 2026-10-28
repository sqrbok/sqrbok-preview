---
title: V&V Activities by Phase
parent: Quality Planning
nav_order: 2
layout: default
---

# V&V Activities by Phase

**Verification and validation (V&V)** checks each work product of a project as it is produced: verification asks whether a product correctly implements its predecessor (code against design, design against requirements), validation whether the product meets the user's needs. Wallace and Fujii define a **minimum set of V&V tasks** for every life-cycle phase, which a quality plan can take as its starting list {% cite wallace1989verification %}.

## 1. The minimum tasks

| Phase | Tasks | Key issues |
|---|---|---|
| Concept | concept-documentation evaluation | user needs, constraints of interfacing systems |
| Requirements | traceability analysis; requirements validation; interface analysis; begin planning system and acceptance testing | requirements trace to the concept; correctness, consistency, completeness, testability; hardware, software and operator interfaces |
| Design | traceability analysis; design evaluation; interface analysis; begin planning component and integration testing | design traces to requirements; design quality; data across interfaces; timing and performance at stress limits |
| Implementation | traceability analysis; code evaluation; interface analysis; component test execution | code traces to design; code quality; component integrity |
| Test | integration, system and acceptance test execution | subsystem interfaces; the whole system at limits and user stress; performance with operational scenarios |
| Installation and checkout | installation-configuration audit; final V&V report | operation with site dependencies |

_Wallace and Fujii's Figure 2, minimum recommended V&V tasks._ {% cite wallace1989verification %}

Two patterns stand out. **Test planning starts early**: system and acceptance tests are planned during requirements, component and integration tests during design. **Traceability runs through every phase**: each product is traced back to the one before it.

## 2. Scaling and organizing V&V

The minimum is extended for critical software. Wallace and Fujii recommend a **criticality analysis**, ranking functions by how severe the consequences of their failure would be, to find the most critical functions and concentrating extra V&V there, because, as Boehm and Papaccio observed, about 20% of the problems cause 80% of the rework cost {% cite wallace1989verification %}. V&V can be organized in four ways: as an independent organization, inside the development systems-engineering group, inside the development quality-assurance group, or in the user organization {% cite wallace1989verification %}.

## Example: two studies, two verdicts

Wallace and Fujii report two studies of independent V&V {% cite wallace1989verification %}:

- On three small flight-dynamics projects (10,000 to 50,000 lines), McGarry found V&V not effective: it added 10–30% to project cost, and development productivity was the lowest of any monitored project.
- On four larger projects, Radatz found 50–89% of errors detected before development testing began, and savings from early detection of 92–180% of the V&V cost for the two projects whose V&V started at requirements, against 20–28% for the two that started at code.

The lesson for a plan: V&V pays most when it starts early and when the project is large or critical enough for errors to be expensive.

## 3. Test process models

A plan also places the team on a scale of test-process maturity. Hrabovská et al. reviewed 17 testing process models; most define maturity levels {% cite hrabovska2019testing %}. TMMi (Test Maturity Model integration), for example, the successor of TMM and a companion to CMMI, has five maturity levels: **Initial, Managed, Defined, Measured, Optimization**. Every level above the first adds process areas, each with goals and the practices that achieve them {% cite hrabovska2019testing %}.

A team need not aim for Level 5; the plan should state which level's practices it commits to.

## When to use it, and when not

- **Use the task list** as a checklist when filling in the plan's first decision (activities) for each phase.
- **Do not apply it literally** to an iterative project: the tasks remain, but they recur in every iteration instead of happening once per phase.

## Limitations

- **The task list dates from 1989** and assumes a waterfall life cycle.
- **The two V&V studies are not comparable**, as Wallace and Fujii note: different data, sizes and measures.

## How solid is this?

- **Where it comes from.** The task list, criticality advice and studies from Wallace and Fujii; the process models from Hrabovská et al.'s review {% cite wallace1989verification hrabovska2019testing %}.
- **What is contested.** Whether independent V&V is worth its cost; the two studies above disagree.
- **What we do not hold.** Recent data on V&V cost-effectiveness in iterative projects.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
