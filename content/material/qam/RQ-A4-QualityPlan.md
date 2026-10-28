---
title: "RQ A04: Quality Planning"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 8
redirect_from: /content/organization/02-plan/RQ-A4-QualityPlan.html
layout: default
page_type: question-set
---

# RQ A04: Quality Planning

Revision questions for [SN A04: Quality Planning](SN-A4-QualityPlan.md). Each can be answered from the study notes and the linked handbook pages. Where a question asks for a number, show the calculation.

---

## Part 1: The plan

### Question 1: Why a plan

Explain why quality activities are dropped under schedule pressure, and how a documented plan protects them. Why does the need for a plan grow when the same people do development and quality work?

### Question 2: Assurance and control

Explain the difference between quality assurance and quality control, with one example of each for a web project. Why does a project need both?

### Question 3: The eleven decisions

List the eleven decisions a quality plan records. For a three-week student project, which three decisions would you write first, and why?

### Question 4: Drivers

Name the four things that drive a plan's answers. For a mobile banking app, give one concrete consequence of each driver for the plan.

### Question 5: Agile and assurance

A team says: "We use Scrum, so we need no quality assurance." Answer them using Koopman's three questions and the roles that Scrum and XP already assign.

---

## Part 2: Activities and rigor

### Question 6: Minimum V&V tasks

Using Wallace and Fujii's minimum tasks, name the V&V tasks for the requirements and design phases. Why does test planning start in these phases rather than after coding?

### Question 7: Is independent V&V worth it?

Summarize the two studies Wallace and Fujii report on independent V&V. Which project conditions made V&V pay off, and what does that imply for a small web project?

### Question 8: Criticality

1. For DO-178B levels A, C and E, give the failure condition and the structural coverage objective.
2. Why does Miranda write gate criteria per criticality class before knowing which modules are critical?
3. Your team builds a parking-payment app with an admin dashboard and a payment module. Assign each to one of Miranda's three classes and justify the choice.

### Question 9: Writing a gate criterion

Write two exit criteria for a "merge to main" gate, each with its metric, desired value, predicate and object. Explain why "the code is good enough" is not a criterion.

### Question 10: Phased gates

Microsoft raised its coverage gate from 65% to 75% to 80% across milestones. Give one advantage and one risk of phasing a gate instead of requiring 80% from the start.

### Question 11: Bug bar

Before a release, the team has five open bugs: one causes data loss, two have workarounds, two are cosmetic. The plan's bug bar says: no open severity-1 bug, at most two open severity-2 bugs. Assign severities, decide whether the release may ship, and say who should be accountable for the decision.

### Question 12: Maturity and roles

1. Name the five TMMi levels.
2. Compare Google's Test Certified Level 1 with Level 5.
3. Write a RACI row for "release decision" in a team with developers, a tester and a product owner.

---

## Part 3: Documents, effort, reports

### Question 13: Addressed, not filled in

What does IEEE 829 mean by a topic being "addressed"? Give one topic a small project could legitimately exclude, with the sentence that addresses it.

### Question 14: Level test plan

Write the "features not to be tested" and "suspension criteria and resumption requirements" entries of a system test plan for an online shop.

### Question 15: Estimating effort

A system has 12 functions: 8 simple and non-critical, 4 complex and critical. Use Miranda's table of tests per function.

1. How many system-level tests are needed?
2. With I = 0.5 and U = 0, how many test cases in total?
3. With 0.5 hours to design a test, 0.2 hours to run it, and R = 0.5, how many hours?
4. The system measures 60 function points. Sanity-check your total with Jones' rule.

### Question 16: Thirty percent of what

A test lead reports "we are 60% done". Name three different things this could mean, and rewrite the report so that a manager can act on it.

### Question 17: Test suite ownership

Why did practitioners rate knowing who owns a test suite as their most important information need in continuous delivery? What happens to a failing suite that nobody owns?

---

## Topics covered

| Topic | Questions |
|---|---|
| The plan and its decisions | Q1, Q2, Q3, Q4, Q5 |
| V&V activities and criticality | Q6, Q7, Q8 |
| Quality gates | Q9, Q10, Q11 |
| Maturity and roles | Q12 |
| Documentation | Q13, Q14 |
| Effort estimation | Q15 |
| Reporting | Q16, Q17 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
