---
title: "SN A05: Software Inspection"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 9
redirect_from: /content/verif/inspection/SN_A05_Inspection.html
layout: default
page_type: study-notes
math: katex
---

# SN A05: Software Inspection

The lecture asks how people, rather than tests, find defects in requirements, designs and code, and how a team knows when it has found enough. [RQ A05: Software Inspection](RQ_A05_Inspection.md) tests this lecture.

## 1. Inspection moved from meetings to small asynchronous reviews

**Inspection** is a structured examination of a work product by people other than its author, with the single goal of finding defects; it needs no running program. The word is used in two senses: the umbrella term for structured peer review, and the most formal review type (§2). After Fagan's method the practice changed four times {% cite fagan1976design basili1996empirical cohen2006secrets sadowski2018codereview zeng2025swrbench %}:

| Period | Practice | What changed |
|---|---|---|
| 1976–1986 | Fagan inspection at IBM | six steps, four roles, a meeting, rate limits (§3) |
| 1996–2002 | reading techniques | detection shifts to individual preparation |
| 2006 | lightweight, tool-assisted review (Cisco) | no meeting; limits on size, rate and time |
| 2015–2018 | modern code review (Google, Microsoft) | every change, one reviewer, hours not days |
| 2025 | LLM reviewers | not ready: an F1 (precision, the share of right comments, combined with recall, the share of issues found; §7) of about 19% |

Three principles survived: someone other than the author reads the product, preparation does most of the finding, and small pieces are reviewed best.

→ [Inspection](../../verif/inspection/index.md)

## 2. More formal reviews find more defects and cost more

Wiegers orders peer reviews from most to least formal {% cite wiegers2002peer %}: **inspection** (trained moderator, roles, entry and exit criteria (§3), individual preparation, a logging meeting, verified rework); **team review** (planned and structured but lighter); **walkthrough** (the author leads the group through the product to explain it and collect comments); **pair programming** (continuous review while writing); **peer deskcheck** (the author asks one colleague, who prepares alone; nobody leads); **passaround** (several colleagues read copies at the same time); and **ad hoc review** (a colleague asked on the spot, with no preparation). Do not confuse a walkthrough with an inspection: in a walkthrough the author presents and nobody prepares; in an inspection a **moderator** (plans and leads; not the author) runs it, a **reader** (not the author) paraphrases aloud, and everyone prepares.

Formality pays: a telecommunications company found 16 to 20 defects per thousand lines by inspection against 3 with informal reviews; at Ford, inspections found 50% more than walkthroughs.

Finding a defect took a most likely 1.58 staff-hours in design inspection and 1.46 in code inspection, against a most likely 6 hours (4.5 to 17) in testing {% cite dodd2003inspection %}. A defect fixed in testing costs 8 to 12 times one fixed in development, and after release 30 to 100 times {% cite cohen2006secrets %}. Inspection and testing are complementary, not alternatives: inspection runs earlier, on any document, and finds the defect itself, including omissions and ambiguities; testing needs executable code and shows a failure whose cause must then be debugged, often one from timing, data or the environment.

→ [Inspection Effectiveness](../../verif/inspection/effectiveness.md)

## 3. Fagan's inspection has six steps, four roles and hard limits

Fagan's six steps are **planning** (the moderator checks the entry criteria, forms the team, schedules), **overview** (the author explains the product), **preparation** (each inspector studies it alone), the **meeting** (the reader paraphrases, the team logs defects), **rework** (the author fixes every defect) and **follow-up** (the moderator checks every fix and the exit criteria) {% cite fagan1976design %}. Entry criteria keep unready products (code that does not compile) out; exit criteria make "done" a decision based on evidence. Skipping or merging steps lowers effectiveness, and the follow-up matters because about one fix in six is wrong or introduces a new defect {% cite fagan1986advances %}.

