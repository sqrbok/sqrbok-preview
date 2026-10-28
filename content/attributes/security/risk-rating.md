---
title: CVSS Scoring
parent: Security
grand_parent: Quality Attributes
nav_order: 7
layout: default
math: katex
page_type: deep-dive   # the CVSS 3.1 weights and equations must be on the page so every worked score can be reproduced by hand
---

# CVSS Scoring

The **Common Vulnerability Scoring System (CVSS)** gives one specific vulnerability a severity score from 0 to 10, computed from a published vector of its characteristics, so that findings can be compared by severity {% cite first_cvss31 %}.

## 1. Three metric groups

CVSS has three metric groups {% cite mell2007cvss %} {% cite first_cvss31 %}: **base** (the vulnerability itself), **temporal** (exploit code, fix, report confidence) and **environmental** (one organisation's system; see [From Severity to Fix Order](cvss-context.md)). A score comes with its **vector** of chosen values, so anyone can recompute it. The base severity bands are None (0.0), Low (0.1–3.9), Medium (4.0–6.9), High (7.0–8.9) and Critical (9.0–10.0).

## 2. Base score: weights and equations

To score, pick for each metric the value whose description fits the case; the weight (in brackets) is what the equations use. Paraphrased from the 3.1 specification {% cite first_cvss31 %}:

| Metric | Choose this value when… |
|---|---|
| Attack Vector (AV) | **N** (network) [0.85]: exploitable remotely, across routers · **A** (adjacent network) [0.62]: only from the same local network or radio link · **L** (local) [0.55]: needs local access (a shell, a console, or a user opening a file) · **P** (physical) [0.20]: the attacker must physically touch the device |
| Attack Complexity (AC) | **L** [0.77]: no special conditions; it works every time · **H** [0.44]: success depends on conditions the attacker does not control, such as winning a race |
| Privileges Required (PR) | **N** [0.85]: no account · **L** [0.62, or 0.68 with S:C]: an ordinary user account · **H** [0.27, or 0.50 with S:C]: administrative control |
| User Interaction (UI) | **N** [0.85]: no one else has to act · **R** [0.62]: a victim must act, e.g. click a link |
| Scope (S) | **U**: the impacted component is the vulnerable one · **C**: a different impacted component, under another security authority (see the procedure below) |
| C, I, A (rated on the impacted component) | **H** [0.56]: total loss, or a partial one with serious consequence (read all data; change any data; deny service fully) · **L** [0.22]: some loss the attacker cannot fully direct (some data read or altered, degraded service) · **N** [0]: no loss |

The **vulnerable component** has the vulnerability; the **impacted component** suffers the impact {% cite first_cvss31 %}. **Scope** (S) is changed (S:C) when the two differ and are managed by different security authorities, as when code escapes a sandbox into the operating system; otherwise S:U. CVSS 4.0 replaced scope with separate impacts on the vulnerable and subsequent systems {% cite first_cvss40 %}. This course uses a procedure:

- **(a) A published CVE:** copy the vector published by NVD (the US National Vulnerability Database), scope included; do not derive it.
- **(b) Your own finding:** score S:U unless the impact lands in a component under a different security authority. The application's own database, host and cloud account, run by the same team, are the same authority even on a separate server. Code running with the application process's own rights also stays S:U when it calls the operating system; as NVD scores Spring4Shell (CVE-2022-22965) and the Struts flaw behind Equifax (CVE-2017-5638), both S:U, 9.8 {% cite nist_cve_2022_22965 %} {% cite nist_cve_2017_5638 %}. Yet NVD scores Log4Shell (Example 2) S:C, although it too runs code in the host application; such inconsistency is why CVSS 4.0 replaced scope. A clear S:C case is cross-site scripting: in FIRST's example CVE-2017-5942, the vulnerable WordPress server's script runs in the victim's browser, a different authority: `AV:N/AC:L/PR:N/UI:R/S:C/C:L/I:L/A:N`, 6.1 {% cite first_cvss31_examples %}. A vulnerable dependency found by `pip-audit` is a published CVE: use (a).

The equations:

$$
\text{ISS} = 1 - (1-C)(1-I)(1-A) \qquad \text{Exploitability} = 8.22 \times AV \times AC \times PR \times UI
$$

$$
\text{Impact} = \begin{cases} 6.42 \times \text{ISS} & \text{scope unchanged} \\ 7.52\,(\text{ISS} - 0.029) - 3.25\,(\text{ISS} - 0.02)^{15} & \text{scope changed} \end{cases}
$$

If Impact ≤ 0, the base score is 0. Otherwise the base score is roundup(min(Impact + Exploitability, 10)) with scope unchanged, and roundup(min(1.08 × (Impact + Exploitability), 10)) with scope changed. **Roundup** rounds any remainder up to one decimal: 9.01 becomes 9.1; 9.0 stays 9.0.

## 3. Three worked scores

**Example 1: the shop's SQL injection.** Network-reachable (AV:N), no special conditions (AC:L), no account (PR:N), no victim action (UI:N). Its own database is under the shop's authority: S:U by procedure (b). Any user name works, exposing all accounts: the attacker can read every user's data (C:H) and act as them (I:H). Availability rates what the attacker can do *on purpose* to the service: a malformed query fails only their own request, and sqlite3's `execute` runs one statement per call, so no table can be dropped: A:N. Vector `AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`:

$$
\text{ISS} = 1 - (1 - 0.56)(1 - 0.56)(1 - 0) = 0.8064, \quad \text{Impact} = 6.42 \times 0.8064 = 5.177
$$

$$
\text{Exploitability} = 8.22 \times 0.85 \times 0.77 \times 0.85 \times 0.85 = 3.887, \quad \text{Base} = \operatorname{roundup}(9.064) = 9.1
$$

**Critical.** If the injection could also delete tables, A:H would give 9.8.

**Example 2: Log4Shell, a published CVE.** NVD publishes `AV:N/AC:L/PR:N/UI:N/S:C/C:H/I:H/A:H` {% cite nist_cve_2021_44228 %}; procedure (a) copies it, S:C included, because NVD published it so. The S:C equations give ISS = 0.9148, Impact = 6.048, Exploitability = 3.887, Base = roundup(min(1.08 × 9.935, 10)) = **10.0**.

**Example 3: the shop's `eval`, procedure (b).** A discount code typed at checkout by any logged-in customer (PR:L) reaches `eval`, which runs it inside the shop process with the shop's rights (S:U) and can read, change and stop anything (C:H/I:H/A:H): `AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H`, Impact = 5.873, Exploitability = 8.22 × 0.85 × 0.77 × 0.62 × 0.85 = 2.835, roundup(8.708) = **8.8**.

## When to use it — and when not

Use the base score to state severity in a form others can check. Do not order patches across systems by base score alone: the fix order follows risk, which [From Severity to Fix Order](cvss-context.md) builds from the temporal and environmental scores.

## Limitation

CVSS measures **severity**, not risk: how bad exploitation would be, not how likely {% cite first_cvss31 %}. Version 4.0 (2023) changes groups and equations, so scores from different versions are not comparable {% cite first_cvss40 %}.

## How solid is this?

- **Where it comes from.** The metric groups date from version 2 {% cite mell2007cvss %}; the weights and equations are from the 3.1 specification {% cite first_cvss31 %}. Every score on this page was recomputed from its vector with the 3.1 equations.
- **What is contested.** Scope: analysts judge it differently, and CVSS 4.0 replaced it {% cite first_cvss40 %}.
- **What we do not hold.** Studies measuring how consistently different analysts score the same vulnerability.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
