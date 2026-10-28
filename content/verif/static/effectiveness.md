---
title: Static Analysis Effectiveness
parent: Static Analysis
nav_order: 10
layout: default
page_type: deep-dive  # the evidence page: per-release yields, costs and their caveats
vegalite: true
---

# Static Analysis Effectiveness

How effective is automated static analysis (ASA) compared with inspection and testing? The best industrial evidence, from three Nortel products, says: it finds about as large a share of the faults present as code inspection does, at a lower cost per fault, and different kinds of fault from testing {% cite zheng2006static %}.

## 1. Example: the Nortel study

Zheng and colleagues analysed fault data from three large network-service products at Nortel Networks, over three million lines of C and C++, with faults reported by more than 200 inspectors and testers and by customers. The ASA tools were FlexeLint, Klocwork and Reasoning's Illuma {% cite zheng2006static %}. Their measure is the **fault-detection yield**: the share of the faults present when a phase starts that the phase finds. The faults present are counted afterwards, as those the phase found plus those every later phase found:

yield = 100 × (faults found by the phase) / (faults found by the phase and by all later phases).

The usual order is inspection, then testing, then customers. Static analysis was slotted in differently per release: before inspection in release C.1, during testing in release C.2 and in product A; release C.0 had none. For example, if static analysis finds 12 faults and inspection, testing and customers later find 8, 20 and 4, its yield is 100 × 12 / 44 ≈ 27%.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 360, "height": 200,
  "data": {"values": [
    {"r": "A", "t": "ASA", "y": 23.39},
    {"r": "C.0", "t": "Inspection", "y": 39.55}, {"r": "C.0", "t": "Testing", "y": 96.73},
    {"r": "C.1", "t": "ASA", "y": 31.00}, {"r": "C.1", "t": "Inspection", "y": 20.48}, {"r": "C.1", "t": "Testing", "y": 98.18},
    {"r": "C.2", "t": "ASA", "y": 36.53}, {"r": "C.2", "t": "Inspection", "y": 33.21}, {"r": "C.2", "t": "Testing", "y": 62.57}
  ]},
  "encoding": {
    "x": {"field": "r", "type": "nominal", "title": "Product or release", "axis": {"labelAngle": 0}},
    "xOffset": {"field": "t", "sort": ["ASA", "Inspection", "Testing"]},
    "y": {"field": "y", "type": "quantitative", "title": "Fault-detection yield (%)", "scale": {"domain": [0, 100]}},
    "color": {"field": "t", "type": "nominal", "title": null, "sort": ["ASA", "Inspection", "Testing"],
              "scale": {"range": ["#019546", "#c99700", "#9bbf9b"]}, "legend": {"orient": "bottom", "labelFontSize": 13}}
  },
  "layer": [
    {"mark": {"type": "bar"}},
    {"mark": {"type": "text", "dy": -6, "fontSize": 11}, "encoding": {"text": {"field": "y", "type": "quantitative", "format": ".0f"}}}
  ],
  "config": {"font": "Tahoma", "axis": {"titleFontSize": 13, "labelFontSize": 12}, "view": {"stroke": null}}
}
```

_Fault-detection yield per practice in each Nortel product or release where it could be computed (Table 4); C.0 used no ASA; product A had no inspection, and its testing yield could not be computed because test and customer failures were not told apart; product B is missing because its inspection records were unavailable. In C.2, ASA ran during testing, so ASA's faults count in testing's denominator._ {% cite zheng2006static %}

## 2. What the numbers say

- **Yield.** ASA found 23% (product A) to 37% (release C.2) of the faults present, inspection 20% (C.1) to 40% (C.0). The authors found the two "not significantly different". Testing found 63% to 98% of the faults present when it started. Comparing testing's yield with static analysis's yield in the same release gives 1.7 for C.2 (62.57 / 36.53) and 3.2 for C.1 (98.18 / 31.00), the paper's "two to three times". It remains the main way to find the faults left by that point {% cite zheng2006static %}.
- **Cost.** The cost of ASA per fault found, divided by the cost of inspection per fault found, was 0.72 for release C.1 and 0.61 for C.2: ASA was cheaper per fault, but of the same order of magnitude {% cite zheng2006static %}.
- **Fault types.** Classified with IBM's Orthogonal Defect Classification, ASA faults were mostly **Checking** (a missing or wrong validation) and **Assignment** (a wrong or missing initial value). Test and customer failures were mostly **Function** and **Algorithm** faults. About 90% of inspection faults were Algorithm, Documentation or Checking {% cite zheng2006static %}.
- **Problem modules.** The number of ASA faults in a module was a fairly good predictor, before testing, of which modules would turn out fault-prone {% cite zheng2006static %}.
- **Product quality.** Whether adding ASA made the final products better was inconclusive: the releases varied too much {% cite zheng2006static %}.

The overall conclusion: ASA is "an economical complement" to inspection and testing, catching mechanical faults early so later phases can focus on functional ones {% cite zheng2006static %}.

## 3. Other evidence on this site

The other pages add pieces the Nortel study lacks. The eBay evaluation relied on an estimate that a defect found during development costs about ten times less than one found in testing, a figure it took from earlier literature rather than measured {% cite jaspan2007understanding %}; at Google, developers marked about 5% of shown findings not useful ([Deploying Static Analysis](deployment.md)) {% cite sadowski2015tricorder %}. Six Java tools ranged from 18% to 86% precision and shared fewer than 0.4% of their warnings ([Comparing Static Analysis Tools](tools.md)) {% cite lenarduzzi2021comparison %}. And KLEE's symbolic execution covered 84.5% of GNU Coreutils lines against the developers' 67.7% ([Symbolic Execution](symbolic-execution.md)) {% cite cadar2008klee %}.

## When to use this evidence — and when not

- Use it to argue for ASA next to inspection and testing, aimed at checking and assignment faults, and to rank modules for extra testing.
- Do not use it to argue for ASA instead of testing: testing's yield was 1.7 to 3.2 times that of static analysis in the same release.

## How solid is this?

- **Where it comes from.** One company, three products, C and C++ code, three ASA tools from the mid-2000s {% cite zheng2006static %}.
- **What is contested.** Yields depend on the phase order. For C.2 the paper explains testing's relatively low yield (63%) by ASA running during testing, so that ASA's faults are in the denominator of testing's yield; it gives no further breakdown. Severity was not considered, and most customer failures were classified as Function faults after the fact for lack of root-cause data.
- **What we do not hold.** A comparable study of modern tools (SonarQube, CodeQL, Infer) against inspection and testing on the same code.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
