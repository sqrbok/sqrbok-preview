---
title: "RQ A01: Cost of Quality"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 2
redirect_from: /content/organization/01-coq/RQ-A1-CoSQ.html
layout: default
page_type: question-set
---

# RQ A01: Cost of Quality

Revision questions for [SN A01: Cost of Quality](SN-A1-CoSQ.md). Each can be answered from the study notes and the linked handbook pages. Where a question asks for a number, show the calculation.

---

## Part 1: The cost model

### Question 1: Money as the unit

Why express quality problems in money rather than in defect counts? Rewrite the statement "defect density rose from 0.8 to 1.4 per thousand lines" as a sentence a manager would act on, naming the cost category that will grow.

### Question 2: Four categories

Classify each item as prevention, appraisal, internal failure or external failure, and justify each choice in one line:

1. a coding-standard training day;
2. running the regression suite;
3. debugging a failure the regression suite found;
4. a hot fix after a customer complaint;
5. a code inspection.

Then explain why items 2 and 3 belong to different categories although one engineer may do both in an afternoon.

### Question 3: Reading a CoSQ split

A release took 2,000 hours: 100 hours of training and reviews, 500 of testing, 300 of debugging and fixing test failures, 100 of customer-reported fixes, and the rest on development.

1. Compute CoSQ in hours and as a share of the release.
2. Compute the ratio of failure cost to prevention cost.
3. Where would Knox advise investing the next hour, and why?

### Question 4: Maturity and the optimum

1. What does Knox's model predict for total CoSQ between CMM Level 1 and Level 5?
2. What did Raytheon's rework data show?
3. The traditional model places an optimum short of perfection. In Houston and Keats' simulation, what happened to the minimum total CoSQ and to its location when inspections were added? What does this say about the traditional model's optimum?

---

## Part 2: Defect flow

### Question 5: Tank and pipes

Describe the tank-and-pipes model. What are residual defects, and what are the only two ways to reduce them?

### Question 6: Process signature

In a project, 60 defects reach testing and each of three test levels removes 40% of the defects present.

1. How many defects ship?
2. Sketch the process signature (cumulative injected and removed by phase).

### Question 7: Two improvement options

Use Miranda's model: each development phase doubles the defects it receives, 10 defects exist after requirements, three development phases follow (high-level design, low-level design, code), and three test levels remove 50% each.

1. Compute the shipped defects if unit testing improves to 70%.
2. Compute the shipped defects if 20% inspections are added after requirements, high-level design and code.
3. How many defects are created in each option? Why does option 2 create fewer?
4. Name the two pieces of information you would need to decide which option is cheaper.

### Question 8: Removal efficiency in practice

Knox lists typical removal efficiencies. A project relies on unit testing (40%) and system testing (40%) only. What fraction of the defects present before testing ships? How would adding a formal inspection (65%) before testing change it?

---

## Part 3: Classification

### Question 9: Origin and detection

Kan's project recorded 122, 859, 939 and 1,537 defects originating in requirements, high-level design, low-level design and code. The high-level design inspection found 730, the low-level design inspection 729 and the code inspection 1,095.

1. Compute the DRE of the code inspection.
2. Of 3,465 defects, 81 were found in the field. Compute the overall DRE before release.
3. List the three data sources a CoQ programme needs and what each provides.

### Question 10: Richer schemes

1. What does ODC record when a defect is found, and what when it is fixed?
2. Huber classified the same defects with ODC and with HP's scheme. Why did HP attribute most of them to design while ODC pointed at the code?
3. Why does HP weight a specification defect 14.25 and a code defect 2.5?

---

## Part 4: Modern practice

### Question 11: Flaky tests

From Leinen et al.'s case, explain in cost-of-quality terms why the company raised the number of automatic reruns. Name one risk of this strategy.

### Question 12: Code review value

A team's review comments are mostly about documentation and naming. Using the findings of Turzo and Bosu and of Yu et al., propose two changes that would raise the value of reviews, and say which finding supports each.

### Question 13: Technical debt

1. Define the principal and the interest of technical debt, and place each in a CoSQ category.
2. Why should a team start repayment with the classes that several tools rank high?

### Question 14: Defect prediction

A defect-prediction model reports 95% accuracy on a data set where 5% of modules are defective. Why is this result not enough to adopt the model? Which measures would you ask for instead?

---

## Topics covered

| Topic | Questions |
|---|---|
| Cost categories and their size | Q1, Q2, Q3 |
| Maturity and the optimum | Q4 |
| Defect injection and removal | Q5, Q6, Q7, Q8 |
| Classification and removal efficiency | Q9, Q10 |
| Continuous delivery, review, debt, prediction | Q11, Q12, Q13, Q14 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
