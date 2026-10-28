---
title: ITIL Framework
parent: Quality Standards
nav_order: 3
layout: default
---

# ITIL Framework

The **IT Infrastructure Library (ITIL)** is a collection of best practices for **IT service management (ITSM)**, managing IT as a provider of services to the business, and is counted as the de facto ITSM standard worldwide {% cite iden2013itil %}. Where CMMI looks at how software is developed, ITIL looks at how it is run and supported once it is in use.

## 1. From version 1 to the service lifecycle

ITIL version 1 was written in the 1980s by a British public body, the Central Computer and Telecommunications Agency, from practices observed in industry. Version 2 (2000–2002) made it popular worldwide. Version 3 (2007, revised in 2011) organized it around a **service lifecycle** in five volumes {% cite iden2013itil itil2007v3 %}:

| Phase | Question it answers | Examples of processes |
|---|---|---|
| Service Strategy | which services create value, for whom? | service portfolio, financial management |
| Service Design | how will a new or changed service meet its targets? | service level, capacity and availability management |
| Service Transition | how do we bring it into operation safely? | change management, release and deployment, testing |
| Service Operation | how do we deliver it day to day? | incident, problem and request management |
| Continual Service Improvement | how do we get better? | measuring services against targets |

## 2. Incident, problem and change

Three processes do most of the quality work, and students often mix them up:

- **Incident management** restores normal service as quickly as possible after a disruption. It does not need to know the cause.
- **Problem management** finds the root cause of one or more incidents and removes it, or documents a *known error* with a workaround.
- **Change management** controls every change to the live infrastructure, assessing its risk and approving it, so that fixes do not cause new outages.

**Example.** An online shop's checkout fails three evenings in a row. Each evening the on-call engineer restarts the payment service: three *incidents*, each closed within minutes. The repetition opens a *problem*; the analysis finds a connection pool that runs out under evening load, and a workaround (restart at 18:00) is recorded as a known error. The permanent fix, a larger pool and a timeout, goes through *change management*, which schedules it for a quiet hour and prepares a rollback. (A constructed example.)

Incident management is ITIL's single-loop learning and problem management its double loop (see [Learning & Improvement Models](../03-process/02-learning-improvement-models.md)).

## 3. Why organizations adopt it

A systematic review of 37 ITSM studies found three main motives (more efficient operations, lower IT costs and better customer service) and similar success factors to other improvement programs: senior management involvement, training, communication and a process-oriented culture {% cite iden2013itil %}. Reported benefits include better service quality, higher customer satisfaction and standardized processes. Certification is possible for the organization against ISO/IEC 20000, and for individuals through ITIL's own qualification scheme.

## When to use it — and when not

Use ITIL when an IT function supports many users and services and needs predictable handling of incidents, changes and service levels. For a small product team that runs its own service, the full framework is heavy; teams usually start with incident management and add others as needed (see [ITIL Implementation](04-itil-implementation.md)).

## Limitations

- **It says what, not how:** ITIL v3 describes what each process should do but not how to introduce it {% cite cruzhinojosa2016itil %}.
- **Change approval can slow delivery** if every change waits for a board; DevOps and SRE practices instead automate low-risk changes (see [Industry Practices](../05-practice/index.md)).
- **Benefits are mostly self-reported;** the review notes that many studies use no explicit theory {% cite iden2013itil %}.

## How solid is this?

- **Where it comes from.** Iden and Eikebrokk's systematic review of ITSM research and the ITIL v3 publications {% cite iden2013itil itil2007v3 %}.
- **What we do not hold.** ITIL 4 (2019) and the ITIL books themselves; the description above is of version 3 as presented in the review. ITIL 4 replaced the five-volume lifecycle with a service value system, and the processes described here reappear in it as practices.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
