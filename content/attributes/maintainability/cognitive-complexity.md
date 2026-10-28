---
title: Cognitive Complexity
parent: Maintainability
grand_parent: Quality Attributes
nav_order: 4
layout: default
---

# Cognitive Complexity

**Cognitive complexity** is a per-method score of how hard code is for a person to understand: it adds one for each break in the linear flow of the code and adds more when such breaks are nested {% cite campbell2018cognitive %}. Campbell designed it at SonarSource because [cyclomatic complexity](../../define/measurement/examples.md) counts paths for testing, not effort for reading.

## 1. The three rules

The score follows three rules {% cite campbell2018cognitive %}:

1. **Ignore readable shorthand.** The method itself and null-coalescing operators such as C#'s `?.` and `??` add nothing.
2. **Add one for each break in linear flow:** `if`, `else if` (Python's `elif`), `else`, ternary operator, `switch`, each loop, `catch`, jumps to a label, each sequence of like Boolean operators (`a and b and c` counts once), and each method in a recursion cycle.
3. **Add the nesting level** for a flow break that sits inside others: an `if`, ternary, `switch`, loop or `catch` at nesting depth *d* costs 1 + *d*. The depth goes up by one inside each `if`, `else if`, `else`, ternary, `switch`, loop and `catch`, and inside nested methods and lambdas. `else` and `else if` add one but never a nesting extra. The method itself adds no nesting, so code extracted into its own method starts again at depth 0.

A sequence of like Boolean operators adds +1 even when it is a single operator: `a and b` adds 1, and so does `a and b and c`; `a and b or c` adds 2.

Two rules make the score differ from cyclomatic complexity (CC). A whole `switch` costs 1, while CC adds one per case. And nesting costs more each level deeper, while CC counts every decision the same.

## 2. Worked example

These three Python functions were scored with the `cognitive_complexity` package 1.3.0 {% cite cognitive_complexity_py %} and with radon 6.0.1 for CC, since radon does not compute cognitive complexity {% cite radon_docs %}; the scores below are the tools' real output.

```python
def flat(order):
    if order.paid and order.confirmed:   # +1 if, +1 and
        ship(order)
    if order.gift:              # +1
        wrap(order)
    if order.express:           # +1
        notify(order)

def nested(order):
    if order.paid and order.confirmed:   # +1 if, +1 and
        if order.gift:          # +2 (depth 1)
            if order.express:   # +3 (depth 2)
                notify(order)

def process_all(items):
    for item in items:          # +1
        if item.valid():        # +2 (depth 1)
            if item.ready():    # +3 (depth 2)
                process(item)
        else:                   # +1, no nesting extra
            skip(item)
```

| Function | Cyclomatic complexity (radon) | Cognitive complexity |
|---|:-:|:-:|
| `flat` | 5 | 4 |
| `nested` | 5 | 7 |
| `process_all` | 4 | 7 |

Radon counts each `and` and `or` as a decision, so `flat` and `nested` both have CC 5 (three `if`s and one `and`), and `process_all` has CC 4. CC cannot tell `flat` from `nested`; cognitive complexity can: `flat` reads top to bottom, while in `nested` a reader must hold four conditions in mind at once. The usual fix for `nested` is guard clauses (`if not order.paid: return`), which flatten it.

**For a whole project.** The `flake8-cognitive-complexity` plugin, built on the same package, scores every function: `flake8 --select=CCR --max-cognitive-complexity=0 .` (tested with flake8 7.4.1). On `shop.py` from [Rating Python Code with Radon](radon.md) it lists `delivery_fee` with 5 and `total` with 1.

## When to use it — and when not

Use cognitive complexity to find methods that are hard to read, and set a review threshold on it in the build. Use cyclomatic complexity, not cognitive complexity, to estimate how many tests a method needs, since CC counts the independent paths.

## Limitations

The score is defined per method; there is no agreed way to add it up into a system rating. The rules were designed by judgement, not derived from experiments on how people read code.

## How solid is this?

- **Where it comes from.** Campbell's definition {% cite campbell2018cognitive %}. She checked acceptance on SonarCloud: of 8,180 open-source projects, 22 projects actively managed cognitive-complexity issues, and 17 of these projects accepted the metric (fixed or kept its issues), an acceptance rate of 77%.
- **What is contested.** The evidence is developer acceptance in 22 projects, not a link to defects or maintenance time, and the study did not compare it with cyclomatic complexity. Campbell notes the sample is too small to generalise.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
