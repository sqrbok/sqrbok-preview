---
title: Triaging Static Analysis Warnings
parent: Static Analysis
nav_order: 3
layout: default
page_type: deep-dive  # worked triage with tested commands, suppressions and a baseline
---

# Triaging Static Analysis Warnings

**Triage** decides, for each warning, whether it is a real fault to fix or a false alarm to suppress with a recorded reason. A clean run is only useful if real faults were fixed and every silenced warning carries a reason the next reader can check {% cite chess2007static %}.

## 1. Example: three questions for each warning

The examples use the 21-line `users.py` from [Pattern, Type and Taint Checks](techniques.md), where four tools found three planted faults with four warnings (pylint and flake8 both flagged the unused variable). For each warning, ask three questions in order:

1. **Is the code reachable** on a real path? If no caller can get there, the finding is real but harmless.
2. **Can an attacker or a user control the input?** In `users.py`, Bandit cannot tell; the reviewer must find out where `name` comes from.
3. **Can a test reproduce it?** A failing test turns the warning into a defect report and stays as a regression test.

Applied to the four warnings, the answers were checked by running the code:

| Warning | Reachable? | Input controlled? | Reproduced by a test? |
|---|---|---|---|
| unused `tax` (pylint W0612, flake8 F841) | yes, on every call of `total()` | no input involved | nothing fails; the line is dead code or a forgotten term |
| `user[1]` may be `None` (mypy) | yes | yes: any name not in the table | yes: `greeting(db, "bob")` raises `TypeError: 'NoneType' object is not subscriptable` |
| query built by concatenation (Bandit B608) | yes | yes, if `name` comes from a request | yes: `find_user(db, "nobody' OR '1'='1")` returns alice's row |

## 2. Severity and confidence

Bandit rates every finding twice: **severity**, how serious the issue would be if real, and **confidence**, how sure the check is that it found one. Its `-l` option filters by severity and `-i` by confidence, and each extra letter raises the minimum level: `-l` Low and above, `-ll` Medium and above, `-lll` High only, and the same for `-i`, `-ii`, `-iii`. B608 above is Medium severity, Low confidence: worth a look, likely to need judgement. Its CWE-89 is the Common Weakness Enumeration's number for SQL injection.

## 3. Real faults: fix, do not suppress

The three warnings on `users.py` are real for the property each tool checks. The unused variable and the injection are faults in any case. The `None` index is real for mypy's property: the type says `Optional` and nothing guarantees the name exists; whether it would actually crash depends on the data. The fixed file on that page removes all three. For `total()`, the unused `tax` is either a forgotten term or dead code; only the requirement can say which. If totals exclude tax, delete the line:

```python
def total(prices: list[float]) -> float:
    result = 0.0
    for p in prices:
        result += p
    return result
```

## 4. False alarms: suppress with a reason

False alarms are suppressed where they occur, with the reason next to them. This second file, `admin.py`, is shown in full as it was run; every warning on it is false. Bandit flags a query built from a module constant, mypy cannot know that a regular expression always matches a fixed string, and pylint does not know that a framework requires both callback arguments:

```python
"""Admin helpers: every warning below is a false alarm."""
import re
import sqlite3

ADMIN = "admin"


def find_admin(db: sqlite3.Connection) -> tuple[int, str]:
    """Look up the built-in admin account."""
    # ADMIN is a module constant: no user input reaches this query
    query = "SELECT id FROM users WHERE name = '" + ADMIN + "'"  # nosec B608
    return db.execute(query).fetchone()


def version_number() -> int:
    """Return the major version of a fixed version string."""
    match = re.match(r"(\d+)\.", "3.12")
    # the pattern always matches this fixed literal, so match is never None
    return int(match.group(1))  # type: ignore[union-attr]


# the framework calls every handler with both arguments
def on_event(  # pylint: disable=unused-argument
        event_name: str, payload: dict[str, str]) -> None:
    """Callback whose signature the framework requires."""
    print("event received")
```

Without the three comments, Bandit reports B608, mypy `union-attr` and pylint two `unused-argument` warnings; with them, Bandit, mypy, pylint and flake8 report nothing at all, and the same tools still report every fault in `users.py`. Naming the rule (`B608`, `union-attr`, `unused-argument`) keeps every other check active on the line; flake8's equivalent is `# noqa: F841`. Bandit's documentation recommends a comment saying why a line was excluded {% cite bandit_docs %}. The codes are listed in [mypy's error-code list](https://mypy.readthedocs.io/en/stable/error_code_list.html) and [Bandit's test list](https://bandit.readthedocs.io/en/latest/plugins/index.html); each Bandit finding also prints its CWE, as in the B608 output above.

A decision for the whole project goes in its configuration instead: `[tool.bandit] skips`, `[tool.mypy] disable_error_code` and `[tool.pylint."messages control"] disable` in `pyproject.toml` (Bandit reads it with `-c pyproject.toml`), and `extend-ignore` in flake8's own `.flake8` file.

## 5. Hundreds of warnings at once

The first run on old code rarely finds three warnings; it finds hundreds. Record them as a **baseline** and report only what is new: `bandit -f json -o baseline.json -r .` saves the current findings, and `bandit -b baseline.json -r .` then shows only findings not in it; tested on `users.py`, the baseline hid B608 and a newly added `subprocess` call with `shell=True` was still reported. Then work through the old ones by priority, as eBay did: some must be removed everywhere, some only kept out of new code, some merely counted ([Deploying Static Analysis](deployment.md)) {% cite jaspan2007understanding %}.

**Where the inputs come from.** Warnings come from the tools' JSON or SARIF output in CI rather than from a developer's screen, so they can be counted, baselined and compared between runs. The answer to *can a user control the input?* comes from reading the callers, or from a taint analysis.

## When to use it — and when not

- Triage every new warning when it first appears, in code review if possible ([Deploying Static Analysis](deployment.md)).
- Do not suppress a warning to make a build pass; if the team disagrees with a rule, change the project configuration openly.

## How solid is this?

- **Where it comes from.** All commands and suppressions were run on 28 October 2026 with pylint 4.1.3, flake8 7.4.1, mypy 2.4.0 and Bandit 1.9.4; the comment-with-reason advice is Bandit's {% cite bandit_docs %}; the priority policy is eBay's {% cite jaspan2007understanding %}.
- **What is contested.** Whether a finding is "false" depends on assumptions (a constant stays constant, a framework keeps its signature); the reason in the comment is what lets a later reader re-check them.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
