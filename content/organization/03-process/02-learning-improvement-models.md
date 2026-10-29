---
title: Learning & Improvement Models
parent: Process Improvement
nav_order: 2
layout: default
---

# Learning & Improvement Models

**Improvement models** describe how an organization learns from its mistakes: whether it only corrects each error, or also changes the assumptions and rules that produced it. Argyris and Schön call these **single-loop** and **double-loop learning** {% cite argyris1978organizational %}; PDCA and Kaizen are the routines that make the second loop a habit.

## 1. Single loop fixes the error, double loop fixes the system

In **single-loop learning** a team detects an error and corrects it while keeping its goals, strategies and rules unchanged. In **double-loop learning** it also asks which rule or assumption let the error happen and changes that, so the next similar situation is handled differently {% cite argyris1978organizational %}.

| Situation | Single-loop response | Double-loop response |
|---|---|---|
| A server runs out of memory | restart it | ask why no one saw memory grow; add monitoring to every deployment checklist |
| Tests fail after a release | fix the tests | ask why the tests were written after the code; change when tests are written |
| A release is late | work overtime | ask why the estimate was wrong; change how the team estimates |

IBM's Defect Prevention Process is double-loop learning built into the development process: after a defect is fixed, a team meeting asks what in the process allowed it, and an action team changes that {% cite mays1990defect %}. See [Defect Prevention Process](05-defect-prevention.md).

## 2. PDCA: the improvement loop

**Plan–Do–Check–Act** (PDCA, also Plan–Do–Study–Act) is a four-step cycle for testing one change at a time, going back to Shewhart and popularized by Deming {% cite shewhart1931economic deming1986crisis %}:

1. **Plan:** state the problem, a target and a measurable change.
2. **Do:** try the change on a small scale.
3. **Check:** compare the results with the target.
4. **Act:** keep and standardize the change if it worked; otherwise adjust and run the cycle again.

**Example.** In a department of four teams, 12 of the 40 bugs reported in a quarter were escapes, bugs found by customers rather than by the teams; the pilot team had 4 of them. *Plan:* require a test for every bug fix, aiming to halve the pilot team's escapes, from 4 to 2. *Do:* apply the rule in the pilot team for one quarter. *Check:* compare the pilot team's escapes with its own baseline of 4, and with the other three teams over the same quarter, which did not change their rule. *Act:* if the pilot team's escapes fell while the others' did not, make the rule part of the definition of done for all teams. (A constructed example.)

A Scrum sprint follows the same shape: planning, the sprint itself, the review, and the retrospective that decides what to change next.

## 3. Kaizen: small improvements by everyone

**Kaizen** (Japanese for "change for the better") is the habit of continuous, small improvements made by everyone, not only by managers. The lecture summarizes it in four principles:

- **Change is incremental:** many small steps rather than one big transformation.
- **Everyone takes part,** from senior managers to developers.
- **Ideas come from the bottom up:** the people doing the work see the problems first; IBM found defect prevention worked best when developers led it {% cite mays1990defect %}.
- **It never stops:** improvement is part of daily work, not a project with an end date.

Retrospectives, continuous integration with small frequent changes, and code review are Kaizen in software: each makes a small adjustment every few days or weeks.

## When to use them — and when not

Use double-loop questions when a problem recurs; use PDCA to test one change with data before rolling it out; use Kaizen routines for steady, low-risk improvement. They do not replace a larger redesign when the whole process is wrong, and a PDCA cycle without a measurable target ("we'll see if it feels better") cannot reach a real Check step.

## Limitations

- **Double-loop learning is uncomfortable.** It questions decisions managers made, so organizations tend to stay in the single loop.
- **Small steps can miss the bottleneck.** Many local improvements may not change overall throughput; the [Theory of Constraints](06-theory-of-constraints.md) addresses that.
- **Improvement costs time first.** The short-term dip is covered in [Change Management](04-change-management.md).

## How solid is this?

- **Where it comes from.** Argyris and Schön's theory of organizational learning, Shewhart's and Deming's quality cycle, and IBM's defect-prevention experience {% cite argyris1978organizational shewhart1931economic deming1986crisis mays1990defect %}.
- **What is contested.** Kaizen's four principles are the lecture's summary of the practice, not a formal standard.
- **What we do not hold.** The books by Argyris and Schön, Shewhart and Deming are cited from the lecture; the area's review holds none of them.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
