---
title: "Worked Case: Load Testing a Catalogue Service"
parent: Performance
nav_order: 7
layout: default
page_type: worked-case
vegalite: true
---

# Worked Case: Load Testing a Catalogue Service

This worked case load-tests one small web service from its access log to a ship decision: workload model, step load, resource counters, diagnosis, fix and re-run against a p95 target. The theory is on [Performance Testing](testing.md), [Load Testing with Locust](load-testing.md) and [Performance Measurement](measurement.md).

**Example: a book catalogue.** A FastAPI service serves `GET /books/{id}` from memory and `GET /search?q=` by a query over 200,000 books in SQLite: a synchronous call inside an `async` handler, with a `LIKE '%term%'` that no index can serve. The team must decide whether one server process can carry next month's peak, which it expects to match last month's. _(Constructed example. The service, the access log and every run were made for this page with Python 3.12.9, FastAPI 0.162.0, uvicorn 0.54.0, Locust 2.47.0 and psutil 7.2.2 on an Apple M4 laptop with 10 cores; the server and Locust ran on the same machine, so the numbers are illustrative, not a benchmark.)_

```python
# catalog service: one fast route, one slow route (constructed example)
import os, sqlite3
from functools import lru_cache
from fastapi import FastAPI

app = FastAPI()
db = sqlite3.connect("catalog.db", check_same_thread=False)
BOOKS = {r[0]: r for r in db.execute("SELECT id, title, price FROM book")}

@app.get("/books/{book_id}")
async def get_book(book_id: int):                 # in-memory lookup
    _, title, price = BOOKS[book_id]
    return {"id": book_id, "title": title, "price": price}

def run_search(q: str):                            # full scan: no usable index
    rows = db.execute("SELECT id, title, price FROM book WHERE lower(title) LIKE ? OR author LIKE ? "
                      "ORDER BY price LIMIT 20", (f"%{q}%", f"%{q}%")).fetchall()
    return [{"id": i, "title": t, "price": p} for i, t, p in rows]

cached_search = lru_cache(maxsize=1024)(run_search)

@app.get("/search")
async def search(q: str):                          # sync DB call inside async handler
    if os.environ.get("SEARCH_CACHE") == "1":
        return cached_search(q)
    return run_search(q)
```

## 1. Write the target first

A target names a measure, a threshold and a load ([Performance Testing](testing.md)). The product owner's requirement, written before any test: **at the expected peak, p95 ≤ 100 ms for book pages and ≤ 200 ms for searches, with no failed requests**. The team adds an operating rule: the server process stays at or below 70% CPU at that peak, to stay left of the bend of the [hockey stick](../queuing/models.md), and a result within 10% of a threshold counts as no margin, hence a fail, because one run is noisy {% cite wilson2008operations %}. Passing therefore needs p95 ≤ 90 ms for book pages, ≤ 180 ms for searches and CPU ≤ 63%.

## 2. Build the workload model from the access log

Weyuker and Vokolos's steps for forming performance test cases are to identify the processes that drive performance, find the input parameters that matter most for each, and give them realistic values from usage data, including the observation window {% cite weyuker2000experience %}. Here the processes are the two routes; the parameters are the mix, the think time between a user's requests, the number of active users and the search terms. The values come from the busiest 15 minutes of last month's access log (constructed for this page, 89,345 lines such as `1761750000023 10.0.0.185 GET /search?q=queue 200 39`):

```text
$ python workload.py access.log
requests 89345 over 900 s; mean X = 99.3 req/s, busiest minute 99.8 req/s
mix: book 75%, search 25%
clients 200; mean think time Z = 2.00 s; mean R in log = 12 ms
active users M = X * (R + Z) = 200
```

The script measures think time as the gap between one client's consecutive requests minus the response time, and turns throughput into users with the interactive response time law, *M* = *X* (*R* + *Z*) {% cite denning1978operational %}. The log's *R* (12 ms) is server-side; the load test measures 39 ms at the client, queueing included; against *Z* = 2 s either changes *M* by about 1%. The log also shows that ten search terms make up all searches, the most popular one most often.

