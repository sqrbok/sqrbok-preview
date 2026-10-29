---
parent: Quality
nav_order: 1.5
title: Cost of Late Fixes
layout: default
vegalite: true
---

# Cost of Late Fixes

The **cost of a late fix** is the extra effort needed to remove a defect found long after it was made, compared with removing it in the phase that made it; the classic claim is that it grows steeply with every phase the defect survives {% cite boehm1981economics %}.

## 1. What the measurements show

The longer a defect survives, the more work it takes to remove: a wrong requirement found in operation means changing the requirement, the design, the code, the tests and the deployed system. Boehm's 1981 data, as NASA's authors reproduce them, put the cost of fixing a requirements error in operation at 40 to 1,000 times the cost of fixing it during requirements {% cite boehm1981economics stecklein2004costs %}. Twenty years later Boehm and Basili restated the rule carefully: a problem found after delivery is *often* 100 times more expensive to fix than one found in requirements and design, but for small, non-critical systems the ratio is "more like 5:1 than 100:1" {% cite boehm2001top10 %}.

NASA measured the effect on its own projects. On a major aircraft programme, Stecklein et al. traced 231 true errors and costed each fix by the phase in which it was found {% cite stecklein2004costs %}:

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 460, "height": 260,
  "data": {"values": [
    {"phase": "Requirements", "i": 0, "cost": 22632},
    {"phase": "Design", "i": 1, "cost": 87832},
    {"phase": "Build", "i": 2, "cost": 354808},
    {"phase": "Test", "i": 3, "cost": 1370888},
    {"phase": "Operations", "i": 4, "cost": 3558215}
  ]},
  "encoding": {
    "x": {"field": "phase", "type": "ordinal", "sort": {"field": "i"}, "axis": {"title": "Phase in which the requirements error was found", "labelAngle": 0}},
    "y": {"field": "cost", "type": "quantitative", "scale": {"type": "log"}, "axis": {"title": "Average cost to fix (US$, log scale)", "format": "~s"}}
  },
  "layer": [
    {"mark": {"type": "line", "color": "#2D6E2A", "strokeWidth": 2}},
    {"mark": {"type": "point", "filled": true, "size": 80, "color": "#019546"}},
    {"mark": {"type": "text", "dy": -12, "fontSize": 12}, "encoding": {"text": {"field": "cost", "type": "quantitative", "format": "$,"}}}
  ],
  "config": {"font": "Tahoma, sans-serif", "axis": {"labelFontSize": 13, "titleFontSize": 13}, "view": {"stroke": null}}
}
```

_Average cost to fix one requirements error on an aircraft programme, by the phase in which it was found; values as printed in the paper (method 2). On the log scale the points lie close to a straight line: the cost grows by a roughly constant factor per phase._ {% cite stecklein2004costs %}

From requirements to operations the cost rises 3,558,215 / 22,632 ≈ 157 times. The same study found about 29 times on spacecraft, so the multiplier depends on the kind of system; the authors limit their results to hardware-software projects of this character {% cite stecklein2004costs %}.

## 2. Example: one requirement, three moments

A booking service must reject reservations for past dates. If a reviewer spots the missing rule while the requirement is written, the fix is one sentence. If testing finds it, the rule must be added to the specification, the validation code and the tests, and the build rerun. If customers find it, the team must also find and repair the bad bookings already stored, notify the affected users and ship a hotfix. Each step adds work that the earlier fix did not need. _(Constructed example.)_

## When to use it, and when not

- **Use** the figures to argue for reviews, inspections and early tests, and to explain why a defect's origin and the phase that found it are both worth recording (see [Defect Injection and Removal](../organization/01-coq/02-defect-injection-removal.md)).
- **Do not** quote "100 times" as a law or apply NASA's multipliers to a small web service: the ratio depends on the system, and the studies say so themselves.

## How solid is this?

- **Where it comes from.** Boehm's 1981 table, Boehm and Basili's 2001 summary and NASA's costed error data from three methods on hardware-software programmes {% cite boehm1981economics boehm2001top10 stecklein2004costs %}.
- **What is contested.** No study measures exactly "100 times": Boehm's range is 40 to 1,000, NASA found about 29 on spacecraft and 157 on the aircraft programme, and Boehm and Basili name 5:1 for small systems. Menzies et al. found no delayed-issue effect in 171 software projects {% cite menzies2017delayed %}. Teach the direction, not a fixed multiplier.
- **What we do not hold.** Comparable costed data for modern web or mobile software delivered continuously.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
