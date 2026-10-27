---
title: "Operational Profile: Industrial Evidence"
parent: Operational Profile
nav_order: 2
layout: default
vegalite: true
---

# Operational Profile: Industrial Evidence

**The industrial evidence for operational profiles consists of two reported telecom and operating-system projects from the early 1990s and, recently, the discovery that Android apps already ship with usage profiles collected from real users.** Both point to the same lesson: test effort spent where users spend their time pays off, and nobody can guess that distribution reliably.

## 1. AT&T and Hewlett-Packard

Musa reports two projects {% cite musa1993operational %}:

- **AT&T International Definity**, a PBX telephone switch. Combining the operational profile *with other quality-improvement techniques* cut customer-reported problems and maintenance costs by a factor of 10, halved the system-test interval and shortened product introduction by 30%. The project had invested two to three staff-years in the customer study behind its profile.
- **Hewlett-Packard**, a multiprocessor operating system. Reorganising system test around the operational profile, with automated test and failure recording, reduced system-test time and cost by at least 50%.

For an average project Musa estimated that profile-driven testing cuts test cost by 56%, or 11.5% of total project cost, and that the benefit-to-cost ratio of building and using a profile is typically 10 or more {% cite musa1993operational %}.

## 2. Android Cloud Profiles

Android compiles the most-used methods of an app ahead of time. To know which ones, Google Play aggregates the methods real users execute into a **Cloud Profile** per app. Bleier et al. noticed that such a profile is an operational profile at method level, collected for free {% cite bleier2025profile %}:

- 99.89% of the top 1,000 apps had a Cloud Profile;
- a Cloud Profile is a usage profile without probabilities: a list of methods; on average the list contained 7.88% of all the methods in an app, so users exercise a small part of the code;
- the Cloud Profiles were not just supersets of the profiles developers declared themselves, so actual use differed from what developers expected.

They define **profile coverage** as the share of the profile's methods that a test run executes, and compare it with ordinary method coverage.

### Worked example: Candy Crush

Bleier et al. ran two test generators on Candy Crush. Android's random input generator Monkey reached 22.52% method coverage and 21.39% profile coverage. The model-based tool Fastbot2 raised method coverage to 39.33%, a 75% increase, yet its profile coverage fell to 3.13% {% cite bleier2025profile %}. By code coverage Fastbot2 is the better tool; by profile coverage it spent its time in parts of the game that players rarely touch.

The tools compared were Monkey, which sends random taps and keystrokes; DroidBot and Fastbot2, which build a model of the app's screens and choose inputs from it; and a baseline that only starts the app.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container", "height": 150,
  "data": {"values": [
    {"tool": "Monkey (random)", "pct": 37.45},
    {"tool": "Fastbot2 (model-based)", "pct": 32.51},
    {"tool": "DroidBot (model-based)", "pct": 29.50},
    {"tool": "Start app, no input", "pct": 24.87}
  ]},
  "encoding": {
    "y": {"field": "tool", "type": "nominal", "title": null, "sort": "-x"},
    "x": {"field": "pct", "type": "quantitative", "title": "Average profile coverage over 50 apps (%)", "scale": {"domain": [0, 50]}}
  },
  "layer": [
    {"mark": {"type": "bar", "cornerRadiusEnd": 3, "color": "#019546"}},
    {"mark": {"type": "text", "align": "left", "dx": 4, "fontSize": 13}, "encoding": {"text": {"field": "pct", "type": "quantitative", "format": ".2f"}}}
  ],
  "config": {"font": "Tahoma", "axis": {"labelFontSize": 13, "titleFontSize": 13}}
}
```

_Average profile coverage of three test generators and a do-nothing baseline on 50 apps; no tool exceeded 70% on any app._ {% cite bleier2025profile %}

These are averages over 50 apps; Monkey's 37.45% is its average, while the 21.39% above is its result on a single app, Candy Crush. The simple random tool covered the most user-relevant code. Coverage of the app's structure and coverage of its actual use are different measures, and ranking tools by one does not rank them by the other.

## When to use it — and when not

Cite this evidence when arguing for usage data in test planning. Do not quote the AT&T factor of 10 as the effect of the profile alone: the project combined several techniques, and no study separates their contributions.

## Limitations

The 1993 results are self-reported by the technique's authors, from two projects, without a control group. The Android result measures where tests go, not how many faults they find.

## How solid is this?

- **Where it comes from.** Musa's 1993 article {% cite musa1993operational %}; the Android study measured profiles of the top 1,000 apps and compared tools on 50 {% cite bleier2025profile %}.
- **What is contested.** The paper's introduction quotes 2.22% method coverage for Monkey on Candy Crush, its tool comparison 22.52%; we use the comparison, where both tools were measured the same way. Whether the industrial savings transfer to other domains; Musa's projects were mostly real-time telecom systems.
- **What we do not hold.** A controlled study of failures found with and without a profile on the same product.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
