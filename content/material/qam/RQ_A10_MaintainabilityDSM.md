---
title: "RQ A10: Maintainability & DSM"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 20
redirect_from: /content/attributes/maintainability/RQ_A10_MaintainabilityDSM.html
layout: default
page_type: question-set
---

# RQ A10: Maintainability & DSM

**Format:** Explain in your own words, give brief examples, and show every calculation step. These questions test the lecture summarised in [SN A10: Maintainability & DSM](SN_A10_MaintainabilityDSM.md). Throughout, X → Y means X depends on Y, drawn as a mark in row X, column Y.

---

## Part 1: Why Maintainability Matters

### Question 1: Lehman's Laws

Law I says a used program must keep changing; Law II says changing it increases its complexity. Describe how this tension shows up in a project you know, and name two practices that keep Law II in check.

### Question 2: Parnas's Two Causes

A clinic's booking system is not updated for a year after the insurer changes its API. When the update is finally rushed through, a new developer copies the old API client into three modules and edits each copy.

1. Which of Parnas's causes of aging does each event show?
2. Which of his three preventive measures would have stopped each one?

### Question 3: "We'll Fix the Architecture Later"

A startup postpones all structural work to ship features. Argue for or against this, using Law II and the real-options view of modularity. Under what condition is postponing reasonable?

### Question 4: A Maintainability Requirement

"A developer should be able to identify the module causing a problem in no more than three hours." Which ISO/IEC 25010 sub-characteristic does this target? How would you test it (hint: think of a timed exercise in which developers locate seeded faults)?

---

## Part 2: Measuring the Code

### Question 5: A SIG Quality Profile

A system's code splits by unit cyclomatic complexity into 62% low, 28% moderate, 9% high and 1% very high risk.

1. Use the SIG thresholds to rate its unit complexity in stars.
2. Which single improvement would raise the rating by one star?
3. Why does SIG rate a profile rather than the average complexity?

### Question 6: Star Calibration

SIG calibrates stars so that 5%, 30%, 30%, 30% and 5% of benchmark systems get 5 to 1 stars. Why not 20% each? What does a 5-star rating tell a buyer?

### Question 7: Technical Debt Ratio

A 12,000-line system has 40 hours of estimated remediation work. Assume 30 minutes to develop each line.

1. Compute the technical debt ratio, and rate it on the SQALE example grid and on SonarQube's default grid.
2. Repeat for 150 hours of remediation. Why can two tools give different letters for the same code?

### Question 8: MI Against SIG

