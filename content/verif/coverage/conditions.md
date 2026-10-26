---
title: Multiple Conditions
parent: Coverage
nav_order: 4
layout: default
---

# Multiple Condition Coverage and MC/DC

**Multiple condition coverage** requires every combination of truth values of the conditions inside a decision; **modified condition/decision coverage (MC/DC)** requires only enough tests to show that each condition, changed on its own, changes the decision's outcome. Both look *inside* a decision, which branch and basis-path coverage treat as one unit.

## 1. Why decisions are not enough

The specification says `A or B`; the code says `A and B`:

```text
if (A and B) then Z = Z + 1      -- should be (A or B)
else              Z = Z + 2
```

The suite *{(T, T), (F, F)}* takes both branches and both basis paths, and passes: for these two inputs `and` and `or` agree. The fault appears only when exactly one condition is true. A criterion that never asks for *(T, F)* or *(F, T)* cannot see it.

## 2. Multiple condition coverage

Every combination of the *c* conditions in a decision is tested: 2<sup>*c*</sup> tests. For `A or B` that is four tests, which include *(T, F)* and expose the fault above. The cost doubles with every condition: 8 tests for three conditions, 64 for six.

Some combinations cannot occur. (Short-circuit evaluation, where Python skips the rest of an `or` once one part is true, does not change which combinations the criterion asks for; it only means some conditions are not evaluated in a test.) In `y == 2 or (y == 6 and flag)` the conditions `y == 2` and `y == 6` cannot both be true, so two of the eight rows are infeasible and are left out.

## 3. MC/DC

MC/DC asks three things {% cite chilenski1994mcdc hayhurst2001practical %}:

1. every decision takes both outcomes;
2. every condition takes both values;
3. each condition is shown to **independently affect** the outcome: there is a pair of tests that differ only in that condition and produce different outcomes.

A decision with *c* conditions can usually be covered with *c* + 1 tests instead of 2<sup>*c*</sup>. Mixed operators work the same way: for `A and (B or C)` the four tests *TTF, FTF, TFF, TFT* give the pairs *TTF/FTF* for A, *TTF/TFF* for B and *TFT/TFF* for C. Decisions that repeat a variable, such as `(A and B) or (A and C)`, can need more.

**Example: `A or B`.** The independence pairs share the row where both conditions are false:

| A | B | A or B | Shows |
|---|---|---|---|
| F | F | F | baseline for both pairs |
| T | F | T | A matters: (F,F) → (T,F) changes the outcome with B fixed |
| F | T | T | B matters: (F,F) → (F,T) changes the outcome with A fixed |

The row *(T, T)* is not needed: flipping either condition alone leaves `A or B` true. Against the faulty `A and B`, rows 2 and 3 fail, so MC/DC finds the fault with three tests.

**Example: `A and B`.** Here the shared row is *(T, T)*; the set is *{(T, T), (F, T), (T, F)}*, and *(F, F)* is the row left out.

## 4. The criteria compared

The table puts the decision-level and condition-level criteria side by side for one decision with *c* conditions.

| Criterion | Tests for one decision | Finds a wrong `and`/`or`? |
|---|---|---|
| Branch | 2 | not reliably |
| MC/DC | *c* + 1 | yes, for each condition |
| Multiple condition | 2<sup>*c*</sup> | yes |

## When to use it — and when not

- **Use MC/DC** where a standard demands it: DO-178C, the standard for airborne software, requires it for the most critical (Level A) airborne software {% cite hayhurst2001practical %}.
- **Use multiple condition coverage** for small decisions with two or three conditions, where 2<sup>*c*</sup> tests are cheap.
- **Do not use either as a general quality score.** They check the logic of decisions, not the computations around them.

## How solid is this?

- **Where it comes from.** MC/DC was defined for avionics; Chilenski and Miller analysed its applicability {% cite chilenski1994mcdc %}, and the NASA tutorial is the standard practical reference {% cite hayhurst2001practical %}.
- **What is contested.** On 274 real faults, MC/DC was no more often effective than branch coverage (19% each), and on randomly built suites it added little beyond statement coverage once suite size was controlled {% cite hemmati2015coverage inozemtseva2014coverage %}. Its value is in logic-heavy code such as control laws, not in typical application code.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
