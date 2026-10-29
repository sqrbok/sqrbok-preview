---
title: "SN A02: Process Improvement"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 3
redirect_from: /content/organization/03-process/SN-A2-Proc.html
layout: default
page_type: study-notes
---

# SN A02: Process Improvement

The lecture asks why organizations that know what good practice looks like so rarely follow it, and what methods and frameworks help them improve how they build software. [RQ A02: Process Improvement](RQ-A2-Proc.md) tests this lecture.

## 1. Organizations say one thing and do another

**Software process improvement (SPI)** is a deliberate change of an organization's technical practices, structure and culture to get better and more predictable results {% cite kuhrmann2016spi %}. Watts Humphrey described a **credibility gap**: managers say measurement, learning and planning matter, yet many measure little, cut training first and rely on **heroics**, a few people working nights before each release {% cite humphrey1989managing %}. His conclusion: professional management should rest on defined, measured processes, so that a success can be repeated.

SPI starts when individual fixes stop working, typically in one of three situations: a large code base keeps producing bugs that customers see; a competent firm is repeatedly beaten to market; a contractor keeps missing milestones on fixed-price work. In each, the quick fix (fix the bug, ask for overtime, promise harder) treats one occurrence, and SPI asks what in the process keeps producing them.

→ [Why Process Improvement?](../../organization/03-process/01-why-process-improvement.md)

## 2. Organizational failure comes before physical failure

The Space Shuttle Columbia was lost in 2003 after a foam strike at launch, a type of event seen on earlier flights and gradually treated as a maintenance matter under schedule pressure {% cite widnall2003columbia %}. Engineers' concerns did not reach decision-makers; in Sheila Widnall's words, "if dissent and questioning are not welcomed, you stop doing it." The physical cause was foam; the reason nobody acted was organizational. Software shows the same pattern when an intermittently failing test is labelled "flaky" and a real failure in it is later ignored.

→ [Why Process Improvement?](../../organization/03-process/01-why-process-improvement.md)

## 3. Only one organization in eight sees the full benefit

Jeffrey Pfeffer's **one-eighth rule** explains why practices with clear evidence spread so slowly {% cite pfeffer1998human %}. Three filters each pass about half of the organizations still trying:

| Filter | Still in |
|---|---|
| believe that management practices affect results | 1/2 |
| try systemic change rather than an isolated fix | 1/4 |
| persist long enough to see results | **1/8 = 12.5%** |

Each filter has a countermeasure: evidence from the organization's own data (belief), changing structure and incentives along with the practice (systemic change), and protecting the program through the first months of lower performance (persistence, §5). The rule is a rule of thumb, three halvings, not a measured rate.

→ [Change Management](../../organization/03-process/04-change-management.md)

## 4. Double-loop learning fixes the system, not just the error

Argyris and Schön distinguish **single-loop learning**, which corrects an error and keeps the same rules ("restart the server"), from **double-loop learning**, which also changes the rule or assumption that let it happen ("add memory monitoring to every deployment") {% cite argyris1978organizational %}. Single-loop talk sounds like "someone screwed up"; double-loop talk like "the system is failing."

Two routines make the second loop a habit. **PDCA** (Plan, Do, Check, Act) tests one change at a time: plan a measurable change, try it small, check the result against the target, then standardize it or adjust {% cite shewhart1931economic deming1986crisis %}.

**Example.** Applied to bug escapes: *plan* to require a test for every bug fix and halve escapes; *do* it in one pilot team for a quarter; *check* that team's escapes against its own previous quarter and against the teams that did not change; *act* by adding the rule to every team's definition of done if the pilot team's escapes fell and the others' did not.

**Kaizen** is continuous improvement by everyone, on four principles: change is incremental, everyone takes part, ideas come from the bottom up, and it never stops. Retrospectives and continuous delivery with small, frequent changes are Kaizen in software.

→ [Learning & Improvement Models](../../organization/03-process/02-learning-improvement-models.md)

