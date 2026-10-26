---
title: Coverage
parent: Verification and Validation
nav_order: 2
layout: default
has_children: true
---

# Test Coverage Criteria

A **coverage criterion** names a set of program elements (statements, branches, paths, conditions, definition-use pairs or mutants) and measures what fraction of them a test suite exercises. It answers *"what has this suite not touched yet?"*, not *"is this suite good?"*.

A criterion is used in two ways {% cite ammann2016introduction %}. As a **generator**, it points at the next test to write: the branch nobody has taken yet. As a **measure**, it scores an existing suite: 87% of branches taken.

## The criteria at a glance

The table compares the criteria by what they demand of a test suite and the least number of tests that can satisfy them. One test can take outcomes of several decisions at once, so counts for a whole function are usually smaller than the sum over its decisions.

| Criterion | A test suite must… | Fewest tests | Page |
|---|---|---|---|
| Statement | execute every statement once | 1, if one path runs every statement | [Statement and Branch Coverage](code.md) |
| Branch | take every outcome of every decision | 2, since every decision needs a true and a false test | [Statement and Branch Coverage](code.md) |
| Basis path | run *V(G)* linearly independent paths | exactly *V(G)*: decisions + 1 for two-way decisions | [Basis Path](basis.md) |
| MC/DC | show that each condition flips its decision on its own | *c* + 1 for a decision with *c* conditions | [Multiple Conditions](conditions.md) |
| Multiple condition | try every combination of conditions | 2<sup>*c*</sup> for a decision with *c* conditions | [Multiple Conditions](conditions.md) |
| All-Uses | connect every definition of a variable to each of its uses | grows with data dependencies | [Data Flow](data-flow.md) |
| Mutation score | make some test fail on every non-equivalent mutant | one suite run per mutant | [Mutation](mutation.md) |

Criteria are ordered by **subsumption**: criterion A subsumes B when every suite that satisfies A also satisfies B.

![Subsumption hierarchy of coverage criteria in three families: data flow, control flow and conditions](subsumption-hierarchy.svg)

_Subsumption among the criteria on these pages. All-Uses subsumes Branch under the usual assumption that every definition reaches a use. Mutation is not in the diagram: whether it subsumes a criterion depends on which mutation operators are used._ {% cite ammann2016introduction rapps1985dataflow %}

Subsumption is about *requirements*, not bugs: a suite built for a weaker criterion can still find a fault that one built for a stronger criterion misses, because detection depends on the inputs chosen {% cite ammann2016introduction %}.

## Reading order

Start with [Terminology](terminology.md) for the control-flow graph, then the criteria from statement to mutation. [Coverage and Fault Detection](discussion.md) summarises the evidence on how well coverage predicts found faults, and [Using Coverage in Practice](practice.md) turns it into rules for a team.

## How solid is this?

- **Where it comes from.** The definitions and the subsumption relations are textbook material {% cite ammann2016introduction %}. They are proofs about what a suite must do, not measurements.
- **What is contested.** Whether higher coverage means *better* testing is an empirical question, and the answer is weaker than most teams assume. See [Coverage and Fault Detection](discussion.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
