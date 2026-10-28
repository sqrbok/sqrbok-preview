---
title: Defect Seeding
parent: Inspection
nav_order: 4
layout: default
---

# Defect Seeding

**Defect seeding** (also *fault injection* or *error seeding*) estimates how many real defects a product contains by planting a known number of artificial defects, running the inspection or test as usual, and assuming the real defects were found at the same rate as the planted ones {% cite mills1972statistical %}. Harlan Mills proposed it at IBM in 1972.

## 1. The estimate

The method rests on one ratio: if inspectors found a fraction *F*<sub>P</sub> / *P* of the planted defects, they probably found the same fraction of the real ones. With

- *P* the number of defects planted,
- *F*<sub>P</sub> the number of planted defects found,
- *F*<sub>R</sub> the number of real (original) defects found, counting every logged real find, including those first raised in the meeting, because the ratio uses all finds ([capture-recapture](capture-recapture-practice.md) leaves them out),

the estimated number of real defects is

*N* = *F*<sub>R</sub> × *P* / *F*<sub>P</sub>.

## 2. Worked example

A moderator plants 12 major defects in a 40-page design document before an inspection. After minor issues are dropped, the team's log holds 15 major defects; checking against the moderator's list shows that 4 of them are planted and 11 are real.

| Quantity | Value |
|---|---|
| Defects planted, *P* | 12 |
| Planted defects found, *F*<sub>P</sub> | 4 |
| Real defects found, *F*<sub>R</sub> | 11 |
| Estimated real defects, *N* = 11 × 12 / 4 | 33 |
| Estimated real defects still in the document, 33 − 11 | 22 |

The inspection found a third of what was planted (4 of 12), so the estimate assumes it also found a third of the real defects. Two thirds are still in the document. On 40 pages, 22 / 40 = 0.55 major defects per page remain, more than twice an exit criterion of 0.25 per page {% cite malotaux2008onepage %}. The product should not pass this inspection.

## When to use it — and when not

Use seeding to measure how effective a team or a reading technique is (in experiments, in training, or to calibrate a new review process), and when too few independent inspectors are available for [capture-recapture](capture-recapture.md). Do not use it when the planted defects would be noticeably different from real ones: the estimate is only as good as that similarity.

## Limitations

- **Planted defects must be as hard to find as real ones.** Artificial defects tend to be simpler and more visible; then *F*<sub>P</sub> / *P* is too high and *N* too low.
- **Planting and removing cost effort,** and every seeded defect must be removed afterwards; one left behind is a real defect.
- **Inspectors who know about seeding behave differently,** for example hunting for planted-looking defects.
- **Few planted finds make the estimate unstable.** With 3 planted defects found instead of 4, the same data give *N* = 11 × 12 / 3 = 44.

## How solid is this?

- **Where it comes from.** Mills' IBM report introduced the idea for statistical validation of programs {% cite mills1972statistical %}.
- **What is contested.** Whether seeded defects can ever be representative; for inspections the usual alternative, which needs no planting, is [capture-recapture](capture-recapture.md).
- **What we do not hold.** The area's literature review has no empirical study of seeding accuracy in inspections.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
