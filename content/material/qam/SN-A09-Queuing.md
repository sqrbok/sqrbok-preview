---
title: "SN A09: Queuing Theory"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 17
redirect_from: /content/attributes/queuing/SN-A09-Queuing.html
layout: default
page_type: study-notes
---

# SN A09: Queuing Theory

The lecture asks how to predict response time, utilization and capacity from what can be measured on a running system, without building and load-testing every alternative. It moves from why queues form, through modelling and measuring them, to five operational laws, response time under load, and a workflow for capacity decisions. [RQ A09: Queuing Theory](RQ-A09-Queuing.md) tests this lecture. A note on symbols: *S* is a service time here (in the performance lecture it is a speedup), and *c* is the number of servers, written *m* only in the model name M/G/m/m+r.

## 1. Queues come from variability, not only from overload

Infinite capacity is infinitely expensive and spare capacity is expensive, so resources are shared and requests sometimes wait. If requests arrived exactly once a minute and each took exactly one minute, nobody would wait. If both times vary, for example between 0.5 and 1.5 minutes with the same one-minute averages, short gaps meet long services and a queue builds even though the server keeps up on average {% cite dobson2012queuing %}. Queuing theory predicts waiting, utilization and the bottleneck without the experiment.

→ [Queuing Theory](../../attributes/queuing/index.md)

## 2. A queue is described by λ, μ, ρ and c

The **arrival rate** λ is requests per second; the **service rate** μ is completions per second of one server; *c* is the number of servers; the **utilization** ρ = λ / (*c* μ) is the fraction of time each server is busy, the same quantity the operational laws below call *U*; and the mean service time is *S* = 1/μ. At ρ = 0.1 almost nobody waits; at ρ = 0.9 long queues form; as ρ approaches 1 the queue grows without bound. Configurations range from one queue and one server to several prioritized queues and several servers. In an **open** system requests arrive from outside regardless of the system's speed; in a **closed** system a fixed number of users circulate, each waiting for a reply and then thinking for *Z* seconds before the next request.

→ [Queuing Models](../../attributes/queuing/models.md)

## 3. Kendall notation names the model

Kendall's notation *A*/*S*/*c* gives the inter-arrival distribution, the service distribution and the number of servers, with M for exponential (memoryless), D for deterministic and G for general {% cite kendall1953stochastic %}. Later positions add the system capacity *K*, the population and the discipline. Typical software cases: M/M/1 is a single-threaded handler; M/M/c a thread pool of *c* workers; M/D/1 a fixed-time batch step; M/G/m/m+r a pool of *m* cloud servers with a buffer of *r* {% cite khazaei2012cloud %}. The method follows the data: with measurements, use **operational analysis**; for a closed network with known service demands, use **mean-value analysis**; with priorities, finite buffers or complex routing, **simulate**.

→ [Queuing Theory](../../attributes/queuing/index.md)

## 4. Operational analysis replaces assumptions with a window and four counters

Classical queuing theory assumes distributions such as Poisson arrivals; operational analysis uses only what an observation window records {% cite denning1978operational %}: the window length *T*, arrivals *A*, completions *C*, the busy time *B* of a resource, and *W*, the total time all requests spent in the system. Everything else is derived: λ = *A*/*T*, *X* = *C*/*T*, *U* = *B*/*T*, *S* = *B*/*C*, *N* = *W*/*T* and the mean response time *R* = *W*/*C*. For example, an API server observed for *T* = 60 s was busy for *B* = 36 s and completed *C* = 1,800 requests: *S* = 20 ms, *U* = 60% and *X* = 30 req/s.

