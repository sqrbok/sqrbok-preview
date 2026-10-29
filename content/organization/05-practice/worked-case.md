---
title: "Worked Case: Releasing and Operating a Service"
parent: Industry Practices
nav_order: 14
layout: default
page_type: worked-case
---

# Worked Case: Releasing and Operating a Service

This worked case takes one service through a release and its first incident: a production readiness review, the choice of deployment strategy, the rollback preconditions, a written error budget policy, and then one incident from the first alert to the blameless postmortem. The concepts are on [Site Reliability Engineering](06-site-reliability-engineering.md) and [Quality Gates & CI/CD](10-quality-gates-cicd.md).

**Example service.** A team of six runs the hotel-booking API of an online travel company, with an on-call rotation. It serves 3,000,000 requests in 30 days, the volume used on the SRE handbook page: about 4,200 requests an hour on average and 9,000 at the evening peak. Release 3.4 changes how prices are cached. _(Constructed example: the service, times and counts were made up for this page.)_

## 1. Production readiness review

Before a new service or a large change goes live, SRE teams hold a **production readiness review (PRR)**: a check that the service can be operated, not only that it works {% cite beyer2016sre %}. The team's checklist, a practitioner synthesis of the SRE books' advice, with the item the incident below added:

| # | Item | Evidence required |
|---|---|---|
| 1 | SLI and SLO written; a dashboard shows latency, traffic, errors and saturation | link to the dashboard |
| 2 | Paging alerts fire on symptoms, by burn rate; slow burns open a ticket | alert rules in the repository |
| 3 | On-call rotation and escalation named; a runbook for every paging alert | rotation, runbook links |
| 4 | Load test at twice the expected peak **with the production configuration** | report; the configuration diff is empty (added after incident 2026-10-14) |
| 5 | Rollback tested in staging for this release, under five minutes | timed run |
| 6 | Database changes backward compatible: the old version runs on the new schema | migration reviewed |
| 7 | Backups taken and a restore tested in the last 90 days | restore log |
| 8 | Each dependency's failure behaviour known (a payment-provider timeout holds the booking, does not lose it) | test or design note |
| 9 | Logs carry request IDs and no personal data | sample log line |
| 10 | Risky features behind flags, off by default | flag list |

The tech lead and the on-call lead sign the completed checklist. An item that fails blocks the launch unless the head of engineering signs a dated exception with a deadline for the fix.

## 2. Choose the deployment strategy

**Decision point: canary or blue-green?** A **canary** sends a small share of traffic to the new version and compares it with the old; a staged rollout widens the share step by step {% cite beyer2018sreworkbook %}. **Blue-green** keeps two production environments as identical as possible: the new release is tested in the idle one (green), the router then sends all traffic to it, and rollback is switching the router back to blue {% cite fowler2010bluegreen %}. The criteria the team used:

| Criterion | Staged canary (1%, 10%, 50%, 100%) | Blue-green |
|---|---|---|
| Traffic to compare versions | 1% is about 42 requests an hour: only gross failures show | all or nothing, unless the router can weight traffic |
| Rollback | stop the rollout and shift the share back | one router switch, seconds |
| Cost | one environment plus canary instances | a second full environment; it doubles as staging for the next release |
| Database changes | both versions share the schema | both versions share the schema |

The team chose **blue-green with a weighted first step**: the router sends 10% to green for 30 minutes, then switches fully. At the average rate, the 10% step sees about 4,200 × 0.1 × 0.5 ≈ 210 requests, so a bug failing 20% of requests would show about 40 failures, an unmistakable signal. The decisive criteria were the one-step rollback and the load balancer's support for two weighted target groups. The known weakness is that 30 minutes at 10% load cannot reveal failures that appear only under full load.

## 3. Rollback preconditions

Rolling back first and fixing afterwards is the SRE default {% cite beyer2016sre %}, but it works only if these hold before the switch:

- **Blue stays untouched and running for 24 hours** after the full switch, with its own configuration.
- **The schema is expanded, not contracted:** new columns are added now and old ones dropped in a later release, so blue runs on the database green has written.
- **The on-call engineer may switch back without asking anyone,** at any hour.
- **The switch-back command was run in staging this release** and took under five minutes (PRR item 5).

## 4. Write the error budget policy

The policy holds the five elements of the workbook's example policy (SLI and SLO, window, what happens when the budget is spent, when a postmortem is required, who decides disputes), plus the budget arithmetic and the alert rules {% cite beyer2018sreworkbook %}. Burn rate is the error ratio divided by the 0.1% the SLO allows, so the alerts are written as ratios; the failure counts hold at average traffic:

```markdown
# Error budget policy: booking API                 (agreed 2026-09-01)

SLI       Successful requests / all requests, measured at the load balancer.
SLO       99.9% over a rolling 30-day window.
Budget    0.1% of requests: about 3,000 failed requests per 30 days.
Alerts    Page:   error ratio over the last hour >= 1.44% (burn rate 14.4).
                  At average traffic, 4,200 requests/hour, that is 60
                  failures, 2% of the budget, in one hour.
          Ticket: error ratio over the last 72 hours >= 0.1% (burn rate 1).
                  At average traffic, 300 failures, 10% of the budget.
Budget spent
          Feature launches stop. Only fixes for reliability and security
          ship until the 30-day SLI is back above 99.9%.
Postmortem
          Required for any incident that consumes more than 20% of the
          budget (600 failed requests), and for any data loss.
Disputes  Decided by the head of engineering. The product manager, the
          tech lead and the on-call lead sign this policy.
```

## 5. Release 3.4 and the incident

