---
title: "Operational Profile Case: UAV Mission Planning"
parent: Operational Profile
nav_order: 6
layout: default
---

# Operational Profile Case: UAV Mission Planning

**This case builds an operational profile for the mission-planning application of an unmanned aerial vehicle (UAV) ground station: two operation modes, usage rates in operations per hour recorded on a prototype, and a merge of correlated operations before the rates become probabilities.** It follows the steps on [Developing an Operational Profile](development.md).

## 1. The system

A UAV system has three major parts: the **ground control station** (GCS), where operators plan and control flights; the **air vehicle**; and a **vehicle-specific module** that translates between the two. The product under test is the **Mission Planning Application** inside the GCS software. An operator builds a mission as a list of waypoints, verifies it against the terrain and uploads it.

## 2. Operation modes

The case separates two modes:

- In **mission composition mode** the operator composes, modifies and stores missions for later use; terrain clearance is verified occasionally.
- In **mission transfer mode** the operator uploads a stored mission to the air vehicle before flight, with occasional last-minute edits.

Each gets its own profile; the tables below are for composition mode.

## 3. Three ways to obtain usage data

The case weighs three sources of occurrence rates:

1. **Estimates from customers and users**: cheap, but not very accurate.
2. **Data from a previous version** of the software: cheap and more accurate, but new features have no history.
3. **Recording operations on a prototype**: relatively expensive, but very accurate.

The project used the third, recording operator sessions on a prototype.

## 4. Worked example: rates, merging and probabilities

The recorded rates, in operations per hour, split some operations by the state they start from:

| Operation | Per hour | Operation | Per hour |
|---|---:|---|---:|
| Create mission (0 missions open) | 4 | Open mission (0 missions) | 3 |
| Create mission (more open) | 6 | Open mission (more) | 8 |
| Add waypoint (< 100 waypoints) | 360 | Save mission (never saved) | 10 |
| Add waypoint (≥ 100 waypoints) | 10 | Save mission (saved before) | 20 |
| Edit waypoint | 66 | Verify mission | 6 |
| Remove waypoint | 54 | Upload mission | 3 |
| Move waypoint | 44 | Clear mission | 1 |
| | | Close mission (never saved / saved before) | 2 / 12 |

The sixteen rates sum to 609 per hour. The case judged mission creation and waypoint editing to be correlated and **merged** the two create operations and the add, edit and remove operations into three: *Create-Add* (370, the two add-waypoint rates together), *Create-Edit* (66) and *Create-Remove* (54). Each Create-X test run starts by creating a mission, so the 10 creates per hour are exercised inside those runs and not counted again: the final profile totals 599 per hour.

Dividing each rate by 599 gives the probabilities (computed in Python, rounded):

| Operation | Per hour | Probability | Runs in 1,000 tests |
|---|---:|---:|---:|
| Create-Add waypoint | 370 | 0.618 | 618 |
| Create-Edit waypoint | 66 | 0.110 | 110 |
| Create-Remove waypoint | 54 | 0.090 | 90 |
| Move waypoint | 44 | 0.073 | 74 |
| Save mission (saved before / never saved) | 20 / 10 | 0.033 / 0.017 | 33 / 17 |
| Close mission (saved before / never saved) | 12 / 2 | 0.020 / 0.003 | 20 / 3 |
| Open mission (more / 0 missions) | 8 / 3 | 0.013 / 0.005 | 13 / 5 |
| Verify mission | 6 | 0.010 | 10 |
| Upload mission | 3 | 0.005 | 5 |
| Clear mission | 1 | 0.002 | 2 |

Plain rounding allocates 999 runs; the largest-remainder method gives the last one to Move waypoint (73.46 runs).

Four waypoint operations take 89% of the tests. *Upload mission*, the step that sends a flight plan to a real aircraft, gets 5 runs in 1,000: a rare, critical operation that needs tests of its own.

**Where the inputs come from.** On a deployed system, rates come from the application's own event log, counted per operation and per hour of use, as on [Worked Case: A Profile from an Access Log](worked-case.md).

## When to use it — and when not

- **Use the case** as a model for a new interactive application, where no field data exists but a prototype can be instrumented.
- **Do not reuse its numbers**: they describe one prototype and its test operators, not a fielded system.

## Limitations

Rates recorded on a prototype reflect how test operators used an early interface, not how pilots will use the released one. The merge is a judgement; the source does not show the data behind it.

## How solid is this?

- **Where it comes from.** This is teaching case material from Behrouz Far's software reliability engineering course at the University of Calgary; we found no published paper behind it. The profile method follows Musa {% cite musa2004sre %}.
- **What is contested.** The source shows the merged rates without the calculation, and the label "more open" stands for a slide's "> 1 mission", which probably means one or more.
- **What we do not hold.** The prototype's session logs, or how many sessions the rates were averaged over.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