## 5. Working smarter makes things worse before better

Repenning and Sterman studied improvement programs in a dozen companies {% cite repenning2001nobody %}. Facing a performance gap, a manager can make people **work harder** (overtime, shortcuts), which raises output at once, or **work smarter** (time for improvement), which lowers output first because the time comes from production and raises capability only later. Under pressure managers choose the first; capability erodes, problems grow and there is even less time to improve: the **capability trap**. A **self-confirming attribution error** keeps them there: pressure does give a short-term gain, so managers conclude that the problem was effort, not the system.

Programs that survive the dip can pay off; the paper reports the payoff, not the size of the dip. At BP's Lima refinery, pumps' mean time between failures rose from 12 to 58 months, and the program created about USD 43 million a year of value for about USD 320,000 a year. Hence the paper's title: nobody ever gets credit for fixing problems that never happened.

→ [Change Management](../../organization/03-process/04-change-management.md)

## 6. Each improvement method starts from a different problem

| Method | Starts from | Routine |
|---|---|---|
| IBM's **Defect Prevention Process (DPP)** | recurring defects | causal-analysis meeting after each stage; an action team removes the cause {% cite mays1990defect %} |
| **Theory of Constraints (TOC)** | one stage limits the flow | five focusing steps {% cite rahman1998toc %} |
| **Six Sigma** | too much variation | DMAIC {% cite antony2002sixsigma %} |
| **Agile retrospective** | the team wants to work better | reflection and actions every iteration {% cite lehtinen2017retrospectives %} |

