---
title: "Revision Questions: Classification Tree Method"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 16
redirect_from: /content/verif/domain/RQ_A08_ClassificationTree.html
layout: default
page_type: question-set
---

# Revision Questions: Classification Tree Method

## Purpose
QAM lecture A08, optional for bachelor students and not examined in the bachelor course. 27 reflection questions for exam preparation, testing the lecture summarised in [Study Notes: Classification Tree Method](SN_A08_ClassificationTree.md). Questions focus on understanding and application, not memorization.

---

## Part 1: Introduction & Motivation

1. Why was CTM developed? What specific problems with Category-Partition does it address?
2. A colleague says "We don't need CTM — our testers are experienced and know what to test." How would you respond using evidence?
3. Evaluate the strength of empirical evidence for CTM. What are its limitations?
4. Why do the industrial results showing >90% test reduction not necessarily mean CTM is 90% more efficient?
5. How does CTM relate to the broader context of combinatorial testing?

---

## Part 2: Core Concepts

6. Why must the classes under one classification be disjoint and complete? What goes wrong in the combination table if two classes overlap?
7. Given this specification, identify the test-relevant aspects and their classes: "A shipping calculator charges based on package weight (up to 50kg), destination (domestic/international), and delivery speed (standard 5-day, express 2-day, overnight). Packages over 50kg are rejected."
8. Explain the difference between composition and classification using a real-world example not from the lectures.
9. Why does the lecture put invalid values in a separate branch? Show with a concrete example what happens to the combination table if they sit inside each classification instead. When is mixing them acceptable?
10. Why are only classes test-selectable, and not compositions or classifications?
11. What is the relationship between equivalence partitioning (EP) and CTM classes?

---

## Part 3: The CTM Process

12. You're testing a mobile banking app. How does choosing "the transfer screen" vs "the entire app" as test object change your classification tree?
13. Identify the test-relevant aspects of the function `search(database, query, filters, sortBy)` and build a classification tree, marking which nodes are compositions and which are classifications.
14. Why did "Avoid" produce smaller test suites than "Replace" in Grindal et al.'s comparison? What happens to a test case under Replace?
15. Compare the minimality criterion and pairwise (2-wise) generation. When would you choose each?
16. What does a tree effectiveness E[T] = 0.17 mean in practical terms? Chen et al. raised it to 0.40 by restructuring: what did they change?
17. Chen et al. note that different testers build different trees from the same specification. Why is that a problem, and what does a quality measure such as E[T] add?

---

## Part 4: Examples & Practice

18. Build a classification tree for an ATM withdrawal function. Include at least 4 aspects and model invalid cases.
19. In the city-tax example, what would go wrong if residency, status and gross pay were all placed at the root level, instead of nesting status and gross pay under the resident class? How does the hierarchy remove meaningless combinations?
20. A row of the combination table for `count(array, value)` reads: "array size: more than 1; occurrences: more than one; order: unsorted." Give three different concrete test cases (array, value, expected count) that match this row.
21. Grindal et al. compared four ways to handle conflicting combinations. Describe each in one sentence and say which produced the smallest suites.
22. Identify the constraints (infeasible combinations) in the count() function tree. How would you handle them?

---

## Part 5: Advanced Topics & Tools

23. You have a web form with 4 tabs, each with different fields. Should you create one big tree or four separate trees? Use the composition/classification distinction and the cost of constraints in your answer.
24. Compare CTE XL and TESTONA. What capabilities does TESTONA add that make it more powerful for modern testing?
25. A novice tester creates a flat tree with 5 classifications at the root level, each with 4 classes. What problems will they face? How would you coach them?
26. The notes name three practical difficulties of CTM: impossible combinations, trees that differ between testers, and confusing composition with classification. Which is most dangerous for test quality, and why?
27. How does CTM integrate with equivalence partitioning, boundary value analysis, and combinatorial testing? Draw the connections.

---

## Key Numbers to Memorize

| Metric | Value | Source |
|--------|-------|--------|
| Full-time students preferring CTM after training | **70%** (20% no preference, 10% own methods) | Yu et al. 2004 |
| Study participants | **162** (104 + 58) | Yu et al. 2004 |
| Training time in study | **3 hours** | Yu et al. 2004 |
| Part-time students' own suites, average detection | **55%** of faulty programs | Yu et al. 2004 |
| Full-time students naming infeasible combinations as the main disadvantage | **60%** | Yu et al. 2004 |
| Full-time students naming tester-dependent trees as a disadvantage | **25%** | Yu et al. 2004 |
| Test reduction (CTE XL) | **>90%** (70,000 → 5,560) | Lehmann 2000 |
| Test suites evaluated (constraints) | **3,854** | Grindal et al. 2007 |
| E[T] before restructuring (bonus program) | **0.17** | Chen et al. 1999, 2002 |
| E[T] after restructuring | **0.40** | Chen et al. 1999, 2002 |
| Best constraint strategy | **Avoid** | Grindal et al. 2007 |

---

## Key Terms Glossary

| Term | Definition |
|------|------------|
| **Classification Tree** | Graphical model of the input domain with hierarchical aspects and classes |
| **Test Object** | The unit under test: a function, module or system with inputs and observable outputs |
| **Aspect** | A test-relevant property that influences behaviour; drawn as a classification (alternatives) or a composition (parts) |
| **Equivalence Class** | A set of input values expected to produce equivalent behavior |
| **Composition** | "Consists of" node: its children exist together |
| **Classification** | "Is a kind of" node: exactly one child class applies |
| **Class** | Leaf node — a selectable value in the combination table |
| **Combination Table** | Matrix mapping classes to test specifications |
| **E[T]** | Tree effectiveness = legitimate cases / potential cases |
| **Dependency Rule** | Constraint marking infeasible class combinations |
| **TSL** | Test Specification Language — text format used in Category-Partition |
| **CTE XL** | Classification Tree Editor eXtended Logics: dependency rules, generation criteria, later numerical constraints checked with a SAT solver |
| **TESTONA** | Successor of CTE XL: built-in constraint solver, test oracles from rules, test script generation |
| **t-wise** | Generation criterion: every combination of classes from any *t* classifications appears |
| **Minimality** | Generation criterion: every class appears in at least one test case |

---

*For detailed explanations, see [Study Notes: Classification Tree Method](SN_A08_ClassificationTree.md).*

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
