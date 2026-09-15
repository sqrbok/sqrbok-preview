---
title: Selected Materials
layout: default
nav_order: 5
has_children: true
---

# Selected Materials

A curated selection of books and papers from the SQR and QAM courses. Each entry is chosen for its foundational or canonical contribution — not for volume. One annotation line explains what it contributes and why it is here.

---

## Study Notes & Revision Questions

Per-lecture Study Notes (SN) and Revision Questions (RQ), grouped by course:

- [SQR Study Materials](sqr/index.md) — L01–L10 (bachelor track)
- [QAM Study Materials](qam/index.md) — A01–A10 (master track)

---

## Textbooks

**Naik, K. & Tripathy, P.** — *Software Testing and Quality Assurance: Theory and Practice* (2008, Wiley)
The primary SQR course textbook; covers the full testing lifecycle from black-box methods through reliability engineering in a single coherent framework.

{% cite ammann2016introduction %} — *Introduction to Software Testing* (Ammann & Offutt, 2016, Cambridge UP)
Rigorous graph-model treatment of coverage criteria and test generation; the theoretical foundation behind the adequacy-criteria and domain-testing lectures.

---

## Industry Books

{% cite winters2020swe %} — *Software Engineering at Google* (Winters, Manshreck & Wright, 2020, O'Reilly)
How Google operationalises the 70/20/10 test pyramid, mandatory code review, and error budgets; the industry benchmark for engineering quality at scale.

{% cite beyer2016sre %} — *Site Reliability Engineering* (Beyer et al., 2016, O'Reilly)
Introduces SLOs, error budgets, and toil reduction as engineering disciplines; the definitive source for reliability as a practice, not just a goal.

{% cite page2008mstesting %} — *How We Test Software at Microsoft* (Page, Johnston & Rollison, 2008, Microsoft Press)
Microsoft's milestone quality gates, SDET role, and ship-room culture; the counterpart to the Google book, showing a different model that reaches similar conclusions.

**Whittaker, J., Arbon, J. & Carollo, J.** — *How Google Tests Software* (2012, Addison-Wesley)
Explains Google's testing org structure, the "testing on the toilet" programme, and continuous integration; complements *SWE at Google* with a practitioner's perspective.

{% cite jain1991art %} — *The Art of Computer Systems Performance Analysis* (Jain, 1991, Wiley)
The reference textbook for systematic performance measurement and modelling; covers experimental design, workload characterisation, and queuing models in one volume.

{% cite visser2016building %} — *Building Maintainable Software* (Visser et al., 2016, O'Reilly)
SIG's ten guidelines with industry-calibrated thresholds; translates abstract maintainability into actionable, measurable code-level rules.

---

## Key Papers by Topic

### Defining Quality (L1)

{% cite garvin_what_1984 %} — Five quality views (transcendental, user, manufacturing, product, value). The conceptual lens every quality model discussion starts from; still the most-cited framing paper fifty years later.

{% cite mccall_factors_1977 %} — First metric-based quality factor–criterion–metric hierarchy. The direct ancestor of ISO 25010; shows how abstract quality goals decompose into measurable attributes.

### Metrics and Measurement (L2)

{% cite basili_applying_1993 %} — Goal/Question/Metric paradigm. The "define the question before choosing the metric" discipline that prevents vanity metrics and governs measurement programmes.

{% cite mccabe1976complexity %} — Cyclomatic complexity as a structural coverage criterion. Foundational for basis-path testing and code-risk classification; over 4000 citations.

### Verification Overview (L3)

{% cite nagappan2008tdd %} — TDD at Microsoft and IBM: 40–90% fewer defects with 15–35% longer initial development. The most-cited empirical trade-off data for TDD adoption decisions.

{% cite grady1994key %} — HP inspection ROI: 10:1 return ratio. The canonical industry-scale business case for shifting defect removal earlier in the lifecycle.

### Coverage Criteria (L4)

{% cite inozemtseva2014coverage %} — When test-suite size is controlled, coverage–fault-detection correlation drops to near zero. The ICSE landmark result that grounds all critical discussion of coverage as a quality proxy.

{% cite chilenski1994mcdc %} — MC/DC criterion applicability in avionics (DO-178B). Explains why safety-critical domains require decision coverage beyond simple branch coverage.

### Random Testing and Fuzzing (L6)

{% cite bounimova2010sage %} — SAGE whitebox fuzzer found one-third of all Windows 7 security bugs before release. The industrial-scale proof that greybox fuzzing outperforms black-box random testing.

### Code Inspection and Review (A5)

{% cite fagan1976design %} — Original IBM formal inspection process; 90%+ defect detection before testing. The paper that established structured review as an engineering discipline.

{% cite cohen2006secrets %} — Cisco study (200 programmers, 3.2M LOC): 8–12× cheaper per defect than testing; 200–400 LOC/hour is the optimal review rate. The most-cited empirical guide for review calibration.

{% cite sadowski2018codereview %} — Modern code review at Google: median 24 lines, 1 reviewer, completed in under 4 hours. Documents how asynchronous lightweight review works at scale without formal inspection overhead.

### Static Analysis (A6)

{% cite engler2001bugs %} — Static analysis of Linux/OS X by modelling developer beliefs found 1000+ bugs. The "bugs as deviant behaviour" framing that underpins modern inter-procedural checkers.

{% cite sadowski2015tricorder %} — Google Tricorder at scale: the 10%-false-positive rule and developer-facing presentation that drove adoption. Shows that usability, not precision, determines whether static analysis is actually used.

### Combinatorial Testing (A7)

{% cite kuhn2004fault %} — NIST study: 93%/98%/100% fault detection at 2/3/4-way interaction coverage; no failure required more than 6-way. The empirical foundation for pairwise testing adoption.

{% cite cohen1997aetg %} — AETG algorithm: pairwise test-suite size grows logarithmically vs. exponentially for exhaustive testing. The economic argument that makes combinatorial testing practical.

{% cite kuhn2013combinatorial %} — *Introduction to Combinatorial Testing* (Kuhn, Kacker & Lei, CRC Press). The primary reference textbook for the combinatorial testing lecture.

### Software Reliability (L8)

{% cite musa2004sre %} — Operational profiles and reliability growth models. The framework for measuring, predicting, and managing software reliability as a quantifiable engineering property.

{% cite avizienis2004basic %} — Dependability taxonomy: fault → error → failure chain; unified vocabulary for reliability and security. The standard reference for any precise reliability discussion.

{% cite knight1986experimental %} — Empirical proof that independently developed modules fail on the same inputs. The decisive evidence against naive N-version programming as a fault-tolerance strategy.

### Performance and Queuing Theory (L9, A9)

{% cite little1961proof %} — Little's Law L = λW. The single equation from which all queuing results follow; essential for any capacity-planning discussion.

{% cite dean2013tail %} — "The Tail at Scale": p99/p999 latency dominates user experience and how to tame it with hedged requests. Reframed how the industry thinks about latency SLOs.

{% cite gunther2007guerrilla %} — Universal Scalability Law extends Amdahl's Law with a coherence penalty. The practical formula for predicting scalability limits from measured throughput data.

### Security (L10)

{% cite saltzer1975protection %} — Eight protection principles (least privilege, fail-safe defaults, complete mediation, …). Fifty years old and still the design checklist every secure system starts from.

{% cite shostack2014threat %} — Definitive threat modelling reference: STRIDE, attack trees, the four-question framework. The book that operationalised threat modelling for software teams.

{% cite mcgraw2006software %} — "Build security in" via touchpoints: 50% of security issues require architectural fixes that testing cannot catch. The argument for shifting security left.

### Maintainability (A10)

{% cite lehman1980programs %} — Lehman's Laws: a system that is not actively adapted will degrade in quality over time. The theoretical basis for continuous refactoring investment.

{% cite parnas1994aging %} — "Software Aging": programs must be rejuvenated or retired; passive maintenance is not sufficient. Gives practitioners a vocabulary for the technical-debt conversation.

{% cite maccormack2006exploring %} — Mozilla and Linux DSM study: highly-tangled architectures accumulate 2–4× more defects in changed modules. Empirical link between coupling and defect density.

### Cost of Quality (A1)

{% cite tassey2002economic %} — NIST study: inadequate software testing costs the US economy $22–60 billion annually. The macroeconomic data behind the business case for quality investment.

{% cite knox1993modeling %} — The modern CoQ model: optimal quality spend shifts toward 100% conformance as processes improve. Challenges the traditional view that quality and cost necessarily trade off.

---

{% bibliography --cited %}
