---
title: "SN L09: Performance Analysis"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 17
redirect_from: /content/attributes/performance/SN_L09_PerformanceAnalysis.html
layout: default
page_type: study-notes
vegalite: true
---

# SN L09: Performance Analysis

The lecture asks how to predict, measure and manage how fast a system responds under load. It moves from metrics through scaling limits and causes of slowness to testing and tail latency. [RQ L09: Performance Analysis](RQ_L09_PerformanceAnalysis.md) tests this lecture. A note on symbols: in this lecture *S* is a speedup, *f* Amdahl's serial fraction and *s* Gustafson's; in the queuing lecture *S* is a service time.

## 1. Performance is a matter of degree, and it costs money

**Performance** is how well a system meets its timeliness objectives: response time, throughput and resource use {% cite jain1991art %}. The business effect is measurable but smaller than folklore says: Google's experiments that added 100 to 400 ms of delay to search pages reduced searches per user by 0.2% to 0.6% {% cite brutlag2009speed %}. Widely quoted figures such as "100 ms costs 1% of sales" have no published study behind them. Google's figure also cannot simply be scaled to another business: it measures searches, not purchases, for one service's users, and the effect depends on where in the page the delay falls.

→ [Performance](../../attributes/performance/index.md)

## 2. Little's law links concurrency, throughput and response time

Three metrics describe a running system: **throughput** *X* (completions per second), **response time** *R* (time per request, waiting included) and **concurrency** *N* (requests in the system). **Little's law**, *N* = *X* · *R*, holds on average over any window in which requests are neither created nor lost inside the system, whatever its arrival pattern, servers or queue discipline {% cite little1961proof %}. Knowing two gives the third. For example, a load balancer with 100 open connections and a mean response time of 0.2 s is serving 100 / 0.2 = 500 requests/s.

→ [Performance Measurement](../../attributes/performance/measurement.md)

## 3. Rates are averaged with the harmonic mean

Two runs process the same work at 30 and 20 transactions per hour. Their average rate is not 25: the slower run lasts longer, so it counts more. With the same work in each run the mean rate is 2 / (1/30 + 1/20) = 24, the harmonic mean. The arithmetic mean is right only if each run lasted the same *time*.

→ [Performance Measurement](../../attributes/performance/measurement.md)

## 4. Percentiles describe users; means hide the slow ones

Response times have long right tails. Two services with the same 1.0 s mean can differ completely: in one every request takes 0.8–1.2 s; in the other 90% take 0.5 s and 10% take 5.5 s. The **p95** (the time 95% of requests stay under) separates them: about 1.2 s against 5.5 s. A percentile p*k* reveals a slow group only if that group is more than (100 − *k*)% of requests: the p90 of the second service is still 0.5 s, because its slow group is exactly 10%. That is why targets use p95 or p99. Changes under 10% may be noise {% cite wilson2008operations %}.

→ [Performance Measurement](../../attributes/performance/measurement.md)

## 5. Under rising load, throughput flattens and response time climbs

