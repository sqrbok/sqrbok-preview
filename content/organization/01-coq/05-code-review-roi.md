---
title: Value of Code Review
parent: Cost of Quality
nav_order: 6
layout: default
---

# Value of Code Review

**Code review** is an appraisal activity in which developers other than the author read a proposed change before it is merged; its value in cost-of-quality terms is the defects and future rework it removes, weighed against the reviewing effort it consumes {% cite turzo2023useful %}. The effort is large: developers spend 10–15% of their time on review, and between 20% and 44% of review comments are marked "not useful" by authors, according to the studies Turzo and Bosu cite {% cite turzo2023useful %}. Three studies of open-source communities show where the value lies.

## 1. What makes a comment useful

Turzo and Bosu classified review comments from the OpenDev community and surveyed its developers (160 usable responses). Comments that find **functional defects** were rated most useful, yet they were rare: 0.48% of the sampled comments, and the whole functional group (functional defects, validation, logic, interfaces, resources, timing) stayed under 20%. A third of all comments (33.32%) concerned documentation, rated in the middle {% cite turzo2023useful %}.

| Comment category | Share of comments | Average usefulness (1–5) |
|---|---:|---:|
| Functional defect | 0.48% | 4.38 |
| Validation | 3.68% | 4.16 |
| Logical | 2.28% | 4.11 |
| Documentation | 33.32% | 3.73 |
| Visual representation | 3.92% | 2.92 |

_Selected rows of Turzo and Bosu's Table 5._ {% cite turzo2023useful %}

Usefulness also depended on how comments were written (clear, respectful, concrete) and on who wrote them: a reviewer's coding experience was associated with more useful comments, while mutual "you review mine, I review yours" pairs were associated with less useful ones {% cite turzo2023useful %}.

## 2. Security defects in review

Yu et al. searched 432,585 review comments from the OpenStack and Qt communities; only 614 were about security, covering 539 security defects. The most frequent were race conditions (39.0%), crashes (22.8%) and resource leaks (10.9%). Of those 539 defects, 355 (65.9%) were resolved; when the reviewer proposed a specific solution the fix rate rose to 81.3%, against 59.5% when the reviewer only captured the problem. The main reasons for leaving a defect unresolved were "not worth fixing now" (45.4%) and disagreement between author and reviewer (34.0%) {% cite yu2023security %}.

## 3. Potential technical debt caught before merge

Fu et al. call a sub-optimal solution spotted during review **potential technical debt (PTD)**: debt that has not yet entered the code base. In the same two communities, 8.0% of review comments raised PTD, and reviewers (not the authors) raised 87.7% of it. Overall, 81.0% of PTD was resolved before merge, but the rate varied by type: test PTD 90.5%, code PTD 89.1%, requirement PTD only 16.7% {% cite fu2022ptd %}. What is not resolved becomes [technical debt](06-technical-debt.md).

## Example: raising the value of a review

A team's reviews produce mostly documentation and naming remarks. From these findings, three changes follow: assign reviewers with experience in the changed code, rotate reviewer pairs rather than letting the same two people swap reviews, and when a reviewer spots a defect, ask them to propose the fix, the condition under which Yu et al. saw the highest resolution rate. _(Constructed example.)_

## When to use it, and when not

- **Use these findings** to set review guidelines and to decide which review comments to track as defects or debt.
- **Do not read** the percentages as targets; they describe two open-source ecosystems, not your team.

## Limitations

- **Correlation, not cause.** The studies observe associations; a reviewer who proposes a fix may also choose easier defects.
- **Counts of comments are not costs.** None of the studies prices review time against the defects avoided.

## How solid is this?

- **Where it comes from.** Usefulness from Turzo and Bosu, security defects from Yu et al., PTD from Fu et al. {% cite turzo2023useful yu2023security fu2022ptd %}.
- **What is contested.** The 10–15% effort and 20–44% "not useful" figures come from the earlier studies Turzo and Bosu cite, not from their own data.
- **What we do not hold.** A controlled cost comparison of reviewed and unreviewed changes.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
