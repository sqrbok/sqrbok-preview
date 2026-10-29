---
parent: Testing Overview
nav_order: 3
title: Test Doubles
layout: default
---

# Test Doubles

**A test double is an object or function that stands in for a real implementation in a test, the way a stunt double stands in for an actor** {% cite winters2020swe %}. Doubles let a unit test run without the slow, external or unpredictable parts the unit depends on: a database, a payment service, the network, the clock.

## 1. Three ways to use a double

*Software Engineering at Google* names three techniques {% cite winters2020swe %}:

- **Faking**: a fake is a lightweight implementation of an API that behaves like the real one but is unfit for production, such as an in-memory database.
- **Stubbing**: a stub gives behaviour to a function that has none of its own, by hard-coding the values it returns.
- **Interaction testing**: the test checks *how* a function was called (whether, how often, with which arguments) without running the real implementation. The object that records and checks the calls is usually called a **mock**.

## 2. Example: a checkout with three doubles

(Constructed example.) `checkout` adds up an order using a price service and e-mails the customer:

```python
def checkout(order, prices, mailer):
    """Add up an order and e-mail the customer its total."""
    total = sum(prices.price_of(item) * qty for item, qty in order.items())
    mailer.send("Your total is %.2f" % total)
    return total
```

The tests below use Python's `unittest.mock.Mock` and a hand-written fake. Note the two meanings of "mock": the *technique* (a double that checks how it was called) and the Python class `Mock`, which can play any role. Below, `Mock()` is a stub when it is given a return value, a mock when the test asserts on its calls, and a mere placeholder when it is passed as a mailer nobody checks:

```python
from unittest.mock import Mock
from checkout import checkout

class FakePrices:
    """A fake: a small working price list kept in memory."""
    def __init__(self, table):
        self.table = table
    def price_of(self, item):
        return self.table[item]

def test_total_with_a_stub():
    prices = Mock()
    prices.price_of.return_value = 2.50      # stub: canned answer
    assert checkout({"pen": 4}, prices, Mock()) == 10.0

def test_total_with_a_fake():
    prices = FakePrices({"pen": 2.50, "pad": 4.00})
    assert checkout({"pen": 2, "pad": 1}, prices, Mock()) == 9.0

def test_customer_is_mailed_once():
    mailer = Mock()
    checkout({"pen": 2}, FakePrices({"pen": 2.50}), mailer)
    mailer.send.assert_called_once_with("Your total is 5.00")
```

All three pass under pytest 9.1.1. When the `mailer.send` line is removed from `checkout`, only the third test fails, with `Expected 'send' to be called once. Called 0 times.` The stub and the fake check the returned total; only the mock notices a missing side effect.

## 3. Which double to choose

Google's order of preference is the real implementation first, if it is fast, deterministic and has simple dependencies; then a fake; and stubbing or interaction testing only when neither works {% cite winters2020swe %}. A real price service that answers in microseconds needs no double at all. Once a unit is tested with the real classes it depends on, the test is closer to [integration testing](testing_levels.md), which is fine when it stays fast.

## When to use it — and when not

- Use a double when the real dependency is slow, non-deterministic, costly (it sends real e-mails or charges cards) or not yet written.
- Do not stub what you could run: every stub encodes an assumption about the real code that can silently become false.

## Limitations

Doubles make tests brittle when overused. Overusing stubs makes tests unclear and exposes implementation details, and interaction tests break whenever the code is refactored to call its dependencies differently, even if the behaviour is unchanged, so Google advises avoiding interaction testing when possible {% cite winters2020swe %}. A double can also be wrong: if `FakePrices` returned euros while the real service returns cents, every test would pass and production would charge 100 times too much.

## How solid is this?

- **Where it comes from.** Definitions and advice from the test-doubles chapter of *Software Engineering at Google* {% cite winters2020swe %}; the runs were made for this page.
- **What is contested.** Vocabulary: other authors and frameworks also use "spy" (a double that records calls), and frameworks name their classes freely, as Python's `Mock` shows.
- **What we do not hold.** No controlled study of how doubles affect fault detection is in the review.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
