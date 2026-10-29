---
title: Reading Techniques
parent: Inspection
nav_order: 2
layout: default
---

# Inspection Reading Techniques

A **reading technique** tells an inspector how to examine a product during individual preparation: what to look for, and in what order {% cite laitenberger2000survey %}. It matters because preparation is where most defects are found: 90–95% of them, with the meeting adding only about 5–10% new ones {% cite aurum2002inspections laitenberger2000survey %}.

## 1. Four techniques, from no guidance to a task

| Technique | The inspector | Reported effect |
|---|---|---|
| **Ad hoc** | reads with no guidance, relying on experience | the baseline; varies with the person |
| **Checklist** | answers a list of questions about the product | better than ad hoc; says *what* to check, not *how* |
| **Scenario-based** | follows a procedure aimed at one aspect: in **defect-based reading** each inspector gets a different defect class; other variants are organized around inputs, files and outputs | about 35% more defects than ad hoc and checklist reading in some experiments {% cite aurum2002inspections %} |
| **Perspective-based (PBR)** | takes a stakeholder's role and builds that stakeholder's work product from the document | 21–30% better individual detection than NASA's usual technique {% cite basili1996empirical %} |

The two figures come from different experiments and baselines, so they cannot be ranked.

## 2. Checklists catch omissions

A good checklist is short and aimed at the team's own common mistakes. Cohen's rules {% cite cohen2006secrets %}: keep the 10 to 20 items most often violated; grow and prune the list from the defect log, adding an item for each recurring defect type that nothing caught; and prevent the most-violated items with a tool where possible. Practitioner rules of thumb are stricter: about seven items per reading pass, and no style items a linter can check. The [worked case](worked-case.md#step-2-the-checklist) has a 16-item Python checklist built this way. Checklists are strongest at **omissions**, the defects hardest to see: each item names something that should be present, so its absence becomes visible.

A security review is a scenario: Howard reviews first old or legacy code, code running by default or with elevated privileges, anonymously accessible or network-listening code, code in memory-unsafe languages and code with past vulnerabilities, then traces input from each entry point through its validation {% cite howard2006process %}.

## 3. Perspective-based reading builds a model

In PBR each reviewer reads a requirements document as one of its consumers and builds what that consumer needs {% cite basili1996empirical shull2000perspective %}:

![A requirements document read by a tester building a test plan, a designer building a design and a user building a user manual; where a model cannot be built, there is a defect](pbr-perspectives.svg)

_The three PBR perspectives; a defect shows up as a step where the reviewer cannot continue._ {% cite basili1996empirical %}

**Example** (constructed). The requirement reads: "After three failed login attempts the account is locked." The **tester** writes test cases and gets stuck: do the three attempts have to be consecutive, and does a successful login reset the count? The **designer** needs to store the counter and cannot tell whether a lock expires or needs an administrator. The **user** writes the manual page "Your account is locked" and cannot tell whether the user is warned before the lock or told how many attempts remain. Three perspectives find three kinds of defect: the tester a requirement with no testable value or rule, the designer missing or inconsistent data and interfaces, the user missing information for users. The rule behind all three: where the model cannot be built, there is a defect. For [capture-recapture](capture-recapture-practice.md), count one defect per needed fix: each perspective's questions are settled by one addition, so these are three defects, each found by one inspector; had one rewrite fixed all three, it would be one defect found by three. Basili's procedures give each perspective its own questions to answer while building its model {% cite basili1996empirical %}. ("Tester" is a perspective here, not Fagan's role.)

## When to use it — and when not

Use PBR for requirements and other documents read by several roles, especially in a domain the reviewers do not know well, and to train new reviewers {% cite shull2000perspective %}. Teams using PBR covered more of the document between them than teams using NASA's usual technique, and the method gives a definite procedure: less experienced reviewers who followed it closely often did better than seniors who fell back on their own habits {% cite basili1996empirical %}. Use a focused checklist for code and for short reviews. Read ad hoc only when nothing else fits the time.

## Limitations

- **PBR costs more time:** up to 30% more effort than the usual approach, partly repaid because the test plan and design sketches are useful later {% cite shull2000perspective %}.
- **Experts may not gain.** Reviewers with well-developed personal heuristics may see no significant benefit {% cite shull2000perspective %}.
- **Checklists go stale.** Without pruning, a list turns into box-ticking and misses what is not on it.

## How solid is this?

- **Where it comes from.** The PBR experiments at NASA Goddard's Software Engineering Laboratory, Shull's implementation guide, and the inspection surveys {% cite basili1996empirical shull2000perspective laitenberger2000survey aurum2002inspections %}.
- **What is contested.** Effect sizes vary between experiments, documents and reviewer populations; the 35% and 21–30% figures are single results, not general constants.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
