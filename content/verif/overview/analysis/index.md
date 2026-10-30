---
title: Verification by Analysis
parent: Verification Overview
nav_order: 3
layout: default
has_children: true
redirect_from:
  - /content/verif/overview/analysis/analysis_definitions.html
  - /content/verif/overview/analysis/checking_example.html
---

# Verification by Analysis

**Analysis verifies a product by calculation and reasoning about a model of it, instead of running it** {% cite nasa2016sehandbook %}. For software the model is the code itself or an abstraction of it, and the reasoning covers every path at once, where a test sees only the path its input takes {% cite chess2007static %}.

That breadth comes at a price. Rice's theorem shows that no algorithm can decide every non-trivial question about what programs do {% cite rice1953classes %}, so an analysis either approximates, raising false alarms or missing bugs, or needs a person to supply a model, a specification or a proof. What a clean result *means* therefore differs by approach:

| Approach | What a clean result means | Example tools | Main cost | Read more |
|---|---|---|---|---|
| Bug finders: style, pattern, type and taint checks | none of the checked patterns was found; nothing is proved | pylint, mypy, Bandit, SpotBugs, CodeQL | noise if not tuned; misses what has no rule | [Pattern, Type and Taint Checks](../../static/techniques.md) |
| Sound analysis of one property | that kind of error cannot occur on any path, for example no division by zero | Polyspace, Astrée | false alarms that a person must clear | [Soundness and Completeness](../../static/limits.md) |
| Model checking | the property holds in every reachable state *of the model* | SPIN, Java PathFinder, CBMC | building the model; state explosion | [Model Checking](../../static/model-checking.md) |
| Program verification (theorem proving) | the code implements its formal specification | Dafny, Frama-C, KeY, Isabelle | writing the specification can take more work than the code | [Hoare Logic](../../static/program-verification.md), [Analysis Tools by Family](tools.md) |

Three terms in the table: **taint** checks follow untrusted input to a dangerous call; a **sound** analysis never misses a violation of its property; **state explosion** is the growth in states a model checker must visit as the model grows. The rows run from cheap and shallow to costly and deep: Chess and West place style checking at the cheap end and program verification at the costly one, which is rare in practice because detailed specifications rarely exist {% cite chess2007static %}.

Three pages in this section complete the map. [What Analysis Can Prove](analysis_properties.md) names the kinds of property (safety, liveness, fairness, temporal) and what no analysis can show. [Analysis Tools by Family](tools.md) lists current tools for each approach. [Dynamic Analysis and the Probe Effect](dynamic-analysis.md) covers the opposite approach, learning from executions, and how observing a run can change it.

A worked example of checking a lock discipline, in which a model checker's two counterexamples turn out to be false alarms, is on the [Abstraction Refinement (CEGAR)](../../static/cegar.md) page.

---

### References

{% bibliography --cited %}

### Acknowledgments

The lectures behind this area took early inspiration from the *Analysis of Software Artifacts* course of Eduardo Miranda and Claire Le Goues (Carnegie Mellon University). Its static analysis and model checking lectures also drew on Jonathan Aldrich and Robert Bocchino. The pages here are original, written from the literature review; any errors are ours.

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
