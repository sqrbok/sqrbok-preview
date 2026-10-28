---
title: Industry Practices
parent: Organizing Quality
nav_order: 5
layout: default
has_children: true
---

# Industry Practices

**Industry quality practices** are the team-level routines (DevOps, testing strategies, reviews, SRE, deployment gates) through which companies build quality into everyday delivery rather than checking it at the end. Most of the evidence comes from companies studying their own data, so read each result together with its context.

## Pages in this section

| Page | What it covers | Best fit | Main cost |
|---|---|---|---|
| [DevOps Foundations](01-devops-foundations.md) | collaborative culture, CI/CD, delivery measures | teams running their own services | changing who owns operations |
| [DevOps Teams & Adoption](02-devops-teams-adoption.md) | four team patterns, a maturity path | reorganizing Dev and Ops | restructuring and coaching |
| [Agile Retrospectives](03-agile-retrospectives.md) | how retrospectives run, problems, what teams discuss | every iterative team | an hour per iteration |
| [Testing Practices](04-testing-practices.md) | shift-left, shift-right, test automation | any product team | automation effort |
| [PSP & TSP](05-psp-tsp.md) | personal and team process with measured data | predictability and very low defect rates | detailed logging and training |
| [Site Reliability Engineering](06-site-reliability-engineering.md) | SLOs, error budgets, golden signals, toil | services with users and on-call | monitoring and an enforced policy |
| [Industry Case Studies](07-industry-case-studies.md) | what Google, Microsoft and Facebook measured | evidence for a change | reading results in context |
| [Google Engineering Practices](08-google-engineering-practices.md) | test sizes, testing at scale, code review, static analysis | teams sharing one code base | tooling |
| [Microsoft Quality Practices](09-microsoft-quality-practices.md) | milestones, bug bars, ship room; review and refactoring research | scheduled releases of installed products | test roles and triage meetings |
| [Quality Gates & CI/CD](10-quality-gates-cicd.md) | gates by stage, canary, feature flags, rollback | continuous delivery | pipeline engineering |
| [Continuous Deployment at Facebook](11-facebook-continuous-deployment.md) | daily web deployment, mobile release trains, Sapienz | high-change products | about 5–15% of engineers on tooling |
| [Chaos Engineering](12-chaos-engineering.md) | fault-injection experiments and safeguards | distributed systems with monitoring | risk of causing an outage |

Process-improvement methods (DPP, Six Sigma, Theory of Constraints) are in [Process Improvement](../03-process/index.md); maturity frameworks in [Quality Standards](../04-standards/index.md).

## How solid is this?

- **Where it comes from.** Peer-reviewed studies of DevOps, retrospectives and company data, SEI reports on PSP and TSP, and practitioner books from Google and Microsoft {% cite luz2019devops lehtinen2017retrospectives sadowski2018codereview davis2003tsp beyer2016sre page2008mstesting %}.
- **What is contested.** Most large-scale evidence is published by the companies themselves.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
