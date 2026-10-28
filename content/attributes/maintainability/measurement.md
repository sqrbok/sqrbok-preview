---
title: Measuring Maintainability
parent: Maintainability
grand_parent: Quality Attributes
nav_order: 2
layout: default
page_type: deep-dive  # the SIG model with a worked profile, and SQALE with a worked ratio
---

# Measuring Maintainability

**Measuring maintainability** means rating properties of the source code that make changes cheap or expensive; the SIG model rates eight such properties against a benchmark of real systems, and SQALE prices the work needed to fix what falls short {% cite sig2016criteria letouzey2012sqale %}.

## 1. The SIG model: eight properties

The Software Improvement Group (SIG) rates eight source code properties, chosen to be largely independent of each other {% cite sig2016criteria %}: volume, duplication, unit size, unit complexity (a **unit** is the smallest piece of code that can be executed and tested on its own, such as a method or function), unit interfacing (parameters per unit), module coupling (incoming dependencies per module, where a SIG module is a grouping of related units in the code, such as a class or file, not a design-level module; pydeps's module is a file), component balance (number and size spread of top-level components, a component being a top-level grouping of modules) and component independence (share of code with no incoming calls from other components). Each property feeds the ISO/IEC 25010 sub-characteristics it affects:

| Property | Analysability | Modifiability | Testability | Modularity | Reusability |
|---|:-:|:-:|:-:|:-:|:-:|
| Volume | X | | X | | |
| Duplication | X | X | | | |
| Unit size | X | | | | X |
| Unit complexity | | X | X | | |
| Unit interfacing | | | | | X |
| Module coupling | | X | | X | |
| Component balance | X | | | X | |
| Component independence | | | X | X | |

Every rating is 1 to 5 stars, calibrated on SIG's benchmark so that 5% of systems get 5 stars, 30% get 4, 30% get 3, 30% get 2 and 5% get 1 star. A certificate needs at least 3 stars overall and at least 2 for every sub-characteristic {% cite sig2016criteria %}.

## 2. Worked example: rating unit complexity with a quality profile

SIG does not average unit complexity. It sorts every unit into a risk category by its cyclomatic complexity. It then adds up the lines of code in each category and compares each category's share of all lines with a maximum {% cite baggen2012standardized %}. The shares are per category, not cumulative:

| Rating | Moderate (CC 11–20), at most | High (CC 21–50), at most | Very high (CC above 50), at most |
|---|:-:|:-:|:-:|
| 5 stars | 25% | 0% | 0% |
| 4 stars | 30% | 5% | 0% |
| 3 stars | 40% | 10% | 0% |
| 2 stars | 50% | 15% | 5% |
| 1 star | anything worse | | |

Units with CC 1–10 are low risk and fill the rest. Suppose a system's code splits 70% low, 20% moderate, 8% high and 2% very high. Five stars fails on high (8% > 0%), four stars fails on high (8% > 5%), three stars fails on very high (2% > 0%), and two stars holds: **2 stars**. The 2% of very complex code decides the rating, while an average over all units would barely move. Duplication is rated more simply, by thresholds of 3%, 5%, 10% and 20% duplicated code for 5 to 2 stars, counting fragments longer than 6 lines; in Python, `pylint --disable=all --enable=duplicate-code --min-similarity-lines=7 mypackage` reports such blocks (tested with pylint 4.1.3); the duplication share is the sum of the reported block lengths divided by all lines of code. Ratings are interpolated on a continuous scale from 0.5 to 5.5, so n stars means a value from n − 0.5 up to n + 0.5; the exact interpolation cannot be reproduced without SIG's benchmark data {% cite baggen2012standardized %}. Each sub-characteristic's rating is then the average of the ratings of the properties mapped to it, and the sub-characteristics combine into one maintainability rating {% cite baggen2012standardized %}. So SIG never averages measurements of units, which would hide the worst ones, but does average star ratings, which are already judgements on whole profiles.

The ratings predict maintenance work: in SIG's validation, issues in 4-star systems were resolved 3 times faster than in 2-star systems {% cite baggen2012standardized %}.

## 3. SQALE: pricing the debt

SQALE turns each rule violation found by static analysis into a remediation cost, and rates the code by the **technical debt ratio**, remediation cost divided by development cost {% cite letouzey2012sqale %}. Costs are in **work units**, an effort unit the user chooses, such as hours; the method's example assumes 1,000 lines cost 100 work units to develop {% cite letouzey2012sqale %}. So 5,000 lines cost 500 work units, and if fixing their violations costs 15, the ratio is 3%. The rating grid is a choice. On the grid in the SQALE method (A up to 1%, B 2%, C 4%, D 8%, E above) the code is rated C {% cite letouzey2012sqale %}. Applied to the same 3% ratio, SonarQube's default grid (A up to 5%, B under 10%, C under 20%, D under 50%) rates the code A {% cite sonarsource_metrics molnar2020study %}.

## 4. And the Maintainability Index?

The Maintainability Index (MI) combines Halstead volume, cyclomatic complexity and lines of code into one number; Coleman et al. proposed cut-offs of 85 and 65 on its 0–171 scale {% cite coleman1994using %}. Formula on the radon page, [Rating Python Code with Radon](radon.md); the inputs are defined on the [Maintainability Index](../../define/measurement/maintainability-index.md) page under Measurement. Letter grades are not comparable: radon's CC letters, radon's MI letters, SQALE's and SonarQube's each use their own bands. How radon computes and grades it, and how its output gives the unit-complexity and unit-size profiles (not the other six properties), is on [Rating Python Code with Radon](radon.md). Across three open-source projects, Molnar and Motogna found MI influenced by system size, while SQALE was not; at class level MI and technical debt had a Spearman rank correlation of ρ ≈ −0.6 {% cite molnar2020study %}. It is negative because a high MI means easy to maintain and high debt means hard; 0.6 is moderate by the usual rule of thumb, which calls values between about 0.4 and 0.7 moderate, so the two measures overlap but do not replace each other.

## When to use it — and when not

Use SIG profiles to compare systems or track one over time, and SQALE to prioritise fixes by cost. Do not compare SQALE letters across tools without checking the grid, and do not read any of these ratings as a measure of architecture: they see units and files, not the dependency structure.

## Limitations

SIG's thresholds come from its own benchmark, which outsiders cannot inspect. SQALE costs are estimates per rule, set by the tool vendor.

## How solid is this?

- **Where it comes from.** SIG's evaluation criteria and its calibration method {% cite sig2016criteria baggen2012standardized %}; the SQALE method definition {% cite letouzey2012sqale %}.
- **What is contested.** Which metrics matter at all: a systematic review found 174 maintainability metrics, most used once {% cite ardito2020maintainability %}.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