Plotted against utilization, response time stays nearly flat and then bends sharply upwards; this **hockey stick** curve arises because near full utilization almost every request finds the resource busy and waits (details on the master's queuing track). As users are added, utilization and throughput first rise with load (**light load**). When the busiest resource saturates, throughput plateaus and response time starts climbing (**heavy load**). Overload can then cause thrashing: throughput falls while response time explodes (the **buckle zone**) {% cite haines2006pro %}. A faster resource moves the bend to higher load; more arrivals only move the system along the same curve. [Performance Measurement](../../attributes/performance/measurement.md) plots throughput and response time against the number of users for a worked example.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 440, "height": 200,
  "layer": [
    {"data": {"values": [{"a":0,"b":5,"z":"light load"},{"a":5,"b":8,"z":"heavy load"},{"a":8,"b":10,"z":"buckle zone"}]},
     "mark": {"type": "rect", "opacity": 0.12},
     "encoding": {"x": {"field": "a", "type": "quantitative", "title": "load (number of users)", "scale": {"domain": [0, 10]}, "axis": {"labels": false, "ticks": false}},
                  "x2": {"field": "b"},
                  "color": {"field": "z", "type": "nominal", "title": null, "scale": {"domain": ["light load","heavy load","buckle zone"], "range": ["#019546","#c99700","#b42318"]}, "legend": {"orient": "top"}}}},
    {"data": {"values": [{"x":0,"y":0},{"x":5,"y":5.5},{"x":6,"y":6},{"x":8,"y":6},{"x":10,"y":3.5}]},
     "mark": {"type": "line", "strokeWidth": 3, "color": "#2D6E2A", "interpolate": "monotone"},
     "encoding": {"x": {"field": "x", "type": "quantitative"}, "y": {"field": "y", "type": "quantitative", "title": "relative value (schematic)", "scale": {"domain": [0, 10]}, "axis": {"labels": false, "ticks": false}}}},
    {"data": {"values": [{"x":0,"y":1},{"x":5,"y":1.4},{"x":6.5,"y":2.5},{"x":8,"y":4.5},{"x":9,"y":7},{"x":10,"y":10}]},
     "mark": {"type": "line", "strokeWidth": 3, "color": "#b42318", "strokeDash": [6,3], "interpolate": "monotone"},
     "encoding": {"x": {"field": "x", "type": "quantitative"}, "y": {"field": "y", "type": "quantitative"}}},
    {"data": {"values": [{"x":3.2,"y":4.4,"t":"throughput X"},{"x":7.6,"y":3.3,"t":"response time R (dashed)"}]},
     "mark": {"type": "text", "fontSize": 12, "align": "left"},
     "encoding": {"x": {"field": "x", "type": "quantitative"}, "y": {"field": "y", "type": "quantitative"}, "text": {"field": "t"}}}
  ],
  "config": {"font": "Tahoma, sans-serif", "view": {"stroke": null}}
}
```

_A schematic of the three zones against load; utilization rises with load until the busiest resource saturates in the heavy-load zone: throughput rises, plateaus and, under thrashing, falls; response time stays flat, climbs, then explodes. Shapes only, no measured data._

→ [Performance Measurement](../../attributes/performance/measurement.md)

## 6. Amdahl: the part you do not improve sets the ceiling

If a fraction *p* of the time is sped up by a factor *q*, the overall speedup is *S* = 1 / ((1 − *p*) + *p*/*q*); if several parts with shares *p*<sub>i</sub> are sped up by *q*<sub>i</sub>, *S* = 1 / ((1 − Σ*p*<sub>i</sub>) + Σ*p*<sub>i</sub>/*q*<sub>i</sub>). In the lecture's example, an algorithm made 1.35 times as fast (*q* = 1.35) covers 60% of the time: *S* = 1 / (0.4 + 0.6/1.35) ≈ 1.18, only an 18% gain. With *N* processors and a **serial fraction** *f* (the share of the time that cannot run in parallel), *S* = 1 / (*f* + (1 − *f*)/*N*), which can never exceed 1/*f*: 10% serial work caps it at 10. So measure a part's share of the time before optimizing it.

→ [Scalability Laws](../../attributes/performance/scalability.md)

## 7. Gustafson: scale the problem, not the time

Amdahl assumes a fixed problem. Gustafson observed that users of a bigger machine run bigger problems in the same time, so the parallel part grows with *N*. The scaled speedup is *S* = *N* − (*N* − 1) · *s*, where *s* is the serial share measured on the parallel machine (not Amdahl's *f*, which is measured on one processor). At Sandia, 1,024 processors with *s* between 0.4% and 0.8% gave measured scaled speedups of 1,016 to 1,021; the formula gives 1,016 to 1,020 over that range, close to the measurements {% cite gustafson1988reevaluating %}. Use Amdahl for a fixed task, Gustafson when capacity buys a bigger task.

→ [Scalability Laws](../../attributes/performance/scalability.md)

## 8. The USL explains why adding servers can make things worse

Gunther's **Universal Scalability Law** gives the relative capacity of *N* nodes as *C*(*N*) = *N* / (1 + α(*N* − 1) + β*N*(*N* − 1)). Here α is **contention**, waiting for something shared such as a database lock, and β is **coherence**, the cost of keeping nodes consistent, which grows with every pair of nodes {% cite gunther2002guerrilla %}. With β = 0 it is Amdahl's law, and α equals the serial fraction *f*. With β > 0, capacity peaks and then falls (retrograde throughput): beyond the peak, every added node costs more coordination than it contributes. Fit α and β to relative capacity *C*(*N*) = *X*(*N*)/*X*(1) measured at four or more sizes. β is reduced architecturally: shard data so that nodes share less, use caches that need no cross-node invalidation, and avoid cluster-wide locks or consensus on the request path. Amdahl suits single-system optimization, Gustafson growing batch workloads, the USL clusters and distributed services.

→ [Scalability Laws](../../attributes/performance/scalability.md)

## 9. Performance is a design property; manage it with a budget

A **performance budget** allocates response time (not money) to each component and tracks actual against budget during development; Jewell puts dedicated performance engineering at 1–5% of project cost {% cite jewell2008performance %}. It is cheaper than relying on load tests alone because it detects a component that overruns its share while the design can still change; a load test finds the same problem only after the system is built, when the fix may be a redesign. Many problems are **anti-patterns**, designs that tuning cannot fix {% cite smith2000antipatterns %}:

- A **God Class** does all the work itself and pulls the data it needs from other objects. Symptom: many messages that only fetch other objects' data. Remedy: move the logic next to the data. For example, a controller that asks each of 20 cart items for its price, discount and stock makes 60 calls; if the cart computes its own total, the controller makes 1 call, `cart.total()`.
- A **Circuitous Treasure Hunt** follows chains of lookups to reach data. Symptom: a request count that grows with the data; one model drawing needed 6,001 database calls. Today this is the N + 1 query problem. Remedy: one query with a join, or a batch.
- A **One-Lane Bridge** forces all traffic through one narrow point, such as a single lock, a single-threaded step or an unsharded database. Symptom: requests queue at that point while other resources sit idle. Remedy: partition (shard) the resource or shorten the time it is held.
- **Excessive Dynamic Allocation** creates and destroys objects on every call. Symptom: allocation and garbage-collection time that grows with load, seen as periodic pauses. Remedy: reuse objects through a pool.

Not every response time has the same value: for trading, value drops off a cliff after a deadline; for a nightly report it hardly matters.

→ [Performance Bugs and Anti-Patterns](../../attributes/performance/bugs.md)

## 10. Performance bugs are found by reading code, and fixed late

In 109 performance bugs from five large open-source projects, two thirds came from misunderstanding the workload or an API, and the median fix was 8 lines {% cite jin2012understanding %}. Between 33.9% and 57.3% were discovered by reasoning about code, and only 5.5–10.4% with a profiler {% cite nistor2013discovering %}. The study does not say why; two reasons are likely: a profiler shows only the slowdowns of the input it runs, and most of these bugs sit in loops whose cost depends on the input; and a slow but correct result raises no alarm in a test, so nothing prompts anyone to profile. Lesson: review code for work that grows with the input, not only for correctness.

→ [Performance Bugs and Anti-Patterns](../../attributes/performance/bugs.md)

## 11. Testing needs six test types and a production-like setup

Molyneaux estimates that moving from release-time validation to performance-driven development cuts performance defects escaping to production from about 30% to 5% {% cite molyneaux2014art %}. Six test types answer six questions: **baseline** (one user, the best case), **load** (the expected peak: are targets met?), **stress** (beyond the peak: where does it break?), **soak** (normal load for hours: leaks?), **smoke** (changed code on each build) and **isolation** (one transaction, repeated, to diagnose it).

A usual order is baseline first (the yardstick), smoke on every build, then load, then stress, then soak, with isolation tests whenever a slow transaction needs diagnosing. A **spike test**, a stress test in which load jumps suddenly rather than rising gradually, belongs after the load test for events such as a ticket sale opening. Load comes from a tool such as Locust, where each simulated user is a short Python class that requests pages and waits a think time; it reports throughput and percentiles per request type.

Results transfer only if hardware, configuration, data volumes and the workload (transaction mix, think times, active users) match production. Each run ramps up, measures at the peak, and ramps down; many systems fail during ramp-up {% cite everett2007performance %}. A response time that keeps rising at constant load suggests a leak; an erratic one, contention.

→ [Performance Testing](../../attributes/performance/testing.md) · [Load Testing with Locust](../../attributes/performance/load-testing.md)

## 12. In distributed systems the tail dominates

When a request fans out to *N* servers and waits for all of them, and each is slow with probability *p*, the request is slow with probability 1 − (1 − *p*)<sup>*N*</sup>: with 100 servers each slow 1% of the time, 63% of requests are slow {% cite dean2013tail %}. Dean and Barroso mask the tail with redundancy: **hedged requests** send a second copy if the first is late, which in one benchmark cut the 99.9th percentile from 1,800 ms to 74 ms for 2% more requests. Sending the copy only after the 95th-percentile expected latency bounds the extra load at about 5%, since only about 5% of requests get that far; the benchmark measured 2%. In a service architecture the performance budget becomes a **per-service SLO**: the end-to-end target is split into budgets, for example p99 of 50, 250 and 100 ms for a 400 ms chain: start from each service's measured p99 and share out the remaining headroom. Such a split is the usual allocation: the chain's p99 is normally below the sum of the parts' p99s, because one request rarely hits the slow 1% of every service at once, unless their slowness is correlated (a shared database, a network fault). The classical practices map onto cloud-native ones: the performance budget per component becomes the **per-service SLO**; tracking the budget during development becomes **performance tests in the CI pipeline** that fail a build on a regression; accepting some degradation becomes an **error budget** (and graceful degradation, such as good-enough results); and queuing models become **capacity models behind autoscaling**. The tools change, but not the physics: each service is still a set of queues, and Little's law and the hockey stick apply to each of them.

→ [Tail Latency at Scale](../../attributes/performance/cloud-bridge.md)

## Where this goes next

The queuing lecture for master's students explains *why* response time explodes near saturation, with the operational laws and queuing models. The performance lab applies this lecture: script a workload with Locust, run a load test against a web application, and read throughput and percentiles from the report; [Load Testing with Locust](../../attributes/performance/load-testing.md) shows how to read one and check it with Little's law.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
