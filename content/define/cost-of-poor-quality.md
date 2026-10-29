---
title: Cost of Poor Software Quality
parent: Quality
nav_order: 1.7
layout: default
---

# Cost of Poor Software Quality

The **cost of poor software quality (CPSQ)** is what deficient software costs those who build, run and use it: failures in operation, cancelled projects, struggling legacy systems and the effort of finding and fixing defects. For the United States in 2022, the Consortium for Information and Software Quality (CISQ) put it at **at least US$2.41 trillion** {% cite krasner2022cpsq %}.

## 1. The headline figures

Herb Krasner's report for CISQ is the third in a series, after 2018 and 2020 {% cite krasner2022cpsq %}. Its 2022 estimate has four overlapping areas, plus technical debt drawn partly outside the total:

- **Operational failures**, US$1.81 trillion: software failing in use, including outages and data loss. Cybercrime that exploits software vulnerabilities accounts for about US$1.44 trillion of this, roughly 80%.
- **Finding and fixing defects**, US$607 billion.
- **Legacy system problems**, US$520 billion.
- **Unsuccessful development projects**, US$260 billion.
- **Technical debt**, about US$1.52 trillion: the accumulated effort it would take to fix known deficiencies in existing code (the "principal"). It is a stock built up over years, not a yearly cost.

![Overlapping areas inside a large circle labelled at least 2.41 trillion: operational failures 1.81 trillion containing cybercrime 1.44 trillion, finding and fixing defects 607 billion, legacy systems 520 billion, unsuccessful projects 260 billion; technical debt 1.52 trillion overlaps the circle from outside](cpsq-2022.svg)

_The cost of poor software quality in the US in 2022, redrawn from the report's Figure 1-0. The areas overlap, so they cannot be added._ {% cite krasner2022cpsq %}

The areas share costs: the four inside the circle add up to US$3.2 trillion, more than the US$2.41 trillion total. Quote the total, and quote a part only as "of which roughly".

## 2. How the number was built

The figure is an extrapolation, not a measurement. The 2020 report combined 88 published sources with expert judgement and arrived at US$2.08 trillion {% cite krasner2022cpsq %}. For 2022, Krasner raised the 2020 operational-failure figure of US$1.56 trillion by the 15% inflation of the two years, giving about US$1.8 trillion (US$1.81 trillion in the report's figure). Cybercrime was estimated by taking the US share of world GDP, 24%, of a global estimate of US$6–7 trillion. All other areas were assumed not to have grown. The report does not show how these parts combine into its 2022 total: raising operational failures alone, to US$1.81 trillion, would give about US$2.33 trillion, and the remaining US$0.08 trillion is not explained.

The arithmetic shows what the headline hides. From US$2.08 trillion to US$2.41 trillion is a rise of 16% in two years, close to the 15% inflation over the same period, so in real terms the estimate barely moved. Even so it is large: about 10% of the projected US GDP of US$23.35 trillion, and more than the US$1.51 trillion the country spent on its entire IT workforce.

## 3. Example: measuring your own share

The report's advice is that an organization should measure its own cost rather than quote the national one, and it proposes a simple log: for each bug, record the hours spent finding it, reproducing it, writing a test, finding the root cause, fixing it, proving the fix and releasing it {% cite krasner2022cpsq %}. Suppose a team of eight logs 40 bugs in a month, at an average of 6 hours each. That is 240 hours, about one and a half person-months (at about 160 working hours per person-month), or roughly a fifth of the team's capacity, spent on finding and fixing alone. The [Cost of Software Quality](../organization/01-coq/01-cosq-foundations.md) page shows how to sort such costs into prevention, appraisal and failure.

## When to use it — and when not

- **Use** the national figure to show that software quality is an economic problem, not only a technical one.
- **Do not** use it to justify a particular project's budget: it says nothing about your system. Use your own logged costs instead, and see [Cost of Late Fixes](cost-of-late-fixes.md) for when catching a defect earlier pays.

## How solid is this?

- **Where it comes from.** A report by an industry consortium, not a peer-reviewed study {% cite krasner2022cpsq %}. An earlier national estimate, NIST's for 2002, is much smaller, at US$59.5 billion a year, but it counted only the cost of inadequate testing infrastructure {% cite tassey2002economic %}.
- **What is contested.** Most of the total is cybercrime, and that part rests on a global estimate scaled by GDP rather than on US loss data. The report calls its figures conservative, and also notes that many organizations do not know their own failure costs: in one survey, 35% of IT executives said they had no idea.
- **What we do not hold.** The 2018 and 2020 reports and the 88 sources behind them, so the parts of the estimate carried over from 2020 cannot be checked here.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
