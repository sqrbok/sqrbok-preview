---
title: Home
layout: home
nav_order: 1
page_type: home
---

<div align="center">
  <img src="/sqrbok-preview/images/logo/sqr_logo.png" alt="SQRBOK Logo" style="width:180px; margin-bottom: 1em;" />
</div>

# Software Quality and Reliability

Course handbook for two complementary programs:

- **SQR** — Software Quality & Reliability (Bachelor)
- **QAM** — Quality Assurance & Management (Master, extends SQR with advanced topics)

These notes cover the full arc from quality theory and measurement through verification techniques and non-functional attributes, grounded in industry practice and current research. The goal is to equip students with the concepts and tools to build quality into a software project from the start — not to inspect it in at the end.

The content reflects current industry practice: continuous integration, automated quality gates, site reliability engineering, and modern code review workflows sit alongside classical foundations. Material is regularly revised to incorporate findings from recent empirical software engineering research.

---

## Overview

**Course topic map** covers the full scope of both courses across four areas: defining quality, organizing quality assurance, verification methods, and quality attributes. Topics marked in yellow are planned for future editions.

![Software Quality Topic Map](images/overview-asa.png)

**Quality assurance in the development lifecycle** maps course topics onto a modern CI-based development process. The diagram shows where each technique fits: static analysis and unit tests run automatically on every commit; code reviews and integration tests gate the build pipeline; and non-functional attributes — maintainability, reliability, performance, security, usability — are evaluated before release. Students are expected to apply these practices within a CI workflow, treating quality gates as first-class engineering artifacts rather than end-of-cycle activities.

![Quality Assurance in Development Lifecycle](images/overview-sqr.png)

---

## Areas Covered

### 1. [Defining Quality](/sqrbok-preview/content/define/)
Quality views, quality models, and the metrics that make quality measurable and comparable across projects and teams.

### 2. [Organizing Quality](/sqrbok-preview/content/organization/)
Cost of quality, project-level quality planning, process improvement, and the standards and industry practices that operationalise these — CMMI, ISO 9000, TMMi, DevOps, SRE, PSP/TSP.

### 3. [Verification and Validation](/sqrbok-preview/content/verif/)
The full range of V&V methods and when to apply each:
- Black-box testing: domain and boundary analysis, equivalence partitioning, decision tables, classification trees, combinatorial, random and property-based, exploratory testing, operational profiles
- White-box testing: statement, branch, MC/DC, and data-flow coverage; mutation testing
- Inspection: code reviews, Fagan inspection, reading techniques
- Static analysis: model checking, symbolic execution, dataflow analysis, tooling

### 4. [Quality Attributes](/sqrbok-preview/content/attributes/)
Maintainability, reliability, performance and queuing theory, security, and usability — each treated as an engineering property with models, metrics, and design implications.

### 5. [Selected Materials](/sqrbok-preview/content/material/)
A study note and revision questions for every SQR and QAM lecture, and an annotated reading list.

---

## Start here

- **Revising after a lecture or before an exam** → [Selected Materials](/sqrbok-preview/content/material/): the study note follows the lecture and links to the pages that hold the detail.
- **Looking up a method** → open its area above, or use the search box.
- **Wondering whether to believe a page** → its **How solid is this?** section, at the bottom of reviewed pages.

---

## How this handbook is built

Each area is re-grounded one topic at a time. A topic gets a literature search, a summary of every paper it keeps, and a synthesis of what the papers agree and disagree on. The review so far covers **21 topics, 326 papers and 393 bibliography entries**. What survives becomes a short page: one method per page, under 800 words, with a worked software example and at least one limitation. Sources are cited where the claim is made, and the evidence behind a page is summarised in **How solid is this?**

---

## Contributing

Corrections and additions are welcome, from students and practitioners alike.

- **Fix a content issue** (a typo, a wrong fact, a citation that does not check out) → [guidelines](https://github.com/sqrbok/sqrbok.github.io/blob/main/CONTRIBUTING.md)
- **Add a method page** → [method template](https://github.com/sqrbok/sqrbok.github.io/blob/main/templates/method-template.md)
- **Write an analysis** → [analysis template](https://github.com/sqrbok/sqrbok.github.io/blob/main/templates/analysis-template.md)

Every page is held to [the page standard](https://github.com/sqrbok/sqrbok.github.io/blob/main/templates/page-standard.md).

---

## Acknowledgements

The material and structure of these courses are inspired by the lectures of **Prof. Claire Le Goues** (Carnegie Mellon University) and **Prof. Eduardo Miranda** (Carnegie Mellon University). Their work laid the conceptual foundations on which these notes are built.

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
