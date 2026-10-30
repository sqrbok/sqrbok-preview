---
title: PICT
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 15
layout: default
page_type: tool-card
tested_with: PICT 3.7.4
---

# PICT

**PICT** (Pairwise Independent Combinatorial Testing) is Microsoft's command-line generator of combinatorial tests: from a text model of parameters, values and constraints, it prints a small table of tests in which every pair of values appears at least once {% cite microsoft_pict %}.

The quality question it answers: *which few tests cover every interaction between two inputs?* The method, and why pairs catch most interaction faults, is on [Combinatorial Testing](../../verif/combinatorial/index.md).

*Run for this card with PICT 3.7.4, October 2026.*

## Install and quick start

```bash
brew install pict        # or build from github.com/microsoft/pict; Windows: download pict.exe
```

A model for a parking ticket machine, `machine.txt` (constructed example): one parameter per line, then constraints.

```text
Payment:  cash, card, app
Member:   yes, no
Stay:     short, hours, overnight
Language: en, de, fr
Receipt:  paper, email, none

IF [Payment] = "cash" THEN [Receipt] <> "email";
```

```bash
pict machine.txt > machine.tsv     # pairwise (strength 2)
pict machine.txt /o:3              # every triple instead
pict machine.txt /s                # statistics only
```

## Reading the output

The output is tab-separated, one test per row, with a header (first rows shown):

```text
Payment	Member	Stay	Language	Receipt
cash	no	hours	fr	paper
card	yes	overnight	de	none
app	yes	hours	de	email
card	no	hours	en	none
...
```

`pict machine.txt /s` prints:

```text
Combinations:   77
Generated tests:13
```

All combinations of the five parameters number 3 × 2 × 3 × 3 × 3 = 162; 13 tests cover all 77 allowed pairs. No pairwise suite can be smaller than 9, the product of the two largest parameters (3 × 3). The cash-and-email pair is excluded, and no row contains it. Without the constraint, PICT generated 11 rows; with strength 3 (`/o:3`), 34.

## Common mistakes

- **Deleting forbidden rows afterwards instead of writing a constraint.** Removing rows from an unconstrained table also removes the other pairs they covered. Put the rule in the model, and PICT covers those pairs elsewhere.
- **Expecting PICT to give the expected results.** It chooses inputs; each row still needs an oracle, the result the test should get.
- **Too many values per parameter.** The table grows with the product of the two largest parameters. Use equivalence classes and boundaries (one value per class), not every value.
- **Mixing invalid values into normal rows.** Mark an invalid value with `~` (`Quantity: 1, 5, ~0`): PICT then puts at most one invalid value in a row, so one error cannot hide another.
- **Assuming the array is right.** Check a generated table against the model before relying on it; [Combinatorial Testing Tools](../../verif/combinatorial/tools.md) has a short checking script.

## In CI

Keep `machine.txt` in version control next to the tests and generate the table in the build; PICT gives the same output on every run unless `/r` asks it to randomise. A parametrised pytest test can read `machine.tsv` row by row.

## Go deeper

- [Combinatorial Input Models](../../verif/combinatorial/practice.md) and [Constraints and Invalid Values](../../verif/combinatorial/advanced.md): building the model.
- [Combinatorial Testing Tools](../../verif/combinatorial/tools.md): PICT against ACTS and CASA, seed tests (`/e`), and a pytest file driven by the table.
- [ACTS](acts.md): NIST's generator, for higher strengths and mixed-strength suites.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
