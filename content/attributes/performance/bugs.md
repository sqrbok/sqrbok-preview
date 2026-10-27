---
title: Performance Bugs and Anti-Patterns
parent: Performance
nav_order: 5
layout: default
page_type: deep-dive   # the area's evidence page: four studies, each needs its setup and numbers stated
---

# Performance Bugs and Anti-Patterns

A **performance bug** is a programming error that causes significant performance degradation without producing a wrong answer {% cite nistor2013discovering %}. A **performance anti-pattern** is a recurring design that causes such degradation and cannot be removed by tuning alone {% cite smith2000antipatterns %}.

## 1. Anti-patterns are design faults

Smith and Williams catalogued designs that make systems slow however well each method is coded {% cite smith2000antipatterns %}. Four are common.

| Anti-pattern | What goes wrong | Example |
|---|---|---|
| God Class | one class does all the work and asks others for their data | a controller that reads a valve's status and then tells it to open needs two messages where one would do; a controller that asks 20 cart items for price, discount and stock makes 60 calls where `cart.total()` makes one |
| Circuitous Treasure Hunt | data is found by following a long chain of lookups | drawing one engineering model took 6,001 database calls: 1 for the model, 2,000 for its beams, 4,000 for their nodes |
| One-Lane Bridge | all traffic must pass one narrow point | a single lock or connection that every request needs |
| Excessive Dynamic Allocation | objects are created and destroyed on every call | a service that opens a new database connection object for every request pays the set-up cost each time, and the garbage collector must pause to reclaim the discarded objects; in the paper, service time (the processing time per call) grows with call depth |

The Treasure Hunt is today's "N + 1 queries" problem. For example, an endpoint loads 100 user ids with one query and then each profile with its own query: 101 round trips where one join would do. At 5 ms per round trip that is about 0.5 s spent on network waits alone. The fix is a redesign (one query, or a batch), not a faster database.

## 2. What empirical studies of performance bugs found

Four studies of open-source bug trackers describe how performance bugs differ from other bugs.

| Study | Data | Finding |
|---|---|---|
| Jin et al. 2012 {% cite jin2012understanding %} | 109 bugs from Apache, Chrome, GCC, Mozilla and MySQL | two thirds came from developers misunderstanding the workload or an API's performance; over three quarters sat in an input-dependent loop or input-event handler; the median fix was 8 lines |
| Nistor et al. 2013 {% cite nistor2013discovering %} | performance and other bugs from Eclipse JDT, Eclipse SWT and Mozilla | 33.9–57.3% of performance bugs were found by reasoning about code, only 5.5–10.4% with a profiler; fixing them was no more likely to introduce new bugs |
| Zaman et al. 2011 {% cite zaman2011security %} | 4,293 performance and 847 security bugs in Firefox | security bugs were triaged 3.64 times faster; more than 62% of performance bugs were never assigned to a developer |
| Zaman et al. 2012 {% cite zaman2012qualitative %} | sampled performance bugs from Firefox and Chrome | performance bugs needed more discussion, were harder to reproduce, and in Chrome were more often regressions |

Two lessons follow. First, a profiler finds the hot spot of a run (the code where that run spent most of its time), but a third to over half of the bugs were found by someone reading the code and noticing work that grows with the input, against 5–10% with a profiler: review for performance, not just test for it. Second, because the typical fix is small, the cost lies in finding the bug, which is an argument for performance budgets (see §3) and early review. Third, since performance bugs are often never assigned, a team has to track them deliberately.

## 3. Why late discovery is expensive

Anti-patterns are fixed by changing the design, which is cheap on a whiteboard and costly after the code depends on it. Smith and Williams' **Software Performance Engineering** therefore models a design's expected response time before it is built, from estimated execution counts and resource demands {% cite smith2001performance %}. Jewell's **performance budget** assigns each component a share of the response time, tracks actual against budget during development, and puts the cost of this work at 1–5% of the project {% cite jewell2008performance %}.

Practice lags behind. In a survey of 26 DevOps practitioners, only about a third evaluated performance regularly, half spent less than 5% of their time on it, and 88% used no performance model, although about 70% said they would like to {% cite bezemer2019devops %}.

## When to use it — and when not

- Use the anti-pattern list in design and code reviews: look for loops that issue a request per item and for classes that pull other objects' data.
- Do not expect a profiler run to replace this review; it shows where time goes in one workload, not which code will grow with the next one.

## Limitations

The studies cover open-source desktop and server projects and their bug trackers; bugs fixed before they were reported, and closed-source systems, are not in the data.

## How solid is this?

- **Where it comes from.** Four empirical studies {% cite jin2012understanding nistor2013discovering zaman2011security zaman2012qualitative %}, the anti-pattern catalogue {% cite smith2000antipatterns %} and the DevOps survey {% cite bezemer2019devops %}.
- **What is small.** The DevOps survey rests on 26 complete responses, so its percentages are indicative only.
- **What varies.** Nistor's discovery shares are ranges because they differ across the three projects studied.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
