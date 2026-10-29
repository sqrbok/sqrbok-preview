---
title: Verification Methods per Quality Characteristic
parent: Quality Planning
nav_order: 2.5
layout: default
---

# Verification Methods per Quality Characteristic

A **method-by-characteristic matrix** records, for each ISO/IEC 25010 quality characteristic a plan keeps, which of the four verification methods (inspection, analysis, testing, demonstration) will supply the evidence that its requirements are met {% cite iso25010_2023 nasa2016sehandbook %}. It answers the plan's first decision, *which activities*, per characteristic rather than per phase, and it shows at a glance where a characteristic rests on one method alone.

## 1. The matrix

The methods are those of [Four Verification Methods](../../verif/overview/techniques.md): inspection is people reading the product, analysis is calculation or tool reasoning about it, testing runs it and measures, demonstration uses it and confirms a capability {% cite nasa2016sehandbook %}. The rows are the nine characteristics of ISO/IEC 25010:2023, where interaction capability was called usability and flexibility portability in the 2011 edition {% cite iso25010_2023 %}.

| Characteristic | Inspection | Analysis | Testing | Demonstration | Typical evidence |
|---|:-:|:-:|:-:|:-:|---|
| Functional suitability | ○ | ○ | ● | ● | unit and acceptance tests; acceptance session |
| Performance efficiency | ○ | ● | ● | ○ | queueing model; load test |
| Compatibility | ○ | ○ | ● | ○ | integration tests against the partner's API |
| Interaction capability | ○ | – | ○ | ● | sessions with real users |
| Reliability | ○ | ● | ● | ○ | reliability model; stress and fault-injection tests |
| Security | ○ | ● | ● | – | security analyzer; penetration test and fuzzing |
| Maintainability | ● | ● | ○ | – | code review; complexity and duplication metrics |
| Flexibility | ○ | ○ | ● | ○ | the test suite run on every target platform |
| Safety | ● | ● | ● | ○ | hazard review; fault-tree analysis; tests of safety functions |

● main method · ○ supporting method · – rarely useful.

**Every cell is a judgement, not a measured result.** No study we hold measures methods per ISO characteristic; choosing the method is engineering judgement {% cite nasa2016sehandbook %}. Three cells carry a stated reason. Poorly maintainable code usually runs exactly like good code, so reading and measuring the code are the main methods for maintainability; testing only supports them, because a regression suite is what makes a change safe and quick to verify. Security inspection is marked supporting on practice, not on effectiveness: of 432,585 review comments in OpenStack and Qt, only 614 were about security {% cite yu2023security %}. A review aimed at security, such as a threat-model review, can make it a main method.

## 2. Reading the matrix: defence in depth

No row rests on one method, and no method is the main one for every row. The methods also find different faults: at Nortel, static analysis found mostly checking and assignment faults, while testing and customers found mostly function and algorithm faults {% cite zheng2006static %}. A plan that keeps a characteristic should therefore name at least two methods for it, a main and a supporting one; where a row has a single ● in the plan, a reviewer should ask what the second line of defence is.

## Example: three requirements, three method mixes

_(Constructed example.)_ A food-delivery app's [project quality model](../../define/foundations/project-model.md) keeps three of its requirements for the first release:

| Requirement | Main method | Supporting method |
|---|---|---|
| p95 menu load ≤ 2 s on 4G (95% of loads within 2 s) | testing: a load test at lunchtime traffic | analysis: a queueing estimate of the menu service before it is built |
| repeat order in ≤ 5 taps | demonstration: five regular customers order while the team counts taps | inspection: a design review of the order flow |
| 0 card numbers stored by the app | analysis: a security analyzer and a schema search on every commit | inspection: review of every change touching payment code |

The plan then budgets each method once, not per requirement: the load-test environment, the user sessions and the analyzer serve several rows.

## Where the inputs come from

The rows come from the project quality model: one per characteristic the team keeps. The cells come from the team's own defect history, if it records the method that found each defect (the *where found* field of [Defect Origin and Detection](../01-coq/03-defect-classification.md)); a characteristic whose escaped defects were never caught by any planned method needs another method.

## When to use it, and when not

- **Use it** when writing the activities section of a [quality plan](01-quality-management-planning.md), and when reviewing one for gaps.
- **Do not use it** to choose between methods on cost; [Wagner's model](../01-coq/02b-wagner-model.md) does that per defect type.

## Limitations

The matrix says which kind of evidence fits, not how much of it is enough; strictness comes from [criticality and quality gates](03-quality-gates-criticality.md). Its cells also shift with the product: safety needs analysis only where the software can harm someone.

## How solid is this?

- **Where it comes from.** Method definitions from the NASA handbook, characteristics from ISO/IEC 25010:2023, fault-type differences from Zheng et al., review comments from Yu et al. {% cite nasa2016sehandbook iso25010_2023 zheng2006static yu2023security %}.
- **What is contested.** Every cell. Teams that call any run of the product a test merge the testing and demonstration columns.
- **What we do not hold.** A study that measures how well each method verifies each ISO characteristic.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
