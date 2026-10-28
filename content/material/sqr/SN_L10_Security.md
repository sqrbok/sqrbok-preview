---
title: "SN L10: Software Security"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 19
redirect_from: /content/attributes/security/SN_L10_Security.html
layout: default
page_type: study-notes
---

# SN L10: Software Security

Ahead: **SAST** (static analysis, §3) reads code; **DAST** (dynamic testing, §4) attacks the running system; a **pentest** (penetration test, §4) is an authorized human attack; **STRIDE** (§5) lists threats per design element; §6 scores, §7 designs out, §8 automates. [Revision questions](RQ_L10_Security.md) test it.

## 1. Security keeps a system confidential, intact and available

Avizienis et al. define security as the **CIA triad**: **confidentiality** (no unauthorized disclosure), **integrity** (no improper change) and **availability** for authorized actions {% cite avizienis2004basic %}.

→ [Security](../../attributes/security/index.md)

## 2. A vulnerability is a fault that an attacker can reach

Security reuses the reliability chain **fault → error → failure** (L08). A **vulnerability** is a fault an attacker can reach and exploit on purpose; malicious input that activates it is an **attack** {% cite avizienis2004basic %}. A shop login builds SQL from the user name:

```python
query = f"SELECT id FROM users WHERE name = '{name}' AND pw = '{hash_password(password)}'"
```

The **fault** is the string-built query; the **attack** `alice' --` comments out the password check (**error**); the attacker is logged in as Alice (**failure**), reading her data and acting as her. O'Brien triggers it by accident: malformed SQL (error), one failed request (failure), not a breach. One fix, a parameterised query, removes both.

McGraw splits security problems roughly evenly into **bugs** (code faults, like this one) and **flaws** (design faults) {% cite mcgraw2006software %}. Strategies: *detecting* finds existing vulnerabilities; *preventing* stops them arising or limits their effect. Of Avizienis's four means, **fault removal** (SAST, DAST, pentests, review) serves detecting; **fault prevention** (secure design, requirements, coding), **fault tolerance** (rate limiting, sandboxing, RASP: runtime application self-protection) and **fault forecasting** serve preventing. Threat modeling is fault forecasting: it serves preventing but is not the means fault prevention.

→ [Vulnerabilities, Bugs and Flaws](../../attributes/security/vulnerabilities.md)

## 3. Static analysis finds bugs early but cannot avoid false positives

**SAST** reads source code without running it, up to taint analysis, which follows untrusted input from a *source* (the request) to a *sink* (`db.execute`) {% cite chess2007secure %}. By **Rice's theorem** no analyser decides every non-trivial program property, so none avoids both **false positives** (warnings on safe code) and **false negatives** (missed vulnerabilities).

**Example.** Bandit on the shop app with a hardcoded password (its secret key), MD5 passwords, the query above and an `eval` of the discount code a logged-in customer enters at checkout reports five issues, four of them below, each with a **CWE** (Common Weakness Enumeration) number {% cite bandit_docs %}:

```text
[B105] Possible hardcoded password       Severity: Low     Confidence: Medium  CWE-259
[B324] Use of weak MD5 hash for security Severity: High    Confidence: High    CWE-327
[B608] Possible SQL injection vector     Severity: Medium  Confidence: Low     CWE-89
[B307] Use of possibly insecure function Severity: Medium  Confidence: High    CWE-78
```

Bandit labels `eval` CWE-78 (OS command injection: the code can reach the OS); CWE-95, eval injection, fits closer, and the quiz accepts it. Bandit's **severity** (its own scale, not CVSS) is the harm if real; **confidence**, the likelihood it is. The fifth, a B608 on `f"SELECT COUNT(*) FROM {ORDERS_TABLE}"` in another file, is a false positive, suppressed with `# nosec B608` and a reason. Practitioners call about 30% of results *effective* (the rest includes real issues not worth fixing) {% cite rajapakse2021security %}; noise causes **alert fatigue**. Bandit scans the shop in about 0.15 s.

→ [Static Security Analysis](../../attributes/security/static-analysis.md)

