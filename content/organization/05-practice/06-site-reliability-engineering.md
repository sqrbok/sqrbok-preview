---
title: Site Reliability Engineering
parent: Industry Practices
nav_order: 6
layout: default
---

# Site Reliability Engineering

**Site reliability engineering (SRE)** is Google's way of running production services: operations work is treated as a software-engineering problem, reliability is set as an explicit, measured target, and the gap between that target and perfection is spent deliberately on change {% cite beyer2016sre %}. The reliability concepts behind it (SLIs, SLOs, availability) are covered in [From Failure Intensity Objectives to SLOs](../../attributes/reliability/slo-bridge.md); this page is about the practice.

## 1. Targets and the error budget

A **service level indicator (SLI)** is a measured ratio, for example successful requests divided by all requests. A **service level objective (SLO)** is the target for it, say 99.9% over 30 days. A **service level agreement (SLA)** is a contract with customers, with penalties if a lower promise is missed. The internal SLO is kept stricter than the SLA, so the team reacts before customers are owed anything {% cite beyer2016sre %}.

The **error budget** is 100% minus the SLO: the failure the team is allowed. 100% is the wrong target: it leaves no room for change and costs more than users notice. The **burn rate** is how fast the budget is being spent: the observed error rate divided by the error rate the SLO allows. At burn rate 1 the budget runs out exactly at the end of the window; at burn rate 10, ten times sooner {% cite beyer2018sreworkbook %}. This handbook uses a 30-day window.

**Example.** A service with a 99.9% SLO handles 3,000,000 requests in 30 days:

| Quantity | Value |
|---|---|
| Error budget | 100% − 99.9% = 0.1% |
| Failed requests allowed in 30 days | 0.001 × 3,000,000 = **3,000** |
| Burn rate 1 (spent evenly) | 3,000 / 720 hours ≈ 4.2 failures per hour |
| Burn rate 10 | budget gone in 30 / 10 = 3 days |
| Page if 2% of the budget burns in one hour | 0.02 × 3,000 = 60 failures in an hour, a burn rate of 0.02 × 720 = 14.4 |
| Ticket if 10% of the budget burns in three days | 0.1 × 3,000 = 300 failures in 72 hours, a burn rate of 0.1 × 720 / 72 = 1 |

Both alert rules are the workbook's: a **fast burn**, one that pages (here burn rate 14.4 or more for an hour), wakes someone; a slow burn becomes a ticket {% cite beyer2018sreworkbook %}. A sustained burn rate of 1 spends exactly the whole budget by the window's end, leaving no margin: worth a ticket, not a page. The workbook's example **error budget policy**, agreed between product management, development and SRE, fixes five things {% cite beyer2018sreworkbook %}:

- **The SLI and SLO**, here successful requests at 99.9%.
- **The window**, here 30 days (the workbook's example policy uses four weeks).
- **What happens when the budget is spent:** feature launches halt until the service is back within its SLO.
- **When a postmortem is required:** when one incident consumes more than 20% of the budget.
- **Who decides** in a dispute, named in advance.

## 2. Monitoring: page on symptoms

SRE monitors four **golden signals**: **latency**, **traffic**, **errors** and **saturation**, how full the most constrained resource is {% cite beyer2016sre %}. Alerts that wake someone should describe a **symptom** users feel ("checkout latency above 2 seconds"), not a **cause** ("database CPU at 90%"), which may never hurt users. Causes belong on dashboards for diagnosis.

## 3. Toil and on-call

**Toil** is operational work that is manual, repetitive, automatable, tactical, without lasting value, and that grows with the size of the service, such as restarting a stuck job by hand every week {% cite beyer2016sre %}. Google caps it at **50%** of an SRE's time; the rest goes to engineering that removes future toil. Without reserved engineering time, a growing service leaves a team doing nothing but toil {% cite beyer2016sre %}. On-call follows the same rule: at least half the time on project work, at most two incidents per 12-hour shift, so each can be analyzed. Every page is treated as a bug that needs a permanent fix.

## When to use it — and when not

Use SLOs and error budgets for any service with users: they turn "ship faster" versus "be careful" into a shared number. The full model needs scale; a small team can adopt the SLO, the error budget and the toil cap without it.

## Limitations

- **An SLO is only as good as its SLI.** Measuring at the server can miss failures users see in the browser or network.
- **Budgets need management backing.** A freeze that product managers can override is not a policy.
- **Industry statistics are weak.** A practitioner article reports 47% higher reliability for structured SRE programs, without a published method {% cite venkatesh2024sre %}.

## How solid is this?

- **Where it comes from.** Google's SRE book and workbook {% cite beyer2016sre beyer2018sreworkbook %}.
- **What is contested.** How far Google's practice transfers to smaller organizations.
- **What we do not hold.** The books' full text; this page draws on the area's notes.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
