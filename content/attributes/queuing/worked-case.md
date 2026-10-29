---
title: "Worked Case: From Counters to Capacity"
parent: Queuing Theory
nav_order: 5
layout: default
page_type: worked-case
---

# Worked Case: From Counters to Capacity

This worked case turns the counters of a real load test into service demands, finds the bottleneck, predicts throughput and response time, checks the predictions against the measurements, and decides on capacity. The theory is on [Operational Laws](operational-laws.md), [Queuing Models](models.md), [Queuing Networks and MVA](networks.md) and [Capacity Planning](applications.md).

**Example: a book catalogue under load.** The service of [Worked Case: Load Testing a Catalogue Service](../performance/worked-case.md) runs as one uvicorn process with one event loop. It serves book pages from memory and searches with a full-scan SQL query, in a 75/25 mix; simulated users think *Z* = 2 s between requests, and the expected peak is 200 active users. The question: how many users can one process carry under the utilization rule, and what is the cheapest way to reach 200? _(Constructed example, measured for this page with Python 3.12.9, uvicorn 0.54.0, Locust 2.47.0 and psutil 7.2.2 on an Apple M4 laptop; the numbers are illustrative.)_

## 1. Choose the measurement window

A service demand is useful only if it describes the system at the loads you care about. The load test has eight steps of 60 s; each step's first 20 s are discarded as warm-up, leaving windows of about 40 s.

**Decision point: which window?** Criteria: flow balance (completions match arrivals, so no backlog builds inside the window), a steady load, and a utilization in the working range, roughly 40–80%, where fixed overheads no longer dominate. The 150-user step meets all three (its counters are in the step 2 table): utilization 55%, no errors, and nearly the same throughput in both halves of the window (73.3 and 74.1 req/s). A window from the 1-user baseline does not, as step 3 shows.

## 2. Read the counters

For each window `steps.py` takes *T* from the counter sampler's timestamps, *C* as the number of requests completed, and *B* as the difference of the server process's CPU time (`psutil.Process(pid).cpu_times()`, user plus system) between the window's first and last sample. The [utilization law](operational-laws.md) then gives *U* = *B*/*T* and the throughput *X* = *C*/*T*. The load generator's CPU is recorded too, as a second resource to rule out. *U* is the process's CPU share; it stands in for the event loop's busy time because the loop never waits for I/O here (the 12 MB database sits in memory).

```text
$ python steps.py before        # the same run and script as the load-testing case; columns trimmed
 users   T_s     C    X/s    B_s     U  D_ms   R_ms locust_U
     1  39.4    18    0.5   0.33  0.01  18.4   16.5    0.001
    25  39.3   479   12.2   6.43  0.16  13.4   19.6    0.014
    50  39.3   979   24.9  10.48  0.27  10.7   19.4    0.017
   100  38.3  1895   49.5  16.52  0.43   8.7   19.8    0.020
   150  39.3  2899   73.7  21.80  0.55   7.5   25.1    0.023
   200  38.3  3747   97.9  26.74  0.70   7.1   38.8    0.028
   250  39.3  4667  118.7  35.22  0.90   7.5  108.7    0.031
   300  38.3  5042  131.7  37.54  0.98   7.4  238.4    0.034
```

## 3. Compute the service demands

The **service demand law** gives the CPU time each request needs, *D* = *U*/*X* = *B*/*C* {% cite denning1978operational %}. In the 150-user window, *D* = 21.80 s / 2,899 requests = **7.5 ms** per request. From 100 users up, *D* stays between 7.1 and 8.7 ms; at 1 user it is 18.4 ms, because the process's idle housekeeping and a CPU running at low clock speed are charged to only 18 requests; the same slow clock makes a search take about 60 ms of wall time at 1 user. That is why step 1 excluded the baseline.

The load generator's demand is about 0.023 / 73.7 ≈ 0.3 ms per request, over 20 times smaller. The **bottleneck** is the resource with the largest demand: the server's event loop.

