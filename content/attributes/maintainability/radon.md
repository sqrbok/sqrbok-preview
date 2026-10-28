---
title: Rating Python Code with Radon
parent: Maintainability
grand_parent: Quality Attributes
nav_order: 3
layout: default
page_type: deep-dive  # the radon MI formula worked on one file, and a tested recipe from radon output to two SIG-style ratings
math: katex
---

# Rating Python Code with Radon

**Radon** is a Python tool that computes cyclomatic complexity, raw line counts, Halstead metrics and the Maintainability Index; this page shows how its MI is computed and how its output gives two of the [SIG properties](measurement.md), unit complexity and, approximately, unit size {% cite radon_docs %}.

## 1. The example file

All numbers here come from radon 6.0.1 on `shop.py`, also used on the [Maintainability Index](../../define/measurement/maintainability-index.md) page under Measurement; the formula is here:

```python
# Pricing rules for the web shop.
def delivery_fee(weight_kg, express, country):
    if weight_kg <= 0:
        raise ValueError(
            "weight must be positive")

    fee = 5.0
    if weight_kg > 10:
        fee += 2.0 * (weight_kg - 10)
    if express and country == "RU":
        fee *= 1.5
    elif express:
        fee *= 2.0
    return round(fee, 2)


def total(prices, coupon):
    subtotal = sum(prices)
    if coupon > subtotal:
        return 0.0
    return subtotal - coupon
```

The file has 21 lines: line 1 is the comment, lines 2–14 are `delivery_fee` (line 6 blank), lines 15–16 are blank, and lines 17–21 are `total`. `radon raw shop.py` counts LOC 21 (all lines), 3 blank lines, 1 comment line, SLOC 17 (source lines: every line that is neither blank nor only a comment) and LLOC 16 (logical lines: statements). Line 5, which holds only the string of the error message, is a source line, so it is in SLOC; but `raise ValueError(` on line 4 and the string on line 5 are one statement, so they count once in LLOC.

## 2. The Maintainability Index in radon

The MI combines Halstead volume $$V = N \log_2 n$$ ($$N$$ operator and operand occurrences, $$n$$ distinct ones), cyclomatic complexity $$G$$ and lines of code $$L$$. Coleman et al.'s three-metric form runs up to 171; above 85 is highly maintainable, 65 to 85 moderately, below 65 difficult {% cite coleman1994using %}:

$$MI = 171 - 5.2 \ln V - 0.23\,G - 16.2 \ln L$$

Radon scores a whole file. It adds a comment term and rescales to 0–100, clamped, in `mi_compute` {% cite radon_docs %}:

$$MI_{\text{radon}} = \Big(171 - 5.2 \ln V - 0.23\,G - 16.2 \ln L + 50 \sin\sqrt{2.46 \cdot \text{radians}(perCM)}\Big) \cdot \frac{100}{171}$$

Each term takes one of the counts above. $$L$$ is LLOC (16); LOC (21) feeds nothing; SIG's unit size uses SLOC. $$G$$ is one complexity for the whole file, 1 plus every decision point in it: in radon's code, the sum of each function's CC minus 1, plus 1. $$perCM$$ is the comment lines (plus multi-line strings) as a percentage of SLOC.

**Example.** Radon counts Halstead operators and operands only inside five kinds of expression: binary operations (`*`, `-`), augmented assignments (`+=`, `*=`), Boolean operations (`and`), comparisons and unary operations. Assignments, calls, `return`, `raise` and bare conditions such as `elif express:` add nothing. The two sides of each operation are its operands; a nested sub-expression is one operand of the operation around it, and, in radon's implementation (checked in its code), operands are told apart per function while operators are pooled across the file. For `shop.py` radon sees:

| Operator (7 distinct) | Count |
|---|:-:|
| `<=` | 1 |
| `>` | 2 |
| `+=` (Add) | 1 |
| `*`, `*=` (Mult) | 3 |
| `-` (Sub) | 2 |
| `and` | 1 |
| `==` | 1 |
| **Total N₁** | **11** |

| Operand (14 distinct) | Count |
|---|:-:|
| `weight_kg` | 3 |
| `fee` | 3 |
| `10`, `2.0` | 2 each |
| `0`, `1.5`, `express`, `country`, `"RU"` | 1 each |
| sub-expressions `2.0 * (…)`, `weight_kg - 10`, `country == "RU"` | 1 each |
| `coupon`, `subtotal` (in `total`) | 2 each |
| **Total N₂** | **22** |

