---
title: PSP & TSP
parent: Industry Practices
nav_order: 5
layout: default
---

# Personal Software Process & Team Software Process

The **Personal Software Process (PSP)** is a defined and measured process for one engineer: the engineer follows scripts to plan, design, review and code, and records time, size and every defect, so that their own data show what improves their work {% cite humphrey1995discipline hayes1997psp %}. The **Team Software Process (TSP)** builds self-directed teams out of PSP-trained engineers {% cite mcandrews2000tsp %}. Both come from Watts Humphrey at the SEI.

## 1. PSP: measure yourself

PSP is taught in a course of programming exercises through levels from PSP0 to PSP2.1. At each level the engineer adds practices and keeps the same records:

- **Time** spent in each phase (plan, design, code, compile, test, postmortem);
- **Defects**: type, the phase where each was injected and the phase where it was removed;
- **Size** of the product, in lines of code.

With these data the engineer estimates size and effort from past projects (Humphrey's PROBE method), reviews design and code against a personal checklist built from their own most frequent defects, and tracks progress with earned value.

**Example.** Hayes and Over analyzed 298 engineers in 23 PSP classes {% cite hayes1997psp %}. Median improvements over the course were:

| Measure | Median improvement |
|---|---|
| Size estimates | 2.5 times more accurate |
| Effort estimates | 1.75 times more accurate |
| Product quality (defects found in unit test) | 2.5 times better |
| Process quality (share of defects removed before compiling) | up by 50% |
| Productivity (lines of code per hour) | no significant change |

The underestimates of size and effort that engineers began with turned into a balanced mix of over- and underestimates, which cancel out over many tasks.

## 2. TSP: a team that plans itself

A TSP project starts with a **launch**, a four-day planning process in which the whole team sets goals, chooses its process, assigns roles (team leader plus managers for planning, process, quality and support) and builds a detailed, balanced plan {% cite mcandrews2000tsp %}. Every member takes at least one role; on teams of more than eight, roles are shared {% cite davis2003tsp %}. During the project the team tracks its own data weekly and re-plans when needed.

Davis and Mullaney summarized more than 20 TSP projects in 13 organizations, including Microsoft, Honeywell and Hill Air Force Base {% cite davis2003tsp %}. The teams finished on average **6% later than planned** (range: 20% early to 27% late), improved productivity by an average of **78%**, and delivered **0.06 defects per thousand lines of code** against 7.5 for typical projects, more than two orders of magnitude better. In system test they averaged 0.4 defects per thousand lines, and they spent an average of 4% of their total effort in system test, against 40% in typical projects; failure cost of quality was 17% against 50%. One first-time TSP team reported ten times fewer problems logged by its testers than in the previous release and an eight times shorter system test.

## When to use them — and when not

Use PSP training to give engineers estimation and review skills based on evidence about themselves; use TSP where predictability and very low defect rates are worth the overhead of detailed personal data, as in the organizations Davis and Mullaney report on. The data collection is heavy for teams that release many small changes a day, where automated metrics from the pipeline replace manual logs.

## Limitations

- **Course data.** PSP improvements were measured on course exercises, not on industrial projects.
- **Reported by the method's home.** Both studies are SEI technical reports on the SEI's own methods; the TSP data come from organizations that chose to report.
- **Self-recorded data.** The results depend on engineers logging time and defects honestly and completely.

## How solid is this?

- **Where it comes from.** The SEI's empirical study of PSP, an overview of TSP and a summary of TSP results {% cite hayes1997psp mcandrews2000tsp davis2003tsp %}.
- **What is contested.** Whether the gains come from the process or from the type of organization that invests in it; the reports have no control groups.
- **What we do not hold.** Humphrey's PSP book, cited from the lecture.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
