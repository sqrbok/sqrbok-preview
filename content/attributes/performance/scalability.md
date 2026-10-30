---
title: Scalability Laws
parent: Performance
nav_order: 2
layout: default
vegalite: true
---

# Scalability Laws

A **scalability law** predicts how much faster, or how much more work, a system delivers when it gets *N* processors or servers instead of one. Three laws form a progression: Amdahl's fixed-size ceiling, Gustafson's scaled speedup, and Gunther's Universal Scalability Law, which also predicts decline.

## 1. Amdahl: the unimproved part sets the ceiling

If a fraction *p* of the running time is sped up by a factor *q*, the overall speedup is

**S = 1 / ((1 − p) + p / q)**

When several parts *i* with shares *p*<sub>i</sub> are each sped up by *q*<sub>i</sub>, the unimproved share stays and each improved share shrinks: *S* = 1 / ((1 − Σ*p*<sub>i</sub>) + Σ *p*<sub>i</sub>/*q*<sub>i</sub>).

For example, a new algorithm runs 1.35 times as fast (*q* = 1.35) and covers 60% of the running time (*p* = 0.6). Then S = 1 / (0.4 + 0.6/1.35) = 1 / 0.844 ≈ **1.18**: an 18% gain overall from a 35% gain locally. With *N* processors and a **serial fraction** *f* (the share of single-processor time that cannot run in parallel; here *p* = 1 − *f* and *q* = *N*), the same reasoning gives S(*N*) = 1 / (*f* + (1 − *f*)/*N*), which can never exceed **1/*f***: 10% serial work caps the speedup at 10, however many processors are added.

Amdahl made the argument in words, not with this formula: about 40% of executed instructions were sequential data-management overhead, perhaps halved in a dedicated machine, which would limit throughput to "five to seven times" the sequential rate {% cite amdahl1967validity %}: an overhead halved to 20% caps speedup at 1/0.2 = 5, and one cut to about 14% at about 7.

## 2. Gustafson: users grow the problem, not the speed

Amdahl assumes a fixed problem. Gustafson observed that with more processors, users run bigger problems in the same time, so the parallel part grows with *N* while the serial part stays fixed. The **scaled speedup** is

**S = N − (N − 1) · s**

where *s* is the serial share of the time measured on the parallel machine. It differs from Amdahl's *f*, which is measured on one processor. On a 1,024-processor machine at Sandia, three applications with *s* between 0.4% and 0.8% reached measured scaled speedups of 1,016 to 1,021 {% cite gustafson1988reevaluating %}; the formula gives 1,016 to 1,020 over that range, close to the measurements.

## 3. The Universal Scalability Law: why adding servers can hurt

Gunther's **Universal Scalability Law** (USL) gives the relative capacity of *N* nodes, meaning throughput relative to one node (not ISO's capacity subcharacteristic), as

**C(N) = N / (1 + α(N − 1) + β N(N − 1))**

where α is **contention** (waiting for a shared resource such as a database lock) and β is **coherence** (the cost of keeping nodes consistent, such as cache invalidation, which grows with every pair of nodes) {% cite gunther2002guerrilla gunther2007guerrilla %}. With β = 0 the USL is Amdahl's law and α equals the serial fraction *f*: dividing numerator and denominator by *N* turns *N* / (1 + α(*N* − 1)) into 1 / (α + (1 − α)/*N*); with β > 0 capacity peaks at *N*\* = √((1 − α)/β) and then **falls**: retrograde throughput.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 420, "height": 230,
  "data": {"values": [
    {"n":1,"law":"Amdahl (f = 0.03)","C":1.00},{"n":1,"law":"USL (α = 0.03, β = 0.0005)","C":1.00},
    {"n":2,"law":"Amdahl (f = 0.03)","C":1.94},{"n":2,"law":"USL (α = 0.03, β = 0.0005)","C":1.94},
    {"n":4,"law":"Amdahl (f = 0.03)","C":3.67},{"n":4,"law":"USL (α = 0.03, β = 0.0005)","C":3.65},
    {"n":8,"law":"Amdahl (f = 0.03)","C":6.61},{"n":8,"law":"USL (α = 0.03, β = 0.0005)","C":6.46},
    {"n":16,"law":"Amdahl (f = 0.03)","C":11.03},{"n":16,"law":"USL (α = 0.03, β = 0.0005)","C":10.19},
    {"n":24,"law":"Amdahl (f = 0.03)","C":14.20},{"n":24,"law":"USL (α = 0.03, β = 0.0005)","C":12.21},
    {"n":32,"law":"Amdahl (f = 0.03)","C":16.58},{"n":32,"law":"USL (α = 0.03, β = 0.0005)","C":13.19},
    {"n":44,"law":"Amdahl (f = 0.03)","C":19.21},{"n":44,"law":"USL (α = 0.03, β = 0.0005)","C":13.60},
    {"n":56,"law":"Amdahl (f = 0.03)","C":21.13},{"n":56,"law":"USL (α = 0.03, β = 0.0005)","C":13.37},
    {"n":64,"law":"Amdahl (f = 0.03)","C":22.15},{"n":64,"law":"USL (α = 0.03, β = 0.0005)","C":13.05},
    {"n":80,"law":"Amdahl (f = 0.03)","C":23.74},{"n":80,"law":"USL (α = 0.03, β = 0.0005)","C":12.25},
    {"n":96,"law":"Amdahl (f = 0.03)","C":24.94},{"n":96,"law":"USL (α = 0.03, β = 0.0005)","C":11.41}]},
  "mark": {"type": "line", "point": {"filled": true, "size": 60}, "strokeWidth": 3},
  "encoding": {
    "x": {"field": "n", "type": "quantitative", "title": "nodes N", "scale": {"domain": [0, 100]}},
    "y": {"field": "C", "type": "quantitative", "title": "relative capacity C(N)", "scale": {"domain": [0, 26]}},
    "color": {"field": "law", "type": "nominal", "title": null, "scale": {"domain": ["Amdahl (f = 0.03)", "USL (α = 0.03, β = 0.0005)"], "range": ["#019546", "#1f5fa8"]}, "legend": {"orient": "top"}},
    "shape": {"field": "law", "type": "nominal", "title": null, "scale": {"domain": ["Amdahl (f = 0.03)", "USL (α = 0.03, β = 0.0005)"], "range": ["circle", "square"]}}},
  "config": {"font": "Tahoma, sans-serif", "view": {"stroke": null}}
}
```

_With 3% contention alone (Amdahl with f = 0.03) the curve keeps rising towards 1/0.03 ≈ 33. Adding a small coherence cost (β = 0.0005) makes capacity peak near 44 nodes at 13.6 times one node, and 96 nodes deliver less than 32 do. Parameters chosen for illustration._

α and β are fitted by regression to measured *C*(*N*) = *X*(*N*) / *X*(1); Gunther reports that four load points usually suffice {% cite gunther2002guerrilla %}.

## 4. Which law to use

The three laws answer different questions.

| Law | Assumes | Predicts | Use for |
|---|---|---|---|
| Amdahl | fixed problem size | a ceiling of 1/*f* | deciding whether optimizing one part is worth it |
| Gustafson | problem grows with *N* | near-linear scaled speedup | batch and scientific workloads that grow |
| USL | contention and coherence | a peak, then decline | clusters and distributed services |

## When to use it — and when not

- Use Amdahl before optimizing: measure the part's share of the time first.
- Use the USL to decide whether more nodes will help.
- Do not extrapolate a fitted USL far beyond the measured range.

## Limitations

The laws describe averages of a stable workload, not percentiles, and fitted α and β change with the code, data or hardware.

## How solid is this?

- **Where it comes from.** Amdahl, Gustafson and Gunther, cited above.
- **What differs between sources.** Gunther's 2002 article nests β inside α, N/(1 + α[(N − 1) + βN(N − 1)]); the form above, his later notation, gives the same curves with re-scaled β.
- **What is assumed.** The USL is empirical: α and β are fitted, not measured.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
