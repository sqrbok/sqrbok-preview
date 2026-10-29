---
title: "Worked Case: A Quality Plan for a Booking Service"
parent: Quality Planning
nav_order: 8
layout: default
page_type: worked-case
---

# Worked Case: A Quality Plan for a Booking Service

This worked case writes a quality plan for one small service and runs its gates on the first increment. Theory: [The Quality Plan](01-quality-management-planning.md), [Criticality and Quality Gates](03-quality-gates-criticality.md) and [Estimating Test Effort](04-test-effort-estimation.md).

**Example service.** A team of four (a tech lead, two developers and a tester) builds a room-booking API for a small hotel chain in Python with FastAPI and SQLite; a release takes six weeks. Modules: `api.py` (endpoints), `rules.py` (prices, overlap check, refunds) and `db.py` (storage); payments go to an external provider. _(Constructed example. The service was written and every command run for this page with Python 3.12.9, radon 6.0.1, flake8 7.5.0, bandit 1.9.4, pytest 9.2.0 and pytest-cov 7.1.0.)_

## 1. Rate each module's criticality

Each module is assigned by its worst plausible failure, rated on IEEE 829's harm scale and mapped as on [Criticality and Quality Gates](03-quality-gates-criticality.md): negligible or marginal → class A, critical → B, catastrophic → C {% cite miranda2015asa ieee829-2008 %}.

| Module | Worst plausible failure | Harm | Class |
|---|---|---|---|
| `db.py` | bookings lost, or the guest list leaked through a query | critical: major financial or social loss | B |
| `rules.py` | a wrong refund rule applied to every cancellation; double bookings | critical: major financial loss, repeated until noticed | B |
| `api.py` | an endpoint fails; staff take bookings by phone | marginal | A |

**Decision point: is anything class C?** Criterion: could a failure cause loss of life, mission, safety or security? The team judged a leaked guest list (names and dates) a major social loss, not a loss of system security; with card data, `db.py` would be class C.

From Miranda's class table, the plan takes these rows {% cite miranda2015asa %}: class A needs statement coverage, coding standards and all-pairs combinatorial tests; class B adds branch coverage, code inspection of every change and all-triples tests; robustness tests apply to the data-input module (`api.py`) in both classes.

## 2. Turn requirements into gate criteria

Each requirement gets a metric, threshold, command and blocking stage, by ISO/IEC 25010 characteristic {% cite iso25010_2023 %}.

| Characteristic | Metric | Threshold | Command | Blocks at |
|---|---|---|---|---|
| Maintainability | cyclomatic complexity (CC) per function | 10 or less (radon grade A or B), unless waived | `python tools/cc_gate.py` | pull request |
| Maintainability | maintainability index per file | grade A | `radon mi -s src/` | reported only |
| Maintainability | style violations | 0 | `flake8 src/ tests/` | commit, pull request |
| Functional suitability | unit and API tests | all pass | `pytest` | pull request |
| Functional suitability | line and branch coverage | 80% of `src/`; 90% of the class B modules | `pytest --cov=src --cov-branch --cov-fail-under=80`, then `coverage report --fail-under=90 --include=...` | pull request |
| Security | static security findings | 0 of medium or high severity | `bandit -q -r src/ -ll` | commit, pull request |
| Performance efficiency | 95th-percentile latency of `POST /bookings` | under 200 ms at 10 users | a `locust` script on staging | release |

Branch coverage everywhere covers both classes' coverage rows; a separate class B floor stops a well-tested `api.py` hiding a weak `rules.py`.

**One attribute worked through: maintainability.** CC is the number of linearly independent paths through a function, so it is also the number of tests that [basis path coverage](../../verif/coverage/basis.md) needs {% cite mccabe1976complexity %}.  McCabe proposed 10 as an upper limit, which is radon's B/C boundary {% cite mccabe1976complexity radon_docs %}. Miranda's table allows 20 for classes A and B; the team chose the stricter 10 because the class B modules carry branch coverage and triples, and each extra path makes both dearer. The plan's action per radon grade (the grade bands are radon's, the actions are this plan's rule):

| CC | Radon grade | Action |
|---|---|---|
| 1–5 | A | none |
| 6–10 | B | none |
| 11–20 | C | blocks the merge; refactor, or add a waiver with an expiry date |
| 21–30 | D | refactor before any other change |
| 31–40 | E | refactor before any other change |
| 41 and above | F | split the function |

