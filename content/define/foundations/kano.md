---
parent: Foundations
nav_order: 9
title: Kano Model
layout: default
vegalite: true
---

# Kano Model

The **Kano model** classifies product attributes by how customer satisfaction responds when the attribute is present or absent: a missing **must-be** attribute causes dissatisfaction, a **one-dimensional** attribute raises satisfaction in proportion, and an **attractive** attribute delights when present but is not missed when absent {% cite kano1984attractive %}. Noriaki Kano and colleagues published it in 1984.

## 1. Five categories

Kano and colleagues named three main categories and two minor ones {% cite kano1984attractive %}:

- A **must-be** attribute is taken for granted: its absence makes customers dissatisfied, and its presence adds nothing.
- A **one-dimensional** attribute works like a slider: more of it means more satisfaction.
- An **attractive** attribute delights when present and is not missed when absent.
- An **indifferent** attribute changes nothing either way.
- A **reverse** attribute makes some customers less satisfied when present.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container", "height": 280,
  "layer": [
    {"data": {"values": [{"y": 0}]}, "mark": {"type": "rule", "strokeDash": [3, 3], "color": "#9e9e9e"},
     "encoding": {"y": {"field": "y", "type": "quantitative", "scale": {"domain": [-1.1, 1.1]}}}},
    {"data": {"values": [{"x": -0.98, "y": 0.06, "t": "neutral"}]}, "mark": {"type": "text", "align": "left", "fontSize": 12, "color": "#757575"},
     "encoding": {"x": {"field": "x", "type": "quantitative"}, "y": {"field": "y", "type": "quantitative"}, "text": {"field": "t"}}},
    {"data": {"values": [{"x": -1, "y": -1, "c": "one-dimensional"}, {"x": -1, "y": -1.0, "c": "must-be"}, {"x": -1, "y": 0.0, "c": "attractive"}, {"x": -0.75, "y": -0.75, "c": "one-dimensional"}, {"x": -0.75, "y": -0.77, "c": "must-be"}, {"x": -0.75, "y": 0.02, "c": "attractive"}, {"x": -0.5, "y": -0.5, "c": "one-dimensional"}, {"x": -0.5, "y": -0.56, "c": "must-be"}, {"x": -0.5, "y": 0.06, "c": "attractive"}, {"x": -0.25, "y": -0.25, "c": "one-dimensional"}, {"x": -0.25, "y": -0.39, "c": "must-be"}, {"x": -0.25, "y": 0.14, "c": "attractive"}, {"x": 0, "y": 0, "c": "one-dimensional"}, {"x": 0, "y": -0.25, "c": "must-be"}, {"x": 0, "y": 0.25, "c": "attractive"}, {"x": 0.25, "y": 0.25, "c": "one-dimensional"}, {"x": 0.25, "y": -0.14, "c": "must-be"}, {"x": 0.25, "y": 0.39, "c": "attractive"}, {"x": 0.5, "y": 0.5, "c": "one-dimensional"}, {"x": 0.5, "y": -0.06, "c": "must-be"}, {"x": 0.5, "y": 0.56, "c": "attractive"}, {"x": 0.75, "y": 0.75, "c": "one-dimensional"}, {"x": 0.75, "y": -0.02, "c": "must-be"}, {"x": 0.75, "y": 0.77, "c": "attractive"}, {"x": 1, "y": 1, "c": "one-dimensional"}, {"x": 1, "y": -0.0, "c": "must-be"}, {"x": 1, "y": 1.0, "c": "attractive"}]},
     "mark": {"type": "line", "strokeWidth": 3},
     "encoding": {
       "x": {"field": "x", "type": "quantitative", "title": "Attribute absent  ←  →  fully present", "scale": {"domain": [-1, 1]}, "axis": {"labels": false, "ticks": false}},
       "y": {"field": "y", "type": "quantitative", "title": "Dissatisfied  ←  →  satisfied", "scale": {"domain": [-1.1, 1.1]}, "axis": {"labels": false, "ticks": false}},
       "color": {"field": "c", "type": "nominal", "legend": null,
         "scale": {"domain": ["must-be", "one-dimensional", "attractive"], "range": ["#b42318", "#2D6E2A", "#019546"]}},
       "strokeDash": {"field": "c", "type": "nominal", "legend": null,
         "scale": {"domain": ["must-be", "one-dimensional", "attractive"], "range": [[1, 0], [6, 4], [1, 0]]}}}},
    {"data": {"values": [{"x": -0.12, "y": -0.04, "t": "one-dimensional"}]},
     "mark": {"type": "text", "fontSize": 14, "fontWeight": "bold", "align": "right", "angle": 0, "color": "#2D6E2A"},
     "encoding": {"x": {"field": "x", "type": "quantitative"}, "y": {"field": "y", "type": "quantitative"}, "text": {"field": "t"}}},
    {"data": {"values": [
       {"x": 0.35, "y": -0.3, "t": "must-be", "c": "must-be"},
       {"x": -0.6, "y": 0.22, "t": "attractive", "c": "attractive"}]},
     "mark": {"type": "text", "fontSize": 14, "fontWeight": "bold", "align": "left"},
     "encoding": {"x": {"field": "x", "type": "quantitative"}, "y": {"field": "y", "type": "quantitative"}, "text": {"field": "t"},
       "color": {"field": "c", "type": "nominal", "legend": null, "scale": {"domain": ["must-be", "one-dimensional", "attractive"], "range": ["#b42318", "#2D6E2A", "#019546"]}}}}
  ],
  "config": {"font": "Tahoma", "axis": {"titleFontSize": 13}}
}
```

_The three main categories. The curves show the shape only; the model gives no numbers._ {% cite kano1984attractive %}

## 2. Classifying an attribute with two questions

Kano classified attributes from a pair of questions per attribute: a **functional** question ("how do you feel if the product has it?") and a **dysfunctional** one ("how do you feel if it does not?"), each answered on the same five-point scale: I like it, I expect it, I am neutral, I can live with it, I dislike it {% cite kano1984attractive %}. The pair of answers gives the category through the **evaluation table**: find the row for the answer about presence and the column for the answer about absence {% cite berger1993kano %}.

| Present ↓ / Absent → | Like | Expect | Neutral | Live with | Dislike |
|---|---|---|---|---|---|
| **Like** | Q | A | A | A | O |
| **Expect** | R | I | I | I | M |
| **Neutral** | R | I | I | I | M |
| **Live with** | R | I | I | I | M |
| **Dislike** | R | R | R | R | Q |

_A attractive, O one-dimensional, M must-be, I indifferent, R reverse, Q questionable: the two answers contradict each other, so the response is discarded._

For example, a team asks 50 users of a video-streaming app about "skip the intro". Their answer pairs (present, absent) are: like and neutral, 18; like and live with, 7; like and dislike, 9; neutral and neutral, 11; expect and dislike, 3; like and like, 2. The table turns them into A = 18 + 7 = 25, O = 9, I = 11, M = 3 and Q = 2. The two questionable answers are dropped, and the feature takes its most frequent category: **attractive**, with 25 of 48 valid answers. The same survey gives "resume where I stopped" M = 38, O = 6, I = 4, A = 2, so must-be, and "4K picture" O = 31, A = 9, I = 6, M = 4, so one-dimensional. Whether the top two categories are "close" is a judgement; one rule of thumb is to report both when they differ by fewer than about a tenth of the valid answers, because the most frequent answer can hide two customer segments. Chapman and Callegaro report that category assignment may be unreliable with samples under 200 {% cite chapman2022kano %}, so treat a 50-person survey as a first signal. The team concludes that resume must work before anything else, 4K is worth steady investment, and skip-intro is a cheap way to stand out.

## When to use it — and when not

- **Use** Kano to prioritise features and to set quality targets: must-be attributes need a reliability threshold, such as "resume works in at least 99.9% of sessions", and one-dimensional ones a target on a performance scale.
- **Do not** use one survey forever: categories drift, and yesterday's attractive feature becomes tomorrow's must-be.

## Limitations

Categories hold for a group of customers at a time, not for everyone: a feature can be must-be for professionals and indifferent for casual users, so the most frequent answer can hide two segments. The model says which way satisfaction moves, not by how much.

## How solid is this?

- **Where it comes from.** Kano and colleagues' 1984 paper, in Japanese, introduced the categories and the question pair {% cite kano1984attractive %}.
- **What is contested.** Scoring rules beyond "most frequent category" differ between authors, and Chapman and Callegaro question whether a method built for durable consumer goods fits technology products {% cite chapman2022kano %}; the drift from attractive to must-be is widely observed but rarely measured.
- **What we do not hold.** The original paper's text. The evaluation table is the form popularised by Berger and colleagues {% cite berger1993kano %}, checked against several published reproductions; the streaming survey is constructed.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
