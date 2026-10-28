---
title: Google Engineering Practices
parent: Industry Practices
nav_order: 8
layout: default
---

# Google Engineering Practices

**Google's engineering practices** are the testing, code-review and static-analysis routines that let tens of thousands of engineers change one shared code base continuously; they are documented in *Software Engineering at Google* and in studies of Google's own data {% cite winters2020swe sadowski2018codereview memon2017testing %}.

## 1. Test sizes and the mix

Google classifies tests by the resources they may use. A **small** test runs in a single process with no network or disk (most unit tests); a **medium** test may use several processes on one machine; a **large** test may span machines and real services (end-to-end tests). Small tests are fast and deterministic; large tests check what users actually experience but are slow and more often flaky. As "a very rough guideline", the book aims for about **80%** narrow unit tests, **15%** integration tests and **5%** end-to-end tests {% cite winters2020swe %}. A suite that inverts this, with mostly end-to-end tests, is slow to run and hard to debug, because a failure could come from anywhere.

A **flaky test** passes and fails on the same code. Flakiness destroys trust in a suite, so teams detect it (for example by running tests in random order), quarantine flaky tests and fix or delete them rather than retrying them forever {% cite winters2020swe %}.

## 2. Testing at scale

Memon and colleagues studied Google's Test Automation Platform (TAP) {% cite memon2017testing %}. On an average day it integrated more than 13,000 projects, with 800,000 builds and 150 million test runs. Over one month, 500,000 changes affected 5.5 million test targets; only **63,000 of those targets ever failed**, about 1.15%, and only 1.23% of all test executions found a breakage or a fix. Tests more than ten dependency edges away from the changed code hardly ever broke. The conclusion: most test executions are wasted, and test selection based on how close a test is to a change can save resources.

## 3. Code review

Every change at Google is reviewed. Sadowski and colleagues analyzed about 9 million reviewed changes by more than 25,000 authors and reviewers (2014–2016) {% cite sadowski2018codereview %}:

| Measure | Finding |
|---|---|
| Median change size | 24 lines; over 35% of changes touch one file |
| Reviewers | median 1; fewer than 25% of changes have more than one |
| Speed | first feedback within an hour for small changes; median review under 4 hours overall |
| Iterations | over 80% of changes need at most one round of comments |
| Satisfaction | 97% of developers satisfied with the review tool, Critique |

The original reason for review at Google was not finding bugs but **education**: making code understandable and maintainable and spreading knowledge. Developers named four expectations: education, maintaining norms, gatekeeping and accident prevention.

**Example.** A small change gets its first feedback within an hour; a very large one waits about five hours {% cite sadowski2018codereview %}, and at Microsoft larger changes received a lower share of useful comments {% cite bosu2015codereview %}. The practice that keeps reviews fast and useful is therefore on the author's side: split work into small, self-contained changes.

## 4. Static analysis in review

**Tricorder** runs analyzers on every change sent for review and posts findings as review comments; reviewers can click NOT USEFUL. An analyzer whose not-useful rate reaches **10%** is put on probation until its authors fix it, because developers ignore tools that cry wolf {% cite sadowski2015tricorder %}. Details are on [Deploying Static Analysis](../../verif/static/deployment.md).

## When to use these practices — and when not

Small changes, single expert reviewers, fast mostly-small tests and a false-alarm budget for analyzers transfer to teams of any size. TAP's scale problems (deciding which of millions of tests to run) do not; a small project can simply run all its tests.

## Limitations

- **Google studies Google.** The code-review and testing papers are written by Google engineers about Google, with its monorepo and tooling.
- **The test mix is a guideline,** not a measured optimum; the book calls it very rough.

## How solid is this?

- **Where it comes from.** *Software Engineering at Google*, and Google's papers on code review, testing and static analysis {% cite winters2020swe sadowski2018codereview memon2017testing sadowski2015tricorder %}.
- **What is contested.** The often-quoted 70/20/10 split is not the book's figure; the book gives roughly 80/15/5.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
