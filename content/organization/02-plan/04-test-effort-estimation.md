---
title: Estimating Test Effort
parent: Quality Planning
nav_order: 4
layout: default
---

# Estimating Test Effort

**Test effort estimation** predicts how many tests a project needs and how many hours designing, running and re-running them will take, so that the quality plan's seventh decision (cost and duration) rests on numbers, not on leftover time {% cite bluemke2021testeffort miranda2015asa %}.

## 1. Published methods

Bluemke and Malanowska's review finds two analytical methods in the literature {% cite bluemke2021testeffort %}:

- **Test Point Analysis (TPA)** estimates black-box testing hours from **function points** (a size measure that counts a system's inputs, outputs, queries, files and interfaces), the test strategy and a productivity factor.
- **Use Case Points (UCP)**, adapted for testing, weights use cases and actors by complexity and converts the points into hours.

Most newer proposals modify one of them; fewer than 84 of the papers found were valuable {% cite bluemke2021testeffort %}.

## 2. Back-of-the-envelope estimates

Miranda estimates in five steps: classify each function by complexity and criticality, estimate its tests, find the mean, scale to the life cycle, and multiply by effort per test {% cite miranda2015asa %}.

**White box.** All basis paths of code with n predicates need n + 1 tests, an upper bound for branch coverage. Code not yet written can be sized by its share of decisions: about 25% of lines in the SONAR database, so 1,000 lines suggest about 250 tests {% cite miranda2015asa %}.

**Black box.** Miranda classes each function by complexity (simple, medium, complex, assumed to have 2, 4 or 6 inputs) and by criticality (non-critical, uncertain, critical), with two equivalence classes per input. The cells come from three formulas in his notes {% cite miranda2015asa %}:

- **Non-critical functions get boundary values only.** His count, 2 + 3 × 2 equivalence classes = 8 (our reading: two out-of-range values plus three values per class), has no input term, so every non-critical function gets 8.
- **Uncertain criticality adds all-pairs tests.** He estimates them as 4 ln n + 1 for n inputs, rounded: 4, 7 and 8 for 2, 4 and 6 inputs, giving 12, 15 and 16.
- **Critical functions add combinatorial tests** whose strength rises with complexity: pairs, triples and quadruples. The notes give 4, 13 and 32 such tests without a derivation, giving 12, 21 and 40.

| Complexity | Non-critical | Uncertain criticality | Critical |
|---|---:|---:|---:|
| Simple (2 inputs) | 8 | 12 | 12 |
| Medium (4 inputs) | 8 | 15 | 21 |
| Complex (6 inputs) | 8 | 16 | 40 |

The table is Miranda's assumption set ("you can pick your own numbers"), not measured data, and is used as printed: it is for estimating only, and the quality gate of a function does not change its column. Outside the table, say at 10 inputs, the formulas extend only to non-critical (8) and uncertain functions (8 + 4 ln 10 + 1 ≈ 18), our judgement. For a critical function outside the table, use judgement or the count a combinatorial tool such as PICT generates.

**Across the life cycle.** Total test cases = (1 + I + U) × the sum of tests per function, where 1 counts the system level, I the extra fraction for integration and U for unit testing (0 if booked under development). Effort = total test cases × (design effort + (1 + R) × execution effort), where R is the fraction of re-runs {% cite miranda2015asa %}.

## Example: twenty functions

A system has 20 functions: 10 simple and non-critical (10 × 8 = 80 tests), 6 of medium complexity and uncertain criticality (6 × 15 = 90) and 4 complex and critical (4 × 40 = 160), so 330 system-level tests. Integration adds half again (I = 0.5) and unit tests are booked under development (U = 0): 1.5 × 330 = 495 test cases. With 0.5 hours to design a test, 0.25 hours to run it and every test run twice (R = 1), effort = 495 × (0.5 + 2 × 0.25) = 495 hours. _(Constructed example using Miranda's table.)_

**Sanity check.** Jones' rule of thumb gives the number of test cases as function points raised to the power 1.2 {% cite miranda2015asa %}: if the 20 functions measure 150 function points (an assumed size), 150^1.2 ≈ 409 test cases, the same order of magnitude as 495. An estimate within about a factor of three passes; ten tests, or 5,000, would signal an error.

## When to use it, and when not

- **Use it** early, to put testing into the schedule before coding, and when the scope changes.
- **Do not present** the result as precise; its value is in the visible assumptions.

## Limitations

- **The constants are local.** The 25% ratio and the tests per function are one data set and one teacher's choices; calibrate them.
- **Test counts are not effort.** One test can take a day to design, another minutes.

## How solid is this?

- **Where it comes from.** Methods from Bluemke and Malanowska; the model, table and Jones' rule from Miranda's notes {% cite bluemke2021testeffort miranda2015asa %}.
- **What is contested.** Few good studies exist; treat any estimate as approximate.
- **What we do not hold.** Jones' original sizing sources.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
