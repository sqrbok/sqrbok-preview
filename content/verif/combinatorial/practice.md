---
title: Combinatorial Input Models
parent: Combinatorial Testing
nav_order: 3
layout: default
---

# Combinatorial Input Models

An **input model** lists the parameters of the system under test and, for each, a small set of values, usually one representative per equivalence class (see [Input Domain Testing](../domain/index.md)); a generator then combines the values into tests that cover every combination of values of any *t* parameters, where *t*, the **interaction strength**, is usually 2 (pairwise) {% cite grindal2005survey %}. The model decides what combinatorial testing can find: a failure whose triggering value is not in the model will not be found at any strength.

## 1. Worked example: a Find dialog

The lecture's example is a text editor's Find dialog with four parameters:

| Parameter | Values | Count |
|---|---|---|
| FindWhat | Empty, lowercase, MixedCase, UPPER, space (text with a space), multiword (several words), special (special characters), long (a long string) | 8 |
| MatchCase | Yes, No | 2 |
| Direction | Up, Down | 2 |
| File | None (no match), Single (one match), Multiple (several matches) | 3 |

All combinations need 8 · 2 · 2 · 3 = 96 tests. Written as a PICT model, one parameter per line, the same input reads:

```text
FindWhat: Empty, lowercase, MixedCase, UPPER, space, multiword, special, long
MatchCase: Yes, No
Direction: Up, Down
File: None, Single, Multiple
```

`pict find.txt` prints a tab-separated table; the first 8 of its 24 rows are {% cite microsoft_pict %}:

```text
FindWhat	MatchCase	Direction	File
UPPER	No	Up	None
multiword	No	Down	Single
space	Yes	Up	Multiple
long	No	Down	Multiple
UPPER	Yes	Down	Single
lowercase	Yes	Up	Single
long	Yes	Down	None
MixedCase	No	Up	None
```

The full output has 24 rows. That is the minimum, because FindWhat and File alone have 8 · 3 = 24 pairs. The model has 72 pairs in all (8·2 + 8·2 + 8·3 + 2·2 + 2·3 + 2·3), and the check script on [Combinatorial Testing Tools](tools.md) confirms that all 72 appear. `pict find.txt /o:3` gives 49 rows for all 140 triples, one more than the lower bound of 8 · 3 · 2 = 48: an array of strength *t* needs at least as many rows as the product of the *t* largest value counts, here FindWhat, File and one of the two-valued parameters.

| Strategy | Tests | What it guarantees |
|---|---|---|
| All combinations | 96 | everything |
| 3-way (PICT) | 49 | every triple of values |
| Pairwise (PICT) | 24 | every pair of values |
| Base choice | 12 | every value, each varied from one base test |

## 2. Base choice is the cheaper alternative

**Base choice** starts from one base test of the most likely value of each parameter, then varies one parameter at a time. If the base test is (lowercase, No, Down, Single), the other 11 tests each change one value of it: 1 + 7 + 1 + 1 + 2 = 12 tests here {% cite grindal2005survey %}. Cohen et al. call it default testing: for four three-valued parameters its 9 tests cover only 30 of the 54 pairs, while a combinatorial design of the same 9 tests covers all 54 {% cite cohen1997aetg %}. In an experiment on five Unix-like programs with 131 seeded faults, base choice found as many faults as the pairwise strategies with fewer tests, and the two found partly different faults. The authors' advice: base choice alone for less important software, together with pairwise for critical software {% cite grindal2005survey %}.

## 3. Inputs or configurations

The same arithmetic serves two uses. In **input testing** the parameters are fields of one form or arguments of one call, all tested in one environment. In **configuration testing** the parameters are the environment (operating system, browser, database version) and the same test suite runs on every row. A row of a configuration array can be expensive: an installed machine, not a function call.

## 4. From abstract values to concrete tests

Each row names classes, not data. Someone still picks a concrete value for every class and works out the expected result. In Hu et al.'s industrial study, 28 faults needed only one input; the combinatorial team's suites found 25 of them and the in-house teams 20. The authors traced 8 of the 28 to random values chosen within a class and 3 to boundary values, which the in-house teams, in the authors' examples, did not use {% cite hu2020realworld %}. Choosing values well matters as much as choosing *t*.

## When to use it — and when not

- **Use** a pairwise model by default for forms, APIs and platform matrices; raise *t* for parameters whose failures are costly.
- **Do not** put invalid values in freely: an error stops the test before other values are tried; see [Constraints and Invalid Values](advanced.md).

## Limitations

The generator trusts the model. A missing class, a wrong boundary or a value that never triggers the fault defeats every strength, and the tests still need an oracle.

## How solid is this?

- **Where it comes from.** Grindal et al.'s survey of combination strategies, the AETG paper, Hu et al.'s five industrial case studies, and PICT 3.7.4 runs on the model above {% cite grindal2005survey cohen1997aetg hu2020realworld microsoft_pict %}.
- **What is contested.** Base choice's showing rests on one experiment with seeded faults.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
