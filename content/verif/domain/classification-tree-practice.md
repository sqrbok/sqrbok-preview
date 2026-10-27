---
title: Classification Trees in Practice
parent: Input Domain Testing
nav_order: 6
layout: default
---

# Classification Trees in Practice

**Applying the [classification tree method](classification-tree.md) in practice** means handling impossible class combinations, judging the quality of a tree, and using tools that generate combination tables. This page covers the three, with the evidence on how well the method works.

## 1. Constraints: removing impossible combinations

Real input spaces always contain combinations that cannot occur: an empty array with more than one occurrence of a value, or a "not applicable" field that still has a value. Constraints are rules that forbid class combinations; their handling went through three generations:

| Generation | Mechanism | Source |
|---|---|---|
| Textual annotations | `[error]`, `[single]`, `[if …]` on choices | category-partition {% cite ostrand1988category %} |
| Boolean dependency rules | rules between classes, such as "array size 0 ⇒ occurrences none" | CTE XL {% cite lehmann2000ctexl %} |
| Numerical constraints | linear expressions such as `a + b > c`, checked with a SAT solver (a program that decides whether a logical formula can be satisfied) | CTE XL {% cite kruse2012numerical %} |

**Example.** For `count(array, value)`, classifications for array size (0, 1, more), occurrences (none, one, more) and order (sorted, unsorted) give 3 × 3 × 2 = 18 combinations. Rules remove the impossible ones: size 0 allows only "none" and has no order (1 combination); size 1 allows only "none" or "one" and counts as sorted (2); size more than 1 keeps all 3 × 2 = 6. The table shrinks from 18 to 9 rows.

When a generator meets a conflict, it can avoid it, replace a value, merge parameters, or split the model. Comparing these four over 3,854 generated test suites, Grindal et al. found that **avoiding** conflicting combinations during generation gave the smallest suites while keeping coverage {% cite grindal2007conflicts %}.

## 2. Measuring the quality of a tree

Chen et al. measure a tree by the share of the combinations it can generate that are legitimate, that is, possible in the real input domain {% cite chen2000integrated %}. Duplicated subtrees are the main cause of waste. In their bonus-program example, restructuring the tree raised this effectiveness from 0.17 to 0.40 {% cite chen1999restructuring chen2002restructuring %}.

## 3. Tools

The Classification Tree Editor (CTE) began as a research prototype. CTE XL added dependency rules and automatic generation of tables by the **minimality** criterion (every class at least once), the **maximality** criterion (every permitted combination) and *t*-wise coverage (every combination of classes from any *t* classifications; *t* = 2 is [pairwise testing](integration.md)); in some industrial uses its rules cut the theoretically possible test cases by more than 90% {% cite lehmann2000ctexl %}. Numerical constraints checked with a SAT solver were added to CTE XL {% cite kruse2012numerical %}; its successor TESTONA adds a built-in constraint solver, uses the rules as test oracles and generates test scripts {% cite kruse2016oracles %}.

## 4. Evidence

The only controlled study taught CTM to 104 full-time and 58 part-time students in a two-hour lecture and a one-hour workshop {% cite yu2004novicetesters %}. Before training, 57% of the full-time students had used white-box methods, 23% black-box methods and 12% a mix of both; the paper does not break down the remaining 8%. Afterwards, Yu et al. report that 70% of these full-time students preferred CTM, 20% had no clear preference and 10% preferred their own methods. The part-time students' own test suites, written before the training, detected on average 55% of the faulty programs; only one suite, built from a mix of black-box and white-box methods, detected all of them. The disadvantage named most often (60% of full-time students) was infeasible class combinations.

Industrial reports come from aerospace, automotive and logistics. In one, an airfield lighting control system of about 2,700 C modules was tested with 263 test cases designed with CTM {% cite grochtmann1993classification %}.

## When to use these tools — and when not

- **Use a tool** once a tree has more than a handful of classifications: generation and constraint checking by hand become error-prone.
- **Use dependency rules** instead of hand-deleting rows, so the reasons stay documented.
- **Do not trust a generated table blindly**: it is only as good as the tree and the rules.

## Limitations

A large tree with many constraints is itself a specification that can be wrong, and nobody tests the tree. Tool support has been developed mainly by one group of companies, so independent industrial evaluations are scarce.

## How solid is this?

- **What is solid.** The Yu et al. figures are measured {% cite yu2004novicetesters %}, but on students, not professionals.
- **What is contested.** The industrial reports are case descriptions from the method's developers and tool vendors {% cite grochtmann1993classification lehmann2000ctexl %}; the "more than 90%" reduction is relative to all *theoretically* possible combinations, not to a test suite a tester would actually write.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
