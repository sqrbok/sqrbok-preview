---
title: "Revision Questions: Quality Measurement"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 4
redirect_from: /content/define/measurement/RQ_L2_Metrics.html
layout: default
page_type: question-set
---

# L02: Quality Measurement — Revision Questions

**Format:** Explain in your own words, give brief examples. These questions test the lecture summarised in [Study Notes: Quality Measurement](SN_L2_Metrics.md).

---

## Question 1: From Quality Model to Metric

Explain the chain *factor → criterion → metric → value*, using McCall's model. Give one example that starts from **maintainability** and ends with a number computed on code.

---

## Question 2: Two Definitions of Measurement

State **Hubbard's** definition of measurement and **Sarle's**. What does each one stress?

Hubbard argues that "everything is measurable". Apply his three-step argument to "developer happiness": what would you observe, and what would count as more or less of it?

---

## Question 3: Entity, Attribute, Rule

The metric "defects per KLOC of a release" is a ratio of two measurements. For each of the two (the defect count and the size), name the entity, the attribute and the counting rule you would need to write down.

---

## Question 4: Resolution, Accuracy, Precision

Define **resolution**, **accuracy** and **precision**.

a) A timing script reports 250 ms for a request that really takes 200 ms, on every run. Is the script accurate? Is it precise?

b) A response-time log truncates every reading to whole seconds. Two requests took 200 ms and 800 ms. What does the log show, and which property is the problem?

c) A complexity tool reports 10 for a function; your hand count gives 12. Before calling the tool inaccurate, what should you check?

---

## Question 5: The Representation Condition

Lines of code satisfy the representation condition for one attribute and not for another. Name both attributes and explain the difference with an example.

---

## Question 6: Relative and Absolute Numbers

A report says "production incidents rose by 100% this quarter". What two numbers would you ask for before reacting? Relate your answer to the 1995 contraceptive-pill warning.

---

## Question 7: Three Kinds of Pitfall

**Case:** A company mandates 80% line coverage for every release. After six months the report shows 88% overall: the UI has 6,000 lines at 95% coverage, the API 3,000 lines at 90%, and the payment module 1,000 lines at 40%. Production defects have not decreased, and developers say they write tests "to hit the number".

Analyse the case as **bad statistics**, **bad decisions** and **bad incentives**: what went wrong under each heading?

---

## Question 8: McNamara Fallacy or Streetlight Effect?

**Case:** A manager ranks developers by commits per week, lines added and tickets closed. Developer A scores high on all three. Developer B scores lower but mentors new hires and removes technical debt. Developer A is promoted.

Which pitfall applies: the McNamara fallacy, the streetlight effect, or both? Justify your answer.

---

## Question 9: Classify by Scale

Give the scale (nominal, ordinal, interval, ratio or absolute) of each field of a defect record, and one summary statistic that is meaningful for it:

- component (UI, API, DB)
- severity 1–4
- date reported
- hours to fix
- number of open defects

---

## Question 10: Is the Statement Meaningful?

For each statement, say whether it is meaningful for its scale, and why:

a) "The average severity of last month's defects was 2.7."

b) "Task A took twice as long as task B" (effort in hours).

c) "Today, at 20 °C, is twice as warm as yesterday at 10 °C."

---

## Question 11: Story Points

In one company's data, the median hours spent were 21, 52, 64, 100 and 111 for stories of 1, 2, 3, 5 and 8 points. If story points were a ratio scale, what medians would you expect from the 1-point median? Where does the data agree with a ratio scale, and where not? Would you sum story points into a velocity? State your assumption.

---

## Question 12: Mean or Median?

Nine bugs took 1 hour each to fix and one took 100 hours. Compute the mean and the median. Which describes a typical fix, and which number would you report for response times?

---

## Question 13: Confounding by Size

What is a **confounding variable**? Draw the three-arrow diagram for class size, a class metric such as CBO, and fault-proneness.

El Emam and colleagues found that after controlling for class size, none of the CK metrics in one system remained associated with faults. What does this imply for a team that plans to reduce faults by reducing CBO?

---

## Question 14: GQM

Apply **Goal–Question–Metric** to the goal "reduce the number of defects that reach production". Write the goal with all five parts (object, purpose, quality focus, viewpoint, context), give two questions, and at least one metric for each question.

---

## Question 15: Operational Definitions

Team A reports 15 defects per KLOC; Team B reports 8. Before concluding that Team B writes better code, list the operational-definition questions you must ask, and say which of Park's two criteria each question protects.

---

## Question 16: Cyclomatic Complexity

Give two counts for this function: the cyclomatic complexity from its control-flow graph (decisions + 1, with `and` drawn as part of one decision), and what `radon cc` would report. Explain the difference:

```python
def shipping(order):
    if order.total > 100 and order.country == "RU":
        return 0
    for item in order.items:
        if item.fragile:
            return 15
    return 5
```

What do the SEI risk bands say about the result?

---

## Question 17: Halstead and the Maintainability Index

A file holds one function, with *n₁* = 7, *n₂* = 12, *N₁* = 9, *N₂* = 18, cyclomatic complexity 6, 11 logical lines of code and no comments. Compute its Halstead volume and its Maintainability Index on the 0–100 scale. Which colour band does it fall into in Visual Studio, and which radon rank? Name two reasons two tools may report different MIs for the same function.

---

## Question 18: CK Metrics

List the six **Chidamber–Kemerer** metrics and what each counts. For a class with four methods, one superclass (`object`), no subclasses, calls to two other classes, and no class that uses it, give WMC (unit weights), DIT, NOC and CBO.

Why is a fixed threshold such as "CBO above 14 is bad", taken from another system, unreliable?

---

## Question 19: Accuracy on Rare Defects

A system has 1,000 classes, 10 of them faulty. Predictor X flags no class. Predictor Y flags 100 classes, including all 10 faulty ones. Compute accuracy, precision and recall for both (what happens to precision when nothing is flagged?). Which predictor is more useful, and what does this say about a paper that reports only accuracy?

---

## Question 20: Metrics for Generated Code

GPT-4's solutions to 72 tasks passed more tests than human solutions but had higher cyclomatic complexity. What does this say about measuring a code generator with a single metric?

---

## Topics Covered

| Topic | Questions |
|-------|-----------|
| Quality models and metrics | Q1 |
| Measurement definitions | Q2, Q3, Q4, Q5 |
| Measurement pitfalls | Q6, Q7, Q8 |
| Measurement scales | Q9, Q10, Q11 |
| Reading metric data | Q12, Q13 |
| GQM and operational definitions | Q14, Q15 |
| Code and OO metrics | Q16, Q17, Q18 |
| Evidence for code metrics | Q19, Q20 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
