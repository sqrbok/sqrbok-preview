---
title: Maintainability Index
parent: Measurement
nav_order: 7
layout: default
page_type: deep-dive   # two worked cases (one function, one file with a comment) where every input must be reproducible against radon
---

# Maintainability Index

The **Maintainability Index** (MI) combines Halstead volume, cyclomatic complexity and line count into one score for a file or module. It is a regression formula from Oman and Hagemeister's work on maintainability metrics, fitted to C and Pascal systems in the early 1990s {% cite oman1992maintainability %}, reported today by Visual Studio and `radon`.

## 1. The formula

Visual Studio uses a version rescaled to 0–100 {% cite msdocs_mi %}:

*MI = max(0, (171 − 5.2 ln V − 0.23 G − 16.2 ln L) · 100 / 171)*

where *V* is the Halstead volume, *G* the cyclomatic complexity and *L* the line count, all defined in [Size and Complexity Metrics](examples.md); radon takes *G* from its own count and *L* = LLOC. The original formula used per-module averages: average volume, average complexity and average lines per module. Here ln is the natural logarithm. Radon adds a fourth term for comments inside the bracket, so its full formula is

*MI = max(0, (171 − 5.2 ln V − 0.23 G − 16.2 ln L + 50 · sin(√(2.46 · C<sub>rad</sub>))) · 100 / 171)*

where *C* = (comments + docstring lines) ÷ SLOC × 100. Radon counts every `#` comment, even at the end of a code line, and the lines of multi-line strings (docstrings), which `radon raw` reports as *Multi*. SLOC leaves out both, so *C* is slightly more than the comments' share of all lines. With *C*<sub>rad</sub> = *C* · π / 180, radon converts the percentage as if it were an angle in degrees, so 6% becomes 0.105 {% cite radon_docs %}.

Visual Studio colours 20–100 green, 10–19 yellow and 0–9 red {% cite msdocs_mi %}. Radon's MI ranks use the same bands, and they are the ones to read in `radon mi` output: A for 20–100, B for 10–19, C for 0–9. These letters are a separate scale from the A–F complexity letters of `radon cc`:

| Command | Scores | A means | Lowest letter |
|---|---|---|---|
| `radon cc` | each function, method, class | complexity 1–5 | F (41 or more) |
| `radon mi` | each file | MI 20–100 | C (MI 0–9) |

The unscaled original ran up to 171, with cut-offs at 85 and 65 ([Measuring Maintainability](../../attributes/maintainability/measurement.md)).

## 2. Example: one function

For a file containing only `delivery_fee` from [Size and Complexity Metrics](examples.md), radon uses *V* = 114.7, its own complexity *G* = 6 (counting the `and`), and the **logical** line count *L* = LLOC = 11. There are no comments, so the fourth term is 0:

171 − 5.2 · ln 114.7 − 0.23 · 6 − 16.2 · ln 11 = 171 − 24.66 − 1.38 − 38.85 = 106.11, and 106.11 · 100 / 171 = **62.05**.

`radon mi -s` prints 62.06 (rank A); the last digit is rounding.

## 3. Example: a whole file

`radon mi` scores a **file**, not each function. It takes one Halstead volume over the whole file, one complexity (1 + all decision points in the file) and the file's logical lines. Here is `shop.py`, with the same function, a second one and one comment line:

```python
# Pricing rules for the web shop.
def delivery_fee(weight_kg, express, country):
    ...    # the 12 lines from Size and Complexity Metrics (this note is not in the real file)


def total(prices, coupon):
    subtotal = sum(prices)
    if coupon > subtotal:
        return 0.0
    return subtotal - coupon
```

Radon reports, for the whole file: *V* = 144.9 (*n₁* = 7, *n₂* = 14, *N₁* = 11, *N₂* = 22), *G* = 1 + 5 + 1 = 7 (five decision points in `delivery_fee`, one in `total`), *L* = LLOC = 16. Against the one-function file, `total` adds two operators (`>`, `-`) used once each, two operands (`coupon`, `subtotal`) used four times, one decision and 5 logical lines (`def`, `subtotal = …`, `if`, two `return`s): 9 + 2 = 11, 18 + 4 = 22, 12 + 2 = 14, 11 + 5 = 16. SLOC = 17 code lines plus 1 comment line, so *C* = 1 / 17 = 5.88%. (Dividing by all 18 non-blank lines, SLOC plus the comment, would give 71.38, not radon's 71.74.) `radon raw shop.py` shows where the line counts come from:

```text
    LOC: 21
    LLOC: 16
    SLOC: 17
    Comments: 1
    Multi: 0
    Blank: 3
```

Then:

- 5.2 · ln 144.9 = 25.88; 0.23 · 7 = 1.61; 16.2 · ln 16 = 44.92;
- comment term: 5.88 × π / 180 = 0.1027; √(2.46 · 0.1027) = 0.5026; sin 0.5026 = 0.4817; × 50 = **24.08**;
- 171 − 25.88 − 1.61 − 44.92 + 24.08 = 122.67, and 122.67 · 100 / 171 = **71.74**.

`radon mi -s shop.py` prints `A (71.74)`. Without the comment line the same code scores 57.66: **one comment line adds 14 points**. A tool without the comment term would report the lower value.

## When to use it — and when not

- **Use** MI to spot files that dropped a band between releases, with the same tool.
- **Use** the three inputs, not the index, to decide what to fix.
- **Do not** read MI as a verdict: it was fitted to a few 1990s C and Pascal systems.
- **Do not** compare MIs from tools with different line counts or comment terms: with *L* = LOC = 13, `delivery_fee` would score 60.5.

## How solid is this?

- **Where it comes from.** Oman and Hagemeister 1992; the rescaling from Microsoft; radon's version from its documentation and source. Every number was produced by radon 6.0.1 and recomputed by hand.
- **What is contested.** The comment term rewards comment lines whatever they say, so it is easy to game. MI also inherits the size effect of its inputs; [Measuring Maintainability](../../attributes/maintainability/measurement.md) compares it with benchmark-based models.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
