---
title: Verification and Validation
nav_order: 4
layout: default
page_type: hub
has_children: true
---

# Verification and Validation

**Verification** shows that software meets its specification (*did we build the product right?*); **validation** shows that it serves its users (*did we build the right product?*). This section starts with the map and then gives one area per family of techniques.

| Area | What it covers |
|---|---|
| [Verification Overview](overview/) | the four methods, testing levels, black and white box, what analysis can prove |
| [Inspection](inspection/) | people reading a work product to find defects |
| [Static Analysis](static/) | tools that check code without running it |
| [Coverage](coverage/) | how much of the code a test suite exercises |
| [Input Domain Testing](domain/) | inputs chosen from partitions and boundaries of the specification |
| [Combinatorial Testing](combinatorial/) | covering every combination of *t* parameter values |
| [Random Testing](random/) | sampled inputs, fuzzing, property-based and mutation testing |
| [Exploratory Testing](exploratory/) | learning, designing and running tests at once |
| [Operational Profile](operational-profile/) | testing in proportion to field use |

New to the topic? Start with [Four Verification Methods](overview/techniques.md). Why verify early at all: [Cost of Late Fixes](../define/cost-of-late-fixes.md).

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
