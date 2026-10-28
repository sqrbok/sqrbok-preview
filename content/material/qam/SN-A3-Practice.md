---
title: "SN A03: Industry Quality Practices"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 5
redirect_from: /content/organization/05-practice/SN-A3-Practice.html
layout: default
page_type: study-notes
---

# SN A03: Industry Quality Practices

The lecture asks how large companies (Google, Microsoft, Facebook, Netflix) build quality into everyday delivery rather than testing it in at the end, and which of their practices a smaller team can adopt. [RQ A03: Industry Quality Practices](RQ-A3-Practice.md) tests this lecture.

## 1. Quality moved into every change

Where a release once ended with a long test phase, these companies check quality on every change: automated tests and static analysis before merge, review by a colleague, gradual rollout with monitoring, and explicit reliability targets in production. The published evidence comes mostly from the companies' own studies, so their numbers describe their context, not universal targets {% cite sadowski2018codereview savor2016facebook %}.

→ [Industry Case Studies](../../organization/05-practice/07-industry-case-studies.md)

## 2. Most tests should be small

Google sizes tests by the resources they may use: **small** tests run in one process without network or disk, **medium** tests on one machine, **large** tests across machines and real services {% cite winters2020swe %}. Small tests are fast and deterministic; large tests check what users experience but are slow, expensive to debug and more often flaky. As a very rough guideline, *Software Engineering at Google* aims for about **80% unit, 15% integration and 5% end-to-end** tests. A suite that inverts the pyramid, mostly end-to-end tests, runs for hours, and when a test fails the cause could be anywhere.

A **flaky test** passes and fails on the same code. It teaches people to ignore red builds, so teams find flaky tests (for example by running tests in random order), quarantine them and fix or delete them.

At Google's scale most test runs are wasted: over one month, only 63,000 of 5.5 million affected test targets ever failed, and tests more than ten dependency edges away from a change hardly ever broke {% cite memon2017testing %}.

→ [Google Engineering Practices](../../organization/05-practice/08-google-engineering-practices.md)

## 3. Coverage is a floor, not a proof

Microsoft's final-release exit criteria include, among others, 80% code coverage from unit tests {% cite page2008mstesting %}. Coverage shows which code ran during the tests; it does not show that the tests checked the right result, and it cannot show functionality that is missing.

**Example.** `def discount(price): return price * 0.9` should give 10% off orders over 100 only. The test `assert discount(200) == 180` executes every line (100% coverage) and passes, yet `discount(50)` wrongly returns 45: the missing condition has no line to cover.

→ [Microsoft Quality Practices](../../organization/05-practice/09-microsoft-quality-practices.md)

## 4. LLM-generated tests check what the code does, not what it should do

LLMs generate tests quickly, but the assertions they write tend to capture the program's *actual* behavior rather than its *expected* behavior, so a test generated from buggy code will often assert the bug {% cite konstantinou2024testoracles %}. Applied to the example above (an illustration, not a measured case): an LLM shown the buggy `discount` would tend to write `assert discount(50) == 45`, a test that passes and protects the bug. Treat generated assertions as claims to check against the specification, not as oracles.

→ [Testing Practices](../../organization/05-practice/04-testing-practices.md)

## 5. Review small changes quickly, with a reviewer who knows the code

At Google, in about 9 million reviewed changes, the median change was **24 lines** and the median number of reviewers **one**; small changes got their first feedback within an hour and the median review took under **4 hours** {% cite sadowski2018codereview %}. Google's main reason for review is education and maintainability, not finding bugs. At Microsoft, reviewers who had reviewed a file before wrote useful comments 65–71% of the time, first-time reviewers 32–37%, and larger changes drew a lower share of useful comments {% cite bosu2015codereview %}. Small changes are what make fast, useful review possible.

Static analysis reports into the same review. Google's Tricorder puts an analyzer on probation when developers mark 10% or more of its findings NOT USEFUL, because developers stop reading a tool that cries wolf {% cite sadowski2015tricorder %}. LLM reviewers are not yet a replacement: on 1,000 real pull requests the best LLM-based reviewer reached an F1 score of 19.38%, held back by false positives {% cite zeng2025swrbench %}.

→ [Google Engineering Practices](../../organization/05-practice/08-google-engineering-practices.md), [Microsoft Quality Practices](../../organization/05-practice/09-microsoft-quality-practices.md)

## 6. Gates and canaries limit how far a bad change gets

