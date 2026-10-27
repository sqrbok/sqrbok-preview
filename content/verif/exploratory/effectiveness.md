---
title: "Exploratory Testing: Evidence"
parent: Exploratory Testing
nav_order: 5
layout: default
vegalite: true
page_type: deep-dive   # the area's evidence page: five studies, each needs its setup stated
---

# Exploratory Testing: Evidence

**The evidence on exploratory testing comes from a handful of controlled experiments that compare it with test-case-based testing (TCT) on the same software.** In equal-length test sessions, exploration finds about as many defects as prepared test cases; counting design time, it costs far less. When the same fixed time must also cover design, exploration finds more. Each study's set-up decides what its numbers mean, so each is described before its result.

## 1. Same detection, a fraction of the effort

Itkonen et al. had 79 students test two feature sets of an open-source text editor in 90-minute sessions, once with test cases they had designed beforehand and once exploring {% cite itkonen2007defect %}. Exploration found 7.04 defects per subject and test cases 6.37; the difference was not statistically significant (p = 0.088). But designing the test cases took about 7 hours on top of the 1.5-hour session. For example, a typical TCT subject spent 8.5 hours in total for the result an ET subject reached in 1.5.

A replication with 51 students removed the fixed session length {% cite itkonen2014testcases %}. Detection again did not differ significantly (ET 5.47, TCT 6.06, p = 0.093), while total effort did: 4.58 hours for exploration against 19.47 hours for test cases, of which 14.12 were design.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container", "height": 170,
  "data": {"values": [
    {"row": "Itkonen 2007 · ET", "part": "Testing", "hours": 1.5},
    {"row": "Itkonen 2007 · TCT", "part": "Testing", "hours": 1.5},
    {"row": "Itkonen 2007 · TCT", "part": "Test case design", "hours": 7},
    {"row": "Itkonen 2014 · ET", "part": "Testing", "hours": 4.58},
    {"row": "Itkonen 2014 · TCT", "part": "Testing", "hours": 5.35},
    {"row": "Itkonen 2014 · TCT", "part": "Test case design", "hours": 14.12}
  ]},
  "mark": {"type": "bar"},
  "encoding": {
    "y": {"field": "row", "type": "nominal", "title": null, "sort": null},
    "x": {"field": "hours", "type": "quantitative", "title": "Mean hours per subject", "stack": "zero", "scale": {"domain": [0, 20]}},
    "color": {"field": "part", "type": "nominal", "title": null,
              "scale": {"domain": ["Testing", "Test case design"], "range": ["#019546", "#c99700"]}, "legend": {"orient": "bottom"}},
    "order": {"field": "part", "sort": "descending"}
  },
  "config": {"font": "Tahoma", "axis": {"labelFontSize": 13, "titleFontSize": 13}, "legend": {"labelFontSize": 13}}
}
```

_Mean effort per subject. Defects found did not differ significantly in either study._ {% cite itkonen2007defect itkonen2014testcases %}

Effort for similar results was thus about 5.7 times lower in the first study (8.5 ÷ 1.5) and 4.3 times lower in the second (19.47 ÷ 4.58). Test cases also produced more false defect reports (duplicates, unclear or non-existent defects): 2.08 against 1.03 per subject in the first study and 2.90 against 1.84 in the replication, both significant {% cite itkonen2007defect itkonen2014testcases %}.

## 2. When the clock includes design

Afzal et al. gave both groups the same 90 minutes for *everything*, test case design included {% cite afzal2015experiment %}. The 70 subjects (46 students, 24 practitioners) tested the same editor. Exploration found 8.34 defects per subject, test cases 1.83, a difference of about 4.6 times (p ≈ 10⁻¹⁰). This does not show exploration is better per se: it shows that under a fixed time budget, writing test cases first leaves little time to run them.

## 3. What kinds of defects

In the first Itkonen study the two approaches found similar mixes of defect types {% cite itkonen2007defect %}. Exploration found more GUI defects (70 against 49) and usability problems (19 against 5), but only the usability difference was significant, and the numbers are small.

Shah et al. compared exploratory, scripted and hybrid testing (test cases plus open missions) with six practitioners with at least three years of experience, each using all three approaches in turn (scripted, then exploratory, then hybrid) on three versions of a calculator application, each seeded with 25 defects {% cite shah2014hybrid %}. Exploration found the most defects (13.16 per tester, hybrid 10.67, scripted 7.0) but covered the fewest of the specified functions (6.67, against 8.83 for hybrid and 7.83 for scripted). Freedom finds more, but leaves gaps no one planned.

## 4. In industry

In two companies using session-based ET, Itkonen and Rautiainen measured 4.8 and 8.7 defects per hour of testing, against 2.47 and fewer than 3 defects per hour reported for functional and use-case-based testing in other studies {% cite itkonen2005exploratory %}. In a survey in Estonia and Finland, 44 of 50 respondents (88%) said they used ET, and 75% of them had no tool support for it {% cite pfahl2014survey %}.

## 5. The tester may matter more than the hours

In the replication, the time an exploring student spent did not correlate with the defects they found (r = 0.025, p = 0.86), while for test cases it did {% cite itkonen2014testcases %}. In Afzal's experiment practitioners found more defects than students (median 9 against 6), but the difference was not significant (p = 0.07) {% cite afzal2015experiment %}, so the evidence points at skill without proving it. Practitioners in the survey named the high demand on tester skill as ET's main drawback {% cite pfahl2014survey %}.

## When to use the evidence — and when not

The evidence supports exploring when effort is limited and the goal is finding defects. It does not support dropping test cases where coverage of specified functions or a reproducible record is required; there, exploration is the complement, as Shah's hybrid suggests.

## Limitations

All the experiments used one small desktop application and sessions of a few hours; most subjects were students. The test case designs were the subjects' own, so a better test designer might change the TCT result.

## How solid is this?

- **Where it comes from.** Four controlled experiments {% cite itkonen2007defect itkonen2014testcases afzal2015experiment shah2014hybrid %}, one industrial case study {% cite itkonen2005exploratory %} and one survey {% cite pfahl2014survey %}.
- **What is contested.** The industrial rates are not a controlled comparison: the 2.47 and fewer than 3 per hour come from different studies, products and testers. The survey's authors warn that its 88% may reflect self-selection: the invitation said the survey was about ET. Shah's study had six subjects.
- **What we do not hold.** Studies with large professional teams or long-lived products; none of the experiments followed a product over several releases.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
