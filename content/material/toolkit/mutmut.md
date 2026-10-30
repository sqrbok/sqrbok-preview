---
title: mutmut
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 7
layout: default
page_type: tool-card
tested_with: mutmut 3.8.1, pytest 9.2.0, macOS and Linux
---

# mutmut

**mutmut** is a mutation testing tool for Python: it makes many small changes to your code, one at a time (a `<` becomes `<=`, a `20` becomes `21`), runs your tests against each changed copy, and reports every change that no test noticed {% cite mutmut_docs %}.

The quality question it answers: *would my tests notice if the code were wrong?* A **killed** mutant made a test fail; a **survived** mutant is a change the suite let through.

*Run for this card with mutmut 3.8.1, pytest 9.2.0, macOS and Linux, October 2026.*

## Install and quick start

```bash
poetry add --group dev mutmut   # needs an upper bound on requires-python: see the Poetry card
```

Configure it in `pyproject.toml`, then run it:

```toml
[tool.mutmut]
source_paths = ["src/parking/fee.py"]                  # the code to mutate
pytest_add_cli_args_test_selection = ["tests/"]         # the tests pytest runs
```

```bash
poetry run mutmut run
poetry run mutmut results            # the survivors
poetry run mutmut show parking.fee.x_fee__mutmut_24   # an ID from mutmut results
poetry run mutmut browse             # interactive view of all mutants
```

## Reading the output

On the fee function from the [pytest card](pytest.md), with its six tests plus `test_member_discount` (`assert fee(120, member=True) == 2`), which reach 100% branch coverage. (Constructed example.) mutmut first runs the whole suite and stops with `failed to collect stats` if any test fails, so the failing property from the [Hypothesis card](hypothesis.md) must be fixed, marked `xfail` or moved out of `tests/` first; the run below moved `test_fee_props.py` out. (Kept in and marked `xfail`, its two passing properties also kill mutants 2, 3 and 24: 20 killed, 4 survived.)

```text
24/24  🎉 17 🫥 0  ⏰ 0  🤔 0  🙁 7  🔇 0  🧙 0
```

24 mutants: 🎉 17 killed and 🙁 7 survived. The others are no tests, timeout, suspicious, skipped, and caught by a type check. `mutmut show` prints a survivor as a diff:

```diff
-    return min(total, 20)
+    return min(total, 21)
```

The seven survivors fell into four groups:

| Mutant | Change | Why it survived | Response |
|---|---|---|---|
| 24 | cap 20 → 21 | no test reaches the cap | test `fee(24 * 60) == 20` |
| 2, 3 | `minutes < 0` → `<= 0`, `< 1` | no test for 0 minutes | test `fee(0) == 0` |
| 18 | `//` → `/` | `4 / 2` is `2.0`, and `2.0 == 2` | test that the result is an `int` |
| 4, 5, 6 | error message text changed | no test reads the message | test the whole message, or exclude it |

The three new tests:

```python
def test_zero_minutes_is_free():
    assert fee(0) == 0


def test_daily_cap():
    assert fee(24 * 60) == 20


def test_member_fee_is_whole_number():
    assert isinstance(fee(120, member=True), int)
```

After them: 21 killed, 3 survived, all of them message mutants. Marking line 7 with `# pragma: no mutate` stops mutmut generating them: 21 of 21 killed.

## Common mistakes

- **Stale results.** In mutmut 3.8.1, adding tests and rerunning `mutmut run` printed the same 7 survivors; the results are cached in `mutants/`. Delete that folder after changing tests.
- **Old configuration keys.** `paths_to_mutate` and `tests_dir` are deprecated; use the keys above.
- **Linting the mutants.** `mutants/` holds a mutated copy of your code. Its code is reformatted, so `flake8 .` reported dozens of style warnings from inside `mutants/` on the example; exclude the folder from linters and from version control.
- **A loose message check.** `pytest.raises(ValueError, match="minutes")` does not kill mutant 5: `match` searches, and `"XXminutes must be >= 0XX"` still contains "minutes". Anchor it: `match=r"^minutes must be >= 0$"`.
- **Excluding everything that survives.** `# pragma: no mutate` is for mutants that should not be killed (an equivalent change, a log message). A surviving boundary is a missing test.
- **A broken `pytest` afterwards.** With `mutants/` present, a plain `poetry run pytest` also collected the mutated copy of the tests and stopped with `import file mismatch`. Add `testpaths = ["tests"]` under `[tool.pytest.ini_options]` in `pyproject.toml`; the same run then gave `10 passed` (with `test_fee_props.py` back in `tests/` and marked `xfail`: `12 passed, 1 xfailed`).
- **Windows.** mutmut 3 needs `fork()`; on Windows, run it in WSL.

## In CI

On large code it is slow, so it usually runs nightly or on changed files only.

## Go deeper

- [Mutation Testing](../../verif/random/mutation.md) and [Mutation Coverage](../../verif/coverage/mutation.md): what the mutation score means, and equivalent mutants.
- [Coverage.py and pytest-cov](coverage.md): why 100% coverage still left seven survivors.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
