---
title: CrossHair
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 14
layout: default
page_type: tool-card
tested_with: CrossHair 0.0.112, Python 3.12
---

# CrossHair

**CrossHair** is a symbolic-execution tool for Python: it runs a function on symbolic values instead of concrete ones, asks the Z3 solver (a program that finds values satisfying a set of conditions) for inputs that take each path, and reports any input that breaks a contract written in the function's docstring {% cite crosshair_docs %}.

The quality question it answers: *is there an input that breaks this contract, and do two versions of a function ever behave differently?* [Hypothesis](hypothesis.md) samples inputs at random; CrossHair searches paths.

*Run for this card with CrossHair 0.0.112, Python 3.12, October 2026.*

## Install and quick start

```bash
poetry add --group dev crosshair-tool      # also installs the z3-solver package
```

Contracts are docstring lines: `pre:` for what callers promise, `post:` for what the function promises, `__return__` for its result. The member rule from the Hypothesis card, as a contract in `src/parking/contracts.py` (constructed example):

```python
from parking.fee import fee


def member_fee(minutes: int) -> int:
    """
    pre: 0 <= minutes <= 3 * 24 * 60
    post: __return__ == fee(minutes) // 2
    """
    return fee(minutes, member=True)
```

```bash
poetry run crosshair check src/parking/contracts.py
poetry run crosshair check --per_condition_timeout 30 src/parking/contracts.py
poetry run crosshair diffbehavior --per_condition_timeout 30 parking.fee.fee parking.fee_v2.fee
poetry run crosshair cover --example_output_format=pytest parking.fee.fee
```

## Reading the output

The first command printed nothing and exited 0 after about one second. The second, with a budget of 30 CPU seconds for each contract condition (a soft limit, not a hard stop), reported after about 21 seconds and exited 1 (path shortened):

```text
src/parking/contracts.py:7: error: false when calling member_fee(601) (which returns 11)
```

Line 7 is the `post:` line; any stay above 600 minutes breaks it, and CrossHair, like Hypothesis, reported 601. **Silence was not a proof**: by default, `check` stops after five attempts (iterations) in a row that explore no new path.

The search was slow because `math.ceil(minutes / 60)` divides into a float, which the solver cannot handle as cheaply as integers, so CrossHair falls back to trying concrete values. In a variant of `fee.py` (not kept) with `hours = (minutes + 59) // 60` instead of the `math.ceil` line, the default `check` found `member_fee(601)` in under a second. Code the solver can reason about is code CrossHair can check.

`diffbehavior` compares two functions. `src/parking/fee_v2.py` is a rewrite in integers that also moves the cap before the discount:

```python
def fee(minutes: int, member: bool = False) -> int:
    """Same rule, integer arithmetic, cap before the member discount."""
    if minutes < 0:
        raise ValueError("minutes must be >= 0")
    if minutes <= 15:
        return 0
    hours = (minutes + 59) // 60
    total = min(2 * hours, 20)
    if member:
        total = total // 2
    return total
```

```text
Given: (minutes=601, member=True),
     parking.fee.fee : returns 11
  parking.fee_v2.fee : returns 10
```

Without the timeout it printed `No differences found. (attempted 22 iterations)`; with it, the difference came after 37 seconds, exit code 1.

`cover` prints tests that record what the code does now (its two import lines trimmed here; `> tests/test_fee_cover.py` saves the whole output, imports included):

```text
def test_fee():
    assert fee(16, member=True) == 1

def test_fee_2():
    with pytest.raises(ValueError, match='minutes\\ must\\ be\\ >=\\ 0'):
        fee(-1, member=False)

def test_fee_3():
    assert fee(0, member=False) == 0
```

These three reached every line of `fee.py` but 5 of 6 branches (no non-member above 15 minutes): 94% on the [Coverage card](coverage.md)'s combined measure. Their assertions catch changes, not wrong rules.

## Common mistakes

- **Trusting the default run.** Set `--per_condition_timeout`, and still read "nothing found" as "nothing found yet".
- **Leaving out the precondition.** Without `pre:`, `check` reported `ValueError: minutes must be >= 0 when calling member_fee(-1)`: an exception the contract does not allow is a counterexample too.
- **Checking code with side effects.** CrossHair really runs the function. Keep contracts on pure functions; never on code that writes files or calls a service.

## In CI

```yaml
- run: poetry run crosshair check --per_condition_timeout 30 src/parking/contracts.py
```

It exits 1 on a counterexample, so on this project the step is red from the first push. Until the member rule is decided, leave the step out of the workflow, or point it at a file that holds only agreed contracts. The budget applies per condition, so the run time grows with the number of contracts.

## Go deeper

- [Symbolic Execution](../../verif/static/symbolic-execution.md): path conditions, the solver, and KLEE's results on C code.
- [Analysis Tools by Family](../../verif/overview/analysis/tools.md): CrossHair beside the other analysers, including a contract it could not settle.
- [Soundness and Completeness](../../verif/static/limits.md): why a bounded search can miss a bug.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
