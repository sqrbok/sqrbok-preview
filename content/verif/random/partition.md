---
title: Random versus Partition Testing
parent: Random Testing
nav_order: 2
layout: default
---

# Random versus Partition Testing

**Partition testing** divides the input domain into subdomains expected to behave alike and tests each one, as equivalence partitioning and boundary value analysis do ([Input Domain Testing](../domain/index.md)); **random testing** samples the whole domain without such a division. The question of which finds more faults per unit of effort has been studied since 1984, and the answer is "it depends, by less than you would think".

## 1. The surprising first result

Duran and Ntafos simulated both strategies on programs whose subdomains had random failure rates {% cite duran1984evaluation %}. In one simulation of 50 trials, partition testing was more likely to find a failure in 36 trials and random testing in 14. The surprise was how close random testing came: on average its probability of detection was 0.932 of partition testing's, with no analysis at all. Because random tests are much cheaper to produce than tests chosen per subdomain, they concluded that random testing "may often be more cost effective". Their experiments on real programs also reached high statement and branch coverage with random inputs alone.

## 2. Twenty percent more tests

Hamlet's survey of the comparisons gives the rule of thumb: partition testing can have the edge for the same number of tests, but "by taking 20% more points in a random test, any advantage a partition test might have enjoyed is wiped out" {% cite hamlet1984random %}. The only way to give partition testing a clear win is a partition whose subdomains really are homogeneous: either every input in a subdomain fails or none does.

## 3. When partitioning wins

Two later results moved the balance back.

- **Cost of failure.** If failures in some subdomains are far more expensive than in others, partition testing can put more tests where failures matter; random testing "has no way to tune itself to the cost factors" {% cite tsoukalas1993reliability %}.
- **Proportional allocation.** Allocating partition tests in proportion to subdomain size guarantees that partition testing is never worse than random {% cite chen1994relationship %}, but Ntafos showed the guaranteed margin is tiny: starting from 500 tests, the relative advantage was below 0.05% and quickly became insignificant {% cite ntafos2001comparisons %}.

The measures behind these results, and the exact conditions, are worked out on [E-measure and P-measure](measures.md).

## 4. When only random will do

Hamlet later named two situations in which systematic partitioning is not a real option {% cite hamlet2006random %}:

- **Large, unstructured input domains**, where no meaningful subdomains suggest themselves. Calling inputs "the same" is then subjective.
- **Programs with state.** A valid test is a *sequence* of inputs starting from initialisation, because a state sampled directly may never occur in practice. Random sequences reach only reachable states.

## Worked example

A tax function has three brackets. A simplified partition suite picks one income per bracket plus one value at each of the two boundaries: five tests. Random testing draws 100 incomes from last year's distribution of real declarations. If the bug is a wrong rate in the top bracket, which holds 2% of declarations, the 100 random tests miss it with probability 0.98<sup>100</sup> ≈ 13%, while the partition suite catches it every time. If the bug is a rounding error that appears for one income in a thousand, spread evenly over the incomes people actually declare and unrelated to the brackets, the five partition tests, each hitting it with probability 0.001 independently, catch it with probability about 0.5% and the 100 random tests with about 10% (1 − 0.999<sup>100</sup>); to be 95% sure, random testing needs about 3,000 tests (1 − 0.999<sup>3000</sup> ≈ 95%). Which strategy wins depends on where the bug is, which nobody knows in advance.

**Where the inputs come from.** Partitions come from the specification (brackets, limits, input classes) and from the code's branches. A random distribution worth sampling comes from real inputs, such as last year's declarations in the example, or from the [operational profile](../operational-profile/index.md); without usage data, random testing falls back to a uniform distribution, which says nothing about reliability.

## When to use each — and when not

- **Use partitions** when the specification names the subdomains and the boundaries, and when some failures are much costlier than others.
- **Use random sampling** for broad coverage, for stateful sequences, and when a reliability estimate is needed.
- **Do not treat them as rivals.** The usual practice is both: systematic tests for the specified cases and boundaries, random tests for everything nobody thought of.

## How solid is this?

- **Where it comes from.** The comparison rests on simulations and analytical models {% cite duran1984evaluation hamlet1984random ntafos2001comparisons tsoukalas1993reliability %}, with small experiments on real programs.
- **What is contested.** Every model assumes known failure rates per subdomain, which a tester never has. The results say which strategy wins *under* those assumptions, not on a given project.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