<details markdown="1">
<summary markdown="span">`gen_log.py` (the constructed log), `workload.py` and `make_db.py`</summary>

```python
# Constructed peak-hour access log: 15 minutes, users browse 3 pages per search,
# think 1-3 s between requests. Format: epoch_ms client method path status ms
import random
random.seed(7)
terms = ["queue","cache","river","garden","winter","python","kernel","market","harbor","signal"]
t0, end, out = 1_761_750_000_000, 1_761_750_000_000 + 15*60*1000, []
sessions = 200
for s in range(sessions):
    t = t0 + random.uniform(0, 3000)
    while t < end:
        if random.random() < 0.25:
            q = random.choices(terms, weights=[1/(k+1) for k in range(len(terms))])[0]
            path, ms = f"/search?q={q}", random.randint(25, 60)
        else:
            path, ms = f"/books/{random.randint(1, 200000)}", random.randint(1, 4)
        out.append((int(t), f"10.0.{s//250}.{s%250}", "GET", path, 200, ms))
        t += ms + random.uniform(1000, 3000)
out.sort()
with open("access.log", "w") as f:
    for r in out: f.write(" ".join(map(str, r)) + "\n")
print(len(out), "lines")
```

```python
# Step 1: derive the workload model from an access log.
import sys, collections, statistics
rows = [l.split() for l in open(sys.argv[1])]
t = [int(r[0]) for r in rows]
span = (max(t) - min(t)) / 1000
per_min = collections.Counter((x - min(t)) // 60000 for x in t)
route = collections.Counter("search" if r[3].startswith("/search") else "book" for r in rows)
last, gaps = {}, []
for ts, client, _, _, _, ms in rows:                     # think time = gap minus own response time
    if client in last: gaps.append((int(ts) - last[client]) / 1000)
    last[client] = int(ts) + int(ms)
X = len(rows) / span
Z = statistics.mean(gaps)
R = statistics.mean(int(r[5]) for r in rows) / 1000
print(f"requests {len(rows)} over {span:.0f} s; mean X = {X:.1f} req/s, busiest minute {max(per_min.values())/60:.1f} req/s")
print(f"mix: book {route['book']/len(rows):.0%}, search {route['search']/len(rows):.0%}")
print(f"clients {len(last)}; mean think time Z = {Z:.2f} s; mean R in log = {R*1000:.0f} ms")
print(f"active users M = X * (R + Z) = {X*(R+Z):.0f}")
```

```python
# Build a 200,000-row book catalogue in SQLite (constructed data).
import random, sqlite3
random.seed(1)
words = ("queue cache latency river garden winter python kernel market harbor "
         "signal lantern forest engine silver ocean thread memory castle orbit").split()
con = sqlite3.connect("catalog.db")
con.execute("DROP TABLE IF EXISTS book")
con.execute("CREATE TABLE book (id INTEGER PRIMARY KEY, title TEXT, author TEXT, price REAL)")
rows = [(i, " ".join(random.choices(words, k=4)) + f" vol {i % 97}",
         f"author{i % 5000}", round(random.uniform(5, 60), 2)) for i in range(1, 200_001)]
con.executemany("INSERT INTO book VALUES (?,?,?,?)", rows)
con.commit(); print(con.execute("SELECT count(*) FROM book").fetchone())
```

</details>

**Decision point: what load to test?** Criterion: the log's peak plus headroom to see past it: 200 users, with steps up to 300 so that the bend shows if it is near.

## 3. Script the users and the step load

The user model copies the log: three book views per search, 1–3 s of think time, popular search terms more often. The whole `locustfile.py`:

```python
# Step 2: the user model and a step-load shape.
import os, random, time
from locust import HttpUser, LoadTestShape, task, between, events

TERMS = ["queue","cache","river","garden","winter","python","kernel","market","harbor","signal"]
WEIGHTS = [1 / (k + 1) for k in range(len(TERMS))]       # popular terms first, as in the log

class Shopper(HttpUser):
    wait_time = between(1, 3)                           # Z = 2 s on average

    @task(3)
    def view_book(self):
        self.client.get(f"/books/{random.randint(1, 200000)}", name="/books/[id]")

    @task(1)
    def search(self):
        q = random.choices(TERMS, weights=WEIGHTS)[0]
        self.client.get(f"/search?q={q}", name="/search")

class Steps(LoadTestShape):
    steps = [1, 25, 50, 100, 150, 200, 250, 300]        # users per step
    step_s = 60

    def tick(self):
        i = int(self.get_run_time() // self.step_s)
        return (self.steps[i], 50) if i < len(self.steps) else None

LOG = open(f"requests_{os.environ['LABEL']}.csv", "w", buffering=1)   # e.g. LABEL=before
@events.request.add_listener
def log(name, response_time, exception, **kw):          # one line per request
    LOG.write(f"{time.time():.3f},{name},{response_time:.1f},{int(exception is not None)}\n")
```

`tick` returns the user count and a spawn rate of 50 users per second, so each step is reached within a second or two. The request hook logs every request to `requests_<label>.csv`. Liu's process asks for a reproducible procedure {% cite liu2011performance %}: here the server is restarted before every run, the first step (1 user) is the baseline, and the first 20 s of each step are discarded as warm-up.

## 4. Record resource counters alongside

A small `psutil` script samples once a second the server process's CPU time and memory, the load generator's CPU, and the load average per core (on Linux, `vmstat 1` and `iostat -x 1` give the same). It writes `counters_<label>.csv`; `run.sh` starts server, Locust and sampler together:

<details markdown="1">
<summary markdown="span">`monitor.py` and `run.sh`</summary>

```python
# Resource counters once a second: server CPU (% of one core), RSS, machine CPU, load per core.
import os, sys, time, psutil
server, gen = psutil.Process(int(sys.argv[1])), psutil.Process(int(sys.argv[2]))
ncpu = psutil.cpu_count()
server.cpu_percent(); gen.cpu_percent(); psutil.cpu_percent()
print("t,server_cpu,server_cpu_s,server_rss_mb,locust_cpu,machine_cpu,load_per_core", flush=True)
while True:
    time.sleep(1)
    try:
        st = server.cpu_times()
        print(f"{time.time():.3f},{server.cpu_percent():.1f},{st.user + st.system:.3f},"
              f"{server.memory_info().rss / 2**20:.0f},{gen.cpu_percent():.1f},"
              f"{psutil.cpu_percent():.1f},{os.getloadavg()[0] / ncpu:.2f}", flush=True)
    except psutil.NoSuchProcess:
        break
```

```bash
#!/bin/bash
# usage: ./run.sh <label> <SEARCH_CACHE 0|1> [locustfile]  -> requests_<label>.csv, counters_<label>.csv
set -e
LABEL=$1; LF=${3:-locustfile.py}
SEARCH_CACHE=$2 uvicorn app:app --port 8077 --log-level warning &
SRV=$!; sleep 3                                              # server up
LABEL=$LABEL locust -f $LF --headless --host http://127.0.0.1:8077 --only-summary > locust_$LABEL.txt 2>&1 &
LOC=$!; sleep 0.5
python monitor.py $SRV $LOC > counters_$LABEL.csv &          # 1 s counter samples
MON=$!
wait $LOC || true
kill $MON $SRV
```

</details>

Practitioner rules of thumb name the red flags to look for:

| Counter | Red flag | Why |
|---|---|---|
| CPU of the serving resource | above 70% on average | response time climbs steeply beyond it, *R* = *S*/(1 − *U*) |
| Disk utilization | above 20% | I/O is becoming the bottleneck |
| Run queue | more than 2 per processor | runnable work is waiting for a CPU |
| Memory (RSS) | rises at constant load | a leak |
| Load generator CPU | near 100% | the tool, not the system, limits throughput |

The service runs as **one process with one event loop**, so its CPU is read as a share of one core.

## 5. Run, and find the knee

```bash
python make_db.py            # the 200,000-book catalogue
./run.sh before 0            # step load, cache off: requests_before.csv, counters_before.csv
python steps.py before
```

`steps.py` computes per step, over its steady part, *X* = requests / window, per-route percentiles and *U* = CPU seconds / window; the same output feeds [Worked Case: From Counters to Capacity](../queuing/worked-case.md):