**DPP** has four components: the **causal-analysis meeting** (about two hours, run by the developers, classifying each defect's cause as communication, oversight, education or transcription), the **action team** that changes process, tools, checklists or training, the **stage kickoff meeting** that feeds the changes into the next stage, and **data collection and tracking** of every action. IBM reported 54% fewer defects during development and field defects 60% below the previous eight releases for about 0.5% of the area's resources.

**TOC** says every system has a constraint and improving anything else does not help. Its five focusing steps are *identify* the constraint, *exploit* it (never let it idle or do the wrong work), *subordinate* everything else to its pace, *elevate* it (add capacity) and *repeat* once it moves, watching that inertia does not become the new constraint. If review is the bottleneck: make sure reviewers only review, let a linter catch formatting, start no more changes than review can absorb, train more reviewers, then look for the next constraint.

**Six Sigma** improves a process by measuring its variation, aiming at 3.4 defects per million opportunities, through **DMAIC**: *Define* the problem and what is critical to quality (CTQ), *Measure* the baseline, *Analyze* the causes with data, *Improve*, and *Control* to keep the gain. Because it counts defects per opportunity and measures variation, it needs a process that repeats many times with comparable outcomes, such as a CI build that runs on every change; the design of a new feature happens once, so there is no distribution to measure.

DPP and retrospectives are often confused: DPP is triggered by defects, takes the stage's defect records as input and asks what in the process caused them, with an action team of cross-project authority; a retrospective is triggered by the end of an iteration, takes the team's experience of that iteration as input, ideally plus repository data, and asks what the team itself should change.

→ [Improvement Methods](../../organization/03-process/03-improvement-methods.md), [Defect Prevention Process](../../organization/03-process/05-defect-prevention.md), [Theory of Constraints](../../organization/03-process/06-theory-of-constraints.md), [Six Sigma](../../organization/03-process/07-six-sigma.md)

## 7. CMMI describes what a mature process contains

**CMMI** (Capability Maturity Model Integration) from the SEI groups best practices into **process areas (PAs)**, each with **specific goals and practices**, plus **generic goals and practices** that apply to every PA and *institutionalize* it: a policy, resources, training, responsibility and measurement, so the process survives deadlines and staff changes {% cite cmmi-dev-2010 %}. In the **staged** representation an organization is rated at one of five maturity levels; in the **continuous** representation each PA gets its own capability level.

| Level | Name | Key change | PAs added (v1.3) |
|---|---|---|---|
| 1 | Initial | ad hoc, heroics | none |
| 2 | Managed | projects plan and control their work; practices are kept under stress | REQM, PP, PMC, SAM, MA, PPQA, CM (7) |
| 3 | Defined | organization-wide standard processes, tailored per project | RD, TS, PI, VER, VAL, OPF, OPD, OT, IPM, RSKM, DAR (11) |
| 4 | Quantitatively Managed | process and quality managed with statistics | OPP, QPM (2) |
| 5 | Optimizing | continuous improvement from understanding variation | OPM, CAR (2) |

The abbreviations: REQM Requirements Management, PP Project Planning, PMC Project Monitoring and Control, SAM Supplier Agreement Management, MA Measurement and Analysis, PPQA Process and Product Quality Assurance, CM Configuration Management; RD Requirements Development, TS Technical Solution, PI Product Integration, VER Verification, VAL Validation, OPF Organizational Process Focus, OPD Organizational Process Definition, OT Organizational Training, IPM Integrated Project Management, RSKM Risk Management, DAR Decision Analysis and Resolution; OPP Organizational Process Performance, QPM Quantitative Project Management; OPM Organizational Performance Management, CAR Causal Analysis and Resolution.

7 + 11 + 2 + 2 = 22 PAs. Each PA also has one of four categories: Process Management, Project Management, Engineering or Support. The transitions read: from chaos to project discipline (1→2), from project-specific to organization-wide (2→3), from qualitative to quantitative (3→4), from control to continuous improvement (4→5). CMMI v2.0 speaks of practice areas and merges the development, acquisition and services models into one {% cite astridita2024cmmi %}.

→ [CMMI Overview](../../organization/04-standards/01-cmmi-overview.md)

## 8. CMMI and Agile fit at the project level

Agile practices cover most of Level 2's project-level PAs, but not the organization-level PAs of Level 3, and they have no counterpart for the statistical management of Levels 4 and 5 {% cite ferdinansyah2021cmmi %}. Configuration management is the usual gap at Level 2. A review of CMMI v2.0 with Agile quotes a 17% gain in estimation accuracy, 70% less rework and 97% on-time delivery {% cite astridita2024cmmi %}, but these are the CMMI Institute's own figures, relayed. Culture decides adoption: Tsai found CMMI Level 3 succeeded with reform-minded teams and group rewards {% cite tsai2020cmmi %}.

→ [CMMI Adoption & Integration](../../organization/04-standards/02-cmmi-adoption-integration.md)

## 9. Change must reach culture, not only structure

Andrews and Stalick separate **operational changes** (structure, process, technology, rewards), which are visible and comparatively easy, from **cultural changes** (mental models, power structures, tolerance of dissent), which decide whether the operational ones last {% cite andrews1994street %}. A new review policy fails if managers still reward whoever ships fastest.

Surveys of Six Sigma, Lean Six Sigma and ITIL adoption all rank **management commitment** first, then culture, training and communication {% cite antony2002sixsigma laureani2012leansixsigma iden2013itil %}: without it, improvement time is lost to firefighting. In a study of 24 practitioners adopting CMMI Level 3 in Bangalore, 42% of interview responses named employee resistance, 29% the tension between standard processes and client-driven flexibility, and 25% insufficient training; the authors recommend pilot groups, role-based training and combining CMMI with Scrum or SAFe (the Scaled Agile Framework) {% cite shelat2025cmmi %}. Repenning and Sterman add: protect improvement time, let staff experience the dynamics in simulations, and let early champions spread the change.

→ [Change Management](../../organization/03-process/04-change-management.md)

## Where this goes next

Process improvement and CMMI are two of several frameworks; [Quality Standards](../../organization/04-standards/index.md) adds ITIL, ISO 9000 and TMMi. A03 moves from frameworks to the everyday practices of Google, Microsoft and Facebook.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
