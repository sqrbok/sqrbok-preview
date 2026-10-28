---
title: Estimating Test Effort
parent: Quality Planning
nav_order: 4
layout: default
---

# Estimating Test Effort

**Test effort estimation** predicts how many tests a project needs and how many hours designing, running and re-running them will take, so that the quality plan's seventh decision (cost and duration) rests on numbers rather than on what is left after development {% cite bluemke2021testeffort miranda2015asa %}.

## 1. Published methods

Bluemke and Malanowska's review found that the literature relies on two analytical methods {% cite bluemke2021testeffort %}:

- **Test Point Analysis (TPA)** estimates the hours for black-box testing from the system's size in function points, the test strategy (which quality characteristics are tested) and a productivity factor that turns test points into hours.
- **Use Case Points (UCP)**, adapted for testing, weights use cases and actors by type and complexity and converts the adjusted points into hours for test planning, writing and execution.

Many newer proposals, including machine-learning ones, modify one of these two. The review also warns that the body of good studies is small: fewer than 84 of the papers it found were valuable {% cite bluemke2021testeffort %}.

## 2. Back-of-the-envelope estimates

When no calibrated method is available, Miranda proposes estimating in five steps: classify each function or user story by complexity and criticality, estimate the tests each needs, find the mean, scale it to the whole life cycle, and multiply by the effort per test case {% cite miranda2015asa %}.

**White box.** Covering all basis paths of a piece of code needs n + 1 tests for n predicates, an upper bound for branch coverage. Code not yet written can be sized by its share of decisions: about 25% of lines in the SONAR database, so 1,000 lines suggest about 250 tests {% cite miranda2015asa %}.

**Black box.** Miranda assumes 2, 4 or 6 inputs for simple, uncertain and complex functions, two equivalence classes per input, and test techniques that grow with criticality: boundary values for non-critical functions, plus all-pairs for uncertain ones, plus combinatorial testing of strength 2 to 4 for critical ones. His resulting tests per function, which he tabulates rather than derives step by step {% cite miranda2015asa %}:

| Complexity | Non-critical | Uncertain | Critical |
|---|---:|---:|---:|
| Simple | 8 | 12 | 12 |
| Uncertain | 8 | 15 | 21 |
| Complex | 8 | 16 | 40 |

**Across the life cycle.** Total test cases = (1 + I + U) × the sum of tests per function, where 1 counts the system level, I is the extra fraction needed for integration (0 to 1) and U for unit testing (0 if unit tests are booked under development). Effort = total test cases × (design effort + (1 + R) × execution effort), where R is the fraction of re-runs {% cite miranda2015asa %}.

## Example: twenty functions

A system has 20 functions: 10 simple and non-critical (10 × 8 = 80 tests), 6 uncertain in both dimensions (6 × 15 = 90) and 4 complex and critical (4 × 40 = 160), so 330 system-level tests. Integration adds half again (I = 0.5) and unit tests are booked under development (U = 0): 1.5 × 330 = 495 test cases. With 0.5 hours to design a test, 0.25 hours to run it and every test run twice (R = 1), effort = 495 × (0.5 + 2 × 0.25) = 495 hours. _(Constructed example using Miranda's table.)_

**Sanity check.** Compare with an independent estimate. Jones' rule of thumb gives the number of test cases as function points raised to the power 1.2 {% cite miranda2015asa %}: if the 20 functions measure 150 function points (an assumed size), 150^1.2 ≈ 409 test cases, the same order of magnitude as 495. Ten tests, or 5,000, would signal an error in the assumptions.

## When to use it, and when not

- **Use it** early, to put testing into the schedule and budget before the first line of code, and again whenever the scope changes.
- **Do not present** the result as precise; every input is an assumption, and the value lies in making those assumptions visible.

## Limitations

- **The constants are local.** The 25% decision ratio and the tests per function are one data set and one teacher's choices; calibrate them from your own history.
- **Test counts are not effort.** One complex test can take a day to design, a simple one minutes.

## How solid is this?

- **Where it comes from.** The methods from Bluemke and Malanowska's review; the back-of-the-envelope model, table and Jones' rule as presented in Miranda's notes {% cite bluemke2021testeffort miranda2015asa %}.
- **What is contested.** Bluemke and Malanowska find the body of good studies small and some published methods untrustworthy; treat any estimate as approximate.
- **What we do not hold.** Jones' original sizing sources.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
