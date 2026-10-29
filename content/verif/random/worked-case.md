---
title: "Worked Case: Properties for a Billing Function"
parent: Random Testing
nav_order: 10
layout: default
page_type: worked-case
---

# Worked Case: Properties for a Billing Function

This worked case takes one function from a hotel booking service, finds its properties, writes a Hypothesis generator for it, lets the generator find a real bug, and ends with a mutation run that decides whether the properties are strong enough to replace hand-picked examples. The theory is on [Property-Based Testing](property-based.md) and [Mutation Testing](mutation.md). (Constructed example.)

## The case

A hotel invoices stays per calendar month, because revenue and tax are booked per month. The module `billing.py` has one function for that: `split_by_month(check_in, check_out)` returns the parts of a stay that fall into each month, as `(start, end)` pairs where `end` is the morning of departure. The specification is one sentence in the ticket: *"split a stay into pieces, each inside one calendar month, that together cover the stay exactly."* The team has two example tests, both for stays in March. This is the code as it was submitted:

```python
# src/billing.py
from datetime import date, timedelta

def nights(check_in: date, check_out: date) -> int:
    if check_out <= check_in:
        raise ValueError("check_out must be after check_in")
    return (check_out - check_in).days

def split_by_month(check_in: date, check_out: date) -> list[tuple[date, date]]:
    """Return (start, end) pieces, each inside one calendar month."""
    nights(check_in, check_out)          # validates the range
    pieces = []
    start = check_in
    while start < check_out:
        first_of_next = date(start.year, start.month + 1, 1)
        end = min(first_of_next, check_out)
        pieces.append((start, end))
        start = end
    return pieces
```

All runs below used Python 3.12.9, pytest 9.2.0, Hypothesis 6.170.13 and mutmut 3.8.1.

## Step 1. Turn the specification into properties

Read the specification sentence by sentence and ask, for each clause, what must be true of *every* output. The six common patterns give a checklist to go through; three of them (differential, round trip, no crash) are the kinds the Jane Street developers reported most {% cite goldstein2024pbt %}.

| Pattern | Question to ask | Property for `split_by_month` |
|---|---|---|
| Invariant | What never changes? | the nights of the pieces sum to the nights of the stay; each piece lies in one month |
| Round trip | Can the output be turned back into the input? | joining the pieces end to start gives the original stay |
| Differential (oracle) | Is there a slow, obvious way? | counting nights day by day per month gives the same numbers |
| Idempotence | What if it runs on its own output? | splitting a piece returns that piece unchanged |
| Metamorphic | How must the output change when the input changes? | moving a stay 28 years later (the calendar repeats then, between 1901 and 2099) gives the same night counts |
| No crash | Which inputs must never break it? | any two dates give a result or a `ValueError`, and `ValueError` only when `check_out <= check_in` |

Seven properties came out of one sentence. None of them restates the code: each is checked by something simpler than the function (a sum, a day-by-day loop, a comparison).

## Step 2. Write the generator

Two independent `st.dates()` would mostly produce invalid or decade-long stays. A composite strategy builds a *valid* stay directly: draw a check-in date, then a number of nights.

```python
@st.composite
def stays(draw, first=date(2000, 1, 1), last=date(2099, 12, 31), max_nights=400):
    check_in = draw(st.dates(min_value=first, max_value=last))
    n = draw(st.integers(min_value=1, max_value=max_nights))
    return check_in, check_in + timedelta(days=n)
```

**Decision point: the input ranges.** The criterion is "every value the production system can hold, and a little more". On a real system, take them from the booking rules and the data: the longest stay the booking engine allows, and the earliest and latest dates in the bookings table (`SELECT min(check_in), max(check_out)`). For example, this booking engine caps stays at 365 nights, so 400 covers it with a margin. Too narrow a range hides bugs; one far wider than real use spends the test budget on values no customer sends.

## Step 3. Run it and read the counterexample

The first property, nights preserved, failed on the first run, in under a second:

```text
>           first_of_next = date(start.year, start.month + 1, 1)
E           ValueError: month must be in 1..12
E           Failing test case: test_nights_are_preserved(
E               stay=(datetime.date(2000, 12, 1), datetime.date(2000, 12, 2)),
E           )
`git apply .hypothesis/patches/2026-10-29--ad0bcf28.patch` to add failing test cases to your code.
1 failed in 0.45s
```

Read the shrunk example for what it keeps, not for its exact values. This run shrank to the smallest possible stay, one night from 1 to 2 December 2000, but three more runs with a fresh database stopped at check-ins of 1 January, 1 June and 1 July 2000. The check-out was always **2 December 2000**: any stay that has a night in December crashes, because `start.month + 1` becomes 13. Shrinking simplifies until no simpler input still fails, but it does not promise the smallest possible input {% cite maciver2019hypothesis %}. The two March examples could never have found this.

