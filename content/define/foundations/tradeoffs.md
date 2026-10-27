---
parent: Foundations
nav_order: 4
title: Quality Trade-offs
layout: default
---

# Quality Trade-offs

A **quality trade-off** is a relationship in which improving one quality characteristic makes another worse, so that no design can maximise both. Perry classified each pair of McCall's factors as **direct** (both improve together), **inverse** (a trade-off) or **neutral** (independent) {% cite perry_effective_1988 %}.

## 1. Efficiency pays for almost everything

The clearest pattern in McCall's factors concerns efficiency. Naik and Tripathy, summarising McCall, state that efforts to improve integrity, usability, maintainability, testability, flexibility, portability, reusability or interoperability will reduce efficiency {% cite naik2008software %}. Each of those adds code that does not compute the answer: access checks, help text, abstraction layers, logging hooks, adapters. Other pairs run the other way; making a system more correct also makes it more reliable.

Perry's matrix gives one sign per pair. For seven of McCall's factors it reads as follows (+ direct, − inverse, blank neutral):

| | Corr. | Rel. | Eff. | Integ. | Usab. | Maint. |
|---|---|---|---|---|---|---|
| **Reliability** | + | | | | | |
| **Efficiency** | | | | | | |
| **Integrity** | | | − | | | |
| **Usability** | + | + | − | + | | |
| **Maintainability** | + | + | − | | + | |
| **Testability** | + | + | − | | + | + |

_Part of Perry's relationship matrix, as reproduced by Zhu. The matrix is triangular: each pair appears once._ {% cite perry_effective_1988 zhu_software_2005 %}

One cell surprises today's readers: usability and integrity are marked direct. Integrity in McCall's sense is controlled access, and Perry judged a clear, well-structured user interface compatible with controlling who may do what. With integrity read as security in the modern sense, many practitioners would mark the pair inverse, since every extra login step costs usability; the sign is a judgement, and this one is debated.

The next table explains the mechanism behind some of these pairs and two from outside the excerpt: portability and efficiency, and reusability and integrity.

| Pair | Relationship | Mechanism |
|---|---|---|
| Integrity and efficiency | inverse | every access check and audit record costs time and storage |
| Usability and efficiency | inverse | help, validation and undo add code and processing |
| Testability and efficiency | inverse | instrumentation and test hooks run in production builds too |
| Portability and efficiency | inverse | an abstraction layer hides the fast, platform-specific path |
| Reusability and integrity | inverse | a component written for any caller checks less about who calls it |
| Correctness and reliability | direct | fewer faults mean fewer failures in use |
| Maintainability and testability | direct | modular, simple code is easier to change and to test |

For example, a bank adds an audit record to every balance query to meet an integrity requirement. Each query now writes one extra row. If a query took 8 ms and the write takes 2 ms, latency rises by 25%, and a 10 ms response-time requirement that used to hold is now just met. The trade-off is not an argument against auditing; it tells the team to decide the two requirements together, perhaps by writing audit records asynchronously, which recovers some of the latency budget at the risk of losing records in a crash.

## 2. Relationships are not symmetric

Gillies, revisiting such matrices, argued that the relationships need not be commutative: improving A can help B while improving B does nothing for A {% cite gillies_software_2011 %}. For example, cleaner, more modular code (maintainability) makes faults easier to find and fix, so it helps reliability; raising reliability by adding a redundant server does nothing for maintainability. A matrix with one sign per pair hides that direction.

## When to use it — and when not

- **Use** a trade-off matrix while writing [quality requirements](requirements.md), to spot two requirements that will compete for the same resource and to decide their priority before design starts.
- **Do not** read a sign as a law: whether security costs performance depends on the design, and many trade-offs can be engineered away at some cost.

## Limitations

Perry's signs are expert judgement for typical 1980s systems, not measurements. A modern compiler can remove a layer of abstraction; hardware encryption makes integrity almost free in time. The useful lesson is the habit of asking "what does this cost elsewhere?", not the particular signs.

## How solid is this?

- **Where it comes from.** Perry's relationship types and matrix, reproduced by Zhu {% cite perry_effective_1988 zhu_software_2005 %}; the efficiency pattern as stated by Naik and Tripathy {% cite naik2008software %}.
- **What is contested.** Individual cells differ between reproductions of the matrix, and Gillies' matrix uses a different list of criteria, so the two cannot be compared cell by cell.
- **What we do not hold.** Perry's book itself; the audit-log figures are a constructed example.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
