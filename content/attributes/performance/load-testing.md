---
title: Load Testing with Locust
parent: Performance
nav_order: 4
layout: default
page_type: deep-dive   # a real Locust report read line by line; each figure on it (rates, percentiles, rounding) must be explained where it appears
vegalite: true
---

# Load Testing with Locust

A **load test** drives a system with many simulated users at once and records each request's response time, to see whether the system meets its targets at a given load. Locust is an open-source load tool in which each simulated user is a small Python class {% cite locust_docs %}.

## 1. The user model is code

A Locust script describes one kind of user: what it requests, how often, and how long it "thinks" between requests. The script below models a reader of a wiki who views three pages for every search.

```python
from locust import HttpUser, task, between

class Reader(HttpUser):
    wait_time = between(1, 3)          # think time between requests, seconds

    @task(3)                            # three page views ...
    def view_page(self):
        self.client.get("/page/42", name="/page/[id]")

    @task(1)                            # ... for every search
    def search(self):
        self.client.get("/search?q=queue", name="/search")
```

The weights 3 and 1 set the **transaction mix**, `between(1, 3)` sets a think time of 2 s on average, and `name=` groups all page views under one row of the report, whatever their id. The run below used the fixed id 42; in a real test vary it, for example `f"/page/{random.randint(1, 1000)}"`, so that caches do not serve every request from memory.

## 2. The run is a load profile

```bash
locust -f locustfile.py --headless -u 40 -r 4 -t 60s --host http://localhost:8099
```

The flags start 40 users (`-u`), add 4 per second (`-r`), and stop after 60 s (`-t`): a 10 s ramp-up, then 50 s at full load.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 420, "height": 140,
  "data": {"values": [{"t":0,"u":0},{"t":10,"u":40},{"t":60,"u":40}]},
  "mark": {"type": "line", "strokeWidth": 3, "color": "#019546"},
  "encoding": {
    "x": {"field": "t", "type": "quantitative", "title": "time (s)", "scale": {"domain": [0, 60]}},
    "y": {"field": "u", "type": "quantitative", "title": "active users", "scale": {"domain": [0, 45]}}},
  "config": {"font": "Tahoma, sans-serif", "view": {"stroke": null}}
}
```

_The load profile of the command above: ramp-up, then a steady phase. Only the steady phase should be compared with a target._

## 3. Worked example: reading the report

The script above, run against a small test server on one laptop, printed this summary (response times in milliseconds, percentile columns trimmed):

```text
Name          # reqs  # fails |  Avg  Min  Max  Med | req/s
/page/[id]       851        0 |   34    0  406   19 | 14.22
/search          250        0 |   85    0  398   60 |  4.18
Aggregated      1101        0 |   46    0  406   24 | 18.40

Name             50%  90%  95%  98%  99%
/page/[id]        19   59   79  400  410
/search           60  190  240  370  390
```

Read it row by row. The 851 page views and 250 searches are about 3.4 to 1, not exactly 3 to 1, because Locust picks each task at random with the given weights; Locust computes req/s as the request count divided by the time from the start of the test to the last request {% cite locust_docs %}, here about 59.85 s: 1,101 / 59.85 ≈ 18.40, and likewise 851 / 59.85 ≈ 14.22 and 250 / 59.85 ≈ 4.18. A Min of 0 ms means under half a millisecond, rounded down. The aggregated median of 24 ms comes from the merged distribution of all 1,101 requests, not from the two row medians. Page views have a median of 19 ms and a p95 of 79 ms, yet a p98 of 400 ms and a p99 of 410 ms: since p95 is below the 400 ms slow group and p98 is in it, between 2% and 5% of page views are an order of magnitude slower than the rest. The test server makes each page view slow at random with probability 2%, so in this run the realized share was slightly above 2%, which is enough to put the p98 in the slow group. That slow group is what the 34 ms mean hides. For page views, a target of "p95 ≤ 100 ms" passes and "p99 ≤ 100 ms" fails; searches (p95 240 ms, p99 390 ms) fail both. Note that the p99 of 410 ms is above the maximum of 406 ms: to keep its statistics small, Locust stores each response time rounded to about two significant digits (406 ms is counted as 410 ms) and computes percentiles from those rounded values, while the maximum is exact {% cite locust_docs %}. Read percentiles above 100 ms as accurate to about two significant digits.

## 4. Check the run against the interactive response time law

Locust users wait for each reply, then think, so with *M* users throughput must satisfy *X* = *M* / (*R* + *Z*) {% cite denning1978operational %}. At full load, 40 users with *R* = 0.046 s and *Z* = 2 s give X ≈ 40 / 2.046 ≈ 19.6 requests/s. Averaged over the whole run, including the ramp-up, there were (10 s × 20 users on average during ramp-up + 50 s × 40 users) / 60 s ≈ 36.7 users (the average number of active users), giving 36.7 / 2.046 ≈ 17.9 requests/s; the report shows 18.4. As a rule of thumb, a difference of a few percent counts as a match, because think times are random and the ramp-up average is approximate. When the measured throughput falls well below this figure, the load generator itself is saturated or the think time is not what you think.

## When to use it — and when not

- Use a scripted tool like Locust when the workload has a mix of request types and think times, and the targets are per request type.
- Do not read throughput from a run whose load generator machine was CPU-bound; spread the users over several workers.

## Limitations

Locust users form a **closed** workload. In an **open** workload, requests arrive at a rate set from outside, whatever the system's speed. In Locust's closed one, when the system slows down, each user sends fewer requests, so the request rate falls exactly when the system is struggling. Traffic from many independent users keeps arriving regardless, so a closed test can understate the overload a real spike causes (see [Queuing Networks and MVA](../queuing/networks.md)).

## How solid is this?

- **Where it comes from.** The script syntax and command-line flags follow the Locust documentation and were run with Locust 2.47 {% cite locust_docs %}.
- **What the numbers are.** The report comes from a small test server built to answer most requests quickly and to make each page view slow (400 ms) with probability 2%; it illustrates how to read a report and is not a benchmark.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