## Step 4. Fix, pin the regression, replay

The fix rolls December over into January of the next year. The counterexample is then pinned with `@example`, so it runs on every future run even if the random search never draws it again; the patch file Hypothesis wrote adds an equivalent decorator.

```python
        if start.month == 12:
            first_of_next = date(start.year + 1, 1, 1)
        else:
            first_of_next = date(start.year, start.month + 1, 1)

@given(stays())
@example((date(2000, 12, 1), date(2000, 12, 2)))       # regression: December crash
def test_nights_are_preserved(stay): ...
```

Replaying needs no setup: Hypothesis stores each failing example in the `.hypothesis/` folder and tries it first next time. Rerunning the unfixed code twice more reported the same example each time; the first run took 0.45 s, the two replays 0.25 s and 0.24 s. On CI the folder is usually lost between runs, which is why the `@example` line, not the database, is what protects the fix.

## Step 5. Do not filter your way to valid inputs

A colleague wanted a test for short stays and wrote it with a filter on two independent dates:

```python
short_stays = st.tuples(st.dates(), st.dates()).filter(
    lambda s: 0 < (s[1] - s[0]).days <= 30)
```

```text
E   hypothesis.errors.FailedHealthCheck: It looks like this test is filtering out a lot
E   of inputs. 0 inputs were generated successfully, while 50 inputs were filtered out.
```

Two random dates between year 1 and 9999 are almost never 1 to 30 days apart, so Hypothesis gave up. `assume()` inside the test has the same effect. The rule: **construct** valid inputs (here `stays(max_nights=30)`) and keep `filter`/`assume` for conditions that reject only a small share of inputs.

## Step 6. Set the budget

Hypothesis runs 100 examples per property by default. The final suite of seven properties took 0.34 s; a profile for the nightly build raises that tenfold:

```python
settings.register_profile("ci", max_examples=1000)   # pytest --hypothesis-profile=ci
```

With 1,000 examples per property the run took 2.78 s and still passed: cheap enough to keep in every build, well inside the 50 ms to 30 s budgets reported in industry {% cite goldstein2024pbt %}.

## Step 7. Ask mutmut whether the properties are strong

Passing properties show only that they found nothing. To see whether they *would* find something, mutmut planted 42 small faults in `billing.py` and ran the suite against each ([Mutation Testing](mutation.md)). Its whole configuration is three lines in `pyproject.toml`, plus the path the tests import from:

```toml
[tool.mutmut]
source_paths = ["src/"]

[tool.pytest.ini_options]
pythonpath = ["src"]
```

At this point the no-crash property was still the one-sided version from Step 1:

```python
@given(st.dates(), st.dates())
def test_any_two_dates_give_result_or_value_error(a, b):  # no crash
    try:
        split_by_month(a, b)
    except ValueError:
        assert b <= a
```

`mutmut run` then printed the result below, condensed: mutmut's summary line, then `mutmut results` with each mutant's change from `mutmut show` on the right:

```text
42/42  🎉 37  ⏰ 1  🙁 4          # killed, timed out, survived (first run, 21 s)
billing.x_nights__mutmut_1: survived      if check_out < check_in:   (was <=)
billing.x_nights__mutmut_2: survived      raise ValueError(None)
billing.x_nights__mutmut_3: survived      raise ValueError("XXcheck_out must be after check_inXX")
billing.x_nights__mutmut_4: survived      raise ValueError("CHECK_OUT MUST BE AFTER CHECK_IN")
billing.x_split_by_month__mutmut_7: timeout   while start <= check_out:
```

The timeout is an infinite loop, so the mutant was detected. The first survivor is a real gap: with `<`, a stay that checks out on the day it checks in returns an empty list instead of raising, and the one-sided property only checks what happens *when* `ValueError` is raised, never that it *is* raised for an invalid stay. Tightening it to state the contract in both directions killed it:

```python
@given(st.dates(), st.dates())
def test_valid_ranges_split_and_others_raise(a, b):     # no crash, plus the contract
    if b > a:
        assert split_by_month(a, b)
    else:
        with pytest.raises(ValueError):
            split_by_month(a, b)
```

```text
42/42  🎉 38  ⏰ 1  🙁 3
```

## Decision

**Criterion:** the properties are strong enough when every surviving mutant is either killed or judged harmless, with the judgement written down. The three survivors only change the wording of an error message, which no caller reads; the team accepted them. Mutation score: 39 of 42 detected, 93%. **Decision: the seven properties replace the two March examples**, and the suite runs with the `ci` profile nightly.

