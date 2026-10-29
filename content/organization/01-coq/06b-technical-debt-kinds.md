---
title: Kinds of Technical Debt
parent: Cost of Quality
nav_order: 7.5
layout: default
---

# Kinds of Technical Debt

**McConnell's taxonomy** sorts technical debt by how it was taken on: **unintentional** debt is the by-product of poor work, while **intentional** debt is a conscious choice to optimise for the present, either **short-term** (tactical, repaid next release) or **long-term** (strategic, carried for years) {% cite mcconnell2007technicaldebt mcconnell2008managing %}. The kind tells a business decision from poor work.

How the resulting costs are booked does not change with the kind. As on [Technical Debt as a Quality Cost](06-technical-debt.md), the principal repaid and the interest paid while developing are internal failure, an incident that users meet is external failure, and a review that keeps a shortcut out is prevention.

## 1. The taxonomy

McConnell's summary of the kinds, with one of his examples each {% cite mcconnell2008managing %}:

| Type | Kind | How it arises | Example |
|---|---|---|---|
| I | Unintentional | low-quality work, or debt bought unknowingly | a junior programmer writes error-prone code; an acquired company's code base turns out to be debt-laden |
| II.A.1 | Intentional, focused short-term | one identifiable shortcut, "like a car loan" | glue code keeps two databases in sync until they are reconciled after the release |
| II.A.2 | Intentional, unfocused short-term | many tiny shortcuts, "like credit card debt" | generic variable names, sparse comments, ignored coding conventions |
| II.B | Intentional, long-term | proactive, strategic | "we will support only one platform for at least five years" |

Delayed work is not always debt. A feature backlog, deferred features and cut features charge no interest, so they are not debt {% cite mcconnell2008managing %}. Type II.A.2 does not pay off even within the first release and should be avoided; a useful team rule is that a shortcut too small to enter in the debt list is too small to take {% cite mcconnell2007technicaldebt %}.

## 2. Why take on debt deliberately

Strategic debt always rests on one judgement: the work costs more today than it will later. McConnell names three reasons this can be true {% cite mcconnell2007technicaldebt %}. Each is listed with its example:

- **Time to market.** If an extra $1 of development now costs $10 of lost revenue, taking the debt is sound even if the same work costs $5 later.
- **Preserving startup capital.** A startup pays the expense a year or two later, out of more money.
- **Retirement wipes the debt.** When a system is retired, its debt is retired with it, so near the end of its service life the expedient solution is hard to beat.

## Example: three options for one shortcut

An application needs five reports. The good path, custom report classes, costs 10 staff days. A quick-and-dirty path costs 2 days now, 12 to retrofit the good solution later, and 0.5 staff days a month of interest until then. A third path, quick but isolated from the rest of the code, costs 3 days now and 12 later, with no interest. At $600 a staff day, and with the retrofit within six months, McConnell puts the interest at $600–$1,800, that is $300 a month for two to six months {% cite mcconnell2008managing %}:

| Option | Cost now | Interest | Cost later | Lifetime cost |
|---|---:|---:|---:|---:|
| Good path | $6,000 | $0 | $0 | $6,000 |
| Quick and dirty | $1,200 | $600–$1,800 | $7,200 | $9,000–$10,200 |
| Quick but not dirty | $1,800 | $0 | $7,200 | $9,000 |

Both shortcuts are focused short-term debt (II.A.1), a "car loan": one identifiable shortcut with a known retrofit. They pay only when a reason from §2 holds. The isolated one charges no interest, but then nothing urges repayment.

## Where the inputs come from

McConnell reports teams keeping a debt list in the defect tracker or product backlog, with the effort to repay each item; one treats unresolved debt older than 90 days as critical {% cite mcconnell2007technicaldebt %}. A type label on each item shows how much debt was chosen.

## When to use it, and when not

- **Use the taxonomy** before a release decision: ask whether a shortcut is focused, isolatable and justified by one of the three reasons.
- **Do not use it** to excuse type I debt after the fact: calling poor work "strategic" does not make it so.

## Limitations

The types blur: McConnell notes that unfocused short-term debt is easy to incur unintentionally. The taxonomy also says nothing about how large the interest is; the [main debt page](06-technical-debt.md) shows how tools estimate the principal instead.

## How solid is this?

- **Where it comes from.** A practitioner blog post (2007) and white paper (2008) by Steve McConnell, not a study {% cite mcconnell2007technicaldebt mcconnell2008managing %}.
- **What is contested.** The dollar figures ($1 against $10; the $600 staff day) are illustrations, not measurements.
- **What we do not hold.** Fowler's technical-debt quadrant, often taught alongside this taxonomy, and any study measuring how much debt of each type real teams carry.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
