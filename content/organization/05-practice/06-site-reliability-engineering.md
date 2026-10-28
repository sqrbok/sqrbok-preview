---
title: Site Reliability Engineering
parent: Industry Practices
nav_order: 6
layout: default
---

# Site Reliability Engineering

**Site reliability engineering (SRE)** is Google's way of running production services: operations work is treated as a software-engineering problem, reliability is set as an explicit, measured target, and the gap between that target and perfection is spent deliberately on change {% cite beyer2016sre %}. The reliability concepts behind it (SLIs, SLOs, availability) are covered in [From FIO to SLO](../../attributes/reliability/slo-bridge.md); this page is about the practice.

## 1. Targets and the error budget

A **service level indicator (SLI)** is a measured ratio, for example successful requests divided by all requests. A **service level objective (SLO)** is the target for it, say 99.9% over 30 days. A **service level agreement (SLA)** is a contract with customers that states what happens (refunds, penalties) if a lower promise is missed. The internal SLO is kept stricter than the SLA, so the team reacts before customers are owed anything {% cite beyer2016sre %}.

The **error budget** is 100% minus the SLO: the failure the team is allowed. SRE's argument is that 100% is the wrong target, because it leaves no room for change and costs more than users notice.

**Example.** A service with a 99.9% SLO handles 3,000,000 requests in 30 days:

| Quantity | Value |
|---|---|
| Error budget | 100% − 99.9% = 0.1% |
| Failed requests allowed in 30 days | 0.001 × 3,000,000 = **3,000** |
| Burn rate 1 (spent evenly) | 3,000 / 720 hours ≈ 4.2 failures per hour |
| Burn rate 10 | budget gone in 30 / 10 = 3 days |
| Page if 2% of the budget burns in one hour | 0.02 × 3,000 = 60 failures in an hour, a burn rate of 0.02 × 720 = 14.4 |

While budget remains, the team can release faster and take risks. When it is spent, the policy the SRE workbook describes halts feature launches and moves engineering to reliability work until the service is back within its SLO, with a postmortem required when one incident consumes a large share (the workbook's example: more than 20% of a four-week budget) {% cite beyer2018sreworkbook %}.

## 2. Monitoring: page on symptoms

SRE monitors four **golden signals**: **latency**, **traffic**, **errors** and **saturation** {% cite beyer2016sre %}. Alerts that wake someone should describe a **symptom** users feel ("checkout latency above 2 seconds"), not a **cause** ("database CPU at 90%"), because a cause may never hurt users while a symptom always does. Causes belong on dashboards for diagnosis.

## 3. Toil and on-call

**Toil** is operational work that is manual, repetitive, automatable, tactical, without lasting value, and that grows with the size of the service, such as restarting a stuck job by hand every week {% cite beyer2016sre %}. Google caps it at **50%** of an SRE's time; the rest goes to engineering that removes future toil. The same rule shapes on-call: at least half the time on project work, and at most two incidents per 12-hour shift, so that each incident can be analyzed properly. Every page is treated as a bug that needs a permanent fix.

## When to use it — and when not

Use SLOs and error budgets for any service with users and an operations cost; they turn the argument between "ship faster" and "be careful" into a shared number. The full model (dedicated SRE teams, on-call rotations) needs scale; a small team can adopt the SLO, the error budget and the toil cap without it.

## Limitations

- **An SLO is only as good as its SLI.** Measuring at the server can miss failures users see in the browser or network.
- **Budgets need management backing.** A freeze that product managers can override is not a policy.
- **Industry statistics are weak.** A practitioner article reports 47% higher reliability and 32% lower MTTR for organizations with structured SRE programs, without a published method {% cite venkatesh2024sre %}.

## How solid is this?

- **Where it comes from.** Google's SRE book and workbook {% cite beyer2016sre beyer2018sreworkbook %}.
- **What is contested.** How far Google's practice transfers to organizations without its scale.
- **What we do not hold.** The books' full text; their content reaches this page through the area's notes on them.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
