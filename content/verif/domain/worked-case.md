---
title: "Worked Case: Testing a Fare Function from Its Specification"
parent: Input Domain Testing
nav_order: 8
layout: default
page_type: worked-case
---

# Worked Case: Testing a Fare Function from Its Specification

This worked case takes one function, a public-transport fare calculator, from its written specification to a decision about whether its tests are done. It applies [equivalence partitioning](equivalence.md), [boundary value analysis](boundary.md), [boundary strategies](boundary-strategies.md) and a [decision table](decision-tables.md) in the order a tester uses them, and then classifies each failure to decide what to fix. (Constructed example; every run below was made for this page with Python 3.12.9, pytest 9.2.0, pytest-cov 7.1.0 and coverage.py 7.17.0.)

## Example input: a specification, not code

The team has a ticket in the backlog and a function signature, `fare(age, zones, card="none")`, which returns a price in cents or raises `ValueError`. The same rules sit behind a `POST /fare` endpoint, where `ValueError` becomes `400 Bad Request`. The specification reads:

1. `age` is a whole number of years, 0–120; `zones` is a whole number, 1–5; `card` is `none`, `student` or `senior`. Anything else is rejected.
2. The full fare is 200 cents for one zone plus 100 cents for each further zone.
3. Without a card, ages 0–5 travel free, ages 6–17 pay half, and ages 18 and over pay the full fare.
4. A student card is valid only for ages 18–25 and gives 30% off. A senior card is valid only from age 65 and gives half price. A card used outside its ages is rejected.

Where do such inputs come from on a real system? The ranges come from the specification and the API schema (an OpenAPI file or a validation library declares `minimum` and `maximum`); the rules come from the product owner's ticket; and the companion values for tests come from production logs or analytics, which show the typical request (here, three zones and no card). When a rule is ambiguous, for example whether "from age 65" includes 65, the tester asks before writing the test.

## Step 1. Partition each input

List the classes per input, valid and invalid, as on [Equivalence Partitioning](equivalence.md). Age has five valid classes, not three, because the cards add the edges 18–25 and 65+:

| Input | Valid classes | Invalid classes | Format tests |
|---|---|---|---|
| `age` | 0–5, 6–17, 18–25, 26–64, 65–120 | below 0, above 120 | not an integer (12.5), a string ("12") |
| `zones` | 1–5 | below 1, above 5 | not an integer (2.5), a string ("3") |
| `card` | none, student, senior | any other value ("gold") | null (`None`) |

The class 18–25 is different from 26–64 only for a student card, and 65–120 is different from 26–64 only for a senior card. That dependency between `age` and `card` is the whole difficulty of this function.

## Step 2. Pick ON and OFF points, and choose the scheme

Every place where the specified result changes is a boundary; each gets one ON and one OFF point (Weak 1×1). The **ON point** satisfies the boundary condition as written (for "6–17 pay half", the condition `age ≤ 17` holds at 17); the **OFF point** is the nearest value that does not (18). For age the edges are −1/0, 5/6, 17/18, 25/26, 64/65 and 120/121; for zones, 0/1 and 5/6.

**Judgement call 1: which other values does each boundary test carry?** Criterion: the companion values must make the two sides of the boundary *produce different results*, or the test cannot fail. The edge 25/26 changes the result only with `card = student`, and 64/65 only with `card = senior`: tested with no card, ages 64 and 65 both pay the full 400 cents, so a wrong senior edge passes unseen. All other companions take the typical value seen in the logs, `zones = 3` and `age = 40`.

**Judgement call 2: is a standard range scheme enough?** The common textbook schemes, described on [Boundary Testing Strategies](boundary-strategies.md), vary one input at a time while the others stay at their *nominal* value, the midpoint of the range: *basic* BVA uses min, min+, max− and max (4*n* + 1 tests for *n* inputs) and *robust* BVA adds under and over (6*n* + 1) {% cite jorgensen2013software %}. They apply to numeric ranges, so *n* = 2 here (`age` and `zones`); `card` is a list of values, not a range, and stays `none`. Criterion: a scheme is enough only if its points sit on every edge where behaviour changes. This script runs both schemes against the faulty implementation of Step 5:

