---
title: ISO 9000 Quality Systems
parent: Quality Standards
nav_order: 5
layout: default
---

# ISO 9000 Quality Systems

**ISO 9000** is a family of international standards for quality management systems; its best-known member, **ISO 9001**, states the requirements an organization must meet to be certified by an external auditor as having a documented, controlled quality system {% cite stelzer1997iso9000 boiral2012iso9000 %}. Unlike CMMI it is not specific to software and has no maturity levels: an organization is certified or not.

## 1. The family

In the version that the software studies examined, the family had distinct parts {% cite stelzer1997iso9000 %}:

| Standard | Purpose |
|---|---|
| ISO 9001 | requirements for external quality assurance: the basis of certification |
| ISO 9004 | guidance for internal quality management and improvement, beyond the minimum |
| ISO 9000-3 | guidelines for applying ISO 9001 to software development, supply and maintenance |

Stelzer and colleagues found that software organizations worked mostly from ISO 9001 and largely ignored ISO 9000-3 and ISO 9004, which is where the improvement-oriented guidance was {% cite stelzer1997iso9000 %}.

## 2. Example: certified, but not improved

Stelzer, Mellis and Herzwurm surveyed software houses in two studies (20 German and 36 European) {% cite stelzer1997iso9000 %}. They asked about five elements of a quality system: code reviews and inspections, software testing, product and process measurement, measurement of quality costs, and demonstration of quality improvements. The paper reports the results below for the surveys together, without the number behind each share. **Forty percent** of the software houses had not changed any of the five. Yet no respondent called the quality system a failure: 47% said their expectations were met and 53% that they were partly met. The authors' conclusion is that the benefit of ISO 9000 for software comes less from its technical content than from the culture created by a company-wide improvement program, and that smaller firms (20 to 100 employees) often gained the most, because they had less structure to begin with.

## 3. What the research reports

Boiral reviewed 111 empirical papers on ISO 9000 published between 1994 and 2008 {% cite boiral2012iso9000 %}. Most reported benefits; the share of papers that found a positive effect, among those that measured it, was:

| Effect | Papers measuring it | Share reporting a positive effect |
|---|---|---|
| Operational efficiency | 27 | 96.3% |
| Customer satisfaction | 38 | 94.7% |
| Productivity | 32 | 93.7% |
| Organizational image | 26 | 96.2% |
| Financial results | 34 | 82.4% |

Boiral's own reading is critical: the studies rely mostly on the perceptions of quality managers, few investigate negative effects such as bureaucracy, paperwork and cost, and their conclusions, he writes, "do not necessarily represent the reality of ISO impacts, but rather the dominant measurements of this reality."

## When to use it — and when not

Seek ISO 9001 certification when customers require it, which is common in supplier relationships, or when an organization wants an externally checked, documented quality system across all its activities, not only software. If the goal is to improve how software is built, certification alone is weak evidence; the Stelzer surveys show that a certificate can coexist with unchanged practice.

## Limitations

- **Certification checks conformance, not outcomes.** An auditor checks that the documented process exists and is followed, not that the product is good.
- **Paperwork can become the goal,** the "paper certificate" problem the Stelzer study describes.
- **The evidence is old.** The software surveys date from the 1990s and the review's papers end in 2008; the standard has been revised several times since.

## How solid is this?

- **Where it comes from.** Two surveys of European software houses and a systematic review of 111 empirical studies {% cite stelzer1997iso9000 boiral2012iso9000 %}.
- **What is contested.** Whether the reported benefits are real: Boiral points out that the studies use heterogeneous measures that are hard to compare and rest largely on quality managers' perceptions {% cite boiral2012iso9000 %}, and a perception survey cannot separate the effect of certification from that of being a firm that seeks it.
- **What we do not hold.** The current ISO 9001 text; the descriptions above follow the versions the studies examined.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
