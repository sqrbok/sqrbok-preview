---
title: Covering Arrays
parent: Combinatorial Testing
nav_order: 2
layout: default
vegalite: true
---

# Covering Arrays

A **covering array** of strength *t* is a table of tests, one row per test and one column per parameter, in which every combination of values of any *t* columns appears in at least one row {% cite cohen1997aetg grindal2005survey %}. A test suite built from it achieves **t-way coverage**; *t* = 2 is called **pairwise**.

## 1. Orthogonal arrays balance; covering arrays only cover

Covering arrays come from **orthogonal arrays**, used since the 1920s in agricultural experiments, where every combination of any *t* columns appears *exactly* the same number of times. This array of three parameters with values 0, 1, 2 contains each of the 9 pairs of every two columns exactly once:

| Test | P1 | P2 | P3 |
|---|---|---|---|
| 1 | 0 | 0 | 0 |
| 2 | 0 | 1 | 1 |
| 3 | 0 | 2 | 2 |
| 4 | 1 | 0 | 1 |
| 5 | 1 | 1 | 2 |
| 6 | 1 | 2 | 0 |
| 7 | 2 | 0 | 2 |
| 8 | 2 | 1 | 0 |
| 9 | 2 | 2 | 1 |

An experiment needs that balance because its measurements are noisy and have to be averaged. A software test does not: it gives the same pass or fail every time, so one occurrence of a combination is enough. Dropping the balance requirement is what lets covering arrays exist for any number of parameters and values, and be smaller {% cite cohen1997aetg %}.

## 2. Counting what must be covered

For *k* parameters with *v* values each, a strength-*t* array must cover C(*k*, *t*) · *v*<sup>*t*</sup> combinations: every choice of *t* columns, times every value combination in them. For example, for 40 parameters with 3 values each and *t* = 2, that is 780 · 9 = 7,020 pairs. When value counts differ, add the products over every pair of parameters, *v*<sub>*i*</sub> · *v*<sub>*j*</sub>: the Find dialog of [Combinatorial Input Models](practice.md), with 8, 2, 2 and 3 values, has 8·2 + 8·2 + 8·3 + 2·2 + 2·3 + 2·3 = 72 pairs.

Two quick checks follow from the definition:

- **Lower bound.** An array needs at least as many rows as the product of the *t* largest value counts, since those combinations all differ. Pairwise for inputs with 8, 3, 2 and 2 values needs at least 8 · 3 = 24 tests.
- **Subsumption.** A 3-way array is also a 2-way array: every pair sits inside some triple.

## 3. Test count grows with the logarithm of the number of parameters

Cohen and colleagues proved that a greedy generator, which always picks the test covering the most uncovered pairs, covers at least 1/*v*<sup>2</sup> of the remaining pairs with each test. After *r* tests at most *N* · (1 − 1/*v*<sup>2</sup>)<sup>*r*</sup> of the *N* pairs remain uncovered, so about *v*<sup>2</sup> · ln *N* tests are enough; *N* grows only with *k*<sup>2</sup>, so the number of tests grows with log *k*, not with *v*<sup>*k*</sup> {% cite cohen1997aetg %}. Their rule of thumb for three-valued parameters is about 4 · log<sub>2</sub> *k* pairwise tests: 15 for 13 parameters, and 21 for 40, where exhaustive testing needs 3<sup>40</sup> ≈ 1.2 × 10<sup>19</sup> tests.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container", "height": 220,
  "data": {"values": [
    {"k": 5, "n": 13, "s": "PICT 3.7.4 (measured)"}, {"k": 10, "n": 18, "s": "PICT 3.7.4 (measured)"}, {"k": 20, "n": 22, "s": "PICT 3.7.4 (measured)"},
    {"k": 40, "n": 27, "s": "PICT 3.7.4 (measured)"}, {"k": 80, "n": 31, "s": "PICT 3.7.4 (measured)"}, {"k": 100, "n": 32, "s": "PICT 3.7.4 (measured)"},
    {"k": 13, "n": 15, "s": "AETG (Cohen et al.)"}, {"k": 40, "n": 21, "s": "AETG (Cohen et al.)"}
  ]},
  "mark": {"type": "line", "point": {"size": 80, "filled": true}, "strokeWidth": 3},
  "encoding": {
    "x": {"field": "k", "type": "quantitative", "title": "Number of parameters k (3 values each)"},
    "y": {"field": "n", "type": "quantitative", "title": "Pairwise tests", "scale": {"domain": [0, 40]}},
    "color": {"field": "s", "type": "nominal", "title": null, "scale": {"domain": ["PICT 3.7.4 (measured)", "AETG (Cohen et al.)"], "range": ["#019546", "#c99700"]}, "legend": {"orient": "bottom", "labelFontSize": 13}},
    "shape": {"field": "s", "type": "nominal", "title": null, "scale": {"domain": ["PICT 3.7.4 (measured)", "AETG (Cohen et al.)"], "range": ["circle", "square"]}}
  },
  "config": {"font": "Tahoma", "axis": {"labelFontSize": 13, "titleFontSize": 13}, "view": {"stroke": null}}
}
```

_Pairwise tests for k three-valued parameters. Twenty times more parameters need fewer than three times as many tests. PICT runs on this page's models; AETG figures as published._ {% cite microsoft_pict cohen1997aetg %}

Raising *t* is the expensive direction, because the count grows with *v*<sup>*t*</sup>. Lei and colleagues' IPOG tool needed, for 10 parameters with 5 values each {% cite lei2007ipog %}:

| Strength *t* | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|
| Tests | 48 | 308 | 1,843 | 10,119 | 50,920 |
| Lower bound 5<sup>*t*</sup> | 25 | 125 | 625 | 3,125 | 15,625 |

Exhaustive testing of the same model needs 5<sup>10</sup> = 9,765,625 tests.

## When to use it — and when not

- **Use** covering arrays when several inputs or settings each take a few classes of values and could interact.
- **Do not** expect them to help with long event sequences or continuous values that cannot be split into classes.

## Limitations

A covering array guarantees which combinations appear, not that any of them fails. Different generators give different sizes for the same model: PICT needs 27 tests for 40 three-valued parameters, AETG reported 21.

## How solid is this?

- **Where it comes from.** Cohen et al.'s logarithmic-growth proof, Lei et al.'s IPOG measurements, and runs of PICT 3.7.4 on this page's models {% cite cohen1997aetg lei2007ipog microsoft_pict %}.
- **What we do not hold.** Tables of the smallest known covering arrays; a generator's result is an upper bound, not the minimum.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
