---
title: "Worked Case: Quality Gates for a Python Repository"
parent: Static Analysis
nav_order: 11
layout: default
page_type: worked-case
---

# Worked Case: Quality Gates for a Python Repository

This worked case takes one small Python repository with no automated checks and wires it to **quality gates**: a complexity limit, a type checker, a security scanner with a baseline, a coverage floor and a coverage check on changed lines, run as pre-commit hooks and in GitHub Actions. It ends with the thresholds the team chose and why. Triage is explained on [Triaging Static Analysis Warnings](warnings.md) and rollout on [Deploying Static Analysis](deployment.md). (Constructed example: the repository was written for this page; every tool was run on it.)

## The case

`shopkit` is the pricing package of a web shop: `pricing.py` (prices, discounts, VAT), `exports.py` (a nightly export to accounting) and six tests. It has grown without review tools, and a refund bug last month came from a function nobody dared to touch. The team wants gates that stop new problems without blocking every pull request on old ones. The tools are pinned in `requirements-dev.txt` (ruff 0.17.3, mypy 2.4.1, Bandit 1.9.4, pytest 9.2.0, pytest-cov 7.1.0, diff-cover 11.0.0, pre-commit 4.7.0), and every run below used Python 3.12.9 after `pip install -r requirements-dev.txt -e .`. The editable install (`-e .`) matters in this `src/` layout: without any install the tests stop at `ModuleNotFoundError: No module named 'shopkit'`, and with a plain `pip install .` coverage measures the installed copy instead of `src/`, so diff-cover printed `No lines with coverage information in this diff`.

## Step 1. Run every tool once, with no gate

The raw input is the repository itself. Before choosing any threshold, run each tool and read what it says about *this* code; a threshold chosen first is a guess. The starting configuration in `pyproject.toml`:

```toml
[build-system]
requires = ["setuptools>=69"]
build-backend = "setuptools.build_meta"

[project]
name = "shopkit"
version = "0.1.0"
requires-python = ">=3.11"

[tool.ruff]
line-length = 100

[tool.ruff.lint]
select = ["E", "F", "B", "C90"]        # pycodestyle, pyflakes, bugbear, McCabe complexity

[tool.ruff.lint.mccabe]
max-complexity = 10

[tool.mypy]
files = ["src"]
check_untyped_defs = true

[tool.coverage.run]
branch = true
source = ["shopkit"]
```

```text
$ ruff check .
C901 `discount` is too complex (11 > 10)
  --> src/shopkit/pricing.py:16:5

$ mypy
src/shopkit/pricing.py:13: error: Unsupported operand types for * ("None" and "int")  [operator]
src/shopkit/pricing.py:13: note: Left operand is of type "float | None"

$ bandit -q -r src
[B403:blacklist]   Severity: Low     Confidence: High  exports.py:2   import pickle
[B404:blacklist]   Severity: Low     Confidence: High  exports.py:4   import subprocess
[B608:hardcoded_sql_expressions]  Severity: Medium  Confidence: Low   exports.py:8
[B301:blacklist]   Severity: Medium  Confidence: High  exports.py:13  pickle.loads
[B602:subprocess_popen_with_shell_equals_true]  Severity: High  Confidence: High  exports.py:17

$ pytest --cov --cov-report=term-missing
Name                      Stmts   Miss Branch BrPart  Cover   Missing
src/shopkit/exports.py       10      2      0      0    80%   13, 17
src/shopkit/pricing.py       31      9     20      4    63%   23-26, 30-33, 35, 37
TOTAL                        41     11     20      4    66%
```

