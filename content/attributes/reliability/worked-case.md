---
title: "Worked Case: When to Stop Testing a Module"
parent: Reliability
grand_parent: Quality Attributes
nav_order: 8
layout: default
page_type: worked-case
math: katex
vegalite: true
---

# Worked Case: When to Stop Testing a Module

This worked case takes a real failure log, checks that it shows reliability growth, fits two growth models to it, and decides whether testing can stop against a failure intensity objective (FIO). The theory is on [Reliability Growth Models](growth-models.md), [Reliability Measurement](measurement.md) and [Severity Classes and the FIO](fio.md).

**Example: the NTDS A-module.** The A-module of the U.S. Navy's Naval Tactical Data System (NTDS), a real-time multicomputer system, logged 26 software failures in its 250-day production (checkout) phase. Goel and Okumoto used these failure times, first published by Jelinski and Moranda, to introduce their model {% cite goel1979time %}. The question at day 250: is the module reliable enough to leave the phase, or does testing continue? The failure times are real; the FIO used in step 5 is constructed. _(Every command was run for this page with Python 3.12.9, NumPy 2.5.4 and SciPy 1.18.1 on a laptop.)_

## 1. Build the failure log

A growth model needs one line per failure with the time it **occurred**, in one time unit, from the start of the phase. On a real project the times come from the test log or the bug tracker: take the moment the tester saw the failure, not the date the report was filed or the fault was fixed, and drop duplicate reports of the same failure. The NTDS log is in calendar days, cumulated from the paper's Table 1 {% cite goel1979time %}:

```text
failure,day
1,9
2,21
3,32
4,36
5,43
6,45
7,50
8,58
9,63
10,70
11,71
12,77
13,78
14,87
15,91
16,92
17,95
18,98
19,104
20,105
21,116
22,149
23,156
24,247
25,249
26,250
```

**Decision point: which failures count?** Criterion: fit one model per [severity class](fio.md) whose failures share an FIO, and count failures, not faults. The NTDS reports are not classified, so all 26 are treated as one class here. On your project, define the classes before testing starts, with the five steps on [Severity Classes and the FIO](fio.md).

{: .note }
> **Side note: finding the failure impacts.** The first of those steps, listing the potential failure impacts, is where a short failure mode and effects analysis (FMEA) or fault tree helps. An FMEA works bottom-up, one line per component failure mode, for example (constructed, a payment API): *currency service returns a stale rate → wrong amount charged → class 2*. A fault tree works top-down from one undesired event split by AND/OR gates: *customer charged twice = retry after timeout AND no idempotency key*.

The whole analysis is one script, `fit.py`, run as `python fit.py ntds.csv 0.02` (the log, then the FIO from step 5). Each step below shows its part of the output.

<details markdown="1">
<summary markdown="span">The complete `fit.py` (Python 3.12.9, NumPy 2.5.4, SciPy 1.18.1)</summary>

