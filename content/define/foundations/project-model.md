---
parent: Foundations
nav_order: 8
title: A Project Quality Model
layout: default
---

# A Project Quality Model

A **project quality model** is a table that lists, for one product, every quality characteristic the team keeps, the subcharacteristic that matters, the measure that stands for it and the threshold the product must meet, plus the characteristics the team drops and why. It is [ISO/IEC 25010](iso25010.md) cut down to one product, with [quality requirements](requirements.md) as its rows.

## 1. One row per kept characteristic

For example, a team building a food-delivery app walks the nine ISO/IEC 25010:2023 characteristics, keeps eight and drops one.

| Characteristic | Subcharacteristic | Measure | Threshold |
|---|---|---|---|
| Functional suitability | functional correctness | share of orders delivered with the items and price the customer confirmed | ≥ 99.5% |
| Performance efficiency | time behaviour | p95 time to load the menu on 4G | ≤ 2 s |
| Compatibility | interoperability | share of calls to the payment provider's API that are well-formed and accepted | ≥ 99.9% |
| Interaction capability | operability | taps from opening the app to a confirmed order, for a repeat order | ≤ 5 |
| Reliability | availability | share of minutes the order service is up, 11:00–14:00 | ≥ 99.9% per month |
| Security | confidentiality | card numbers stored by the app | 0: card data held only by the payment provider |
| Maintainability | modifiability | time from a critical defect report to the fix in the app stores | ≤ 24 h |
| Flexibility | adaptability | mobile platforms on which all of the above pass | current and previous major iOS and Android |
| Safety | dropped | the app controls nothing that can harm a person | — |

Each row becomes a requirement with the [template](requirements.md), for instance "While in production, the order service shall be available at least 99.9% of the minutes between 11:00 and 14:00, measured per month."

## 2. Choosing the measure and the threshold

Two choices in the table deserve a reason. The payment-API row sits under compatibility rather than reliability because it tests whether the two systems exchange messages correctly, not whether either one stays up. The interaction row counts taps, a property of the product that can be checked in a design review; how many first-time users finish without help is a [quality-in-use](quality-in-use.md) measure, taken with real users in their own context.

Thresholds come from the current baseline, competitors, what users expect, a service-level agreement, or the cost of the next nine: each extra 9 of availability costs more than the last.

## When to use it — and when not

- **Use** a project quality model at the start of a project, and revisit it when the product or its users change.
- **Do not** fill every cell for show: a characteristic with no requirement belongs in the dropped list, with its reason.

## Limitations

One measure per characteristic is thin. Maintainability and interaction capability usually need several weak indicators, and a single number invites the team to optimise it rather than the quality. The table also sets no priority when two rows conflict.

## How solid is this?

- **Where it comes from.** The structure is the ISO/IEC 25010:2023 product quality model {% cite iso25010_2023 %}; the food-delivery figures are a constructed example, not data from a real product.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