The four roles are **moderator** (plans, leads, follows up; Fagan's "key person"), **author** (overview, answers, rework), **reader** (paraphrases the product; never the author, whose explanation would fill the gaps) and **tester** (inspects from the testing viewpoint). Logging the defects is an extra duty, the **recorder**'s.

The ground rules: find problems, do not solve them (solving eats the meeting); the reader, not the author, presents; nobody attends without having prepared (preparation finds most defects); stop after two hours, because participation is "extremely taxing"; criticize the product, never the producer. The last is Weinberg's **egoless programming**, as Wiegers uses it: treating one's work as open to the team's improvement, because an author who feels attacked defends the code instead {% cite wiegers2002peer %}. Fagan's rates are 500 NCSS (non-commentary source statements) per hour for the overview, 100–125 for preparation and at most 125 in the meeting, with a team of four. For example, four people inspecting a 400-NCSS module spend 4 × 400/500 = 3.2 staff-hours on the overview, 4 × 400/125 = 12.8 on preparation and 4 × 400/100 = 16 in the meeting (4 hours at 100 NCSS/h, below preparation's 125 because the reader paraphrases aloud: two meetings): 32 before rework.

→ [Fagan Inspection Process](../../verif/inspection/fagan-process.md)

## 4. How inspectors read matters more than the meeting

Preparation finds 90–95% of defects; the meeting adds about 5–10% new ones and mainly weeds out false positives {% cite aurum2002inspections laitenberger2000survey %}. A **reading technique** tells inspectors how to prepare. **Ad hoc** reading gives no guidance. A **checklist** lists questions but not how to answer them. **Scenario-based** reading gives a procedure for one class of defect, and found about 35% more defects than ad hoc and checklist reading in some experiments {% cite aurum2002inspections %}. **Perspective-based reading (PBR)** improved individual detection by 21–30% over NASA's usual technique {% cite basili1996empirical %}.

In PBR each reviewer takes a stakeholder's role (a perspective, not one of Fagan's roles) and builds that stakeholder's product from a requirements document: the **tester** writes test cases, the **designer** a high-level design, the **user** a user manual or function list. Where the model cannot be built, there is a defect: the tester finds a requirement with no testable value or rule, the designer missing or inconsistent data and interfaces, the user a missing user task. PBR costs up to 30% more effort, partly repaid by the models, and novices who followed it closely often beat seniors {% cite shull2000perspective %}.

A good checklist is short (10 to 20 items), lists the mistakes the team actually makes most often, and is pruned when a mistake stops occurring {% cite cohen2006secrets %}. A long, generic or never-updated list turns into box-ticking. Checklists are most effective at **omissions**, the hardest defects to see because nothing on the page points to them: each item names something that should be present, so its absence becomes visible. A security review is a scenario: Howard reviews first legacy code, privileged code, anonymously reachable or network-listening code, memory-unsafe languages (C, C++, assembly) and code with past vulnerabilities {% cite howard2006process %}.

→ [Reading Techniques](../../verif/inspection/reading-techniques.md)

## 5. Modern code review works on small changes, with limits

In 2,500 reviews of 3.2 million lines by 50 programmers at Cisco, defects found per line fell sharply above 200–400 lines reviewed at once, to near zero beyond 400 {% cite cohen2006secrets %}. Three "magic numbers" follow: review at most **200–400 lines** in a sitting, at most **300–500 lines per hour**, for at most **60–90 minutes**, because after that reviewers tire, lose focus and find less. Above about 500 lines per hour a review is a rubber stamp; in three open-source systems McIntosh and colleagues found that changes reviewed faster than their cut-off of 200 lines per hour (a study threshold, not a recommendation), or approved without discussion, had more post-release defects {% cite mcintosh2015codereview %}. Fagan's meeting and preparation limits (125 statements per hour, two hours) are tighter because a group paraphrases aloud, while a Cisco reviewer reads a diff alone. Authors who annotate their change find defects themselves, and reviewing 20–33% of the code creates an **ego effect**: knowing others will read it improves all code. A SmartBear customer cut support calls from 50,000 to 20,000 a year, a reported USD 2.6 million saved.

At Google the median change has 24 lines and one reviewer, and the median review takes under 4 hours, against Fagan's four people and scheduled meetings {% cite sadowski2018codereview %}. Its developers review mainly for education, norms, owner gatekeeping and accident prevention; defects come second. Of 1.5 million comments in five Microsoft projects, 65.5% were useful; reviewers who had worked on the file wrote 65–71% useful comments, first-time reviewers only 32–37% {% cite bosu2015codereview %}.

Anti-patterns undo all this: reviewing more than 400 lines at once or faster than 500 lines per hour; approving without reading or discussing ("LGTM"); an author who justifies instead of clarifying; and review data used in performance evaluations. Counting defects against authors makes them stop submitting work {% cite wiegers2002peer %}. Watched developers game or hide the data {% cite cohen2006secrets %}. The remedies are cultural: review the code, not the person; use reviews for mentoring; include praise; keep metrics for improving the process.

→ [Modern Code Review](../../verif/inspection/modern-code-review.md)

## 6. Two estimates tell a team how many defects remain

Both estimates count **major** defects only: a major defect would probably cause a failure or a wrong implementation if left in, a minor one would not (a spelling mistake). **Mills' defect seeding** plants *P* known defects, runs the inspection, counts the *F*<sub>P</sub> planted and *F*<sub>R</sub> real defects found, and estimates *N* = *F*<sub>R</sub> × *P* / *F*<sub>P</sub> total real defects, assuming both kinds are found at the same rate {% cite mills1972statistical %}. For example, with 12 planted, 4 of them found and 11 real found, *N* = 11 × 12 / 4 = 33, so about 22 remain. Planted defects must be as hard to find as real ones.

**Capture-recapture** uses the overlap between independent inspectors instead {% cite briand2000capture %}. Capture-recapture has two estimators. With two inspectors finding *n*<sub>1</sub> and *n*<sub>2</sub> defects, *m*<sub>2</sub> of them the same (the overlap), the **Lincoln–Petersen** estimate of the total is $$\hat{N} = \dfrac{n_1 \times n_2}{m_2}$$. As with tagged fish recaught, the share of A's defects that B also found, *m*<sub>2</sub>/*n*<sub>1</sub>, estimates B's share of all defects, *n*<sub>2</sub>/*N*. For 20, 15 and 6: $$\hat{N}$$ = 50, with 29 found, so about 21 remain. Inspectors may differ in skill (several may be pooled into one), but every defect must be equally easy to find. A small overlap makes it large and unstable (*m*<sub>2</sub> = 3 gives 100); *m*<sub>2</sub> = 0 gives no estimate.

Briand and colleagues compared four models, named by what makes detection probability vary: M<sub>0</sub> nothing; M<sub>t</sub> the inspector (each is one capture occasion; Lincoln–Petersen is this model with two); M<sub>h</sub> the defect (heterogeneity: some defects are harder); M<sub>th</sub> both. Defect difficulty mattered most, so they recommend M<sub>h</sub> with the **jackknife** estimator and at least **four** inspectors. Its simplest, first-order form uses *S*, the distinct defects found; *f*<sub>1</sub>, those found by only one inspector; and *k*, the number of inspectors: $$\hat{N} = S + f_1 \times \dfrac{k-1}{k}$$ {% cite burnham1978estimation %}. The method assumes closure (no defects added or fixed meanwhile) and independent inspectors; all models tend to **underestimate**, as inspectors overlap mostly on easy defects.

The moderator matches duplicates (one defect per needed fix), the meeting confirms majors, and each defect is credited to every inspector who listed it. Defects first raised in the meeting are found but not inputs, because they are not independent captures, and are subtracted separately (seeding counts them, since its ratio uses all finds): remaining = $$\hat{N}$$ − *S* − meeting-only finds, at least 0. Example: four inspectors, 16 distinct major defects, 6 found by only one inspector, none raised in the meeting: $$\hat{N}$$ = 16 + 6 × 3/4 = 20.5, so 4.5 remain, 0.225 per page on 20 pages. Against an exit criterion of at most 0.25 remaining major defects per page {% cite malotaux2008onepage %} the document **passes, narrowly**; since the estimate is uncertain and tends to be low, record it as a narrow pass and watch later defects. Report the jackknife.

With perspective-based reading, detection varies by inspector and by defect (M<sub>th</sub>), but the course keeps M<sub>h</sub>. Less overlap between perspectives pushes the estimate up, harder defects push it down, so the net bias is uncertain (the course's reading). Assigning two inspectors the tester perspective (for requirements) restores some overlap.

→ [Defect Seeding](../../verif/inspection/defect-seeding.md), [Capture-Recapture Estimation](../../verif/inspection/capture-recapture.md), [Capture-Recapture in Practice](../../verif/inspection/capture-recapture-practice.md)

## 7. LLM reviewers are an extra pair of eyes, not a reviewer

On SWR-Bench, 1,000 verified pull requests from 12 Python projects, the best LLM-based reviewer reached an F1 score of 19.38%, held back mainly by false positives {% cite zeng2025swrbench %}. Aggregating several review passes raised F1 by up to 43.67% (relative or absolute: unstated), and the tools did better on functional errors than on documentation and style. A person must still check every comment.

→ [Modern Code Review](../../verif/inspection/modern-code-review.md)

## Where this goes next

Inspection is the manual half of static verification; A06 covers the automated half, static analysis.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
