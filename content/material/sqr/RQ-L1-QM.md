---
title: "Revision Questions: Quality Models"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 2
redirect_from: /content/define/foundations/RQ-L1-QM.html
layout: default
page_type: question-set
---

# Revision Questions: Quality Models

These questions test the lecture summarised in the [study notes](SN-L1-QM.md). Answer in your own words, with a short software example where asked.

---

## Question 1: Garvin's Views — Definitions

Explain the difference between the **user-based** and **manufacturing-based** views of quality.

Give one software example in which the same product is judged high quality in one view and low quality in the other.

---

## Question 2: Garvin's Views — Which View Dominates?

For ONE of the following ways of working, say which Garvin view dominates and justify your answer:

- Scrum or XP, where work is accepted by customer demo
- a team audited against a process standard such as CMMI
- a medical-device project that must pass FDA validation
- a start-up shipping weekly to learn what customers want

---

## Question 3: Garvin's Eight Dimensions

Name Garvin's eight dimensions of product quality. Using the piano example (Steinway and Yamaha), explain why a firm need not compete on all eight, and give a software analogue.

---

## Question 4: The Quality Model Hierarchy

Take the requirement "the payment service must be reliable". Walk it down the quality model hierarchy (characteristic → subcharacteristic → measure), then add a threshold to turn it into a requirement. Explain what each step removes from the original sentence.

---

## Question 5: Internal and External Quality

What is the difference between internal and external quality measures? Give two examples of each, and explain why a quality model has to connect them.

---

## Question 6: QA versus QC

Define quality assurance and quality control. For each of the following, say which it is and why:

- a) a reviewer checks one pull request against the coding standard
- b) an auditor confirms that every pull request in the last quarter was reviewed before merge
- c) a tester runs the regression suite on release 4.2

---

## Question 7: McCall's Model Structure

McCall's model has three levels: **factors**, **criteria** and **metrics**.

Explain the purpose of each level. Use **maintainability** to illustrate all three, and explain how a criterion is scored.

---

## Question 8: McCall's Categories

Pick **one** of McCall's three categories and list its factors. For each factor, write one sentence on what it means for a mobile banking app.

---

## Question 9: McCall Is Not Orthogonal

McCall's criteria for maintainability and for testability overlap. Name the shared criteria and explain one consequence, good or bad, for a team that invests in modularity.

---

## Question 10: Quality Attribute Trade-offs

Pick one pair and explain the trade-off with a concrete software example, including a rough number:

- integrity and efficiency
- usability and efficiency
- testability and efficiency

Then give one pair whose relationship is **direct** and explain why.

---

## Question 11: From McCall to ISO/IEC 25010

List the nine characteristics of ISO/IEC 25010:2023. Which three are plain renamings of McCall factors? Which two collect content that McCall kept inside differently named factors, and which factors were they? Which characteristic has no McCall counterpart at all? Name two characteristics that the 2023 edition renamed or added compared with 2011.

---

## Question 12: ISO/IEC 25010 as a Checklist

For a food-delivery app, choose three ISO/IEC 25010 characteristics that matter and one that you would drop. For each kept characteristic write one testable requirement; for the dropped one, write the reason.

---

## Question 13: Writing a Quality Requirement

A developer writes: *"The system shall be fast and reliable."*

Explain why this is a poor quality requirement. Rewrite it as **two separate testable requirements** using the template:

```text
While [condition, with any measurement window], the [component] shall [measure] [threshold].
```

Explain why the performance requirement uses a percentile rather than an average.

---

## Question 14: The Six Steps

Apply the six steps to write a testable **reliability** requirement for a login service. Show your decision at each step.

---

## Question 15: Quality in Use

Explain the difference between product quality and quality in use. Give an example of a product with high product quality but low quality in use, and say how you would detect the problem.

---

## Question 16: Process Quality

Name the four groups of process quality characteristics. Why does a high-quality process not guarantee a high-quality product?

---

## Question 17: Kano Model

Explain the difference between **must-be**, **one-dimensional** and **attractive** attributes. Classify two features of a video-streaming app into each category, and explain why a feature can move from attractive to must-be over time.

---

## Question 18: Quality Pioneers

Match each idea to its pioneer and give one place it appears in software practice: Plan–Do–Check–Act; the quality trilogy; conformance to requirements; the fishbone diagram; the Capability Maturity Model.

---

## Topics Covered

| Topic | Questions |
|---|---|
| Garvin's views and dimensions | Q1, Q2, Q3 |
| Quality models and terminology | Q4, Q5, Q6 |
| McCall's model and trade-offs | Q7, Q8, Q9, Q10 |
| ISO/IEC 25010 and requirements | Q11, Q12, Q13, Q14 |
| Quality in use and process quality | Q15, Q16 |
| Kano and the pioneers | Q17, Q18 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