Which requests use it? Two isolation runs, one route at a time for 60 s with 1–3 s think time (searches with 40 users, book views with 200), analysed the same way, give a demand per request class:

```text
users    X/s     B_s     U   D_ms
   40   19.4   23.87  0.61   31.3     (search only)
  200   99.4    0.82  0.02    0.2     (books only: 0.82 s / 3,899 requests = 0.21 ms)
```

Weighted by the mix, 0.25 × 31.3 + 0.75 × 0.21 = 8.0 ms, within 7% of the 7.5 ms measured in the mix, and searches account for 7.83 / 8.0 = 98% of it.

## 4. Predict, then compare with the measurement

With one queuing resource of demand *D* = 7.5 ms and users who think *Z* = 2 s, the bounds follow from the demand alone ([Queuing Networks and MVA](networks.md)): throughput can never exceed 1/*D*, and *M*\* = (*D* + *Z*)/*D* is the user count where the two straight-line bounds, *X* ≤ *M*/(*D* + *Z*) and *X* ≤ 1/*D*, cross. The measured curve bends before *M*\*: the load test saw it between 200 and 250 users. Exact MVA for this one-queue model adds the curve between them:

```python
# Predict X and R for M users: one queue (the event loop, demand D) plus think time Z.
D, Z = 0.0075, 2.0
print(f"X_max = 1/D = {1/D:.0f} req/s; knee M* = (D+Z)/D = {(D+Z)/D:.0f} users")
Q = 0.0                                          # exact MVA, one queuing resource
for M in range(1, 301):
    R = D * (1 + Q); X = M / (R + Z); Q = X * R
    if M in (100, 150, 200, 250, 300):
        U = X * D
        print(f"M={M}: MVA X={X:6.1f} R={R*1000:6.1f} ms U_MVA={U:.2f} | open M/M/1 at U_MVA: R=D/(1-U)={D/(1-U)*1000 if U<1 else float('inf'):6.1f} ms")
```


```text
$ python predict.py
X_max = 1/D = 133 req/s; knee M* = (D+Z)/D = 268 users
M=100: MVA X=  49.7 R=  11.8 ms U_MVA=0.37 | open M/M/1 at U_MVA: R=D/(1-U)=  12.0 ms
M=150: MVA X=  74.4 R=  16.7 ms U_MVA=0.56 | open M/M/1 at U_MVA: R=D/(1-U)=  17.0 ms
M=200: MVA X=  98.6 R=  27.5 ms U_MVA=0.74 | open M/M/1 at U_MVA: R=D/(1-U)=  28.8 ms
M=250: MVA X= 121.1 R=  64.9 ms U_MVA=0.91 | open M/M/1 at U_MVA: R=D/(1-U)=  81.5 ms
M=300: MVA X= 132.9 R= 257.2 ms U_MVA=1.00 | open M/M/1 at U_MVA: R=D/(1-U)=2360.7 ms
```

Throughput is predicted within about 2% at every step listed (49.7 against 49.5 measured, 98.6 against 97.9, 132.9 against 131.7), and the 300-user step runs at 98% utilization, as the bound says. The M/M/1 column uses MVA's own utilization *U*<sub>MVA</sub>. Response time is not predicted well: at 150 users the models give about 17 ms and the test measured 25 ms; at 200, 28 ms against 39 ms. The interactive response time law applied to the measurement, *R* = *M*/*X* − *Z*, gives 200 / 97.9 − 2 = 43 ms rather than the 39 ms timed. It subtracts two numbers near 2 s: a 1% error in *X* moves *R* by about 20 ms. Use it to check throughput, not to measure response time.

**Decision point: is the model good enough to plan with?** Criterion: predicted throughput within 10% of the measurement, and response time either within about 20% or with an explained, one-sided error. Throughput passes. The response-time error needs an explanation before the model can be trusted.

## 5. Explain the gap: service times vary