## 4. Dynamic testing and humans attack the running system

**DAST** sends attacks to a running application, from **passive** exploration to **active** attack {% cite owasp2020wstg %}; it sees only the paths it reaches. A **white-box exploit test** sends the payload straight to the function, skipping HTTP, on every build (as the lab's `test/hack.py` does):

```python
def test_injection_does_not_bypass_password(db):
    assert shop.find_user(db, "alice' --", "wrong") is None
```

**Fuzzing** sends masses of malformed inputs, best at parsers and input-heavy interfaces {% cite takanen2008fuzzing %}; it catches crashes, not wrong business logic.

**Penetration testing** is an authorized human attack in NIST SP 800-115's four phases: **planning** (rules, written approval), **discovery** (gathering information, finding vulnerabilities), **attack** (exploiting a found weakness to gain access, escalating, looping back) and **reporting** {% cite nist2008sp800115 %}. A pentest samples; it cannot prove vulnerabilities absent. Weidman adds **post-exploitation**: what an attacker could do once inside {% cite weidman2014penetration %}. Code review catches logic and concurrency defects tools miss, only where reviewers look: under 1% of 432,585 review comments concerned security; of the 539 security defects reviewers raised, none was SQL injection, 65.9% were fixed, and 81.3% when the reviewer proposed a fix {% cite yu2023security %}.

→ [Dynamic Security Testing](../../attributes/security/dynamic-testing.md) · [Penetration Testing and Security Code Review](../../attributes/security/pentest-review.md)

## 5. Threat modeling predicts design flaws, but misses most threats

Shostack's four questions: *What are we building? What can go wrong? What will we do? Did we do well?* {% cite shostack2014threat %} The model is a **data flow diagram**; **trust boundaries** mark privilege changes.

**STRIDE** names one threat against each of C, I, A and three derived properties, **authentication**, **authorization** and **non-repudiation**: **S**poofing (authentication), **T**ampering (integrity), **R**epudiation (non-repudiation), **I**nformation disclosure (confidentiality), **D**enial of service (availability), **E**levation of privilege (authorization) {% cite microsoft_stride %}. Per element: external entities S, R; processes all six; flows T, I, D; stores T, I, D, plus R for logs {% cite shostack2014threat %}. Mitigations: S, authentication; T, integrity checks; R, logging; I, encryption and access control; D, rate limiting; E, authorization. **S goes on the element whose identity is faked** (course convention): the §2 injection gives S on the Customer, I and T on the shop database;

Scandariato et al.'s 57 students reached 81% **precision** (reported threats real) but 36% **recall** (real threats reported) {% cite scandariato2015stride %}; Yskout et al. rate the field level 1 (ad hoc) of their five-level maturity scale, at about 69 hours of effort per project {% cite yskout2020tmmaturity %}. Alternatives include **PASTA** (seven risk-centred stages) and **attack trees** (a goal split into ways to reach it) {% cite shevchenko2018tm %}. **LINDDUN** (privacy) {% cite linddun_threat_types %}: **L**inking data about a person; **I**dentifying them; **N**on-repudiation, so no deniability (a threat to the user, unlike STRIDE's property); **D**etecting their involvement; **D**ata disclosure, too much personal data; **U**nawareness, users uninformed or powerless; **N**on-compliance with law.

→ [Threat Modeling](../../attributes/security/threat-modeling.md)

## 6. OWASP Top 10 and CVSS decide what comes first

Background only: the **OWASP** (Open Worldwide Application Security Project) **Top 10** ranks web risk categories; the course uses the lecture's 2021 edition (2025 current): Injection (A03), Cryptographic Failures (A02), Identification and Authentication Failures (A07) {% cite owasp2021top10 %} {% cite owasp2025top10 %}.

**CVSS** (Common Vulnerability Scoring System) scores one vulnerability 0–10 from **base**, **temporal** and **environmental** groups {% cite first_cvss31 %}. Bands: None 0.0, Low 0.1–3.9, Medium 4.0–6.9, High 7.0–8.9, Critical 9.0–10.0. 3.1 weights:

