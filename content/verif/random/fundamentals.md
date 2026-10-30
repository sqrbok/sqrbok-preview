---
title: Random Testing Fundamentals
parent: Random Testing
nav_order: 1
layout: default
vegalite: true
---

# Random Testing Fundamentals

**Random testing** selects each test input independently from a defined probability distribution over the input domain, runs it, and lets an oracle judge the output. "Random" means statistically independent sampling, not haphazard tests {% cite hamlet1984random %}.

## 1. When random tests predict reliability

Any random sampling with an automatic oracle can find bugs; a **reliability figure** needs three things {% cite hamlet1984random %}:

1. **A system-level test** of the whole program.
2. **Inputs drawn from the operational profile**: the frequencies with which real users supply each kind of input, sampled independently by a pseudorandom generator.
3. **An effective oracle**: something that decides pass or fail for every output without a person.

The tools on the next pages break one on purpose:

- [Feedback-directed testing](feedback.md) (extending inputs that ran correctly) and coverage-guided fuzzing (keeping inputs that reach new code) pick later inputs *because of* earlier results, so the draws are no longer independent.
- Property-based tools such as Hypothesis draw inputs independently and have an automatic oracle, but their distribution is deliberately skewed towards edge values; a confidence figure from them describes the generator, not real use.

They give up the reliability claim to find bugs faster.

## 2. What N passing tests let you claim

If the true failure rate is *F* (the chance that one input drawn from the profile fails; a bug confined to rarely used inputs has a small *F* even if it is easy to trigger), the probability that *N* independent tests reveal at least one failure is

$$
C = 1 - (1 - F)^N, \qquad N = \frac{\ln(1 - C)}{\ln(1 - F)}
$$

*C* is both the detection probability and, read backwards, the confidence: if *N* tests all pass, you can be *C* confident that the failure rate is below *F* {% cite hamlet1984random %}. For *F* = 0.001 and *C* = 0.95, *N* = ln 0.05 / ln 0.999 = 2,994.2, rounded up to 2,995 tests. The table gives *N*, always rounded up:

| Failure rate *F* | *C* = 0.90 | *C* = 0.95 |
|---|---|---|
| 0.01 | 230 | 299 |
| 0.001 | 2,302 | 2,995 |
| 0.0001 | 23,025 | 29,956 |

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 600, "height": 220,
  "layer": [
    {"data": {"sequence": {"start": 0, "stop": 30001, "step": 250, "as": "N"}},
     "transform": [{"calculate": "1 - pow(0.999, datum.N)", "as": "C"}, {"calculate": "'F = 0.001'", "as": "F"}],
     "mark": {"type": "line", "strokeWidth": 3},
     "encoding": {"x": {"field": "N", "type": "quantitative", "title": "Passing random tests (N)"},
                  "y": {"field": "C", "type": "quantitative", "title": "Confidence C", "scale": {"domain": [0, 1]}},
                  "color": {"field": "F", "type": "nominal", "title": "Failure rate", "scale": {"domain": ["F = 0.001", "F = 0.0001"], "range": ["#019546", "#1f5fa8"]}, "legend": {"symbolStrokeWidth": 3}},
                  "strokeDash": {"field": "F", "type": "nominal", "title": "Failure rate", "scale": {"domain": ["F = 0.001", "F = 0.0001"], "range": [[1, 0], [8, 4]]}}}},
    {"data": {"sequence": {"start": 0, "stop": 30001, "step": 250, "as": "N"}},
     "transform": [{"calculate": "1 - pow(0.9999, datum.N)", "as": "C"}, {"calculate": "'F = 0.0001'", "as": "F"}],
     "mark": {"type": "line", "strokeWidth": 3},
     "encoding": {"x": {"field": "N", "type": "quantitative"}, "y": {"field": "C", "type": "quantitative"},
                  "color": {"field": "F", "type": "nominal"}, "strokeDash": {"field": "F", "type": "nominal"}}},
    {"data": {"values": [{"y": 0.9}]}, "mark": {"type": "rule", "strokeDash": [4, 4], "color": "#757575"},
     "encoding": {"y": {"field": "y", "type": "quantitative"}}}
  ],
  "config": {"font": "Tahoma", "axis": {"labelFontSize": 13, "titleFontSize": 13}, "legend": {"labelFontSize": 13, "orient": "bottom"}}
}
```

_Confidence after N passing tests; dashed line at 90%. The dashed blue curve reaches 90% about ten times later than the solid green one: a ten times rarer failure needs about ten times more tests, as the table shows._

## 3. Worked example

The binary search below has a bug: the loop condition should be `lo <= hi`. A random test generates sorted lists and targets, and the oracle compares the result with Python's `in` operator.

```python
import random

def binary_search(arr, target):
    lo, hi = 0, len(arr) - 1
    while lo < hi:                      # bug: should be lo <= hi
        mid = (lo + hi) // 2
        if arr[mid] == target:   return mid
        elif arr[mid] < target:  lo = mid + 1
        else:                    hi = mid - 1
    return -1

random.seed(1)
for i in range(10_000):
    arr = sorted(random.sample(range(100), k=random.randint(0, 10)))
    target = random.choice(arr) if arr and random.random() < 0.5 else random.randint(0, 99)
    if (binary_search(arr, target) != -1) != (target in arr):
        print(f"test {i}: arr={arr} target={target}")
        break
```

With seed 1 (the generator's starting value, which makes the run repeatable; fuzzers use *seed* differently), Python 3 prints (the exact inputs depend on Python's `random` module) (counting from 0, so test 1 is the second):

```text
test 1: arr=[12, 26, 48, 57, 60, 83, 97] target=97
```

The second random test finds the bug. Whenever the search narrows to a single position, `lo == hi`, the condition `lo < hi` is false and the loop ends before looking there; searching for the last element always ends that way, and so do many other searches.

## 4. Oracles for random inputs

Random inputs have no hand-written expected values. From cheapest to most precise {% cite manes2019fuzzing claessen2000quickcheck %}:

- **Crash or hang**: any unhandled exception, crash or time-out is a failure. Free, but blind to wrong answers.
- **Sanitizers**: compiler-added checks that turn hidden memory and undefined-behaviour errors in C or C++ into crashes.
- **Contracts and assertions**: rules written into the code that a correct run never breaks, such as preconditions (what a caller must supply), postconditions (what a routine promises) and invariants (what stays true of an object between calls).
- **Properties**: rules every correct output satisfies, such as "the result is sorted" or "decoding the encoding gives the input back".
- **Differential testing**: compare with another implementation, such as an older version or a slow obvious algorithm, as the `in` operator was above.

## When to use it — and when not

- **Use it** when an oracle is cheap: a reference implementation, an inverse function, or a property.
- **Use it** for a release reliability figure, sampled from the operational profile.
- **Do not rely on it** for rare, specific inputs such as one boundary value: a uniform sample of 32-bit integers almost never hits exactly zero.

## Limitations

The reliability claim is only as good as the profile: if real usage concentrates on inputs the profile underweights, the prediction can be "arbitrarily" optimistic, and the error is always in the optimistic direction {% cite hamlet1984random %}. 

## How solid is this?

- **Where it comes from.** The formula and the three requirements are Hamlet's {% cite hamlet1984random %}; the arithmetic is standard probability.
- **What is assumed.** Independent tests and a fixed failure rate. Programs with state break independence: a test is then a sequence of inputs from reset, which [Random versus Partition Testing](partition.md) discusses.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
