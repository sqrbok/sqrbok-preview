---
title: "RQ L03: Verification Methods"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 6
redirect_from: /content/verif/overview/RQ_L03_Verification.html
layout: default
page_type: question-set
---

# RQ L03: Verification Methods

**Format:** Explain in your own words, give brief examples. These questions test the lecture summarised in [SN L03: Verification Methods](SN_L03_Verification.md).

---

## Question 1: Four Verification Methods

List the four verification methods (inspection, analysis, testing, demonstration) and give a one-sentence definition of each.

Two questions separate them: is the product run, and does the evidence come from people's observation or from calculation and measured data? Place each method on these two questions, and give one software requirement for which each method is the most appropriate choice.

---

## Question 2: Verification or Validation?

A program does exactly what its specification says, and users still cannot do their job with it. Has it failed verification, validation, or both? Explain with the two questions "did we build the product right?" and "did we build the right product?".

---

## Question 3: The Review Spectrum

Name **four** review types from the most to the least formal. Explain how a walkthrough differs from an inspection: who leads it, and what the participants do before the meeting.

---

## Question 4: Inspection vs Testing

Compare inspection and testing across **three** dimensions: what they observe (fault or failure), when they can start, and which kinds of fault they find. Explain why using both is recommended.

---

## Question 5: Safety vs Liveness Properties

Define **safety** and **liveness** properties and give one software example of each.

Why is a safety violation easier for a model checker to show than a liveness violation? Describe the shape of the counterexample in each case.

---

## Question 6: Writing a Property in LTL

Using `[]` (always), `<>` (eventually) and `U` (until), write:

(a) "the account balance is never negative";
(b) "every placed order is eventually confirmed or rejected".

Which of the two is a safety property? Give one correct behaviour of a real shop that would make (b) fail, and say what that shows about proved properties.

---

## Question 7: Rice's Theorem — Sound vs Complete

Explain the trade-off between **soundness** and **completeness** in static analysis, using the terms false positive and false negative.

What does Rice's theorem tell us about the limits of static analysis? Why do practical bug finders accept both kinds of error instead of being sound?

---

## Question 8: What a Clean Result Means

pylint rates a function 10/10 and mypy reports no issues, yet the function divides by zero on an empty list. Explain why neither tool contradicts itself. What would a program verifier require before accepting the division?

---

## Question 9: Fault, Error, Failure

`num_zero` should count the zeros in a list, but its loop starts at index 1. For the inputs `[2, 7, 0]` and `[0, 7, 2]`, say whether each run reaches the fault, enters an error state, and fails. Use reachability, infection and propagation in your answer.

---

## Question 10: Test Levels

Describe the testing levels unit, integration, system and acceptance. For each, identify: (a) what the test is checked against and (b) one example of a defect that would be found at that level and not at a lower one.

---

## Question 11: Test Doubles

Distinguish a stub, a fake and a mock. What can a mock detect that a stub cannot?

Why does Google prefer real implementations, then fakes, and advise against overusing interaction testing?

---

## Question 12: Black Box vs White Box

Compare black-box and white-box testing. For each, describe: (a) where the tests come from, (b) what kind of gap it cannot see, and (c) one technique associated with it.

A white-box test asserts that a 1,200 € order pays 20 € shipping, a value taken from the code because the specification is silent. What is wrong with this test's oracle?

---

## Question 13: Google's Test Pyramid

*Software Engineering at Google* recommends a mix of about 80% unit, 15% integration and 5% end-to-end tests.

What do these percentages count? Explain **why** this shape is recommended. What is the "ice cream cone" anti-pattern and why is it a problem? Is the ratio a measured optimum?

---

## Question 14: Test-Driven Development

Describe the red–green–refactor cycle. The industrial case studies at Microsoft and IBM reported 40–90% lower defect density, while controlled experiments with TDD newcomers found no quality gain. Give two reasons the results may differ.

---

## Question 15: Demonstration

Define demonstration and explain how it differs from testing. Give **two** examples of demonstration activities in software development, and one property that can only be verified this way.

---

## Question 16: Defense in Depth

Different verification methods suit different quality characteristics. For **maintainability**, **usability** and **reliability**, say which method or methods you would rely on most and why. Then explain why a team should not rely on one method alone, using a finding about the fault types different methods detect.

---

## Topics Covered

| Topic | Questions |
|-------|-----------|
| Four methods, verification and validation | Q1, Q2, Q15, Q16 |
| Inspection | Q3, Q4 |
| Analysis | Q5, Q6, Q7, Q8 |
| Testing fundamentals | Q9, Q10, Q11, Q12 |
| Test mix and TDD | Q13, Q14 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
