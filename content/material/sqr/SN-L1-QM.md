---
title: "SN L01: Quality Models"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 1
redirect_from: /content/define/foundations/SN-L1-QM.html
layout: default
page_type: study-notes
---

# SN L01: Quality Models

The lecture asks *"what is software quality, and how do we make it specific enough to build and test?"* It shows why people disagree about quality, introduces quality models, follows them from McCall to ISO/IEC 25010, turns one into testable requirements, and ends with the quality pioneers. The [revision questions](RQ-L1-QM.md) test this.

## 1. People mean five different things by quality

Garvin found five views of quality in the literature {% cite garvin_what_1984 %}; §2 sets two of them aside for software:

| View | Quality is… | Who said it |
|---|---|---|
| **Transcendent** | excellence you recognise but cannot define | Pirsig |
| **Product-based** | more of a desired attribute | Abbott |
| **User-based** | fitness for use: satisfying the user's wants | Juran |
| **Manufacturing-based** | conformance to requirements | Crosby |
| **Value-based** | excellence at an acceptable price | Broh |

The same release can score high in one view and low in another. A banking app with zero open defects against its specification is high quality in the manufacturing view; if users need six taps to make a transfer, it is low quality in the user view. Garvin also split product quality into eight dimensions (performance, features, reliability, conformance, durability, serviceability, aesthetics, perceived quality); firms compete on a few, Steinway pianos on tone, Yamaha on reliability and conformance.

→ [Views of Quality](../../define/foundations/views.md)

## 2. Most projects run on a blend, but one view dominates

Three views are practical for software; the product-based view is set aside as a general definition because "more is better" holds only for some attributes (Kano's one-dimensional ones, where more always satisfies more, §10), and the transcendent view because it cannot be tested. The user-based view is checked by **validation**, here meaning checking with users that the product fits their use; the manufacturing-based view by verification against the specification; the value-based view asks whether quality is worth its cost. Which view dominates is a judgement about a project: a team that accepts work by customer demo, as in Scrum or XP, leans on the user view; a team audited against a process standard such as CMMI (a model that rates an organisation's processes on maturity levels), or one whose product a regulator such as the FDA must approve, leans on the manufacturing view; a start-up shipping weekly to learn leans on the value view. The FDA case is not the user view: in regulation, *validation* has its second meaning, showing conformance to a mandated specification and process. The start-up ships known defects when fixing them would cost more than the lesson.

→ [Views of Quality](../../define/foundations/views.md)

## 3. A quality model replaces "I know it when I see it"

A **quality model** is a defined set of quality characteristics, and of the relationships between them, used to specify quality requirements and to evaluate whether a product meets them {% cite iso25010_2023 %}. Every model has the same shape: broad **characteristics** split into **subcharacteristics**, each tied to a **measure** with a scale and a counting method. For example, "the payment service must be reliable" becomes: characteristic reliability, subcharacteristic recoverability, measure mean time to restore service in minutes. The model stops at the measure; the threshold, 5 minutes, belongs to the requirement: "While in production, measured over each quarter, the payment service shall restore service after a crash within a mean of 5 minutes." A mean suits restore time because crashes are few and each restore counts; percentiles need many events.

Measures are **internal** when taken on code without running it (complexity, coupling) and **external** when taken on the running system (response time, failure rate). Models link the two because internal measures are cheap and available early, so a team can use them to predict external quality before users see it. 

→ [What Is a Quality Model?](../../define/foundations/quality-models.md)

## 4. Quality assurance builds confidence; quality control checks products

ISO 9000 defines the activities of quality management {% cite iso9000_2015 %}. **Quality planning** sets objectives and processes; **quality assurance** provides confidence that quality requirements will be fulfilled; **quality control** checks work products and corrects them; **quality improvement** increases the ability to fulfil requirements. A review of one pull request is quality control; an audit showing that every pull request is reviewed is quality assurance.

