---
title: "RQ A05: Software Inspection"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 10
redirect_from: /content/verif/inspection/RQ_A05_Inspection.html
layout: default
page_type: question-set
---

# RQ A05: Software Inspection

These questions test [SN A05: Software Inspection](SN_A05_Inspection.md). Explain in your own words and give brief examples; for calculations, show each step.

---

## Question 1: Evolution of Inspection

Inspection practice changed considerably between Fagan's IBM method of 1976 and the LLM reviewers of 2025.

Trace the evolution through its five stages: Fagan's method and the four changes that followed. What changed at each stage? Which principles stayed the same?

## Question 2: Review Types

Wiegers orders peer reviews from most to least formal: inspection, team review, walkthrough, pair programming, peer deskcheck, passaround, ad hoc review.

Compare inspection, walkthrough, peer deskcheck and ad hoc review by who leads, whether reviewers prepare, and how formal they are. What do the telecommunications and Ford data say about inspections against lighter reviews? Which type would you choose for a security-critical interface, and which for a one-line fix?

## Question 3: Cost-Benefit of Inspection

Finding a defect takes a most likely 1.46–1.58 staff-hours by inspection and 6 staff-hours by testing.

Explain why inspection is often cheaper per defect than testing. How does the cost of fixing a defect grow from development to testing to production?

## Question 4: Fagan's Six Steps

Fagan defined six steps: planning, overview, preparation, meeting, rework and follow-up.

Describe what happens in each step and who is responsible. Why do entry and exit criteria matter? Why is the follow-up not a formality?

## Question 5: The Four Roles

Fagan's inspection uses four roles: moderator, author, reader and tester.

Describe each role's responsibility. Why must the reader not be the author? Why should inspection results never be used to evaluate the author?

## Question 6: Ground Rules for Effective Meetings

Fagan's meetings follow rules such as "find problems, don't solve them" and "at most two hours".

List at least four ground rules for an inspection meeting and the reason for each. Why does "criticize the product, not the producer" matter for the team?

## Question 7: Reading Techniques

Reading techniques range from ad hoc reading to perspective-based reading (PBR).

Compare ad hoc, checklist-based, scenario-based and perspective-based reading. Which technique improved individual detection by 21–30%? In which part of the inspection are 90–95% of defects found?

## Question 8: Perspective-Based Reading

PBR assigns the perspectives of tester, designer and user.

For each perspective, give the work product the reviewer builds and a kind of defect it exposes. Why does the rule "where the model cannot be built, there is a defect" make PBR work?

## Question 9: Checklist Design

Checklists are the most common reading aid for code.

Give three things a good checklist does and three things a bad one does. Why are checklists most effective at finding omissions?

## Question 10: Cisco Study: Size

A study of 2,500 reviews of 3.2 million lines of code by 50 programmers at Cisco related review size to defects found.

What happens to the defects found per line when more than 400 lines are reviewed at once? What is the recommended size per sitting, and why?

## Question 11: Review Rate

Review speed affects what reviewers find.

According to the Cisco data, above what rate does a reviewer stop reviewing and start rubber-stamping? McIntosh and colleagues used a stricter threshold, 200 lines per hour: what did they find about changes reviewed faster than that, or approved without discussion?

## Question 12: The Magic Numbers

Three numbers summarize the Cisco recommendations.

State the limits on size, rate and duration of a review session. What did a SmartBear customer gain by introducing code review, and how was the gain measured?

## Question 13: Google Modern Code Review

Sadowski and colleagues studied code review at Google.

Give Google's median change size, number of reviewers and review time. How do these compare with Fagan's parameters? What do Google's developers see as the main purposes of review?

## Question 14: Microsoft Findings

Bosu and colleagues studied 1.5 million review comments at Microsoft.

What share of comments was useful? How does a reviewer's previous work on the file change the usefulness of their comments? What does this mean for choosing reviewers?

## Question 15: Anti-Patterns

Some practices destroy the value of reviews.

List at least four anti-patterns and why each is harmful. Why should review metrics never be used in performance evaluations?

## Question 16: Defect Seeding

Mills' defect seeding estimates the number of real defects from planted ones.

Explain the steps of the method. Given 12 planted defects, 4 planted defects found and 11 real defects found, estimate the total number of real defects and the number still in the product. What assumption does the estimate depend on?

## Question 17: The Lincoln–Petersen Estimate

