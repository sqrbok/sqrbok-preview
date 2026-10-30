---
title: Hypothesis
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 6
layout: default
page_type: tool-card
tested_with: Hypothesis 6.170.14, pytest 9.2.0
---

# Hypothesis

**Hypothesis** is a property-based testing library for Python: you state a rule that must hold for every input, it generates many inputs to try to break it, and when one does, it shrinks the input to the simplest one that still fails {% cite hypothesis_docs maciver2019hypothesis %}.

The quality question it answers: *does a rule hold for inputs I did not think of?*

*Run for this card with Hypothesis 6.170.14, pytest 9.2.0, October 2026.*

## Install and quick start

```bash
poetry add --group dev hypothesis
```

Three properties of the parking-fee function from the [pytest card](pytest.md), in `tests/test_fee_props.py`. (Constructed example.)

```python
from hypothesis import given, strategies as st
from parking.fee import fee

minutes = st.integers(min_value=0, max_value=3 * 24 * 60)


@given(minutes)
def test_never_above_daily_cap(m):
    assert 0 <= fee(m) <= 20


@given(minutes, minutes)
def test_longer_stay_never_cheaper(a, b):
    short, long = sorted((a, b))
    assert fee(short) <= fee(long)


@given(minutes)
def test_members_pay_half(m):
    assert fee(m, member=True) == fee(m) // 2
```

```bash
poetry run pytest -q tests/test_fee_props.py
```

## Reading the output

```text
..F                                                                      [100%]
...
m = 601

    @given(minutes)
    def test_members_pay_half(m):
>       assert fee(m, member=True) == fee(m) // 2
E       assert 11 == (20 // 2)
E        +  where 11 = fee(601, member=True)
E        +  and   20 = fee(601)
E       Failing test case: test_members_pay_half(
E           m=601,
E       )
...
`git apply .hypothesis/patches/2026-10-30--fc2f93bb.patch` to add failing test cases to your code.
```

The first two properties held for every input tried (100 per test by default). The third failed, and Hypothesis shrank the failure to **601 minutes**: 11 started hours. A non-member pays 22, capped to 20; a member pays 11, which is not half of 20. The cap is applied after the member discount. Whether that is a bug or the rule is a question for whoever owns the rule. The property found the case; deciding it is not the tool's job.

The patch line appeared because `libcst` was installed (`poetry add --group dev "hypothesis[codemods]"`); applying it adds `@example(m=601)` above the test, so this case is rerun every time from now on. Without `libcst` the line is missing; add `@example(m=601)` by hand, above `@given`, with `from hypothesis import example`. (mutmut also installs `libcst`, so after the mutmut card the line appears anyway.)

## Common mistakes

- **Leaving a failing property in the suite.** Until the rule is decided, `test_members_pay_half` fails every run, and tools that need a green suite first, such as [mutmut](mutmut.md), refuse to start. Fix the code or the property, or mark it as an expected failure, with the open question as the reason:

  ```python
  import pytest

  @pytest.mark.xfail(reason="cap vs member discount: rule not decided")
  @given(minutes)
  def test_members_pay_half(m):
      ...
  ```
- **Restating the code as the property.** `assert fee(m) == min(2 * ceil(m / 60), 20)` only repeats the implementation. Good properties are bounds, orderings, round trips, or a comparison with a simpler model.
- **Strategies that are too narrow or too wide.** `st.integers()` without bounds spends most tries on huge numbers; bounds that stop below the interesting region (here, below 601) never reach it.
- **Not keeping the failure.** Hypothesis stores failing cases in `.hypothesis/` and tries them first next time, but a fresh CI checkout does not have that folder. Add the case as an `@example`.
- **Filtering too much.** `assume()` or `.filter()` that rejects most inputs makes Hypothesis give up with a health-check error. Build the input you want instead.

## In CI

The properties are ordinary pytest tests, so `poetry run pytest` runs them. Settings profiles can raise `max_examples` for nightly runs.

## Go deeper

- [Property-Based Testing](../../verif/random/property-based.md): which properties to write.
- [Worked Case: Properties for a Billing Function](../../verif/random/worked-case.md): properties chosen for a real function and the failures they found.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