```python
# Usage: python fit.py <failure-log.csv> <FIO per time unit>
# Fits reliability growth models to failure times (column 2 of the CSV).
import sys
import numpy as np
from scipy.optimize import brentq, minimize

t = np.loadtxt(sys.argv[1], delimiter=",", skiprows=1, usecols=1)
FIO = float(sys.argv[2])
n, T = len(t), t[-1]                       # observation ends at the last failure

# 1. Laplace trend test, failure-truncated: the window ends at failure i
def laplace(i):
    s = t[:i]
    return (s[:-1].mean() - s[-1] / 2) / (s[-1] * np.sqrt(1 / (12 * (i - 1))))
print("Laplace u after failure i:",
      ", ".join(f"{i}: {laplace(i):+.2f}" for i in (10, 15, 20, 21, 22, 23, 24, 26)))

# 2. Goel-Okumoto maximum likelihood: one equation in b, then a = n / (1 - exp(-bT))
def go_fit(s, T):
    m = len(s)
    g = lambda b: m / b - s.sum() - m * T * np.exp(-b * T) / (1 - np.exp(-b * T))
    b = brentq(g, 1e-7, 1.0)               # ValueError: no root, the data show no growth
    return m / (1 - np.exp(-b * T)), b
a, b = go_fit(t, T)
lam = a * b * np.exp(-b * T)
ll_go = n * np.log(a * b) - b * t.sum() - a * (1 - np.exp(-b * T))   # log-likelihood
print(f"Goel-Okumoto: a = {a:.2f}, b = {b:.5f}/day, remaining a - n = {a - n:.1f}, "
      f"lambda(T) = {lam:.4f}/day, logL = {ll_go:.2f}")

# 3. Musa-Okumoto logarithmic Poisson: m(t) = ln(1 + l0*th*t)/th, lambda(t) = l0/(1 + l0*th*t)
#    log-likelihood of a Poisson process: sum of ln lambda(t_i) minus m(T)
def nll(p):
    l0, th = np.exp(p)                     # fit on the log scale to keep both positive
    return -(np.sum(np.log(l0 / (1 + l0 * th * t))) - np.log(1 + l0 * th * T) / th)
r = minimize(nll, np.log([0.2, 0.05]), method="Nelder-Mead",
             options={"xatol": 1e-10, "fatol": 1e-10})
l0, th = np.exp(r.x)
print(f"log-Poisson:  lambda0 = {l0:.4f}/day, theta = {th:.4f}/failure, "
      f"lambda(T) = {l0 / (1 + l0 * th * T):.4f}/day, logL = {-r.fun:.2f}")

# 4. When does each model reach the FIO?  Solve lambda(t) = FIO in closed form.
t_go = np.log(a * b / FIO) / b                     # G-O: t = ln(ab / FIO) / b
t_lp = (l0 / FIO - 1) / (l0 * th)                  # log-Poisson: t = (l0/FIO - 1) / (l0*th)
print(f"G-O reaches FIO {FIO}/day at day {t_go:.0f}: {t_go - T:.0f} more test days, "
      f"{a * (np.exp(-b * T) - np.exp(-b * t_go)):.1f} more failures expected")
print(f"log-Poisson reaches FIO at day {t_lp:.0f}: {t_lp - T:.0f} more test days, "
      f"{(np.log(1 + l0*th*t_lp) - np.log(1 + l0*th*T)) / th:.1f} more failures expected")

# 5. Parametric bootstrap of lambda(T): simulate logs from the fitted G-O model, refit each
rng = np.random.default_rng(1)                     # fixed seed: the same samples every run
lams, skipped = [], 0
for _ in range(2000):
    m = rng.poisson(a * (1 - np.exp(-b * T)))      # number of failures in [0, T]
    s = np.sort(-np.log(1 - rng.random(m) * (1 - np.exp(-b * T))) / b)   # their times
    try:
        aa, bb = go_fit(s, T)
        lams.append(aa * bb * np.exp(-bb * T))
    except (ValueError, ZeroDivisionError):        # a log without growth has no G-O fit
        skipped += 1
lo, hi = np.percentile(lams, [5, 95])
print(f"bootstrap 90% interval for lambda(T): {lo:.4f} to {hi:.4f}/day "
      f"({len(lams)} samples, {skipped} skipped)")

# 6. Curve values for a chart: observed counts against both fitted m(t)
print("m(t) G-O / log-Poisson:", ", ".join(
    f"{x}: {a * (1 - np.exp(-b * x)):.2f}/{np.log(1 + l0 * th * x) / th:.2f}"
    for x in (50, 100, 150, 200, 250, 300, 350, 400)))
```

</details>

## 2. Check for growth before fitting anything

A growth model assumes the failure intensity is falling. The **Laplace test** checks that assumption {% cite kanoun1994trend %}. With failure times *t*<sub>1</sub>, …, *t*<sub>*i*</sub> and the window ending at the last failure,

$$
u(i) = \frac{\frac{1}{i-1}\sum_{j=1}^{i-1} t_j \;-\; \frac{t_i}{2}}{t_i\,\sqrt{\frac{1}{12\,(i-1)}}}
$$

If failures arrive at a constant rate, they are spread evenly over the window, their mean sits near the middle *t*<sub>*i*</sub>/2, and *u* is near 0 (approximately standard normal). If they bunch early, the mean is left of the middle and *u* is negative. Computing *u* after each failure shows when growth appeared:

