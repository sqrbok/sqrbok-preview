---
title: Changes since S26
layout: default
nav_exclude: true
permalink: /changelog/
---

# Changes since S26

This page records what changed between the **S26 edition** (Spring 2026, tag `s26`, archived at
[sqrbok.github.io/sqrbok-s26](https://sqrbok.github.io/sqrbok-s26/)) and the **S27 revision**
(tag `s27-rc`). It is the entry point for tracing a change and for continuing the open work.

The full trail of each area's rewrite (round-by-round student reviews, every number checked, every
claim removed with its reason) is in the area's `status.md` log in the companion repository
`sqr_lit_review` (`review/<domain>/<topic>/status.md`).

## In numbers

| | S26 | S27 |
|---|---:|---:|
| Content pages | 179 | 290 |
| Defining Quality | 12 | 26 |
| Organizing Quality | 40 | 54 |
| Verification and Validation | 62 | 93 |
| Quality Attributes | 22 | 50 |
| Selected Materials (study notes, questions, tool cards) | 43 | 66 |
| Commits between the two | | 53 |

Every S27 page passes the page standard (`scripts/check_pages.py`), and every citation resolves
(`scripts/check_citations.py`).

## Framework

- **Page standard** (`templates/page-standard.md`), adapted from MSDBOK. It sets:
  - **Budgets per page type:** hub, topic hub, method (400–800 words) and deep dive (up to 1,200).
  - **Required elements:** a definition in the first 40 words, an example, a limitation, a paragraph on where the inputs come from, and a "How solid is this?" section.
  - **Two new page types:**
    - *worked case* (one per area, 1,200–2,000 words): a real input carried through to a decision;
    - *study notes* / *question set* for lecture materials.
  - **No course logistics on handbook pages.** Practitioner content (tool commands, CI configuration, templates) is allowed.
  - **Sources and figures:** sources must come from the area's literature review, or be labelled practitioner material or a constructed example; diagrams are drawn in draw.io, and charts are Vega-Lite built only from source data.
- **Checks:** `scripts/check_pages.py` (budgets, style caps) and `scripts/check_citations.py`.
- **Site-wide fixes:**
  - Single `$` is no longer a math delimiter; it had turned dollar amounts into math. Inline math is written `$$…$$`.
  - All charts use colour-blind-safe palettes, with shapes or dashes as a second cue.
  - Every page title is unique, and sibling navigation orders are fixed.
- **Acknowledgments:** the home page and each area hub now name where the lectures took early inspiration: the *Analysis of Software Artifacts* course (Miranda and Le Goues, CMU, with Aldrich and Bocchino for static analysis), and Far's software reliability engineering lectures (Calgary) for reliability and operational profile. The pages themselves are original, and industry practice has no external inspiration.

## What changed, by area

Every area was rewritten from its literature review. Each number was checked against the source
paper, and claims that could not be traced were removed. Every area then passed student-review
rounds; most areas also had a Gemini cross-check.

- **Defining Quality.**
  - Rewritten: quality models (ISO/IEC 25010:2023, McCall, Kano, quality in use), requirements, and measurement (scales, statistics, GQM, maintainability index, OO metrics, evidence).
  - New pages: cost of late fixes (Boehm, NASA, with the counter-evidence), cost of poor software quality (CISQ 2022), defect-density benchmarks (Shah 2012), and the project quality model.
  - Kano now uses the MSDBOK figure, satisfaction coefficients and the tie-break rule.
- **Organizing Quality.**
  - Rewritten: cost of quality, quality planning, process improvement, standards and industry practice.
  - New pages: defect prevention, theory of constraints, Six Sigma, classification schemes, Facebook continuous deployment, chaos engineering, continuous integration, kinds of technical debt, Wagner's model, and verification methods per quality characteristic.
  - Worked cases: a complete quality plan, a cost-of-quality log, an improvement proposal, and release & incident.
- **Verification and Validation.**
  - The overview is rebuilt, with new pages on test doubles, the test pyramid and TDD.
  - Coverage, input domain, random testing, combinatorial testing, exploratory testing, operational profile, inspection and static analysis are each rewritten.
  - New pages include:
    - interaction faults;
    - basis paths on binary search and test-suite maintenance;
    - P- and E-measures;
    - the UAV operational-profile case and the machine-vision classification tree;
    - Kripke structures and LTL, and Hoare logic;
    - dynamic analysis, CEGAR, warning triage, capture-recapture practice and modern code review.
  - Worked cases in coverage, domain, random, operational profile, inspection and static analysis. Their code was run as printed.
- **Quality Attributes.**
  - Reliability, performance, queuing, security and maintainability are rewritten and split into method pages.
  - New pages include reliability block diagrams, growth models, the failure-intensity objective, load testing, scalability, queuing networks, the OWASP Top 10 (2025), CVSS in context, threat modeling, DSM operations and propagation cost.
  - Worked cases from measured runs, one per area:
    - performance: a load test;
    - queuing: service demand and capacity;
    - reliability: a growth model fitted to the NTDS data;
    - security: a threat model and Bandit triage;
    - maintainability: refactoring the Click repository.
- **Selected Materials.**
  - New **Practice Toolkit**: 19 tool cards (Poetry and uv, GitHub Actions, Podman and Docker, pytest, Coverage.py, Hypothesis, mutmut, flake8 and Ruff, Radon, mypy, Bandit and pip-audit, Trivy, Atheris, CrossHair, Pynguin, Locust, Prometheus, PICT, ACTS), each run for the card with its version stated; new page type `tool-card` (300–600 words).
  - Study notes and revision questions are rewritten for L01–L10 and A01–A10, and added for L00.
  - Each study note follows its lecture and links to the handbook.
  - The reading list is updated.

**Pages removed or merged** (old URLs redirect where noted in the logs):
- `attributes/maintainability/dsm-modularity`
- `attributes/security/testing-techniques`
- `define/foundations/models`
- `verif/overview/inspection`
- `verif/overview/analysis/analysis_definitions`
- `verif/overview/analysis/checking_example`
- `verif/overview/testing/testing_target`

## Conventions introduced

These are judgements made by this handbook and labelled as such on the pages. Change them in one
place if the course decides otherwise:
- **Cost of quality:**
  - reviews and inspections are booked as prevention;
  - a failure cost is external when users met the failure.
- **Severity:** severity 1 follows Microsoft's definition; severities 2 and 3 are a course scale.
- **Criticality:** the harm levels map to Miranda's classes A–C.
- **Performance:** a result within 10% of a threshold counts as a fail (the margin rule).
- **Security:**
  - in-house findings are scored with CVSS Scope unchanged (S:U) by default;
  - spoofing is recorded on the element whose identity is faked.
- **Inspection:** 50 lines of code count as one "page".
- **Coverage floors** are practitioner rules of thumb: round down to the next multiple of 5, and 80% of changed lines.

## Continue later

Open work, with where its details live:

1. **Lecture decks.**
   - The handbook corrects about 100 figures and claims that the decks still show.
   - Restored topics also need to go back into the decks: reliability block diagrams, Kripke structures and LTL, Hoare logic, the technical-debt taxonomy, and the lecture hooks.
   - The details are in `sqr_lit_review/review/restore-list-2026-10.md` and the "Deck errors" sections of the area status logs.
2. **A real-student pilot** of the study notes, the questions and the worked cases. So far every review has been done by AI reviewers.
3. **"Apply it" revision questions** tied to the worked cases. About 85 of roughly 430 questions ask for the student's own example.
4. **A Toyota unintended-acceleration case study** (Koopman). The legacy slides only link to it.
5. **Sources not held:**
   - Ammann & Offutt, 2nd edition (only the 1st is held);
   - the ECSS and SAP quality-model texts;
   - Weyuker & Jeng and Chen & Yu in full text (cited via Ntafos).

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
