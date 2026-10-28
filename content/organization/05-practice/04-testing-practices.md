---
title: Testing Practices
parent: Industry Practices
nav_order: 4
layout: default
---

# Testing Practices

Modern testing practice moves quality activities to both ends of the delivery pipeline: **shift-left** testing starts verification as early as possible (reviews, static analysis, tests written with the code), and **shift-right** testing continues it in production (canary releases, monitoring, experiments) {% cite keating2024shift %}. Test automation connects the two.

## 1. Shift left: find defects where they are made

Shift-left moves verification closer to where defects are introduced: design reviews, code review and inspection, static analysis, acceptance criteria written as tests (ATDD), and unit and integration tests run on every change. A study comparing early-phase verification (design review, code review, inspection) with testing after development reports that the early techniques consistently did better at preventing defects {% cite magori2024early %}. The reasoning is the defect-cost argument of [Cost of Quality](../01-coq/index.md): a defect found in review is fixed before anyone builds on it.

## 2. Shift right: keep testing in production

Some failures appear only with real users, data and load. Shift-right techniques test there under control {% cite keating2024shift %}:

- **Canary release:** send a small share of traffic to the new version and compare its errors and latency with the old one (see [Quality Gates & CI/CD](10-quality-gates-cicd.md)).
- **Feature flags:** deploy code switched off and turn it on for chosen users.
- **A/B testing:** run two variants and compare outcomes.
- **Chaos engineering:** inject failures deliberately to check resilience (see [Chaos Engineering](12-chaos-engineering.md)).
- **Observability:** logs, metrics and traces, increasingly with machine-learned anomaly detection, to notice problems before users report them.

Gottam's *convergent quality engineering* closes the loop: production telemetry decides which tests matter most, and test results decide where to watch production most closely {% cite gottam2025convergent %}.

## 3. Example: test automation at F-Secure

Wang and colleagues report on test automation improvement in "Epics", an 11-engineer DevOps team at the Finnish security company F-Secure that builds Windows endpoint-security software {% cite wang2020testautomation %}. The team named the factors behind its success:

- **Whole-team effort:** everyone writes and maintains automation. In 2005 a separate two-person team built the tests for the feature teams, and the feature teams lacked the skills to maintain them.
- **Tool choice and architecture:** test tools are treated as interchangeable "Lego bricks", each small enough to replace as the team learns.
- **Product testability:** visibility and control designed into the product so it can be tested in isolation.
- **Telemetry and an incremental approach:** measure, improve a little, repeat.

The time from a release decision to the release reaching the first customer machines fell from 5 days in 2018 to 4 hours in 2019.

## When to use them — and when not

Every team benefits from shift-left basics (review, static analysis, fast unit tests). Shift-right needs a service the team runs itself, users whose experience can be measured, and the ability to roll back quickly; it is not available for software installed by customers on isolated machines, and it is no substitute for testing before release where failures are dangerous.

## Limitations

- **Thin evidence.** The early-V&V study is a preprint that has not been peer reviewed; the convergent-quality and shift-right papers are conceptual frameworks, not measurements {% cite magori2024early gottam2025convergent keating2024shift %}.
- **One team.** The F-Secure report is a self-reported experience report of one team.

## How solid is this?

- **Where it comes from.** An experience report, a preprint on early verification, and two conceptual papers {% cite wang2020testautomation magori2024early keating2024shift gottam2025convergent %}.
- **What is contested.** How much to test in production; it moves risk to users, and the frameworks above do not quantify that trade-off.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
