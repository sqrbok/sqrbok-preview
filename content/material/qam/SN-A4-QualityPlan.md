---
title: "SN A04: Quality Planning"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 7
redirect_from: /content/organization/02-plan/SN-A4-QualityPlan.html
layout: default
page_type: study-notes
---

# SN A04: Quality Planning

This lecture asks how a project decides, before the work starts, which quality activities it will run and how strictly. The answer is a quality plan: a short set of documented decisions, tuned to how critical each part of the product is, enforced through gates, sized by an effort estimate, written down in test plans and tracked through reports.

## 1. Without a plan, quality is an afterthought

Under deadline pressure, quality work is the first thing dropped. A **quality plan** is "a set of documented decisions concerning quality"; the more development and quality roles are combined, the more it must build in checks so that quality activities "are not easily tossed aside as deadlines loom" {% cite miranda2015asa %}. The plan belongs to quality management, which has four parts {% cite miranda2015asa %}:

- **Quality planning** identifies the activities, tools and training, and organizes them.
- **Quality assurance (QA)** gives confidence that a product **will** meet its quality requirements: is there a defect process, are entry and exit criteria met, are metrics collected?
- **Quality control (QC)** verifies that a product **is** of the required quality and reports deficiencies.
- **Quality improvement** makes the processes themselves more effective.

QA watches the process, QC the product; a project needs both, because a good process can still ship a bad build and a good build says nothing about the next one. Google builds the plan into the tooling: its Test Certified programme starts at Level 1 with a continuous build and tests classified by size, so the process demands tests before anyone has to remember them {% cite winters2020swe %}.

→ [The Quality Plan](../../organization/02-plan/01-quality-management-planning.md)

## 2. A plan is eleven documented decisions

Planning runs in five steps (determine what to do, estimate effort and lead times, coordinate with the development plan, organize the activities, document the plan) and records eleven decisions {% cite miranda2015asa %}:

1. **activities** (assurance, control, improvement);
2. their **interactions** with each other and with development;
3. the **artifacts** they apply to;
4. **when**;
5. **by whom**;
6. to what **extent**;
7. **cost and duration**, up-front and recurring;
8. **tools**;
9. **training**;
10. **defect handling**;
11. **measurements**.

Four things drive the answers: applicable process, regulations and standards; the deliverables, seen by users (features, quality attributes) and by producers (modules, builds); the cost of quality; and criticality {% cite miranda2015asa %}.

**Example.** A four-person team building a booking web application answers in eleven lines: (1) code review, unit and API tests, static analysis; (2) review blocks merging; (3) all production code and the API specification; (4) on every pull request plus a test pass before release; (5) developers write unit tests, a tester owns API tests; (6) 70% line coverage on new code; (7) about 20% of sprint capacity; (8) pytest, a linter, the CI service; (9) one session on API testing; (10) every defect logged with severity and origin, severity-1 defects fixed before release; (11) coverage, open defects by severity, escaped defects per release.

→ [The Quality Plan](../../organization/02-plan/01-quality-management-planning.md)

## 3. Each phase has minimum V&V tasks

Wallace and Fujii list minimum verification and validation (V&V) tasks for every phase: traceability analysis in every phase, requirements validation and interface analysis, design and code evaluation, and test planning that starts early (system and acceptance tests are planned during requirements, component and integration tests during design) {% cite wallace1989verification %}. For critical software, a **criticality analysis** finds the most critical functions and concentrates extra V&V there, since about 20% of problems cause 80% of rework cost. Evidence on independent V&V is mixed: on three small projects it added 10–30% to cost with no clear benefit; on four larger ones it found 50–89% of errors before development testing and saved 92–180% of its own cost when it started at requirements {% cite wallace1989verification %}.

→ [V&V Activities by Phase](../../organization/02-plan/02-vv-activities-process-models.md)

## 4. Criticality sets the rigor

Criticality measures how much harm a failure does. Regulated domains fix levels and attach verification to them. DO-178B classifies avionics software by failure condition, with structural coverage objectives rising with the level {% cite miranda2015asa rtca1992do178b %}:

| Level | Failure condition | Structural coverage objective |
|---|---|---|
| A | catastrophic | MC/DC |
| B | hazardous | decision |
| C | major | statement |
| D | minor | none |
| E | no effect | none |

IEEE 829-2008 uses four integrity levels (4 catastrophic, 3 critical, 2 marginal, 1 negligible) and scales the test documents with them {% cite ieee829-2008 %}. Early in a project, nobody knows which modules will be critical, so Miranda writes criteria **per class** now and assigns modules later, which avoids planning paralysis. His three classes (A to C, rising from A; not the DO-178B letters, where A is the highest) step coverage from statement to branch to basis paths, add code inspections from class B, raise combinatorial strength from pairs to triples to quadruples, and tighten the cyclomatic complexity limit from 20 to 15 {% cite miranda2015asa %}.

→ [Criticality and Quality Gates](../../organization/02-plan/03-quality-gates-criticality.md)

## 5. Gates turn criteria into checkpoints

A **quality gate** is a checkpoint with **entry criteria** (when an activity may start) and **exit criteria** (when it is done) {% cite uzunova2024qualitygates %}. Each criterion should name four things: the metric, the desired value, the predicate comparing them, and the object measured, for example "cyclomatic complexity < 10 for any single method"; a tool such as SonarQube can enforce it {% cite uzunova2024qualitygates %}.

