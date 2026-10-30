---
title: Trivy
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 12
layout: default
page_type: tool-card
tested_with: Trivy 0.76.1, Podman 6.1.4
---

# Trivy

**Trivy** is a security scanner for what you ship around your code: it lists the operating-system and language packages inside a container image and looks each one up in vulnerability databases, and it checks a `Containerfile` and other configuration files for risky settings {% cite trivy_docs %}.

The quality question it answers: *does the image I deploy carry known vulnerabilities or an unsafe configuration?* [Bandit](bandit.md) and pip-audit never see the Debian packages under your code.

*Run for this card with Trivy 0.76.1, Podman 6.1.4, October 2026.*

## Install and quick start

```bash
brew install trivy                       # other systems: see the Trivy documentation
trivy image IMAGE                        # Docker, Podman or a registry (on macOS with Podman: next two lines)
podman save -o parking.tar localhost/parking:0.1
trivy image --input parking.tar          # scan an image saved as a file
trivy config Containerfile               # check the build file
trivy fs --scanners vuln .               # only vulnerabilities, in the project's lock files
```

The first run downloads the vulnerability database. On the example project, `trivy fs` found 0 vulnerabilities in `poetry.lock`.

The image is the example service from the [Podman and Docker card](containers.md). (Constructed example.)

## Reading the output

```text
parking.tar (debian 13.7)
=========================
Total: 164 (UNKNOWN: 1, LOW: 61, MEDIUM: 58, HIGH: 44, CRITICAL: 0)

│ Library   │ Vulnerability  │ Severity │ Status       │ Installed Version │ Fixed Version │
│ perl-base │ CVE-2026-9538  │ HIGH     │ fix_deferred │ 5.40.1-6+deb13u1  │               │
...
Python (python-pkg)
===================
Total: 6 (UNKNOWN: 0, LOW: 1, MEDIUM: 5, HIGH: 0, CRITICAL: 0)

│ pip (METADATA) │ CVE-2025-8869  │ MEDIUM   │ fixed  │ 25.0.1 │ 25.3 │

# Status: affected = no fix released; fix_deferred = the distribution put the
# fix off; fixed = a fixed version exists. UNKNOWN: no source rated the severity.
```

All 164 Debian findings come from the `python:3.12-slim` base image, and **none had a fix**: 162 were `affected` and 2 `fix_deferred`. The six Python findings were in `pip`, which the service does not need at run time; all had a fix. Flask was clean.

`trivy config Containerfile` reported two misconfigurations: `DS-0002 (HIGH)`, no `USER`, so the service runs as root, and `DS-0026 (LOW)`, no `HEALTHCHECK`.

**The fixed image**, `Containerfile.fixed`, removes pip after installing Flask, runs as user ID 1000 instead of root, and adds a health check:

```dockerfile
FROM python:3.12-slim
WORKDIR /app
RUN pip install --no-cache-dir flask==3.1.3 \
 && pip uninstall -y pip
COPY src/ ./src/
ENV PYTHONPATH=/app/src
USER 1000
EXPOSE 5000
HEALTHCHECK CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:5000/fee?minutes=0')"
CMD ["flask", "--app", "parking.app", "run", "--host", "0.0.0.0", "--port", "5000"]
```

Built with `podman build --format docker -t parking:0.3 -f Containerfile.fixed .` (see the mistakes below) and started with `podman run -d --name p3 -p 8083:5000 parking:0.3`, `podman ps` showed the container `(starting)` and, after the first check (the default interval is 30 seconds), `(healthy)`. Its scan showed no Python findings and a clean configuration; the 164 base-image findings stayed, because nothing in the project can fix them.

## Common mistakes

- **Expecting a red build.** Trivy exits 0 by default, even with 164 findings; add `--exit-code 1`.
- **A gate that passes despite 44 HIGH.** `--severity HIGH,CRITICAL --ignore-unfixed --exit-code 1` (skip findings with no released fix) exited 0 on this image: all 44 HIGH findings were unfixed. A fair gate (fail on what you can fix), but report the unfixed count and weigh it when choosing a base image.
- **Upgrading instead of removing.** An image `parking:0.2` with `RUN pip install --no-cache-dir --upgrade pip` before the Flask install moved pip to 26.2.2 and cleared its six findings, but its bundled list of vendored packages added eight new ones (five HIGH, in vendored `urllib3`, `msgpack` and `setuptools`). A tool the service does not use is safer gone than updated.
- **Trivy cannot see the image.** On macOS, `trivy image localhost/parking:0.1` failed with `no podman socket found`: it looks for Docker's or Podman's socket where Linux puts them. Save the image to a file and use `--input`.
- **A HEALTHCHECK that does nothing.** Podman builds OCI images by default, and the build warned `HEALTHCHECK is not supported for OCI image format and will be ignored`. Build with `podman build --format docker` to keep it; the container then showed `(healthy)`.

## In CI

```yaml
- run: podman build --format docker -t parking:ci -f Containerfile.fixed .
- run: podman save -o image.tar parking:ci
- run: trivy image --input image.tar --severity HIGH,CRITICAL --ignore-unfixed --exit-code 1
- run: trivy config --exit-code 1 Containerfile.fixed
```

Install Trivy on the runner first (its documentation has a GitHub action). It downloads its database on each fresh run; the same image can fail tomorrow without any change on your side, because a new CVE was published.

## Go deeper

- [DevSecOps and Supply Chain Security](../../attributes/security/devsecops.md): where image scanning fits among the other checks.
- [From Severity to Fix Order](../../attributes/security/cvss-context.md): why a HIGH in a package nobody calls can wait.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
