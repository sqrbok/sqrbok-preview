---
title: Home
layout: home
nav_order: 1
---

# Software Quality and Reliability

Course handbook for two complementary programs:

- **SQR** — Software Quality & Reliability (Bachelor)
- **QAM** — Quality Assurance & Management (Master, extends SQR with advanced topics)

These notes cover the full arc from quality theory and measurement through verification techniques and non-functional attributes, grounded in industry practice and current research. The goal is to equip students with the concepts and tools to build quality into a software project from the start — not to inspect it in at the end.

The content reflects current industry practice: continuous integration, automated quality gates, site reliability engineering, and modern code review workflows sit alongside classical foundations. Material is regularly revised to incorporate findings from recent empirical software engineering research.

---

## Topic Map

Full scope of both courses across four areas. Topics in yellow are planned for future editions.

![Software Quality Topic Map](images/overview-asa.png){: loading="lazy"}

Quality assurance mapped onto a modern CI-based development process — where each technique fits from commit through release.

![Quality Assurance in Development Lifecycle](images/overview-sqr.png){: loading="lazy"}

---

## Areas Covered

### 1. [Defining Quality](/content/define/)
Quality views, quality models, and the metrics that make quality measurable and comparable across projects and teams.

### 2. [Organizing Quality](/content/organization/)
Cost of quality, project-level quality planning, process improvement, and the standards and industry practices that operationalise these — CMMI, ISO 9000, TMMi, DevOps, SRE, PSP/TSP.

### 3. [Verification and Validation](/content/verif/)
The full range of V&V methods and when to apply each:
- Black-box testing: domain and boundary analysis, equivalence partitioning, decision tables, classification trees, combinatorial, random and property-based, exploratory testing, operational profiles
- White-box testing: statement, branch, MC/DC, and data-flow coverage; mutation testing
- Inspection: code reviews, Fagan inspection, reading techniques
- Static analysis: model checking, symbolic execution, dataflow analysis, tooling

### 4. [Quality Attributes](/content/attributes/)
Maintainability, reliability, performance and queuing theory, security, and usability — each treated as an engineering property with models, metrics, and design implications.

### 5. [Selected Materials](/content/material/)
Curated textbooks and key research papers, one annotation per entry explaining the core contribution and why it earns a place in the list.

---

## Acknowledgements

The material and structure of these courses are inspired by the lectures of **Prof. Claire Le Goues** (Carnegie Mellon University) and **Prof. Eduardo Miranda** (Carnegie Mellon University). Their work laid the conceptual foundations on which these notes are built.
