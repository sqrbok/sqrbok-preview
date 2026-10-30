---
title: Bandit and pip-audit
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 11
layout: default
page_type: tool-card
tested_with: Bandit 1.9.4, pip-audit 2.10.1
---

# Bandit and pip-audit

**Bandit** is a static security analyser for Python: it parses each file and reports code patterns known to cause weaknesses, such as a shell command built from a string or a weak hash {% cite bandit_docs %}. **pip-audit** checks the other half, your dependencies: it looks each pinned package version up in a vulnerability database {% cite pipaudit_docs %}.

The quality question they answer: *does my code, or a package I depend on, contain a known security weakness?*

*Run for this card with Bandit 1.9.4, pip-audit 2.10.1, October 2026.*

## Install and quick start

```bash
poetry add --group dev bandit
poetry run bandit -r src/                 # -r: every file under src/, recursively
poetry run bandit -r src/ -ll             # medium severity and above only
poetry add --group dev pip-audit
poetry run pip-audit                      # audits the project's environment
```

## Reading the output

A small export module, `src/parking/export.py` (constructed example):

```python
import hashlib
import pickle
import subprocess


def archive(day: str) -> None:
    subprocess.run(f"tar czf /tmp/parking-{day}.tgz logs/{day}", shell=True)


def ticket_id(plate: str) -> str:
    return hashlib.md5(plate.encode()).hexdigest()[:8]


def load_state(blob: bytes) -> dict:
    return pickle.loads(blob)
```

Bandit reported five issues. One of them in full:

```text
>> Issue: [B602:subprocess_popen_with_shell_equals_true] subprocess call with shell=True identified, security issue.
   Severity: High   Confidence: High
   CWE: CWE-78 (https://cwe.mitre.org/data/definitions/78.html)
   Location: src/parking/export.py:7:4
```

- **Test ID** (`B602`): the rule; its documentation explains the risk and the fix.
- **Severity:** how bad it is if real. **Confidence:** how sure Bandit is that it is real.
- **CWE:** the weakness class. CWE-78 is OS command injection: a `day` of `x; rm -rf ~` runs a second command.

The others were `B324` (MD5, High), `B301` (`pickle.loads` on data from outside, Medium) and two Low findings for merely importing `pickle` (`B403`) and `subprocess` (`B404`). With `-ll`, only the three medium-and-high findings remained.

**After the fixes**: an argument list without `shell=True` (keep the `/tmp` path out of it too: written as a plain string in the list, Bandit reports it as `B108`, hard-coded temporary directory), `usedforsecurity=False` for an MD5 that only shortens a plate into an ID, `json` instead of `pickle`, and a `# nosec` with its test ID and reason on the reviewed import:

```python
import hashlib
import json
import subprocess  # nosec B404 - fixed argument list, no shell


def archive(day: str) -> None:
    subprocess.run(["tar", "czf", f"parking-{day}.tgz", f"logs/{day}"], check=True)


def ticket_id(plate: str) -> str:
    return hashlib.md5(plate.encode(), usedforsecurity=False).hexdigest()[:8]


def load_state(blob: bytes) -> dict:
    return json.loads(blob)
```

Bandit then raised two new Low findings on the `subprocess.run` line: `B603` (check the input) and `B607` (`tar` is found through `PATH`). Fixing one finding often exposes the next; triage is a loop.

On the example project, `poetry run pip-audit` printed `No known vulnerabilities found` (and skipped the project itself, which is not on PyPI). On a requirements file with two old pins, `jinja2==3.1.2` and `requests==2.31.0`, run as `pip-audit -r requirements.txt --disable-pip --no-deps` (audit the listed pins as they are, without installing them into a temporary environment):

```text
Name     Version ID              Fix Versions
-------- ------- --------------- ------------
jinja2   3.1.2   PYSEC-2026-1473 3.1.3
...
Found 16 known vulnerabilities in 2 packages
```

The fix is in the last column. A package can have several entries; upgrade to the highest fix version listed for it (jinja2 needed 3.1.6 to clear all of its entries).

## Common mistakes

- **A bare `# nosec`.** It hides every finding on the line, including future ones. Write the test ID and a reason: `import subprocess  # nosec B404 - fixed argument list, no shell`. Bandit then counts it under "skipped".
- **Gating on every Low.** Import warnings (`B403`, `B404`) are reminders, not defects. Gate on `-ll` and review the Lows.
- **`safety check`.** Older instructions use it; the command is deprecated, and the current Safety CLI needs an account. pip-audit needs none.
- **Trusting a clean run.** Bandit matches patterns in one file at a time; it does not follow data between files, and it knows nothing about authorisation logic.

## In CI

```yaml
- run: poetry run bandit -r src/ -ll
```

Bandit exits 1 when it reports anything at or above the chosen level; after the fixes above, `-ll` exited 0 with the two Lows still listed in a full run.

## Go deeper

- [Static Security Analysis](../../attributes/security/static-analysis.md) and [DevSecOps and Supply Chain Security](../../attributes/security/devsecops.md): where each tool fits.
- [Trivy](trivy.md): the same question for the container image around the code.
- [Worked Case: Threat Model and SAST Triage for a Web Shop](../../attributes/security/worked-case.md): Bandit findings triaged into a fix order.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
