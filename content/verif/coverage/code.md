---
title: Statement and Branch Coverage
parent: Coverage
nav_order: 2
layout: default
---

# Statement and Branch Coverage

**Statement coverage** is the fraction of a program's statements that a test suite executes at least once; **branch coverage** is the fraction of decision outcomes (each true and each false edge) that it takes. They are the two criteria every coverage tool reports, and the weakest ones on the [subsumption hierarchy](index.md).

## 1. Statement coverage

$$
\text{statement coverage} = \frac{\text{statements executed}}{\text{all statements}}
$$

It is also called line coverage, and in some standards C0. A statement that no test has executed may hide any fault at all, so 100% statement coverage is a minimum: if a line never ran, the suite cannot have checked it.

## 2. Branch coverage

$$
\text{branch coverage} = \frac{\text{decision outcomes taken}}{\text{all decision outcomes}}
$$

It is also called decision coverage or C1. Every `if` needs a test where it is true and a test where it is false, even when the false side has no statements of its own. Branch coverage **subsumes** statement coverage: taking every edge of the graph passes through every node.

In Python, pytest-cov measures both; [Measuring Coverage in Python](python.md) reads a real report line by line, including how a loop counts.

## 3. Example: the missing `else`

```text
input A, z
x = 0
if A < 5 then
    x = 4
end if
y = z / x
```

The single test *A = 2* executes every statement, so statement coverage is 100%, and it passes. Branch coverage is 50%, because the false outcome of `A < 5` was never taken. The test that branch coverage asks for, say *A = 6*, skips `x = 4` and divides by zero.

![Control-flow graph: the path for A = 2 executes all statements; the false branch for A = 6 leads to division by zero](statement-vs-branch.svg)

_The false edge has no statements on it, so statement coverage cannot see it._

## 4. Where branch coverage still fails

Branch coverage treats each decision on its own. In this program the specification says *Z* should be 2 whenever *A* is true; otherwise 2 if *B* is true and 0 if not:

```text
input A, B
Z = 0
if A then Z = Z + 1
if B then Z = 2
print Z
```

The suite *{(A = T, B = T), (A = F, B = F)}* takes all four branches and passes: it prints 2 and 0, as specified. The fault shows only for *(A = T, B = F)*, which prints 1. [Data-flow coverage](data-flow.md) forces that test: the value assigned by `Z = Z + 1` reaches `print Z` without being overwritten only when B is false, and All-Uses requires every assignment to reach every use it can reach without being reassigned. [Basis paths](basis.md) may or may not include it: this graph has V(G) = 3, and whether *(T, F)* is among the three independent paths depends on which are chosen.

## When to use them — and when not

- **Use** statement coverage as a floor: code that no test executes is untested, whatever else is true.
- **Use** branch coverage as the default report: it is cheap, every tool measures it, and it finds the missing-`else` class of fault. coverage.py treats `if a and b:` as one decision with two outcomes, so it cannot tell you which condition was tried.
- **Do not** aim for all paths: with loops their number is unbounded; [Basis Path](basis.md) explains the practical substitute and why some paths can never run.
- **Do not** read either number as test quality. A suite can reach 100% of both while checking no output at all; see [Coverage and Fault Detection](discussion.md).

## How solid is this?

- **Where it comes from.** Definitions and subsumption follow Ammann and Offutt {% cite ammann2016introduction %}; the naming C0/C1 varies between standards and tools.
- **What is contested.** On 274 real faults, statement coverage was effective for only 10% of them and branch coverage for 19%: only that often did the test that exposed the fault add any statement or branch coverage {% cite hemmati2015coverage %}. Most tests that found a fault ran code the suite already covered, so a coverage gain rarely signalled a newly found fault.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
