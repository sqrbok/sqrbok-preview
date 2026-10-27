---
title: Do Code Metrics Predict Quality?
parent: Measurement
nav_order: 9
layout: default
vegalite: true
page_type: deep-dive   # the area's evidence page: each study needs its setup and numbers stated
---

# Do Code Metrics Predict Quality?

**Code metrics predict quality only partly, and less than a single headline figure suggests.** They rank classes by risk better than chance, but much of their signal is size, and the accuracy figures reported for metric-based predictors are often inflated by how rare faulty classes are. This page collects the evidence behind that verdict.

## 1. Much of the signal is size

Large classes score high on almost every metric and also contain more faults. In two C++ systems studied by El Emam and colleagues, controlling for class size removed the association with faults for every CK metric in one system {% cite elemam2001confounding %}, and in the other, also C++, only 4 of 24 object-oriented metrics stayed associated with faults, and only 2, both coupling metrics, improved prediction {% cite elemam1999validation %}. The practical rule: before believing that a metric predicts faults, check that it predicts them better than lines of code alone; [Reading Metric Data](statistics.md) works through how to compare within size bands.

## 2. Accuracy misleads when faulty classes are rare

Most classes are fine, so a predictor that always answers "fine" is almost always right. Count a flagged class that really is faulty as a **true positive** (TP), a flagged class that is fine as a **false positive** (FP), a faulty class left unflagged as a **false negative** (FN), and a fine class left unflagged as a **true negative** (TN). Here *accuracy* and *precision* describe a classifier. They are not the accuracy and precision of a single measurement in [Measurement Definitions](definitions.md), though the words are the same. Five measures follow:

- **Accuracy** = (TP + TN) / all classes: the share classified correctly.
- **Precision** = TP / (TP + FP): of the classes flagged, the share that really are faulty. If nothing is flagged, it is 0/0 and undefined.
- **Recall** = TP / (TP + FN): of the faulty classes, the share that were flagged.
- **F1** = 2 · precision · recall / (precision + recall): high only when both are.
- **True negative rate** (TNR) = TN / (TN + FP): of the fine classes, the share left unflagged.


**Example.** A system has 1,000 classes, 10 of them faulty. A predictor that flags nothing is 99% accurate, with recall 0 and no defined precision. A predictor that flags 100 classes, including all 10 faulty ones, has recall 100%, precision 10%, F1 = 2 · 0.10 · 1.00 / 1.10 ≈ 18%, and accuracy 91%: it is *less* accurate than doing nothing, yet far more useful.

This is the situation of real code smells. Pecorelli and colleagues studied five smells over 125 releases of 13 open-source systems. The God Class smell, a class that centralises too much behaviour, never exceeded about 1% of a system's classes: at most 24 of 2,220 classes (1.1%) in one Apache Derby release. A constant predictor that marks every class clean made errors on about 1% of classes, fewer than the machine-learning model trained on the metrics (1–2%) {% cite pecorelli2019comparing %}. The rule-based detector DECOR found every God Class (recall 100%), but only 8% of the classes it flagged were God Classes. Across the five smells, the highest F1 scores were 41% for the machine-learning model (Naive Bayes) and 44% for DECOR {% cite pecorelli2019comparing %}.

