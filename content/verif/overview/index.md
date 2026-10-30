---
title: Verification Overview
parent: Verification and Validation
nav_order: 1
layout: default
has_children: true
redirect_from:
  - /content/verif/overview/inspection.html
---

# Verification Overview

**Verification** is the work of showing that a product meets its specified requirements: *did we build the product right?* **Validation** shows that it serves its intended purpose for its users: *did we build the right product?* {% cite nasa2016sehandbook %} Both use the same four methods: inspection, analysis, testing and demonstration. None of them proves software correct in general; each gives evidence of a different kind, and each misses faults that another finds {% cite zheng2006static %}.

This section is the map. It says what each method is, where it fits and what it cannot do, and links to the areas that hold the detail.

| Method | What it finds | Best fit | Main cost | Where to read more |
|---|---|---|---|---|
| **Inspection** | faults visible in the text: omissions, wrong logic, unclear design | requirements, designs and code before they run | several people's reading time | [Four Verification Methods](techniques.md), [Inspection](../inspection/index.md) |
| **Analysis** | violations of a property on every path: type errors, unsafe calls, deadlocks | rules that must hold everywhere; concurrency | false alarms, or building a model | [Verification by Analysis](analysis/index.md) |
| **Testing** | failures on the inputs that were run | behaviour, interfaces, regressions | writing and maintaining tests and their expected results | [Testing Overview](testing/index.md) |
| **Demonstration** | whether the product does the job in use | acceptance, usability, operation in the real environment | access to users and a realistic environment | [Four Verification Methods](techniques.md) |

Inspection and analysis do not run the product; testing and demonstration do. Inspection and demonstration rest on what people observe; analysis and testing on calculation or measured data. The [Four Verification Methods](techniques.md) page explains this split with one software example per method.

The detail areas each go deeper into one method: [static analysis](../static/index.md) and [inspection](../inspection/index.md) for the methods that do not run the code; [coverage criteria](../coverage/index.md), [input domain testing](../domain/index.md), [random testing](../random/index.md), [combinatorial testing](../combinatorial/index.md) and [exploratory testing](../exploratory/index.md) for ways of choosing tests.

---

### References

{% bibliography --cited %}

### Acknowledgments

The lectures behind this area took early inspiration from the *Analysis of Software Artifacts* course of Eduardo Miranda and Claire Le Goues (Carnegie Mellon University). The pages here are original, written from the literature review; any errors are ours.

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