Gates can be **phased**: Microsoft raised its coverage gate from 65% at the second milestone to 75% at the third and 80% at release {% cite page2009microsoft %}, and set exit criteria such as no open severity-1 bugs, a **bug bar** that blocks release {% cite page2009microsoft %}. Google stages gates along the delivery flow: pre-submit checks (review, static analysis, unit tests) before a change merges, post-submit tests after it, and release checks before deployment {% cite winters2020swe %}.

→ [Criticality and Quality Gates](../../organization/02-plan/03-quality-gates-criticality.md)

## 6. Organizing testing: maturity and roles

Hrabovská et al. reviewed 17 testing process models. TMMi has five maturity levels: Initial, Managed, Defined, Measured, Optimization; every level above the first adds process areas {% cite hrabovska2019testing %}. Google's Test Certified levels run from Level 1 (continuous build, tests classified by size) to Level 5 (a regression test for every bug fix, 60% total coverage, 40% small tests) {% cite winters2020swe %}. A plan states which level's practices the team commits to; no team needs Level 5 on day one.

The plan's fifth decision (by whom) is often written as a **RACI** table: for each activity, who is **R**esponsible (does it), **A**ccountable (decides), **C**onsulted and **I**nformed. Developers are responsible for their own unit tests; a release decision has one accountable person.

→ [V&V Activities by Phase](../../organization/02-plan/02-vv-activities-process-models.md)

## 7. IEEE 829 asks for decisions, not paperwork

IEEE 829-2008 defines a **Master Test Plan** for the whole effort and a **Level Test Plan** per test level. The level plan's key entries are features not to be tested (with reasons), item pass/fail criteria, and suspension criteria and resumption requirements {% cite ieee829-2008 %}. Every content topic must be **addressed**: documented before execution (in a document or a tool), documented after, left to the process, or excluded {% cite ieee829-2008 %}. "Excluded: no third-party components" is complete; a blank section is not. A small project can address the main topics on one page.

→ [Test Documentation (IEEE 829)](../../organization/02-plan/05-test-documentation-ieee829.md)

## 8. Effort can be estimated before code exists

The literature relies on two methods: Test Point Analysis (from function points, test strategy and a productivity factor) and Use Case Points adapted to testing {% cite bluemke2021testeffort %}. Without them, Miranda estimates on the back of an envelope {% cite miranda2015asa %}:

- **White box:** n + 1 tests cover the basis paths of code with n predicates; about 25% of lines are decisions, so 1,000 lines suggest about 250 tests.
- **Black box:** tests per function by complexity and criticality, from 8 (any non-critical function) to 40 (a complex critical one).
- **Life cycle:** total test cases = (1 + I + U) × the sum of tests per function, with I and U the extra fractions for integration and unit testing; effort = total × (design effort + (1 + R) × execution effort), with R the re-run fraction.

**Example.** 330 system-level tests with I = 0.5 and U = 0 give 495 test cases; at 0.5 hours to design and 0.25 hours to run, each run twice (R = 1), effort = 495 × (0.5 + 2 × 0.25) = 495 hours. A sanity check with Jones' rule (test cases ≈ function points^1.2) gives about 409 for a 150-function-point system, the same order of magnitude {% cite miranda2015asa %}.

→ [Estimating Test Effort](../../organization/02-plan/04-test-effort-estimation.md)

## 9. Reports must say "thirty percent of what"

"Testing is 30% done" can mean coverage against a model of good-enough testing, tasks done against the plan, comparison with past projects, the risks still open, results such as bugs found, obstacles, or effort spent {% cite kaner2000measurement %}. A report names its dimension and adds open risks. In continuous delivery, Ahmad et al. found 39 information needs; the most important was knowing who **owns** each test suite {% cite ahmad2024infoneeds %}.

→ [Test Status Reporting](../../organization/02-plan/06-metrics-dashboards-reporting.md)

## 10. Agile teams still need assurance

"We are agile, so we need no QA" does not hold: Scrum and XP assign process checking to the Scrum Master and the coach {% cite miranda2015asa %}. Koopman warns that "without an external check and balance it is all too easy to have a process failure" and asks three questions: show me your written process; how can an outsider tell you follow it; how can an outsider tell it produces the quality you want {% cite koopman2010agile %}?

→ [The Quality Plan](../../organization/02-plan/01-quality-management-planning.md)

## 11. Domains change the answers

The same eleven decisions get different answers in robotics (formal verification and simulation), IoT (interoperability and performance across devices), automotive (V-model, in-the-loop testing split between supplier and car maker) and model-based engineering (verifying models before building) {% cite araujo2022ras minani2024iot arcanjo2023automotive cederbladh2023vv %}.

→ [Domain-Specific V&V](../../organization/02-plan/07-domain-specific-vv.md)

## Where this goes next

The next sessions fill in the plan's activities: inspections (A05), static analysis (A06) and combinatorial testing (A07), each with its own cost and removal efficiency. The cost model behind the plan's trade-offs is in [SN A01: Cost of Quality](SN-A1-CoSQ.md); the revision questions for this lecture are in [RQ A04: Quality Planning](RQ-A4-QualityPlan.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
