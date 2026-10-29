---
title: "SN A01: Cost of Quality"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 1
redirect_from: /content/organization/01-coq/SN-A1-CoSQ.html
layout: default
page_type: study-notes
---

# SN A01: Cost of Quality

This lecture asks how much quality costs and where the money should go. It answers by sorting all quality-related effort into four categories, following defects from the phase that creates them to the activity that removes them, and showing that early removal usually costs less than late removal. The second half applies the same lens to modern practice: classification schemes, continuous delivery, code review, technical debt and defect prediction.

## 1. Poor quality is a large, mostly hidden cost

When manufacturers first measured quality-related costs, they found 10 to 30 percent of sales, far more than their accounts showed, and most of it avoidable {% cite miranda2013coq %}. Software is worse. Houston and Keats report a **cost of software quality (CoSQ)** of 20 to 70 percent of development cost, roughly twice the manufacturing figure; organizations that had not started process improvement spent 60 percent and more {% cite houston1999cost %}. At national scale, NIST estimated that an inadequate infrastructure for software testing cost the US economy USD 59.5 billion a year, about 60% of it borne by users, and that feasible improvements could save USD 22.2 billion {% cite tassey2002economic %}. Expressing quality in money is what lets a quality engineer argue with managers in their own unit.

→ [Cost of Software Quality](../../organization/01-coq/01-cosq-foundations.md)

## 2. Four categories separate achieving quality from failing

CoSQ has four categories {% cite houston1999cost knox1993modeling %}:

| Category | Definition | Software examples |
|---|---|---|
| Prevention | efforts to ensure product quality | SQA administration, inspections, process improvement, metrics |
| Appraisal | discovering the condition of the product | testing, product quality audits |
| Internal failure | failures found before any user meets them | defect management, rework, retesting |
| External failure | failures users meet | support, complaints, fixes, lost sales |

Prevention and appraisal are the **cost of achieving quality** (also called conformance); the two failure categories are the **cost of failing** (nonconformance). The categories separate activities that look alike. Running a test is appraisal: it discovers the product's condition. Debugging the failure it exposes, fixing it and retesting is internal failure: that effort exists only because a defect does. "Testing takes too long" often means "fixing takes too long". A cost is **external** when it arises after release to users (support, incidents, rollback of shipped code, customer harm), **internal** when it arises before.

The categories are a **booking convention**. This course, following Houston and Keats and Knox, books reviews and inspections as **prevention**: they stop defects before testing, and a work product is not complete until reviewed. Many other CoQ texts book them as appraisal, because a review examines the product; either is fine if a team applies one rule consistently {% cite houston1999cost knox1993modeling %}. Rework caused by an inspection finding is internal failure. Automated checks such as static analysis are appraisal here, or booked with reviews if a team always does so.

**Example.** A release of 1,000 hours has 40 hours of reviews and 20 of training (prevention 60), 200 of testing (appraisal), 150 of debugging and fixing (internal failure) and 90 of fixing customer-reported defects (external failure). CoSQ is 500 hours, half the release, and failure (240) is four times prevention (60). Booking the reviews as appraisal leaves CoSQ and failure unchanged but makes failure twelve times prevention (240 / 20): mixing rules between releases fakes a trend.

→ [Cost of Software Quality](../../organization/01-coq/01-cosq-foundations.md)

## 3. Process maturity moves effort from failure to prevention

Knox mapped the categories onto the five levels of the Capability Maturity Model. His model predicts total CoSQ falling by about two-thirds, from about 60 percent of development cost at Level 1 to about 20 percent at Level 5 {% cite knox1993modeling %}. Measured data point the same way: after a process improvement programme started in 1988, Raytheon's total CoSQ fell from 55–67 percent of development cost at Level 1 to about 15 percent, and rework fell fourfold, to under 10 percent of development cost {% cite houston1999cost %}.

The **traditional** cost-of-quality model puts an optimum short of perfection: past some point, preventing the last defects costs more than the failures they avoid; in manufacturing it lies at 95–100% conformance. Knox's **emerging** model argues that software shows no point of diminishing returns yet {% cite knox1993modeling %}. Houston and Keats simulated an 80,000-line project: adding inspections lowered the minimum total CoSQ from 3,500 to 2,900 hours and moved it from 2.7 to 1.6 defects per thousand lines {% cite houston1999cost %}. A better process does not just save money at the same quality; it moves the cheapest point towards higher quality.

→ [Cost of Software Quality](../../organization/01-coq/01-cosq-foundations.md)

## 4. Defects flow through a project like water through a tank

The **tank-and-pipes** model treats each development phase as a pipe pouring defects into a tank and each verification activity as a pipe draining them; what is left at release are the **residual defects** {% cite miranda2013coq %}. Two numbers describe the pipes:

- **Injection rates.** COQUALMO uses nominal rates of 10, 20 and 30 defects per thousand source lines for requirements, design and code {% cite chulani1999coqualmo %}, though fitted projects needed far lower rates, 0.5 to 6.1 {% cite houston2009coqualmo %}.
- **Defect removal efficiency (DRE)**: the fraction of present defects an activity removes. Knox's typical values: formal inspections 65%, informal reviews 45%, unit and system testing 25–50% each, beta testing 25% {% cite knox1993modeling %}.

Plotting cumulative injected and removed defects by phase gives the **process signature**. In Miranda's baseline, 80 defects reach testing, three test levels each remove 50%, and 80 × 0.5³ = 10 ship.

→ [Defect Injection and Removal](../../organization/01-coq/02-defect-injection-removal.md)

## 5. Early removal wins when defects multiply

