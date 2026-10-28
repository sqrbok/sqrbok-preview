---
title: "RQ A09: Queuing Theory"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 18
redirect_from: /content/attributes/queuing/RQ-A09-Queuing.html
layout: default
page_type: question-set
---

# RQ A09: Queuing Theory

**Format:** Explain in your own words and show your calculations. These questions test the lecture summarized in [SN A09: Queuing Theory](SN-A09-Queuing.md).

---

## Part 1: Why queues matter

### Q1: Variability

Two servers both receive on average one request per minute and serve on average one request per minute. In system A all times are exactly one minute; in system B they vary between 0.5 and 1.5 minutes. Which system forms a queue, and why?

### Q2: Find the queues

A food-delivery app takes orders, sends them to restaurants and assigns drivers. Name three queues in it and say for each whether it is better modelled as open or closed.

### Q3: Why not provision for the peak?

Explain why queues are an economic choice rather than a design failure. Give an example where sizing for the peak would waste most of the capacity.

---

## Part 2: Modelling

### Q4: Kendall notation

Write the Kendall notation for:

1. a single-threaded event loop with exponential inter-arrival and service times;
2. a toll booth with random arrivals and a fixed 15-second service time;
3. a call center with 8 agents serving one queue.

### Q5: Open or closed?

Twenty developers push commits; each push starts a 5-minute build; after seeing the result, a developer works about 30 minutes before pushing again. Is this an open or a closed system? What are *M* (the number of users) and *Z*?

### Q6: Choosing a method

For each case, choose operational analysis, mean-value analysis or simulation, and justify:

1. a production system with monitoring data;
2. a planned service with known service demands and a fixed pool of 500 app users;
3. a message broker with three priority levels, a finite buffer and batch consumers.

---

## Part 3: Measurement

### Q7: From counters to metrics

A cache server is observed for *T* = 120 s: *A* = *C* = 6,000 requests, busy time *B* = 48 s. Derive λ, *X*, *S* and *U*. Which metric can you not derive without *W*, and how could you estimate it?

### Q8: Mapping a dashboard to counters

An engineer reports: "45,000 completed requests in the last 15 minutes, p50 latency 120 ms, CPU at 65%." Map each statement to *T*, *A*, *C*, *W* or *B*. Why is the p50 not enough to compute *W*?

### Q9: Broken assumptions

Over 5 minutes you observe *A* = 10,000 and *C* = 9,200. Which assumption is broken, what could cause it, and how would you repair the analysis?

### Q10: Full pipeline

A search server observed for 120 s was busy 54 s and completed 2,700 requests. Derive *S*, *U* and *X*; estimate *R* with the M/M/1 formula; compute *N* with Little's law; and predict *R* if utilization rises to 90%.

---

## Part 4: The laws

### Q11: Little's law

1. A coffee shop serves 2 customers per minute, each staying 10 minutes on average. How many are inside?
2. Monitoring shows 4.5 requests in the system at 1.5 req/s. What is the mean response time?
3. You measure *X* = 200 req/s, *R* = 25 ms and *N* = 10. Are these consistent? If not, name two likely causes.

### Q12: Utilization law with replicas

A database has *D* = 40 ms per transaction at *X* = 20 transactions/s. What is its utilization? What is the utilization per replica with two replicas sharing the load, each with its own queue, and what does the M/M/1 formula say about response time in each case?

### Q13: Find the bottleneck

| Resource | Visits per request | Service time per visit |
|---|---|---|
| Nginx | 1 | 2 ms |
| Application | 1 | 12 ms |
| PostgreSQL | 5 | 6 ms |

Compute each service demand, name the bottleneck, and give the maximum throughput. Name two ways to raise it.

### Q14: Forced flow

At *X* = 10 transactions/s each transaction reads the cache 8 times and the database twice. What throughput does each resource see? A new cache layer cuts database visits from 4 to 1 per request in another system with *S* = 8 ms per query and *X* = 0.45 req/s: what happens to the database's service demand and utilization?

### Q15: Impossible utilization

At *X* = 50 transactions/s, *D*<sub>app</sub> = 15 ms and *D*<sub>db</sub> = 25 ms. Compute both utilizations. What does the result tell you, what is the real maximum throughput, and what happens to it if database service time doubles?

---

## Part 5: Response time under load

### Q16: The hockey stick

With *S* = 10 ms, compute *R* at 50%, 80%, 90% and 95% utilization. By how much does *R* grow from 80% to 95%?

### Q17: Utilization for a target

A target says *R* ≤ 100 ms, and *S* = 15 ms. What is the highest utilization that meets it under the M/M/1 model, and why would you plan for less?

### Q18: Tail amplification

A request fans out to 50 services, each slow (over 500 ms) on 2% of requests independently. What share of requests is slow? If at least 99% of requests must be fast, on what share of its requests may each service at most be slow?

### Q19: Sizing an interactive system

An online IDE has think time *Z* = 45 s and service demand *D* = 2 s per compile-and-run (one visit, so *D* = *S*), on one server.

1. At a utilization target of 70%, what throughput can the server sustain?
2. Estimate the response time at that utilization with the M/M/1 formula, and the number of developers from the interactive response time law.
3. The team also wants *R* ≤ 5 s. Is that met? If not, what utilization target meets it, and how many developers does that allow?

### Q20: Adding servers

At 75% utilization per server, the mean wait is 3.0 *S* with one server and 0.51 *S* with four servers sharing one queue. Explain why each added server reduces waiting less than the one before, and why one shared queue beats four separate ones.

### Q21: Response time across tiers

A request passes Nginx (*D* = 3 ms), an application server (*D* = 20 ms) and PostgreSQL (*D* = 15 ms) at *X* = 30 req/s. Treating each as M/M/1, compute each tier's utilization and residence time, and the total response time. Which tier would you improve first?

### Q22: MVA by hand

A closed system has an application server with *D* = 0.1 s and a database with *D* = 0.3 s; users think *Z* = 0.5 s. Run the MVA recursion for *n* = 1, 2 and 3 users, and compute the knee *M*\* = (*D* + *Z*) / *D*<sub>max</sub>, with *D* = Σ*D*<sub>k</sub>.

---

## Part 6: From theory to practice

### Q23: The workflow

A checkout page is slow. Describe what you would do at each step of the performance-engineering workflow, from measuring to refining.

### Q24: When to simulate

Recommend analytic models or simulation for a message queue with three priority levels, a buffer of 1,000 messages, consumers that take 10 messages at a time, and auto-scaling. Justify each reason.

---

## Topics covered

| Topic | Questions |
|---|---|
| Variability, queues in systems | Q1, Q2, Q3 |
| Kendall notation, open and closed, method choice | Q4, Q5, Q6 |
| Counters, derived metrics, assumptions | Q7, Q8, Q9, Q10 |
| Little, utilization, service demand, forced flow | Q11, Q12, Q13, Q14, Q15 |
| Hockey stick, tail, sizing, servers, MVA | Q16 to Q22 |
| Workflow, simulation | Q23, Q24 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
