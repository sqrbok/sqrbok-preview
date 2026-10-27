---
title: Foundations
parent: Quality
nav_order: 2
layout: default
has_children: true
page_type: topic-hub
---

# Foundations

A **quality model** is a defined set of quality characteristics, and of the relationships between them, used to specify quality requirements and to evaluate whether a product meets them; it turns the vague word "quality" into something a team can measure. These pages start from Garvin's observation (1984) that people mean five different things by quality, go back to McCall's 1977 model, the first for software, and end with the ISO/IEC 25010 standard used today.

## The models at a glance

The models differ in what they decompose quality into and in who they were written for.

| Model | Year | Decomposes quality into | Best for | Page |
|---|---|---|---|---|
| Garvin's views and dimensions | 1984 | 5 views of what quality is; 8 product dimensions | explaining why stakeholders disagree | [Views of Quality](views.md) |
| McCall's model | 1977 | 11 factors → 23 criteria → metrics | seeing how code properties support user-visible qualities | [McCall's Quality Model](mccall.md) |
| ISO/IEC 25010 | 2011, revised 2023 | 9 product characteristics, each with subcharacteristics | a shared checklist for requirements and evaluation | [ISO/IEC 25010 Product Quality](iso25010.md) |
| Quality in use (ISO/IEC 25019) and process quality (Kroeger and Davidson) | 25019: 2023; Kroeger: 2009 | outcomes for users; properties of the process | judging quality as users experience it, and the process behind it | [Quality in Use and Process Quality](quality-in-use.md) |
| Kano model | 1984 | must-be, one-dimensional, attractive attributes | deciding which features raise satisfaction | [Kano Model](kano.md) |

The rows follow the reading order below, not the order of publication.

## Reading order

Start with [Views of Quality](views.md), then [What Is a Quality Model?](quality-models.md), which gives the shared vocabulary (characteristic, subcharacteristic, measure). [McCall's Quality Model](mccall.md) and [Quality Trade-offs](tradeoffs.md) show the first model and its main lesson: characteristics pull against each other. [ISO/IEC 25010 Product Quality](iso25010.md) and [Quality in Use and Process Quality](quality-in-use.md) give the current standard, and [Writing Quality Requirements](requirements.md) and [A Project Quality Model](project-model.md) show how to use it. [Kano Model](kano.md) and [Quality Pioneers](gurus.md) close with the customer's view and the people whose ideas the models borrow.

## How solid is this?

- **Where it comes from.** Garvin's article, McCall's report as summarised by Naik and Tripathy, and the ISO/IEC 25010:2023 text {% cite garvin_what_1984 mccall_factors_1977 naik2008software iso25010_2023 %}.
- **What is contested.** No quality model is complete or orthogonal: each reflects the concerns of the people who wrote it, and the same property can sit under different characteristics in different models.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
