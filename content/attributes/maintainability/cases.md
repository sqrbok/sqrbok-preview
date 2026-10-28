---
title: Refactoring Case Studies
parent: Maintainability
grand_parent: Quality Attributes
nav_order: 9
layout: default
---

# Refactoring Case Studies

**Refactoring** in these studies means changing a system's structure to make later changes cheaper; four published cases measure what it bought, each with a different measure {% cite maccormack2006exploring kim2014refactoring wahler2016refactoring szoke2015refactoring %}.

## 1. Mozilla: a deliberate redesign

In late 1998, after Netscape released its browser code, the Mozilla team redesigned it. Propagation cost fell from 17.35% to 2.78%, 84% lower (the paper says a change could reach "80% fewer" files), and dependencies fell from 6,717 to 3,037 {% cite maccormack2006exploring %}. The details are on [Propagation Cost](propagation-cost.md).

## 2. Windows 7: refactoring at Microsoft

Kim et al. surveyed 328 Microsoft engineers and analysed the Windows 7 version history {% cite kim2014refactoring %}. 78% of the engineers defined refactoring as a change that improves readability, maintainability or performance, not only as a behaviour-preserving transformation, and 76% named regression bugs as a risk. In Windows 7, the top 5% of "preferentially refactored" modules, ranked much higher for refactoring than for ordinary commits, changed their inter-module dependencies by a factor of 0.85 of the average change per module, against 1.10 for the other modules, a ratio of 0.77. The refactored modules grew in lines of code even as some complexity measures fell, and their post-release defects fell only slightly more than the rest's: a change factor of 0.93 against 1.00, a 7% relative difference. The difference is small and confounded with other changes to the same modules, so refactoring alone did not explain the defect reduction in Windows 7.

## 3. Worked example: ABB's simulation tool

At ABB Corporate Research, DID was a physics simulation for designing magnetic components, about 30 kloc of Java grown by researchers without software-engineering training {% cite wahler2016refactoring %}. Adding a new magnetic component needed at least 59 changes in 17 existing classes, mostly copy and paste. The original developer refactored it, with software engineers spending about three hours a week for three months reviewing structure and answering questions. The work separated concerns, built a finer data model and added unit, integration and system tests.

| Time to add | Before | After | Less time |
|---|:-:|:-:|:-:|
| A magnetic component | 6–8 weeks | 1–2 weeks | 75% |
| A cooling method | 8–12 weeks | 1–2 weeks | 83% |

| Tool count | Before (15 kloc) | After (5 kloc) | Fewer per kloc |
|---|:-:|:-:|:-:|
| Duplicated code (PMD) | 5,647 lines | 338 lines | 82% |
| FindBugs warnings | 1,450 | 374 | 23% |

The refactored version did not yet have all features, so for the tool measurements the authors removed the GUI and file input-output code from the old version: 15 kloc of the old 30 kloc were compared with 5 kloc of the new version. The improvements are therefore per kloc. Duplicated lines fell from 5,647 / 15 = 376 to 338 / 5 = 68 per kloc, 82% fewer, while the raw count fell by 94% partly because the new code is smaller. FindBugs warnings fell from 1,450 / 15 = 97 to 374 / 5 = 75 per kloc, 23% fewer. The time figures are the developer's own estimates; the paper's 75% and 83% use the upper ends of the ranges (8 to 2, 12 to 2 weeks); midpoints give about 79% for a component.

## 4. Automatic refactoring at four companies

Szoke et al. had four companies apply almost 4,000 automatic refactorings, in more than 1,000 commits, to projects of about 5 million lines in total, mostly Java {% cite szoke2015refactoring %}. Measured with a maintainability model per refactoring type and project, 55% of the cases improved maintainability, 10% decreased it and 35% made no traceable difference. Results depended on the type. Removing unnecessary constructors, the dominant fix in one company, lowered the score every time: the deleted constructors were short, simple methods, so the distribution of method lengths shifted towards longer ones, and the model rates distributions, not size.

## 5. What the cases share

Each case targeted a known problem: coupling at Mozilla and Windows, duplication at ABB, rule violations in the automatic study. Each reported a different measure, so the cases cannot be ranked against each other. And where the measure was not structural, such as defects at Microsoft or the maintainability score in the automatic study, refactoring explained only part of the change.

## When to use it — and when not

Use these cases to argue for targeted refactoring, measured as planned. Do not quote one case's percentage as what refactoring delivers.

## Limitations

Each case is one organisation or one system. ABB's time figures are self-reported, and Microsoft's results come from one product's history.

## How solid is this?

- **Where it comes from.** A management science study of Mozilla and Linux, a Microsoft field study, an ABB industrial report and a four-company study {% cite maccormack2006exploring kim2014refactoring wahler2016refactoring szoke2015refactoring %}.
- **What is contested.** Whether a maintainability score captures what developers gain: in the Microsoft study, refactored modules grew in size while dependencies fell {% cite kim2014refactoring %}.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
