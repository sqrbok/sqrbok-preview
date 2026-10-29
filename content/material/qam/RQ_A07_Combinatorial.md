---
title: "RQ A07: Combinatorial Testing"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 14
redirect_from: /content/verif/combinatorial/RQ_A07_Combinatorial.html
layout: default
page_type: question-set
---

# RQ A07: Combinatorial Testing

These questions test [SN A07: Combinatorial Testing](SN_A07_Combinatorial.md); the handbook pages it links give the detail. Explain in your own words and give brief examples; for calculations, show each step.

---

## Question 1: Interaction Faults

a) Define an interaction fault and the FTFI number.

b) Write a small `if` condition that hides a fault with FTFI number 3. Which test inputs trigger it?

c) Why can testing every value of every input on its own miss your fault from (b)?

## Question 2: Reading the NIST Data

Use the table in SN §1.

a) What does "93% at 2" mean for the NASA system, in terms of tests you would write?

b) The browser's 2-way figure is 76%, the medical devices' 97%. Give one possible reason for the difference. (Hint: compare the kind of system, its size and its release stage, from the list of systems in SN §1.)

c) What is the largest FTFI number in any of the four systems? What does "pseudo-exhaustive" claim, and why "pseudo"?

d) Name two reasons to be careful before applying these numbers to your own system.

## Question 3: A Covering Array by Hand

Three parameters A, B and C each take the values 0 and 1.

a) How many pairs of values must a pairwise array cover? Use C(*k*, *t*) · *v*<sup>*t*</sup>, where *k* is the number of parameters, *t* the interaction strength (2 for pairwise) and *v* the number of values per parameter.

b) What is the lower bound on the number of rows? Construct an array that reaches it.

c) Is your array also an orthogonal array? Explain the difference between the two kinds.

d) Compare its size with exhaustive testing.

## Question 4: Logarithmic Growth

A system has 20 parameters with 3 values each.

a) How many tests does exhaustive testing need?

b) How many pairs must a pairwise suite cover?

c) Estimate the pairwise test count with Cohen's rule of thumb, 4 · log<sub>2</sub> *k*.

d) Doubling the parameters to 40 changes the estimate only a little. Explain why, using the greedy argument in SN §4.

e) Why does raising the strength from 2 to 3 cost far more than doubling the parameters? Use the IPOG figures for 10 parameters with 5 values.

## Question 5: Tracing IPOG

Parameters: P1 in {0, 1}, P2 in {0, 1, 2}, P3 in {0, 1}; strength 2; ties go to the smallest value.

a) Write the six starting rows for P1 and P2.

b) Apply horizontal growth for P3, row by row. For each row, write how many new pairs each value of P3 would cover.

c) Which pairs remain uncovered? Does vertical growth add any rows?

d) Compare the result with the trace in SN §7, where vertical growth was needed. What made the difference?

## Question 6: AETG and IPOG

a) State the difference in one sentence each: "one test at a time" and "one parameter at a time".

b) What does AETG do with its 50 candidates, and why is it not deterministic?

c) What is a don't-care position, and when does IPOG fill it?

d) Which generator does NIST's ACTS use by default?

e) Zeb et al. sorted search-based strategies into five kinds. Name them, and say which algorithm stood out ([Covering Array Generation](../../verif/combinatorial/algorithms.md)).

## Question 7: Base Choice versus Pairwise

a) Compute the base choice test count for the Find dialog in SN §5, and explain the formula.

b) For four parameters with three values each, base choice gives 9 tests that cover 30 of 54 pairs. Show where 54 comes from.

c) Grindal et al. found base choice as good as pairwise in one experiment. Why not use it everywhere? What did the authors advise?

## Question 8: Constraints

PICT generated these 9 pairwise rows for OS × Browser × Language, without the constraint "Safari runs only on macOS":

