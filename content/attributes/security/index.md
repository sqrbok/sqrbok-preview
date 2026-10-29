---
title: Security
parent: Quality Attributes
has_children: true
nav_order: 5
layout: default
page_type: topic-hub
---

# Security

**Software security** is the absence of unauthorized access to, or handling of, system state: the system keeps its **confidentiality**, **integrity** and **availability** (the CIA triad) even when someone tries to break them {% cite avizienis2004basic %}. Security sits beside the other dependability attributes, reliability, safety and maintainability, and interacts with them: a security failure can cause a safety failure, and maintainable code can be patched quickly {% cite avizienis2004basic %}. Unlike a feature, it cannot be added at the end. A firewall or an encryption library does not make insecure code secure, because security is a property of the whole system {% cite mcgraw2006software %}.

There are two complementary strategies, and every page below serves one or both. **Detecting** means finding vulnerabilities that already exist in code or a running system: analyse code, attack it, review changes. **Preventing** means stopping vulnerabilities from arising: model threats, follow design principles, write requirements, and build security into the delivery pipeline. McGraw estimates that about half of security problems are coding bugs and half are design flaws, which is why neither strategy is enough alone: tools that read code find bugs, and only design analysis finds flaws {% cite mcgraw2006software %}.

Read the pages in the order of the table, from vulnerabilities to case studies.

| Page | Strategy | What it answers | Main limitation |
|---|---|---|---|
| [Vulnerabilities, Bugs and Flaws](vulnerabilities.md) | both | What makes a defect a vulnerability? | Classifying needs judgement |
| [Static Security Analysis](static-analysis.md) | detect | What can a tool see in the source? | False positives; no design flaws |
| [Dynamic Security Testing](dynamic-testing.md) | detect | What does the running system do under attack? | Only reached paths are tested |
| [Penetration Testing and Security Code Review](pentest-review.md) | detect | What do skilled humans find? | Costly; depends on the people |
| [Threat Modeling](threat-modeling.md) | prevent | What can go wrong in this design? | Most threats are still missed |
| [OWASP Top 10](owasp-top10.md) | both | Which risk categories matter most? | Ranking is partly survey-based |
| [CVSS Scoring](risk-rating.md) | both | How severe is a finding? | Severity, not risk |
| [From Severity to Fix Order](cvss-context.md) | both | Which findings are fixed first? | Coarse adjustments |
| [Secure Design Principles](secure-design.md) | prevent | Which design rules stop whole classes of attack? | Principles need interpretation |
| [Security Requirements](security-requirements.md) | prevent | How do we state and check what "secure" means? | Elicitation is still manual |
| [DevSecOps and Supply Chain Security](devsecops.md) | both | Where does each activity run in delivery? | Scan time against deploy speed |
| [Security Breach Case Studies](case-studies.md) | both | What did real breaches teach? | Hindsight makes causes look obvious |
| [Worked Case: Threat Model and SAST Triage for a Web Shop](worked-case.md) | both | What blocks this release? | One small app |

The dependability chain of faults, errors and failures that this section builds on is introduced under [Reliability](../reliability/index.md). Fuzzing has its own page, [Fuzz Testing](../../verif/random/fuzzing.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