One risk is recorded, not tested: `split_by_month(date(9999, 12, 30), date(9999, 12, 31))` raises `year 10000 is out of range`, found by reading the fix, not by Hypothesis, which passed 1,000 examples over all dates without drawing it. The booking engine refuses dates after 2099, so the team left it.

## The finished artifact

The property file as it now stands in the repository; copy the structure, replace the properties.

```python
from collections import Counter
from datetime import date, timedelta

import pytest
from hypothesis import example, given, settings, strategies as st

from billing import nights, split_by_month

settings.register_profile("ci", max_examples=1000)


@st.composite
def stays(draw, first=date(2000, 1, 1), last=date(2099, 12, 31), max_nights=400):
    check_in = draw(st.dates(min_value=first, max_value=last))
    n = draw(st.integers(min_value=1, max_value=max_nights))
    return check_in, check_in + timedelta(days=n)


@given(stays())
@example((date(2000, 12, 1), date(2000, 12, 2)))       # regression: December crash
def test_nights_are_preserved(stay):                     # invariant
    pieces = split_by_month(*stay)
    assert sum(nights(a, b) for a, b in pieces) == nights(*stay)


@given(stays())
def test_pieces_join_back_into_the_stay(stay):           # round trip
    pieces = split_by_month(*stay)
    assert pieces[0][0] == stay[0] and pieces[-1][1] == stay[1]
    for (_, end), (start, _) in zip(pieces, pieces[1:]):
        assert end == start


@given(stays())
def test_each_piece_is_inside_one_month(stay):           # invariant
    for a, b in split_by_month(*stay):
        last_night = b - timedelta(days=1)
        assert (a.year, a.month) == (last_night.year, last_night.month)


@given(stays())
def test_matches_day_by_day_count(stay):                 # differential (slow oracle)
    check_in, check_out = stay
    expected = Counter()
    d = check_in
    while d < check_out:
        expected[(d.year, d.month)] += 1
        d += timedelta(days=1)
    got = {(a.year, a.month): nights(a, b) for a, b in split_by_month(*stay)}
    assert got == dict(expected)


@given(stays())
def test_splitting_a_piece_changes_nothing(stay):        # idempotence
    for piece in split_by_month(*stay):
        assert split_by_month(*piece) == [piece]


@given(stays(last=date(2070, 12, 31)))
def test_shift_by_28_years(stay):                        # metamorphic
    shifted = tuple(d.replace(year=d.year + 28) for d in stay)
    before = [nights(a, b) for a, b in split_by_month(*stay)]
    after = [nights(a, b) for a, b in split_by_month(*shifted)]
    assert before == after


@given(st.dates(), st.dates())
def test_valid_ranges_split_and_others_raise(a, b):     # no crash, plus the contract
    if b > a:
        assert split_by_month(a, b)
    else:
        with pytest.raises(ValueError):
            split_by_month(a, b)
```

## Common mistakes

1. **A property that restates the code.** `assert split_by_month(a, b) == my_copy_of_the_loop(a, b)` repeats the bug. *Spot it:* the oracle uses the same algorithm; a differential oracle must be simpler or independent, like the day-by-day count.
2. **A one-sided no-crash property.** "Does not raise anything except `ValueError`" passed while an invalid stay returned `[]`. *Spot it:* a surviving mutant on a guard condition; state what valid inputs must return as well.
3. **Filtering instead of constructing.** *Spot it:* `FailedHealthCheck: filter_too_much`, or a test that is suddenly slow; build valid values with `st.composite` or `flatmap`.
4. **Trusting the database to keep a regression.** *Spot it:* the fix has no `@example` line; a fresh CI runner may never draw the failing case again.
5. **Reading a passing run as proof.** *Spot it:* no mutation run, or nobody has looked at the generator's ranges; the year-9999 case passed 1,000 examples.

## How solid is this?

- **Where it comes from.** Every command and output above was run for this page on 29 October 2026 with the versions listed. The property patterns follow the Jane Street interview study {% cite goldstein2024pbt %}; shrinking and the example database are described in the Hypothesis paper {% cite maciver2019hypothesis %}; QuickCheck's authors found errors split between generators, properties and code {% cite claessen2000quickcheck %}.
- **What is contested.** Using mutation score as the stopping rule is a practitioner choice; mutants stand in for real faults only as far as the coupling effect holds {% cite jia2011mutation %}.
- **What we do not hold.** A study measuring how many properties a function needs; the seven here came from the specification, not from a rule.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
