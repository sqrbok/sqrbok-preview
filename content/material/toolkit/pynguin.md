---
title: Pynguin
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 15
layout: default
page_type: tool-card
tested_with: Pynguin 0.48.0, Python 3.12.16 (container), Podman 6.1.4
---

# Pynguin

**Pynguin** generates unit tests for a Python module: a search (by default DynaMOSA, an evolutionary algorithm that breeds new call sequences from those closest to each uncovered branch) builds sequences of calls until they reach every branch, then it adds assertions that record the values the code returned, keeping those that detect mutants {% cite pynguin_docs lukasczyk2022pynguin %}. Its default search follows EvoSuite, a test generator for Java; `--algorithm RANDOM` works like Randoop, another Java tool. Its authors call Pynguin a research prototype.

The quality question it answers: *which tests can be derived from the code alone, and what do they not know?*

*Run for this card with Pynguin 0.48.0, Python 3.12.16 (container), Podman 6.1.4, October 2026.*

## Install and quick start

Pynguin gets its own container, for two reasons: `poetry add --group dev pynguin` failed because Pynguin 0.48.0 needs pytest below 9, and it calls the code under test with arbitrary arguments, so it refuses to start unless `PYNGUIN_DANGER_AWARE` is set. Its authors recommend an isolated environment. `Containerfile.pynguin`:

```dockerfile
FROM python:3.12-slim
RUN pip install --no-cache-dir pynguin==0.48.0
ENV PYNGUIN_DANGER_AWARE=1
WORKDIR /work
ENTRYPOINT ["pynguin"]
```

```bash
podman build -t pynguin:0.48.0 -f Containerfile.pynguin .
mkdir -p generated
podman run --rm --network none \
  -v ./src:/work/src:ro,Z -v ./generated:/work/generated:Z \
  pynguin:0.48.0 --project-path src --module-name parking.fee --output-path generated --seed 1
# src read-only, no network; only generated/ is writable
```

## Reading the output

After about a second, `generated/test_fee.py`:

```python
def test_0():
    var_0 = 3
    var_1 = fee_.fee(minutes=var_0)
    assert var_1 == 0


def test_1():
    var_0 = 613
    var_1 = fee_.fee(minutes=var_0)
    assert var_1 == 20
    var_3 = fee_.fee(minutes=3545)
    var_4 = 120
    var_5 = fee_.fee(minutes=var_4)
    assert var_5 == 4
    var_19 = fee_.fee(minutes=var_3)
    assert var_19 == 2
    var_20 = -1739
    with pytest.raises(ValueError):
        fee_.fee(minutes=var_20)


@pytest.mark.xfail(strict=True)
def test_2():
    var_0 = 881
    var_5 = fee_.fee(minutes=var_0)
    assert var_5 == 20
    fee_.fee(minutes=4536.61)
    var_7 = True
    var_8 = fee_.fee(minutes=var_0, member=var_7)
    assert var_8 == 15
    fee_.fee(minutes=2676)
    var_11 = fee_.fee(minutes=2, member=True)
    assert var_11 == 0
    fee_.fee(minutes=complex(-4023.052, 232.577788))
```

(Imports trimmed: `fee_` is the module `parking.fee`.) `poetry run pytest generated/` gave `2 passed, 1 xfailed`, at 100% branch coverage of `fee.py`. Without `testpaths`, a plain `poetry run pytest` also collected `generated/` and stopped with `import file mismatch`, because both folders hold a `test_fee.py`; `testpaths = ["tests"]` from the [mutmut card](mutmut.md) keeps the run to `tests/`.

- **The assertions record, they do not judge.** `fee(881, member=True) == 15`: a member pays 15 where a non-member pays 20, more than half. That is the cap-after-discount question from the [Hypothesis card](hypothesis.md), now written down as the expected result.
- **Type hints are only hints.** `minutes: int` did not stop a float or a complex number.
- **`xfail(strict=True)`** (strict: an unexpected pass fails the run) marks a test whose last call raised an unexpected exception (here `TypeError` from the complex number), for you to judge.

With `--report-dir generated/report --output-variables BranchCoverage MutationScore`, `generated/report/statistics.csv` showed `1.0` and `0.81`: of the 42 mutants Pynguin made itself, 8 survived its own assertions. (The file gains a row per run.) (Compare the [mutmut card](mutmut.md).)

## Common mistakes

- **Keeping an `xfail` test with assertions in it.** With the cap moved before the discount, `fee(881, member=True)` returns 10, but `test_2` was still reported as `xfailed`: inside an expected failure, a broken assertion is just another failure. Split such tests before keeping them.
- **Moving the file into `tests/` unread.** Rename the file (a `tests/test_fee.py` exists) and the tests, replace recorded values with the rule, and delete what you cannot explain.
- **Running it on code with side effects.** On `parking.export`, the generated calls would run `tar` for real; the container limits the damage.
- **Running without a seed.** Without `--seed`, three runs gave three different files; with `--seed 1`, every run gave the file above. Record the seed and the version with the tests.

## In CI

Pynguin is not a gate: its tests pass by construction. Run it by hand on legacy modules that lack tests, and review the result as you would a colleague's pull request.

## Go deeper

- [Feedback-Directed Random Testing](../../verif/random/feedback.md): Randoop, the method behind `--algorithm RANDOM`, and where it plateaus.
- [Mutation Testing](../../verif/random/mutation.md): what the mutation score on the statistics line measures.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
