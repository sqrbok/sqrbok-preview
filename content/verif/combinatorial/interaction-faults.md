---
title: Interaction Faults
parent: Combinatorial Testing
nav_order: 1
layout: default
vegalite: true
---

# Interaction Faults

An **interaction fault** is a fault that causes a failure only when several inputs take particular values together; the number of inputs involved is the failure's **failure-triggering fault interaction (FTFI) number** {% cite kuhn2004fault %}. A failure with FTFI number 2 needs a particular pair of values, and is found by any test suite that contains that pair.

## 1. One, two and three interacting conditions

For example, the lecture's constructed bank code has three branches:

```python
if balance <= 0:                                        # FTFI 1: one input decides
    ...
if balance <= 0 and overdraft:                          # FTFI 2: a pair of values
    ...
if balance <= 0 and overdraft and check_amount >= 100:  # FTFI 3: a triple
    ...
```

A fault in the first branch shows up whenever `balance` is zero or less, whatever the other inputs are. A fault in the third branch needs all three conditions in the same test. Testing each value on its own will almost certainly reach the first branch, and may never reach the third.

## 2. Real failures need few interacting inputs

Kuhn, Wallace and Gallo counted the FTFI number of real failures in four systems {% cite kuhn2004fault %}:

- **NASA:** 329 error reports from development and integration testing of a large distributed data-management system at NASA Goddard.
- **Medical devices:** 109 FDA recalls of software-controlled devices; only these 109 of 342 recalls held enough detail {% cite wallace2001medical %}.
- **Browser and server:** bug reports of early releases of the Mozilla browser and the Apache server.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container", "height": 260,
  "data": {"values": [
    {"s": "NASA system", "n": 1, "p": 68}, {"s": "NASA system", "n": 2, "p": 93}, {"s": "NASA system", "n": 3, "p": 98}, {"s": "NASA system", "n": 4, "p": 100},
    {"s": "Medical devices", "n": 1, "p": 66}, {"s": "Medical devices", "n": 2, "p": 97}, {"s": "Medical devices", "n": 3, "p": 99}, {"s": "Medical devices", "n": 4, "p": 100},
    {"s": "Browser (Mozilla)", "n": 1, "p": 29}, {"s": "Browser (Mozilla)", "n": 2, "p": 76}, {"s": "Browser (Mozilla)", "n": 3, "p": 95}, {"s": "Browser (Mozilla)", "n": 4, "p": 97}, {"s": "Browser (Mozilla)", "n": 5, "p": 99}, {"s": "Browser (Mozilla)", "n": 6, "p": 100},
    {"s": "Server (Apache)", "n": 1, "p": 42}, {"s": "Server (Apache)", "n": 2, "p": 70}, {"s": "Server (Apache)", "n": 3, "p": 89}, {"s": "Server (Apache)", "n": 4, "p": 96}, {"s": "Server (Apache)", "n": 5, "p": 96}, {"s": "Server (Apache)", "n": 6, "p": 100}
  ]},
  "mark": {"type": "line", "point": {"size": 90, "filled": true}, "strokeWidth": 2.5},
  "encoding": {
    "x": {"field": "n", "type": "ordinal", "title": "Number of interacting conditions (FTFI number)", "axis": {"labelAngle": 0}},
    "y": {"field": "p", "type": "quantitative", "title": "Cumulative % of failures", "scale": {"domain": [0, 100]}},
    "color": {"field": "s", "type": "nominal", "title": null,
      "scale": {"domain": ["Medical devices", "NASA system", "Browser (Mozilla)", "Server (Apache)"], "range": ["#019546", "#c99700", "#1f5fa8", "#b42318"]},
      "legend": {"orient": "bottom", "columns": 2, "labelFontSize": 13, "symbolSize": 120}},
    "shape": {"field": "s", "type": "nominal",
      "scale": {"domain": ["Medical devices", "NASA system", "Browser (Mozilla)", "Server (Apache)"], "range": ["circle", "square", "triangle-up", "diamond"]}}
  },
  "config": {"font": "Tahoma", "axis": {"labelFontSize": 13, "titleFontSize": 13}, "view": {"stroke": null}}
}
```

_Cumulative share of failures triggered by at most n conditions, from Table 1 of the source. Read the browser line at 2: 76% of its failures needed two conditions or fewer._ {% cite kuhn2004fault %}

Three things stand out. Between 70% and 97% of failures needed at most two conditions. No failure in any of the four systems needed more than six. And the fielded medical devices were not harder to trigger than the system still in development: 97% of their failures needed at most two conditions.

From this the authors draw the key claim of the field: if every fault in a system is triggered by at most *n* conditions, testing all *n*-way combinations is **effectively exhaustive**. Since real inputs have far more values than a test can try, this holds only for the classes chosen for each input, so they call it **pseudo-exhaustive** {% cite kuhn2004fault %}.

## When to use it — and when not

- **Use** the FTFI data to justify a strength: pairwise as the default, 3-way or more where the remaining failures matter.
- **Do not** carry the numbers over to software that depends on sequences of events, such as real-time control: the authors note that combinations of values may not catch failures that need a particular order {% cite kuhn2004fault %}.

## Limitations

The FTFI number describes the conditions written in a bug report, not the code. Some reports were unclear about which conditions mattered, so the authors assumed the worst, which pushes some numbers up. And a combination of classes can be covered while the one value that triggers the failure is never tried.

## How solid is this?

- **Where it comes from.** One NASA system, FDA recall data, and two open-source projects; NASA's Remote Agent study found that pairwise testing caught over 80% of its correctness and convergence bugs, but only about half of its engine and interface bugs {% cite kuhn2004fault %}.
- **What is contested.** Four systems are a small sample, and the authors themselves say many more studies are needed before calling six a general limit.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
