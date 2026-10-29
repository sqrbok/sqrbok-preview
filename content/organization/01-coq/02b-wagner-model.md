---
title: "Choosing Defect-Detection Techniques: Wagner's Model"
parent: Cost of Quality
nav_order: 2.5
layout: default
math: katex
---

# Choosing Defect-Detection Techniques: Wagner's Model

**Wagner's model** is an economic model of defect-detection techniques: for each technique and each defect type it estimates the cost of applying the technique, the field costs it saves and the field costs it leaves, so a team can compare techniques and their combinations by expected net return {% cite wagner2006model %}. Unlike [Defect Injection and Removal](02-defect-injection-removal.md), detection depends on the defect type.

## 1. Three cost components

Wagner splits the economics of a technique A into three parts {% cite wagner2006model %}:

- **Direct costs** $$d_A$$: setup $$u_A$$ (tools, test environment), execution $$e_A$$ (test runs, review meetings) and the removal of every fault the technique finds. In [CoSQ terms](01-cosq-foundations.md#1-four-categories), setup and execution are appraisal for tests and prevention for inspections (this handbook's convention), and removal is internal failure.
- **Future costs** $$t_A$$: the field costs of faults the technique misses that then fail at users, external failure.
- **Revenues** $$r_A$$: the field costs the technique saved, because faults it found would otherwise have failed at users.

The model covers what Wagner calls *analytical* quality assurance, techniques that find defects; *constructive* measures such as training are left out. The practical form groups faults into **defect types** such as ODC's ([Defect Classification Schemes](04-classification-schemes.md)), because measuring each fault is impossible {% cite wagner2006model %}. For $$n_\tau$$ faults of type $$\tau$$, a detection probability $$p_A(\tau)$$ (Wagner writes $$1-\theta_A$$, where the *difficulty* $$\theta_A$$ is the probability of missing the fault), removal cost $$v_A(\tau)$$, failure probability $$\pi_\tau$$ and field cost $$c_F(\tau)$$ per failing fault:

$$d_A = u_A + e_A + \sum_\tau n_\tau\, p_A(\tau)\, v_A(\tau) \qquad r_A = \sum_\tau n_\tau\, \pi_\tau\, p_A(\tau)\, c_F(\tau)$$

The future costs are the rest: $$t_A = \sum_\tau n_\tau\, \pi_\tau\, (1-p_A(\tau))\, c_F(\tau)$$. For a sequence of techniques, each later technique sees only the faults the earlier ones missed. The best combination maximises $$r - d$$ {% cite wagner2006model %}.

## 2. Example: inspection, system test, or both?

_(Constructed example.)_ A release has 100 faults: 60 logic faults, of which 30% would fail in the field, and 40 interface faults, of which 60% would. A field failure costs 30 staff-hours, the survey's mean of about 27 hours to remove a field defect plus 3 of support; removal costs 2.7 hours per fault found by code inspection and 8.4 by system test, also survey means {% cite wagner2006survey %}. Detection probabilities are fixed here; in the full model they grow with effort.

| | Code inspection | System test |
|---|---|---|
| Setup + execution (h) | 5 + 35 | 20 + 60 |
| Detection probability: logic / interface | 0.4 / 0.2 | 0.3 / 0.7 |

| Plan | Faults found | Direct costs $$d$$ (h) | Revenues $$r$$ (h) | Net $$r-d$$ (h) |
|---|---:|---:|---:|---:|
| Inspection only | 32 | 126.4 | 360.0 | 233.6 |
| System test only | 46 | 466.4 | 666.0 | 199.6 |
| Inspection, then system test | 65.2 | 485.3 | 860.4 | **375.1** |

Without either technique the expected field cost is (60 × 0.3 + 40 × 0.6) × 30 = 1,260 hours; the combination leaves 1,260 − 860.4 = 399.6. The defect-type mix decides which single technique wins: with 20 logic and 80 interface faults, system test alone nets 461.2 hours against the inspection's 255.2, and the combination 607.4.

## Where the inputs come from

From finished projects: classify each fault by type, record which technique found it or whether users did, and take hours from time reports. Failure probability comes from field data or expert estimate {% cite wagner2006model %}. On a first project the model only ranks options roughly: the survey's averages are per technique, not per type (tests miss about 45% of faults, inspections about 65%), so the team estimates per-type values and replaces them after the first release {% cite wagner2006survey %}.

## When to use it, and when not

- **Use it** to choose which techniques to combine, and in which order, when defect types differ in how they are found and how often they fail.
- **Do not use it** for constructive measures such as training, which it leaves out, or treat its output as a forecast while the per-type values are still estimates.

## Limitations

The model assumes found faults are removed perfectly and effort can be varied freely. Wagner notes it is unclear whether defect types really give stable distributions of removal costs, difficulty and failure probability {% cite wagner2006model %}.

## How solid is this?

- **Where it comes from.** Wagner's model and his literature survey {% cite wagner2006model wagner2006survey %}.
- **What is contested.** The sensitivity analysis ranks effort per technique and field removal costs among the most influential inputs, and the survey's ranges are wide: inspection effectiveness ran from 8.5% to 92.7% {% cite wagner2006survey %}.
- **What we do not hold.** A published application of the practical model to a full industrial project.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
