---
title: "Worked Case: An Improvement Proposal with a Pilot"
parent: Process Improvement
nav_order: 8
layout: default
page_type: worked-case
---

# Worked Case: An Improvement Proposal with a Pilot

This worked case takes one improvement from a recurring problem to a decision to institutionalize it: problem, method choice, baseline, target, a pilot with a control team, a measurement window that survives the dip, and the decision. The methods are explained on [Improvement Methods](03-improvement-methods.md), [Defect Prevention Process](05-defect-prevention.md), [Learning & Improvement Models](02-learning-improvement-models.md) and [Change Management](04-change-management.md).

**Example department.** An online travel company has four product teams of six developers. Each team ships at the end of every two-week sprint, and production defects are counted for 30 days after each release. Two teams take part: **Hotels** (the pilot) and **Flights** (the control). The head of engineering asks for a proposal: escaped defects have not fallen in a year, and each one costs the on-call engineer an evening. _(Constructed example: all numbers below were made up for this page and are labelled as such.)_

## 1. Check the basics with a short self-assessment

An improvement method needs a process that is at least planned and measured. Before choosing one, the team lead scored the CMMI Level 2 process areas {% cite cmmi-dev-2010 %} with one question each (yes, partly, no):

| Process area | Question for the team | Hotels |
|---|---|---|
| Requirements Management (REQM) | Is every change traced to a ticket and its acceptance criteria? | yes |
| Project Planning (PP) | Is there a written sprint plan with estimates? | yes |
| Project Monitoring and Control (PMC) | Is progress compared with the plan, and are deviations acted on? | partly |
| Supplier Agreement Management (SAM) | Are third-party services under an agreement with stated quality? | not applicable |
| Measurement and Analysis (MA) | Are defects recorded with fields that allow analysis? | **partly** |
| Process and Product Quality Assurance (PPQA) | Does anyone outside the team check that the process is followed? | no |
| Configuration Management (CM) | Are releases built from tagged versions in version control? | yes |

**Decision point: improve now, or fix the basics first?** Criterion: the chosen method's inputs must exist. Measurement was the gap: escaped defects were counted, but tickets had no defect type. Defect types were therefore added from the past six releases before anything else, by the tech lead reading 37 tickets in one afternoon. The missing PPQA check was noted for later; it does not block a pilot.

## 2. State the problem with data

The six releases before the pilot gave this baseline (escaped defects per release):

| Team | Last six releases | Mean | Range |
|---|---|---:|---|
| Hotels | 7, 6, 5, 7, 6, 6 | 37 / 6 = 6.17 | 5–7 |
| Flights | 6, 5, 6, 7, 5, 6 | 35 / 6 = 5.83 | 5–7 |

The new type field showed that 15 of Hotels' 37 escaped defects (41%) fell into two types: date and time-zone handling (9) and missing input validation (6). The same mistakes were being made release after release. The time logged on Hotels' escaped-defect tickets in those releases averaged about 6 hours per defect: diagnosis, fix, review and the on-call evening.

## 3. Choose the method

The [method table](03-improvement-methods.md) starts from the kind of problem, not from the method:

| If the problem is | Method | Does it fit here? |
|---|---|---|
| defects of the same kind keep recurring | Defect Prevention Process | **yes:** two types make up 41% |
| one stage limits the whole flow | Theory of Constraints | no: lead time is not the complaint |
| output varies too much | Six Sigma | no: six releases are too few data points for its statistics |
| the team wants to work better in general | retrospective | already held every sprint, without effect on these types |

**Decision point.** Criterion: a recurring defect type with records to analyze. Both hold, so the proposal is DPP {% cite mays1990defect %}. The retrospective's failure is itself a clue: it asked "what went wrong this sprint", a single-loop question (correct this error), while DPP asks which part of the process let this kind of defect through, a double-loop one (change the rule that produced it; see [Learning & Improvement Models](02-learning-improvement-models.md)).

## 4. Write the proposal as one PDCA cycle

The proposal follows Plan–Do–Check–Act {% cite deming1986crisis %}, with the criteria and the decision rule fixed **before** the pilot starts, so that nobody redefines success during the dip:

1. **Plan.** Hypothesis: causal analysis of escaped and late-found defects, with an action team that changes checklists, tests and tools, reduces Hotels' escaped defects. Three criteria, measured on the sprints after the dip: (C1) Hotels' percentage change in mean escaped defects from its baseline, minus Flights' percentage change, is −25 points or lower; (C2) every one of those sprints is below Hotels' lowest baseline value, 5; (C3) the hours saved, (baseline mean − pilot mean) × 6 hours per escaped defect, at least cover the hours DPP takes. Decision rule: all three met → institutionalize; C1 and C2 met but not C3 → adjust the format to cut its cost and re-run the window; C1 or C2 missed → stop.
2. **Do.** Hotels holds a two-hour causal-analysis meeting each sprint, led by a trained team member, on that sprint's escaped and staging defects; an action team of the tech lead, one developer and a platform engineer implements the actions; each sprint planning opens with a five-minute kickoff on the common errors {% cite mays1990defect %}. Flights changes nothing but is measured the same way.
3. **Check.** Thirty days after sprint 6's release, when its escaped defects are known, compare both teams on escaped defects and throughput.
4. **Act.** Apply the decision rule.

