---
title: "SN A10: Maintainability & DSM"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 19
redirect_from: /content/attributes/maintainability/SN_A10_MaintainabilityDSM.html
layout: default
page_type: study-notes
math: katex
---

# SN A10: Maintainability & DSM

Three parts: decay and code ratings (§1–6), structure in a design structure matrix (§7–11), refactoring evidence (§12). Tested by [RQ A10](RQ_A10_MaintainabilityDSM.md).

## 1. Maintenance dominates because used software must keep changing

Lehman's Laws I and II {% cite lehman1980programs %}:

- **Law I, continuing change:** a program used in the real world must keep adapting or become progressively less satisfactory.
- **Law II, increasing complexity:** as a program evolves, its complexity increases unless work is done to reduce it.

**Technical debt** is the extra cost of future changes caused by shortcuts now: **principal** is the repair cost, **interest** the extra cost of each later change, rising as complexity grows (Law II). Changes come in four kinds {% cite iso14764_2022 %}: **corrective** (fix failures), **adaptive** (keep up with a changed environment), **perfective** (improve, add features) and **preventive** (fix latent problems, e.g. refactoring).

→ [Software Evolution and Aging](../../attributes/maintainability/evolution.md)

## 2. Software ages through neglect and through ignorant surgery

Parnas names two causes {% cite parnas1994aging %}. **Lack of movement** is the owners' failure to adapt the product to changing needs. **Ignorant surgery** is change made without understanding the original design. For example, a tax rate added in the report module instead of the tax module. His prevention: **design for change** (hide each decision likely to change behind a module interface) makes timely change cheap, against lack of movement; **keep records** (design documentation) and **seek second opinions** (reviews) stop ignorant surgery.

→ [Software Evolution and Aging](../../attributes/maintainability/evolution.md)

## 3. Maintainability has five ISO sub-characteristics

ISO/IEC 25010 splits maintainability into **modularity** (changes stay local), **reusability** (use in more than one system), **analysability** (diagnosing faults, change impact), **modifiability** (change without new defects) and **testability** {% cite iso25010_2023 %}.

## 4. SIG rates code properties by their distribution, not the average of unit measurements

The SIG (Software Improvement Group) model rates eight source code properties: volume, duplication, unit size, unit complexity, unit interfacing, where a **unit** is a method or function; module coupling (incoming dependencies per class or file), component balance and component independence {% cite sig2016criteria %}. Ratings run from 1 to 5 stars, calibrated so that 5%, 30%, 30%, 30% and 5% of SIG's benchmark systems get 5, 4, 3, 2 and 1 stars, chosen so that only very good systems get 5, which a uniform 20% split would not ensure. A sub-characteristic's rating is the average of its properties' star ratings, never of unit measurements {% cite baggen2012standardized %}.

For unit complexity, each unit goes into a risk category by its cyclomatic complexity (CC): low 1–10, moderate 11–20, high 21–50, very high above 50 {% cite baggen2012standardized %}. Each category's share of lines of code (not cumulative) has a maximum:

| Stars | Moderate at most | High at most | Very high at most |
|---|:-:|:-:|:-:|
| 5 | 25% | 0% | 0% |
| 4 | 30% | 5% | 0% |
| 3 | 40% | 10% | 0% |
| 2 | 50% | 15% | 5% |
| 1 | anything worse | | |

**Worked example.** Code split 70% low, 20% moderate, 8% high, 2% very high fails 5 and 4 stars on "high" and 3 stars on "very high", and gets **2 stars**. SIG rates the distribution because an average would hide those few very complex units.

→ [Measuring Maintainability](../../attributes/maintainability/measurement.md)

## 5. SQALE prices the debt; the Maintainability Index condenses code metrics into one number

SQALE (Software Quality Assessment based on Lifecycle Expectations) rates the **technical debt ratio**: remediation cost of static-analysis findings over development cost {% cite letouzey2012sqale %}. In the method's example, 15 work units (effort, e.g. hours) of fixes on code that cost 500 to develop give 3%: C on the SQALE example grid (A up to 1%, B up to 2%, C up to 4%, D up to 8%, E above), but A on SonarQube's default grid (A up to 5%) {% cite sonarsource_metrics %}.

The **Maintainability Index** (MI) on its original 0–171 scale (radon rescales it to 0–100, see below) is {% cite coleman1994using %}

$$MI = 171 - 5.2\ln V - 0.23\,\mathit{CC} - 16.2\ln L$$