So $$N = 11 + 22 = 33$$ and $$n = 7 + 14 = 21$$, and $$V = 33 \log_2 21 = 144.9$$. $$G = (6 - 1) + (2 - 1) + 1 = 7$$. $$L = 16$$ and $$perCM = 1/17 = 5.88\%$$. The three main terms give $$171 - 25.88 - 1.61 - 44.92 = 98.6$$, above Coleman's 85. The comment term takes the percentage 5.88 as if it were degrees: radians(5.88) = 0.1027, 2.46 × 0.1027 = 0.2526, √0.2526 = 0.5026, sin 0.5026 = 0.4817, × 50 = 24.08. Feeding a percentage into radians() has no meaning in the units; it is an empirical fit, so $$(98.6 + 24.08) \cdot 100/171 = 71.74$$, which `radon mi -s shop.py` prints as A (71.74). Without the comment line the file scores 57.66. The published four-metric form adds $$50\sin\sqrt{2.4 \cdot perCM}$$ {% cite ardito2020maintainability %} (−28.88 with the percentage 5.88), so tools' MI values differ.

Radon grades MI A above 19, B above 9 up to 19, C at 9 or less {% cite radon_docs %}; so 19.5 is an A. Coleman's 85 and 65 become 49.7 and 38.0 on radon's scale, and radon's A boundary is 32.5 on the 0–171 scale. The comparison is loose: radon's value includes the comment term, Coleman's cut-offs do not.

## 3. Recipe: from radon output to ratings

Tested on `shop.py` and a file with a class:

1. `radon cc -s -j shop.py` lists each function with `complexity`, `lineno` and `endline`: `delivery_fee` CC 6, lines 2–14; `total` CC 2, lines 17–21. endline − lineno + 1 (13 and 5) is an acceptable approximation of a function's length, but it counts blank and comment lines inside it. For SLOC per function, run this script with the file names as arguments; it prints `shop.py delivery_fee 6 12` and `shop.py total 2 5`:

   ```python
   import json, subprocess, sys
   from textwrap import dedent
   from radon.raw import analyze
   out = subprocess.check_output(["radon", "cc", "-j", *sys.argv[1:]])
   for path, blocks in json.loads(out).items():
       lines = open(path).read().splitlines()
       for f in blocks:
           if f["type"] == "class":  # its methods are listed on their own
               continue
           span = lines[f["lineno"] - 1 : f["endline"]]
           body = dedent("\n".join(span))
           print(path, f["name"], f["complexity"], analyze(body).sloc)
   ```

   For a class, radon lists the class (spanning all its methods, with their complexity added up) and each method again on its own; the script skips the class entry so that no line is counted twice. On this file it prints `cart.py add 3 3`:

   ```python
   class Cart:
       def add(self, item, qty):
           if qty > 0 and item:
               self.items.append((item, qty))
   ```

2. Put each function into SIG's band by its CC (1–10, 11–20, 21–50, above 50) and add up its SLOC per band. Here all 17 lines are low risk, so unit complexity gets 5 stars. Do not use radon's own letters for this: radon grades CC A for 1–5, B 6–10, C 11–20, D 21–30, E 31–40 and F 41 or more, so `delivery_fee` is a B in radon but low risk for SIG. The same SLOC give a unit-size profile; our sources do not publish SIG's unit-size bands, so report shares of SLOC in bins of your own choice, such as 1–15, 16–30, 31–60 and over 60 lines.
3. `radon mi -s` gives one score per file. No published threshold says what share of files may be B or C: report the shares and name the worst files, not a project average.

Radon has no cognitive complexity, duplication, interfacing, coupling or component measures; tested Python tools are on [Cognitive Complexity](cognitive-complexity.md) (next page), [Measuring Maintainability](measurement.md) and [Propagation Cost](propagation-cost.md). A pydeps module is a file; SIG's modules are classes or files rated by incoming dependencies; a design module may span many files.

## When to use it — and when not

Use radon to find complex functions and profile complexity and size; never compare MI across tools.

## Limitations

Radon's MI rewards comment lines whatever they say, and its thresholds are not calibrated on a benchmark the way SIG's are.

## How solid is this?

- **Where it comes from.** Radon's documentation and source, version 6.0.1 {% cite radon_docs %}; the MI formulas from Coleman et al. and as summarised by Ardito et al. {% cite coleman1994using ardito2020maintainability %}. Every number here was printed by radon and recomputed in Python.
- **What is contested.** Which MI formula is "the" MI: the comment term differs between sources and tools.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