## 3. Place the gates by stage

Short-lived feature branches merge into a protected, always deployable `main` only by pull request; a `hotfix/*` branch for a P1 gets a faster review but the same gates.

| Stage | Where | Gate | Blocks when |
|---|---|---|---|
| Commit | `feature/*`, on the developer's machine | `pre-commit` runs flake8 and bandit | any flake8 error, or a bandit finding of medium or high severity |
| Pull request | `feature/*` into `main` | CI job below, plus one approving review; for class B files the review uses the inspection checklist | a red step or no approval (branch protection) |
| Main | `main` after merge | the same CI job on push | red `main` is fixed or the merge reverted before anything else merges |
| Release | a tag on `main`, deployed to staging | smoke tests, the `locust` run, the bug bar of section 5 | a P1 open, more than three P2s open, or the latency threshold missed |

The files that enforce the first three stages; the YAML parses, the hooks ran with pre-commit 4.7.0, and every CI step ran in a fresh clone. `pyproject.toml` sets `pythonpath = ["src"]` for pytest, so CI installs no package. A waiver is a line in `cc-waivers.txt` (`path:[Class.]function`, expiry date, reason), added in a pull request whose reviewer approves the reason; the script honours it only for grade C and only until the expiry date:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: flake8
        name: flake8
        entry: flake8
        language: system
        types: [python]
      - id: bandit
        name: bandit
        entry: bandit -q -ll
        language: system
        types: [python]
        exclude: ^tests/
```

```yaml
# .github/workflows/quality.yml
name: quality-gate
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]
jobs:
  gate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install fastapi httpx pytest pytest-cov radon flake8 bandit
      - name: Style and security
        run: |
          flake8 src/ tests/
          bandit -q -r src/ -ll
      - name: Complexity (CC 10 or less unless waived)
        run: python tools/cc_gate.py
      - name: Tests and coverage, 80% overall
        run: pytest -q --cov=src --cov-branch --cov-fail-under=80
      - name: Coverage of class B modules, 90%
        run: >
          coverage report --fail-under=90
          --include="src/booking/db.py,src/booking/rules.py"
```

```python
# tools/cc_gate.py
"""Fail on CC above 10 unless waived; a waiver covers grade C only, up to and including its expiry date."""
import datetime
import json
import subprocess
import sys

out = subprocess.run(["radon", "cc", "-j", "-n", "C", "src/"],
                     capture_output=True, text=True, check=True).stdout
waivers = {}   # cc-waivers.txt lines: path:[Class.]function YYYY-MM-DD # reason
try:
    with open("cc-waivers.txt") as f:
        for line in f:
            fields = line.split("#")[0].split()
            if fields:
                if len(fields) < 2:
                    sys.exit(f"cc-waivers.txt: no expiry date in: {line.strip()}")
                waivers[fields[0]] = datetime.date.fromisoformat(fields[1])
except FileNotFoundError:
    pass
today = datetime.date.today()
failed = False
for path, blocks in json.loads(out).items():
    for b in blocks:
        if b["type"] == "class":   # radon also scores whole classes; gate functions and methods
            continue
        name = b["name"] if not b.get("classname") else f"{b['classname']}.{b['name']}"
        key = f"{path}:{name}"
        ok = b["rank"] == "C" and waivers.get(key, today - datetime.timedelta(days=1)) >= today
        failed |= not ok
        print(f"{'waived' if ok else 'FAIL'} {key} CC {b['complexity']} grade {b['rank']}")
sys.exit(1 if failed else 0)
```

## 4. Run the gates on the first increment

The first pull request held the three modules and seven tests. Four checks failed (output shortened):

```text
$ python tools/cc_gate.py
FAIL src/booking/rules.py:refund CC 13 grade C

$ bandit -q -r src/ -ll
>> Issue: [B608:hardcoded_sql_expressions] Possible SQL injection vector
   through string-based query construction.
   Severity: Medium   Confidence: Low
   Location: src/booking/db.py:56:14
56	    query = f"SELECT * FROM bookings WHERE guest LIKE '%{name}%'"