At 14:00 green took 10% of traffic and served about 210 requests in 30 minutes without a failure (at blue's 0.05%, about 0.1 failures were expected); at 14:30 the router switched fully. At 18:50, as traffic reached its evening peak, 30% of requests began to time out.

Incident management follows a fixed set of roles so that the person fixing the system is not also answering the phone {% cite beyer2016sre %}: the **incident commander** owns the incident, assigns roles and decides; the **operations lead** is the only one changing production; the **communications lead** updates the status page, customer support and management. The timeline:

| Time | Event |
|---|---|
| 18:50 | Errors start: 30% of 9,000 requests an hour, 45 failures a minute |
| 18:53 | The burn-rate alert pages the on-call engineer: the one-hour error ratio passes 1.44% (45 × 3 = 135 failures of about 9,000 requests an hour) |
| 18:55 | Ana, on call, acknowledges and sees customer-visible errors |
| 18:58 | Ana **declares an incident** and becomes incident commander; Ben operations lead, Chen communications lead |
| 19:01 | Ben finds one change in the last 24 hours: release 3.4 |
| 19:04 | Ben switches the router back to blue |
| 19:06 | Error rate back at 0.05%; incident mitigated |
| 20:30 | Root cause found in a configuration diff; incident closed |

Each step was a decision point with a criterion:

- **Declare?** The team declares when users see errors above the paging threshold, or when a second person is needed. Both held at 18:58. Declaring early costs a few minutes of coordination; declaring late leaves one engineer debugging while support phones ring.
- **Roll back or fix forward?** Roll back when a recent change correlates with the onset and the preconditions of section 3 hold. All did, so Ben rolled back without diagnosing; diagnosis waited until users were no longer affected.
- **Postmortem required?** 16 minutes × 45 failures = 720 failed requests, 720 / 3,000 = 24% of the budget, above the policy's 20%. Yes.
- **Freeze launches?** The window had spent 900 failures before the incident; 900 + 720 = 1,620 of 3,000, 54%. The budget is not spent, so launches continue, but the postmortem's actions go first.

During the incident the burn rate was 30% / 0.1% = 300. At average traffic that would spend the budget in 720 h / 300 = 2.4 hours; at the peak's 2,700 failures an hour, the 3,000-failure budget would have lasted 3,000 / 2,700 ≈ 1.1 hours.

## 6. The blameless postmortem

The postmortem describes how the system allowed the failure, not who caused it; it is shared openly and every action has an owner and a ticket {% cite beyer2016sre %}. The filled template, the artifact to copy:

```markdown
# Postmortem: booking API timeouts after release 3.4    (2026-10-14)
Status: actions in progress    Authors: Ana, Ben    Blameless: yes

Summary   For 16 minutes at the evening peak, 30% of booking requests timed
          out. Rolled back to the previous release; no booking data lost.
Impact    720 failed requests, 24% of the 30-day error budget.
          About 300 guests (distinct sessions among the failed requests)
          saw an error; most retried successfully.
Trigger   Evening peak traffic (9,000 requests/hour) on release 3.4.
Root causes
          1. Green was built from the configuration template, which sets a
             database pool of 10 connections. Blue had been tuned to 40 by
             hand a year ago; the change never reached the template.
          2. The PRR load test ran in staging with staging configuration,
             so the pool limit was never exercised.
          3. Pool utilization was on no dashboard and raised no alert.
Detection Burn-rate alert paged 3 minutes after onset.
Resolution
          Router switched back to blue at 19:04; recovered by 19:06.
What went well
          The alert fired fast; roles were assigned within 8 minutes of
          onset; service recovered 2 minutes after the switch because
          blue was kept running.
What went badly
          The 10% step could not show a failure that needs full load.
          Nobody knew the two environments' configurations differed.
Where we got lucky
          Failed bookings failed before commit, so nothing was half written.
Action items
  Type     Action                                          Owner  Ticket
  prevent  Generate both environments from one versioned   Ben    OPS-512
           configuration; an empty config diff becomes a
           release gate
  prevent  PRR load test at 2x peak with production        Dana   OPS-513
           configuration (PRR item 4)
  detect   Dashboard panel and ticket alert at 80%         Chen   OPS-514
           database pool utilization
Timeline  See the incident log, 18:50-20:30.
```

Root cause 1 is not "Ben forgot to copy a setting". A process that keeps two environments' settings in someone's memory will lose one of them sooner or later; the first action removes that possibility for everyone.

## Common mistakes

1. **Fixing forward under pressure.** Debugging while users fail spends the budget at the incident's burn rate. Spot it: a timeline where the first action is a code change, not a rollback.
2. **A rollback that cannot run.** A migration dropped a column the old version needs. Spot it: a migration that removes or renames something in the same release that stops using it.
3. **The incident commander also debugging.** Nobody coordinates, nobody informs support. Spot it: one name in every row of the timeline.
4. **A postmortem that names a person.** People then hide the next mistake. Spot it: a root cause whose subject is a person rather than a process, tool or configuration.
5. **Action items without owners.** The same incident happens again. Spot it: actions with no owner or ticket, or tickets still open at the next incident.

## How solid is this?

- **Where it comes from.** The PRR, incident roles, rollback-first practice and blameless postmortems from Google's SRE book; the error budget policy and canary arithmetic from the workbook; blue-green deployment from Fowler's practitioner article {% cite beyer2016sre beyer2018sreworkbook fowler2010bluegreen %}.
- **What is contested.** Whether a small team needs separate incident roles; with three people on hand, the incident commander role still matters most.
- **What we do not hold.** The books' full text; their content reaches this handbook page through the area's notes, and the PRR checklist is a synthesis, not Google's list.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
