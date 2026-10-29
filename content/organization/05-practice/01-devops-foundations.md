---
title: DevOps Foundations
parent: Industry Practices
nav_order: 1
layout: default
---

# DevOps Foundations

**DevOps** is a way of organizing software delivery in which development and operations stop working as separate departments and share responsibility for building, releasing and running the software, supported by heavy automation of build, test and deployment {% cite luz2019devops williams2019devops %}. It is a culture first and a toolchain second.

## 1. Culture is the core

Luz and colleagues built a grounded theory of DevOps adoption from interviews in 15 adoption scenarios in five countries {% cite luz2019devops %}. Their first candidate for the central concept was **automation**; the data pushed them to **collaborative culture** instead, the one category that explained the others. In their model:

- **Collaborative culture** is the core: operations tasks become part of developers' everyday work, and failures are a shared problem, not someone else's.
- **Enablers** make the culture possible: automation, transparency and sharing of information, continuous measurement and quality assurance.
- **Outcomes** follow: agility (frequent, low-risk releases) and resilience (fast recovery).

Their adoption model has three steps: first spread the importance of a collaborative culture between development and operations, then select the enablers that suit the company's context, then check the outcomes against the company's needs.

Williams' study of teams after adoption describes the same shift in practice: silos broken down, shared responsibility in place of blame, servant leadership instead of top-down control, and continuous learning {% cite williams2019devops %}.

## 2. The technical enablers

- **Continuous integration (CI):** every change is merged into the main line and built and tested automatically, many times a day; see [Continuous Integration](13-continuous-integration.md).
- **Continuous delivery or deployment (CD):** every change that passes the pipeline can be, or is, released to production automatically.
- **Infrastructure as code (IaC):** servers and environments are described in version-controlled files, so environments can be rebuilt and reviewed like code.
- **Monitoring and telemetry:** the team running the service sees how it behaves in production and learns from it.

Four delivery measures are widely used to track the result: **deployment frequency** (how often changes reach production), **lead time** for changes (from commit to running in production), **mean time to recovery (MTTR)** after a failure in production, and **change failure rate** (the share of deployments that cause one) {% cite sethupathy2025devops %}.

## 3. Example: TCU

The Brazilian Federal Court of Accounts (TCU) is the case Luz and colleagues used to test their adoption model {% cite luz2019devops %}. Before DevOps, the operations team's rigid policies allowed deployments of the most important systems once a week. After the adoption, the team made 29 deployments of one of those systems in a single day. The gain came less from new tools than from operations and developers agreeing to own releases together.

## When to use it — and when not

DevOps fits services that change often and that the organization runs itself. Regulated domains can adopt it but need audit trails and approvals built into the pipeline: a study of U.S. healthcare names the Mayo Clinic as an early adopter, and found regulation (HIPAA) and patient-data sensitivity slowing adoption elsewhere {% cite akinola2023devops %}. It is a poor fit for software shipped once to customers who install it themselves, where frequent deployment has no meaning.

## Limitations

- **"DevOps" means different things.** It is both a culture and a job title (DevOps engineer), and organizations implement it very differently; see [DevOps Teams & Adoption](02-devops-teams-adoption.md).
- **Adoption is hard to start.** A review of 358 articles plus interviews in six organizations names missing standard guidelines and skills, resistance and management hesitation, and tool costs as the main obstacles {% cite hamza2024devops %}.
- **Headline multipliers lack sources here.** Figures such as "46 times more deployments" come from industry surveys that the area's review does not hold.

## How solid is this?

- **Where it comes from.** A grounded theory of DevOps adoption, a mixed-methods study of cultural change, a review-plus-interview study of adoption, and a maturity-framework study {% cite luz2019devops williams2019devops hamza2024devops sethupathy2025devops %}.
- **What is contested.** Whether automation or culture comes first; in Luz and colleagues' analysis automation was the first candidate for the core and lost to culture because it explained less of the data {% cite luz2019devops %}.
- **What we do not hold.** The State of DevOps (DORA) survey reports behind the most quoted performance comparisons.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