$ pytest -q --cov=src --cov-branch --cov-report=term-missing --cov-fail-under=80
Name                   Stmts   Miss Branch BrPart  Cover   Missing
src/booking/api.py        35      8      8      1    74%   24, 38-45, 50
src/booking/db.py         23      6      0      0    74%   42-45, 49-52, 56-57
src/booking/rules.py      31      9     24      7    67%   16, 19, 27-32, 34, 36, 39
TOTAL                     89     23     32      8    71%
FAIL Required test coverage of 80% not reached. Total coverage: 71.07%
# Missing lists line ranges, which can name more lines than Miss counts:
# a range spans blank and continuation lines that are not statements.
7 passed

$ coverage report --fail-under=90 --include="src/booking/db.py,src/booking/rules.py"
TOTAL                     54     15     24      7    69%
Coverage failure: total of 69 is less than fail-under=90
```

Each failure is a decision point:

- **`refund` at CC 13.** Criterion: grade C blocks. Nested `if` chains per tariff became a table of `(days before arrival, share)` steps read by a helper; `refund` dropped to B (6) and the new `base_share` is A (3). A waiver was refused: the rule will grow with every new tariff. (With `src/booking/rules.py:refund 2027-01-31` in `cc-waivers.txt`, the script prints `waived` and passes; with a past date it fails again.)
- **B608 in `search_by_guest`.** Criterion: medium severity or higher blocks, whatever the confidence. The guest name comes from the URL, so the finding is real; the query now passes the name as a parameter.
- **Coverage 71% overall, 69% on class B.** Criteria: 80% and 90%. Four tests for the cancel and search endpoints and the untested `refund` branches closed the gap.

After the changes every step passes: 11 tests at 100% line and branch coverage, overall and on class B (the refactoring cut `rules.py` from 31 to 23 statements, so 81 instead of 89). The 100% reflects a tiny service; the gates stay at 80% and 90%. **Decision: merge.**

## 5. Write the triage rules

Severity (the levels of [Criticality and Quality Gates](03-quality-gates-criticality.md)) is the impact; priority is when the fix happens. The team added security breaches to severity 1 {% cite page2009microsoft %}.

| Priority | Default for | Sprint rule | Release gate |
|---|---|---|---|
| P1 | severity 1: crash, data loss, a main feature unusable, or a security breach | fix now on a `hotfix/*` branch; only the product owner may interrupt the sprint | blocks the release |
| P2 | severity 2: a main feature broken, workaround exists | fix this sprint if capacity allows | at most three open, each with an owner and a documented workaround |
| P3 | severity 3: minor or cosmetic | backlog | tracked, does not block |
| P4 | an enhancement, not a defect | backlog | not tracked for the release |

The tester triages with the product owner daily. A priority may be raised (a cosmetic error every guest sees can become P2), but P1 is reserved for severity 1. Each defect also records **origin** and **where found**, which make [defect removal efficiency](../01-coq/03-defect-classification.md) computable.

## 6. Assign the roles

RACI: **R**esponsible does it, **A**ccountable decides (one per row), **C**onsulted, **I**nformed.

| Activity | Tech lead | Developers | Tester | Product owner |
|---|---|---|---|---|
| Write unit tests | A | R | C | I |
| Write API, robustness and load tests | A | C | R | I |
| Review and inspect class B changes | A | R | C | – |
| Keep the CI gates and `main` green | A | R | C | I |
| Triage defects | C | C | R | A |
| Decide the release | C | I | R | A |

## 7. Estimate the effort

Miranda's tests per function, from [Estimating Test Effort](04-test-effort-estimation.md), with the combinatorial tests a critical function adds in brackets {% cite miranda2015asa %}:

| Complexity | Non-critical | Critical |
|---|---:|---:|
| Simple (2 inputs) | 8 | 12 (8 + 4 pairs) |
| Medium (4 inputs) | 8 | 21 (8 + 13 triples) |
| Complex (6 inputs) | 8 | 40 (8 + 32 quadruples) |

Harm ratings map to the table's labels as critical or catastrophic → critical, negligible or marginal → non-critical. Availability is non-critical though it calls the critical overlap check: a wrong answer costs one retry, because creating the booking checks again. The one-input search lies outside the table; with no combinations possible it keeps the 8 boundary-value tests (judgement, as the effort page allows):

| Function | Inputs | Criticality | Tests |
|---|---|---|---:|
| Create booking | 4 (room, guest, start, end): medium | critical | 21 |
| Cancel with refund | 6 (price, start, cancellation date, tariff, group, promotion): complex | critical | 40 |
| Find bookings by guest | 1 (name): below the table | critical (the leak path) | 8 |
| Check availability (planned) | 2 (room, dates): simple | non-critical | 8 |
| Occupancy report (planned) | 2 (from, to): simple | non-critical | 8 |
| **System level** | | | **85** |

Test cases = (1 + I + U) × system-level tests; effort = test cases × (design + (1 + R) × run). With I = 0.5 for integration and U = 0 (unit tests booked as development): 1.5 × 85 = 127.5, so 128 test cases. At the effort page's example rates (0.5 h design, 0.25 h run, R = 1): 128 × (0.5 + 2 × 0.25) = **128 hours**, 61% of the tester's 210 hours (six weeks at 35). Sanity check: at an assumed 40 function points, Jones' rule gives 40^1.2 ≈ 84 test cases; 128 is within a factor of three.

**Estimate versus gates.** By the handbook's convention, the effort table, used as printed, sizes the work; the class table sets what gates require; where a gate asks more than the estimate assumed, the plan records an estimation risk. The class B functions are covered (triples for create booking, quadruples for refund; no combinations for the one-input search). Two risks remain: availability and the occupancy report live in class A `api.py`, whose gate requires all pairs, which the non-critical column does not include. Pairs on two inputs add 4 tests each (the simple critical cell is 8 + 4): 93 system-level tests, 140 test cases, 140 hours, 67% of the tester's time.

## 8. The finished plan on one page

The artifact to copy; each section names the decisions it answers.

```markdown
# Quality Plan: booking API, release 1.0          (v3, owner: tech lead)

1. Scope (decision 3: artifacts)
   In: api.py, rules.py, db.py, the OpenAPI spec. Out: the payment
   provider (covered by its certification), the hotel's front-desk UI.
   Criticality: db.py and rules.py class B; api.py class A.

2. Approach (1 activities, 2 interactions, 6 extent, 8 tools)
   Review of every pull request (inspection checklist for class B);
   unit, API and robustness tests; static analysis in CI.
   Statement coverage (A), branch coverage (B); 80% overall, 90% on
   class B modules; pairs (A), triples (B).
   Tools: pytest, pytest-cov, radon, flake8, bandit, locust.

3. Gates (4 when, 10 defect handling, 11 measurements)
   Commit: flake8, bandit. PR: tests pass, coverage >= 80% (90% class B),
   CC <= 10 unless waived in cc-waivers.txt,
   0 medium/high bandit findings, one approval. Main: same job, red main
   fixed or reverted first. Release: 0 P1, <= 3 P2, P95 < 200 ms.
   Triage: tester + PO daily; P1-P4 rules; origin and where-found fields.
   Reported per sprint: coverage, CC > 10 count, open defects by
   priority, escaped defects per release.

4. Roles (5 by whom, 9 training)
   RACI as agreed; one 90-minute session for all four on writing API
   tests with pytest and on the inspection checklist in week 1.

5. Schedule and cost (4 when, 7 cost and duration)
   Gates from day 1; load test in each release week.
   Estimate: 128 test cases, 128 tester hours over six weeks (risk:
   +12 h for class A pairs); unit tests booked as development;
   CI setup one day (8 h) in week 1.

6. Risks
   Flaky API tests erode trust: quarantine within a day.
   A late tariff change touches refund: re-run triples for refund.
```

## Common mistakes

1. **A threshold without a command.** Spot it: a requirement row with an empty command column.
2. **A gate written in the plan but not in the pipeline.** Spot it: branch protection off, or a CI step with `continue-on-error: true` or `|| true`.
3. **Getting to green by lowering the bar.** Spot it: `--cov-fail-under` reduced in a pull request, or the count of `# nosec` comments rising without written reasons.
4. **One criticality for everything.** Spot it: a class table where only one column is used.
5. **Priority inflation.** Spot it: more open P1s than severity-1 defects.

## How solid is this?

- **Where it comes from.** The planning model, class table and effort table from Miranda's notes; the harm scale from IEEE 829; the CC limit from McCabe; the grade bands from radon's documentation; severity 1 from Microsoft {% cite miranda2015asa ieee829-2008 mccabe1976complexity radon_docs page2009microsoft %}. The commands and outputs were run for this page.
- **What is contested.** The thresholds (80% coverage, CC 10, three open P2s, 200 ms) are practitioner rules of thumb, not measured optima.
- **What we do not hold.** A study of how often teams keep the gates their plans state.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
