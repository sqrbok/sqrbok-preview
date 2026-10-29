---
title: Defect Density Benchmarks
parent: Measurement
nav_order: 10
layout: default
vegalite: true
---

# Defect Density Benchmarks

**Defect density** is the number of defects divided by the size of the software, usually per thousand lines of code (KLOC); a **defect density benchmark** is a set of published figures from other projects against which a team can compare its own {% cite shah2012defect %}.

## 1. What was measured

The best-known overview is a scoping study, a systematic search of the published literature, by Shah, Morisio and Torchiano {% cite shah2012defect %}. From 3,774 papers published in 2000–2011 they kept 19 that reported whole-project defect densities: 109 projects, after dropping one outlier at 120 defects per KLOC. Their measure is cumulative and counts only defects found **after release**: all post-release defects up to the end of the observed period, divided by the size in KLOC at that point.

## 2. The typical figure

Across the 109 projects the mean was 7.47 defects per KLOC, the median 4.3 and the standard deviation 7.99 {% cite shah2012defect %}. The values span three orders of magnitude, from 0.05 to close to 50, and 89 of the 109 projects lie between 1 and 25. A few very defective projects pull the mean up, so the median is the better "typical project".

**Example.** A team's Python web service has 12,000 lines of code, and users reported 30 defects in its first year after release. Its defect density is 30 / 12 = 2.5 defects per KLOC. At 12,000 lines it belongs to the small projects of §3, whose median is 5.3, so it sits below the typical small project. So the service is not unusual, which is not the same as good: a Python service is not like the C and Java projects behind the figures, and the team may count defects differently. The benchmark counts only defects found after release; a project with no users yet has no comparable figure, and should compare its own releases over time instead.

## 3. Three factors that moved the figure

The study compared groups of projects {% cite shah2012defect %}:

- **Open versus closed source.** The 32 open-source projects had a median of 2.75 against 5.4 for the 77 closed-source ones; the means differ by about 4 defects per KLOC (4.66 against 8.6), a statistically significant, medium-sized difference.
- **Language.** C projects had a mean of 10.0 and Java projects 5.9, a significant difference of 4.1. C++ did not differ significantly from either.
- **Size.** Projects were clustered into small (up to 400,000 lines, 88 projects), medium (400,000 to 2 million, 15) and large (above 2 million, 5); that is 108 projects, and the paper does not say where the 109th falls. The means fell from 8.63 to 3.3 to 0.38 (small-project median 5.3), and every pair differed significantly.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 420, "height": 300,
  "data": {"values": [
    {"group": "All projects (109)", "i": 0, "factor": "All", "median": 4.3, "mean": 7.47},
    {"group": "Closed source (77)", "i": 1, "factor": "Development mode", "median": 5.4, "mean": 8.6},
    {"group": "Open source (32)", "i": 2, "factor": "Development mode", "median": 2.75, "mean": 4.66},
    {"group": "C (43)", "i": 3, "factor": "Language", "median": 7.9, "mean": 10.0},
    {"group": "Java (45)", "i": 4, "factor": "Language", "median": 3.5, "mean": 5.9},
    {"group": "C++ (11)", "i": 5, "factor": "Language", "median": 1.3, "mean": 8.73},
    {"group": "Small, < 400K LOC (88)", "i": 6, "factor": "Size", "median": 5.3, "mean": 8.63},
    {"group": "Medium, 400K-2M LOC (15)", "i": 7, "factor": "Size", "median": 2.8, "mean": 3.3},
    {"group": "Large, > 2M LOC (5)", "i": 8, "factor": "Size", "median": 0.40, "mean": 0.38}
  ]},
  "encoding": {
    "y": {"field": "group", "type": "nominal", "sort": {"field": "i"}, "axis": {"title": null, "labelLimit": 220}}
  },
  "layer": [
    {"mark": {"type": "bar", "height": 20},
     "encoding": {
       "x": {"field": "median", "type": "quantitative", "axis": {"title": "Post-release defects per KLOC (bar = median, black tick = mean)"}},
       "color": {"field": "factor", "type": "nominal", "scale": {"domain": ["All", "Development mode", "Language", "Size"], "range": ["#2D6E2A", "#019546", "#7fbf8f", "#c99700"]}, "legend": {"title": null, "orient": "bottom"}}}},
    {"mark": {"type": "tick", "color": "#282828", "thickness": 3, "size": 22},
     "encoding": {"x": {"field": "mean", "type": "quantitative"}}}
  ],
  "config": {"font": "Tahoma, sans-serif", "axis": {"labelFontSize": 12, "titleFontSize": 12}, "legend": {"labelFontSize": 12}, "view": {"stroke": null}}
}
```

_Median (bar) and mean (tick) post-release defect density by group; values as printed in Tables 4 and 5 of the paper, number of projects in brackets. The C++ group shows how far a mean can sit from the median in a small, skewed sample._ {% cite shah2012defect %}

## When to use it — and when not

- **Use** the benchmark to judge whether your own figure is in a plausible range, or to set a first quality goal before you have history of your own.
- **Do not** use it to rank teams or languages. Compare a project with its own earlier releases first, as [Developing Metrics with GQM](development.md) shows for two teams' defect densities.

## Limitations

The three factors are tangled. In the sample, 56% of the Java projects were open source but only 9% of the C projects, so "Java beats C" and "open source beats closed" may be partly the same finding {% cite shah2012defect %}. The large-project group has only five projects. The authors' explanation for the size effect, more testing in large projects, is untested: the study recorded no process data.

## How solid is this?

- **Where it comes from.** One scoping study of 109 projects reported in 19 papers {% cite shah2012defect %}; for 46% of them the paper reported the figure, for 45% the authors computed it and for 9% extrapolated it from averages. The languages named are C, Java and C++, with ten projects in other languages such as Perl.
- **What is contested.** Lines of code and defects were counted in different ways: nearly all source papers did not say how size was measured, and the definition of a defect varied. Published projects are likely to be the more successful ones, which would bias the figures downward. The paper's summary says large projects are "10 times less defective than medium ones"; its own means (3.3 against 0.38) give a ratio of about 9.
- **What we do not hold.** More recent project-level benchmarks, and the commercial databases (such as Capers Jones') that the paper does not use.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
