---
title: Inspection Effectiveness
parent: Inspection
nav_order: 7
layout: default
vegalite: true
page_type: deep-dive  # the evidence page: detection rates, effort, cost ratios and company results, each with its source and caveat
---

# Inspection Effectiveness

**Inspection effectiveness** is the share of a product's defects that an inspection finds; its **efficiency** is measured as the effort spent per defect found, so a higher figure is worse. Surveys report that inspections find 60% to 90% of all defects (the reported range; 57% is the fitted typical value in §1), at a fraction of the effort testing needs per defect {% cite laitenberger2000survey %}.

## 1. Detection rates

Single companies report the high end. At IBM, inspections found 90% of all the defects found over a product's life {% cite fagan1976design %}; IBM's RESPOND project in the UK found 93%, and Aetna found 38 of 46, or 83% {% cite fagan1986advances dodd2003inspection %}. A distribution fitted to published results by Briand and colleagues in 1998, as reported by Dodd, is more sober {% cite dodd2003inspection %}:

| Inspection type | Minimum | Most likely | Maximum |
|---|---|---|---|
| Design | 25% | 57% | 84% |
| Code | 19% | 57% | 70% |

The most likely value, 57%, means that an average inspection still leaves over two fifths of the defects for later activities.

## 2. Effort per defect

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 520, "height": 150,
  "data": {"values": [
    {"t": "Design inspection", "min": 0.58, "ml": 1.58, "max": 2.9},
    {"t": "Code inspection", "min": 0.67, "ml": 1.46, "max": 2.7},
    {"t": "Testing", "min": 4.5, "ml": 6, "max": 17}
  ]},
  "encoding": {"y": {"field": "t", "type": "nominal", "title": null, "sort": null, "axis": {"labelFontSize": 14}}},
  "layer": [
    {"mark": {"type": "rule", "strokeWidth": 4, "color": "#9bbf9b"},
     "encoding": {"x": {"field": "min", "type": "quantitative", "title": "Staff-hours per defect found (minimum, most likely, maximum)", "scale": {"domain": [0, 18]}}, "x2": {"field": "max"}}},
    {"mark": {"type": "point", "filled": true, "size": 160, "color": "#019546"},
     "encoding": {"x": {"field": "ml", "type": "quantitative"}, "color": {"condition": {"test": "datum.t === 'Testing'", "value": "#b42318"}, "value": "#019546"}}},
    {"mark": {"type": "text", "dy": -14, "fontSize": 13},
     "encoding": {"x": {"field": "ml", "type": "quantitative"}, "text": {"field": "ml", "type": "quantitative"}}}
  ],
  "config": {"font": "Tahoma", "axis": {"titleFontSize": 13, "labelFontSize": 12}, "view": {"stroke": null}}
}
```

| Staff-hours per defect | Minimum | Most likely | Maximum |
|---|---|---|---|
| Design inspection | 0.58 | 1.58 | 2.9 |
| Code inspection | 0.67 | 1.46 | 2.7 |
| Testing | 4.5 | 6 | 17 |

_Effort to find one defect: the bar runs from minimum to maximum, the dot is the most likely value. Distribution from Briand and colleagues (1998), as reported by Dodd._ {% cite dodd2003inspection %}

**Example.** A module has 30 defects that someone will find either way. At the most likely values, finding them by code inspection takes 30 × 1.46 = 43.8 staff-hours, by testing 30 × 6 = 180 staff-hours: about four times as much. The comparison assumes both find the same defects, which they do not: see the limitations.

## 3. Cost ratios

Later defects cost more to fix as well as to find. At the Jet Propulsion Laboratory, fixing a defect during inspection cost 1/10 to 1/34 of fixing it during formal testing; at IBM's Santa Teresa lab, 1/20 {% cite dodd2003inspection %}. SmartBear's practitioners put fixes during testing at 8 to 12 times the cost of fixes during development, and fixes after release at 30 to 100 times {% cite cohen2006secrets %}. Laitenberger and DeBaud's survey reports that design inspections save 44% and code inspections 39% of total defect costs compared with testing alone {% cite laitenberger2000survey %}.

## 4. Inspection against lighter reviews

Inspections find more than informal reviews, at more effort per review {% cite wiegers2002peer %}. At Ford Motor Company, inspections found 50% more defects per thousand lines than walkthroughs; in one industry study, team reviews found two thirds as many defects per hour as inspections; IBM's Federal Systems Division measured about half the coding productivity in projects that used structured walkthroughs instead of inspections. Fagan's IBM samples had 38% fewer defects per thousand lines than those that only had walkthroughs {% cite fagan1976design %}.

## 5. Company results

- **IBM.** In Fagan's first report, inspections raised net coding productivity by 23% and cost 9% less than walkthroughs {% cite fagan1976design %}; the Federal Systems Division figure in §4 compares whole projects, a different measure from a different part of IBM. Between 1976 and 1984 IBM nearly doubled the code shipped for System/370 while reducing defects per thousand lines by two thirds {% cite fagan1986advances %}.
- **Standard Bank of South Africa and Aetna.** Fagan's 1986 update reports a 95% reduction in corrective maintenance and 0.15 defects per thousand lines at Standard Bank, and a 25% reduction in development resources at Aetna {% cite fagan1986advances %}.
- **Hewlett-Packard.** Moving rework earlier through inspections was estimated to save USD 21,454,000 a year {% cite dodd2003inspection %}.
- **A SmartBear customer.** Using review to reduce defects and usability problems, support calls fell from 50,000 to 20,000 a year over a few years while sales doubled, a saving reported as USD 2.6 million {% cite cohen2006secrets %}.

Against this, design and code inspections typically cost about 15% of the project {% cite fagan1986advances %}.

## When to use it — and when not

Use these figures to argue for inspecting the products where late defects are expensive, and to set expectations: a team that finds 57% has done an average job, not a bad one. Do not use a single company's figure as a target for your own team; the conditions behind it are rarely reported.

## Limitations

- **Inspection and testing find different defects.** Inspection finds omissions, unclear requirements and maintainability problems that tests cannot see; testing finds failures that come from timing, data and the environment. Cost per defect does not compare like with like, and both are needed.
- **Self-reported success.** Most company figures come from the organizations that ran the programs, and the SmartBear figures from a vendor of review tools.

## How solid is this?

- **Where it comes from.** Fagan's IBM papers, Laitenberger and DeBaud's survey, Dodd's compilation of industrial results, Wiegers' guide and SmartBear's practitioner book {% cite fagan1976design fagan1986advances laitenberger2000survey dodd2003inspection wiegers2002peer cohen2006secrets %}.
- **What is contested.** Effort per defect for testing varies widely (4.5 to 17 hours, most likely 6), and the cost ratios depend on how cost is counted.
- **What we do not hold.** The 1998 meta-analysis by Briand and colleagues is known here only through Dodd's report.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