with $$V = N\log_2 n$$ the Halstead volume ($$N$$, $$n$$: all and distinct operators and operands), $$\mathit{CC}$$ the cyclomatic complexity and $$L$$ the lines of code. Coleman's rule of thumb: above 85 highly maintainable, 65–85 moderate, below 65 difficult. Radon takes $$L$$ as **LLOC**, logical lines (statements), and counts **SLOC**, lines neither blank nor only a comment. **Worked example.** A file has 16 logical lines, 17 source lines (one more than LLOC: one statement spans two lines; SLOC excludes comments) and 1 comment line, with $$V = 144.9$$ as given by radon and $$\mathit{CC} = 7$$ (see **Which CC?**): $$171 - 25.88 - 1.61 - 44.92 = 98.6$$, highly maintainable. Radon adds a comment term $$50\sin\sqrt{2.46\cdot\text{radians}(perCM)}$$, with $$perCM$$ the comment percentage, comment lines / SLOC × 100 = 1/17 × 100 = 5.88; radon feeds 5.88 into radians(): radians(5.88) = 0.1027, × 2.46 = 0.2526, √ = 0.5026, sin = 0.4817, × 50 = 24.08. Using radians() is radon's implementation choice, an empirical fit meaningless in the units. Radon rescales to 0–100 (× 100 / 171, clamped): 57.66 without the term, and 71.74, grade A, with it, as `radon mi` reports. Radon grades MI A (above 19), B (10–19), C (≤ 9) on 0–100, unlike Coleman's 85/65 on 0–171 {% cite radon_docs %}.

**Which CC?** CC is decision points + 1 (A02). `radon cc` reports it per function or method: 6 and 2 for this file's two functions. For MI, radon uses one value per file, all decision points + 1, i.e. Σ(function CC − 1) + 1: (6 − 1) + (2 − 1) + 1 = 7. Coleman's original MI used per-module averages of V, CC and L instead {% cite coleman1994using ardito2020maintainability %}.

→ [Maintainability Index](../../define/measurement/maintainability-index.md)

## 6. Cognitive complexity charges for nesting

Cognitive complexity scores how hard a method is to read {% cite campbell2018cognitive %}. It adds 1 for each break in linear flow: `if`, `else if` (`elif`), `else`, ternary, `switch`, loops, `catch`, recursion, and each sequence of the same Boolean operator, even a single one (`a and b` adds 1; `a and b or c` adds 2). A nested `if`, ternary, `switch`, loop or `catch` also adds its depth, which rises inside each of these and inside `else if` and `else`. `else` and `else if` never get the depth extra; the method body itself starts at depth 0, so code extracted into its own method restarts there. **Radon** computes CC (each `and` and `or` is a decision), not cognitive complexity.

```python
def flat(order):
    if order.paid and order.confirmed: ship(order)   # +1 if, +1 and
    if order.gift: wrap(order)                       # +1
    if order.express: notify(order)                  # +1

def nested(order):
    if order.paid and order.confirmed:               # +1 if, +1 and
        if order.gift:                 # +2 (depth 1)
            if order.express:          # +3 (depth 2)
                notify(order)
```

Radon gives CC = 5 for both (three `if`s, one `and`); the `cognitive_complexity` package gives 4 for `flat` and 7 for `nested` {% cite cognitive_complexity_py %}.

→ [Cognitive Complexity](../../attributes/maintainability/cognitive-complexity.md)

## 7. Modular designs multiply options for change

**Coupling** is how much modules depend on each other (keep it low); **cohesion** is how strongly a module's parts belong together (keep it high). Sullivan et al. treat each module as a real option (the right, not the obligation, to replace it): a non-modular design offers one option, $$n$$ independent modules offer $$n$$. An option is worth more the less certain future requirements are; if nothing will change, extra modules cost effort and pay back nothing {% cite sullivan2001structure %}. A design module may span several of SIG's file-level modules. Parnas's KWIC has a processing-step design (a module per step) and an information-hiding design (a module per hidden decision): a change to the line-storage format touches every module of the first, one of the second.

→ [Modularity and Its Value](../../attributes/maintainability/modularity.md)

## 8. A DSM records who depends on whom

A **design structure matrix** (DSM) lists the same elements, in the same order, on rows and columns. Here X → Y means X depends on Y, drawn as a mark in row X, column Y {% cite maccormack2006exploring %}. Some tools use the transpose.  Between two elements there are three patterns: **independent** (no mark), **dependent** (one mark) and **mutually dependent**, a cycle (marks both ways) {% cite yassine2004dsm %}.

**Worked example.** B, C and D depend on A; F on B; G and H on D; H on G; E, in the bottom layer with A, starts calling G. In the intended layer order, the lowest layer drawn in the first rows:

| | A | E | B | C | D | F | G | H |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **A** | . | | | | | | | |
| **E** | | . | | | | | **V** | |
| **B** | × | | . | | | | | |
| **C** | × | | | . | | | | |
| **D** | × | | | | . | | | |
| **F** | | | × | | | . | | |
| **G** | | | | | × | | . | |
| **H** | | | | | × | | × | . |