Construct two systems with the same average complexity, where a Maintainability Index computed with average complexity (as in Coleman's form) would rate both alike but SIG's profile would rate one clearly worse.

### Question 9: Cognitive Complexity

```python
def total(cart):
    s = 0
    for item in cart:
        if item.qty > 0:
            if item.discount and item.member:
                s += item.price * 0.9
            else:
                s += item.price
    return s
```

1. Compute its cognitive complexity, increment by increment.
2. Its cyclomatic complexity, counting the `and` as radon does, is 5. Which measure better reflects how hard it is to read, and why?
3. Rewrite it so that the cognitive complexity falls without changing what it computes.

### Question 10: Code Metrics and Structure

Can a system have good code metrics and poor structure? Give an example and name the measure that would reveal the problem.

---

## Part 3: Modularity

### Question 11: Options Under Uncertainty

Why does a modular design gain value when future requirements are uncertain, and gain little when they are stable?

### Question 12: KWIC

What principle separates Parnas's information-hiding design of KWIC from the processing-step design? How would you recognise the difference in a real codebase?

### Question 13: Split or Keep

Splitting a module can lower or raise coupling. How would you decide whether to split a module, and what would you look for in a DSM before deciding?

### Question 14: Microservices

A system has loosely coupled services, but each service is internally a tangle of mutually dependent classes. Is the system modular? Answer for the system level and for the service level.

---

## Part 4: Design Structure Matrices

### Question 15: Layers and a Violation

Four modules have the intended layers, bottom to top, L (logging), D (data), S (services), U (user interface). The dependencies are S → D, S → L, U → S, D → L and D → S.

1. Draw the DSM in the order L D S U.
2. Which mark is above the diagonal, and what does it mean for the layering?
3. Which pattern do D and S form, and what is the risk?

### Question 16: Conventions

Two architects read the same DSM, one assuming rows depend on columns and the other the reverse. What wrong conclusions could the second one draw about a vertical bus and about a layering violation?

### Question 17: Partitioning and Tearing

Five elements have the dependencies P → R, Q → P, R → T, S → Q and S → T.

1. Partition the DSM and give the order.
2. Now add R → P. Partition again and name the block. P is meant to be the higher layer, using R: which mark do you tear, and how would you break it in code?

### Question 18: An Element That Is Both a Bus and a Client

An element has a full column and depends on several other elements. Is it a library, a controller or something else? What are the architectural risks?

### Question 19: How Bad Is a Violation?

Are all marks above the diagonal of a layered DSM equally harmful? Name factors that decide the severity.

---

## Part 5: Propagation Cost

### Question 20: Computing Propagation Cost

Five elements have A → B, B → C, C → D and E → D.

1. Build the visibility matrix and compute the propagation cost.
2. Add D → A and compute it again. Explain the jump.

### Question 21: Mozilla and Linux

Linux 2.1.105 had more dependencies than the first open-source Mozilla of April 1998, a version of similar size (9,110 against 6,717), but a much lower propagation cost (5.82% against 17.35%). How is this possible, and what does the redesigned Mozilla of December 1998 (2.78%) show about architecture?

### Question 22: Change-Neutral Dependencies

Geipel and Schweitzer found that in most of 35 projects, more than half of the dependencies never carried a change. What does this mean for a team that prioritises refactoring from a static DSM, and what data would they add?

### Question 23: Weighted Dependencies

Nord et al. weight dependencies by how well they are shielded (0.1, 0.5 or 1). What would make it hard to assign these weights in a system with thousands of dependencies?

### Question 24: The Logging Library

A logging library used by all 100 modules raises the propagation cost. Should it be refactored to lower the number? Why or why not?

### Question 25: Propagation Cost as a KPI

What could go wrong if a team were rewarded for lowering propagation cost? (Hint: see SN §10; an interface that hides a decision can raise PC.)

---

## Part 6: Evidence

### Question 26: ABB's Return

In the ABB case (a 30 kloc system; the tool counts compare 15 kloc of the old code with 5 kloc of the new), duplicated code fell from 5,647 lines to 338 lines.

1. Compute the raw reduction and the reduction per kloc.
2. Adding a magnetic component fell from 6–8 to 1–2 weeks, and a cooling method from 8–12 to 1–2 weeks (the paper's 75% and 83%, comparing the upper ends). The experts reviewed the structure and answered the developer's questions. Why might about three hours a week of such help for three months produce such a cut? When would this approach fail?

### Question 27: Smaller Coupling, Bigger Modules

At Microsoft, the most preferentially refactored Windows 7 modules reduced their inter-module dependencies but gained lines of code. How can both happen, and what does it say about judging refactoring by one metric?

### Question 28: One Metric for Ten Years

You may track one measure for a ten-year enterprise system: the Maintainability Index, the SIG rating or propagation cost. Which would you choose, and what would you miss?

---

## Topics Covered

| Topic | Questions |
|---|---|
| Lehman, Parnas, ISO sub-characteristics | 1, 2, 3, 4 |
| SIG, SQALE, Maintainability Index | 5, 6, 7, 8, 28 |
| Cognitive complexity; code vs structure | 9, 10 |
| Modularity, options, coupling and cohesion | 11, 12, 13, 14 |
| DSM reading, layering, buses, operations | 15, 16, 17, 18, 19 |
| Propagation cost and its limits | 20, 21, 22, 23, 24, 25, 28 |
| Refactoring evidence | 26, 27 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