Capture-recapture comes from ecology.

State the Lincoln–Petersen formula and explain why it works. Inspector A finds 20 defects, inspector B finds 15, and 6 are found by both. Estimate the total and the remaining defects.

## Question 18: Capture-Recapture Models

Briand and colleagues compared four models: M<sub>0</sub>, M<sub>t</sub>, M<sub>h</sub> and M<sub>th</sub>.

What does each model allow to vary? Which model and estimator do they recommend, and with how many inspectors at least?

## Question 19: Capture-Recapture Limitations

Capture-recapture estimates rest on assumptions.

List at least three limitations. What happens when the overlap *m*<sub>2</sub> is zero? Why do the estimates tend to be too low?

## Question 20: LLM Code Review

Zeng and colleagues evaluated LLM-based reviewers on SWR-Bench.

What F1 score did the best reviewer reach, and what held it back? Why does a person still have to review the change?

## Question 21: Improving LLM Review

LLM reviewers differ in what they find and how reliably.

On which kind of issue did the tools do better, functional errors or documentation and style? What technique raised F1 by up to 43.67%, and how does it resemble using several independent inspectors?

## Question 22: Inspection versus Testing

Inspection and testing are complementary.

Build a table comparing them by when they can start, what kinds of defects each finds, and effort per defect. Why should a project use both?

---

## Practical Problems

## Question 23: Capture-Recapture Calculation

Four inspectors log 13 distinct defects in one module, identified by line number:

| Line | Inspector A | Inspector B | Inspector C | Inspector D |
|------|:-----------:|:-----------:|:-----------:|:-----------:|
| 8    | x | | x | x |
| 12   | | x | | x |
| 33   | | x | | |
| 55   | | x | | |
| 56   | x | | | |
| 78   | | | x | |
| 120  | | x | | |
| 145  | | | x | x |
| 146  | | x | | |
| 230  | | | | x |
| 255  | | | | x |
| 300  | | x | | |
| 321  | x | | x | |

a. Count *S* and *f*<sub>1</sub>, then apply the first-order jackknife. How many defects remain?
b. Treat inspector B as one "inspector" and A, C and D together as the other, and apply Lincoln–Petersen.
c. Apply Lincoln–Petersen to the pairs A–B and B–D. What goes wrong?
d. Why do the estimates differ so much, and which would you report? Hint: look at the size of each overlap.

## Question 24: Inspection Prioritization Under Budget

You must inspect a banking system of about 20,000 lines of COBOL and 25,000 lines of new Java code for vulnerabilities, but the budget covers only 10,000 lines.

How would you choose what to inspect? Which criteria would you use, and why? Hint: apply Howard's criteria (age of the code, privileges, exposure to the network, language, history of vulnerabilities) to the old COBOL and the new Java separately. Note that COBOL and Java are memory-safe in the usual sense, unlike C and C++. State any assumptions you make about the system.

## Question 25: Egoless Programming

Wiegers builds on Weinberg's idea of "egoless programming".

What does it mean? Why does it matter for peer review, and how does it relate to the rule "criticize the product, not the producer"?

## Question 26: Session Limits

The Cisco study sets limits on tool-assisted code review sessions.

State a guideline, with its justification, for:
a. the number of lines inspected in one sitting;
b. the review rate in lines per hour;
c. the maximum session length.

Why are Fagan's limits for a formal inspection meeting (about 125 statements per hour, two hours) tighter?

## Question 27: Undesirable Attitudes

Mandatory reviews can create negative attitudes.

Describe two undesirable attitudes or behaviours that can appear in an organization with mandatory reviews. Describe three things that management, the organization or the programmers can do to prevent them.

## Question 28: Good and Bad Practices

Describe, in your own words, two good inspection practices and why they work, and two bad practices and why they should be avoided.

---

## Topics Covered

| Topic | Questions |
|-------|-----------|
| History and review types | Q1, Q2 |
| Cost and effectiveness | Q3, Q22 |
| Fagan process | Q4, Q5, Q6, Q25 |
| Reading techniques | Q7, Q8, Q9 |
| Lightweight and modern code review | Q10, Q11, Q12, Q13, Q14, Q26 |
| Anti-patterns and culture | Q15, Q27, Q28 |
| Defect seeding | Q16 |
| Capture-recapture | Q17, Q18, Q19, Q23 |
| LLM code review | Q20, Q21 |
| Prioritization | Q24 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