The laws need three testable assumptions: **flow balance** (*A* ≈ *C*, broken if a server crashes mid-window), **one-step behavior** (no simultaneous events, broken by a batch of 50 submissions arriving at once), and **homogeneity** (rates that do not depend on the system's state, broken by a database that slows down as its cache thrashes). Choose a window many times longer than a typical busy period.

→ [Operational Laws](../../attributes/queuing/operational-laws.md)

## 5. Five laws connect all the metrics

- **Little's law**, *N* = *X* · *R*, holds on average over any window in which requests are neither created nor lost inside the system, whatever the servers, arrival pattern, service times or discipline {% cite little1961proof %}. At *X* = 0.45 req/s and *R* = 3 s, about 1.35 requests are in the system. If measured *N*, *X* and *R* disagree, the measurements are wrong {% cite little2011littles %}: typically they were taken over different windows or at different system boundaries, during start-up rather than a steady state, or with requests dropped or retried.
- The **utilization law**, *U* = *X* · *D*, uses the **service demand** *D*, the total service a request needs from a resource; with *c* identical servers sharing the work, each does 1/*c* of it, so the per-server utilization is *X* · *D* / *c*.
- The **service demand law**, *D* = *V* · *S*, multiplies the visits per request by the service time per visit: four database queries of 8 ms each give *D* = 32 ms; with one visit, *D* = *S*.
- The **forced flow law**, *X*<sub>k</sub> = *V*<sub>k</sub> · *X*, says that resource *k* sees *V*<sub>k</sub> times the system throughput: four queries per request at 0.45 req/s make 1.8 queries/s.
- The **interactive response time law**, *R* = *M*/*X* − *Z*, holds for closed systems with *M* users in total and think time *Z*; rearranged, *X* = *M* / (*R* + *Z*). *M* counts all users; Little's *N* only the requests in the system.

Service demand is the key parameter: since no utilization can exceed 1, throughput can never exceed 1 / *D*<sub>max</sub>. A web tier with 5 ms, an application tier with 15 ms and a database with 25 ms of demand cannot pass 40 req/s, whatever is done to the first two {% cite denning1978operational %}.

→ [Operational Laws](../../attributes/queuing/operational-laws.md)

## 6. Response time explodes as utilization approaches 100%

Response time is waiting plus service. For an M/M/1 queue, *R* = *S* / (1 − ρ): twice the service time at 50% utilization, 4 times at 75%, 10 times at 90%, 100 times at 99%. The same API server (*S* = 20 ms, ρ = 60%) answers in 50 ms on average with *N* = 1.5 requests in the system; if load grows to ρ = 90%, it answers in 200 ms. Hence the rule of keeping utilization below 70–80%. When a request passes several tiers in turn, treat each as an M/M/1 queue: with *U*<sub>k</sub> = *X* · *D*<sub>k</sub>, the time spent at tier *k* (its **residence time**) is *R*<sub>k</sub> = *D*<sub>k</sub> / (1 − *U*<sub>k</sub>), and the response time is the sum of the residence times.

The mean also hides a tail: in M/M/1 the response time is exponentially distributed, so the p95 is −ln 0.05 ≈ 3 times the mean. In systems that fan a request out to many servers, the tail compounds: with 100 servers each slow 1% of the time, 63% of requests are slow {% cite dean2013tail %}. Report p90 or p99, not only the mean. To set each service's target, invert the formula: for 99% of requests through 50 services to be fast, (1 − *p*)<sup>50</sup> ≥ 0.99, so each service may be slow on at most *p* = 1 − 0.99<sup>1/50</sup> ≈ 0.0002 of its requests, about 1 in 5,000.

→ [Queuing Models](../../attributes/queuing/models.md)

## 7. Size an interactive system with the laws, then check the response time

To find how many users one server carries, combine the utilization law with the interactive response time law, and check that the response time at the target utilization meets its own target. Example: demand *D* = 0.4 s, think time *Z* = 30 s, target *U* ≤ 75%. Throughput at the target is *X* = 0.75 / 0.4 = 1.875 req/s; the M/M/1 estimate of response time there is 0.4 / 0.25 = 1.6 s; so *M* = 1.875 × (1.6 + 30) ≈ 59 users. The check matters: a target of *R* ≤ 1 s would not be met at 75% utilization, and the utilization target would have to drop until *S*/(1 − ρ) ≤ 1 s.

**Mean-value analysis** (MVA) computes the same closed system exactly, one user at a time. With *Q*<sub>k</sub>(*n*) the mean number of requests at resource *k* when *n* users are in the system (*n* = 1, …, *M*), starting from *Q*<sub>k</sub>(0) = 0: the residence time at each resource is *R*<sub>k</sub>(*n*) = *D*<sub>k</sub>(1 + *Q*<sub>k</sub>(*n* − 1)), the throughput is *X*(*n*) = *n* / (*Z* + Σ*R*<sub>k</sub>), and *Q*<sub>k</sub>(*n*) = *X*(*n*) · *R*<sub>k</sub>(*n*) {% cite reiser1980mva %}. By hand for a two-resource system (an application server with *D*<sub>1</sub> = 0.2 s, a database with *D*<sub>2</sub> = 0.4 s, *Z* = 1 s): with one user, *R*<sub>1</sub> = 0.2, *R*<sub>2</sub> = 0.4, *X* = 1 / (1 + 0.6) = 0.625 req/s, so *Q*<sub>1</sub> = 0.125 and *Q*<sub>2</sub> = 0.25; with two users, *R*<sub>1</sub> = 0.2 × 1.125 = 0.225, *R*<sub>2</sub> = 0.4 × 1.25 = 0.5, and *X* = 2 / (1 + 0.725) ≈ 1.16 req/s. For the one-server example above, 59 iterations (run by a short script or a tool such as JMT, not by hand) give 1.40 s at 59 users, so the M/M/1 estimate of 1.6 s is pessimistic. The bounds of the operational laws locate the **knee** without any recursion: with *D* = Σ*D*<sub>k</sub> the total demand of one request over all resources and *D*<sub>max</sub> the largest, the knee lies at *M*\* = (*D* + *Z*) / *D*<sub>max</sub> users, where the throughput ceiling 1/*D*<sub>max</sub> is reached; beyond it each extra user mainly adds waiting. For the one-server example, *M*\* = 30.4 / 0.4 = 76 users; for the two-resource system, *D* = 0.6 s and *M*\* = (0.6 + 1) / 0.4 = 4 users.

→ [Capacity Planning](../../attributes/queuing/applications.md) · [Queuing Networks and MVA](../../attributes/queuing/networks.md)

## 8. Each added server helps less; one shared queue beats many

With *c* servers sharing one queue, a request waits only when all *c* are busy, which grows rarer as *c* rises. With the offered load *a* = λ/μ = *c*ρ (the average number of busy servers), the **Erlang C formula** gives the probability of waiting as the weight of the "all busy" states over the weight of all states: *P*<sub>wait</sub> = *E*<sub>c</sub> / (Σ<sub>k=0..c−1</sub> *a*<sup>k</sup>/*k*! + *E*<sub>c</sub>) with *E*<sub>c</sub> = *a*<sup>c</sup> / (*c*! (1 − ρ)), and then the mean wait *W*<sub>q</sub> = *P*<sub>wait</sub> · *S* / (*c*(1 − ρ)) and *R* = *S* + *W*<sub>q</sub> {% cite sharma2007queuing %}. For four servers at ρ = 0.75, *a* = 3: the sum is 1 + 3 + 4.5 + 4.5 = 13 and *E*<sub>c</sub> = 81 / (24 × 0.25) = 13.5, so *P*<sub>wait</sub> = 13.5 / 26.5 ≈ 0.51 and *W*<sub>q</sub> = 0.51 *S* / (4 × 0.25) ≈ 0.51 *S*; at the same per-server utilization the system carries *c* times the load and each request waits less. At 75% per server the mean wait is 3.0 *S* with one server, 1.29 *S* with two, 0.76 *S* with three and 0.51 *S* with four: the second server cuts waiting by more than half, the fourth by a third. Four servers sharing one queue also beat four separate queues, which would each wait 3.0 *S*: with separate queues a request can wait in one queue while another server sits idle. Every bounded resource in software is such a queue: a thread pool is M/M/c, a database connection pool without a waiting room is M/M/c/c, and an auto-scaled service adjusts *m* in M/G/m/m+r. When any queue reaches the steep part of its curve, the whole system slows.

→ [Queuing Models](../../attributes/queuing/models.md) · [Capacity Planning](../../attributes/queuing/applications.md)

## 9. Simulate when the model gets complex; iterate in any case

Priorities, finite buffers, batching and complex routing defeat closed-form models; simulate the network instead, for example with JMT, and validate against a simplified analytic case {% cite jmt2023 %}. The performance-engineering workflow is a loop: build or prototype, measure the counters, derive the metrics, model, predict what-ifs, validate the prediction against a new measurement, and refine.

→ [Capacity Planning](../../attributes/queuing/applications.md)

## Where this goes next

The next lecture turns to maintainability, measured on the structure of the code rather than on a running system. The bachelor lecture on performance analysis covers the testing side of this material: scalability laws, test types and load testing with Locust ([SN L09: Performance Analysis](../sqr/SN_L09_PerformanceAnalysis.md)).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
