---
title: "SN L00: Course Introduction and CI"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 0.1
layout: default
page_type: study-notes
---

# SN L00: Course Introduction and CI

The opening lecture asks two questions: why should a software engineer care about quality at all, and what does checking quality look like in a modern team? Its answer to the first is the cost of failure; its answer to the second is continuous integration, where every change is built and tested automatically within minutes. The [revision questions](RQ_L00_Intro_CICD.md) test this lecture.

## 1. Software failures are expensive, and each one breaks a nameable quality

The lecture opens with failures students may remember. In July 2024 a faulty content update to CrowdStrike's Falcon security software crashed about 8.5 million Windows machines, under 1% of all Windows machines, and still stopped airlines, hospitals and banks {% cite weston2024crowdstrike %}. In 2012 Knight Capital lost more than US$460 million in 45 minutes because a deployment left old code active on one of its servers {% cite sec2013knight %}. In December 2021 a critical remote-code-execution flaw in Log4j, a widely used Java logging library, was already being exploited when the fix was released {% cite cisa2021log4j %}.

The useful habit is to name the quality that failed: availability for CrowdStrike, maintainability for Knight Capital (dead code nobody removed), security for Log4j. A named quality can become a requirement, and a requirement can be checked.

→ [Why Software Quality Matters](../../define/importance.md)

## 2. Poor quality has a price for a whole economy, but the estimate is rough

Single incidents make the news; the total is larger. A report for the Consortium for Information and Software Quality estimated the cost of poor software quality in the United States in 2022 at **at least US$2.41 trillion**, spread over four overlapping areas: operational failures (the largest, mostly cybercrime), legacy-system problems, unsuccessful projects, and finding and fixing defects; technical debt is estimated separately {% cite krasner2022cpsq %}. Treat the figure as an order of magnitude. It is extrapolated from earlier estimates, its parts overlap, and its rise from US$2.08 trillion in 2020 (16%) is almost exactly the 15% inflation of those two years.

For a team, the more useful number is its own: how many hours went into finding and fixing defects this month. Whether a defect costs more the later it is found is a separate question, with a less dramatic answer than the often-quoted claim that a fix in production costs 100 times more than one made during requirements: measured ratios run from about 5:1 on small systems to over 100:1 on large aircraft programmes, and one study of 171 software projects found no such effect {% cite boehm2001top10 stecklein2004costs menzies2017delayed %}.

→ [Cost of Poor Software Quality](../../define/cost-of-poor-quality.md) · [Cost of Late Fixes](../../define/cost-of-late-fixes.md)

## 3. Released software still carries defects: a few per thousand lines

How many defects reach users? A study that collected published figures for 109 projects found a median of 4.3 post-release defects per thousand lines of code (KLOC), and a mean of 7.47 pulled up by a few very defective projects {% cite shah2012defect %}. Open-source projects, Java projects (compared with C) and very large projects had lower densities in that sample. These are rough benchmarks: the projects counted lines and defects in different ways.

For example, to compare your own project, divide post-release defects by size: 30 defects reported in the first year of a 12,000-line service is 30 / 12 = 2.5 defects per KLOC.

→ [Defect Density Benchmarks](../../define/measurement/defect-density-benchmarks.md)

## 4. The course moves from defining quality to checking it

The course follows the order in which a team meets the problem. First it defines quality: quality models say which characteristics matter, and measurement puts numbers on them. Then it checks quality: verification methods, coverage criteria, input-domain, random and exploratory testing. Finally it covers qualities in practice: reliability, performance and security. Setting up quality gates in a CI pipeline runs through all of it as the main practical skill: each later technique becomes one more automated check.

→ [Quality](../../define/index.md)

## 5. Continuous integration checks every change, automatically

**Continuous integration (CI)** means that every developer merges changes into the shared main line at least daily, and every merge is verified by an automated build that includes the tests, so that integration errors appear within minutes {% cite fowler2024ci %}. Without it, developers work on separate branches for weeks and then spend days making them fit together. A broken build is fixed first; in Kent Beck's words, quoted by Fowler, "nobody has a higher priority task than fixing the build". The quickest fix is often to revert the faulty commit and repair it afterwards; while the build is red, nobody can tell whether a newly pushed change is good, and new failures hide behind the old one {% cite fowler2024ci %}.

Two extensions share the abbreviation CD. **Continuous delivery** keeps every change that passes the pipeline ready to release; **continuous deployment** releases it automatically. Both depend on a trusted green build.

The evidence is encouraging but not proof. In a study of open-source projects on GitHub, projects using CI released about twice as often (0.54 against 0.24 releases a month), yet the same projects had already released 0.34 times a month before adopting CI, more than projects that never did {% cite hilton2016ci %}.

