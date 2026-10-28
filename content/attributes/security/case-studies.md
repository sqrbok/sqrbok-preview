---
title: Security Breach Case Studies
parent: Security
grand_parent: Quality Attributes
nav_order: 12
layout: default
page_type: deep-dive   # seven incidents, each needs its cause, numbers and a contestable principle reading
---

# Security Breach Case Studies

A **breach case study** traces a real incident from the fault that made it possible to the failure and its cost, to show which practice would have broken the chain. This page summarises seven incidents that the other security pages refer to; each fact is from the cited report.

## 1. Seven incidents

| Incident | Year | Fault | Failure |
|---|---|---|---|
| Target | 2013 | Network access from a contractor's credentials reached the payment systems | Card data stolen at point-of-sale terminals |
| Heartbleed | 2014 | OpenSSL trusted a length field in a heartbeat message (CVE-2014-0160) | Server memory, including keys and passwords, readable remotely |
| Equifax | 2017 | A public Apache Struts web application was left unpatched (CVE-2017-5638) | Personal data of about 148 million people stolen |
| WannaCry | 2017 | Windows SMBv1 servers left unpatched (CVE-2017-0144) | Ransomware spread worldwide; NHS care disrupted |
| SolarWinds | 2020 | Attackers inside the vendor's build system | A signed, malicious update installed by customers |
| Log4Shell | 2021 | Log4j resolved lookup expressions in logged text (CVE-2021-44228) | Remote code execution in any application that logged attacker input |
| XZ Utils | 2024 | A trusted maintainer inserted a backdoor (CVE-2024-3094) | Backdoored releases 5.6.0 and 5.6.1 of a widely used library |

## 2. What each one shows

**Example: Heartbleed.** The TLS (Transport Layer Security) heartbeat message carries its own payload length; OpenSSL copied that many bytes back without checking them against the real payload, returning up to 64 KB of adjacent memory per request {% cite nist_cve_2014_0160 %}. Netcraft estimated that about 17% of trusted HTTPS sites, around half a million, were vulnerable when it was disclosed {% cite netcraft2014heartbleed %}. Its CVSS base vector is `AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N`, 7.5: it leaks data but cannot change it. The fix was a bounds check, the textbook case of "all input is evil".

**Equifax.** The Struts vulnerability was disclosed on 7 March 2017; the attackers' first access to Equifax's unpatched dispute portal was on 13 May, more than two months later {% cite house2018equifax %}. A known vulnerability with a published fix was the entry point, which is why patching and inventory belong in security, not only in operations.

**WannaCry.** Microsoft had released the fix for the SMBv1 vulnerability two months before the May 2017 outbreak {% cite nist_cve_2017_0144 %}. In the UK, the Department of Health and Social Care estimated the cost to the NHS at about £92 million, with around 19,000 appointments cancelled {% cite dhsc2018wannacry %}.

**Log4Shell.** A logging library interpreted parts of logged text as instructions to look up and load remote code, a feature enabled by default. Any application that logged a user-controlled string, such as a user name or a browser identifier, could be taken over. It scored the maximum CVSS 10.0 {% cite nist_cve_2021_44228 %}; Wiz and EY reported that 93% of the enterprise cloud environments they analysed were vulnerable {% cite wiz2021log4shell %}. Its first fix, 2.15.0 (10 December 2021), was incomplete (CVE-2021-45046) and was replaced by 2.16.0 {% cite apache_log4j_security %}.

**SolarWinds.** Attackers compromised the build of the Orion monitoring product, so the malicious code was compiled, signed and delivered through the normal update channel {% cite cisa_solarwinds %}. About 18,000 customers installed the affected update {% cite gao2021solarwinds %}. The customers' own security tools trusted the update precisely because it was signed.

**Target.** The attackers first entered with network credentials stolen from a heating and ventilation contractor, then moved from that access to the payment network and installed malware on the store terminals {% cite krebs2014hvac %}.

**XZ Utils.** Releases 5.6.0 and 5.6.1 of the xz compression library contained malicious code added by a maintainer {% cite nist_cve_2024_3094 %}. Through the library, the backdoor reached SSH servers on systems that linked it; Andres Freund reported it in March 2024 after noticing unusual behaviour {% cite freund2024xz %}. Williams et al. use it as the example of a supply chain attack through humans rather than code {% cite williams2025supplychain %}.

## 3. Which principle was violated? A judgement

Mapping an incident to one [secure design principle](secure-design.md) is a useful exercise, but most incidents support more than one reading. State your reading and why; the table shows a main reading and a defensible alternative. The vocabulary is Saltzer and Schroeder's eight principles plus Howard and LeBlanc's SD3 commitments; "validate all input" is a coding practice, and Heartbleed breaks it on top of either reading. Saltzer and Schroeder's *fail-safe defaults* is about access decisions (deny unless permitted); a risky feature switched on at installation breaks SD3's *secure by default* instead. SolarWinds' main reading, separation of privilege, asks whether one compromised path was enough to ship code.

| Incident | Main reading | Also defensible |
|---|---|---|
| Heartbleed | Economy of mechanism: a rarely used feature added code, and a missing check, to a critical library | Secure by default (SD3): the extension was enabled for every server, used or not |
| Equifax | Secure in deployment: a known fix was not applied | Least privilege: once inside, the attackers could query many databases |
| WannaCry | Secure in deployment: patches not applied | Secure by default (SD3): an obsolete protocol was enabled by default |
| Log4Shell | Secure by default (SD3): remote lookups were on by default | Economy of mechanism: a logger should not execute code |
| SolarWinds | Separation of privilege: one compromised build path was enough | Complete mediation: signed updates were trusted without further checks |
| Target | Least privilege: contractor access reached payment systems | Least common mechanism: networks were not separated |
| XZ Utils | Separation of privilege: one trusted person could ship a release | Open design helped detection: the public source let an outsider trace the anomaly |

## When to use it — and when not

Use these cases to explain why a practice matters, and as worked examples for tracing a fault-to-failure chain. Do not use them as evidence of how common a cause is: famous incidents are chosen because they are dramatic, not because they are typical.

## Limitation

Hindsight makes every cause look obvious. At the time, each organisation had competing priorities, and the "one missing check" is clear only after the attack revealed it.

## How solid is this?

- **Where it comes from.** Each fact is from the cited official report, vulnerability record or reputable investigation.
- **What is contested.** The principle mapping is an interpretation, not a finding of any of the reports. Victim counts change as investigations continue; the Equifax figure in particular was revised several times.
- **What we do not hold.** Full official reports for Target and for XZ Utils; their accounts here rely on a journalist's investigation and on a research survey.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
