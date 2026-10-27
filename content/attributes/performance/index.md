---
title: Performance
parent: Quality Attributes
has_children: true
nav_order: 3
layout: default
page_type: topic-hub
---

# Performance

**Performance** is the degree to which a system meets its timeliness objectives: how fast it responds, how much work it completes per second, and how many resources it uses doing so {% cite jain1991art %}. Unlike functional correctness, it is a matter of degree: an answer in 0.5 s and one in 5 s are both correct, but their value differs {% cite jewell2008performance %}.

ISO/IEC 25010 calls the characteristic *performance efficiency* and splits it into three subcharacteristics: **time behavior** (response time, throughput), **resource utilization** (the CPU, memory and I/O used) and **capacity** (the largest load under which the system still meets its targets) {% cite iso25010_2023 %}.

## The pages at a glance

Each page answers one question a team asks about speed under load.

| Page | Question it answers | Main tool | Main cost |
|---|---|---|---|
| [Performance Measurement](measurement.md) | What do we measure, and how do we summarize it honestly? | Little's law, percentiles | Instrumentation |
| [Scalability Laws](scalability.md) | Will more processors or servers help, and by how much? | Amdahl, Gustafson, USL | Measured load points |
| [Performance Testing](testing.md) | How do we show the system meets its targets before release? | Six test types | A production-like environment |
| [Load Testing with Locust](load-testing.md) | How do we generate realistic load and read the result? | A scripted user model | Script upkeep |
| [Performance Bugs and Anti-Patterns](bugs.md) | Where do slowdowns come from, and why are they found late? | Empirical studies | Design reviews |
| [Tail Latency at Scale](cloud-bridge.md) | Why are distributed systems slow at the 99th percentile? | Fan-out arithmetic, hedging | Extra requests |
| [Queuing Theory](../queuing/index.md) (separate section) | Why does response time explode near full utilization, and how much capacity is needed? | Operational laws, queuing models | Measured service demands |

The last row is a section of its own, holding the models behind these pages.

## Reading order

Read the pages in table order. [Performance Measurement](measurement.md) comes first: every later page uses its three metrics and its rule that percentiles, not means, describe what users wait. [Queuing Theory](../queuing/index.md) can be read after [Scalability Laws](scalability.md) or at the end.

## How solid is this?

- **Where it comes from.** The definitions follow Jain's textbook and the ISO/IEC 25010:2023 model; each page names its own sources.
- **What is contested.** Popular figures such as "100 ms of latency costs 1% of sales" circulate without a published study behind them; these pages use only figures they can trace.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