| Metric | Weights | Choose when… |
|---|---|---|
| *Base* | | |
| AV, attack vector | N 0.85, A 0.62, L 0.55, P 0.20 | N: remote; A (adjacent network): same local network; L: local shell, or a user opens a file; P: physical touch |
| AC, attack complexity | L 0.77, H 0.44 | H: needs conditions outside the attacker's control (e.g. a race) |
| PR, privileges | N 0.85; L 0.62 (S:C 0.68); H 0.27 (S:C 0.50) | N: no account; L: user account; H: admin |
| UI, user interaction | N 0.85, R 0.62 | R: a victim must act (e.g. click a link) |
| C, I, A impact | H 0.56, L 0.22, N 0 | H: total or seriously harmful loss; L: partial loss the attacker cannot direct |
| *Temporal* | | |
| E, exploit maturity | H 1, F 0.97, P 0.94, U 0.91 | H: autonomous exploit (worm, tool) or none needed; F: working code; P: proof of concept; U: none |
| RL, remediation | U 1, W 0.97, T 0.96, O 0.95 | U: no fix; W: unofficial workaround; T: official temporary fix; O: official patch |
| RC, report confidence | C 1, R 0.96, U 0.92 | C: confirmed; R: cause unconfirmed; U: cause unknown |
| *Environmental* | | |
| CR, IR, AR requirements | L 0.5, M 1, H 1.5 | this asset's need for C, I, A |

Temporal and requirement metrics also take X (not defined) = 1.

The Impact Sub-Score ISS = 1 − (1 − C)(1 − I)(1 − A); Exploitability = 8.22 × AV × AC × PR × UI. Scope **unchanged** (S:U): Impact = 6.42 × ISS, Base = roundup(min(Impact + Exploitability, 10)). S:C uses another formula, not computed in this course. If Impact ≤ 0, the Base score is 0; roundup rounds any remainder up to one decimal.

**Scope.** S:C when the **vulnerable component** (with the vulnerability) and the **impacted component** (suffering the impact) differ under different security authorities {% cite first_cvss31 %}. Even NVD (the National Vulnerability Database) is inconsistent: Log4Shell (code execution via the Log4j logging library, 2021) is S:C {% cite nist_cve_2021_44228 %}, but Spring4Shell, a Spring web-framework flaw that likewise runs attacker code in the host application, is S:U, 9.8 {% cite nist_cve_2022_22965 %}; CVSS 4.0 replaced scope {% cite first_cvss40 %}. Course procedure: **(a) a published CVE (Common Vulnerabilities and Exposures entry): copy NVD's vector; (b) your own finding: S:U unless the impact lands in a component under a different security authority**. The app's own database, host and cloud account are one authority even on separate servers, so shop injection and `eval` are S:U; a clear S:C case is cross-site scripting, a server's script running in the victim's browser (FIRST: CVE-2017-5942, 6.1) {% cite first_cvss31_examples %}. `eval`, `AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H` (8.8): PR:L, as only logged-in customers reach checkout; A:H, as it can also stop the service. In-house: E, RL, RC = X.

**Example.** The shop injection, `AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N`: C:H and I:H, any user's data read and changed; A:N, a broken query fails only the attacker's request. ISS = 1 − (1 − 0.56)(1 − 0.56) = 0.8064, Impact = 5.177, Exploitability = 8.22 × 0.85 × 0.77 × 0.85 × 0.85 = 3.887, Base = roundup(9.064) = **9.1**.

**Temporal and environmental.** Temporal = roundup(Base × E × RL × RC), for published CVEs only. Log4Shell at CVE publication (10 December 2021): base 10.0; fix 2.15.0 out (RL:O) {% cite cisa2021log4j %}; a one-line string sufficed and mass attempts followed within hours (E:H) {% cite cloudflare2021log4j %}; RC:C; temporal = 10.0 × 0.95 = 9.5. Environmental (S:U): MISS (Modified Impact Sub-Score) = min(1 − (1 − CR·C)(1 − IR·I)(1 − AR·A), 0.915), with C, I, A the impact weights, Modified Impact = 6.42 × MISS, then roundup(roundup(min(Modified Impact + Modified Exploitability, 10)) × E × RL × RC) (Modified Exploitability = Exploitability: no base metric modified). CVSS measures **severity**, not **risk** (≈ likelihood × impact).