<details markdown="1">
<summary markdown="span">The complete `steps.py`</summary>

```python
# Per-step table from one load-test run: python steps.py <label> [users,users,...]
# Reads requests_<label>.csv (time,route,ms,failed) and counters_<label>.csv (1 s samples).
import sys, csv
import numpy as np
label = sys.argv[1]
STEPS = [int(u) for u in sys.argv[2].split(",")] if len(sys.argv) > 2 else [1, 25, 50, 100, 150, 200, 250, 300]
STEP_S, SKIP = 60, 20                                         # step length, warm-up (s)
req = np.genfromtxt(f"requests_{label}.csv", delimiter=",", dtype=None, encoding=None)
cnt = list(csv.DictReader(open(f"counters_{label}.csv")))
t0 = min(r[0] for r in req)                                   # the test's first request
print("users  T_s     C    X/s  p50_book p95_book p50_srch p95_srch err   B_s     U   D_ms  R_ms  "
      "machine% load/core locust_U rss_MB")
for i, users in enumerate(STEPS):
    a, b = t0 + i * STEP_S + SKIP, t0 + (i + 1) * STEP_S      # steady part of the step
    c = [r for r in cnt if a <= float(r["t"]) < b]
    ta, tb = float(c[0]["t"]), float(c[-1]["t"])               # window = first to last sample
    w = [r for r in req if ta <= r[0] < tb]                    # requests completed in it
    T, C = tb - ta, len(w)
    B = float(c[-1]["server_cpu_s"]) - float(c[0]["server_cpu_s"])   # server CPU seconds
    X, U = C / T, B / T                                        # throughput, utilization
    pc = [np.percentile([r[2] for r in w if r[1] == k] or [np.nan], q) for k in ("/books/[id]", "/search") for q in (50, 95)]
    print(f"{users:5d} {T:5.1f} {C:5d} {X:6.1f} {pc[0]:8.0f} {pc[1]:8.0f} {pc[2]:8.0f} {pc[3]:8.0f} "
          f"{sum(r[3] for r in w):3d} {B:6.2f} {U:5.2f} {U / X * 1000:5.1f} "
          f"{np.mean([r[2] for r in w]):5.1f} {np.mean([float(r['machine_cpu']) for r in c]):8.0f} "
          f"{max(float(r['load_per_core']) for r in c):9.2f} "
          f"{np.mean([float(r['locust_cpu']) for r in c]) / 100:8.3f} "
          f"{max(float(r['server_rss_mb']) for r in c):6.0f}")
```

</details>

