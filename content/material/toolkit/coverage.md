---
title: Coverage.py and pytest-cov
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 5
layout: default
page_type: tool-card
tested_with: coverage 7.17.0, pytest-cov 7.1.0, pytest 9.2.0
---

# Coverage.py and pytest-cov

**Coverage.py** records which lines and branches of your code ran while the tests ran; **pytest-cov** is the pytest plugin that switches it on with one flag {% cite coveragepy_docs %}.

The quality question it answers: *which parts of the code did no test execute?* It cannot say whether the executed parts were checked; that is what [mutmut](mutmut.md) is for.

*Run for this card with coverage 7.17.0, pytest-cov 7.1.0, pytest 9.2.0, October 2026.*

## Install and quick start

```bash
poetry add --group dev pytest-cov      # already there if you followed the Poetry card
poetry run pytest -q --cov=parking --cov-branch --cov-report=term-missing
```

`--cov=parking` names the package to measure, `--cov-branch` adds branch coverage, and `term-missing` lists the lines not run.

## Reading the output

The fee function from the [pytest card](pytest.md), with its six tests (constructed example), as numbered in the file:

```text
 5  def fee(minutes: int, member: bool = False) -> int:
 6      if minutes < 0:
 7          raise ValueError("minutes must be >= 0")
 8      if minutes <= 15:
 9          return 0
10      hours = math.ceil(minutes / 60)
11      total = 2 * hours
12      if member:
13          total = total // 2
14      return min(total, 20)
```

```text
Name                      Stmts   Miss Branch BrPart  Cover   Missing
---------------------------------------------------------------------
src/parking/__init__.py       0      0      0      0   100%
src/parking/fee.py           11      1      6      1    88%   13
---------------------------------------------------------------------
TOTAL                        11      1      6      1    88%
```

- **Stmts / Miss:** 11 statements (the docstring and blank lines are not counted), 1 never ran: line 13, because no test passes `member=True`.
- **Branch / BrPart:** the three `if`s have two outcomes each, 6 branches. `BrPart` counts decisions that took only one way: the `if member:` on line 12 was always false.
- **Cover** combines both: (statements run + branches taken) / (statements + branches) = (10 + 5) / (11 + 6) = 88%.

Without `--cov-branch` the same run reports 91% (10 of 11 statements). Adding one test to `tests/test_fee.py` brings both to 100%:

```python
def test_member_discount():
    assert fee(120, member=True) == 2
```

## Common mistakes

- **Reading 100% as "tested".** At 100% branch coverage, no test yet checked the cap of 20: `min(total, 20)` holds a decision inside a function call, which branch coverage does not see. [mutmut](mutmut.md) found it.
- **Measuring the wrong thing.** `--cov` without a package name measures everything imported, including the tests; `--cov=parking` measures only the product code.
- **Comparing statement and branch numbers.** 91% and 88% here are the same tests; say which measure a coverage floor uses.
- **A floor that never fails.** `--cov-report` only reports. `--cov-fail-under=90` turned the run red: `FAIL Required test coverage of 90% not reached. Total coverage: 88.24%`, with exit code 1, although all six tests passed.

## In CI

```yaml
- run: poetry run pytest --cov=parking --cov-branch --cov-fail-under=85
```

Set the floor just below your current number, so the gate holds the line rather than demanding a jump; a common rule of thumb rounds down to the next multiple of 5 (85 for the 88% above; at 100% it would be 95). `--cov-report=html` writes a browsable report to `htmlcov/`, with the missed lines in red.

## Go deeper

- [Statement and Branch Coverage](../../verif/coverage/code.md) and [Multiple Conditions](../../verif/coverage/conditions.md): what each criterion requires.
- [Worked Case: Basis Paths and a Coverage Gate for a Checkout Function](../../verif/coverage/worked-case.md): a floor chosen from real numbers.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
