---
title: Prometheus
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 17
layout: default
page_type: tool-card
tested_with: Prometheus 3.15.1, prometheus-client 0.26.0, Locust 2.47.0, Podman 6.1.4
---

# Prometheus

**Prometheus** is a monitoring system: every few seconds it fetches ("scrapes") a service's metrics from its `/metrics` endpoint, stores them as time series, and answers questions in its query language, PromQL {% cite prometheus_docs %}. Python services publish metrics with `prometheus-client` {% cite prometheus_client_python %}.

The quality question it answers: *what did the service itself measure while it was under load?* [Locust](locust.md) reports what clients saw; Prometheus, what the server saw.

*Run for this card with Prometheus 3.15.1, prometheus-client 0.26.0, Locust 2.47.0, Podman 6.1.4, October 2026.*

## Install and quick start

`src/parking/monitored.py` wraps the Locust card's service with a request counter, a latency histogram and `/metrics`. (Constructed example.)

```python
import time

from flask import g, request
from prometheus_client import Counter, Histogram, make_wsgi_app
from werkzeug.middleware.dispatcher import DispatcherMiddleware

from parking.app import app

REQUESTS = Counter("http_requests_total", "Requests served",
                   ["route", "status"])
LATENCY = Histogram("http_request_duration_seconds", "Time to serve a request",
                    ["route"],
                    # boundaries around /history's 200 ms: see Common mistakes
                    buckets=[0.005, 0.01, 0.05, 0.1, 0.19, 0.2, 0.21, 0.22, 0.25, 0.5, 1])


@app.before_request
def start_timer():
    g.start = time.perf_counter()


@app.after_request
def record(response):
    route = request.url_rule.rule if request.url_rule else "unknown"
    LATENCY.labels(route).observe(time.perf_counter() - g.start)
    REQUESTS.labels(route, str(response.status_code)).inc()
    return response


app.wsgi_app = DispatcherMiddleware(app.wsgi_app, {"/metrics": make_wsgi_app()})
```

`Containerfile.monitor`:

```dockerfile
FROM python:3.12-slim
WORKDIR /app
RUN pip install --no-cache-dir flask==3.1.3 prometheus-client==0.26.0
COPY src/ ./src/
ENV PYTHONPATH=/app/src
USER 1000
EXPOSE 5000
CMD ["flask", "--app", "parking.monitored", "run", "--host", "0.0.0.0", "--port", "5000"]
```

Prometheus reads `prometheus.yml`:

```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: parking
    static_configs:
      - targets: ["parking:5000"]
```

Both run on one container network, where Prometheus finds the service by name:

```bash
podman rm -f parking                  # the container card's container, if still running
podman build -t parking-mon:0.2 -f Containerfile.monitor .
podman network create mon
podman run -d --name parking --network mon -p 8080:5000 --cpus 1 --memory 256m parking-mon:0.2
podman run -d --name prom --network mon -p 9090:9090 \
  -v ./prometheus.yml:/etc/prometheus/prometheus.yml:Z docker.io/prom/prometheus:v3.15.1
# :Z relabels the file so the container may read it
# the monitor image skips the pip removal and health check of the Trivy card
```

In `http://localhost:9090`, **Status → Target health** should show `parking` up. Run the load (longer `-t`, more time to query):

```bash
poetry run locust -f locustfile.py --headless -u 20 -r 5 -t 90s --host http://localhost:8080 --only-summary
```

Use the **Query** page during the run's last seconds, while the one-minute window holds only load.

## Reading the output

The queries and their results:

```text
# rate(x[1m]): per-second increase of counter x over the last minute
# sum by (route): add the series up, keeping one result per route
# a histogram publishes _count (requests), _sum (seconds in total) and
# _bucket{le="0.21"}: requests that took at most (le: less or equal) 0.21 s;
# histogram_quantile estimates a percentile from the buckets, so keep le

# throughput per route and status
sum by (route, status) (rate(http_requests_total[1m]))
    /fee 200: 7.98/s    /fee 400: 2.36/s    /history 200: 2.64/s

# mean latency per route, in seconds
sum by (route) (rate(http_request_duration_seconds_sum[1m]))
  / sum by (route) (rate(http_request_duration_seconds_count[1m]))
    /fee 0.0002    /history 0.2026

# 95th percentile per route, in seconds
histogram_quantile(0.95, sum by (le, route) (rate(http_request_duration_seconds_bucket[1m])))
    /history 0.2096    (/fee 0.0047: see the bucket mistake below)

# CPU used, in cores
rate(process_cpu_seconds_total[1m])
    0.0242
```

In the same 90-second run (its Locust table is not printed), Locust measured `/history` at 208 ms on average and `/fee` at 5–6 ms; the server measured 202.6 ms and 0.2 ms. The difference is network, port forwarding and the client.

The CPU line feeds the [operational laws](../../attributes/queuing/operational-laws.md): at a total of 12.98 requests per second, the service used 0.0242 of a core, so each request cost about 0.0242 / 12.98 ≈ 1.9 ms of CPU (utilization law, $$D = U / X$$; U here is a share of one core, the `--cpus 1` limit). That exceeds `/fee`'s 0.2 ms because `process_cpu_seconds_total` counts the whole process: web server, request log and scrapes, which the histogram does not time. The rest of `/history`'s 200 ms is waiting, which uses no CPU.

## Common mistakes

- **Default histogram buckets.** With the library's default buckets, an earlier run under the same load gave a median of 175 ms for `/history`, although every request took about 200 ms: `histogram_quantile` can only interpolate inside a bucket, and 200 ms fell in the 100–250 ms bucket. Put boundaries around the latencies you care about; a quantile is never finer than its bucket (`/fee`'s 4.7 ms "p95" only says "under 5 ms").
- **Labelling by raw path.** `/tickets/8812` as a label makes one time series per ticket. Label by route template.
- **Expecting process metrics everywhere.** They were published inside the Linux container; run on macOS (`poetry add prometheus-client`, then `flask --app parking.monitored run`), the same library published no process metrics at all, because it reads them from Linux's `/proc`.
- **A `rate` window shorter than the scrapes.** `rate` needs two samples in its window; at a 5 s scrape interval, `rate(http_requests_total[5s])` returned nothing.
- **Reading a counter as a rate.** A counter's raw value is the total since the process started, reset on restart. Wrap counters in `rate`, or `increase` (the total growth over the window).

## In CI

Prometheus watches running systems. A load-test job can still send these queries to its API (`curl localhost:9090/api/v1/query --data-urlencode 'query=…'`) and fail when the server-side p95 misses its target.

## Go deeper

- [Operational Laws](../../attributes/queuing/operational-laws.md) and [Worked Case: From Counters to Capacity](../../attributes/queuing/worked-case.md): from counters like these to capacity.
- [Load Testing with Locust](../../attributes/performance/load-testing.md): the client side of the same run.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
