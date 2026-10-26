---
title: "Study Notes: Adequacy Criteria"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 7
redirect_from: /content/verif/coverage/SN_L04_Coverage.html
layout: default
page_type: study-notes
---

# Study Notes: Adequacy Criteria

The lecture asks *"how do you know when you have tested enough?"* Adequacy criteria give a measurable answer: they name the elements a test suite should exercise and count how many it does. The lecture builds the criteria up from statements to mutants, then asks what the research says a coverage number actually tells you. The [revision questions](RQ_L04_Coverage.md) test this lecture.

## 1. A criterion counts elements; it does not judge tests

A coverage criterion names a set of elements (statements, branches, paths, conditions, definition-use pairs or mutants) and reports the fraction a suite exercises {% cite ammann2016introduction %}. It serves two purposes: as a **generator** it points at the next test to write, and as a **measure** it scores an existing suite. Criteria on code need the source; criteria on the input space, such as boundaries and combinations, need only the specification.

→ [Test Coverage Criteria](../../verif/coverage/index.md)

## 2. Control-flow criteria are counted on the control-flow graph

The control-flow graph turns a function into nodes (statements or basic blocks) and edges (transfers of control). A decision is a node with two or more outgoing edges; a condition is a Boolean expression without logical operators inside a decision, so `a > b and c == 1` is one decision with two conditions. For example, take this function, with two decisions and therefore four branches:

```python
def grade(score):
    if score >= 90:
        return "A"
    elif score >= 70:
        return "B"
    else:
        return "C"
```

The test `grade(95)` takes 1 of the 4 branches. Adding `grade(75)` takes the false side of the first decision and the true side of the second: 3 of 4. Adding `grade(50)` completes branch coverage.

→ [Coverage Terminology](../../verif/coverage/terminology.md)

## 3. Branch coverage subsumes statement coverage, and both are weak

Statement coverage asks that every statement runs; branch coverage asks that every decision outcome is taken, including the false side of an `if` with no `else`. A suite that runs every statement can still skip that empty false side. In the lecture's example, `x = 0; if A < 5: x = 4; y = z / x`, the test `A = 2` runs every statement, and only the false side (`A = 6`) divides by zero. Branch coverage subsumes statement coverage, but it treats each decision on its own, so it misses faults that appear only for one *combination* of outcomes.

→ [Statement and Branch Coverage](../../verif/coverage/code.md)

## 4. Basis paths cover decisions in combination, at linear cost

McCabe's cyclomatic complexity *V(G) = e − n + 2* (for two-way decisions, simply decisions + 1) is the number of linearly independent paths through the graph {% cite mccabe1976complexity %}. Running a basis set of *V(G)* paths subsumes branch coverage, and each new path is built by flipping one decision of an earlier one. A basis is not unique, and some of its paths may be infeasible when decisions share data.

→ [Basis Path Coverage](../../verif/coverage/basis.md)

## 5. MC/DC looks inside a decision

Branch and basis-path coverage treat `A and B` as one decision, so they can be satisfied without ever trying the input that tells `and` from `or`. Multiple condition coverage tries all 2<sup>*c*</sup> combinations of the *c* conditions; MC/DC needs as few as *c* + 1 tests for a chain of `and` or `or`, because it asks that each condition be shown to change the decision's outcome on its own {% cite chilenski1994mcdc %}. DO-178C requires MC/DC for the most critical airborne software {% cite hayhurst2001practical %}.

→ [Multiple Condition Coverage and MC/DC](../../verif/coverage/conditions.md)

## 6. Data-flow coverage follows values, mutation asks whether tests would notice

Data-flow criteria require that each definition of a variable reaches its uses along a path that does not overwrite it {% cite rapps1985dataflow %}; All-Uses can demand tests that branch coverage lets you skip. Mutation coverage plants small faults (`>` becomes `>=`) and counts how many the suite detects {% cite demillo1978hints %}. A test that runs the mutated line but asserts nothing lets the mutant survive, so mutation measures the strength of the checks, which no reach-based criterion does.

→ [Data Flow Coverage](../../verif/coverage/data-flow.md) · [Mutation Coverage](../../verif/coverage/mutation.md)

## 7. Input-space criteria cover combinations and transitions, not code

For many inputs, exhaustive combinations explode: four parameters with three values each give 81 combinations. Pairwise testing covers every pair of values in far fewer tests, and NIST's fault studies found most failures triggered by one or two parameters {% cite kuhn2004fault %}. For state machines, N-switch coverage asks for every sequence of N + 1 transitions: 0-switch covers every transition, 1-switch every pair of consecutive transitions.

→ [Combinatorial Testing](../../verif/combinatorial/index.md)

## 8. Coverage predicts fault detection only weakly

The research half of the lecture reverses the popular belief. Once suite size is held constant, the correlation between coverage and effectiveness becomes weak {% cite inozemtseva2014coverage %}; the number of assertions explains far more {% cite zhang2015assertions %}. On 274 real faults, only 10% of the fault-revealing tests added any statement coverage; counting all four control-flow criteria the share was 28%, and adding def-use pairs raised it to 85% {% cite hemmati2015coverage %}. Of the metrics compared, mutation score is the most reliable and statement coverage the least {% cite zhang2024assessing %}.

→ [Coverage and Fault Detection](../../verif/coverage/discussion.md)

## 9. Use coverage as a clue, not a target

Marick's rule closes the lecture: coverage tools give clues, not commands {% cite marick1997coverage %}. Design tests from the specification, then treat each uncovered line as a question about which feature is under-tested. A fixed percentage used as a gate invites quick tests that satisfy the tool and check nothing.

→ [Using Coverage in Practice](../../verif/coverage/practice.md)

## Where this goes next

The next lecture moves from code to the input space: [Input Domain Testing](SN_L05_Domain.md) designs tests from equivalence classes and boundaries, and its effectiveness is again something coverage alone cannot show. The coverage lab applies this lecture: measure statement and branch coverage on a small program with pytest-cov, then add the tests the report points to.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
