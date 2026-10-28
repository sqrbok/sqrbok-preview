---
title: Modern Code Review
parent: Inspection
nav_order: 3
layout: default
---

# Modern Code Review

**Modern code review** is a lightweight, tool-based review of each code change before it is merged: one or two reviewers read the diff in a tool and comment asynchronously, without a meeting {% cite sadowski2018codereview %}. It keeps a second person reading the code and drops the roles, the meeting and the schedule.

## 1. Limits on size, rate and time

The largest early study of lightweight review covered 2,500 reviews of 3.2 million lines of code by 50 programmers at Cisco {% cite cohen2006secrets %}. Its recommendations:

- **Review at most 200–400 lines in one sitting.** Beyond that, the number of defects found per line falls sharply and is close to zero after 400 lines.
- **Read at most 300–500 lines per hour.** Above about 500, a review becomes a rubber stamp.
- **Stop after 60–90 minutes.** After that reviewers get tired and lose focus, and their performance drops off.
- **Have the author annotate the change first.** Annotating makes authors re-read their own code, and they often find defects before the review starts.

Tool-assisted reviews took about one fifth of the time of formal meetings, and an efficient team found and fixed a defect every 10 to 15 minutes {% cite cohen2006secrets %}. These limits are looser than Fagan's (at most 125 statements per hour, two-hour meetings) because they describe something different: one person reading a code diff in a tool, not a group paraphrasing a design or module aloud and counting only non-commentary statements.

**Example.** A pull request changes 1,200 lines. By size, 1,200 / 400 = 3 sittings (6 at 200 lines). By time, 2.4 to 4 hours of reading at 300–500 lines per hour is two to four sittings of 60–90 minutes. Better: split it into three or four changes that can each be reviewed in one sitting.

## 2. Google: small changes, one reviewer, hours not days

At Google, with about 9 million reviewed changes from over 25,000 developers, the median change is 24 lines, most changes have one reviewer (fewer than 25% have more), the median time to complete a review is under 4 hours, and over 80% of changes need at most one round of revisions {% cite sadowski2018codereview %}. Finding defects is welcome but is not the main purpose: developers named education, keeping norms such as style and readability, gatekeeping by code owners, and preventing accidents.

## 3. Microsoft and open source: who reviews, and how, matters

Of 1.5 million review comments in five Microsoft projects, 65.5% were judged useful; reviewers who had worked on the file before wrote 65–71% useful comments, first-time reviewers of a file only 32–37% {% cite bosu2015codereview %}. In three open-source systems (Qt, VTK, ITK), components with many changes approved without discussion, or reviewed faster than 200 lines per hour, had more post-release defects; even full review coverage did not prevent them {% cite mcintosh2015codereview %}. The rule to follow: read at most 300–500 lines per hour; McIntosh's data suggest slower, about 200, for risky code.

## 4. LLM reviewers are not yet ready

On SWR-Bench, 1,000 manually verified pull requests from 12 Python projects, the best LLM-based reviewer reached an F1 score of 19.38%, held back mainly by false positives {% cite zeng2025swrbench %}. F1 combines precision (the share of the tool's comments that point to real issues) and recall (the share of real issues it comments on); it is high only when both are. Aggregating several review passes raised F1 by up to 43.67%, and the tools did better on functional errors than on documentation and style. Treat an LLM as an extra reviewer whose comments a person checks.

## When to use it — and when not

Use modern code review on every change: the cost per change is small and it spreads knowledge of the code base. For a high-risk design or an interface between teams, add a [Fagan inspection](fagan-process.md); a single reviewer reading a diff sees only what changed, not the whole design.

## Limitations

- **Speed and silent approval.** A "looks good to me" without reading or discussion is a review in name only, and such changes are more defect-prone {% cite mcintosh2015codereview %}.
- **Misused metrics.** If developers suspect that review data feeds their performance evaluations, they game or hide it {% cite cohen2006secrets %}.

## How solid is this?

- **Where it comes from.** The Cisco study as reported by SmartBear, a vendor of review tools; the Google case study; the Microsoft usefulness study; McIntosh's regression study of three open-source systems {% cite cohen2006secrets sadowski2018codereview bosu2015codereview mcintosh2015codereview %}.
- **What is contested.** The Cisco curves are not peer-reviewed; the Google and Microsoft numbers describe practice, not an optimum.
- **What we do not hold.** SWR-Bench appeared in 2026 (Proceedings of the ACM on Software Engineering); our figures come from the preprint, and LLM reviewers change fast.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
