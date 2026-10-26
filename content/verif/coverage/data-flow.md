---
title: Data Flow
parent: Coverage
nav_order: 5
layout: default
---

# Data Flow Coverage

**Data-flow coverage** measures whether a test suite exercises the paths along which a value travels from the statement that **defines** a variable to the statements that **use** it. Control-flow criteria ask *which code ran*; data-flow criteria ask *which values reached which uses* {% cite rapps1985dataflow su2017dataflow %}.

## 1. Definitions, uses and DU pairs

The vocabulary has four terms.

- A **definition** (def) of a variable is a statement that assigns it: `x = a + b`.
- A **use** reads it: a *c-use* in a computation (`y = x * 2`), a *p-use* in a predicate (`if x > 10`). A p-use has an outcome for each branch of the predicate, so it counts once per branch.
- A **definition-clear path** from a def to a use is one on which the variable is not reassigned.
- A **DU pair** is a def and a use connected by at least one definition-clear path.

## 2. The criteria

The three main criteria differ in how many of the paths behind each DU pair a suite must run.

| Criterion | A test suite must… | Strength |
|---|---|---|
| All-Defs | reach *some* use from every definition | weakest |
| All-Uses | reach *every* use from every definition, and every outcome of a p-use | strong; the usual target |
| All-DU-Paths | run *every* definition-clear path of every DU pair | strongest; some paths may be impossible to run (infeasible) |

All-Uses [subsumes](index.md) branch coverage (every suite that satisfies it also takes every branch), under the usual assumption that every definition reaches some use; All-Defs does not, because one use per definition can leave a branch untaken {% cite rapps1985dataflow %}.

## 3. Worked example

```python
def process(flag, value):
    if flag:
        x = value * 2      # d1
    else:
        x = value + 10     # d2
    result = x + 1         # c-use of x
    if x > 20:             # p-use of x
        result = result * 2
    return result
```

Each definition of `x` reaches the c-use, and reaches the p-use with two outcomes. All-Uses therefore needs each definition to reach `x > 20` both true and false: 2 definitions × (1 c-use + 2 p-use outcomes) = 6 DU pairs for `x`. The table tracks variable `x` only; All-Uses would also count the pairs of `result`, defined at `result = x + 1` and `result = result * 2` and used at `result * 2` and `return result`.

| Test | flag | value | x | Covers |
|---|---|---|---|---|
| T1 | True | 15 | 30 | d1 → c-use, d1 → p-use (true) |
| T2 | True | 5 | 10 | d1 → c-use, d1 → p-use (false) |
| T3 | False | 15 | 25 | d2 → c-use, d2 → p-use (true) |
| T4 | False | 5 | 15 | d2 → c-use, d2 → p-use (false) |

Two tests (T1 and T4) already give 100% branch coverage. All-Uses asks for two more, because it insists that *each* way of computing `x` meets *each* outcome of the check on `x`. A bug that only shows when `x` comes from `d1` and stays at or below 20 is caught by T2 and nothing else.

## When to use it — and when not

- **Use All-Uses** on code that transforms data through several assignments: calculations, parsers, state updates.
- **Use it after branch coverage**, to find the tests that branch coverage lets you skip.
- **Do not expect wide tool support.** Mainstream coverage tools report statements and branches; data-flow coverage mostly needs research tools or static analysers {% cite su2017dataflow %}.

## Limitations

The number of DU pairs grows quickly with aliasing, arrays and pointers, and static analysis over-approximates them, so some required pairs are infeasible and must be ruled out by hand {% cite su2017dataflow %}. Like every structural criterion, it cannot ask for a test of code that is missing.

## How solid is this?

- **Where it comes from.** Rapps and Weyuker defined the criteria family {% cite rapps1985dataflow %}; Su et al. survey 97 papers on data-flow testing {% cite su2017dataflow %}.
- **What the evidence says.** On 274 real faults from Defects4J, all four control-flow criteria together (statement, branch, MC/DC and loop coverage, which runs each loop zero, one and several times) were effective for 28% of faults; adding def-use pair coverage raised this to 85%, and def-use coverage was effective for 79% of the faults that control flow missed {% cite hemmati2015coverage %}. These are upper bounds: a criterion counted as effective when the fault-revealing test increased it.
- **What is weaker than it looks.** The often-quoted claim that 90% data-flow coverage finds twice as many defects as 90% branch coverage comes from a practitioner website quoted in the survey, not from a controlled study {% cite su2017dataflow %}.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