```python
# Points are (age, zones); card stays "none". Nominal = range midpoint.
from fare import fare

def spec(age, zones):                     # oracle, from the specification
    if not (0 <= age <= 120 and 1 <= zones <= 5):
        return "reject"
    price = 200 + 100 * (zones - 1)
    return 0 if age <= 5 else price // 2 if age <= 17 else price

def run(age, zones):
    try:
        return fare(age, zones)
    except ValueError:
        return "reject"

def points(robust):
    pts = [(60, 3)]                                    # all nominal
    for v in [0, 1, 119, 120] + ([-1, 121] if robust else []):
        pts.append((v, 3))                             # vary age
    for v in [1, 2, 4, 5] + ([0, 6] if robust else []):
        pts.append((60, v))                            # vary zones
    return pts

for robust in (False, True):
    pts = points(robust)
    fails = [p for p in pts if run(*p) != spec(*p)]
    print(f"{'robust 6n+1' if robust else 'basic 4n+1'}: {len(pts)} tests {pts}")
    print(f"  failing: {fails}")
```

```text
$ python bva_schemes.py
basic 4n+1: 9 tests [(60, 3), (0, 3), (1, 3), (119, 3), (120, 3), (60, 1), (60, 2), (60, 4), (60, 5)]
  failing: []
robust 6n+1: 13 tests [(60, 3), (0, 3), (1, 3), (119, 3), (120, 3), (-1, 3), (121, 3), (60, 1), (60, 2), (60, 4), (60, 5), (60, 0), (60, 6)]
  failing: [(60, 6)]
```

Robust BVA finds the missing upper limit on zones because it tries 6 zones; neither scheme finds a fault at an inner edge such as 17/18 or at a card edge, because they never place a point there. So the suite uses Weak 1×1 at every edge, plus the robust out-of-range points, which it already contains.

## Step 3. Build the decision table for the dependent inputs

`card` and the age class combine, so they get a decision table: 3 cards × 5 age classes = 15 combinations. Merging rules with the same action that differ in one condition, as on [Decision Tables](decision-tables.md), leaves 7 rules (plus the rule for an unknown card):

| Rule | card | age | Action |
|---|---|---|---|
| R1 | none | 0–5 | free |
| R2 | none | 6–17 | half fare |
| R3 | none | 18–120 | full fare |
| R4 | student | 18–25 | 30% off |
| R5 | student | 0–17 or 26–120 | reject |
| R6 | senior | 65–120 | half fare |
| R7 | senior | 0–64 | reject |

Check the total: R1–R3 cover 5 combinations, R4 and R6 one each, R5 four and R7 four, so 5 + 1 + 4 + 1 + 4 = 15.

## Step 4. Write the expected values from the specification

The **oracle** step: each expected value is worked out by hand from the rules before the code runs. Three zones cost 200 + 2 × 100 = 400 cents, half fare is 200, and 30% off is 400 × 0.7 = 280. Copying the result the code returns into the test would make the suite confirm the code, faults included.