M/M/1 and MVA assume exponentially distributed service times. This service has two very different requests: 31.3 ms for a search, 0.21 ms for a book view. For a single server with general service times (M/G/1), the mean wait of M/M/1 is scaled by (1 + *CV*²)/2, where *CV* is the coefficient of variation of the service time (its standard deviation divided by its mean) {% cite shortle2018fundamentals %}. With *CV*² = 0, constant service times, the factor is ½, the M/D/1 case on [Queuing Models](models.md); exponential times have *CV*² = 1 and a factor of 1.

```text
$ python variability.py
CV^2 = 2.84 (isolation mean 8.0 ms); S = D = 7.5 ms; wait factor (1+CV^2)/2 = 1.92
M=100: measured U=0.43  M/M/1 R= 13.2 ms  M/G/1 R= 18.4 ms  measured R=19.8 ms
M=150: measured U=0.55  M/M/1 R= 16.7 ms  M/G/1 R= 25.1 ms  measured R=25.1 ms
M=200: measured U=0.70  M/M/1 R= 25.0 ms  M/G/1 R= 41.1 ms  measured R=38.8 ms
M=250: measured U=0.90  M/M/1 R= 75.0 ms  M/G/1 R=137.2 ms  measured R=108.7 ms
```

<details markdown="1">
<summary markdown="span">The complete `variability.py`</summary>

```python
# M/G/1 correction of the M/M/1 wait, with the service-time variability of the request mix.
p_s, D_s, D_b = 0.25, 0.0313, 0.00021    # search share; demand per search and per book view (isolation runs)
mean = p_s * D_s + (1 - p_s) * D_b       # mean of the two-class mix
ES2 = p_s * D_s**2 + (1 - p_s) * D_b**2  # each class's time taken as fixed
cv2 = ES2 / mean**2 - 1                  # CV^2 is a ratio, so the 8.0 ms isolation mean is used only here
S = 0.0075                               # the demand measured in the mix (150-user window)
print(f"CV^2 = {cv2:.2f} (isolation mean {mean*1000:.1f} ms); S = D = {S*1000:.1f} ms; "
      f"wait factor (1+CV^2)/2 = {(1+cv2)/2:.2f}")
for M, U, Rm in [(100, 0.43, 19.8), (150, 0.55, 25.1), (200, 0.70, 38.8), (250, 0.90, 108.7)]:  # measured U, R
    mm1 = S / (1 - U)
    mg1 = S + U * S / (1 - U) * (1 + cv2) / 2
    print(f"M={M}: measured U={U:.2f}  M/M/1 R={mm1*1000:5.1f} ms  M/G/1 R={mg1*1000:5.1f} ms  measured R={Rm} ms")
```

</details>

The script takes each class's time as fixed and uses the isolation demands (mean 8.0 ms) only to compute *CV*², a ratio; every response time uses *S* = *D* = 7.5 ms from the mix and the measured *U* of each step. Without isolation runs, take *CV*² from the request log of a low-load step, where queueing is negligible: the variance of the response times divided by their squared mean. With the variability included, the prediction is within about 7% of the measurement up to 70% utilization. At 90% the open formula overshoots, because closed users stop sending while they wait, as [Capacity Planning](applications.md) notes. The model is good enough to plan with, below about 75% utilization.

## 6. Decide the capacity