→ [What Is a Quality Model?](../../define/foundations/quality-models.md#3-words-for-quality-work)

## 5. McCall linked what users see to what developers build

McCall, Richards and Walters built the first software quality model in 1977 for the US Air Force {% cite mccall_factors_1977 %}. It has 11 **factors** in three groups:

| Group | Factor | McCall's definition |
|---|---|---|
| Product operation: how well does it run? | Correctness | extent to which a program satisfies its specification and the user's objectives |
| | Reliability | extent to which it performs its intended function with required precision |
| | Efficiency | computing resources and code required to perform a function |
| | Integrity | extent to which access by unauthorized persons can be controlled |
| | Usability | effort to learn, operate, prepare input and interpret output |
| Product revision: can we change it? | Maintainability | effort to locate and fix a defect |
| | Flexibility | effort to modify an operational program |
| | Testability | effort to test that it performs its intended functions |
| Product transition: can we move it? | Portability | effort to transfer it to another hardware or software environment |
| | Reusability | extent to which parts can be reused in other applications |
| | Interoperability | effort to couple it with another system |

Factors are **external** in McCall's sense: what users experience, not directly measurable; the external measures of §3 are how such factors are later observed on the running system. McCall tied each to **criteria**, 23 internal properties of the software, and each criterion to **metrics**: yes/no checklist questions, scored as the share of "yes" answers {% cite naik2008software %}. For modularity: "does this module implement one function?"; 30 of 40 modules give 0.75, and criterion scores are averaged into a factor score. Maintainability, the "effort required to locate and fix a defect", rests on consistency, simplicity, conciseness, self-descriptiveness and modularity; testability rests on simplicity, instrumentation, self-descriptiveness and modularity. Three criteria serve both factors, so the model is not orthogonal. Security sits under integrity.

→ [McCall's Quality Model](../../define/foundations/mccall.md)

## 6. Quality characteristics trade off against each other

Perry classified each pair of McCall's factors as **direct** (improve together), **inverse** (one worsens the other) or **neutral** {% cite perry_effective_1988 %}. Efficiency loses to almost everything: improving most other factors adds code that does not compute the answer, so it reduces efficiency {% cite naik2008software %}. For example, writing an audit record on every balance query (integrity) adds 2 ms to an 8 ms query, a 25% rise in latency. Other pairs are direct: correctness and reliability, or maintainability and testability, improve together. Gillies added that relationships need not be symmetric {% cite gillies_software_2011 %}: modular code helps reliability, but a redundant server does nothing for maintainability. The signs are judgement; decide competing requirements together.

→ [Quality Trade-offs](../../define/foundations/tradeoffs.md)

## 7. ISO/IEC 25010 is today's shared vocabulary

ISO/IEC 9126 (2001) and 25010:2011 came between McCall and today. The current **ISO/IEC 25010:2023** has nine product characteristics {% cite iso25010_2023 %}: functional suitability, performance efficiency, compatibility, interaction capability, reliability, security, maintainability, flexibility and safety. The 2023 edition added safety, renamed usability (now interaction capability) and portability (now flexibility), and moved quality in use to ISO/IEC 25019. A **plain renaming** keeps the content under a new name; identical names are not counted. Three ISO names are plain renamings: functional suitability, performance efficiency and interaction capability are McCall's correctness, efficiency and usability. Interaction capability counts: it keeps McCall's interface properties, while usability as an outcome moved to quality in use. Flexibility is roughly his portability plus scalability. Two collect content McCall kept inside differently named factors: security (his integrity) and compatibility (his interoperability). Safety has no counterpart. Words changed meaning too:

| McCall factor | Home in ISO/IEC 25010:2023 | Note |
|---|---|---|
| Correctness | functional suitability (functional correctness) | |
| Reliability | reliability | |
| Efficiency | performance efficiency | product time and resources, not the user's effort (quality-in-use efficiency) |
| Integrity | security | ISO *integrity* is a security subcharacteristic |
| Usability | interaction capability | a prerequisite for usability, now under beneficialness (ISO/IEC 25019) |
| Maintainability, testability, reusability | maintainability | |
| Flexibility | maintainability (modifiability) | **names swap:** ISO's *flexibility* is McCall's portability plus scalability |
| Portability | flexibility | **names swap** |
| Interoperability | compatibility (interoperability) | |
| no counterpart | safety | |

Subcharacteristics worth knowing: **interaction capability** (appropriateness recognizability, learnability, operability, user error protection, user engagement, inclusivity, user assistance, self-descriptiveness), **performance efficiency** (time behaviour, resource utilization, capacity), **reliability** (faultlessness, availability, fault tolerance, recoverability) and **security** (confidentiality, integrity, non-repudiation, accountability, authenticity, resistance). Use the model as a **checklist**: walk all nine, keep those that yield requirements, and write down why the others were dropped; a food-delivery app drops safety: it controls nothing that can harm anyone.

→ [ISO/IEC 25010 Product Quality](../../define/foundations/iso25010.md)

## 8. A quality requirement has a condition, a measure and a threshold

A testable quality requirement follows a template:

```text
While [condition, with any measurement window], the [component] shall [measure] [threshold].
```

Thresholds come from the current baseline, competitors, what users expect, a service-level agreement, or the cost of the next nine: each extra 9 of availability (99.9% → 99.99%) costs more than the last.

"The system shall be fast and reliable" has no condition, no measure and no threshold, so no test can fail it. Split it into one requirement per characteristic: "While up to 1,000 concurrent users are active, the search API shall respond within 200 ms for at least 95% of requests" and "During business hours (condition), measured per calendar month (window), the order service (component) shall be available (measure) at least 99.9% of the time (threshold)." The **95th percentile** (**p95**) of response time is the value that 95% of requests stay under. If 98% of requests take 70 ms and 2% take 5 s, the mean is 0.98 × 70 + 0.02 × 5,000 ≈ 169 ms, which no request takes; p95 = 70 ms; the 5 s tail shows only at p99 = 5 s. So a requirement names the percentile that matters, and adds p99 when the tail matters: "…within 200 ms for 95% of requests and within 1 s for 99%".

Requirements are best derived from the model in six steps {% cite firesmith2008mfesa %}: (1) select the relevant characteristics, (2) read the context (operations, data volumes, peak users, interfaces), (3) state a quality goal per characteristic, (4) choose a measure, (5) write the requirement with a threshold, (6) check that it is testable, feasible and complete, and loop back if not. 

→ [Writing Quality Requirements](../../define/foundations/requirements.md)

## 9. Product quality is not enough: quality in use and process quality

**Quality in use** is the degree to which specific users achieve specific goals in a specific context of use. ISO/IEC 25010:2011 named five characteristics: **effectiveness** (e.g. 90% of first-time users complete a booking without help), **efficiency** (a returning user books in under 2 minutes), **satisfaction**, **freedom from risk** (no booking charged twice) and **context coverage** (the targets hold on a phone) {% cite iso25010 %}. ISO/IEC 25019:2023 now regroups them as beneficialness (which holds usability), freedom from risk and acceptability {% cite iso25019_2023 %}. For the project, use the more concrete 2011 five. The same completion rate is product quality (learnability) when measured in a controlled test, and quality in use (effectiveness) when measured with real users in their own context.

**Process quality** is the quality of how the product is built. Kroeger and Davidson grouped it into suitability, usability, manageability and evolvability, here describing the process {% cite kroeger2009quality %}. A good process tends to produce good products but cannot guarantee them: a sound process can still build the wrong thing. The three form a chain: process quality influences product quality, which influences quality in use.

→ [Quality in Use and Process Quality](../../define/foundations/quality-in-use.md)

## 10. The quality pioneers supplied the ideas the models use

Shewhart separated common-cause from special-cause variation {% cite shewhart1931economic %} and started the improvement cycle that **Deming** spread as **Plan–Do–Check–Act**; Deming's 14 points include "cease dependence on inspection" ("build quality in") {% cite deming1986crisis %}. **Juran** defined quality as fitness for use and management as a trilogy of planning, control and improvement {% cite juran1986trilogy %}. **Crosby** defined quality as conformance to requirements and argued that doing it right first time is cheaper ("Quality Is Free") {% cite crosby1979quality %}. **Ishikawa** gave teams the fishbone (cause-and-effect) diagram and the seven basic tools {% cite ishikawa1985tqc %}, used in post-incident reviews. **Humphrey** applied process maturity to software, which became the CMM, later CMMI {% cite humphrey1989managing %}.

**Kano** classified attributes by how satisfaction responds {% cite kano1984attractive %}: a missing **must-be** attribute dissatisfies and its presence adds nothing (a streaming app resumes where you stopped); a **one-dimensional** attribute satisfies in proportion (picture quality); an **attractive** attribute delights and is not missed (skip the intro). Attractive features drift to must-be as users get used to them.

→ [Quality Pioneers](../../define/foundations/gurus.md) · [Kano Model](../../define/foundations/kano.md)

## Where this goes next

The next lecture, [Measurement](SN_L2_Metrics.md), asks when a measure really stands for a quality characteristic. The course project needs a [quality model for your product](../../define/foundations/project-model.md): keep the relevant ISO/IEC 25010 characteristics, justify those you drop, and write requirements with the template above. It covers product quality; add quality-in-use rows only where user outcomes decide success, with a plan to measure them with real users.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
