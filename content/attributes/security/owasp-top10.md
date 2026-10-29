---
title: OWASP Top 10
parent: Security
grand_parent: Quality Attributes
nav_order: 6
layout: default
---

# OWASP Top 10

The **OWASP Top 10** is a ranked list of the ten most serious categories of web application security risk, published by the Open Worldwide Application Security Project and revised every few years from testing data and a practitioner survey {% cite owasp2025top10 %}.

## 1. The 2025 list

Each category groups many CWE weaknesses under one name, and each category's page on the OWASP site lists the CWEs mapped to it {% cite owasp2025top10 %}. A scanner's CWE number therefore gives the category by lookup, not by guess (section 2 shows it for the shop). *Server-side request forgery* (SSRF) tricks a server into sending requests on the attacker's behalf, typically to internal systems the attacker cannot reach directly. Its 2025 move into Broken Access Control reflects that the root problem is a request the server should have refused. The 2025 edition, with the 2021 positions for comparison {% cite owasp2025top10 %} {% cite owasp2021top10 %}:

| 2025 | 2025 category | 2021 position and name |
|---|---|---|
| A01 | Broken Access Control | A01 Broken Access Control, plus A10 Server-Side Request Forgery |
| A02 | Security Misconfiguration | A05 Security Misconfiguration |
| A03 | Software Supply Chain Failures | widens A06 Vulnerable and Outdated Components |
| A04 | Cryptographic Failures | A02 Cryptographic Failures |
| A05 | Injection | A03 Injection |
| A06 | Insecure Design | A04 Insecure Design |
| A07 | Authentication Failures | A07 Identification and Authentication Failures |
| A08 | Software or Data Integrity Failures | A08 Software and Data Integrity Failures |
| A09 | Security Logging and Alerting Failures | A09 Security Logging and Monitoring Failures |
| A10 | Mishandling of Exceptional Conditions | new |


## 2. Example: classifying the shop's findings

The four Bandit findings on the shop module in [Static Security Analysis](static-analysis.md), looked up by CWE in OWASP's mapping lists {% cite owasp2021top10 %} {% cite owasp2025top10 %}:

| Finding | CWE | 2021 | 2025 |
|---|---|---|---|
| String-built SQL query | CWE-89 | A03 Injection | A05 Injection |
| `eval` of a discount rule | CWE-95 (Bandit reports CWE-78) | A03 Injection | A05 Injection |
| MD5 for passwords | CWE-327 | A02 Cryptographic Failures | A04 Cryptographic Failures |
| Hardcoded password (the shop's secret key) | CWE-259 | A07 Identification and Authentication Failures | A07 Authentication Failures |

For `eval`, Bandit reports CWE-78 (OS command injection) because the evaluated code can reach the operating system as the application's user; **CWE-95** (eval injection) is the closer fit. Both are mapped to Injection, so the category does not change. The four findings fall into three categories: input interpreted as code, a fast unsalted hash that lets a stolen table be cracked, and a key that anyone reading the source holds. *Hypothetically*, if the shop also depended on a library with a known vulnerability, such as Log4j before its fix, that would be a **Software Supply Chain Failure** (A03 in 2025).

**Where the inputs come from on a real system.** The CWE of each finding is in the scanner's report: Bandit's JSON output (`bandit -r app -f json`) carries an `issue_cwe` id for every result, and dependency scanners and NVD entries list CWEs for published CVEs. Look the id up in OWASP's mapping list for the edition you report against; a finding without a CWE, such as a missing authorization check found in review, is classified by hand.

The mapping tells the team which checklist items and tests to add: an injection test for every input that reaches a query, a review rule against fast hashes, and a dependency scan in the build.

## When to use it — and when not

Use the Top 10 to decide what to test and review in a web application, as a shared vocabulary in reports, and as a minimum checklist for a team new to security. Do not use it as a complete test plan or as a compliance target: OWASP calls it an awareness document, and a system can avoid all ten categories and still be insecure through its business logic.

## Limitation

The ranking combines measured incidence with survey votes for risks that testing data cannot yet show well, so the order of neighbouring categories is not precise. Numbers also shift between editions, which makes "A03" ambiguous unless the year is given.

## How solid is this?

- **Where it comes from.** The categories and their 2021 mapping are from OWASP's own publications {% cite owasp2025top10 %} {% cite owasp2021top10 %}.
- **What is contested.** Which categories should be merged or split changes with every edition; the 2025 merge of SSRF into access control is one example.
- **What we do not hold.** The contributed data set behind the ranking.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