```text
$ python fit.py ntds.csv 0.02
Laplace u after failure i: 10: +0.69, 15: +1.05, 20: +1.68, 21: +1.10, 22: -0.63, 23: -0.60, 24: -3.20, 26: -2.45
```

**Decision point: fit a model?** Criterion: *u* < −1.96 means growth at the 5% significance level; *u* > +1.96 means reliability is getting worse; in between, there is no evidence either way. For the first 21 failures *u* was positive: no growth. The 33-day gap after failure 21 turned it slightly negative (−0.63, −0.60), and only the 91-day gap after failure 23 made it significant (−3.20). At day 250, *u* = −2.45: growth, so fitting is justified. Note how much this rests on two long gaps; step 7 returns to that.

## 3. Fit the Goel–Okumoto model

The [Goel–Okumoto model](growth-models.md) has the mean value function *m*(*t*) = *a*(1 − e<sup>−*bt*</sup>) and intensity λ(*t*) = *ab* e<sup>−*bt*</sup>. **Maximum likelihood** chooses the parameter values under which the observed failure times are most probable; the **log-likelihood** (logL) is the logarithm of that probability density, so a higher value means a better fit. For *n* failure times observed up to *T*, the estimates solve two equations {% cite goel1979time %}:

$$
\frac{n}{a} = 1 - e^{-bT}, \qquad \frac{n}{b} = \sum_{j=1}^{n} t_j + a\,T\,e^{-bT}
$$

Substituting the first into the second leaves one equation in *b*, which `go_fit` in the script solves with a root finder (`brentq`):

```text
Goel-Okumoto: a = 33.99, b = 0.00579/day, remaining a - n = 8.0, lambda(T) = 0.0463/day, logL = -82.69
```

These are the estimates the paper reports, *a* = 33.99 and *b* = 0.00579, and its 8.0 expected remaining failures {% cite goel1979time %}, so the script reproduces the published fit. The initial intensity is λ(0) = *ab* = 33.99 × 0.00579 ≈ 0.1968 failures per day, and the current one is λ(250) = 0.1968 × e<sup>−0.00579 × 250</sup> = 0.1968 × e<sup>−1.4475</sup> ≈ 0.046 failures per day, about one failure every 22 days.

## 4. Fit a second model as a cross-check

Musa recommends fitting both a finite-failures model and the Musa–Okumoto **logarithmic Poisson** model, because the two tend to bracket the truth {% cite musa2004sre %}. Its expected failures *m*(*t*) = ln(1 + λ<sub>0</sub>θ*t*)/θ never level off, and its intensity λ(*t*) = λ<sub>0</sub> / (1 + λ<sub>0</sub>θ*t*) falls more slowly. Here λ<sub>0</sub> is the initial intensity (failures per day) and θ the decay per failure (per failure, so that λ<sub>0</sub>θ*t* has no unit). The script fits both by maximising the Poisson-process log-likelihood, Σ ln λ(*t*<sub>*j*</sub>) − *m*(*T*), with `scipy.optimize.minimize`, started from λ<sub>0</sub> = 0.2 and θ = 0.05:

```text
log-Poisson:  lambda0 = 0.1907/day, theta = 0.0427/failure, lambda(T) = 0.0628/day, logL = -83.09
```

