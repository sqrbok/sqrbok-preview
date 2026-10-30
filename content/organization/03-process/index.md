---
title: Process Improvement
parent: Organizing Quality
nav_order: 3
layout: default
has_children: true
---

# Process Improvement

**Software process improvement (SPI)** is the deliberate change of how an organization develops software (its technical practices, its structure and its culture) to get better and more predictable results {% cite kuhrmann2016spi %}. It treats a recurring problem as a property of the process that produced it, not of the person who made the last mistake.

## Why it is hard

Most improvement programs do not last. Jeff Pfeffer's "one-eighth rule" estimates that only one organization in eight sees the full benefit of a management practice, because each step from belief to persistence loses half of those still trying {% cite pfeffer1998human %}. Repenning and Sterman explain one mechanism: improvement takes time away from production, so performance first gets worse, and managers under pressure stop before it gets better {% cite repenning2001nobody %}.

## Pages in this section

| Page | What it covers | Best fit | Main cost |
|---|---|---|---|
| [Why Process Improvement?](01-why-process-improvement.md) | motives for SPI, Columbia, Humphrey's credibility gap | making the case to management | none: it is the argument |
| [Learning & Improvement Models](02-learning-improvement-models.md) | single- and double-loop learning, PDCA, Kaizen | framing any improvement step | time to reflect, not just fix |
| [Improvement Methods](03-improvement-methods.md) | choosing between DPP, Theory of Constraints, Six Sigma and retrospectives | picking a method for a problem | learning the method |
| [Defect Prevention Process](05-defect-prevention.md) | IBM's causal analysis of defects | recurring defect types | about 0.5% of a team's effort |
| [Theory of Constraints](06-theory-of-constraints.md) | five focusing steps on the bottleneck | one stage limits throughput | finding the true bottleneck |
| [Six Sigma](07-six-sigma.md) | DMAIC, variation, 3.4 defects per million | measurable, repetitive processes | statistics skills, trained roles |
| [Change Management](04-change-management.md) | worse-before-better, capability trap, two levels of change | keeping an improvement alive | management attention for months |

[Worked Case: An Improvement Proposal with a Pilot](worked-case.md) takes one improvement from baseline data through a piloted DPP with a control team to the decision to institutionalize it.

Maturity frameworks such as CMMI, which define *what* a mature process contains, are in [Quality Standards](../04-standards/index.md); team-level practices such as retrospectives and DevOps are in [Industry Practices](../05-practice/index.md).

## How solid is this?

- **Where it comes from.** IBM's defect-prevention reports, Repenning and Sterman's case studies, and a mapping study of 769 SPI publications {% cite mays1990defect repenning2001nobody kuhrmann2016spi %}.
- **What is contested.** Of 769 SPI papers, 38.2% propose solutions and 34.3% are philosophical, while only 14.8% evaluate an approach; counted separately by topic, 16.4% look for success factors, and failed programs are rarely published {% cite kuhrmann2016spi %}.

---

### References

{% bibliography --cited %}

### Acknowledgments

The lectures behind this area took early inspiration from the *Analysis of Software Artifacts* course of Eduardo Miranda and Claire Le Goues (Carnegie Mellon University). The pages here are original, written from the literature review; any errors are ours.

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