```text
users    X/s  p50_book p95_book p50_srch p95_srch err     U   machine% load/core locust_U rss_MB
    1    0.5        3        4       59       61   0  0.01       10      0.19    0.001    111
   25   12.2        2       44       51       65   0  0.16        9      0.16    0.014    112
   50   24.9        1       48       43       80   0  0.27       11      0.16    0.017    113
  100   49.5        1       47       38       73   0  0.43       12      0.18    0.020    113
  150   73.7        6       64       40       90   0  0.55       13      0.18    0.023    114
  200   97.9       22      105       54      141   0  0.70       15      0.20    0.028    115
  250  118.7       83      263      115      281   0  0.90       17      0.20    0.031    116
  300  131.7      226      463      243      477   0  0.98       16      0.21    0.034    116
```

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 440, "height": 220,
  "data": {"values": [
    {"u":1,"run":"before fix","p":4},{"u":25,"run":"before fix","p":44},{"u":50,"run":"before fix","p":48},{"u":100,"run":"before fix","p":47},
    {"u":150,"run":"before fix","p":64},{"u":200,"run":"before fix","p":105},{"u":250,"run":"before fix","p":263},{"u":300,"run":"before fix","p":463},
    {"u":1,"run":"after fix","p":2},{"u":25,"run":"after fix","p":4},{"u":50,"run":"after fix","p":3},{"u":100,"run":"after fix","p":3},
    {"u":150,"run":"after fix","p":3},{"u":200,"run":"after fix","p":2},{"u":250,"run":"after fix","p":2},{"u":300,"run":"after fix","p":1}]},
  "layer": [
    {"mark": {"type": "line", "point": true, "strokeWidth": 3},
     "encoding": {
       "x": {"field": "u", "type": "quantitative", "title": "active users", "scale": {"domain": [0, 300]}},
       "y": {"field": "p", "type": "quantitative", "title": "p95 of /books/[id] (ms)", "scale": {"domain": [0, 500]}},
       "color": {"field": "run", "type": "nominal", "title": null, "scale": {"domain": ["before fix", "after fix"], "range": ["#b42318", "#019546"]}, "legend": {"orient": "top"}}}},
    {"data": {"values": [{"y": 100}]}, "mark": {"type": "rule", "strokeDash": [6, 3], "color": "#2D6E2A"},
     "encoding": {"y": {"field": "y", "type": "quantitative"}}}
  ],
  "config": {"font": "Tahoma, sans-serif", "view": {"stroke": null}}
}
```

_Measured p95 of the fast route per step, before and after the fix in step 7; dashed line, the 100 ms target. Steady 40 s of each 60 s step, one run each._

Throughput rises in proportion to users up to 200 (each 50 users add about 24 req/s), then adds only 21 and 13: the bend of the curve lies between 200 and 250 users. Past it, the book-page p95 rises 2.5 times, then 1.8 times, while throughput gains less and less. (The *M*\* = 268 users of the queuing worked case is where the two straight-line bounds cross; a real curve bends earlier.)

**Decision point: does it meet the target at 200 users?** Criterion: the step 1 target and margin rule, at the step 2 load. Book pages have p95 = 105 ms against 100 ms: **fail**. CPU is at 70% against the 63% that passing needs, so there is no margin either, and 25% more users push p95 to 263 ms. Searches (141 ms) pass and there are no errors. Memory is flat (111–116 MB: no leak) and Locust used under 4% of a core (the run is valid).

## 6. Diagnose

First, the machine-wide CPU averages at most 17% per step and the load per core stays at or below 0.21, yet the server process reaches 98% of its one core: the bottleneck is the single event loop, not the machine. Second, a book view takes 1–3 ms at the median but 44 ms at p95 with only 25 users. An in-memory lookup cannot take 44 ms on its own; it is **waiting**. Because the search query runs synchronously inside an `async` handler, every request that arrives during a 30 ms search waits for it to finish: head-of-line blocking. In the anti-pattern catalogue this is a One-Lane Bridge: all traffic must pass one narrow point {% cite smith2000antipatterns %} ([Performance Bugs and Anti-Patterns](bugs.md)).

An isolation test confirms which route consumes the core: one route at a time for 60 s, searches with 40 users and book views with 200, both with 1–3 s think time, run with `ROUTE=search USERS=40 ./run.sh iso_search 0 iso.py` and analysed with `python steps.py iso_search 40` (columns trimmed; *D* = *U*/*X* is the CPU time per request):

<details markdown="1">
<summary markdown="span">`iso.py`</summary>

```python
# Isolation runs: one route at a time, 60 s at a fixed user count.
import os, random, time
from locust import HttpUser, LoadTestShape, task, between, events
from locustfile import TERMS, WEIGHTS
ROUTE, USERS = os.environ["ROUTE"], int(os.environ["USERS"])

class OneRoute(HttpUser):
    wait_time = between(1, 3)
    @task
    def go(self):
        if ROUTE == "search":
            self.client.get(f"/search?q={random.choices(TERMS, weights=WEIGHTS)[0]}", name="/search")
        else:
            self.client.get(f"/books/{random.randint(1, 200000)}", name="/books/[id]")

class Flat(LoadTestShape):
    def tick(self):
        return (USERS, 50) if self.get_run_time() < 60 else None
```

</details>


```text
users    X/s  p50_book p95_book p50_srch p95_srch     U   D_ms
   40   19.4      nan      nan       44      117  0.61   31.3     (search only)
  200   99.4        1        1      nan      nan  0.02    0.2     (books only)
