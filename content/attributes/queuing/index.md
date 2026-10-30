---
title: Queuing Theory
parent: Quality Attributes
has_children: true
nav_order: 4
layout: default
page_type: topic-hub
---

# Queuing Theory

**Queuing theory** models a system as requests that arrive, wait for a busy resource, are served and leave, and predicts waiting time, queue length and utilization from the arrival and service rates. Every thread pool, disk and message broker is a queue {% cite shortle2018fundamentals %}.

Queues form because of **variability**, not only overload. If requests arrive exactly once a minute and each takes exactly one minute, nobody waits; if both times vary around those averages, a queue builds, although the server keeps up on average {% cite dobson2012queuing %}. A queue is described by the arrival rate λ, the service rate μ of one server, the number of servers *c*, and the **utilization** ρ = λ / (*c* μ).

**Kendall notation** names a queue as *A*/*S*/*c*: the inter-arrival distribution, the service-time distribution and the number of servers, where M means exponential (memoryless), D deterministic and G general {% cite kendall1953stochastic %}. Later authors added the capacity *K*, population and discipline: M/M/4/104 holds at most 104 requests.

## Symbols

The pages use one set of symbols.

| Symbol | Meaning |
|---|---|
| λ, *X* | arrival rate and throughput; equal when no requests are lost |
| *S* = 1/μ | mean service time per visit, measured as *B*/*C* (busy time over completions, see [Operational Laws](operational-laws.md)) |
| *D* = *V* · *S* | service demand: total service per request at one resource (*D* = *S* with one visit) |
| *U* = ρ | utilization, the fraction of time a server is busy |
| *c* | number of servers (written *m* in M/G/m/m+r, see [Queuing Models](models.md)) |
| *R*, *R*<sub>k</sub> | response time of the whole system; residence time at resource *k* |
| *W*<sub>q</sub>, *W* | mean wait in the queue before service; total time all requests spent in the system in a window |
| *N*, *M*, *Z* | requests in the system; users; think time |

Letters reused elsewhere: M and D in Kendall names; *S* (speedup), *N* (nodes, leaves) and *p* (a fraction) on the performance pages; *W* in Little's original L = λW is the mean time per request; *E*<sub>c</sub> is a term of the Erlang C formula.

## The pages at a glance

Each page adds one step.

| Page | What it gives you | Needs | Main limit |
|---|---|---|---|
| [Operational Laws](operational-laws.md) | throughput, utilization, bottleneck from measured counters | a window and four counters | averages only |
| [Queuing Models](models.md) | response time as utilization rises (the hockey stick) | arrival and service rates | assumed distributions |
| [Queuing Networks and MVA](networks.md) | response time and throughput of a multi-tier system for M users | service demand per resource | product-form assumptions |
| [Capacity Planning](applications.md) | how many servers, and when to add them | the above plus a growth forecast | the forecast |

## Reading order

Read the pages in table order, then the [worked case](worked-case.md). [Performance Measurement](../performance/measurement.md) covers their metrics.

## How solid is this?

- **Where it comes from.** Erlang's telephone work {% cite erlang1909theory %}, Kendall {% cite kendall1953stochastic %} and Little {% cite little1961proof %}.
- **What is assumed.** The single-queue formulas assume distributions; the operational laws do not.

---

### References

{% bibliography --cited %}

### Acknowledgments

The lectures behind this area took early inspiration from the *Analysis of Software Artifacts* course of Eduardo Miranda and Claire Le Goues (Carnegie Mellon University). The pages here are original, written from the literature review; any errors are ours.

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