Every × is below the diagonal: each element uses only lower layers. The mark **V** (E → G) sits above it: a **layering violation**, because a change to G can now break the bottom layer. It is worse when the upper element changes often, many depend on the violator, or the call bypasses an interface. A column marked in many rows is a **vertical bus** (a library; MacCormack et al. use ≥10% of elements, which every called element meets here, even B at 1 of 8; A, used by 3 of 8, is the only clear bus); a row marked in many columns is a **horizontal bus** (a controller using many). If a module's elements form two blocks with no marks between them, split it.

→ [Design Structure Matrix](../../attributes/maintainability/dsm.md)

## 9. Partitioning and tearing reveal layers and break cycles

**Partitioning** reorders the DSM to lower-triangular form by repeatedly placing an element that depends on nothing left to place; elements on a cycle are placed together as a block {% cite yassine2004dsm %}. **Tearing** chooses marks inside the blocks to remove so that re-partitioning is fully lower-triangular; in code, a dependency to break, e.g. behind an interface.

**Worked example.** With A → C, B → F, C → D, C → G, D → B, D → F, E → A, E → C, E → D, E → G and G → B, partitioning gives F B D G C A E (F depends on nothing; ties may go in any order), with all 11 marks below the diagonal.

Add D → C: C and D now form one block, F B G {C D} A E, one mark above the diagonal. **Default tearing rule:** tear as few marks as possible; among equal choices, tear the dependency that violates the intended layering or is cheapest to put behind an interface. One tear suffices: D → C (order F B D G C A E) or C → D (order F B G C A D E). If D is the lower layer (C uses D), tear D → C.

→ [DSM Operations](../../attributes/maintainability/dsm-operations.md)

## 10. Propagation cost measures how far a change can reach

Add the powers of the DSM's 0/1 matrix $$M$$, $$R = \text{binary}\big(\sum_{p=0}^{K} M^p\big)$$, where $$K$$ is the number of elements, $$M^0$$ (the identity) adds the diagonal, and binary(·) sets every non-zero entry to 1, to get the **visibility matrix** (who reaches whom via any chain); **propagation cost** is $$PC = \tfrac{1}{K^2}\sum_{i,j} r_{ij}$$ {% cite maccormack2006exploring %}.

**Worked example.** A depends on B and C, B on D, C on E, E on F. Two-step chains add A→D, A→E and C→F; a three-step chain adds A→F. With the diagonal, the row sums of $$R$$ are 6, 2, 3, 1, 2, 1 = 15, so $$PC = 15/36 = 41.7\%$$.

| | Mozilla, Apr 1998 (first open-source) | Linux 2.1.105 (similar size) | Mozilla, Dec 1998 (redesigned) | Linux 2.1.88 (similar size) |
|---|:-:|:-:|:-:|:-:|
| Files | 1,684 | 1,678 | 1,508 | 1,538 |
| Dependencies | 6,717 | 9,110 | 3,037 | 8,519 |
| PC | 17.35% | 5.82% | 2.78% | 5.65% |

_Source: MacCormack et al._ {% cite maccormack2006exploring %}

Linux had more dependencies but a third of Mozilla's PC: the pattern decides coupling, not the count. The late-1998 redesign cut it 84%: architecture is a choice. PC is not a target, though: an interface hiding a decision adds an element and can raise PC while making change easier.

→ [Propagation Cost](../../attributes/maintainability/propagation-cost.md)

## 11. Not every dependency propagates change

From version-control co-changes in 35 Java projects, Geipel and Schweitzer found that in all but three more than half of the dependencies never carried a change {% cite geipel2009software %}. Nord et al. showed PC stayed at 33% after a strictly layered redesign; weighting each dependency by how well tactics shield it (0.1 well, 0.5 partly, 1 not at all) revealed the improvement {% cite nord2013propagation %}.

→ [Propagation Cost](../../attributes/maintainability/propagation-cost.md)

## 12. Targeted refactoring pays off

At Microsoft, the top 5% of preferentially refactored Windows 7 modules decreased inter-module dependencies by a factor of 0.85, the rest increased them by 1.10, relative to the average change per module; yet they grew in lines of code {% cite kim2014refactoring %}. At ABB, a 30 kloc Java simulation was refactored with about three hours a week of expert help for three months; adding a magnetic component then took 1–2 weeks instead of 6–8, and a cooling method 1–2 weeks instead of 8–12; the paper's 75% and 83% less time compare the upper ends (8 → 2 and 12 → 2 weeks). Tool counts compare 15 kloc of old code (GUI and file code removed; the new version lacked features) with 5 kloc of new: duplicated lines 5,647 → 338, 82% fewer per kloc {% cite wahler2016refactoring %}.

→ [Refactoring Case Studies](../../attributes/maintainability/cases.md)

## Where this goes next

A10 ends QAM; it builds on [Measurement](../../define/measurement/index.md) and [SN A06: Static Analysis](SN_A06_Static.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
