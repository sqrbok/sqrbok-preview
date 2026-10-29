---
title: Test Status Reporting
parent: Quality Planning
nav_order: 6
layout: default
---

# Test Status Reporting

**Test status reporting** answers the question "how much testing has been done, and is the product ready?" with measures chosen in advance in the quality plan, its eleventh decision, and delivered to each stakeholder in a form they can act on {% cite kaner2000measurement ahmad2024infoneeds %}. No single number answers it.

## 1. "We are 30% done": thirty percent of what?

Kaner shows that a figure such as "30% of testing is done" can rest on any of several dimensions {% cite kaner2000measurement %}:

| Dimension | What the 30% compares |
|---|---|
| Product coverage | tests done against a model of good-enough testing, a model that changes as bugs and new knowledge appear |
| Agreement-based | tasks finished against the tasks promised in the plan |
| Project history | this project against similar past projects |
| Risk-based | the critical risk areas still open, and the time needed to close them |
| Evaluation | whether testing is turning up surprises that suggest it is weaker than expected |
| Results | bug statistics, tests completed |
| Results and obstacles | progress plus the obstacles that blocked testing |
| Effort | hours or weeks of testing spent |

A useful report states which dimension it uses, and usually combines several: progress against plan, the remaining risks, and what is blocking.

## 2. What people in a pipeline need to know

In continuous integration and delivery, Ahmad et al. collected **39 information needs** from studies and practitioners. The most important and most frequent was **who owns a test suite**: who maintains it and fixes it when it fails. Next came the flaws and trends in the end-to-end delivery flow, and which components had the most recent test failures {% cite ahmad2024infoneeds %}.

**Dashboards** serve these needs. Salagundi describes one built from Jenkins pipelines that store results in Elasticsearch, a Flask back end, a web front end and links back to the raw logs, so that teams see trends and anomalies as builds run {% cite salagundi2024dashboard %}. Report generators such as Allure collect results in the pipeline and publish an interactive report with stack traces, logs, screenshots, history across releases and a matrix tracing tests to requirements {% cite peddireddy2024allure %}.

## Example: one status, three audiences

A release is a week away. The **developers** see the dashboard: 4 failing tests, each with its owner and a link to the log. The **test lead** sees agreement and risk: 85 of 100 planned scenarios run, payment and refunds still untested, the staging database down for two days (an obstacle). The **project manager** gets one paragraph: two risk areas remain, about four days of work, release on time if staging is restored by Tuesday. _(Constructed example.)_

## Where the inputs come from

Each dimension has a source the team already runs: coverage from the test runner (pytest-cov, JaCoCo), agreement from the list of planned scenarios marked as run, risk from the plan's risk list, effort from time reports, obstacles from blocked tickets in the tracker, and the pipeline's state from the CI server's API. A dashboard joins these sources; it does not create the data.

## When to use it, and when not

- **Decide the measures in the plan**, before testing starts, so that nobody chooses the flattering one at the end.
- **Do not report a bare percentage.** Always name the dimension and add the open risks.

## Limitations

- **Measures change behaviour.** Kaner asks what unintended effects measuring, and managing by the measures, will have {% cite kaner2000measurement %}; a completion rate, for instance, rewards running the easy tests first.
- **Dashboards show what is collected.** A risk nobody logs does not appear on any chart.

## How solid is this?

- **Where it comes from.** Dimensions from Kaner; information needs from Ahmad et al.'s study; dashboard and report tooling from Salagundi and Peddireddy {% cite kaner2000measurement ahmad2024infoneeds salagundi2024dashboard peddireddy2024allure %}.
- **What is contested.** The dashboard and Allure papers describe tools in use; they do not measure whether teams decide better with them.
- **What we do not hold.** A controlled study of reporting styles and release decisions.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
