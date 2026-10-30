---
title: pytest
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 4
layout: default
page_type: tool-card
tested_with: pytest 9.2.0, Python 3.12
---

# pytest

**pytest** is Python's most used test runner: it finds files named `test_*.py`, runs every function named `test_*` in them, and reports each `assert` that fails with the values involved {% cite pytest_docs %}.

The quality question it answers: *do the examples I wrote down still hold after this change?*

*Run for this card with pytest 9.2.0, Python 3.12, October 2026.*

## Install and quick start

The function under test charges nothing for the first 15 minutes, then 2 per started hour, with members paying half and a cap of 20 for the whole stay; the docstring's "per day" is looser than the code, one of the questions the later cards run into. (Constructed example.)

`src/parking/fee.py`:

```python
"""Parking fee: first 15 minutes free, then 2 per started hour, capped at 20 per day."""
import math


def fee(minutes: int, member: bool = False) -> int:
    if minutes < 0:
        raise ValueError("minutes must be >= 0")
    if minutes <= 15:
        return 0
    hours = math.ceil(minutes / 60)
    total = 2 * hours
    if member:
        total = total // 2
    return min(total, 20)
```

`tests/test_fee.py`:

```python
import pytest
from parking.fee import fee


def test_free_quarter_hour():
    assert fee(15) == 0


def test_one_started_hour():
    assert fee(16) == 2


@pytest.mark.parametrize("minutes, expected", [(60, 2), (61, 4), (120, 4)])
def test_started_hours(minutes, expected):
    assert fee(minutes) == expected


def test_negative_minutes_rejected():
    with pytest.raises(ValueError):
        fee(-1)
```

```bash
poetry add --group dev pytest   # already there if you followed the Poetry card
poetry run pytest -q             # -q: quiet, one character per test
```

## Reading the output

```text
......                                                                   [100%]
6 passed in 0.01s
```

Each dot is one passed test: four functions, but `parametrize` turned one of them into three tests. An `F` is a failed assertion, an `E` an error outside the assertion (an import failure, an exception in setup). `-v` prints one line per test, `-k started` runs only tests whose name contains "started", and `--collect-only -q` lists the tests without running them:

```text
tests/test_fee.py::test_started_hours[60-2]
tests/test_fee.py::test_started_hours[61-4]
```

For a failure, pytest prints the assertion with the values it compared; the [Hypothesis card](hypothesis.md) shows one in full. Read the bottom line first: it names the test and the comparison that failed.

## Common mistakes

- **`ModuleNotFoundError` at collection.** With a `src/` layout, the package must be installed into the environment pytest runs in. A system `pytest` on this project stopped with `No module named 'parking'` and `Interrupted: 1 error during collection`; `poetry run pytest` passed.
- **A test that cannot fail.** A test with no `assert`, or asserting something always true, passes whatever the code does. [mutmut](mutmut.md) finds these.
- **Several behaviours in one test.** The first failing `assert` stops the function, so the others go unchecked. Prefer one behaviour per test, or `parametrize`.
- **Checking an exception too loosely.** `pytest.raises(ValueError)` passes for any `ValueError`; add `match=r"^minutes must be >= 0$"` when the message matters. `match` searches the message, so an unanchored pattern also accepts longer messages.

## In CI

```yaml
- run: poetry run pytest -q
```

pytest exits 0 when every test passes, 1 when a test fails, 2 when the run was interrupted, for example by the import error above, and 5 when it found no tests at all.

## Go deeper

- [Test Levels](../../verif/overview/testing/testing_levels.md) and [Test Doubles](../../verif/overview/testing/test-doubles.md): what to test at which level.
- [Coverage.py and pytest-cov](coverage.md): what these tests executed.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
