---
title: CMMI Adoption & Integration
parent: Quality Standards
nav_order: 2
layout: default
---

# CMMI Adoption & Integration

**CMMI adoption** is the organizational project of reaching a maturity level (usually 2 or 3) and passing an appraisal; today it is mostly combined with Agile methods, which raises the question of how much of [CMMI](01-cmmi-overview.md) Agile practices already cover {% cite astridita2024cmmi ferdinansyah2021cmmi %}.

## 1. How far Agile gets you

A review of seven primary studies on combining Agile and CMMI concludes that the two fit well at the project level and poorly at the organization level {% cite ferdinansyah2021cmmi %}:

| Maturity level | Fit with Agile alone | Why |
|---|---|---|
| 2 Managed | fully compatible, apart from configuration management (below) | its process areas work at project level, with no organization-level ones, and Scrum-style practices cover most of them |
| 3 Defined | partially compatible | organization-wide process areas (standard processes, training) have no Agile counterpart |
| 4–5 | not achievable with Agile alone | Agile has no equivalent of statistical process management |

Configuration management is the clearest gap at Level 2: Agile methods do not say how to preserve and baseline work products, so teams add explicit practices for it. The review found lack of knowledge of CMMI or Agile, and organizational culture, to be the most frequent challenges.

## 2. Reported benefits

A systematic review of 23 studies on CMMI v2.0 with Agile project management reports better estimation, planning, risk management and requirements validation {% cite astridita2024cmmi %}. The figures usually quoted with it, a **17% increase in estimation accuracy**, a **70% reduction in rework** and **97% on-time delivery**, are the CMMI Institute's own numbers as relayed by another author, not results measured in the review.

## 3. Example: what culture does to an appraisal

Tsai studied a Taiwanese information-service company's project teams as they implemented CMMI Level 3 {% cite tsai2020cmmi %}. Success went with a strong team culture along six dimensions, among them teams oriented toward reform, concentrated decision-making (which suits Level 3's organization-wide processes), shared ownership, and rewards given to the group rather than to individuals, which prevented members from competing. Teams that built their own subculture could overcome obstacles in the wider organization.

A mixed-methods study of 24 practitioners in Bangalore IT companies at Level 3 shows the other side {% cite shelat2025cmmi %}: 42% of interview responses named employee resistance as a major challenge, 29% the tension between standard processes and client-driven flexibility, and 25% insufficient training. At the same time, 37% reported improved software quality and 29% higher employee engagement. The authors recommend combining CMMI with Scrum, SAFe (the Scaled Agile Framework) or Lean, rolling it out through pilot groups, and training by role rather than in general.

## When to use it — and when not

Pursue a maturity level when customers or regulators require an appraisal, or when an organization of several teams needs common processes (Level 3). An Agile team that does not need an appraisal can borrow single process areas, configuration management or measurement, without adopting the model as a whole.

## Limitations

- **Small samples.** The Bangalore study has 24 participants, the Agile–CMMI review seven primary studies {% cite shelat2025cmmi ferdinansyah2021cmmi %}.
- **Reported benefits come with self-selection:** organizations that completed an appraisal report on it; those that gave up rarely publish {% cite kuhrmann2016spi %}.
- **Percentages are not exclusive.** Respondents could name several challenges, so the figures above do not add up to 100%.

## How solid is this?

- **Where it comes from.** Two systematic reviews of Agile–CMMI integration, one case study and one mixed-methods study {% cite astridita2024cmmi ferdinansyah2021cmmi tsai2020cmmi shelat2025cmmi %}.
- **What is contested.** The 17%, 70% and 97% figures trace back to the CMMI Institute, which sells the model and its appraisals.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
