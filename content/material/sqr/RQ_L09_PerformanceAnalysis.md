---
title: "RQ L09: Performance Analysis"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 18
redirect_from: /content/attributes/performance/RQ_L09_PerformanceAnalysis.html
layout: default
page_type: question-set
---

# RQ L09: Performance Analysis

**Format:** Explain in your own words and show your calculations. These questions test the lecture summarized in [SN L09: Performance Analysis](SN_L09_PerformanceAnalysis.md).

---

## Part 1: Why performance matters

### Q1: The business value of latency

Google's experiments found that adding 100–400 ms of delay to search pages reduced searches per user by 0.2–0.6%. A shop processes 50,000 orders a day at an average of 80 USD, and an upgrade costing 200,000 USD a year would cut its response time by 800 ms.

1. Why can you not simply scale the Google result ("0.2–0.6% per 100–400 ms") to estimate the shop's revenue gain?
2. What would you measure in the shop itself to decide whether the upgrade pays off?

### Q2: Why late fixes are expensive

Explain why a performance problem caused by an anti-pattern cannot be fixed by tuning. Give one example of a problem that a design review could have caught, and say what the redesign would be.

---

## Part 2: Metrics

### Q3: Little's law

A Kubernetes deployment shows on average 250 active connections and a mean response time of 125 ms.

1. What is the throughput in requests per second?
2. The team wants a mean response time of 80 ms at the same throughput. How many requests will be in the system?
3. A cache cuts response time by 40%. At the same throughput, what happens to concurrency?

### Q4: Averaging rates

Two benchmark runs of the same workload achieve 40 and 60 requests per second. What is the average throughput, and why is it not 50? When would 50 be the right answer?

### Q5: Percentiles versus means

Two endpoints both report a mean response time of 500 ms.

- Endpoint A: p50 = 450 ms, p90 = 550 ms, p99 = 600 ms.
- Endpoint B: p50 = 100 ms, p90 = 200 ms, p99 = 8,000 ms.

1. Which endpoint serves most users better?
2. Which one violates a target of "p99 < 1,000 ms"?
3. Why can two endpoints with such different percentiles have the same mean?

### Q6: Behavior under rising load

Sketch utilization, throughput and response time against the number of users, and mark the light-load, heavy-load and buckle zones. For each zone, say what happens to each metric and to the user. What changes if the bottleneck resource is replaced by one twice as fast?

---

## Part 3: Scalability laws

### Q7: Amdahl's law

An application spends 45% of its time in database queries, 35% in business logic and 20% in network I/O. A new index makes queries 3 times faster.

1. What is the overall speedup?
2. What is the most you could gain from making queries infinitely fast?
3. If network I/O is also made 2 times faster, what is the combined speedup?

### Q8: Amdahl or Gustafson?

A training pipeline loads data for 10 minutes (serial) and trains for 50 minutes on one GPU (parallel).

1. With 10 GPUs and the same data, how long does the pipeline take?
2. Under Gustafson's view (loading still takes 10 minutes), what could the team do with 10 GPUs in the original 60 minutes, and what is the scaled speedup?
3. Which view fits a team that wants better models rather than faster training runs?

### Q9: Retrograde throughput

A service cluster measures:

| Instances | Throughput (req/s) |
|---|---|
| 1 | 100 |
| 2 | 185 |
| 4 | 330 |
| 8 | 500 |
| 16 | 450 |
| 32 | 320 |

1. Which USL parameter, α or β, explains the decline beyond 8 instances?
2. What is physically happening in the cluster?
3. Propose one architectural change that would reduce that parameter.

---

## Part 4: Risk, anti-patterns and bugs

### Q10: Name the anti-pattern

For each case, name the anti-pattern and propose a fix.

1. An endpoint fetches 100 user ids with one query, then each profile with a separate query; it takes 3 s.
2. An `OrderController` class computes every order itself: it asks the cart for each item, each item for its price, and the stock component for each item's availability, then applies the discounts. An order of 20 items takes about 60 calls. After your fix, how many calls does the controller make, and where has the work gone?
3. A service creates and discards a database connection object on every request, and garbage-collection pauses cause 2 s spikes.

### Q11: How performance bugs are found

Studies found that 33.9–57.3% of performance bugs were discovered by reasoning about code and only 5.5–10.4% with a profiler.

1. Why does a profiler find so few of them?
2. How would you change your team's code review to catch more?

### Q12: Performance budget

Explain what a performance budget allocates, how it is tracked during development, and why it is cheaper than load testing alone.

---

## Part 5: Testing

### Q13: A test strategy for a ticket sale

A concert ticket platform normally has 5,000 concurrent users; at the moment sales open it expects 200,000 within 60 seconds, for a two-hour sale.

1. Which of the six test types would you run, and in what order? Where does a spike test fit?
2. Which metrics would you monitor, and what targets would you write?
3. What does a realistic workload mean here?
4. What risk remains if you run only a load test and skip stress and soak tests?

### Q14: Reading a run

During a soak test at constant load, the response time rises slowly but steadily for four hours. What is the likely cause, and what would you look at next?

---

## Part 6: Distributed systems

### Q15: Tail amplification

A search query fans out to 80 shards; each shard is slow (over 200 ms) on 0.5% of requests, independently.

1. What share of queries hits at least one slow shard?
2. With hedged requests, a sub-request is slow only if both the original and its copy are slow. If the two are independent, what is the new probability per sub-request, and for the whole query?
3. Why does Dean and Barroso's advice to send the copy only after the 95th-percentile expected latency keep the extra load to about 5%?

### Q16: Per-service budgets

A chain of three services has an end-to-end target of p99 < 400 ms. Measured p99s are: gateway 30 ms, recommendation 280 ms, database 50 ms.

1. Propose a per-service budget.
2. The recommendation team wants a new model that raises its p99 to 350 ms. Should it be approved, and what would have to change elsewhere?
3. Why is adding the per-service p99s only an approximation of the chain's p99?

### Q17: Classical and cloud performance engineering

Map each classical concept to its cloud-native counterpart: the performance budget per component, tracking the budget during development, accepting some degradation, and queuing models. Explain why Little's law and queuing still apply to microservices.

---

## Topics covered

| Topic | Questions |
|---|---|
| Business impact, design-time fixes | Q1, Q2 |
| Little's law, harmonic mean, percentiles | Q3, Q4, Q5 |
| Behavior under load | Q6 |
| Amdahl, Gustafson, USL | Q7, Q8, Q9 |
| Anti-patterns, bugs, budgets | Q10, Q11, Q12 |
| Test types and analysis | Q13, Q14 |
| Tail latency, per-service SLOs | Q15, Q16, Q17 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