→ [Continuous Integration](../../organization/05-practice/13-continuous-integration.md)

## 6. A pipeline is a chain of quality gates

A **quality gate** is a checkpoint a change must pass before it moves on {% cite winters2020swe %}. The lecture's pipeline has four: before the merge (code review, linter, unit tests), the build (compilation, and static analysis: tools that inspect the code without running it), the full test run (unit, integration, end-to-end) and the release (checks on a staging system, then a **canary** that sends a small share of real traffic to the new version). A gate that is automated runs on every change; a gate that waits for a person runs only when someone has time.

For example, in a small Python project the first gates take one short GitHub Actions workflow: on every push, check out the code, install the dependencies, run the linter (ruff), then the tests (pytest). The first two steps only set the job up; the linter and the tests are the checks, and any failing step turns the build red. The author then sees a red cross next to the commit or pull request, gets a notification and can open the log of the failing step; if the repository requires a green build, the pull request cannot be merged. A linter is a light form of static analysis: it checks style and simple error patterns without running the code.

→ [Quality Gates & CI/CD](../../organization/05-practice/10-quality-gates-cicd.md)

## 7. Most tests should be small and fast

A pipeline that takes an hour defeats its purpose, so the test suite needs a shape: many narrow unit tests, fewer integration tests, a few end-to-end tests. *Software Engineering at Google* recommends about 80% unit, 15% integration and 5% end-to-end tests by count {% cite winters2020swe %}. The 70/20/10 split on the lecture slide comes from an earlier Google testing-blog post; both are rules of thumb, not measured optima. Small tests are fast and point at the faulty code; end-to-end tests are slow and fail for reasons unrelated to the change.

→ [The Test Pyramid](../../verif/overview/testing/test-pyramid.md)

## 8. At Google's scale, CI has to choose which tests to run

Google's Test Automation Platform integrates and tests more than 13,000 projects a day, with 800,000 builds and 150 million test runs, while developers commit about once a second {% cite memon2017testing %}. That is too fast to test every commit on its own, so commits are batched, and developers sometimes waited hours for results. A **test target** is one buildable test suite, run again for every change that affects it, so the daily runs far outnumber the targets. In one month, of 5.5 million test targets affected by changes, only about 63,000 ever failed. Most tests almost never fail, which is why test selection, running first the tests most likely to fail, pays off; Google's researchers propose it for exactly this reason.

→ [The Test Pyramid](../../verif/overview/testing/test-pyramid.md)

## 9. Coverage shows what no test touched, not that the code is right

**Statement coverage** is the share of statements that at least one test executes. The lecture shows a coverage report at 37%: 63% of the statements never ran in any test. Either that code is unused and should go, or nobody knows whether it works. Full coverage proves less than it seems: a test can run every line and assert nothing, and coverage cannot reveal a feature that is missing altogether. Read an uncovered line as a clue about a missing test, not as a number to push up.

→ [Using Coverage in Practice](../../verif/coverage/practice.md)

## 10. Automate the repeatable checks, and do not tolerate flaky tests

Regression tests, builds, static analysis and coverage reports run on every change because a machine repeats them faster and more reliably than a person. Judging usability, exploring for unexpected behaviour and reviewing privacy still need people.

A **flaky test** passes and fails on the same code, for example because it depends on network timing or on the order in which tests run. In one company's million-line Java project, about one test-suite run in twenty had at least one flaky failure, and handling flaky tests took at least 2.5% of developers' productive time {% cite leinen2024flaky %}. The bigger damage is to trust: when a red build is often a false alarm, people stop reacting to red builds.

→ [Continuous Integration](../../organization/05-practice/13-continuous-integration.md)

## 11. Quality does not stop at deployment

The lecture closes with a preview of site reliability engineering (SRE), Google's way of running production services {% cite beyer2016sre %}. A service level objective (SLO) sets a measured reliability target, such as 99.9% of requests succeeding over 30 days; the **error budget** is the remaining 0.1%, the failure the team is allowed to spend on change. The mindset is the same as in CI: measure, automate, improve.

→ [Site Reliability Engineering](../../organization/05-practice/06-site-reliability-engineering.md)

## Where this goes next

The next lecture asks what "quality" means precisely: [SN L01: Quality Models](SN-L1-QM.md) turns vague praise like "reliable" or "secure" into named characteristics that can be specified and measured, the step from section 1's incidents to requirements. The first lab is practical: set up a Python project and a GitHub Actions workflow that installs it, runs a linter and the tests on every push, and blocks a merge when the build is red.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
