---
title: Locust
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 16
layout: default
page_type: tool-card
tested_with: Locust 2.47.0, Flask 3.1.3
---

# Locust

**Locust** is a load-testing tool in which simulated users are plain Python classes: each user repeatedly picks a task, sends HTTP requests, waits, and Locust records the response time and outcome of every request {% cite locust_docs %}.

The quality question it answers: *how do response time and failures behave as more users arrive?*

*Run for this card with Locust 2.47.0, Flask 3.1.3, October 2026.*

## Install and quick start

The service under test, `src/parking/app.py`, has a fast route, `/fee`, which rejects a negative stay with 400, and a slow one, `/history`, which sleeps 0.2 s to stand in for a database query. (Constructed example.)

```python
import time

from flask import Flask, abort, request

from parking.fee import fee

app = Flask(__name__)


@app.get("/fee")
def get_fee():
    minutes = request.args.get("minutes", type=int)
    if minutes is None or minutes < 0:
        abort(400)
    return {"minutes": minutes, "fee": fee(minutes)}


@app.get("/history")
def history():
    time.sleep(0.2)  # stands in for a slow database query
    return {"tickets": []}
```

Start it with `poetry run flask --app parking.app run --port 5055`, or run it in a container as on the [Podman and Docker card](containers.md) and point Locust at port 8080. The `locustfile.py`, in the project folder:

```python
from locust import HttpUser, between, task


class Driver(HttpUser):
    wait_time = between(1, 2)

    @task(3)
    def quote(self):
        self.client.get("/fee?minutes=90")

    @task(1)
    def history(self):
        self.client.get("/history")

    @task(1)
    def bad_input(self):
        with self.client.get("/fee?minutes=-5", name="/fee [invalid]",
                             catch_response=True) as resp:
            if resp.status_code == 400:
                resp.success()          # rejecting bad input is correct here
            else:
                resp.failure(f"expected 400, got {resp.status_code}")
```

`@task(3)` makes a task three times as likely as a `@task(1)`; `wait_time` is the pause between tasks; `name=` groups requests in the report.

```bash
poetry add --group dev locust
poetry run locust -f locustfile.py --headless -u 20 -r 5 -t 30s \
    --host http://localhost:5055 --csv results --only-summary
```

`-u` is the number of users, `-r` how many start per second, `-t` the run time, and `--only-summary` prints only the final tables. Without `--headless`, Locust serves a web UI on port 8089.

## Reading the output

The summary, with the columns narrowed:

```text
Type     Name              # reqs      # fails |    Avg     Min     Max    Med |   req/s  failures/s
GET      /fee [invalid]        77     0(0.00%) |      3       1       8      4 |    2.58        0.00
GET      /fee?minutes=90      228     0(0.00%) |      3       1      21      4 |    7.63        0.00
GET      /history              69     0(0.00%) |    208     204     212    210 |    2.31        0.00
         Aggregated           374     0(0.00%) |     41       1     212      4 |   12.52        0.00
```

Times are in milliseconds. A second table gives percentiles (50% to 100%) per route, and `--csv results` writes both to `results_stats.csv` for later analysis. Read the **per-route rows** and the **high percentiles**: `/history` takes about 210 ms on every request, while the aggregated median of 4 ms describes neither route.

## Common mistakes

- **Expected errors counted as failures.** Without `catch_response`, a run of the same length reported `74(100.00%)` failures on the invalid-input route and exited with code 1, because Locust counts every 4xx and 5xx response as a failure. When rejecting the input is the correct behaviour, mark it a success.
- **Reading the throughput as the server's capacity.** 20 users who wait 1.5 s on average between requests can send at most about 20 / 1.5 ≈ 13 requests per second; the run measured 12.5. That is the users' pace, not a limit of the server. To find the capacity, raise `-u` until response times climb.
- **Load generator and service on one laptop.** Both compete for the same CPU, so a slow result may be Locust's. Watch the generator's CPU, limit the service (for example with a container's `--cpus`), or run them on separate machines.
- **One URL for every request.** Requests to the same URL can be served from a cache; vary the inputs the way real users would, and group them with `name=`.

## In CI

In a workflow, start the service in the background first (for example `podman run -d … parking:0.1`, or the Flask command followed by `&`), then a short headless run (`-t 30s`) can gate the build: Locust exits 1 if any request failed. For response-time thresholds, add a small check on `results_stats.csv`, or a `quitting` event hook in the locustfile that sets `environment.process_exit_code` when, say, the 95th percentile is too high.

## Go deeper

- [Load Testing with Locust](../../attributes/performance/load-testing.md): designing the load, and what to measure.
- [Worked Case: Load Testing a Catalogue Service](../../attributes/performance/worked-case.md): a load test from plan to verdict.
- [Worked Case: From Counters to Capacity](../../attributes/queuing/worked-case.md): why throughput levels off as users grow.
- [Prometheus](prometheus.md): the server's side of the same run.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
