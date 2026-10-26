---
title: Using Coverage in Practice
parent: Coverage
nav_order: 8
layout: default
---

# Using Coverage in Practice

**Using coverage well means treating an uncovered element as a clue about a gap in test design, not as a target to satisfy.** Marick put it as "coverage tools don't give commands, they give clues" {% cite marick1997coverage %}. This page turns the evidence in [Coverage and Fault Detection](discussion.md) into working rules.

## 1. Design tests from the specification, then measure

Marick's 1991 study is the positive half of the story. Seven units were first tested with a methodical black-box technique derived from the specification; the suites reached nearly 100% of feasible branch coverage, loop coverage (each loop run zero, one and several times) and [multiple condition coverage](conditions.md), and closing the remaining gap took only a few percent of the total testing time {% cite marick1991cost %}. His conclusion: reaching full coverage *after* measuring matters less than nearly reaching it with the first specification-based suite, and **low initial coverage signals a problem in the testing process** {% cite marick1991cost %}.

The reverse order fails. A suite designed *to reach* coverage is uniformly weak in exactly the way coverage cannot detect: it misses faults of omission, and the tool can no longer show where it is weak {% cite marick1997coverage %}.

## 2. Turn each uncovered line into a question

Marick's procedure for a missed coverage condition has three steps {% cite marick1997coverage %}.

1. Ask which **feature** the uncovered code belongs to; that feature is under-tested in at least one way.
2. Rethink how that feature should have been tested, without aiming at the missed condition itself.
3. Run the new tests and check that the condition is now covered as a side effect.

**Example.** A pull request report shows that `if retries > MAX_RETRIES: raise TimeoutError` was never taken. The quick fix is a test that sets `retries` to 99. The useful question is "how do we test the timeout feature?", which also produces tests for a server that recovers on the last retry and for a timeout during the first request, neither of which the coverage report asked for.

## 3. Do not make a percentage a gate

A fixed threshold changes behaviour. In organisations with an 85% shipping gate, Marick found most developers clustered just above 85%, and when he traced the number back he found someone who had "pulled 85% out of a hat" {% cite marick1997coverage %}. Inozemtseva and Holmes concluded that a fixed coverage value used as a quality target "is unlikely to produce an effective test suite" {% cite inozemtseva2014coverage %}.

Better uses of the number, in order of value:

- **Review uncovered code in every change**: a diff-coverage report, which shows only the changed lines that no test ran, is a list of questions for the reviewer. In Python, the command below lists missed lines and missed branches (shown as `12->15`, a jump from line 12 to line 15 never taken), and the `diff-cover` tool limits the report to the lines a pull request changed.

  ```bash
  pytest --cov=pkg --cov-branch --cov-report=term-missing
  ```

- **Watch the trend**: a drop in coverage on a module signals tests that were deleted or never written.
- **Use a low floor, not a target**: a floor is set well *below* where the code already is, so nobody writes tests to reach it; it only fires when a module is neglected. A target sits *above* current coverage and invites tests written to reach the number. The exact floor is a team choice.

## 4. Strengthen the checks, not only the reach

Because assertions explain most of what coverage seems to explain {% cite zhang2015assertions %}, every coverage review should also ask what the covering test *asserts*. For code where a missed fault is expensive, run [mutation testing](mutation.md) on the changed files: a surviving mutant names a specific missing check.

## When to use these rules — and when not

- **They apply** to unit and integration testing of application code, where coverage tools are cheap and reports are routine.
- **Standards override them.** Where DO-178C or a similar standard mandates MC/DC, the percentage *is* a gate; the rules above still decide how the tests are designed.

## Limitations

Marick's 1991 cost figures come from seven units in one company and from a tester trained in the technique; they show what is possible, not what a typical team achieves. The 85% anecdote is an experienced consultant's observation, not a measurement.

## How solid is this?

- **Where it comes from.** Marick's two papers are practitioner experience {% cite marick1991cost marick1997coverage %}; the case against coverage targets rests on the controlled studies summarised in [Coverage and Fault Detection](discussion.md) {% cite inozemtseva2014coverage zhang2015assertions %}.
- **What we do not hold.** No study in the review compares teams with and without a coverage gate, so the cost of gates is argued from mechanism and anecdote.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
