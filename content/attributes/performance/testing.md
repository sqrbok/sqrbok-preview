---
title: Performance Testing
parent: Performance
nav_order: 3
layout: default
---

# Performance Testing

**Performance testing** runs a system under a controlled, realistic workload and measures response times, throughput and resource use, to check that it meets stated targets and to find where it stops meeting them. Functional tests ask whether the answer is right; performance tests ask whether it arrives in time {% cite everett2007performance %}.

## 1. Six test types, six questions

Molyneaux distinguishes six types of performance test, each answering a different question {% cite molyneaux2014art %}.

![Baseline feeds load; load is pushed into stress, held for soak, or narrowed to isolation; smoke gates each build](test-types.svg)

_How the six test types relate: run a smoke test first on each build; if it passes, run the load test, then push it into stress, hold it for a soak, or narrow it to an isolation test._ {% cite molyneaux2014art %}

| Type | Load | Question |
|---|---|---|
| Baseline | one user, best case | What is the response time with no contention? |
| Load | the expected peak | Does the system meet its targets at that peak? |
| Stress | beyond the expected peak | Where does it break, and how does it fail? |
| Soak | the expected load for hours | Do memory leaks or slow drift appear over time? |
| Smoke | the changed code only | Did this build make a known path slower? |
| Isolation | one transaction, repeated | Which step of a slow transaction is slow? |

## 2. Write the target as a percentile under a stated load

A target needs a measure, a threshold and a load. "Average response time under 2 s" fails twice: an average hides slow requests, and no load is named. Everett recommends stating the *maximum* acceptable time per transaction group {% cite everett2007performance %}; in practice the maximum is stated at a percentile, such as "no slower than 3 s for 95% of requests", because the absolute maximum of a long run is one outlier. For example, "with 500 active users, 95% of searches complete within 3 s and no request fails" names all three.

## 3. Make the test environment and workload like production

Results transfer to production only when the test matches it: the same hardware class and configuration, realistic data volumes (not an empty database), and a **workload model** with the right transaction mix, think times between requests and number of active users. The number of key transactions to script rarely exceeds 20 {% cite molyneaux2014art %}. The model's numbers come from production: the access log gives the mix and the peak request rate, gaps between one client's requests give the think time, and the interactive response time law turns those into active users ([Worked Case: Load Testing a Catalogue Service](worked-case.md)). Liu's process puts most of the effort before the first run: design the workload, write the scripts, choose the hardware, set up the environment, fix a repeatable procedure, establish a baseline, then analyze bottlenecks and tune {% cite liu2011performance %}.

## 4. Run each test in three phases

Each run ramps users up to the peak, holds the peak while measuring, and ramps down to check that resources are released. Many systems fail during ramp-up rather than at the peak, because allocating sessions, threads and memory is harder than steady processing {% cite everett2007performance %}. Measure only the steady phase, and compare every result against the baseline.

## 5. Read the curves, not just the numbers

The shape of the response-time curve during a run points to a cause.

| Pattern during a run | Likely cause |
|---|---|
| Rises, then flattens as load levels off | normal saturation of one resource |
| Keeps rising at constant load | a leak: memory, connections, file handles |
| Becomes erratic | contention or garbage-collection pauses |

Large tests record thousands of counters. Malik and colleagues reduced them automatically to 5–20 "performance signature" counters and detected deviations from a baseline with 95% average precision, so a pipeline can flag regressions without a person reading every graph {% cite malik2013automatic %}.

## When to use it — and when not

- Run a load test before every release that changes a hot path (code that runs on most requests), and a smoke test on every build.
- Run stress and soak tests before launches and capacity changes.
- Do not trust results from an environment that differs from production in hardware, data size or configuration.

## Limitations

A test shows behavior only for the workload it models: an unmodeled transaction mix or traffic spike remains untested. Few coverage measures exist for load tests; the main one counts only the share of likely workload states that a test visits {% cite jiang2015survey %}. Deciding when a system has been tested enough is therefore a judgement.

## How solid is this?

- **Where it comes from.** Practitioner books {% cite molyneaux2014art everett2007performance liu2011performance %} and a survey of 147 load-testing papers and tools {% cite jiang2015survey %}.
- **What is contested.** Molyneaux's figure that moving from release-time validation to performance-driven development cuts defects escaping to production from about 30% to 5% is an experience estimate, not a study result {% cite molyneaux2014art %}.
- **What supports targeting.** At AT&T, 93% of performance-related problems found in architecture reviews sat in the 30% of projects judged weakest {% cite weyuker2000experience %}: risk reviews tell you where to test hardest.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
