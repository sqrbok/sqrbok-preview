---
title: "RQ L00: Course Introduction and CI"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 0.2
layout: default
page_type: question-set
---

# RQ L00: Course Introduction and CI

**Format:** Explain in your own words, give brief examples. These questions test the lecture summarised in [SN L00: Course Introduction and CI](SN_L00_Intro_CICD.md).

---

## Question 1: Naming the Quality That Failed

For each incident, name the quality characteristic that failed and say in one sentence why:

- the CrowdStrike Falcon update of July 2024;
- the Knight Capital deployment of 2012;
- the Log4j vulnerability of December 2021.

---

## Question 2: From Incident to Requirement

Why is it useful to name the failed quality characteristic, rather than just saying "the software was buggy"? Use one of the incidents in Question 1 to show the step from incident to a checkable requirement.

---

## Question 3: The National Cost Estimate

The cost of poor software quality in the US in 2022 was estimated at "at least US$2.41 trillion".

1. Name three of the areas that make up the estimate.
2. Why can the areas not simply be added to get the total?
3. The 2020 estimate was US$2.08 trillion. Compute the rise in percent and compare it with the 15% inflation over the same two years. What does the comparison suggest?

---

## Question 4: Your Own Cost of Poor Quality

A team of five logs 25 bugs in a month, at an average of 4 hours each to find, fix and release. How many hours is that, and what share of the team's capacity, assuming 160 working hours per person per month? Why is this number more useful to the team than the national estimate?

---

## Question 5: Defect Density

1. Define defect density.
2. A library of 8,000 lines of code had 20 defects reported by users in its first year. Compute its post-release defect density and compare it with the median of the 109-project benchmark.
3. Why is the benchmark median a better "typical value" than its mean of 7.47?
4. Give two reasons why comparing your project's figure with the benchmark can mislead.

---

## Question 6: Continuous Integration

Define continuous integration in one sentence. Then list three of its practices and explain what goes wrong if a team skips each one.

---

## Question 7: Delivery or Deployment?

What is the difference between continuous delivery and continuous deployment? Why do both depend on CI?

---

## Question 8: The Red Build

A teammate pushes a change and the CI build turns red ten minutes before you planned to push your own work.

1. What should happen first, according to CI practice?
2. Why is reverting the faulty commit often the quickest fix?
3. Why should you not push your change on top of the red build?

---

## Question 9: Reading a Workflow

A GitHub Actions workflow runs these steps on every push: check out the code, set up Python 3.12, `pip install -r requirements.txt`, `ruff check .`, `pytest`.

1. Which steps are quality gates, and what does each one check?
2. The test step fails. Is the build green or red, and what does the author see?
3. Name one check the workflow does not perform that a later stage of a pipeline might add.

---

## Question 10: Does CI Make Projects Better?

In the GitHub study, projects using CI released 0.54 times a month, projects without CI 0.24 times, and the CI projects had released 0.34 times a month before they adopted CI. What can you conclude about the effect of CI, and what can you not conclude?

---

## Question 11: The Test Pyramid

1. Sketch the test pyramid and label its three layers.
2. Google's book recommends 80/15/5; the lecture slide shows 70/20/10. Why do the two splits not contradict each other?
3. Why does a CI pipeline need the base of the pyramid to be wide?

---

## Question 12: Testing at Google Scale

Google's CI system runs about 150 million tests a day, and developers commit about once a second. Why can it not test each commit on its own, and what does it do instead?

---

## Question 13: Coverage

A coverage report shows 37% statement coverage.

1. What does the remaining 63% tell you about the code?
2. Give two reasons why 100% statement coverage still does not show the code is correct.

---

## Question 14: Flaky Tests

1. Define a flaky test and give two possible causes.
2. In one industrial project, about 1 test-suite run in 20 had a flaky failure. Why is the loss of trust worse than the time lost?

---

## Question 15: After Deployment

What is an error budget for a service with a 99.9% SLO, and how does SRE extend the CI mindset beyond deployment?

---

## Topics Covered

| Topic | Questions |
|-------|-----------|
| Why quality matters | Q1, Q2 |
| Cost and benchmarks | Q3, Q4, Q5 |
| Continuous integration | Q6, Q7, Q8, Q9, Q10 |
| Testing in the pipeline | Q11, Q12, Q13, Q14 |
| Beyond deployment | Q15 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