The same trap appears in fault prediction. Aziz and colleagues tested whether adding inheritance metrics to the CK suite improves a neural-network fault predictor on public datasets {% cite aziz2019inheritance %}. The added metrics count inherited methods (NOMI) and attributes (NOAI), immediate base classes (IFANIN), inheritance coupling (IC) and functional abstraction (MFA); fan-in and fan-out count calls into and out of a class:

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container", "height": 230,
  "data": {"values": [
    {"set": "CK only", "m": "Accuracy", "v": 69.3}, {"set": "CK only", "m": "F1", "v": 69.5}, {"set": "CK only", "m": "TNR", "v": 68.8},
    {"set": "CK + IC, MFA", "m": "Accuracy", "v": 75.3}, {"set": "CK + IC, MFA", "m": "F1", "v": 50.5}, {"set": "CK + IC, MFA", "m": "TNR", "v": 79.1},
    {"set": "CK + NOMI, NOAI, IFANIN", "m": "Accuracy", "v": 82.6}, {"set": "CK + NOMI, NOAI, IFANIN", "m": "F1", "v": 84.6}, {"set": "CK + NOMI, NOAI, IFANIN", "m": "TNR", "v": 82.2},
    {"set": "CK + fan-in, fan-out, NOMI, NOAI", "m": "Accuracy", "v": 91.3}, {"set": "CK + fan-in, fan-out, NOMI, NOAI", "m": "F1", "v": 63.9}, {"set": "CK + fan-in, fan-out, NOMI, NOAI", "m": "TNR", "v": 96.3}
  ]},
  "mark": {"type": "bar", "cornerRadiusEnd": 3},
  "encoding": {
    "y": {"field": "set", "type": "nominal", "title": null, "sort": null, "axis": {"labelLimit": 260}},
    "yOffset": {"field": "m", "sort": ["Accuracy", "F1", "TNR"]},
    "x": {"field": "v", "type": "quantitative", "title": "%", "scale": {"domain": [0, 100]}},
    "color": {"field": "m", "type": "nominal", "title": null, "sort": ["Accuracy", "F1", "TNR"],
              "scale": {"range": ["#019546", "#b42318", "#81c784"]}, "legend": {"orient": "bottom"}}
  },
  "config": {"font": "Tahoma", "axis": {"labelFontSize": 12, "titleFontSize": 13}, "legend": {"labelFontSize": 13}}
}
```

_Average results per metric set. TNR (true negative rate): share of fault-free classes correctly left unflagged. Data: Aziz et al., Table 7._ {% cite aziz2019inheritance %}

The headline result, accuracy rising from 69.3% to 91.3%, comes from the last set. But its F1 *fell*, from 69.5% to 63.9%, while its true negative rate rose to 96.3%: the gain came mostly from labelling fault-free classes correctly. The set with the best balance was a different one, at 82.6% accuracy and 84.6% F1. Compared dataset by dataset, inheritance + CK beat CK alone in 26 of 30 datasets on accuracy and 25 of 30 on F1 {% cite aziz2019inheritance %}: adding inheritance metrics helps, but by less than the headline number.

## 3. Learned detectors do not escape the problem

Machine learning is now the most common way to turn metrics into smell or fault predictions. A survey of 42 studies found decision trees used in 27 of them (64%), and accuracy the most often reported measure {% cite yadav2024codesmell %}: the measure least able to show the imbalance problem above.

## 4. The metrics still apply to generated code

LOC, cyclomatic complexity and Halstead's metrics work unchanged on code written by large language models, and they show differences that pass rates hide. On 72 Python tasks, code generated by GPT-4 passed 87.3% of the tests against 54.9% for human-written code, yet its average cyclomatic complexity was 5.0 against 3.1, 61% higher {% cite licorish2025llmcode %}. Across 2,405 generated Python files, the prompting style changed code size but not complexity: chain-of-thought prompts gave a median of 7 source lines against 11 for few-shot prompts, while cyclomatic complexity and Halstead values showed no significant difference {% cite dellaporta2025llm %}. Correctness and complexity are separate attributes; a code generator can improve one and worsen the other.

## How solid is this?

- **Where it comes from.** Two El Emam reports on industrial C++ systems; one large study of real smells {% cite pecorelli2019comparing %}; one fault-prediction experiment on public datasets {% cite aziz2019inheritance %}; a survey {% cite yadav2024codesmell %}; and two recent studies of generated code, one of them an unreviewed preprint {% cite licorish2025llmcode %}.
- **What is contested.** Results vary strongly between systems and datasets, which is why every study above reports ranges or per-dataset wins rather than one number. The 72-task comparison with human code used one model and one set of human solutions.
- **What we do not hold.** A 2025 Google study linking architectural complexity to maintenance effort across many projects, and Gil and Lalouche's 2017 replication of the size effect, are cited in our literature review but were not available in full; they are not used on this page.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
