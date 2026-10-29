---
title: From Severity to Fix Order
parent: Security
grand_parent: Quality Attributes
nav_order: 8
layout: default
math: katex
page_type: deep-dive   # the temporal and environmental values, with when to choose each, must be on the page so every adjusted score can be reproduced by hand
---

# From Severity to Fix Order

A [CVSS](risk-rating.md) base score says how bad exploitation would be, anywhere. Choosing what to fix first also needs likelihood now and what the system holds: the **temporal** and **environmental** scores {% cite first_cvss31 %}.

## 1. Severity is not risk

**Risk** is roughly likelihood × impact. The base score is a severity: it assumes the attack happens and rates the damage. Two equal base scores can differ in risk: one vulnerability has a public exploit on an internet-facing payment server, the other none on an internal demo. The temporal score adjusts for likelihood, the environmental score for what is at stake; fix order follows them, not the base score alone.

## 2. Temporal score

The temporal score is roundup(Base × E × RL × RC), with the weights below, paraphrased from the 3.1 specification {% cite first_cvss31 %}. Each metric also has **X** (Not Defined) [1], scored like the highest value.

| Metric | Choose this value when… |
|---|---|
| Exploit Code Maturity (E) | **H** [1]: autonomous exploit code (a worm, a point-and-click tool), or none needed and details public · **F** [0.97]: functional exploit code works in most situations · **P** [0.94]: only proof-of-concept code, which a skilled attacker must adapt · **U** [0.91]: no exploit code, or the attack is theoretical |
| Remediation Level (RL) | **U** [1]: no fix, or none that can be applied · **W** [0.97]: an unofficial, non-vendor workaround · **T** [0.96]: an official but temporary fix, such as a vendor hotfix · **O** [0.95]: a complete vendor fix, a patch or an upgrade |
| Report Confidence (RC) | **C** [1]: confirmed, by the vendor, the source code or a working reproduction · **R** [0.96]: details public, root cause not fully confirmed · **U** [0.92]: impact reported, cause unknown or disputed |

E, RL and RC describe a *published* vulnerability's exploit and fix status, so temporal scoring applies to published CVEs.

**Example: Log4Shell at CVE publication.** NVD published CVE-2021-44228 on 10 December 2021 with base 10.0 {% cite nist_cve_2021_44228 %}, and the official fix, 2.15.0, was out that day {% cite cisa2021log4j %}: RL:O. Exploiting it needed only a one-line string in any logged field, with no tooling, and mass attempts followed within hours; Cloudflare saw the first nine minutes after disclosure and probes from 1 December {% cite cloudflare2021log4j %}. That is "no exploit required, details widely available": E:H. The vendor confirmed it: RC:C. Temporal = 10.0 × 1 × 0.95 × 1 = **9.5**. The fix later proved incomplete (CVE-2021-45046; 2.16.0 replaced it) {% cite apache_log4j_security %}, so a remediation level is a snapshot.

## 3. Environmental score

The environmental score recomputes the base score with the organisation's Confidentiality, Integrity and Availability Requirements (CR, IR, AR: Low 0.5 · Medium 1 · High 1.5) and, optionally, **modified base metrics** (MAV, MAC, MPR, MUI, MC, MI, MA), which restate a base metric for one deployment. With scope unchanged, the specification caps MISS at 0.915:

| Requirement (CR, IR, AR) | Low [0.5] | Medium [1] | High [1.5] |
|---|---|---|---|
| Loss of C, I or A would have… | a limited adverse effect | a serious adverse effect | a catastrophic adverse effect |

$$
\text{MISS} = \min\bigl(1 - (1 - CR \cdot MC)(1 - IR \cdot MI)(1 - AR \cdot MA),\ 0.915\bigr), \quad \text{Modified Impact} = 6.42 \times \text{MISS}
$$

**Modified Exploitability** = 8.22 × MAV × MAC × MPR × MUI, the base exploitability with any modified values put in. The environmental score is roundup(roundup(min(Modified Impact + Modified Exploitability, 10)) × the three temporal weights). With scope changed (not needed for the shop) it follows the base pattern: Modified Impact = 7.52 × (MISS − 0.029) − 3.25 × (MISS × 0.9731 − 0.02)^13 (as the 3.1 specification states), and the sum is multiplied by 1.08 before the cap. A requirement left Not Defined (X) counts as 1, the same as Medium, whereas a temporal X counts as the highest value; a modified base metric left X keeps its base value.

**Example (a hypothetical demo shop).** The shop's SQL injection, `AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`, base 9.1, no temporal metrics (weights 1). A demo shop whose user table holds only nicknames sets CR:L, with IR and AR Medium (1): MISS = 1 − (1 − 0.5 × 0.56)(1 − 1 × 0.56)(1 − 1 × 0) = 0.6832, Modified Impact = 4.386, and roundup(min(4.386 + 3.887, 10)) = roundup(8.273) = **8.3**, High rather than Critical. The full vector is `AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N/CR:L/IR:M/AR:M`.

**Where the inputs come from on a real system.** Temporal values change with the outside world: the vendor advisory gives remediation level and confirmation; exploit status comes from public exploit databases and CISA's Known Exploited Vulnerabilities catalogue. CR, IR and AR come from the organisation's data classification, set once per system, not per finding.

## 4. Example: ranking the shop's findings

The Bandit findings from [Static Security Analysis](static-analysis.md). Two are reachable vulnerabilities and get a score; neither reaches another system, so S:U:

| Finding | Who can reach it | Base = temporal (E, RL, RC = X, unpublished) | Environmental (CR:M/IR:M/AR:M) |
|---|---|---|---|
| SQL injection in login | anyone, through the login form | 9.1 | 9.1 |
| `eval` of the discount code | any logged-in customer at checkout (PR:L) | 8.8 | 8.8 |

Two findings are not scored, because the vulnerability they enable depends on events outside the code shown. The **hardcoded password (B105)**, the shop's `SECRET_KEY`, is never used in that code, and its impact depends on how the key leaks and what it signs. **Unsalted MD5 (B324)** only raises the impact of another breach that leaks the user table, since cracked passwords are reused elsewhere.

**The order.** All findings go into one list sorted by score: the environmental score when requirements are set (it already includes the temporal weights), otherwise temporal for published CVEs and base for in-house findings; ties are broken by exposure (who can reach it now); unscored hardening items (changes that remove a weakness before any attack uses it) come last, ordered by who can reach them. Here: the injection (9.1, anyone), then `eval` (8.8). Then the key, moved out of the code and rotated, before MD5, because the key is exposed to anyone who reads the source while MD5 matters only after another breach; both are hardening, fixed after the scored findings.

## When to use it — and when not

Use these scores to order fixes inside one organisation; a vendor advisory states only the base score.

## Limitation

Both adjustments are coarse. Temporal weights lower a score by at most a fifth (0.91 × 0.95 × 0.92 = 0.80), and CVSS 3.1 does not measure whether attackers actually target a vulnerability.

## How solid is this?

- **Where it comes from.** The weights and equations are from the 3.1 specification {% cite first_cvss31 %}; every score was recomputed from its vector.
- **What is contested.** How far an organisation's requirements should outweigh the vendor's base score.
- **What we do not hold.** Data comparing CVSS-ordered patching with other orderings in real organisations.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
