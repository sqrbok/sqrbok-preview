---
title: Criticality and Quality Gates
parent: Quality Planning
nav_order: 3
layout: default
---

# Criticality and Quality Gates

A **quality gate** is a predefined checkpoint in the life cycle where a work product must meet stated criteria before it may move on: **entry criteria** say when an activity may start, **exit criteria** when it is finished {% cite uzunova2024qualitygates miranda2015asa %}. How strict a gate is should depend on the **criticality** of what passes through it: the harm a failure would do to users, and the cost of recovering from it for the producer {% cite miranda2015asa %}.

![A gate between two phases: entry criteria admit work, the activity runs, exit criteria decide pass, which moves the work on, or fail, which sends it back](quality-gate.svg)

_A quality gate as entry and exit criteria around an activity._ {% cite uzunova2024qualitygates %}

## 1. Criticality levels from two standards

Regulated domains define criticality levels and tie verification effort to them. Avionics software is classified by the failure condition it could cause or contribute to {% cite miranda2015asa rtca1992do178b %}:

| DO-178B level | Failure condition | Structural coverage objective |
|---|---|---|
| A | catastrophic | modified condition/decision coverage (MC/DC) |
| B | hazardous or severe | decision coverage |
| C | major | statement coverage |
| D | minor | none required |
| E | no effect on aircraft or pilot workload | none required |

IEEE Std 829-2008 uses four **integrity levels**, 4 catastrophic, 3 critical, 2 marginal, 1 negligible, and scales the documentation with them: a master test plan is expected at levels 4 and 3 but not at 2 or 1, and level 1 needs level documents only for integration and system testing {% cite ieee829-2008 %}. The [coverage criteria](../../verif/coverage/index.md) named here are explained in the verification section.

## 2. Gates by criticality

Early in a project, nobody knows yet which modules will be critical. Miranda's advice is to write **unequivocal criteria per criticality class** now and assign modules to classes later, which avoids planning paralysis {% cite miranda2015asa %}. His example uses three classes, labelled A to C in rising criticality (not the DO-178B letters):

| Condition | Class A | Class B | Class C |
|---|---|---|---|
| Coverage | statement | branch | basis paths |
| Code inspections | no | yes | yes |
| Coding standards | yes | yes | yes |
| Combinatorial testing strength | all pairs | all triples | all quadruples |
| Robustness testing | data-input modules only | data-input modules only | all modules |
| Cyclomatic complexity | below 20 | below 20 | below 15 |

_Miranda's example of quality gates by criticality class._ {% cite miranda2015asa %}

## 3. Writing a gate criterion

Uzunova et al. recommend documenting each criterion with four parts: the **metric**, the **desired value**, the **predicate** that compares them, and the **object** of measurement. Their example: cyclomatic complexity, below 10, for any single method, measured by a static analysis tool; a method at 10 or above fails the gate and triggers review and refactoring {% cite uzunova2024qualitygates %}. Tools such as SonarQube can enforce such criteria automatically and block a build that fails them {% cite uzunova2024qualitygates %}.

## Example: phasing a gate over milestones

A criterion need not be met at once. Microsoft raised its code-coverage gate over a product's milestones, 65% at the second milestone, 75% at the third and 80% at release {% cite page2009microsoft %}. The gate grows with the product, which is how phasing implements "good enough" quality within scope, time and cost {% cite miranda2015asa %}.

## When to use it, and when not

- **Use gates** at every hand-over the team cares about: merge, integration, release. Write each criterion with all four parts so that two people reach the same verdict.
- **Do not copy** another domain's levels; a web application is not avionics. Choose classes that match what your failures cost.

## Limitations

- **A gate measures what it measures.** 80% coverage can be reached with weak tests; pair coverage criteria with reviews.
- **Gates invite gaming** when people are rewarded for passing them rather than for the quality they stand for.

## How solid is this?

- **Where it comes from.** Gate definition and documentation from Uzunova et al.; DO-178B levels from Miranda's notes and RTCA; integrity levels from IEEE 829; the class table from Miranda; Microsoft's milestones from Page et al. {% cite uzunova2024qualitygates miranda2015asa rtca1992do178b ieee829-2008 page2009microsoft %}.
- **What is contested.** The thresholds in Miranda's table are one teacher's example, not a standard.
- **What we do not hold.** The DO-178B text itself; the coverage objectives are taken from its widely documented Table A-7.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
