---
title: Quality Gates & CI/CD
parent: Industry Practices
nav_order: 10
layout: default
---

# Quality Gates & CI/CD

A **quality gate** is a checkpoint in the delivery pipeline that a change must pass before it moves on, such as review approval, passing tests or a healthy canary; in continuous integration and delivery (CI/CD) most gates are automated so that they can run on every change {% cite winters2020swe beyer2016sre %}.

## 1. Gates by stage

| Stage | Typical gates | Who or what decides |
|---|---|---|
| **Pre-submit** (before merge) | code review approval; static analysis; affected tests pass | reviewer plus automated checks {% cite sadowski2018codereview sadowski2015tricorder %} |
| **Post-submit** (on the main line) | full build and test suite; the main line must stay green | CI system; a broken build is fixed or the change reverted first ([Continuous Integration](13-continuous-integration.md)) |
| **Release** | error budget not exhausted; canary healthy; production readiness review for new services | SRE policy and automated canary analysis {% cite beyer2018sreworkbook %} |

Microsoft's milestone model puts the same idea into a schedule: exit criteria and a bug bar at each milestone, and a ship-room decision at the end (see [Microsoft Quality Practices](09-microsoft-quality-practices.md)).

## 2. Canary and staged rollout

A **canary** release sends a small share of real traffic to the new version and compares its error rate and latency with the version still serving everyone else; if it looks worse, the rollout stops and is rolled back {% cite beyer2018sreworkbook %}. The sources give the comparison, not a fixed threshold; as an example, a team might stop the rollout when the canary's error rate exceeds the control's by more than the SLO allows, here 0.1 percentage point. _(Constructed example.)_ Such a threshold needs volume: a 0.1-point difference shows only after many thousands of canary requests, so at the 3 million requests a month of the SRE example (about 42 an hour on a 1% canary) the stage must run for days or use a larger share. A **staged rollout** then widens the share step by step, for example 1%, 10%, 50%, 100%, checking at each stage.

**Example.** A new version has a bug that makes 20% of its requests fail. The share of all users' requests that fail is the canary's share times its error rate {% cite beyer2018sreworkbook %}:

| Rollout | Overall error rate |
|---|---|
| Canary 1% | 0.01 × 20% = 0.2% |
| Canary 5% | 0.05 × 20% = 1% |
| No canary (100% at once) | 20% |

With a 99.9% SLO the error budget allows 0.1% of requests to fail. The [burn rate](06-site-reliability-engineering.md) is the overall error rate divided by that 0.1%: 2 for the 1% canary, 10 for the 5% canary. Left running, they would spend a 30-day budget in 15 and 3 days. A canary stopped after one hour spends only 2 / 720 ≈ 0.3% or 10 / 720 ≈ 1.4% of the budget. Both stay below the SRE page line (2% of the budget in an hour, burn rate 14.4), so no page fires: the canary gate, comparing canary with control, catches the bad version, not the page alert. The canary limits the **blast radius**: how many users a bad change can reach before it is caught.

## 3. Feature flags and rollback

A **feature flag** separates deploying code from launching a feature: the code ships switched off and is turned on for chosen users. Turning a flag off is much faster than building, testing and redeploying, which the SRE book puts at 15 minutes or more, and it disables one feature without touching the others {% cite beyer2016sre winters2020swe %}. Each short-lived flag is technical debt and should be removed once the feature is stable.

When a release goes wrong, the SRE practice is to **roll back first and fix afterwards**: restore the last good version, then diagnose the fault without users waiting {% cite beyer2016sre %}. Rolling back needs the previous version to be available and database changes that both versions can work with.

## When to use gates — and when not

Automate every gate that can be checked by a machine and keep human gates for judgment (review, release of risky changes). A canary needs enough traffic to compare error rates; with a few dozen requests an hour a 1% canary sees almost nothing, and a short staged rollout with careful monitoring serves better.

## Limitations

- **Gates slow delivery when they are slow.** A pre-submit check that takes an hour encourages large, batched changes, the opposite of what the gates are for.
- **A green gate is not proof.** Gates check what they check; a missing test passes every gate.

## How solid is this?

- **Where it comes from.** Google's SRE book and workbook, *Software Engineering at Google*, and Google's studies of code review and static analysis {% cite beyer2016sre beyer2018sreworkbook winters2020swe sadowski2018codereview sadowski2015tricorder %}.
- **What we do not hold.** The books' full text; their content reaches this page through the area's notes on them.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
