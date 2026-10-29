---
title: Improvement Methods
parent: Process Improvement
nav_order: 3
layout: default
---

# Improvement Methods

An **improvement method** is a repeatable procedure for finding what limits a process and changing it. Four methods recur in software organizations; each starts from a different kind of problem, so the first step is to name the problem, not the method.

## 1. Four methods, four starting problems

| Method | Starts from | Core routine | Typical software use |
|---|---|---|---|
| [Defect Prevention Process (DPP)](05-defect-prevention.md), IBM 1983 | defects that keep recurring | causal-analysis meeting after defects are found; an action team removes the cause {% cite mays1990defect %} | a defect type returns release after release |
| [Theory of Constraints (TOC)](06-theory-of-constraints.md), Goldratt 1984 | one stage limits the whole flow | five focusing steps on the bottleneck {% cite goldratt1984goal rahman1998toc %} | reviews or testing hold up every change |
| [Six Sigma](07-six-sigma.md), Motorola 1985 | output varies too much | DMAIC: define, measure, analyze, improve, control {% cite antony2002sixsigma %} | build times or defect rates swing unpredictably |
| [Agile retrospective](../05-practice/03-agile-retrospectives.md), 2001 | the team wants to work better | the team reflects at the end of each iteration and commits to actions {% cite lehtinen2017retrospectives %} | any team, every iteration |

All four are forms of [double-loop learning](02-learning-improvement-models.md): they change the process, not just the result.

## 2. DPP and retrospectives compared

The two are often confused, because both are meetings that end with actions.

| | DPP | Retrospective |
|---|---|---|
| Trigger | defects found in a stage | end of an iteration |
| Question | what in the process caused this defect? | what should the team change? |
| Input | the defect records | the team's experience, ideally plus repository data |
| Who acts | an action team with authority across projects | the team itself |
| Typical output | a changed checklist, tool or training | a few action items for the next iteration |

## 3. Example: choosing a method

A team ships every two weeks, but changes wait on average three days for code review while coding takes one. The problem is a bottleneck, not recurring defects or variation, so TOC fits: treat review as the constraint that sets the pace for the rest of the work (*subordinate*, step 3 of TOC's five focusing steps on [Theory of Constraints](06-theory-of-constraints.md)), and limit work in progress so that reviewers are not flooded. If, after that, reviews pass quickly but the same null-pointer defects keep escaping, the problem has changed and DPP's causal analysis becomes the right tool. (A constructed example.)

## When to use a method — and when not

Use a named method when the problem is clear enough to measure; it gives the team a shared vocabulary and an order of steps. Do not adopt one because a competitor did: a Six Sigma program on a process that runs only a few times a year has too little data to analyze.

## Limitations

- **Each method sees one kind of problem.** DPP does not find bottlenecks, TOC does not reduce defects, Six Sigma needs repeated measurements.
- **None works without the people.** Surveys on Six Sigma and Lean Six Sigma rank management commitment and culture above every technical factor {% cite antony2002sixsigma laureani2012leansixsigma %}; see [Change Management](04-change-management.md).

## How solid is this?

- **Where it comes from.** IBM's DPP reports, reviews of TOC, survey studies of Six Sigma's success factors, and a longitudinal study of retrospectives {% cite mays1990defect rahman1998toc antony2002sixsigma lehtinen2017retrospectives %}.
- **What is contested.** No study compares the four methods head to head in software; the choice above is reasoning from what each method targets.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
