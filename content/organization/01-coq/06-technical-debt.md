---
title: Technical Debt as a Quality Cost
parent: Cost of Quality
nav_order: 7
layout: default
---

# Technical Debt as a Quality Cost

**Technical debt (TD)** is the extra maintenance cost a system carries because of sub-optimal code or design: its **principal** is the effort needed to remove the inefficiencies, and its **interest** is the additional maintenance effort the team pays every time it changes the affected code {% cite amanatidis2020td %}. In cost-of-quality terms, paying the principal is a prevention cost and the interest is an internal failure cost that recurs. How tools compute it (SQALE, the technical debt ratio) is on [Measuring Maintainability](../../attributes/maintainability/measurement.md); this page covers the two hard questions for a manager: **which tool to believe** and **where debt accumulates**.

## 1. Tools disagree on where the debt is

Amanatidis et al. ran three TD tools (CAST, Squore and SonarQube) on 50 open-source projects, 25 in Java and 25 in JavaScript, and measured how well the tools agreed on which classes carry the most debt. Each tool applies its own ruleset: CAST weighs complexity and coupling, Squore complexity measures and comment density, SonarQube code smells, duplications and cognitive complexity {% cite amanatidis2020td %}.

| Language | Agreement among the three tools (Kendall's W, mean) | Strength |
|---|---:|---|
| Java | 0.777 | strong |
| JavaScript | 0.647 | moderate |

Kendall's W runs from 0 (no agreement on the ranking) to 1 (identical rankings). The authors attribute the gap to more mature analysis tools for Java. Their proposal: treat the classes that **all** tools rank high as a benchmark of validated high-debt classes, and start repayment there {% cite amanatidis2020td %}.

## 2. Self-admitted debt in machine-learning code

Developers often admit debt in comments ("ugly hack", "TODO: fix properly"). Bhatia et al. searched for such **self-admitted technical debt (SATD)** in 318 machine-learning projects and 318 other projects {% cite bhatia2023satd %}:

| Finding | Value |
|---|---|
| Comments admitting debt, median | 1.87% in ML projects, 0.92% in others: about twice as many |
| ML pipeline stage with most SATD | model building (29.0%), then data preprocessing (24.7%) |
| Time until SATD is introduced | 10 days in ML projects, 1,405 days in others: about 140 times sooner |
| How fast SATD is removed | 3.7 times faster in ML projects |

Two kinds of debt specific to ML stood out in their sample: configuration debt (hard-coded settings, hyperparameters) and inadequate testing of ML components {% cite bhatia2023satd %}.

## Example: planning a repayment sprint

A team has one sprint to reduce debt in a Java service that also trains its own recommendation model. SonarQube alone flags 120 classes. The team also runs a second tool and starts with the classes both rank in their top group, where the evidence is strongest; then it greps comments for "TODO" and "hack" in the model-training code, the stage where Bhatia et al. found most admitted debt in ML projects. _(Constructed example.)_

## When to use it, and when not

- **Use multi-tool agreement** to rank where to repay first, and SATD comments as a cheap signal that developers themselves know about.
- **Do not equate** one tool's remediation hours with real cost; the tools disagree, and none measures the interest.

## Limitations

- **Interest is rarely measured.** All tools here estimate principal; the cost a team pays while living with the debt stays unknown.
- **SATD is only what developers write down.** Debt nobody admits is invisible to this method.

## How solid is this?

- **Where it comes from.** Tool agreement from Amanatidis et al.; SATD in ML from Bhatia et al. {% cite amanatidis2020td bhatia2023satd %}. Debt that reviewers catch before merge is on [Value of Code Review](05-code-review-roi.md).
- **What is contested.** Which ruleset represents "real" debt; agreement among tools is evidence, not proof.
- **What we do not hold.** A study that measures the interest paid on debt in money or hours.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
