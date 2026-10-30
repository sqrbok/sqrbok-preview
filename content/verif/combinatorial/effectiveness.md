---
title: Combinatorial Testing in Industry
parent: Combinatorial Testing
nav_order: 7
layout: default
vegalite: true
page_type: deep-dive  # the evidence page: each industrial study with its numbers, setting and caveat
---

# Combinatorial Testing in Industry

The **industrial evidence** for combinatorial testing is a set of case studies comparing it with a company's usual tests on real systems: how many faults each finds, and at what cost. The strongest show combinatorial tests finding interaction faults that experienced testers missed {% cite hu2020realworld daoud2025effectiveness %}. A **pairwise** (2-way) suite contains every pair of values of any two input parameters, a **3-way** suite every triple; tools such as ACTS generate them from an input model ([Combinatorial Testing](index.md)).

## 1. Five systems, four companies

Hu, Wong, Kuhn and Kacker tested five industrial systems with pairwise suites from ACTS and compared them with each company's in-house testing: a calligraphy application, an IoT water-flow controller, a sales data system, a locomotive dashboard and a Linux file manager {% cite hu2020realworld %}. The sponsors wanted at most about 120 tests per system, so only pairwise was used.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container", "height": 170,
  "data": {"values": [
    {"kind": "Faults needing one input (28)", "who": "Combinatorial tests", "n": 25, "pct": "89.3%"},
    {"kind": "Faults needing one input (28)", "who": "In-house testing", "n": 20, "pct": "71.4%"},
    {"kind": "Faults needing several inputs (30)", "who": "Combinatorial tests", "n": 28, "pct": "93.3%"},
    {"kind": "Faults needing several inputs (30)", "who": "In-house testing", "n": 2, "pct": "6.7%"}
  ]},
  "encoding": {
    "y": {"field": "kind", "type": "nominal", "title": null, "axis": {"labelLimit": 260}},
    "yOffset": {"field": "who"},
    "x": {"field": "n", "type": "quantitative", "title": "Faults found", "scale": {"domain": [0, 32]}},
    "color": {"field": "who", "type": "nominal", "title": null, "scale": {"range": ["#019546", "#1f5fa8"]}, "legend": {"orient": "bottom", "labelFontSize": 13}}
  },
  "layer": [
    {"mark": {"type": "bar", "cornerRadiusEnd": 3}},
    {"mark": {"type": "text", "align": "left", "dx": 4, "fontSize": 13}, "encoding": {"text": {"field": "pct"}}}
  ],
  "config": {"font": "Tahoma", "axis": {"labelFontSize": 13, "titleFontSize": 13}, "view": {"stroke": null}}
}
```

_Faults found in all five systems together, from Table 40 of the source._ {% cite hu2020realworld %}

The gap is in faults that need several inputs together: the pairwise suites found 28 of 30, the in-house teams 2. For example, in four of the five systems the in-house teams found none of them. The authors' explanation: the in-house teams chose test values from experience and functional requirements, so the particular pairs that trigger these faults were rarely in the same test, while a pairwise suite contains every pair by construction. Across the five systems, 28 faults needed only one input. The combinatorial team's suites found 25 of them, the in-house teams 20. The authors traced 8 of the 28 to random values chosen within a class and 3 to boundary values, two ways of choosing values that the in-house teams, in the authors' examples, did not use. For these faults the edge came from the values, not from the combinations.

## 2. Tricentis: real defects, measured hours

Daoud, Bures and Alipio applied ACTS at Tricentis to its test automation product for ServiceNow, using real historical defects and copies of them placed in nearby code {% cite daoud2025effectiveness %}.

| Study | Original tests | Pairwise | Mixed strength (pairs only within chosen parameter groups) |
|---|---|---|---|
| Two forms (9 and 10 parameters): defects found | 18 of 22 | 22 of 22 | 21 of 22 |
| Two forms: hours per defect found | 5.11 | 3.45 | 3.39 |
| Configurations (OS, browser, version): execution hours | 394 | 205 | — |
| Configurations: hours per defect found | 4.38 | 2.28 | — |

The configuration study is a separate study from the forms: it ran all 790 commands of the product on combinations of operating system (2), browser (2) and product version (6). Pairwise needed 12 configurations, against an uncontrolled set before; execution fell by 48%, and both found all 90 historical defects, which makes the hours comparable. The authors, with over 15 years of experience in test automation and combinatorial testing, applied the method themselves, and only one product was studied.

## 3. When pairwise is not enough

Bures and Ahmed compared pairwise and 3-way suites on the JTrac issue tracker, injecting faults into 10 copies of its input model {% cite bures2017effectiveness %}. The pairwise suite had 48 tests, the 3-way suite 322. In 3 of the 10 copies only the 3-way suite found the fault. Per test the two were alike: 19 of 48 pairwise tests (39.6%) and 130 of 322 3-way tests (40.4%) found nothing. The authors judged pairwise more economical overall, and 3-way the only way to catch the three faults.

## 4. Earlier and newer settings

- **Telephony, 1992.** At AT&T, 354 tests from orthogonal arrays and 68 hand-written ones were used on a messaging system. The authors estimated that 22% more faults would have been found than by the previous release's conventional testing, which had taken twice as long {% cite grindal2005survey %}.
- **Emergency braking, 2019.** At AVL, Tao et al. turned the Euro NCAP test protocols into a model of 37 parameters with 39 constraints. The IPOG generation algorithm produced 319 pairwise scenarios, which ran in a driving simulator. The study had no automatic pass or fail check and no comparison with another method {% cite tao2019industrial %}.
- **Machine-learning data, 2021.** Lanus et al. measured how many label-centred pairs each of two satellite-image sets contains: each image is described by 12 features split into 3 bins each, and a label-centred pair is one feature bin together with the image's label (plane or not), so there are 12 · 3 · 2 = 72. The northern set covered 67 of 72 (0.93), the southern 60 of 72 (0.83), although it had twice as many images: 21,151 against 10,849. More data is not the same as more varied data {% cite lanus2021metrics %}.

## When to use it — and when not

- **Use** the numbers to argue for pairwise suites where inputs interact: forms, configurations, platform matrices.
- **Do not** quote them as a guarantee: each comes from a handful of systems, chosen by researchers who expected combinatorial testing to help.

## Limitations

Hu et al. name the barriers their sponsors met: the size of suite testers will run by hand, the need for detailed requirements to build a good model, and **masking**: an error or failure that ends a test early hides what the test would have checked afterwards (the same effect as invalid values in [Constraints and Invalid Values](advanced.md)); they advise running tests and checking outputs in parallel {% cite hu2020realworld %}.

## How solid is this?

- **Where it comes from.** Two industrial studies with real faults, one study with injected faults, and older reports collected by Grindal et al. {% cite hu2020realworld daoud2025effectiveness bures2017effectiveness grindal2005survey %}.
- **What is contested.** The baselines differ, from in-house testing to a previous release's tests, so the percentages cannot be compared across studies. Bures and Ahmed used injected faults, which may differ from real ones.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
