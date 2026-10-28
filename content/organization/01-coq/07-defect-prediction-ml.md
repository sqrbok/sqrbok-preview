---
title: Defect Prediction with Machine Learning
parent: Cost of Quality
nav_order: 8
layout: default
---

# Defect Prediction with Machine Learning

**Defect prediction** trains a classifier on metrics of past code (size, complexity, coupling, change history) to flag the modules most likely to contain defects, so that reviews and tests can be concentrated there {% cite khleel2023bilstm bhutamapuram2022predictive %}. Its value for the cost of quality is appraisal effort saved on modules predicted clean; its risk is the defects in modules predicted clean that nobody then examines.

## 1. Accuracy misleads on imbalanced data

Defect datasets are imbalanced: most modules are clean. A model that predicts "clean" for everything scores high accuracy and finds nothing. Khleel and Nehéz trained a bidirectional LSTM (a neural network that reads a sequence in both directions) on six Java projects. On the original data, accuracy averaged 0.88 but the **F-measure** (the harmonic mean of precision and recall on the defective class) only 0.51. On the jEdit dataset the model reached 0.97 accuracy with precision and recall of 0.00: it never flagged a single defective module correctly {% cite khleel2023bilstm %}.

**Oversampling** adds copies or synthetic examples of the rare class before training. With random oversampling the averages rose to 0.94 accuracy, 0.94 F-measure and 0.96 AUC; with SMOTE, which synthesizes new minority examples, to 0.92, 0.92 and 0.95 {% cite khleel2023bilstm %}.

## 2. Measure what the project gains

Bhutamapuram and Sadam argue that accuracy and F-measure say little about the project. They propose measures computed from the same confusion matrix, among them the **percent of saved budget** (code that testers need not visit because it is correctly predicted clean), the **remaining service time** (hours still needed for defective code the model missed) and the **gratuitous service time** (hours wasted on modules wrongly flagged) {% cite bhutamapuram2022predictive %}.

On four Java projects (Eclipse, Equinox, Mylyn, PDE), self-training changed the averages as follows:

| Measure | Before | After | Change |
|---|---:|---:|---:|
| Accuracy (%) | 86.80 | 85.84 | −0.96 |
| F-measure (%) | 86.92 | 86.80 | −0.12 |
| Percent of saved budget | 65.51 | 65.84 | +0.33 |
| Remaining service time (hours) | 388.36 | 377.63 | −10.73 |
| Gratuitous service time (hours) | 154.66 | 143.93 | −10.73 |

_Averages from Bhutamapuram and Sadam's Table 6._ {% cite bhutamapuram2022predictive %}

By the traditional measures the model got slightly worse; by the project measures it got slightly better: testers could skip about 66% of the code, and both kinds of wasted hours fell.

## Example: choosing between two models

A release has 100 modules, 10 of them defective. Model A flags 5 modules, 3 of them defective; model B flags 25, 9 of them defective. Accuracy, the share of modules classified correctly, is (3 + 88) / 100 = 0.91 for A and (9 + 74) / 100 = 0.83 for B, so accuracy favors A. Now price the errors as the two service-time measures do. Say a defective module the model misses costs 20 hours later, and examining a wrongly flagged clean module wastes 4 hours. A misses 7 defective modules (140 hours remaining) and wastes 2 inspections (8 hours): 148 hours. B misses 1 (20 hours) and wastes 16 inspections (64 hours): 84 hours. For the test manager, B is the better model. _(Constructed example.)_

## When to use it, and when not

- **Use it** to prioritize reviews and tests in a large code base with a defect history; report precision, recall or project measures, never accuracy alone.
- **Do not use it** on a new project without history, or as a reason to skip testing modules predicted clean in safety-critical code.

## Limitations

- **Predictions inherit the history.** A model trained on last year's defects flags last year's kind of code.
- **Oversampling can inflate results.** If copies of defective rows end up in both the training and the test set, the test scores look better than real performance; Khleel and Nehéz report scores on "balanced datasets" without stating whether they oversampled before or after splitting.

## How solid is this?

- **Where it comes from.** The imbalance results from Khleel and Nehéz, the project measures from Bhutamapuram and Sadam {% cite khleel2023bilstm bhutamapuram2022predictive %}.
- **What is contested.** The improvements in section 2 are small; the paper reports them as significant at p < 0.05 for the saved budget and the remaining service time, not for accuracy.
- **What we do not hold.** A study that converts prediction results into money for a real project.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
