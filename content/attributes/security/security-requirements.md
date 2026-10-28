---
title: Security Requirements
parent: Security
grand_parent: Quality Attributes
nav_order: 10
layout: default
---

# Security Requirements

A **security requirement** states what the system must do or prevent to keep its assets confidential, intact and available, precisely enough to be tested; **security requirements engineering** is the process of eliciting, prioritising and checking such requirements early, before design {% cite mead2005square %}.

## 1. Why they are hard

Functional requirements describe what users want; security requirements describe what attackers must *not* achieve, and customers rarely state them. Left unstated, they become vague goals such as "the system shall be secure", which no one can test, and security is handled late at higher cost {% cite mead2005square %}. McGraw's **abuse cases** address the first problem: next to each use case, write how an attacker would misuse it, then derive the requirement that blocks the misuse {% cite mcgraw2006software %}. SQUARE calls the same artefact a *misuse case*; the two names mean the same thing.

## 2. SQUARE: nine steps

Mead et al.'s SQUARE (Security Quality Requirements Engineering) method structures the work in nine steps, carried out by a requirements engineer with stakeholders {% cite mead2005square %}:

1. Agree on definitions of terms such as "threat" and "asset".
2. Identify security goals, from business drivers and the assets to protect.
3. Develop artefacts: architecture diagrams, use and misuse cases, attack trees.
4. Assess risks.
5. Select an elicitation technique.
6. Elicit the security requirements.
7. Categorise them, for example by level and by the goal they serve.
8. Prioritise them.
9. Inspect them for ambiguity and gaps.

In SQUARE's case studies, the Accelerated Requirements Method worked well for elicitation and the Analytic Hierarchy Process for prioritisation, while joint application design and issue-based argument tools did not produce usable security requirements {% cite mead2005square %}.

## 3. Example: from abuse case to test

For the shop's login, the steps produce, in short:

- **Asset and goal (step 2):** customer accounts; only the account owner can log in.
- **Abuse case (step 3):** an attacker types a crafted user name to log in as someone else.
- **Requirement (step 6):** *"The login shall authenticate a user only when the submitted password matches the stored hash for exactly the submitted user name; user input shall never be interpreted as query syntax."*
- **Category and priority (steps 7–8):** authentication, highest priority, because it protects every other asset.

The requirement is testable, and the white-box exploit test on [Dynamic Security Testing](dynamic-testing.md) checks it on every build.

## 4. Requirements as code

Sadovykh and Ivanov take this one step further for compliance-driven settings {% cite sadovykh2024devsecops %}. **ARQAN** uses semantic text search to map a vague requirement from a standard such as IEC 62443 to concrete recommendations in the Security Technical Implementation Guides (STIGs). **RQCODE** turns each recommendation into a Java class with a `check()` method that tests the setting and an `enforce()` method that fixes it, run from the delivery pipeline. In an industrial evaluation, ARQAN found matching STIG recommendations for 85% of 40 security requirements, and 62% of those recommendations had a ready test and fix in RQCODE; the 172 RQCODE classes covered 66.15% of the 260 Windows 10 STIG recommendations {% cite sadovykh2024devsecops %}.

## When to use it — and when not

Use a structured method such as SQUARE when security is part of the contract or a regulation, or when the system holds valuable data; use abuse cases in every backlog refinement for features that handle input, money or personal data. A full nine-step SQUARE pass is heavy for a small internal tool; there, abuse cases and the [OWASP Top 10](owasp-top10.md) as a checklist give most of the value.

## Limitation

Elicitation still depends on people imagining attacks, so a requirement set is only as complete as the threat analysis behind it. Requirements-as-code tools cover configuration settings well, but not application logic such as the login rule above.

## How solid is this?

- **Where it comes from.** SQUARE and its case-study findings are from the SEI technical report {% cite mead2005square %}; abuse cases from McGraw {% cite mcgraw2006software %}; ARQAN and RQCODE from Sadovykh et al. {% cite sadovykh2024devsecops %}.
- **What is contested.** The SQUARE technique comparisons come from a small number of case studies, not controlled experiments.
- **What we do not hold.** Evidence that projects using SQUARE ship fewer vulnerabilities than projects that do not.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
