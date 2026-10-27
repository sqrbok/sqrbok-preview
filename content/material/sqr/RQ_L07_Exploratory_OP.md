---
title: "Revision Questions: Exploratory Testing & OP"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 14
redirect_from: /content/verif/exploratory/RQ_L07_Exploratory_OP.html
layout: default
page_type: question-set
---

# Exploratory Testing & Operational Profile — Revision Questions

**Format:** Explain in your own words, give brief examples. These questions test the lecture summarised in [Study Notes: Exploratory Testing & Operational Profile](SN_L07_Exploratory_OP.md).

---

## Question 1: ET vs OP Fundamental Difference

What is the fundamental difference between exploratory testing and operational profile-based testing?

---

## Question 2: Scripted and Exploratory Together

Why does the lecture recommend using scripted and exploratory testing together? What does each approach do well, and what does each miss?

---

## Question 3: ET vs Ad-Hoc Testing

How does exploratory testing differ from ad-hoc testing?

---

## Question 4: Coupon System Example

Explain the coupon system example. Why couldn't scripted testing find those bugs?

---

## Question 5: Charter Definition

What is a charter and why is it important for exploratory testing? Give the template format.

---

## Question 6: Hendrickson's Heuristics

Describe three of Hendrickson's heuristics and give an example for each.

---

## Question 7: Session-Based Test Management

Explain Session-Based Test Management (SBTM). Why was it created? What are its four parts?

---

## Question 8: Whittaker's Districts

Name four of Whittaker's tour districts, say what each focuses on, and give one tour from each.

---

## Question 9: Five Levels of Exploration

Describe the five levels of exploration (Ghazi et al. 2017). When should you use each?

---

## Question 10: ET Effectiveness Evidence

Summarize the experimental evidence comparing ET with test-case-based testing. What did the experiments measure, what did they find for defects and for effort, and how does a fixed total time change the result?

---

## Question 11: Defending ET to Management

A manager says "ET is just ad-hoc testing." How would you respond with evidence?

---

## Question 12: More Defects, Less Coverage

In Shah et al.'s comparison, exploratory testing found the most defects but covered the fewest specified functions. How can both be true, and what does it imply for planning tests?

---

## Question 13: Operational Profile Definition

What is an operational profile? Give a concrete example with probabilities.

---

## Question 14: Function vs Operation vs Run

Explain the difference between a function, an operation, and a run (Musa 1993).

---

## Question 15: Operational Modes

What is an operational mode? Give three examples.

---

## Question 16: Profile Probabilities Sum to 1.0

Why must operational profile probabilities sum to 1.0? What happens if the sum is greater or less?

---

## Question 17: 6-Step OP Procedure

Walk through the six-step OP procedure using a university enrollment system as an example.

---

## Question 18: Tabular vs Graphical Representation

When should you use tabular vs. graphical (tree) representation for an operational profile?

---

## Question 19: OP Calculation (Practical)

Calculate the operational profile for this system:

| Endpoint | Rate/hr |
|----------|---------|
| GET /users | 4,000 |
| POST /orders | 2,500 |
| GET /products | 3,000 |
| PUT /users | 500 |

---

## Question 20: AT&T and HP Results

What were the results of OP adoption at AT&T and HP? Why should the AT&T figure not be quoted as the effect of the profile alone?

---

## Question 21: Operational Coverage

Explain usage-weighted coverage and operational coverage (the published 1/3/9 rule), each with a worked example. How do they differ from traditional coverage?

---

## Question 22: Android Cloud Profiles

What are Android Cloud Profiles and why do they matter? What percentage of methods do users exercise?

---

## Question 23: Saturation

What is saturation in profile-based testing? Why do OP and ET complement each other once it occurs? No study gives a threshold for switching: which signals would you use to decide?

---

## Question 24: Two-Phase Testing Strategy

Describe the two-phase testing strategy. When should OP come first, and what would you do first without usage data?

---

## Question 25: Startup Testing Decision

A startup has limited testing time. Should they build an OP or train testers in ET? Why?

---

## Question 26: Accountability Mechanisms

SBTM makes exploratory testing reportable through session reports and debriefings. What does an operational profile, together with operational coverage, give a manager to judge how much testing each operation received and when usage-based testing is enough?

---

## Question 27: Banking App Strategy (Practical)

Design a testing strategy for a banking mobile app using both OP and ET. Include specific charters and tours.

---

## Question 28: Operational vs Traditional Coverage

Why does coverage weighted by use (usage-weighted or operational) give a more useful signal than traditional coverage? Name which one you use.

---

## Question 29: Cloud Profiles Validation

Explain how Android Cloud Profiles support the claim "you cannot assume how users will use your software." What did comparing test tools by profile coverage show?

---

## Question 30: Lecture Summary

Explain the entire lecture in 5 minutes. Cover ET, OP, and how they bridge together.

---

## Topics Covered

| Topic | Questions |
|-------|-----------|
| ET vs OP overview | Q1, Q2 |
| Exploratory testing | Q3, Q4, Q5, Q6, Q7, Q8, Q9, Q10, Q11, Q12 |
| Operational profile definition | Q13, Q14, Q15, Q16 |
| Developing operational profiles | Q17, Q18, Q19 |
| Evidence & modern OP | Q20, Q21, Q22 |
| ET + OP integration | Q23, Q24 |
| Cross-cutting & practical | Q25, Q26, Q27, Q28, Q29, Q30 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
