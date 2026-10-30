---
title: Reliability Growth Models
parent: Reliability
grand_parent: Quality Attributes
nav_order: 4
layout: default
vegalite: true
---

# Reliability Growth Models

A **software reliability growth model** (SRGM) describes how failure intensity falls during testing as failures are observed and their faults removed; fitted to failure data, it predicts when the failure intensity will reach the [failure intensity objective](fio.md) {% cite goel1979time vouk1997sre %}.

## 1. The Goel–Okumoto model

Goel and Okumoto assume that the expected number of failures in a short interval is proportional to the number of faults not yet found {% cite goel1979time %}. That gives an exponential curve for the expected cumulative number of failures m(t) by test time t:

m(t) = a (1 − e<sup>−bt</sup>) and λ(t) = ab e<sup>−bt</sup>

The model counts one fault removed per failure. Here a is the expected number of faults that testing will eventually find, and b is the detection rate per fault (per week of testing, say). The failure intensity λ(t) starts at ab and decays; the expected number of faults still to find is a − m(t) = a e<sup>−bt</sup> = λ(t)/b. Musa's *basic execution time* model has the same shape, measured in execution time rather than calendar time {% cite vouk1997sre %}.

## 2. Worked example: when do we stop?

A team estimates a = 50 faults and b = 0.05 per week; the FIO is 0.5 failures per week.

1. Initial intensity: λ(0) = 50 × 0.05 = 2.5 failures per week.
2. Solve FIO = λ(0) e<sup>−bt</sup> for t. First, e<sup>−bt</sup> = FIO / λ(0) = 0.5 / 2.5 = 0.2. Then t = ln(λ(0) / FIO) / b = ln 5 / 0.05 ≈ 32.2 weeks.
3. Faults found by then: m = 50 × (1 − 0.2) = 40; 10 remain (0.5 / 0.05).

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 460, "height": 220,
  "data": {"values": [
    {"t":0,"l":2.5},{"t":5,"l":1.947},{"t":10,"l":1.516},{"t":15,"l":1.181},{"t":20,"l":0.92},
    {"t":25,"l":0.716},{"t":30,"l":0.558},{"t":32.2,"l":0.5},{"t":35,"l":0.434},{"t":40,"l":0.338},
    {"t":50,"l":0.205},{"t":60,"l":0.124},{"t":70,"l":0.075},{"t":80,"l":0.046}]},
  "layer": [
    {"mark": {"type": "line", "strokeWidth": 3, "color": "#b42318"},
     "encoding": {"x": {"field": "t", "type": "quantitative", "title": "Test time (weeks)", "scale": {"domain": [0, 80]}},
                  "y": {"field": "l", "type": "quantitative", "title": "Failure intensity λ(t), failures per week", "scale": {"domain": [0, 2.6]}}}},
    {"data": {"values": [{"y": 0.5}]}, "mark": {"type": "rule", "strokeDash": [6, 3], "strokeWidth": 2, "color": "#019546"},
     "encoding": {"y": {"field": "y", "type": "quantitative"}}},
    {"data": {"values": [{"x": 32.2}]}, "mark": {"type": "rule", "strokeDash": [3, 3], "color": "#6b7280"},
     "encoding": {"x": {"field": "x", "type": "quantitative"}}},
    {"data": {"values": [{"x": 34, "y": 1.2, "t": "release at 32.2 weeks"}, {"x": 60, "y": 0.62, "t": "FIO = 0.5"}]},
     "mark": {"type": "text", "align": "left", "fontSize": 13, "color": "#282828"},
     "encoding": {"x": {"field": "x", "type": "quantitative"}, "y": {"field": "y", "type": "quantitative"}, "text": {"field": "t"}}}
  ],
  "config": {"font": "Tahoma, sans-serif", "axis": {"labelFontSize": 12, "titleFontSize": 13}, "view": {"stroke": null}}
}
```

_λ(t) = 2.5 e<sup>−0.05t</sup> for the worked example; testing stops where the curve crosses the FIO._

## 3. Families of models

Vouk groups the many published models into three families {% cite vouk1997sre %}:

| Family | Expected failures over time | Examples |
|---|---|---|
| Finite failures | level off at a finite total | Goel–Okumoto; Musa basic execution time |
| Infinite failures | keep growing, ever more slowly | Musa–Okumoto logarithmic Poisson |
| S-shaped | rise slowly, then fast, then level off | Yamada delayed S-shaped |

You need to recognise the logarithmic Poisson and S-shaped families, not compute them. No model fits every project. Musa recommends fitting both the basic and the logarithmic Poisson model, since the two tend to bracket the data {% cite musa2004sre %}, and checking first, with a trend test, that the failure data show growth at all {% cite vouk1997sre %}.

## 4. Certifying a finished release

When the code is frozen and a FIO is given, Musa's **demonstration chart** gives a go or no-go decision without fitting a model {% cite musa2004sre %}. The horizontal axis is normalized time, test time × FIO; the vertical axis is the failure number, the count of failures so far (1, 2, 3, …). Each failure moves the path one step up; failure-free test time moves it right. Testing stops the first time the path enters a region: Accept means release, Reject means stop and rework; in between, keep testing. Reading the figure: by normalized time 2.0 the path shows one failure, which lies between the lines, so testing continues; at about 4.1, with two failures, the path first touches the Accept line, and testing stops. The chart is for reading, not constructing: its boundaries are illustrative; real ones follow from the accepted risks of a wrong decision (formulas in Musa's book).

![Accept, continue and reject regions of a demonstration chart, with a failure history moving into the accept region](demonstration-chart.svg)

_Schematic demonstration chart with illustrative boundaries; failures occur at normalized times of about 0.8 and 2.7._ {% cite musa2004sre %}

## When to use it — and when not

Use an SRGM in system testing when code changes only to fix faults found and tests follow the operational profile. Do not use it when features keep arriving during testing: every change adds new faults and breaks the decaying curve.

## Limitations

The models assume that each fault is fixed at once and perfectly, that testing follows real usage, and that the code is otherwise stable. Continuous delivery breaks the last assumption, which is why web services replaced release-time growth models with error budgets in production ([From FIO to SLO](slo-bridge.md)). Predictions far beyond the observed data are unreliable.

## How solid is this?

- **Where it comes from.** The Goel–Okumoto model {% cite goel1979time %}, Musa's models and certification chart {% cite musa2004sre %}, and Vouk's overview {% cite vouk1997sre %}.
- **What the evidence says.** In one industrial case reported by Vouk, reliability engineering cut customer-reported problems and maintenance cost by a factor of 10, halved software problems in system test, and the product had no serious service outage in its first two years; other studies put the cost-benefit ratio at six or more {% cite vouk1997sre %}. These are practitioner reports, not controlled studies.
- **What is contested.** Which model to use; model choice remains case by case.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