| OS | Browser | Language |
|---|---|---|
| Windows | Safari | EN |
| macOS | Chrome | EN |
| Windows | Firefox | DE |
| Linux | Chrome | DE |
| Windows | Chrome | DE |
| macOS | Firefox | EN |
| macOS | Safari | DE |
| Linux | Firefox | EN |
| Linux | Safari | DE |

a) Which rows are invalid?

b) Delete them. Which valid pairs are no longer covered?

c) How many valid pairs are there in total? Explain how a constraint-aware generator avoids the loss, and why it may need an extra row.

d) When would you choose CASA over PICT for a constrained model? Use the 21-second finding.

## Question 9: Invalid Values and Masking

a) What is the masking effect? Give an example with two invalid inputs to an order form.

b) State the rule for invalid values in a combinatorial suite.

c) How does PICT's `~` prefix apply the rule?

## Question 10: Industrial Evidence

a) Hu et al.'s pairwise suites found 93.3% of faults needing several inputs; the in-house teams found 6.7%. Why is the gap so large?

b) For faults needing one input the gap was smaller (89.3% against 71.4%). Where did the combinatorial team's advantage come from there?

c) Why did Hu et al. use only pairwise suites? Name one other barrier they report.

d) At Tricentis, configuration testing fell from 394 to 205 hours. Compute the saving and say what made it a fair comparison.

## Question 11: Is Pairwise Enough?

a) Summarize Bures and Ahmed's result: suite sizes, and in how many mutated versions only 3-way found the fault.

b) About 40% of the tests in each suite found nothing. What does that say about efficiency per test?

c) A medical-device team uses pairwise only. What would you recommend, and why?

## Question 12: Choosing Strength

A web application has authentication parameters (username format, password state, MFA method; security-critical), UI parameters (theme, language, font size; low risk) and payment parameters (card type, amount class, currency; medium risk).

a) Recommend a strength for each group and justify it.

b) How would a mixed-strength array help, and what did mixed strength cost at Tricentis?

c) If the pairwise suite finds no failures, is the system correct? Explain.

## Question 13: Calculation

A mobile app is tested on OS {iOS, Android}, Browser {Chrome, Safari, Firefox}, Screen {Small, Medium, Large}, Network {WiFi, 4G, 3G} and Language {EN, FR, DE}.

a) Compute the exhaustive test count.

b) What is the lower bound for a pairwise suite? PICT generated 13 rows. Why can a tool exceed the bound?

c) At 10 minutes per test, compare the time for exhaustive and for the 13-row pairwise suite.

d) Add the constraint "Safari only on iOS". Which pairs are no longer required? Should you delete rows or regenerate? Explain.

## Question 14: Tools and Seeds

a) Match each tool to a situation: PICT, ACTS, CASA. (i) a model regenerated in every CI build; (ii) a heavily constrained model with expensive tests; (iii) a team that wants the tool used in most industrial studies.

b) A team has a pairwise suite of 500 tests and needs 3-way coverage. Should it use the suite as seeds? What did Bombarda and Gargantini find?

c) When are seeds worth using anyway?

## Question 15: Combinatorial Testing in New Settings

a) Tao et al. generated 319 scenarios for an emergency-braking function. Why simulation, and what was missing from the study?

b) Lanus et al. found that a satellite-image set of 10,849 images covered 67 of 72 label-centred pairs, while a set of 21,151 covered 60. Compute both coverage values and explain where 72 comes from (12 features, 3 bins, 2 labels).

c) What does (b) imply for judging a training set by its size?

---

## Topics Covered

| Topic | Questions |
|---|---|
| Interaction faults and NIST data | Q1, Q2 |
| Covering arrays and growth | Q3, Q4, Q13 |
| Generation algorithms | Q5, Q6 |
| Input models and base choice | Q7, Q13 |
| Constraints and invalid values | Q8, Q9, Q13 |
| Industrial evidence and strength | Q10, Q11, Q12, Q15 |
| Tools and seeds | Q14 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