The two log-likelihoods differ by 0.4: the data are only e<sup>0.4</sup> ≈ 1.5 times as probable under Goel–Okumoto, far too little to choose between the models; both put the current intensity between 0.046 and 0.063 failures per day.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 460, "height": 240,
  "layer": [
    {"data": {"values": [
      {"t":0,"model":"Goel–Okumoto fit","m":0},{"t":50,"model":"Goel–Okumoto fit","m":8.54},{"t":100,"model":"Goel–Okumoto fit","m":14.94},
      {"t":150,"model":"Goel–Okumoto fit","m":19.73},{"t":200,"model":"Goel–Okumoto fit","m":23.32},{"t":250,"model":"Goel–Okumoto fit","m":26.00},
      {"t":300,"model":"Goel–Okumoto fit","m":28.01},{"t":350,"model":"Goel–Okumoto fit","m":29.51},{"t":400,"model":"Goel–Okumoto fit","m":30.64},
      {"t":0,"model":"log-Poisson fit","m":0},{"t":50,"model":"log-Poisson fit","m":8.0},{"t":100,"model":"log-Poisson fit","m":13.95},
      {"t":150,"model":"log-Poisson fit","m":18.69},{"t":200,"model":"log-Poisson fit","m":22.63},{"t":250,"model":"log-Poisson fit","m":26.00},
      {"t":300,"model":"log-Poisson fit","m":28.95},{"t":350,"model":"log-Poisson fit","m":31.56},{"t":400,"model":"log-Poisson fit","m":33.91}]},
     "mark": {"type": "line", "strokeWidth": 2.5},
     "encoding": {
       "x": {"field": "t", "type": "quantitative", "title": "test time (days)", "scale": {"domain": [0, 400]}},
       "y": {"field": "m", "type": "quantitative", "title": "cumulative failures", "scale": {"domain": [0, 36]}},
       "color": {"field": "model", "type": "nominal", "title": null, "scale": {"domain": ["Goel–Okumoto fit", "log-Poisson fit"], "range": ["#019546", "#1f5fa8"]}, "legend": {"orient": "top", "symbolStrokeWidth": 3}},
       "strokeDash": {"field": "model", "type": "nominal", "title": null, "scale": {"domain": ["Goel–Okumoto fit", "log-Poisson fit"], "range": [[1, 0], [6, 3]]}}}},
    {"data": {"values": [
      {"t":9,"n":1},{"t":21,"n":2},{"t":32,"n":3},{"t":36,"n":4},{"t":43,"n":5},{"t":45,"n":6},{"t":50,"n":7},{"t":58,"n":8},{"t":63,"n":9},
      {"t":70,"n":10},{"t":71,"n":11},{"t":77,"n":12},{"t":78,"n":13},{"t":87,"n":14},{"t":91,"n":15},{"t":92,"n":16},{"t":95,"n":17},
      {"t":98,"n":18},{"t":104,"n":19},{"t":105,"n":20},{"t":116,"n":21},{"t":149,"n":22},{"t":156,"n":23},{"t":247,"n":24},{"t":249,"n":25},{"t":250,"n":26}]},
     "mark": {"type": "point", "filled": true, "size": 40, "color": "#282828"},
     "encoding": {"x": {"field": "t", "type": "quantitative"}, "y": {"field": "n", "type": "quantitative"}}}
  ],
  "config": {"font": "Tahoma, sans-serif", "view": {"stroke": null}}
}
```

_Observed NTDS failures (dots, from Goel and Okumoto's Table 1) and the two models fitted for this page (curve values from the last line of `fit.py`'s output), extrapolated to day 400. The models agree on the observed 250 days and diverge after it._ {% cite goel1979time %}

## 5. Compare with the FIO, with its uncertainty

Suppose the module's FIO is 0.02 failures per day, one failure per 50 days of use (a constructed objective; derive yours with [Severity Classes and the FIO](fio.md)). Solving λ(*t*) = FIO gives a closed form for each model: *t* = ln(*ab*/FIO)/*b* for Goel–Okumoto and *t* = (λ<sub>0</sub>/FIO − 1)/(λ<sub>0</sub>θ) for the logarithmic Poisson model. The uncertainty of λ(250) comes from a **parametric bootstrap**: pretend the fitted model is true, simulate many failure logs from it, refit each, and see how much the estimate varies. The script draws 2,000 logs over 250 days with a fixed seed, so the run repeats exactly, refits Goel–Okumoto to each, skips logs that show no growth (no fit exists), and reads the 5th and 95th percentiles of λ(250).

```text
G-O reaches FIO 0.02/day at day 395: 145 more test days, 4.5 more failures expected
log-Poisson reaches FIO at day 1047: 797 more test days, 26.8 more failures expected
bootstrap 90% interval for lambda(T): 0.0179 to 0.0885/day (1973 samples, 27 skipped)
m(t) G-O / log-Poisson: 50: 8.54/8.00, 100: 14.94/13.95, 150: 19.73/18.69, 200: 23.32/22.63, 250: 26.00/26.00, 300: 28.01/28.95, 350: 29.51/31.56, 400: 30.64/33.91
```

The Goel–Okumoto figure checks by hand: ln(0.1968 / 0.02) / 0.00579 = 2.286 / 0.00579 ≈ 395 days, and the log-Poisson one as (0.1907 / 0.02 − 1) / (0.1907 × 0.0427) ≈ 1,048 days with these rounded values (1,047 unrounded). The bootstrap interval covers only the noise in the data under the Goel–Okumoto model; it does not cover the chance that the model itself is wrong; dropping the 27 no-growth resamples makes its upper end optimistic, which the gap between the two models shows is the larger risk.

## 6. Decide

**Decision point: stop testing?** Criterion (the team's rule): stop when the current intensity is at or below the FIO under both models and the upper end of its 90% interval is within twice the FIO. Here the point estimates are 0.046 and 0.063 failures per day, 2.3 and 3.1 times the FIO, and the 90% interval reaches 0.089. **Decision: continue testing.** By point estimates the rule cannot be met before day 1,047 (log-Poisson); Goel–Okumoto alone reaches the FIO at day 395. No stop date can be promised while the models disagree by two years; refit and recheck the rule after each new failure.

What happened next: the module showed five more failures in a further test phase, the last at day 540, one in use and two after the module was reworked: eight in all, exactly the remaining failures the model estimated at day 250 {% cite goel1979time %}.

## 7. The artifact: a stop-testing record

Fill one record per severity class at each decision point and keep it with the release notes.

| Field | NTDS A-module, day 250 |
|---|---|
| Failure log | 26 failures, calendar days 9–250, one class, duplicates removed |
| Trend test | Laplace *u* = −2.45 (growth at 5%); *u* > 0 until failure 21 |
| Model 1 | Goel–Okumoto: *a* = 33.99, *b* = 0.00579/day; λ = 0.046/day; 8.0 failures remaining |
| Model 2 | logarithmic Poisson: λ<sub>0</sub> = 0.191/day, θ = 0.043; λ = 0.063/day |
| Uncertainty | 90% bootstrap interval for λ: 0.018–0.089/day |
| FIO | 0.02 failures/day (constructed) |
| Decision | continue; refit after each failure; no stop date yet (G-O alone: day 395; log-Poisson: day 1,047) |
| Risks | growth rests on two long gaps (33 and 91 days); was test effort constant then? |

## Common mistakes

These mistakes recur when teams fit growth models; each has a symptom you can check.

1. **Fitting before the trend test.** Refitted after each failure, the Goel–Okumoto estimate of the total *a* is 134 at failure 22, 155 at 23, 27 at 24 and 34 at 26. Before failure 22 the estimation has no solution at all (`brentq` raises a `ValueError`). Spot it: *u* > −1.96, or estimates that jump between refits.
2. **Logging fix dates instead of failure dates.** Fixes come in batches before a release, so the log shows bunches that are not in the failures. Spot it: many failures on one day, often a Friday.
3. **Calendar time with uneven test effort.** A 91-day gap is growth only if testing went on; a holiday or a team moved to another project looks the same in the log. Spot it: compare the gaps with the test hours booked. Musa prefers execution time for this reason {% cite musa2004sre %}.
4. **Reading the point estimate as a fact.** λ = 0.046 has a 90% interval from 0.018 to 0.089, a factor of five between its ends. Spot it: a decision that flips when you use the interval's upper end.
5. **New features during testing.** Each feature adds faults and restarts the curve. Spot it: *u* turning positive again after a merge.

## How solid is this?

- **Where it comes from.** The data, the model and its estimates are Goel and Okumoto's {% cite goel1979time %}; the trend test follows Kanoun and Laprie {% cite kanoun1994trend %}; the two-model practice is Musa's {% cite musa2004sre %}; the five steps for defining severity classes and the FMEA and fault-tree examples follow the course material derived from Musa.
- **What the evidence says.** One dataset, one good prediction: eight failures predicted and eight observed is encouraging, not proof. Goel and Okumoto's own confidence region for *a* and *b* is broad.
- **What is computed here.** The log-Poisson fit, the bootstrap interval and the time to the FIO were computed for this page; the FIO is constructed.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
