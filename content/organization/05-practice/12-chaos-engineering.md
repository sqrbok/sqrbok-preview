---
title: Chaos Engineering
parent: Industry Practices
nav_order: 12
layout: default
---

# Chaos Engineering

**Chaos engineering** is the deliberate injection of controlled faults (killed instances, network delays, failed dependencies) into a production or production-like system to discover weaknesses in its resilience before real failures expose them {% cite owotogbe2024chaos %}. Netflix pioneered it in 2008 for its streaming infrastructure.

## 1. Testing the known, discovering the unknown

**Resilience testing** checks a property the team already expects, such as "the service survives the loss of one database replica". **Chaos engineering** asks an open question, "what happens if this fails?", and so finds weaknesses nobody had thought to test {% cite beyer2018sreworkbook %}. It complements, not replaces, the [fault-tolerance](../../attributes/reliability/fault-tolerance.md) mechanisms it probes.

## 2. The experiment lifecycle

A multivocal review of 96 sources (49 academic, 47 grey literature, 2016–2024) found six recurring activities {% cite owotogbe2024chaos %}:

1. **Establish the steady state:** measure normal behavior (for example request success rate and latency) and state a hypothesis about how the system will cope.
2. **Set up monitoring** that can show any deviation from it.
3. **Conduct the experiment:** inject the fault.
4. **Test and refine:** compare with the hypothesis, fix what broke, repeat.
5. **Grow the blast radius:** widen the experiment step by step.
6. **Expand to production,** with safeguards.

The safeguards matter as much as the faults: a **blast-radius limit** confines the fault to approved targets, automatic **abort conditions** stop the experiment when error rate or latency crosses a threshold, and a **rollback** returns the system to its steady state. The review classifies tools (Chaos Monkey, Chaos Mesh, LitmusChaos, Toxiproxy and others) by where they run (virtual machines, containers, serverless), how automated they are, and whether they target staging or production.

## 3. Example: one experiment

A team runs three replicas of a checkout service behind a load balancer and believes it tolerates the loss of one (a constructed example). *Steady state:* 99.9% of checkout requests succeed, median latency 200 ms. *Hypothesis:* killing one replica leaves both within normal range. *Blast radius:* one replica, in one region, for ten minutes, aborting if the success rate drops below 99.5%. *Result:* success stays high but latency doubles, because the two survivors' connection pools are too small for the extra load. Nobody had tested that; the fix (larger pools, autoscaling) goes in before a real outage finds the same weakness.

## When to use it — and when not

Use it when a system is distributed and already has monitoring, redundancy and rollback; the experiments check that these work. Do not start in production: begin in staging, keep the blast radius small, and run experiments when engineers are available to respond. A system without basic monitoring gains nothing from chaos experiments, because no one could see their effect.

## Limitations

- **It can cause the outage it looks for.** Cascading failures and unexpected interactions are the main technical risks the review names; cultural resistance, skills and cost are the main organizational ones {% cite owotogbe2024chaos %}.
- **It finds weaknesses, not their absence.** A passed experiment shows that one fault was handled once.

## How solid is this?

- **Where it comes from.** A multivocal literature review of chaos engineering and the SRE workbook {% cite owotogbe2024chaos beyer2018sreworkbook %}.
- **What is contested.** The review's taxonomy has not been externally validated, and its window starts in 2016, so early practice such as Netflix's original Chaos Monkey is only partly covered.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
