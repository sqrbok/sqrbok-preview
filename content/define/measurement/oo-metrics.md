---
title: Object-Oriented Metrics
parent: Measurement
nav_order: 8
layout: default
---

# Object-Oriented Metrics

**Object-oriented metrics** measure classes rather than functions: how much a class does, how deep it sits in an inheritance hierarchy, and how strongly it depends on other classes. The best known set is the six metrics of **Chidamber and Kemerer (CK)** {% cite chidamber1994metrics %}, still the most studied object-oriented suite {% cite mishra2021thresholds %}.

## 1. The six CK metrics

| Metric | What it counts | A high value suggests |
|---|---|---|
| **WMC**, weighted methods per class | sum of the complexities of the class's methods; with every weight set to 1, the number of methods | the class does too much and is costly to test |
| **DIT**, depth of inheritance tree | steps from the class up to the root of its hierarchy | behaviour is spread over many ancestors |
| **NOC**, number of children | immediate subclasses | a change to the class affects many others |
| **CBO**, coupling between objects | other classes it uses or is used by | changes ripple; the class is hard to reuse alone |
| **RFC**, response for a class | its own methods plus the methods they call directly | many paths to test when a message arrives |
| **LCOM**, lack of cohesion in methods | pairs of methods that use no attribute in common (*P*), minus pairs that share at least one (*Q*); 0 if *Q* ≥ *P* | the class holds unrelated responsibilities and could be split |

## 2. Example: computing them for one class

```python
class Cart:
    def __init__(self, pricing):
        self.items = []
        self.pricing = pricing            # a Pricing object

    def add(self, item):
        self.items.append(item)

    def total(self):
        return sum(self.pricing.price(i) for i in self.items)

    def checkout(self, payment):          # a Payment object
        payment.charge(self.total())
```

Counting by hand, with every method weighted 1 and calls to Python built-ins left out:

- **WMC = 4**: `__init__`, `add`, `total`, `checkout`.
- **DIT = 1** and **NOC = 0**: `Cart` inherits only from `object` and has no subclasses. This page counts `object` as the root, at depth 0, as Java tools count `Object`; some Python tools start at `Cart` and report 0, so state the convention.
- **CBO = 2**: it uses `Pricing` and `Payment`, and no other class uses it here.
- **RFC = 6**: its 4 methods plus `Pricing.price` and `Payment.charge`.
- **LCOM = 0**: of the 6 method pairs, 3 share at least one attribute (`__init__`–`add`, `__init__`–`total`, `add`–`total`) and 3 do not (each pair with `checkout`, which reaches the data only through `total`). *P* = 3, *Q* = 3, so *P* − *Q* = 0.

Change one counting rule and the numbers change: count `list.append` and `sum` as called methods and RFC becomes 8; weight methods by their cyclomatic complexity and WMC becomes the sum of those complexities: `radon cc` gives `total` 2 (the generator expression counts as a decision) and the other three methods 1 each, so WMC = 5. Tools differ on exactly these choices, which is why a CK value is comparable only within one tool.

## 3. Thresholds: when is a value too high?

Practitioners want a red line: "CBO above *x* is a problem". A mapping study of 45 papers on deriving such **thresholds** found that most derive them statistically from the distribution of values in a corpus of systems, and that a considerable number never check the thresholds against a quality outcome such as faults {% cite mishra2021thresholds %}. The thresholds also differ between systems and tools. A threshold taken from someone else's corpus says how unusual a class is, not whether it is faulty.

## When to use them — and when not

- **Use** CK metrics to rank the classes of one system and pick the ones to review or refactor first.
- **Use** coupling (CBO) as the first signal: in El Emam's 1999 study the only two metrics that improved fault prediction after size control were coupling metrics {% cite elemam1999validation %}.
- **Do not** apply a fixed threshold from another system without checking it against your own defect data.
- **Do not** compare classes of very different sizes without controlling for size.

## How solid is this?

- **Where it comes from.** The definitions are Chidamber and Kemerer's {% cite chidamber1994metrics %}; LCOM in particular has several later variants with different formulas.
- **What is contested.** Much of what CK metrics predict is class size. Once size was controlled, none of the CK metrics in one C++ system remained associated with faults {% cite elemam2001confounding %}, and in another only coupling metrics helped prediction {% cite elemam1999validation %}. [Do Code Metrics Predict Quality?](evidence.md) collects the newer evidence.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