```

A search costs about 150 times the CPU of a book view (31.3 against 0.21 ms); at a 25% share, searches take 98% of the server's work.

## 7. Fix the cause, then re-run

Three fixes were considered; an index cannot serve `LIKE '%term%'`.

| Option | Effect on the bottleneck | Verdict |
|---|---|---|
| Make the handler `def` (thread pool) | frees the event loop while the query runs, but each search still costs 31 ms of CPU | partial |
| A second worker process | doubles capacity, doubles memory; each worker still blocks | fallback |
| Cache search results | ten terms make up all searches in the log, so nearly every search is a dictionary hit | **chosen** |

The cache is already in `app.py` behind a switch: `./run.sh after 1` starts the server with `SEARCH_CACHE=1`, and the search handler then calls `cached_search`. The re-run, same script and procedure (rows trimmed):

```text
users    X/s  p50_book p95_book p50_srch p95_srch err     U
    1    0.5        1        2        1       26   0  0.00
  100   49.6        2        3        2        4   0  0.03
  200  100.2        1        2        1        3   0  0.04
  300  150.4        0        1        1        1   0  0.03
```

Throughput now follows the user count to 300 users, and the server uses 4% of a core at the peak. The 26 ms search p95 at 1 user rests on five searches; each of the ten terms misses the cache once, and one such miss fell in that window.

**Decision point: ship?** Criterion: the step 1 target and margin rule at 200 users, by the same measurement. Book pages p95 = 2 ms, searches 3 ms, no errors, CPU 4%: **pass, with margin; ship the fix.** The team also adds the 200-user step as a smoke test on every build.

## 8. The artifact: a load-test record

Copy this record for every load test; the right column is this case.

| Field | Catalogue service |
|---|---|
| Target | at 200 active users: p95 ≤ 100 ms (books), ≤ 200 ms (search), 0 errors; server CPU ≤ 70% |
| Workload source | access log, busiest 15 min: 99 req/s, 75/25 mix, Z = 2.0 s, 200 users |
| Script and shape | Locust `Shopper`, steps 1–300 users, 60 s each, first 20 s discarded |
| Knee | curve bends between 200 and 250 users; 98% CPU at 300 |
| Result before | books p95 105 ms at 200 users, CPU 70%: fail, no margin |
| Cause | synchronous full-scan search in the event loop; search = 98% of CPU work |
| Fix | cache search results (10 terms) |
| Result after | books p95 2 ms, search 3 ms, CPU 4% at 200 users: pass |
| Follow-up | smoke test at 200 users per build; alert if the cache hit ratio falls |

## Common mistakes

These mistakes recur in load tests; each has a symptom you can check.

1. **Reading machine-wide CPU.** At 300 users it said 16% while the service's one core was 98% busy. Spot it: compare the process's CPU with the number of cores it can use.
2. **Reporting the whole run.** Locust's end-of-run summary mixes all steps: it reported a book-page p95 of 390 ms for this run, which matches no load level. Spot it: a single row of numbers for a step-load test.
3. **Testing the cache instead of the system.** A fixed book id, or one search term, makes every request a cache hit. Spot it: fewer distinct values in the script than in the log.
4. **A saturated load generator.** If Locust's own CPU nears 100%, throughput flattens for the wrong reason. Spot it: record the generator's CPU next to the server's.

## How solid is this?

- **Where it comes from.** The test-case steps follow Weyuker and Vokolos {% cite weyuker2000experience %}, the procedure Liu's process {% cite liu2011performance %}, the user model and report the Locust documentation {% cite locust_docs %}.
- **What is a rule of thumb.** The red-flag thresholds are practitioner rules, not measured laws; the 70% CPU line follows from the M/M/1 curve, and the 10% noise rule is Wilson's advice {% cite wilson2008operations %}.
- **What the numbers are.** One run per configuration on one laptop with other programs running. Rerun from clean copies of the printed files, the step load gave book p95 114 ms at 200 users, and the search-only run's *D* fell from 30.5 to 1.1 ms with `SEARCH_CACHE=1`. The shape (throughput levelling off near 1/*D* = 133 req/s, a fast route blocked by a slow one) is the finding, not the milliseconds. A Universal Scalability Law fit to the eight points gave a coherence term about equal to its standard error, so no peak *N*\* is claimed ([Scalability Laws](scalability.md)).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