```python
import pytest
from fare import fare

REJECT = ValueError  # spec: invalid input is rejected

# id, age, zones, card, expected (from the spec, worked out by hand)
CASES = [
    ("A1", -1, 3, "none", REJECT),   ("A2", 0, 3, "none", 0),
    ("A3", 5, 3, "none", 0),         ("A4", 6, 3, "none", 200),
    ("A5", 17, 3, "none", 200),      ("A6", 18, 3, "none", 400),
    ("A7", 120, 3, "none", 400),     ("A8", 121, 3, "none", REJECT),
    ("A9", 12.5, 3, "none", REJECT), ("A10", "12", 3, "none", REJECT),
    ("Z1", 40, 0, "none", REJECT),   ("Z2", 40, 1, "none", 200),
    ("Z3", 40, 5, "none", 600),      ("Z4", 40, 6, "none", REJECT),
    ("C1", 17, 3, "student", REJECT),("C2", 18, 3, "student", 280),
    ("C3", 25, 3, "student", 280),   ("C4", 26, 3, "student", REJECT),
    ("C5", 64, 3, "senior", REJECT), ("C6", 65, 3, "senior", 200),
    ("C7", 40, 3, "gold", REJECT),   ("C8", 65, 3, "none", 400),
    ("Z5", 40, 2.5, "none", REJECT), ("Z6", 40, "3", "none", REJECT),
    ("C9", 40, 3, None, REJECT),     # card null
    # interior points, away from every edge
    ("E1", 10, 3, "none", 200),      ("E2", 21, 3, "student", 280),
    ("E3", 80, 3, "senior", 200),    ("E4", 40, 3, "student", REJECT),
]

@pytest.mark.parametrize("tid,age,zones,card,expected", CASES,
                         ids=[c[0] for c in CASES])
def test_fare(tid, age, zones, card, expected):
    if expected is REJECT:
        with pytest.raises(ValueError):
            fare(age, zones, card)
    else:
        assert fare(age, zones, card) == expected
```

Every decision-table rule has a test: R1 by A2, R2 by A4, R3 by A6 and C8, R4 by C2, R5 by C1, C4 and E4, R6 by C6 and R7 by C5. The interior points E1–E4 sit inside a class, away from every edge; Step 6 needs them.

## Step 5. Run against the implementation

The first implementation submitted for review:

```python
BASE, PER_ZONE = 200, 100          # cents

def fare(age, zones, card="none"):
    if not isinstance(age, int) or not 0 <= age <= 120:
        raise ValueError("age")
    if not isinstance(zones, int) or zones < 1:
        raise ValueError("zones")
    if card not in ("none", "student", "senior"):
        raise ValueError("card")
    price = BASE + PER_ZONE * (zones - 1)
    if card == "student":
        if not 18 <= age <= 26:
            raise ValueError("card not valid for age")
        return price * 30 // 100
    if card == "senior":
        if age < 65:
            raise ValueError("card not valid for age")
        return price // 2
    if age <= 5:
        return 0
    if age < 17:
        return price // 2
    return price
```

```text
$ pytest -q --tb=line
FAILED test_fare.py::test_fare[A5] - AssertionError: assert 400 == 200
FAILED test_fare.py::test_fare[Z4] - Failed: DID NOT RAISE ValueError
FAILED test_fare.py::test_fare[C2] - AssertionError: assert 120 == 280
FAILED test_fare.py::test_fare[C3] - AssertionError: assert 120 == 280
FAILED test_fare.py::test_fare[C4] - Failed: DID NOT RAISE ValueError
FAILED test_fare.py::test_fare[E2] - AssertionError: assert 120 == 280
6 failed, 23 passed in 0.01s
```

## Step 6. Classify each failure: domain or computation

A **domain error** sends an input down the wrong path: a boundary in the wrong place, missing, or with the wrong operator. A **computation error** takes the right path and computes the wrong value {% cite white1980domain %}. **Judgement call 3.** Criteria, in order: (a) an input accepted that should be rejected, or the reverse, took the wrong path, so it is a domain error; (b) otherwise compare with an **interior point**, a value inside the same class, on the expected side of the edge, with the same other inputs: if it passes, only the edge is wrong (domain error); if it fails too, the whole class computes wrongly (computation error).

| Failing test | Point | Criterion | Type | Cause in the code |
|---|---|---|---|---|
| A5 (17, none) | ON of 17/18 | (b): interior E1 (10, none) passes | domain: closure | `age < 17` instead of `age <= 17` |
| C4 (26, student) | OFF of 25/26 | (a): accepted, should be rejected; interior E4 (40, student) is rejected correctly | domain: shift | `age <= 26` instead of `<= 25` |
| Z4 (zones 6) | OFF of 5/6 | (a): accepted, should be rejected | domain: missing boundary | no upper limit on `zones` |
| C2, C3, E2 | ON points and interior point | (b): interior E2 (21, student) fails too; E2 itself fails, so the whole class is wrong | computation | `price * 30 // 100` charges 30% instead of 70% |

