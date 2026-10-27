---
title: Combining Techniques
parent: Input Domain Testing
nav_order: 7
layout: default
vegalite: true
---

# Combining Input Domain Techniques

**Combining input domain techniques** means using them in sequence, classes, then boundaries, then rules, and then choosing how many combinations of the resulting values to test. The second decision, the **combination strategy**, decides most of the cost, and the evidence on fault interactions decides how far it is worth going.

## 1. The sequence

1. **Partition** each input into [equivalence classes](equivalence.md), valid and invalid.
2. **Add boundaries**: [ON and OFF points](boundary.md) for every class edge.
3. **Tabulate rules** where conditions combine, with a [decision table](decision-tables.md).
4. **Combine** the values of different inputs with a combination strategy, or model the whole input space as a [classification tree](classification-tree.md) first.

## 2. How many combinations?

The strategies form a scale {% cite ostrand1988category grindal2005survey %}. **Example.** A web page is tested on 4 browsers and 4 operating systems, at 3 screen sizes, in 3 languages and 2 colour themes:

| Strategy | Requirement | Tests for 5 inputs with 4, 4, 3, 3, 2 values |
|---|---|---|
| Each choice | every value of every input appears at least once | at least 4 |
| Pairwise (2-way) | every pair of values from two inputs appears together | at least 4 × 4 = 16 |
| *t*-way | every combination of *t* inputs' values appears | grows with *t* |
| All combinations | every combination | 4 × 4 × 3 × 3 × 2 = 288 |

The lower bounds follow from the two largest inputs: each choice needs at least as many tests as the largest input has values, pairwise at least the product of the two largest. Generators such as NIST ACTS or Microsoft PICT come close to those bounds; [Combinatorial Testing](../combinatorial/index.md), a separate section of this handbook under Verification and Validation, explains how.

## 3. Why pairwise is often enough

Kuhn, Wallace and Gallo traced real failures back to the number of input conditions needed to trigger them {% cite kuhn2004fault %}.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container", "height": 180,
  "data": {"values": [
    {"system": "Medical device software (recalls)", "pct": 97},
    {"system": "NASA distributed system (development)", "pct": 93},
    {"system": "Mozilla browser (bug reports)", "pct": 76},
    {"system": "Apache server (bug reports)", "pct": 70}
  ]},
  "encoding": {
    "y": {"field": "system", "type": "nominal", "sort": "-x", "title": null, "axis": {"labelLimit": 300}},
    "x": {"field": "pct", "type": "quantitative", "title": "% of failures triggered by one or two interacting inputs", "scale": {"domain": [0, 100]}}
  },
  "layer": [
    {"mark": {"type": "bar", "color": "#019546", "cornerRadiusEnd": 3}},
    {"mark": {"type": "text", "align": "left", "dx": 4, "fontSize": 13}, "encoding": {"text": {"field": "pct", "type": "quantitative", "format": "d"}}}
  ],
  "config": {"font": "Tahoma", "axis": {"labelFontSize": 13, "titleFontSize": 13}}
}
```

_% of failures triggered by one or two interacting inputs, by system: the failures that pairwise testing of the right values would have triggered._ {% cite kuhn2004fault wallace2001medical %}

No failure in any of the systems needed more than six conditions, which led the authors to argue that testing all 4- to 6-way combinations could be close to exhaustive for such software {% cite kuhn2004fault %}.

## When to use which strategy — and when not

- **Use each choice** as a smoke test: cheap, but it never tests two values together.
- **Use pairwise** as the default for configuration and form inputs: in the studied systems it reached 70% to 97% of failures.
- **Use 3-way or more** for safety-critical code, where the remaining failures matter.
- **Do not combine invalid values freely**: an invalid value usually stops processing, so it should be tested on its own, not paired with every valid value.

## Limitations

A combination strategy only combines the values it is given: if the classes or boundaries are wrong, every combination inherits the error. Pairwise also says nothing about the expected result of each test, which still has to be worked out.

## How solid is this?

- **Where it comes from.** Kuhn et al. analysed 329 error reports from a NASA system and compared them with medical device recalls and Mozilla and Apache bug reports {% cite kuhn2004fault %}; the medical data come from Wallace and Kuhn {% cite wallace2001medical %}.
- **What is contested.** Four data sets, all from the early 2000s; the percentages are shares of *reported* failures. They support pairwise as a baseline, not as a guarantee for other kinds of software.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
