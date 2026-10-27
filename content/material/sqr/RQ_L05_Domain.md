---
title: "RQ L05: Input Domain Testing"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 10
redirect_from: /content/verif/domain/RQ_L05_Domain.html
layout: default
page_type: question-set
---

# RQ L05: Input Domain Testing

**Format:** Explain in your own words, give brief examples. These questions test the lecture summarised in [SN L05: Input Domain Testing](SN_L05_Domain.md). Classification trees belong to the master's lecture A08 and are not asked here.

---

## Question 1: Core Definitions

Match each testing term with its definition:

| Term | Definition |
|------|------------|
| A. Test Case | 1. Mechanism to determine correct output |
| B. Test Suite | 2. Values provided to system |
| C. Oracle | 3. Input + expected output |
| D. Test Input | 4. Collection of test cases |

---

## Question 2: Positive Test Bias

What is the positive test strategy (positive test bias)? Which tests does it tend to leave out, and why would line or branch coverage not reveal the gap?

---

## Question 3: Black-Box vs White-Box

A tester has no access to source code and must test a payment gateway API. What testing approach should they use and why?

---

## Question 4: Partition Requirements

What two properties must equivalence partitions satisfy?

---

## Question 5: Weak vs Strong EP

A system has three input variables with the following **valid** equivalence classes:
- Variable A: 4 valid classes
- Variable B: 3 valid classes
- Variable C: 5 valid classes

a) How many tests are needed for weak EP? For strong EP?
b) Variable A also has 2 invalid classes and variable C has 1. How many tests does each approach need now, and why are the invalid classes counted separately?

---

## Question 6: Finding Equivalence Classes

For a function that validates email addresses, identify at least 5 equivalence classes.

---

## Question 7: Partition Heuristics

Match the heuristic to its example:

| Heuristic | Example |
|-----------|---------|
| A. Range-based | 1. US regions (Northeast, South, Midwest, West) |
| B. Enumeration | 2. Age groups (0–12, 13–19, 20–64, 65–120) |
| C. Error types | 3. Input that causes "File not found" error |

---

## Question 8: Boundary Fault Types

Classify each bug by its boundary fault type:

| Bug | Type |
|-----|------|
| A. `if (x < 10)` should be `if (x <= 10)` | ? |
| B. `if (x < 10)` should be `if (x < 20)` | ? |
| C. Upper bound check is missing | ? |

For an integer `x`, could any test tell bug A apart from the shift `if (x <= 9)`? Explain.

---

## Question 9: ON/OFF Points

For the condition `x >= 100`, identify:
a) The ON point
b) The OFF point
c) Is the boundary open or closed?

---

## Question 10: BVA Test Count

A function takes 3 numeric inputs.
a) Testing each input at min, min+, max− and max while the others stay nominal, plus one all-nominal test: how many tests?
b) Extreme point combinations (under, min, max, over for every input, all combined, plus one nominal point): how many tests?
c) Why does one count grow linearly and the other exponentially, and what does each miss? (Note: "weak" in Weak 1×1 is unrelated to weak EP.)

---

## Question 11: Closed vs Open Intervals

For the valid range $1 < x < 100$, which test points should be used?

---

## Question 12: BVA Limitations

Name three limitations of boundary value analysis.

---

## Question 13: Decision Table Components

What are the four main components of a decision table?

---

## Question 14: Rule Count

A decision table has 3 binary conditions (Y/N). How many rules are needed before consolidation?

---

## Question 15: Checksum Verification

A decision table has one condition with 3 values and two yes/no conditions, so 3 × 2 × 2 = 12 rules. After consolidation it has 5 rules that cover {2, 3, 1, 4, 2} of the original rules. Does the checksum confirm the consolidation? Could an overlap between two merged rules together with a missing combination still sum to 12? What else would you check to be sure that each original combination is covered exactly once?

---

## Question 16: The 8-Step Method

Put the Beizer 8-step decision table method in correct order:

A. Specify expected outputs per rule
B. List the conditions with their values
C. Verify the checksum
D. Fill the columns with all combinations
E. Develop one test case per rule
F. List the actions
G. Calculate the number of rules
H. Identify common actions and consolidate

---

## Question 17: When to Use Decision Tables

Which scenarios are good fits for decision table testing?

| Scenario | Good Fit? |
|----------|-----------|
| A. Tax calculation with multiple brackets and deductions | ? |
| B. Performance testing under load | ? |
| C. Access control based on role, department, and time | ? |
| D. Continuous output (temperature prediction) | ? |

---

## Question 18: How Many Conditions Trigger a Failure?

Kuhn, Wallace and Gallo (2004) traced failures in four systems to the number of interacting inputs (parameters) that triggered them. What range of failures needed only one or two inputs, and what was the largest number any failure needed? What does this suggest about pairwise testing?

---

## Question 19: Pairwise Test Reduction

A configuration has 34 yes/no switches.
a) How many tests does "all combinations" need?
b) What is the lower bound for a pairwise suite from the product of the two largest value counts?
c) Show that 4 tests cover all pairs for 3 switches. How many tests do 34 switches need, and why is the product bound not reached?

---

## Question 20: Interaction Strength Selection

Using the Kuhn et al. (2004) data, argue which combination strategy suits each context (an opinion question: justify your choice, there is no single right answer): each choice (every class at least once), pairwise, or 3-way and more:

| Context | Strength |
|---------|----------|
| A. General UI testing | ? |
| B. Medical device software | ? |
| C. Business-critical financial system | ? |

---

## Question 21: Combinatorial Testing Limitations

Name three limitations of combinatorial testing.

---

## Question 22: Technique Matching

Match each scenario to the best primary technique:

| Scenario | Technique |
|----------|-----------|
| A. Testing a numeric range 1-100 | ? |
| B. Testing a system with 20 configuration options | ? |
| C. Testing ATM withdrawal with multiple conditions | ? |
| D. Testing user types: admin, manager, user, guest | ? |

---

## Question 23: Integration Order

What is the recommended order for combining input domain techniques?

---

## Practical Problems

---

## Question 24: EP and BVA Relationship (Practical)

Explain why boundary value analysis complements equivalence partitioning rather than replacing it. Provide an example.

---

## Question 25: Decision Table Construction (Practical)

A login system has the following conditions:
- Username known? (Y/N)
- Password correct? (Y/N)
- Account active? (Y/N)

a) Create a complete decision table
b) Consolidate using don't-care values
c) Verify your consolidation with checksum

---

## Question 26: Critical Analysis (Practical)

A team decides to use only pairwise testing for a safety-critical medical device control system. Evaluate this decision.

---

## Topics Covered

| Topic | Questions |
|-------|-----------|
| Testing fundamentals | Q1, Q2, Q3 |
| Equivalence partitioning | Q4, Q5, Q6, Q7 |
| Boundary value analysis | Q8, Q9, Q10, Q11, Q12 |
| Decision tables | Q13, Q14, Q15, Q16, Q17, Q25 |
| Combinatorial testing | Q18, Q19, Q20, Q21, Q26 |
| Technique selection | Q22, Q23 |
| Practical problems | Q24, Q25, Q26 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