A **quality gate** is a checkpoint a change must pass: review and static analysis before merge (pre-submit), the full suite on the main line (post-submit), and at release a healthy canary and an error budget not yet spent {% cite beyer2018sreworkbook %}. A **canary** sends a small share of traffic, often 1%, to the new version and compares its errors and latency with the old one; a staged rollout then widens to 10%, 50%, 100%.

**Example.** A new version fails 20% of its requests. With a 5% canary the overall error rate is 0.05 × 20% = **1%**; with a 1% canary 0.2%; without a canary, 20%. The canary limits the **blast radius**, the share of users a bad change can reach before it is caught.

A **feature flag** ships code switched off and turns it on separately; switching a flag off is far faster than a rebuild and redeploy, which the SRE book puts at 15 minutes or more {% cite beyer2016sre %}. When a release goes wrong the rule is **roll back first, fix afterwards**.

→ [Quality Gates & CI/CD](../../organization/05-practice/10-quality-gates-cicd.md)

## 7. SRE turns reliability into a budget

An **SLI** is a measured ratio (successful requests ÷ all requests), an **SLO** its internal target (say 99.9%), and an **SLA** the contract with customers, with penalties {% cite beyer2016sre %}. The SLO is kept stricter than the SLA so the team reacts before customers are owed anything. The **error budget** is 100% − SLO: with a 99.9% SLO, 0.1% of requests may fail.

**Example.** With a 99.9% SLO and 3,000,000 requests in 30 days, the budget is 0.001 × 3,000,000 = **3,000** failed requests. Spent evenly, that is about 4.2 per hour; at a burn rate of 10 the budget is gone in 3 days. While budget remains the team can release faster; when it is spent, the workbook's example policy halts feature launches until the service is back within its SLO {% cite beyer2018sreworkbook %}.

SRE monitors four **golden signals**: latency, traffic, errors and saturation. Pages should fire on **symptoms** users feel ("checkout is slow"), not on **causes** ("database CPU at 90%"), which may never affect users. **Toil**, manual, repetitive, automatable work without lasting value that grows with the service, is capped at **50%** of an SRE's time, and on-call is limited to two incidents per 12-hour shift so each can be analyzed.

→ [Site Reliability Engineering](../../organization/05-practice/06-site-reliability-engineering.md)

## 8. The people who write the code own its quality

Facebook and OANDA practiced continuous deployment without a separate test team: developers wrote the tests and answered for their code in production {% cite savor2016facebook %}. Facebook developers released 3.5 updates a week each, with a median of 33 lines, while the team grew 20-fold and quality held; about 5% of engineers built the tooling that made this possible. Mobile apps, which users must install, use a weekly or two-weekly release train with stabilization and a soak period instead {% cite rossi2016mobilefb %}.

Microsoft's milestone model keeps quality decisions explicit instead: a **bug bar** defines which bugs must be fixed at each stage (a severity-1 bug always blocks release), and a **ship room** of development, test and program-management leads decides what ships {% cite page2008mstesting %}.

→ [Continuous Deployment at Facebook](../../organization/05-practice/11-facebook-continuous-deployment.md), [Microsoft Quality Practices](../../organization/05-practice/09-microsoft-quality-practices.md)

## 9. Chaos engineering looks for unknown weaknesses

**Resilience testing** checks a property the team expects; **chaos engineering** injects real faults (killed instances, delays, failed dependencies) into a production or production-like system to find weaknesses nobody thought to test {% cite owotogbe2024chaos beyer2018sreworkbook %}. Netflix pioneered it in 2008. An experiment establishes the steady state, sets up monitoring, injects the fault, compares the result with a hypothesis, and widens the blast radius step by step, with abort conditions and rollback as safeguards. A team that discovers in an experiment that losing one of three replicas doubles latency can fix the connection pools before a real outage finds the same weakness.

→ [Chaos Engineering](../../organization/05-practice/12-chaos-engineering.md)

## 10. AI assistants need human gates

In the 2025 DORA survey of nearly 5,000 technology professionals, 90% reported using AI at work {% cite dora2025ai %}. Letting an AI iterate unchecked is risky: in a controlled experiment, repeatedly asking an LLM to "improve" code increased critical vulnerabilities by 37.6% after five iterations, and the authors recommend human validation between iterations {% cite shukla2025security %}. The same rules as for any contributor apply: small changes, review by someone who knows the code, and tests whose assertions come from the specification (§4).

→ [Modern Code Review](../../verif/inspection/modern-code-review.md)

## Where this goes next

A04 turns these practices into a quality plan for a project: which reviews, tests and gates to use, when, and how to know they worked.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
