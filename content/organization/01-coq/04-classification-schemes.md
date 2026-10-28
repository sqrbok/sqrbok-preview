---
title: Defect Classification Schemes
parent: Cost of Quality
nav_order: 4
layout: default
---

# Defect Classification Schemes

A **defect classification scheme** is a fixed set of attributes, each with a closed list of values, that every defect report records so that the defects of a release can be counted, compared and traced back to the process steps that should have stopped them {% cite butcher2002improving huber1999comparison %}. The [origin / where-found matrix](03-defect-classification.md) uses two attributes; the schemes on this page use more.

## 1. Orthogonal Defect Classification (ODC)

ODC, developed at IBM, chooses attributes that do not overlap ("orthogonal"), so that together they span the defect information space {% cite chillarege2000odc butcher2002improving %}. Two people classify each defect:

| Who | Attribute | What it records |
|---|---|---|
| Submitter, when the defect is found | Activity | the process step that exposed it, such as code inspection or function test |
| | Trigger | the condition that exposed it, such as coverage, variation, sequencing, interaction |
| | Impact | the actual or perceived effect on the customer |
| Responder, when it is fixed | Target | the high-level work product that was fixed: design, code, documentation |
| | Defect type | the nature of the correction |
| | Qualifier | whether the fix added something missing, corrected something incorrect, or removed something extraneous |
| | Source, Age | where the code came from (written in house, reused from a library, ported, or outsourced) and whether it was new, base, rewritten or refixed |

**Example from the field.** Butcher, Munro and Kratschmer report an IBM product whose team expected field defects in new code. ODC showed many in base (unchanged) and rewritten code; most field escapes came from function verification test, the most common trigger was *variation* (varying the inputs of a single function), and two components held over 40% of the base-code defects. The team's actions followed directly: more variation test cases, more regression testing for one component, and documented changes for the other {% cite butcher2002improving %}.

## 2. HP's origin, type and mode

Hewlett-Packard's scheme asks where a defect **could have been prevented**, not where it was fixed. Its three levels are *origin*, the first activity where the defect could have been prevented; *type*, the area within that origin responsible, such as logic or a software interface; and *mode*, why it occurred, a level little used within HP. Huber's engineers classified 93 high-priority defects with ODC and 85 of the same set with HP's scheme {% cite huber1999comparison %}:

- ODC's *target* points at the work product that was changed, usually code.
- HP's *origin* points at the first activity that could have prevented the defect. Under HP, 63% of the 85 defects, about 54, originated in design.

HP then **weights** defects by origin with industry-derived factors: specification 14.25, design 6.25, code 2.5. A specification defect costs more because the fix changes the specification, the design and possibly several places in the code {% cite huber1999comparison %}. Weighted counts direct improvement money to the phase where defects are most expensive, not merely most frequent.

## 3. LiDeC: a lighter IEEE 1044

IEEE Std 1044 organizes defect attributes by life-cycle phase: recognition (what were you doing, symptom), investigation (actual cause, source, type), action (resolution, corrective action) and impact identification (severity and others). At Volvo Car Corporation, where suppliers write most of the code, Mellegård et al. adapted it into LiDeC: they raised the level of abstraction, shifted the focus from source code to requirements and other artefacts, and used the company's own terms {% cite mellegard2012lightweight %}.

## When to use which

| Scheme | Choose it when |
|---|---|
| Origin / where found | you need escape data and removal efficiencies with almost no extra effort |
| ODC | a technical team wants process feedback from triggers and defect types, and can train classifiers |
| HP | the goal is a retrospective that targets prevention, with cost weights |
| LiDeC | most code is written by suppliers and you see requirements, not source |

## Limitations

- **Classification costs time.** A root-cause analysis of year-old defect reports took 19 minutes per defect on average in one study cited by Mellegård et al. {% cite mellegard2012lightweight %}; classify while the knowledge is fresh.
- **Values drift.** Without training, different people pick different triggers for the same defect.

## How solid is this?

- **Where it comes from.** ODC's attributes and the IBM case from Butcher et al.; the HP comparison and weights from Huber; LiDeC from Mellegård et al. {% cite butcher2002improving huber1999comparison mellegard2012lightweight %}.
- **What is contested.** Huber notes that weighting factors vary between projects and should be recalibrated from local data.
- **What we do not hold.** Chillarege's original ODC description is cited through Butcher et al.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
