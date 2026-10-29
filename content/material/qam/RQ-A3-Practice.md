---
title: "RQ A03: Industry Practices"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 6
redirect_from: /content/organization/05-practice/RQ-A3-Practice.html
layout: default
page_type: question-set
---

# RQ A03: Industry Quality Practices

These questions test [SN A03: Industry Quality Practices](SN-A3-Practice.md). Explain in your own words and give brief examples; for calculations, show each step.

---

## Question 1: The Test Mix

*Software Engineering at Google* aims, as a very rough guideline, for about 80% unit, 15% integration and 5% end-to-end tests.

1. Define small, medium and large tests by the resources they may use.
2. Why does the guideline favor small tests? What goes wrong in a suite that is mostly end-to-end tests?

---

## Question 2: Flaky Tests

What is a flaky test, and why is one flaky test a problem for the whole suite? Describe how a team can detect and handle flaky tests.

---

## Question 3: Coverage Limitations

Microsoft's final-release exit criteria include 80% code coverage from unit tests.

1. What does coverage measure, and what can it not detect?
2. Give your own example of code with 100% line coverage and a passing test that still hides a bug.

---

## Question 4: LLM-Generated Tests

Research finds that LLM-generated test assertions tend to capture a program's actual behavior rather than its expected behavior. Explain why such tests can pass on buggy code, and how a developer should review a generated assertion.

---

## Question 5: Code Review Size and Speed

Google's median change is 24 lines, reviewed by one person, with a median review time under 4 hours.

1. Why do small changes make fast review possible?
2. What did Microsoft find about who writes the most useful review comments, and about large changes?
3. What is the main purpose of code review at Google?

---

## Question 6: False Alarms in Static Analysis

Google's Tricorder puts an analyzer on probation when 10% or more of its findings are marked NOT USEFUL. Why do false alarms matter so much for adoption? What happens when developers receive too many?

---

## Question 7: Canary Deployment

1. Explain how a canary release limits the blast radius.
2. A new version fails 20% of its requests. Ignoring the old version's own errors, compute the overall error rate with a 1% canary, a 5% canary and no canary.
3. With a 99.9% SLO, what does the 5% canary mean for the error budget?

---

## Question 8: Feature Flags and Rollback

How does a feature flag separate deployment from release? Why is "roll back first, fix afterwards" the rule when a release goes wrong?

---

## Question 9: SLIs, SLOs and SLAs

Explain the hierarchy of SLI, SLO and SLA with one example of each. Why should the internal SLO be stricter than the SLA?

---

## Question 10: Error Budgets

A service has a 99.9% SLO and serves 3,000,000 requests in 30 days.

1. Calculate the error budget in failed requests and the even burn rate per hour.
2. How many days does the budget last at a burn rate of 10, that is, ten times the even rate?
3. What does a team do when the budget is exhausted, and what can it do while budget remains?

---

## Question 11: Golden Signals and Paging

Name the four golden signals. Explain the difference between alerting on symptoms and on causes, and give a bad (cause) and a good (symptom) alert for the same underlying problem.

---

## Question 12: Toil and On-Call

Define toil. Give two examples of toil and two of engineering work that reduces it. Why does Google cap toil at 50% and on-call at two incidents per 12-hour shift?

---

## Question 13: Who Owns Quality

Compare Facebook's continuous deployment without a separate test team with Microsoft's bug bar and ship room. Which suits an online service, and which a product that customers install? Why does Facebook's mobile app use a release train?

---

## Question 14: Chaos Engineering

1. What is the difference between resilience testing and chaos engineering?
2. List the steps of a chaos experiment and the safeguards that keep it from causing an outage.
3. Why is finding an unknown weakness before customers do worth the risk?

---

## Question 15: AI Assistants

Most professionals now use AI at work. Why does letting an LLM repeatedly "improve" code without human review increase security risk, according to the experiment in the notes? What practices should apply to AI-generated changes?

---

## Topics Covered

| Topic | Questions |
|-------|-----------|
| Testing practices | Q1, Q2, Q3, Q4 |
| Code review and static analysis | Q5, Q6 |
| CI/CD and deployment | Q7, Q8 |
| SRE | Q9, Q10, Q11, Q12 |
| Quality organization | Q13 |
| Chaos engineering | Q14 |
| AI in development | Q4, Q15 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
