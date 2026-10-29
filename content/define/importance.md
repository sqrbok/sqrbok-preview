---
parent: Quality
nav_order: 1
title: Why Software Quality Matters
layout: default
---

# Why Software Quality Matters

A **software failure** is an event in which a system does not do what its users need, and its cost is the clearest argument for treating quality as engineering work rather than a finishing touch. Each failure below broke one named quality characteristic; naming it is the first step towards a requirement that would have caught it.

## 1. Failures, and the characteristic each one broke

The table pairs eight documented failures with the [ISO/IEC 25010](foundations/iso25010.md) characteristic they violated, written as *characteristic (subcharacteristic)*; the [Foundations](foundations/) pages define each term.

| Year | System | What went wrong | Characteristic |
|---|---|---|---|
| 1962 | Mariner 1 probe | A bar missing from a symbol in the guidance equation let raw, noisy radar data steer the rocket; it was destroyed about 294 s after launch (about US$18.5 million at the time) {% cite nasa_mariner1 %} | Functional suitability (functional correctness) |
| 1985–87 | Therac-25 radiotherapy | Race conditions in the control software allowed six massive radiation overdoses; several patients died {% cite leveson1993therac %} | Safety (fail safe) |
| 1996 | Ariane 5, flight 501 | Converting a 64-bit floating-point value to a 16-bit integer overflowed in reused Ariane 4 code; the launcher self-destructed about 40 s after lift-off {% cite lions1996ariane %} | Reliability (fault tolerance) |
| 1999 | Mars Climate Orbiter | Ground software reported thruster impulse in pound-force seconds; the navigation software expected newton-seconds {% cite nasa1999mco %} | Compatibility (interoperability) |
| 2012 | Knight Capital | A deployment left old code active on one server; in 45 minutes the firm's router sent over 4 million orders and lost more than US$460 million {% cite sec2013knight %} | Maintainability (analysability) |
| 2014 | OpenSSL "Heartbleed" | A missing length check let any client read up to 64 KB of server memory, including private keys {% cite nvd2014heartbleed %} | Security (confidentiality) |
| 2015 | Boeing 787 | A counter in the generator control units overflowed after 248 days of continuous power, which could shut down all AC power; the interim fix was to power-cycle the aircraft {% cite faa2015boeing787 %} | Reliability (faultlessness) |
| 2024 | CrowdStrike Falcon update | A faulty content update crashed about 8.5 million Windows machines, under 1% of the total, but stopped airlines, hospitals and banks {% cite weston2024crowdstrike %} | Reliability (availability) |

For example, the Knight Capital loss was not a programming error in the new code. The firm reused a flag that had once switched on a function retired years earlier, and one of eight servers did not receive the new release. That is a maintainability failure: dead code nobody removed made the system hard to analyse, and a deployment nobody checked made it fatal.

## 2. Downtime is a business cost

Failures that kill no one still cost money. A 2024 survey of executives at 2,000 of the world's largest companies put the cost of unplanned downtime at about US$400 billion a year, about 9% of their profits {% cite splunk2024downtime %}. The figure is self-reported by a vendor's survey, so read it as an order of magnitude, not a measurement. For a whole economy, see the national estimate in [Cost of Poor Software Quality](cost-of-poor-quality.md).

## 3. Late fixes usually cost more

A defect that survives into operation costs more to fix than one caught in requirements, though by how much depends on the system; [Cost of Late Fixes](cost-of-late-fixes.md) gives the measured figures and the counter-evidence.

## 4. From incident to requirement

A failure list is useful only if it changes what a team specifies. Ask of each incident: which characteristic failed, and what measurable requirement would have caught it? For the Boeing 787 the answer is a reliability requirement on continuous operation, tested by running the counter past its limit. The pages on [quality models](foundations/quality-models.md) and [quality requirements](foundations/requirements.md) show how to write such a requirement.

## When to use it — and when not

- **Use** incident stories to motivate a quality requirement and to test whether your own quality model would have named the risk.
- **Do not** use them to estimate how likely a failure is: famous incidents are chosen because they are rare and dramatic.

## How solid is this?

- **Where it comes from.** Each row cites one source: an official inquiry or regulator (Ariane 5, Mars Climate Orbiter, Knight Capital, Boeing 787), the academic investigation by Leveson and Turner (Therac-25), Microsoft's statement (CrowdStrike), NASA's mission page (Mariner 1) or the public vulnerability record (Heartbleed).
- **What is contested.** Whether the famous incidents are representative: they are remembered because they were dramatic, and none gives a failure rate.
- **What we do not hold.** Downtime costs per company or per hour come from vendor surveys; we hold no independent estimate.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
