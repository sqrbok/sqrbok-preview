---
title: Podman and Docker
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 3
layout: default
page_type: tool-card
tested_with: Podman 6.1.4 (rootless, macOS machine), Docker CLI 29.8 against Podman's socket
---

# Podman and Docker

**Podman** and **Docker** build and run containers: a service packaged with its runtime and libraries as an image, started as an isolated process with its own file system and network {% cite podman_docs docker_docs %}. The commands are the same; this card uses `podman`, and every command works unchanged with `docker`.

The quality question it answers: *does the service run the same way on every machine?* A load or security test is only repeatable if the system under test is.

*Run for this card with Podman 6.1.4 (rootless, macOS machine), Docker CLI 29.8 against Podman's socket, October 2026.*

**Which one?** Podman runs without a background daemon and, by default, **rootless**: a container cannot do more than your own user could. Docker's daemon runs as root unless configured otherwise. On macOS and Windows both run containers inside a small Linux virtual machine (`podman machine init`, or Docker Desktop).

## Install and quick start

```bash
brew install podman          # Linux: your package manager; Windows: winget install RedHat.Podman
podman machine init && podman machine start   # macOS and Windows only
```

A `Containerfile` (Docker calls it `Dockerfile`; both tools read either) for the example Flask service, whose code, `src/parking/app.py`, is printed on the [Locust card](locust.md); build the image once that file exists:

```dockerfile
FROM python:3.12-slim
WORKDIR /app
RUN pip install --no-cache-dir flask==3.1.3
COPY src/ ./src/
ENV PYTHONPATH=/app/src
EXPOSE 5000
CMD ["flask", "--app", "parking.app", "run", "--host", "0.0.0.0", "--port", "5000"]
```

The image pins only Flask, to keep the file short; for a real service, export the lock file (`poetry export -f requirements.txt`, from the `poetry-plugin-export` plugin) and install from it, so the container runs the versions you tested.

```bash
podman build -t parking:0.1 -f Containerfile .
podman run -d --name parking -p 8080:5000 --cpus 1 --memory 256m parking:0.1
curl "localhost:8080/fee?minutes=90"          # {"fee":4,"minutes":90}
```

## Reading the output

{% raw %}
```text
$ podman ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}"
NAMES       IMAGE                  STATUS        PORTS
parking     localhost/parking:0.1  Up 3 seconds  0.0.0.0:8080->5000/tcp

$ podman stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
NAME        CPU %       MEM USAGE / LIMIT
parking     2.27%       22.24MB / 268.4MB
```
{% endraw %}

`0.0.0.0:8080->5000/tcp` reads "port 8080 on your machine forwards to port 5000 in the container". The limit column shows the `--memory` cap. `podman logs parking` prints what the service wrote; `podman stop parking` and `podman rm parking` clean up.

## Common mistakes

- **The service listens on 127.0.0.1 inside the container.** Started without `--host 0.0.0.0`, Flask logged `Running on http://127.0.0.1:5000`, and `curl` from the host got `Empty reply from server`. Inside a container, 127.0.0.1 is the container itself.
- **No resource limits in a performance test.** Without `--cpus` and `--memory`, the service uses whatever your laptop has free, and the numbers change with what else is running.
- **The wrong CPU architecture.** An image built on an Apple Silicon Mac is `arm64` (`podman image inspect … --format '{% raw %}{{.Architecture}}{% endraw %}'`); a project that publishes separate `amd64` and `arm64` images needs the one for your machine.
- **Compose.** `podman compose` hands the file to an installed compose provider; on the test machine it ran `docker-compose` v5.5.1.

## In CI

GitHub's Ubuntu runners have both tools installed, so `podman build` and `podman run` work in a workflow step as they do locally.

## Go deeper

- [Load Testing with Locust](../../attributes/performance/load-testing.md): a containerised service under load.
- [Secure Design Principles](../../attributes/security/secure-design.md): least privilege, which rootless containers apply.
- [Trivy](trivy.md): scanning the image and the Containerfile.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
