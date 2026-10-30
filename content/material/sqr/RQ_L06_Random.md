---
title: "RQ L06: Random Testing"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 12
redirect_from: /content/verif/random/RQ_L06_Random.html
layout: default
page_type: question-set
---

# RQ L06: Random Testing

**Format:** Explain in your own words, give brief examples. These questions test the lecture summarised in [SN L06: Random Testing](SN_L06_Random.md).

---

## Question 1: What Makes Random Testing "Random"

What makes random testing "random" in the technical sense? What three conditions must be satisfied (Hamlet)?

---

## Question 2: Random vs Partition Testing

When would random testing be a poor choice compared to partition testing?

---

## Question 3: Statistical Confidence Limits

A colleague says "we ran 10,000 random tests with no failures, so the software is bug-free." What is wrong with this claim? Use the formula $$C = 1-(1-F)^N$$ with a true failure rate of $F = 1/10{,}000$.

---

## Question 4: Reliability Calculation

Calculate how many random tests are needed for 95% confidence that the failure rate is below 1 in 10,000.

*Hint: Use $N = \frac{\ln(1-C)}{\ln(1-F)}$*

---

## Question 5: Randoop Plateau Effect

What is the "plateau effect" in Randoop and what causes it?

---

## Question 6: Hamlet's Argument for Random Testing

Why did Hamlet (2006) argue that "only random testing will do" in certain scenarios? Name two such scenarios.

---

## Question 7: Oracle and Effectiveness

Explain the relationship between the oracle and the effectiveness of random testing. Compare crash detection, sanitizers, properties, and differential oracles.

---

## Question 8: Fuzz vs Random Testing

How does fuzz testing differ from random testing, and when would you choose fuzzing over general random testing?

---

## Question 9: Three Generations of Fuzzing

Explain the three generations of fuzzing (blackbox, whitebox, greybox). What does each add, and what does greybox fuzzing trade away compared with whitebox fuzzing?

---

## Question 10: Miller's Persistent Bug Classes

Miller's fuzz studies found the same bug classes persisting for 16 years (1990-2006). What does this tell us about the software industry?

---

## Question 11: Directed Greybox Fuzzing

What is directed greybox fuzzing and why was AFLGo able to find Heartbleed in 20 minutes when symbolic execution failed in 24 hours?

---

## Question 12: Crash Count vs Bug Count

Why is the finding that "57,142 unique crashes = only 9 actual bugs" (Klees 2018) important for fuzzing practice?

---

## Question 13: Evaluating Fuzzers

Klees et al. (2018) found that rankings of two fuzzers reversed between 6 and 24 hours of fuzzing. What should a fair comparison of two fuzzers include, and why?

---

## Question 14: QuickCheck Bug Distribution

QuickCheck's authors found the errors divided roughly evenly between generators, properties and programs. Why is this surprising, and what does it say about writing properties?

---

## Question 15: Hypothesis's Universal Representation

What problem does Hypothesis's "single universal representation" of test cases solve?

---

## Question 16: JQF Improvement

On the Trie example, pure random generation found no failure in over 7 million executions while JQF's Zest found one after about 5,000. What does Zest do differently?

---

## Question 17: PBT Property Challenge

In Goldstein et al. (2024), 16 of 30 practitioners said writing specifications slowed them down, and one called "not knowing what properties to test" the most common failure mode. Why is finding properties hard, and which kinds of property did practitioners use most?

---

## Question 18: PBT vs Fuzzing Time Budgets

Compare the time budgets of PBT (50ms-30s) versus fuzzing campaigns (hours/days). What does this reveal about their integration patterns?

---

## Question 19: Coupling Effect

Explain the coupling effect and why it justifies testing with simple mutations only.

---

## Question 20: Equivalent Mutant Problem

Why is the equivalent mutant problem the most persistent challenge in mutation testing? Use the share of equivalent mutants and the decidability of equivalence.

---

## Question 21: Small vs Large LLMs for Mutant Detection

In Tian et al. (2024), a fine-tuned code model of modest size (UniXCoder) outperformed prompting large general models such as GPT-4 on equivalent mutant detection. Suggest reasons why training on code and fine-tuning for the task could matter more than model size.

---

## Question 22: Crossfire Phenomenon

What is the "crossfire" phenomenon in mutation testing?

---

## Question 23: Mutation Score Target

A team achieves 85% mutation score. Should they aim for 100%? Why or why not? Consider equivalent mutants and the cost of each remaining survivor.

---

## Question 24: Mutation vs PBT Comparison

Compare how mutation testing and property-based testing approach test quality. How are they complementary?

---

## Question 25: Branch Coverage and Mutation

*(Relies on L04, Adequacy Criteria.)* What is the relation between branch coverage and mutation testing? Does one subsume the other?

---

## Question 26: Plain versus Guided Random Testing

Feedback-directed testing and coverage-guided fuzzing choose later inputs because of earlier results; property-based tools skew their distribution towards edge values. Which of Hamlet's conditions for a reliability figure does each break, and what can you then no longer claim?

---

## Question 27: Random Seeds

In the AutoTest study, one seed found 5 bugs and another 23 for the same class in 30 minutes. What does this mean for how you run and report random testing?

---

## Question 28: Oracle Problem

What is the oracle problem? Why is it the bottleneck of random testing?

---

## Question 29: Oracle Evolution

Trace the oracles across the four techniques: random testing, fuzz testing, property-based testing and mutation testing. (In mutation testing, the oracle is the test suite's own verdict: a mutant is killed when some test fails.)

---

## Question 30: Single Technique Selection

If you could only apply one technique from this lecture to a new project, which would you choose and why? Consider different project types.

---

## Question 31: Bug Classes vs Tool Progress

The same kinds of programming errors kept appearing across Miller's studies from 1990 to 2006, while recent models reach an F1-score of about 87% on equivalent mutant detection. What does this contrast suggest about where testing tools have improved and where they have not?

---

## Practical Problems

---

## Question 32: Testing Strategy Design (Practical)

Design a testing strategy for a JSON parser that combines techniques from all four topics (random testing, fuzzing, PBT, mutation testing).

---

## Question 33: JQF's Significance (Practical)

Why might JQF's convergence of PBT and fuzzing be the most significant development across all four topics? Explain how it resolves limitations of both approaches.

---

## Question 34: When Random Testing Stops Finding Bugs (Practical)

Randoop stopped finding new errors after about twelve hours, and Miller's fuzz studies kept finding the same kinds of bugs over sixteen years. What do these two observations say about how long a random-testing tool stays useful, and what would you change to keep finding bugs?

---

## Topics Covered

| Topic | Questions |
|-------|-----------|
| Random testing basics | Q1, Q2 |
| Reliability calculation | Q3, Q4 |
| Random testing theory | Q6, Q26 |
| Oracles | Q7, Q28, Q29 |
| Feedback-directed tools | Q5, Q27 |
| Fuzz testing | Q8, Q9, Q10, Q11, Q12, Q13 |
| Property-based testing | Q14, Q15, Q16, Q17, Q18 |
| Mutation testing | Q19, Q20, Q21, Q22, Q23 |
| Comparisons | Q24, Q25 (uses L04) |
| Cross-cutting | Q30, Q31 |
| Practical problems | Q32, Q33, Q34 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
