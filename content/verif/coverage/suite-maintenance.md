---
title: Updating and Reducing a Test Suite
parent: Coverage
nav_order: 12
layout: default
---

# Updating and Reducing a Test Suite

**Test-suite maintenance is deciding, after each change to the program, which tests to add, repair or remove, so that the suite keeps its coverage criterion and its value while its running cost stays affordable.** Adding one test costs little; over years the small additions pile up, and a suite that takes hours to run is run less often.

## 1. When a regression test fails, find out which side is wrong

A regression test compares the new version's result with the result it was written to expect. A failure has three possible causes, and each needs a different action:

- **the software has a fault**: the change broke something, so fix the code (often the fix of a fix);
- **the test's inputs are no longer valid**: the interface or the input rules changed, so modify or delete the test;
- **the expected output is no longer valid**: the requirement changed on purpose, so update the expected value.

Deciding which applies is often the expensive part. Updating the expected value without asking is the dangerous shortcut, because it turns a real fault into a passing test.

## 2. Policies for adding and keeping tests

Two policies decide what goes in, and they work together:

- **Add a test for every problem report**, so that a fixed bug stays fixed.
- **Keep the coverage criterion satisfied** after each change, so that new code gets tests before it merges ([Using Coverage in Practice](practice.md)).

## 3. Rules for removing tests, from safest to riskiest

The rules below are practitioner rules, ordered by the risk they carry:

1. **Remove tests that add nothing to the coverage criterion.** If a suite of *N* tests satisfies the criterion, the tests can often be reordered so that the first *N − x* already satisfy it, and the last *x* can go. Finding the smallest such subset is NP-complete, so tools use a greedy heuristic: keep the test that covers most of what is still uncovered, and repeat {% cite yoo2012regression %}.
2. **Remove tests that have never found a fault.** Risky: a test that never failed may be guarding code that has not changed yet.
3. **Remove tests that found the same fault as other tests.** Also risky: the same fault today does not mean the same faults tomorrow.

When in doubt, leave the test in: a redundant test costs seconds per run, a missing one can cost a release.

## 4. Worked example: greedy reduction drops the only test that mattered

(Constructed example.) A suite of seven tests for the binary search on [Basis Paths for Binary Search](basis-binary-search.md) reaches 100% branch coverage. coverage.py records which test took which jump (`--cov-context=test`), and a 17-line script (`reduce.py`) applies the greedy rule (coverage.py 7.17, pytest 9.2):

```python
# test_search.py
from search import binary_search

def test_found_middle():   assert binary_search([1, 3, 5], 3) == 1
def test_empty():          assert binary_search([], 3) == -1
def test_found_right():    assert binary_search([1, 3, 5], 5) == 2
def test_found_left():     assert binary_search([1, 3, 5], 1) == 0
def test_absent_inside():  assert binary_search([1, 3, 5], 4) == -1
def test_single_found():   assert binary_search([7], 7) == 0
def test_long_right():     assert binary_search(list(range(0, 20, 2)), 18) == 9
```

```python
# reduce.py
"""Greedy reduction: keep the test that adds most uncovered arcs, repeat."""
import re, sys
from coverage import CoverageData
data = CoverageData(sys.argv[1]); data.read()
src = [f for f in data.measured_files() if f.endswith("search.py")][0]
arcs = {}
for ctx in sorted(c for c in data.measured_contexts() if c):
    data.set_query_contexts([re.escape(ctx)])
    arcs[ctx.split("::")[-1].split("|")[0]] = set(data.arcs(src) or [])
everything = set().union(*arcs.values())
kept, covered = [], set()
while covered != everything:
    best = max(arcs, key=lambda t: (len(arcs[t] - covered), -list(arcs).index(t)))
    print(f"keep {best:20} +{len(arcs[best] - covered)} arcs")
    kept.append(best); covered |= arcs[best]
print("dropped:", ", ".join(t for t in arcs if t not in kept))
print(f"{len(kept)} of {len(arcs)} tests keep all {len(everything)} arcs")
```

```text
$ pytest -q --cov=search --cov-branch --cov-context=test
$ python reduce.py .coverage
keep test_absent_inside   +11 arcs
keep test_found_left      +2 arcs
dropped: test_empty, test_found_middle, test_found_right, test_long_right, test_single_found
2 of 7 tests keep all 13 arcs
```

(An arc is coverage.py's name for a jump between lines, including entry to and exit from the function. Arcs are not the edges of a hand-drawn control-flow graph; that both counts are 13 for this function is a coincidence.) Two tests keep full branch coverage. Now plant a fault, `high = len(arr)` instead of `len(arr) - 1`:

```text
$ pytest -q                                          # full suite, output trimmed
FAILED test_search.py::test_empty - IndexError: list index out of range
1 failed, 6 passed in 0.01s
$ pytest -q -k "absent_inside or found_left"         # reduced suite
2 passed, 5 deselected in 0.00s
```

Only the empty-list test reads past the end of the list; it added no new arc, so the reduction dropped it.

**Where the inputs come from.** Per-test coverage comes from the coverage tool's context recording (coverage.py `--cov-context=test`, JaCoCo per-test sessions); fault history comes from the issue tracker linked to the tests that failed.

## When to use it — and when not

- **Use reduction** when a suite is too slow to run on every change, and keep the full suite for a nightly run.
- **Do not reduce on coverage alone** for code where a missed fault is expensive; a mutation score ([Mutation Coverage](mutation.md)) shows what a reduction loses.

## Limitations

Coverage-preserving reduction keeps the elements, not the inputs that reach them: two tests can take the same branches with different values, and only one of them may reveal a fault.

## How solid is this?

- **Where it comes from.** The removal and update rules are practitioner rules. The evidence comes from the regression-testing survey by Yoo and Harman {% cite yoo2012regression %}.
- **What is contested.** The studies disagree. Wong and colleagues found that minimised suites lost almost no fault detection; Rothermel and colleagues found the loss above 50% for more than half of 1,000 suites on the Siemens programs, and 8.9% on a larger program, against 18.1% for random reduction to the same size {% cite yoo2012regression %}. The survey traces the difference to the programs, the suites and the faults studied.
- **What we do not hold.** The primary minimisation studies themselves; the figures above are as reported in the survey.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
