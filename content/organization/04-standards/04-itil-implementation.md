---
title: ITIL Implementation
parent: Quality Standards
nav_order: 4
layout: default
---

# ITIL Implementation

**ITIL implementation** is the introduction of [ITIL](03-itil-framework.md) (IT Infrastructure Library, a public set of IT service management practices) processes into a working IT organization, during daily operations, usually a few processes at a time. There is no single accepted procedure: ITIL v3 says what each process should do but not how to introduce it {% cite cruzhinojosa2016itil %}.

## 1. Where organizations start

Most organizations begin with **incident management**, because restoring service is the most visible need {% cite cruzhinojosa2016itil %}. A systematic review of 39 studies on ITIL in small and medium enterprises (SMEs) found that research on SMEs is scarce, and that complexity, cost and a shortage of skilled staff are their main barriers. It also noted an advantage: SMEs tend to show lower resistance to change than large companies {% cite cruzhinojosa2016itil %}.

## 2. Example: a scaled-down ITIL at Nordex

Nordex, a German manufacturer of wind turbines, introduced ITIL for the IT that supports its products {% cite schmidtbauer2013itil %}. Full ITIL, with a separate person for every role, was too heavy for a medium-sized company, so it was scaled down:

- **Few processes:** request fulfilment, incident management and problem management, with access management folded into request fulfilment.
- **Combined roles:** one person holds several ITIL roles where the workload is small.
- **Three layers of description:** a high-level picture of the help desk's function, the detailed processes, then concrete instructions and checklists for each role.
- **Internal change management:** the company's own staff led the introduction rather than outside consultants, and all process diagrams used one standard format.

The authors' conclusion is that ITIL is a reference model to be interpreted and tailored, not a cookbook.

## 3. Example: a failed implementation analyzed

Ahmad and Shamsudin collected 17 critical success factors from the literature and grouped them into seven classes, including top-management support, change management and culture, communication, project management, and training {% cite ahmad2013itil %}. They then asked fifteen people involved in a failed ITIL project at a financial firm in the United Arab Emirates (five IT staff, five managers, five end users) to weigh the factors with the analytic hierarchy process (AHP), a method for ranking options by pairwise comparison. IT staff and managers ranked top-management support first; end users ranked communication first. The project had treated ITIL as an IT matter: end users had been left out of decisions, training was not mandatory, and the need for ITIL was never explained to them.

## When to use this — and when not

Start small and with the process whose failure hurts most, usually incidents; add problem and change management once incidents are recorded consistently, because problem management needs that record. Do not try to introduce all lifecycle processes at once in a small organization: the Nordex case shows that a deliberately reduced set can work.

## Limitations

- **Case studies, not comparisons.** Both examples describe one organization each; neither compares implementation strategies.
- **Success factors repeat across frameworks.** Management support, training and communication top the lists for ITIL, CMMI and Six Sigma alike, which says more about change in general than about ITIL (see [Change Management](../03-process/04-change-management.md)).

## How solid is this?

- **Where it comes from.** A review of ITIL in SMEs, the Nordex case study and the AHP study of a failed project {% cite cruzhinojosa2016itil schmidtbauer2013itil ahmad2013itil %}.
- **What we do not hold.** The Nordex paper's full text could not be machine-read, so its details come from the area's summary of it.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