Miranda's example assumes that each development phase **doubles** the defects it receives (10 after requirements, 20 after high-level design, 40 after low-level design, 80 after code), because a wrong requirement produces wrong design and wrong code {% cite miranda2013coq %}. Two improvements are compared:

- **Option 1**, unit testing from 50% to 70%: 80 × 0.3 × 0.5 × 0.5 = **6** defects ship; testing removes 74.
- **Option 2**, 20% inspections after requirements, high-level design and code: requirements 10 → 8 after inspection; high-level design doubles that to 16 → 12.8; low-level design, not inspected, doubles to 25.6; code doubles to 51.2 → 40.96, which reach testing, and 40.96 × 0.5³ = **5.12** ship. The inspections remove only 15.44 defects, but because each early removal stops a doubling, only 56.4 defects are ever created instead of 80.

Both options roughly halve the shipped defects. Which is cheaper depends on the cost of an inspection hour against a test-and-fix hour, and on how likely each option is to reach its assumed efficiency. The model's value is to make that comparison before paying for either.

→ [Defect Injection and Removal](../../organization/01-coq/02-defect-injection-removal.md)

## 6. Classifying defects by origin and detection makes CoQ actionable

The minimum classification records, for every defect, its **origin** (the phase that introduced it) and **where it was found**. The data come from time reports (appraisal and rework hours), the bug tracker (counts and escapes) and the classification itself {% cite miranda2013coq %}. Kan's project recorded 3,465 defects; from the margins of its origin / where-found matrix, the DRE of each activity follows {% cite kan2002metrics miranda2013coq %}:

DRE = found by the activity / (introduced so far − found earlier)

In Kan's project, requirements, high-level design and low-level design introduced 122, 859 and 939 defects; the high-level design inspection found 730 and the low-level design inspection 729. So for the low-level design inspection: 729 / ((122 + 859 + 939) − 730) = 729 / 1,190 = 61.3%. Overall, only 81 of 3,465 defects reached the field: 97.7% were removed before release, most of them by inspections, while unit testing removed only 36.7% of what reached it.

→ [Defect Origin and Detection](../../organization/01-coq/03-defect-classification.md)

## 7. Richer schemes add triggers, types and cost weights

**Orthogonal Defect Classification (ODC)** records non-overlapping attributes: activity, trigger and impact when a defect is found; target, defect type, qualifier, source and age when it is fixed. At one IBM product, ODC showed most field escapes coming from function verification test, mostly with the *variation* trigger, so the team added variation test cases {% cite butcher2002improving %}. HP's scheme records where a defect **could have been prevented**. Huber classified the same high-priority defects with both: HP attributed 63% to design, while ODC's *target*, the work product that was changed, pointed mostly at code; the difference is where a defect was fixed against where it could have been prevented. HP also weights defects by origin: specification 14.25, design 6.25, code 2.5, because a specification defect forces changes to the specification, the design and the code {% cite huber1999comparison %}.

→ [Defect Classification Schemes](../../organization/01-coq/04-classification-schemes.md)

## 8. Continuous delivery prices quality in new places

The same four categories apply to pipelines: tests in continuous integration are appraisal, investigating a red pipeline is internal failure. Leinen et al. measured flaky tests (tests that pass or fail on unchanged code) in a project of about 30 developers: at least 2.5% of developer time went on them, and investigating one failed pipeline cost USD 5.67 against 0.02 cents for an automatic rerun, so the company raised the number of reruns {% cite leinen2024flaky %}. The risk: a test that fails intermittently because of a real defect, such as a race condition, is not flaky, and rerunning it until green hides that defect.

→ [Quality Costs in Continuous Delivery](../../organization/01-coq/04-quality-economics-devops.md)

## 9. Reviews pay when they find defects and propose fixes

In the OpenDev community, comments that find functional defects were rated most useful but made up only 0.48% of sampled comments; a third concerned documentation {% cite turzo2023useful %}. In OpenStack and Qt, security defects raised in review were fixed in 81.3% of cases when the reviewer proposed a solution, against 59.5% when the reviewer only pointed out the problem {% cite yu2023security %}. Reviewers also catch potential technical debt before merge; 81.0% of it was resolved there {% cite fu2022ptd %}.

→ [Value of Code Review](../../organization/01-coq/05-code-review-roi.md)

## 10. Debt tools disagree, and accuracy misleads

Technical debt's **principal** is the effort to remove the inefficiencies; its **interest** is the extra maintenance paid while they stay {% cite amanatidis2020td %}. The source does not assign these to CoSQ categories; this course books both as internal failure, since both are development effort spent only because a flaw is in the code, and no user has met a failure; the test is whether users met the failure, not whether the code has shipped. An incident the debt causes in production is external failure; keeping new debt out in review is prevention. Three debt tools agreed strongly on Java projects (Kendall's W = 0.777) and only moderately on JavaScript (0.647), so the classes all tools rank high are the safest place to start repayment {% cite amanatidis2020td %}. **Defect prediction** flags modules likely to be defective; on imbalanced data, accuracy misleads: on jEdit a model reached 0.97 accuracy while finding no defective module at all (precision, the share of flagged modules that are defective, and recall, the share of defective modules flagged, both 0.00) {% cite khleel2023bilstm %}.

→ [Technical Debt as a Quality Cost](../../organization/01-coq/06-technical-debt.md) · [Defect Prediction with Machine Learning](../../organization/01-coq/07-defect-prediction-ml.md)

## Where this goes next

The cost categories and removal efficiencies are the inputs to the next questions: how to improve the process that injects the defects (A02) and how to plan which quality activities one project will run, when and at what cost (A04, quality planning). The revision questions for this lecture are in [RQ A01: Cost of Quality](RQ-A1-CoSQ.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