**Decision point: how long is the window?** Criterion: long enough to get past the dip and the lag. Meetings and action work take time from features at once, while the benefit arrives only with code written *after* the first actions and is visible only when that code has been in production for 30 days. This is Repenning and Sterman's worse-before-better pattern {% cite repenning2001nobody %}. The window is therefore six sprints; sprints 1 and 2 are reported but excluded from the target.

The pilot's cost: the meeting takes 6 people × 2 hours = 12 hours a sprint and the actions about 10 more (the action team's estimate), 22 hours of the team's roughly 360 sprint hours (6 developers × 60 focused hours), about 6%.

## 5. Run the pilot and measure

Escaped defects and delivered story points (each team's own estimation unit, so compare a team only with itself) per sprint _(constructed)_:

| Sprint | Hotels escaped | Hotels points | Flights escaped | Flights points |
|---|---:|---:|---:|---:|
| Baseline mean | 6.17 | 40 | 5.83 | 38 |
| 1 | 7 | 35 | 6 | 38 |
| 2 | 6 | 35 | 6 | 38 |
| 3 | 4 | 38 | 6 | 38 |
| 4 | 4 | 39 | 5 | 38 |
| 5 | 3 | 39 | 6 | 38 |
| 6 | 4 | 40 | 5 | 38 |

The dip is visible: in sprints 1 and 2 Hotels delivered 35 points instead of 40, about 12% less, and escaped defects did not move. A manager reading only those two sprints would stop the pilot, which is exactly the trap the fixed window guards against.

Sprints 3 to 6 against the target:

| Measure | Hotels | Flights |
|---|---:|---:|
| Mean escaped, sprints 3–6 | (4 + 4 + 3 + 4) / 4 = 3.75 | (6 + 5 + 6 + 5) / 4 = 5.5 |
| Change from baseline | (3.75 − 6.17) / 6.17 = −39% | (5.5 − 5.83) / 5.83 = −6% |
| Every sprint below the team's lowest baseline value? | yes (all ≤ 4 < 5) | no |

C1: −39 − (−6) = −33 points, beyond the required −25. Of Hotels' 15 escaped defects in sprints 3 to 6, one was a date or time-zone defect, against 9 of 37 before. The action team's three main actions explain why: a shared date-handling helper with property-based tests for time-zone boundaries, a validation checklist item in review, and a linter rule against naive `datetime` objects.

## 6. Decide

The pilot's results against the three criteria of the plan:

| Criterion | Result | Met? |
|---|---|---|
| C1: change versus control −25 points or lower | −33 points | yes |
| C2: every post-dip sprint below 5 | 4 of 4 sprints | yes |
| C3: hours saved cover hours spent | (6.17 − 3.75) × 6 ≈ 15 hours saved, against 22 spent | **no** |

**Decision: adjust**, as the rule says for C1 and C2 met without C3. The effect is real but, at pilot cost, it does not pay in hours, before counting users' frustration, which hours leave out. IBM's teams analyzed a sample of defects, at times a third {% cite mays1990defect %}, so Hotels moves to a one-hour meeting on a sample: 6 meeting hours plus about 5 action hours (the action team's estimate for fewer analyzed defects), 11 hours a sprint, below the 15 saved. The window is re-run with the lighter form; if all three criteria then hold, DPP is institutionalized and Flights becomes the next pilot, with a third team as its control.

**Keeping it alive.** Andrews and Stalick's two levels both need changing {% cite andrews1994street %}: the operational change is the meeting, the action team and the tracker's type field; the cultural change is that defect discussions stay blameless and that the action team's changes are funded. Pfeffer's last filter is persistence {% cite pfeffer1998human %}, so the head of engineering committed to two more quarters, whatever the first sprints of the re-run show.

## Common mistakes

1. **Choosing the method first.** "We will do Six Sigma" before anyone has looked at the defects. Spot it: the proposal names a method before it states a problem with numbers.
2. **No control group.** A drop in escaped defects during a quiet season proves nothing. Spot it: only the pilot team's numbers before and after.
3. **Judging during the dip.** Stopping after two sprints because throughput fell. Spot it: success criteria written after the pilot started, or none written at all.
4. **A meeting without an action team.** Causal analysis produces lists nobody implements. Spot it: action items with no owner or date, or the same defect type in the next analysis.
5. **Counting only hours.** User harm and on-call load are part of the case. Spot it: a decision table with a single cost column.

## How solid is this?

- **Where it comes from.** DPP from Mays and colleagues; PDCA from Deming; the dip and the capability trap from Repenning and Sterman; the two levels of change from Andrews and Stalick; the persistence filter from Pfeffer; the Level 2 process areas from CMMI {% cite mays1990defect deming1986crisis repenning2001nobody andrews1994street pfeffer1998human cmmi-dev-2010 %}.
- **What is contested.** The criteria (25 points, below the baseline minimum) are practitioner rules of thumb for small samples, not statistical tests; with six data points per team, a pilot can only show a large effect.
- **What we do not hold.** A controlled study of DPP in agile teams; IBM's results compare products with their own history.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
