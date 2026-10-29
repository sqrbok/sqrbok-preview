---
parent: Testing Overview
nav_order: 1
title: What Testing Is
layout: default
---

# What Testing Is

**Testing is evaluating software by observing its execution: a test runs the program on chosen inputs and compares what happens with an expected result** {% cite ammann2016introduction %}. A test that exposes a wrong result has done its job; a passing test only shows that this input worked.

## 1. Fault, error and failure

Three words describe what goes wrong, and testing sees only the last {% cite ammann2016introduction %}:

- A **fault** is a static defect in the code, such as a wrong loop bound.
- An **error** is an incorrect internal state caused by the fault while the program runs.
- A **failure** is external behaviour that is wrong with respect to the requirements.

**Example.** This Python port of Ammann and Offutt's `numZero` example should count the zeros in a list, but its loop starts at index 1 instead of 0:

```python
def num_zero(xs):
    """Return the number of zeros in xs."""
    count = 0
    for i in range(1, len(xs)):  # fault: the loop should start at 0
        if xs[i] == 0:
            count += 1
    return count
```

Two pytest tests run it. `num_zero([2, 7, 0])` returns 1, which is right; `num_zero([0, 7, 2])` returns 0, and pytest reports `assert 0 == 1`. Both runs execute the fault, and both are in an error state from the first iteration, because `i` is 1 where it should be 0. In the first run the error does not reach the output: the skipped element is not a zero, so the count is still correct. Only the second run fails {% cite ammann2016introduction %}.

For a failure to be seen, four things must happen: the test must **reach** the fault, the fault must **infect** the state, and the infected state must **propagate** to the program's output, and the test must check that output, a fourth condition Ammann and Offutt call **revealability** {% cite ammann2016introduction %}. A test that reaches the fault but checks nothing, or checks the wrong output, sees no failure. **Debugging** is the separate work of finding the fault once a failure has been seen.

## 2. What a test needs

A test case has input values and an **expected result**: the output the program should produce if it meets its specification {% cite ammann2016introduction %}. Whatever decides whether the actual output matches is the **oracle**. Writing the expected value by hand is the usual oracle; others are listed here:

- A reference implementation, such as the old system a new one replaces, gives the expected output for any input.
- A property checks every output without knowing it exactly, for example "a sorted list has the same elements, in order"; see [Property-Based Testing](../../random/property-based.md).
- A crash detector treats any crash or unhandled exception as a failure, which is all a fuzzer needs; see [Fuzz Testing](../../random/fuzzing.md).

## 3. Why testing cannot show that faults are absent

Dijkstra put it in one line: "Program testing can be used to show the presence of bugs, but never to show their absence!" {% cite dijkstra1970notes %} The reason is the size of the input space. (Constructed example.) A function of two 64-bit integers has 2<sup>128</sup> ≈ 3.4 × 10<sup>38</sup> possible inputs; at a billion tests per second, trying them all would take about 10<sup>22</sup> years. Every real suite is a tiny sample, so the art of testing is choosing that sample: see the [Testing Overview](index.md).

## When to use it — and when not

- Use testing when the expected behaviour is known and the program can be run, including after every change.
- Do not use a passing suite as evidence that a property holds on every input; use [analysis](../analysis/index.md) for that.

## Limitations

A test is only as good as its oracle. In the example, a test that called `num_zero([0, 7, 2])` but asserted nothing would have run the fault, infected the state and propagated a wrong output, and still passed.

## How solid is this?

- **Where it comes from.** The definitions, the `numZero` example and the reachability–infection–propagation model are from Ammann and Offutt {% cite ammann2016introduction %}; the quotation from Dijkstra's *Notes on Structured Programming* {% cite dijkstra1970notes %}. The Python runs were made for this page (pytest 9.1.1).
- **What is contested.** Terminology: standards and textbooks use "error", "fault", "defect" and "bug" in different senses; this page follows Ammann and Offutt.
- **What we do not hold.** No source in the review measures how often real suites have weak oracles.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
