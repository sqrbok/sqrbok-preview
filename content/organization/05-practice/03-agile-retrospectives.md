---
title: Agile Retrospectives
parent: Industry Practices
nav_order: 3
layout: default
---

# Agile Retrospectives

An **Agile retrospective** is a meeting at the end of each iteration in which the team looks back at how it worked and agrees on a few changes for the next iteration, putting the Agile Manifesto's principle of regular reflection and adjustment into practice {% cite lehtinen2017retrospectives %}. It is the team-level, everyday form of [process improvement](../03-process/03-improvement-methods.md).

## 1. How a retrospective runs

Most retrospectives follow Derby and Larsen's five phases, which Matthies and colleagues use to organize retrospective activities {% cite matthies2019retrospective %}: **set the stage** (make it safe to speak), **gather data** (what happened in the iteration), **generate insights** (why it happened), **decide what to do** (a few concrete actions) and **close**. The output is a short list of action items that someone owns.

Activities give each phase a format. In **Sailboat**, the team draws a boat and marks what pushed it forward (wind) and what held it back (anchors). **Five Whys** asks "why?" repeatedly to get from a symptom to a cause. **Six Thinking Hats** has everyone look at the iteration from one viewpoint at a time.

## 2. Matching activities to problems

Retrospectives go stale in recognizable ways. Matthies and colleagues mapped ten common problems to thirteen activities that counter them {% cite matthies2019retrospective %}:

| Problem | Countermeasure |
|---|---|
| Not speaking up | Sailboat, Peaks and Valleys timeline, Tweet My Sprint, Collective Painting |
| Blame game | Peaks and Valleys, Guess Who, Team Oscars |
| Group think | Six Thinking Hats |
| All action, no talk | Six Thinking Hats, Five Whys |
| All talk, no action | Reverse Brainstorming ("how could we make this worse?", then invert) |

## 3. Example: what teams actually discuss

Lehtinen, Itkonen and Lassenius followed 37 retrospectives of seven Scrum teams in one company over almost three years and classified 879 statements {% cite lehtinen2017retrospectives %}. Half (51%) described negative experiences, 29% positive ones and 20% were corrective actions. Three process areas close to the team (sprint planning, implementation and testing) took 75% of the statements, while problems owned by others were rarely raised. Some topics returned again and again, yet only 19% of the corrective actions addressed those recurring discussions.

The team said "low bug count" whenever the number of open bugs was roughly 90 or less, but nobody showed the actual count in the meeting: the discussion ran on impressions. Matthies and colleagues propose closing that gap with **data-informed retrospectives**: a health check that pulls measures such as test coverage, commit patterns or build failures from the repository before the meeting, and a **remedy appraisal** that measures whether a past action item changed the metric it targeted {% cite matthies2020mining %}.

## Where the inputs come from

A data-informed retrospective gathers its numbers before the meeting: open and escaped bugs from the tracker, build failures and their causes from the CI server, coverage from the last pipeline run, and the state of the previous sprint's action items from the team board. One person exports them the day before, so the meeting discusses numbers rather than impressions.

## When to use it — and when not

Hold one at the end of every iteration, short and with the whole team. It is the right tool for how the team itself works. It is the wrong tool for problems owned outside the team (a slow approval board, another team's API), which the Lehtinen data show retrospectives rarely touch; those need [change management](../03-process/04-change-management.md) at the organization's level.

## Limitations

- **Opinion over evidence.** Without data, strong opinions dominate (participant bias), as the bug-count example shows.
- **Symptoms over causes.** Teams tended to act on visible symptoms rather than investigate root causes {% cite lehtinen2017retrospectives %}.
- **One company.** The longitudinal study observed a single organization.

## How solid is this?

- **Where it comes from.** A longitudinal case study, a mapping of retrospective problems to activities, and a proposal for repository-based retrospectives {% cite lehtinen2017retrospectives matthies2019retrospective matthies2020mining %}.
- **What is contested.** Whether data-informed retrospectives improve outcomes: the paper proposes the approach, and the area's review holds no evaluation of it.
- **What we do not hold.** Derby and Larsen's book, the origin of the five phases.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
