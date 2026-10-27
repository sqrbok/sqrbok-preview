---
title: Maintainability Index
parent: Measurement
nav_order: 7
layout: default
math: katex
page_type: deep-dive   # two worked cases (one function, one file with a comment) where every input must be reproducible against radon
---

# Maintainability Index

The **Maintainability Index** (MI) combines Halstead volume, cyclomatic complexity and line count into one score for a file or module. It is a regression formula from Oman and Hagemeister's work on maintainability metrics, fitted to C and Pascal systems in the early 1990s {% cite oman1992maintainability %}, reported today by Visual Studio and `radon`.

## 1. The formula

The original index used per-module averages of volume, complexity and lines, and ran up to 171:

$$
MI_{171} = 171 - 5.2 \ln \overline{V} - 0.23\,\overline{G} - 16.2 \ln \overline{L}
$$

Visual Studio rescales it to 0–100 and cuts it off at 0 {% cite msdocs_mi %}:

$$
MI = \max\left(0,\ \left(171 - 5.2 \ln V - 0.23\,G - 16.2 \ln L\right) \cdot \frac{100}{171}\right)
$$

where $$V$$ is the Halstead volume, $$G$$ the cyclomatic complexity and $$L$$ the line count, all defined in [Size and Complexity Metrics](examples.md), and $$\ln$$ is the natural logarithm. Radon takes $$G$$ from its own count and $$L$$ = LLOC, and adds a fourth term for comments inside the bracket, so its full formula is

$$
MI_{\text{radon}} = \max\left(0,\ \left(171 - 5.2 \ln V - 0.23\,G - 16.2 \ln L + 50 \sin\sqrt{2.46\, C_{\text{rad}}}\right) \cdot \frac{100}{171}\right)
$$

with

$$
C = \frac{\text{comments} + \text{docstring lines}}{\text{SLOC}} \cdot 100, \qquad C_{\text{rad}} = C \cdot \frac{\pi}{180}.
$$

Radon counts every `#` comment, even at the end of a code line, and the lines of multi-line strings (docstrings), which `radon raw` reports as *Multi*. SLOC leaves out both, so $$C$$ is slightly more than the comments' share of all lines. Radon converts the percentage as if it were an angle in degrees, so 6% becomes 0.105 {% cite radon_docs %}.

Visual Studio colours 20–100 green, 10–19 yellow and 0–9 red {% cite msdocs_mi %}. Radon's MI ranks use the same bands, and they are the ones to read in `radon mi` output: A for 20–100, B for 10–19, C for 0–9. These letters are a separate scale from the A–F complexity letters of `radon cc`:

| Command | Scores | A means | Lowest letter |
|---|---|---|---|
| `radon cc` | each function, method, class | complexity 1–5 | F (41 or more) |
| `radon mi` | each file | MI 20–100 | C (MI 0–9) |

The unscaled original had cut-offs at 85 and 65 ([Measuring Maintainability](../../attributes/maintainability/measurement.md)).

## 2. Example: one function

For a file containing only `delivery_fee` from [Size and Complexity Metrics](examples.md), radon uses $$V = 114.7$$, its own complexity $$G = 6$$ (counting the `and`), and the **logical** line count $$L = \text{LLOC} = 11$$. There are no comments, so the fourth term is 0:

$$
171 - 5.2 \ln 114.7 - 0.23 \cdot 6 - 16.2 \ln 11 = 171 - 24.66 - 1.38 - 38.85 = 106.11
$$

$$
MI = 106.11 \cdot \frac{100}{171} = \mathbf{62.05}
$$

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

Radon reports, for the whole file: $$V = 144.9$$ ($$n_1 = 7$$, $$n_2 = 14$$, $$N_1 = 11$$, $$N_2 = 22$$), $$G = 1 + 5 + 1 = 7$$ (five decision points in `delivery_fee`, one in `total`), $$L = \text{LLOC} = 16$$. Against the one-function file, `total` adds two operators (`>`, `-`) used once each, two operands (`coupon`, `subtotal`) used four times, one decision and 5 logical lines (`def`, `subtotal = …`, `if`, two `return`s): 9 + 2 = 11, 18 + 4 = 22, 12 + 2 = 14, 11 + 5 = 16. SLOC = 17 code lines plus 1 comment line, so $$C = 1 / 17 = 5.88\%$$. (Dividing by all 18 non-blank lines, SLOC plus the comment, would give 71.38, not radon's 71.74.) `radon raw shop.py` shows where the line counts come from:

```text
    LOC: 21
    LLOC: 16
    SLOC: 17
    Comments: 1
    Multi: 0
    Blank: 3
```

Then the three main terms are

$$
5.2 \ln 144.9 = 25.88, \qquad 0.23 \cdot 7 = 1.61, \qquad 16.2 \ln 16 = 44.92,
$$

the comment term is

$$
C_{\text{rad}} = 5.88 \cdot \frac{\pi}{180} = 0.1027, \quad \sqrt{2.46 \cdot 0.1027} = 0.5026, \quad 50 \sin 0.5026 = 50 \cdot 0.4817 = \mathbf{24.08},
$$

and the index is

$$
171 - 25.88 - 1.61 - 44.92 + 24.08 = 122.67, \qquad MI = 122.67 \cdot \frac{100}{171} = \mathbf{71.74}.
$$

`radon mi -s shop.py` prints `A (71.74)`. Without the comment line the same code scores 57.66: **one comment line adds 14 points**. A tool without the comment term would report the lower value.

## When to use it — and when not

- **Use** MI to spot files that dropped a band between releases, with the same tool.
- **Use** the three inputs, not the index, to decide what to fix.
- **Do not** read MI as a verdict: it was fitted to a few 1990s C and Pascal systems.
- **Do not** compare MIs from tools with different line counts or complexity rules: with $$L = \text{LOC} = 13$$, `delivery_fee` would score 60.5, and with McCabe's graph count $$G = 5$$, 62.2.

## How solid is this?

- **Where it comes from.** Oman and Hagemeister 1992; the rescaling from Microsoft; radon's version from its documentation and source. Every number was produced by radon 6.0.1 and recomputed by hand.
- **What is contested.** The comment term rewards comment lines whatever they say, so it is easy to game. MI also inherits the size effect of its inputs; [Measuring Maintainability](../../attributes/maintainability/measurement.md) compares it with benchmark-based models.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
