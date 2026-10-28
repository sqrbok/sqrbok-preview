---
title: Microsoft Quality Practices
parent: Industry Practices
nav_order: 9
layout: default
---

# Microsoft Quality Practices

**Microsoft's quality practices** grew out of shipping large packaged products such as Windows and Office on fixed schedules: development runs in milestones with quality gates, a **bug bar** decides which defects must be fixed before release, and dedicated test engineers work alongside developers. They are described in *How We Test Software at Microsoft* and studied in Microsoft Research papers {% cite page2008mstesting bosu2015codereview bird2009vista kim2014refactoring %}.

## 1. Milestones, bug bars and the ship room

A product moves through milestones (M1, M2, M3, Beta, Release), and each milestone has **exit criteria** that tighten as release approaches; for the final release the book names, among others, no open severity-1 bugs and 80% code coverage from unit tests {% cite page2008mstesting %}. A **bug bar** is the predefined threshold for which bugs must be fixed at a given stage. Bugs are triaged by severity: a severity-1 bug (a crash, data loss, a main feature unusable) always blocks release; lower-severity bugs are classed as must-fix or should-fix against the current bar. Near release a **ship room** (or war room), a committee of development, test and program-management leads, reviews the remaining bugs and decides to fix, defer or ship.

Work is organized in **feature crews**: a program manager with three to five developers and three to five testers; the crew reports to three leads, one each for development, test and program management. Test engineers who write code to test the product are **SDETs** (software development engineers in test), held to the same coding standard as developers (SDEs) {% cite page2008mstesting %}.

## 2. What Microsoft's research found

| Study | Data | Finding |
|---|---|---|
| Code review usefulness {% cite bosu2015codereview %} | 1.5 million review comments in five projects (Azure, Bing, Visual Studio, Exchange, Office) | reviewers who had reviewed a file before wrote useful comments 65–71% of the time, first-time reviewers 32–37%; usefulness rose with prior reviews up to about five, then plateaued; more files in a change meant a lower share of useful comments |
| Distributed development {% cite bird2009vista %} | post-release failures of Windows Vista binaries | binaries developed by distributed teams had slightly more failures, but after controlling for team size the difference was negligible |
| Refactoring {% cite kim2014refactoring %} | survey of 328 engineers and Windows 7 history | the 5% most refactored modules decreased their dependencies by a factor of 0.85 while the rest increased by 1.10, relative to the average change per module |

**Example.** The review finding has a direct use: when choosing a reviewer, prefer someone who has already reviewed or changed the files involved. If a newcomer must learn the code through reviews, adding an experienced reviewer to the same change keeps the comments useful while the newcomer learns.

## When to use these practices — and when not

Milestones, bug bars and a ship room suit products released on a schedule to customers who install them, where a fix after release is expensive. For an online service that deploys daily, an [error budget](06-site-reliability-engineering.md) and automated [quality gates](10-quality-gates-cicd.md) do the same job continuously. The research findings on reviewers and distributed teams apply to both.

## Limitations

- **The book describes Microsoft around 2008.** The practices above are a model of milestone-based delivery, not a description of how Microsoft works today.
- **Book figures are not measured results.** The 80% coverage criterion and the crew sizes are the book's guidance.
- **One company.** All three studies use Microsoft data.

## How solid is this?

- **Where it comes from.** *How We Test Software at Microsoft* and three Microsoft Research studies {% cite page2008mstesting bosu2015codereview bird2009vista kim2014refactoring %}.
- **What is contested.** Milestone exit criteria for M2 and M3 are quoted with different values in the area's notes on the book, so they are left out here.
- **What we do not hold.** The book's text; its content reaches this page through the area's notes on it.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