Under the 70% rule, the process can deliver *X* = *U*/*D* = 0.7 / 0.0075 ≈ 93 req/s. The response time at that utilization, from M/G/1, is 7.5 + 0.7 × 7.5 / 0.3 × 1.92 ≈ 41 ms, so by the interactive response time law the process carries *M* = *X* (*R* + *Z*) = 93.3 × 2.041 ≈ **190 users** at the 70% ceiling. The pass line of the margin rule (step 1 of the performance case) is 63%: *X* = 84 req/s, *R* ≈ 32 ms, about **171 users**. The test measured *U* = 0.70 at 200 users, slightly more users than predicted, because *D* in that window was 7.1 ms instead of 7.5. Either way 200 users fail: the book-page p95 of 105 ms misses the 100 ms target, and 70% CPU is above the 63% that the margin rule needs for a pass.

Two ways to raise capacity follow from *U* = *X* · *D*:

| Option | What changes | Users at 70% | Cost |
|---|---|---|---|
| A second worker process | two servers share the work: *X* = 2 × 0.7 / *D* ≈ 187 req/s | about 380 (approximate: reuses the one-server response time) | a second core and about 115 MB more memory; each worker still blocks on searches |
| Cut the search demand with a cache | *D* = 0.25 × *m* × 31.3 + 0.75 × 0.21 ms (isolation demands, 7% above the mix's, so the bound is cautious), where *m* is the share of searches that miss the cache | 200 users need *X* ≈ 98 req/s, so *D* ≤ 0.63 / 98 ≈ 6.4 ms at the 63% pass line, which holds for *m* ≤ 0.80 (0.89 at the 70% ceiling) | code change; stale results until the cache is refreshed |

**Decision point: which option?** Criterion: the cheapest way to raise the ceiling 1/*D*<sub>max</sub> is to cut the largest demand {% cite denning1978operational %}; adding servers raises it too, but costs hardware and leaves the per-request cost unchanged. Searches are 98% of *D*, and the access log shows ten search terms, so a cache hits almost always. **Decision: cache the searches; keep the second worker as a fallback.** The re-run with the cache measured *U* = 4% at 200 users and *X* = 100.2 req/s, so *D* = 0.04 / 100.2 ≈ 0.4 ms: the event loop is no longer near saturation. The formula predicts thousands of users, far outside the measured range, so the team states capacity as "at least 300 users, measured" and repeats the loop after the next release.

## 7. The artifact: a capacity worksheet

Fill one row per resource from one window; this is the catalogue service at 150 users.

| Resource | *T* (s) | *C* | *B* (s) | *U* = *B*/*T* | *X* = *C*/*T* | *D* = *B*/*C* | 1/*D* (req/s) |
|---|---|---|---|---|---|---|---|
| Server event loop (1 core) | 39.3 | 2,899 | 21.80 | 0.55 | 73.7 | 7.5 ms | 133 |
| Load generator (1 core) | 39.3 | 2,899 | about 0.9 | 0.02 | 73.7 | about 0.3 ms | about 3,200 |

Then record: bottleneck (largest *D*): event loop; dominant class: search, 98%; think time *Z* = 2 s; knee *M*\* = 268; *CV*² = 2.84; users at 70% = 190, at the 63% pass line = 171; target 200; decision: cache, fallback second worker; validation: re-run at 200 users, *U* = 4%.

## Common mistakes

These mistakes recur when demands are measured; each has a symptom you can check.

1. **Taking *D* from a quiet window.** At 1 user *D* came out at 18.4 ms, 2.5 times the working value; a capacity plan built on it would buy servers it does not need. Spot it: *D* falling as load rises.
2. **Trusting M/M/1 for a mixed workload.** Here it underestimated response time by a third at 55% utilization. Spot it: request classes whose service times differ by an order of magnitude; compute *CV*².
3. **Mixing boundaries.** Locust's response time includes the client and the loopback network, the server's CPU time does not; at low load the two differ by a millisecond or two, which matters only when *D* is small, as after the fix. Spot it: Little's law, *N* = *X* · *R*, failing between counters taken at different points {% cite little2011littles %}.
4. **Extrapolating a demand past the next bottleneck.** After the fix, 1/*D* promises over 2,000 req/s, which no run has shown. Spot it: a prediction beyond the largest measured load.

## How solid is this?

- **Where it comes from.** The service demand law, the bottleneck rule and the bounds are Denning and Buzen's {% cite denning1978operational %}; the M/G/1 scaling is standard queuing theory {% cite shortle2018fundamentals %}; the loop follows Gunther {% cite gunther2007guerrilla %}.
- **What the numbers are.** One run per configuration on one laptop. The agreement of throughput with 1/*D* is robust; the response-time agreement after the M/G/1 correction is good here, but the correction assumes a single first-come, first-served server, which an event loop only approximates.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
