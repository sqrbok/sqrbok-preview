---
parent: Testing Overview
nav_order: 6
title: Testing by Purpose
layout: default
---

# Testing by Purpose

**Testing by purpose classifies a test by the question it answers: is the result right (functional), does it still work after a change (regression), is it fast enough (performance), and does it survive the unexpected (robustness)?** The same unit, integration or system test can serve any of these purposes; purpose and [level](testing_levels.md) are separate choices.

## 1. Functional testing

A functional test checks that each function does what it should and does not do what it should not. Its inputs come from typical values, boundary values and invalid values, and from combinations of inputs likely to interact, such as form fields submitted together; see [input domain testing](../../domain/index.md) and [combinatorial testing](../../combinatorial/index.md). Single actions are not enough for faults that depend on state: a **scenario** test strings several actions into one realistic story (log in, change the address, order, log out) without resetting the system between steps.

## 2. Regression testing

Regression testing re-runs tests after a change to check that the updated software still has the functionality it had before {% cite ammann2016introduction %}. It is the main reason to automate: a suite that runs on every commit turns each past bug into a permanent check. At Google's scale the cost is the problem: more than 150 million test runs a day {% cite memon2017testing %}, so Google tests batches of commits together instead of each commit on its own {% cite memon2017testing %}.

## 3. Performance testing

Performance tests run the system under a controlled workload and measure response time, throughput and resource use {% cite everett2007performance %}. A **load** test applies the expected peak and asks whether targets are met; a **stress** test goes beyond the peak and asks where and how the system breaks {% cite molyneaux2014art %}. These are covered in [Performance Testing](../../../attributes/performance/testing.md).

## 4. Robustness testing

A robustness test feeds the system what it was not designed for, such as malformed input, a full disk or a service that does not answer, and checks that it fails gracefully: it rejects the input, reports the problem and recovers, instead of crashing or corrupting data. [Fuzz testing](../../random/fuzzing.md) automates this for input; [chaos engineering](../../../organization/05-practice/12-chaos-engineering.md) does it for infrastructure in production.

## 5. Example: one login service, four purposes

(Constructed example.) A login service has one requirement per purpose:

| Purpose | Test | Passes when |
|---|---|---|
| Functional | correct user name and password; wrong password; empty password | the first logs in, the other two are refused with a message |
| Regression | the full login suite, re-run after the password-hashing library is upgraded | every test that passed before still passes |
| Performance (load) | 500 simulated users log in per minute for ten minutes | 95% of logins answer within one second |
| Robustness | the user database is stopped during a login | the user sees "try again later"; no stack trace, no partial session |

Each row needs a different oracle: an expected value, the previous results, a percentile target, and a rule about how failure must look.

## When to use it — and when not

- Name the purpose before writing a test; it decides the oracle and the environment.
- Do not run performance tests on a developer laptop and quote the result as the system's: the workload and the hardware must resemble production.

## Limitations

The list is not exhaustive: security, usability and compatibility testing are purposes too, each with its own area of this site. And a test rarely serves one purpose only: the functional login suite becomes the regression suite the day after it is written.

## How solid is this?

- **Where it comes from.** Regression testing as defined by Ammann and Offutt {% cite ammann2016introduction %}; performance test types from Everett and McLeod and from Molyneaux {% cite everett2007performance molyneaux2014art %}; the Google figure from Memon and colleagues {% cite memon2017testing %}.
- **What is contested.** Names: "load", "stress" and "performance" testing are used loosely in industry.
- **What we do not hold.** A source for the functional and robustness categories as a taxonomy; they follow common usage.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