→ [OWASP Top 10](../../attributes/security/owasp-top10.md) · [CVSS Scoring](../../attributes/security/risk-rating.md) · [From Severity to Fix Order](../../attributes/security/cvss-context.md)

## 7. Secure design rules out whole classes of vulnerability

Saltzer and Schroeder's principles {% cite saltzer1975protection %}:

| Principle | Meaning |
|---|---|
| Economy of mechanism | Keep it small and simple |
| Fail-safe defaults | Deny unless permitted |
| Complete mediation | Check every access |
| Open design | No secret designs; secret keys |
| Separation of privilege | Require two conditions |
| Least privilege | Minimum rights |
| Least common mechanism | Share little between users |
| Psychological acceptability | Easy to use correctly |

**SD3** (Howard and LeBlanc): secure by design, by default, in deployment (patched, securely operated) {% cite howard2006writing %}. Their rule *all input is evil until proven otherwise* is a practice, not one of the eight: use an **allowlist** (accept only known-good input), not a **blocklist** (reject known-bad patterns), canonicalise before validating (`%27`, and double-encoded `%2527`, decode to `'`), and validate at one **chokepoint**, a single entry routine every input passes.

**Breaches.** **Heartbleed** (CVE-2014-0160) trusted a length field {% cite nist_cve_2014_0160 %}. **Log4Shell** loaded remote code by default {% cite nist_cve_2021_44228 %}. **Equifax** (Apache Struts) and **WannaCry** (SMBv1, Windows file sharing) hit systems left unpatched two months after the fix {% cite house2018equifax %} {% cite nist_cve_2017_0144 %}. Hint for Heartbleed: **economy of mechanism** (a rarely needed feature), SD3's **secure by default** (on for all servers) or **complete mediation** (length never checked). At **Target**, a contractor's stolen credentials reached the payment network {% cite krebs2014hvac %}.

→ [Secure Design Principles](../../attributes/security/secure-design.md) · [Security Breach Case Studies](../../attributes/security/case-studies.md)

## 8. DevSecOps checks every change, including the supply chain

Until a fix ships, narrow who can reach the endpoint (network restriction, web application firewall rule) {% cite cloudflare2021log4j %}. **DevSecOps** gives each stage its activity: threat modeling at planning; IDE plug-ins, pre-commit secret scans and review while coding; SAST and SCA (software composition analysis) at build; DAST at test; configuration checks at deployment; monitoring in operation. Rangnau et al.'s dynamic-test job took 14 minutes 6 seconds, so it runs as a parallel, non-blocking job {% cite rangnau2020devsecops %}. `bandit -r app -ll` fails the build on medium or high severity, whatever the confidence; the low-severity hardcoded password passes, so add a secrets scanner.

Supply chain entry routes {% cite williams2025supplychain %}: **dependencies** (Log4Shell; control: SCA and an **SBOM**, a component inventory), **build infrastructure** (SolarWinds: attackers inserted code into the vendor's build system, and the signed update shipped it to customers; control: signed, reproducible builds) and **humans** (XZ Utils; control: two-maintainer review).

**SQUARE** is a nine-step security requirements process (see Q13) {% cite mead2005square %}. **RQCODE** writes checkable configuration requirements, not behaviour, as code (`check()`, `enforce()`); with ARQAN, a search over STIGs (Security Technical Implementation Guides), it matched 85% of 40 industrial requirements, 62% with a ready test {% cite sadovykh2024devsecops %}.

→ [DevSecOps and Supply Chain Security](../../attributes/security/devsecops.md) · [Security Requirements](../../attributes/security/security-requirements.md)

## Where this goes next

The security lab applies §3, §4, §6 and §8; its `test/hack.py` holds exploit tests like §4's.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