The two kinds of failure also look different: C4 and Z4 fail because no rejection happens at all, a path question, while C2, C3 and E2 return a wrong *amount*. One cause per row: four faults, six failures.

## Step 7. Decide: what to fix, and are we done?

**Judgement call 4.** The developer fixes the four faults:

```python
BASE, PER_ZONE = 200, 100          # cents

def fare(age, zones, card="none"):
    if not isinstance(age, int) or not 0 <= age <= 120:
        raise ValueError("age")
    if not isinstance(zones, int) or not 1 <= zones <= 5:
        raise ValueError("zones")
    if card not in ("none", "student", "senior"):
        raise ValueError("card")
    price = BASE + PER_ZONE * (zones - 1)
    if card == "student":
        if not 18 <= age <= 25:
            raise ValueError("card not valid for age")
        return price * 70 // 100
    if card == "senior":
        if age < 65:
            raise ValueError("card not valid for age")
        return price // 2
    if age <= 5:
        return 0
    if age <= 17:
        return price // 2
    return price
```

Criterion for "done": every class in the Step 1 table has a test, every boundary has its ON and OFF point carried by companions that make it visible, every decision-table rule has a test, all tests pass, and branch coverage reports no code the specification did not mention. The rerun on the fixed version:

```text
$ pytest -q --cov=fare --cov-branch --cov-report=term-missing
Name      Stmts   Miss Branch BrPart  Cover   Missing
-----------------------------------------------------
fare.py      22      0     18      0   100%
29 passed in 0.02s
```

All of these hold, so testing of this function stops here. Had the report shown an uncovered branch, it would point at behaviour the specification never described, a question for the product owner rather than a test to add blindly (see [Using Coverage in Practice](../coverage/practice.md)).

## The artifact: a partition and test table to copy

Fill one row per test; the empty table is the plan, the filled one is the review record.

| ID | Technique | Input under test | Class or edge | Companion values | Table rule | Expected (from spec) | Spec clause | Result | Error type if failed |
|---|---|---|---|---|---|---|---|---|---|
| A5 | BVA ON | age = 17 | edge 17/18 | zones 3, card none | R2 | 200 | 3 | fail: 400 | domain (closure) |
| C4 | BVA OFF | age = 26 | edge 25/26 | zones 3, card student | R5 | reject | 4 | fail: 120 | domain (shift) |
| E2 | interior point | age = 21 | 18–25 | zones 3, card student | R4 | 280 | 4 | fail: 120 | computation |
| … | | | | | | | | | |

## Common mistakes

Five mistakes recur when this procedure is applied for the first time; each leaves a visible trace.

- **Expected values copied from the code.** Spot it: the expected numbers appear in no specification clause, or a test was "fixed" by changing its expected value after a failure.
- **A boundary test with the wrong companion.** Spot it: the ON and OFF tests of one edge have the same expected result, as with ages 64 and 65 without a senior card.
- **Range BVA only.** Spot it: the test values are all min, min+, max−, max and their neighbours, and no value sits at an inner class edge.
- **Every failure called "a bug at the boundary".** Spot it: the interior point of the class fails too; that is a computation error, and moving the boundary will not fix it.
- **Format classes forgotten.** Spot it: no test sends a string, a decimal or a missing field, although the endpoint receives JSON from clients you do not control.

## How solid is this?

- **Where it comes from.** The partition, boundary and decision-table steps follow the method pages and their sources {% cite tian2005sqe beizer1995black %}; the 4*n* + 1 and 6*n* + 1 schemes are textbook practice {% cite jorgensen2013software %}; the domain/computation distinction is White and Cohen's {% cite white1980domain %}.
- **What we do not hold.** The faults were planted for this page, so the run shows what the suite *can* detect, not how often such faults occur in practice. The "done" criterion is a practitioner rule, not a measured stopping rule.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