(Bandit's output is condensed to one line per finding.) Seven findings and a coverage figure: few enough to triage every one by hand.

## Step 2. Triage the first findings

Each finding gets the three questions from [Triaging Static Analysis Warnings](warnings.md): is it reachable, is the input controlled, can a test reproduce it.

| Finding | Verdict | Action |
|---|---|---|
| C901 `discount` complexity 11 | real: 11 independent paths; lines 23–37, all inside `discount`, are untested | split into `customer_rate` and `coupon_rate` |
| mypy `None * int` in `line_total` | real bug: an unknown SKU raises `TypeError` | raise `KeyError("unknown SKU")`, add a test |
| B608 SQL built by concatenation | real: `customer` comes from a request | use a `?` parameter |
| B602 `shell=True` with a path | real: a crafted file name runs a command | pass a list, no shell |
| B301 `pickle.loads` | the cache is written only by this service, today | accept for now, record in the baseline |
| B403, B404 import pickle, subprocess | noise: the import is not the risk, the call is | skip both project-wide |

Three of these deserve a word. The refactoring could have changed prices, so the split was checked once with a differential property test against the old function: 2,000 random inputs with Hypothesis gave identical results ([Property-Based Testing](../random/property-based.md)); that check was a one-off and is not part of the test suite. The `shell=True` fix produced two new **Low** findings, B603 and B607 (a subprocess call with a partial path `scp`); fixing a warning can create others, which is one reason to gate on severity. The two import findings are skipped in the configuration, where the next reader sees the reason:

```toml
[tool.bandit]
skips = ["B403", "B404"]   # importing pickle/subprocess is not the risk; the calls are
```

```text
$ bandit -q -c pyproject.toml -r src        # after the fixes
[B301:blacklist]  Severity: Medium  Confidence: High
[B607:start_process_with_partial_path]  Severity: Low  Confidence: High
[B603:subprocess_without_shell_equals_true]  Severity: Low  Confidence: High
```

Bandit reads that section only when given `-c pyproject.toml`; without it the same run still lists B403 and B404.

Some faults come from no tool. For example, `pricing.py` keeps prices in `float`, which rounds money wrongly in some cases. None of the five tools checks for it; it is a review item, as on [Worked Case: Inspecting a Refund Change](../inspection/worked-case.md), and the team logged it as a separate ticket.

## Step 3. Decide the thresholds

Each gate needs a number, and each number needs a reason the team can defend when a pull request fails.

**Complexity: 10.** McCabe proposed 10 as the upper bound for a module's cyclomatic complexity {% cite mccabe1976complexity %}. The criterion for keeping it: only one function exceeded it, and splitting that one was cheap. Had thirty legacy functions exceeded it, the team would have kept 10 for new code and listed the old ones under `per-file-ignores`, the eBay policy of "no new instances" ([Deploying Static Analysis](deployment.md)) {% cite jaspan2007understanding %}.

**Types: zero mypy errors** with `check_untyped_defs`. The criterion: the first run had one error and it was a real bug.

**Security: no new Medium or High finding.** The accepted findings go into a baseline, and the gate reports only what is not in it:

```bash
bandit -q -c pyproject.toml -r src -f json -o .bandit-baseline.json   # once, after triage
bandit -q -c pyproject.toml -r src -b .bandit-baseline.json -ll       # the gate
```

`-ll` hides Low severity; the High finding was fixed, not baselined.

**Coverage floor: 85%.** After triage the tests covered 87% (statements and branches together). The criterion: the floor is the current value rounded down to a multiple of 5, so it never fails a pull request that changes nothing, and it is raised when coverage rises. A floor above the current value makes the gate red on day one; one far below lets coverage slide. Coverage shows what the tests run, not what they check, so the floor is a minimum, not a target ([Coverage and Fault Detection](../coverage/discussion.md)).

**Changed lines: 80%.** In a repository of several thousand lines, a total floor barely moves when one new function arrives untested: here, with only 62 measured units, the same function drops the total by 5 points and the floor alone would catch it (Step 5), but in a 5,000-line repository it would move the total by well under one point. `diff-cover` reads the coverage report and the Git diff against `main`, and fails when the changed lines are not covered enough. The number is a practitioner rule of thumb, not a measured optimum: high enough that a new function needs tests for its branches, low enough that a defensive line which is hard to reach (a timeout handler) does not block a small change. A team that finds 80% blocks nothing should raise it.

## Step 4. Wire the gates

Fast checks run on every commit through pre-commit; tests and coverage run in CI, where nobody can skip them. Local hooks call the tools installed in the project's environment from the same pinned `requirements-dev.txt` that CI installs, so both run the same versions:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: ruff
        name: ruff (lint + complexity)
        entry: ruff check
        language: system
        types: [python]
      - id: mypy
        name: mypy
        entry: mypy
        language: system
        types: [python]
        pass_filenames: false
      - id: bandit
        name: bandit (new findings only)
        entry: bandit -q -c pyproject.toml -r src -b .bandit-baseline.json -ll
        language: system
        pass_filenames: false
```

```yaml
# .github/workflows/quality.yml
name: quality-gates
on:
  pull_request:
  push:
    branches: [main]
jobs:
  gates:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0            # diff-cover needs main to compare against
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -r requirements-dev.txt -e .   # pinned tools; the package, editable
      - run: ruff check .
      - run: mypy
      - run: bandit -q -c pyproject.toml -r src -b .bandit-baseline.json -ll
      - run: pytest --cov --cov-report=xml --cov-fail-under=85
      - if: github.event_name == 'pull_request'
        run: diff-cover coverage.xml --compare-branch=origin/main --fail-under=80
```

Checked by cloning the repository into a fresh folder, creating a new virtual environment, and running the workflow's `run:` lines in order, on `main` and on both commits of the pull request in Step 5. On `main`:

```text
$ pip install -r requirements-dev.txt -e .
$ ruff check .
All checks passed!
$ mypy
Success: no issues found in 3 source files
$ bandit -q -c pyproject.toml -r src -b .bandit-baseline.json -ll; echo $?
0
$ pytest --cov --cov-report=xml --cov-fail-under=85
Required test coverage of 85% reached. Total coverage: 87.10%

$ pre-commit run --all-files
ruff (lint + complexity).................................................Passed
mypy.....................................................................Passed
bandit (new findings only)...............................................Passed

$ python -c "import yaml; print(list(yaml.safe_load(open('.github/workflows/quality.yml'))))"
['name', True, 'jobs']
```

The workflow parses; the `True` is PyYAML reading the key `on` as a YAML 1.1 boolean, which GitHub does not do, so it is harmless. The workflow file itself was not run on GitHub for this page, only its steps, as above.

## Step 5. The first pull request through the gates

A developer adds `shipping_fee(weight_kg, country, express)` with a single test for a domestic parcel:

```text
$ pytest --cov --cov-report=xml --cov-fail-under=85
FAIL Required test coverage of 85% not reached. Total coverage: 81.82%

$ diff-cover coverage.xml --compare-branch=origin/main --fail-under=80
src/shopkit/pricing.py (66.7%): Missing lines 54,57,59
Total:   9 lines
Missing: 3 lines
Coverage: 66%
Failure. Coverage is below 80%.
```

The three missing lines are the error for a zero weight, the heavy-parcel surcharge and the express doubling: the three places where a pricing bug would hide. Two more tests (a 25 kg express parcel to France, and weight 0) gave 89.61% in total and 100% on the changed lines, and both gates passed.

## Decision

The team keeps these gates, and reviews them every quarter:

| Gate | Tool and setting | Threshold | Why this number |
|---|---|---|---|
| Complexity | `ruff` C901, `max-complexity = 10` | 10 per function | McCabe's bound; only one function exceeded it |
| Types | `mypy`, `check_untyped_defs = true` | 0 errors | the one error found was a real bug |
| Security | `bandit -b .bandit-baseline.json -ll` | 0 new Medium/High | fix High; baseline only what was judged acceptable |
| Coverage floor | `pytest --cov-fail-under` | 85% | current 87%, rounded down to 5; raised as coverage rises |
| Changed lines | `diff-cover --fail-under` | 80% | new code is tested even when the total barely moves |

## Tools by language

Other ecosystems have the same kinds of tool: coverage for the floor and the changed-lines gate, static analysis for the lint, type and security gates, and mutation testing to check test strength. The table names common tools from their documentation; it is not a comparison.

| Language | Coverage | Mutation | Static analysis |
|---|---|---|---|
| Java | JaCoCo | PIT | SpotBugs, Semgrep |
| Python | coverage.py, pytest-cov | mutmut | ruff, mypy, Bandit, Semgrep |
| C, C++ | gcov, llvm-cov | Mull | clang-tidy, Semgrep |
| JavaScript, TypeScript | c8 | Stryker | ESLint, Semgrep |
| Go | `go test -cover` | — | `go vet`, Semgrep |
| C# | coverlet | Stryker.NET | Roslyn analyzers, Semgrep |

diff-cover reads Cobertura, Clover, JaCoCo and LCOV reports, so the changed-lines gate works for every row.

## Common mistakes

1. **Baselining a High finding.** Bandit's baseline matches findings without their line numbers. In a trial where the original B602 *had* been baselined instead of fixed, a second `shell=True` call added to `exports.py` was **not reported**, while the same call in a new file was. *Spot it:* High-severity entries in the baseline file; fix them instead.
2. **An aspirational coverage floor.** A floor of 90% on a 66% code base fails every pull request, and developers learn to add tests that run code without checking it. *Spot it:* the floor is above the coverage on `main`. Check test strength with [mutation testing](../random/mutation.md) instead.
3. **A shallow checkout in CI.** With `git clone --depth 1`, diff-cover printed `Could not find the branch to compare to. Does 'origin/main' exist?`. *Spot it:* the checkout step has no `fetch-depth: 0`.
4. **Gates only in pre-commit.** `git commit --no-verify` skips every hook. *Spot it:* the CI workflow does not repeat the checks.
5. **Raising the limit to make old code pass.** *Spot it:* `max-complexity = 25` in the config; keep the limit and list legacy exceptions per file instead.

## How solid is this?

- **Where it comes from.** All commands and outputs were run for this page on 29 October 2026 with the versions listed; the triage questions and rollout order come from the area's sources {% cite chess2007static jaspan2007understanding sadowski2015tricorder %}; Bandit's baseline and severity options are tool documentation {% cite bandit_docs %}.
- **What is contested.** The thresholds are practitioner rules of thumb, apart from McCabe's 10 {% cite mccabe1976complexity %}; coverage floors in particular are set by local habit, not by measured effect.
- **What we do not hold.** A study of which coverage floor, if any, reduces field defects.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
