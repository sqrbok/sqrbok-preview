---
title: Property-Based Testing of Web Services
parent: Random Testing
nav_order: 8
layout: default
---

# Property-Based Testing of Web Services

**Property-based testing of a web service** generates many random requests, sends each to the running service, and checks rules that every response must satisfy, such as "no server error" or "the response matches its schema". It applies the ideas of [Property-Based Testing](property-based.md) to an HTTP API instead of a function.

## 1. Worked example: an order service

This template for an order service, to adapt rather than run as is, generates JSON bodies, calls the service, and checks two properties on every response: the status code (no 5xx, and 2xx for a valid order) and the response schema (the expected fields, a non-negative total):

```python
import requests
from hypothesis import given, settings, strategies as st

BASE_URL = "http://localhost:8000"          # the service under test

order = st.fixed_dictionaries({
    "item": st.sampled_from(["book", "pen", "lamp"]),
    "quantity": st.integers(min_value=1, max_value=1000),
})

@settings(max_examples=200, deadline=None)  # no per-example time limit: network calls are slow
@given(order)
def test_orders_are_handled(body):
    r = requests.post(f"{BASE_URL}/orders", json=body, timeout=5)
    assert r.status_code < 500               # no server error, ever
    assert 200 <= r.status_code < 300        # a valid order is accepted
    data = r.json()
    assert set(data) >= {"id", "total"}      # the response schema
    assert data["total"] >= 0
```

Run it with `pytest` while the service is running. For a service with state, reset it before each example, or Hypothesis may fail to replay and shrink a failure. `fixed_dictionaries` builds a dict with the given keys, each value from its own strategy; `sampled_from` picks one of the listed values. To test invalid input too, generate it separately (for example `quantity` from `st.integers(max_value=0)`) and assert that the service rejects it with a 4xx status, never a 5xx.

If the service crashed on some quantity, Hypothesis would report the request body that triggered the 5xx, shrunk to the smallest failing quantity, which is the input to put in a bug report.

## 2. Properties worth checking

Good properties for a service come in a few kinds:

- **Status codes**: a valid request gets a 2xx answer; an invalid one gets a 4xx; nothing ever gets a 5xx.
- **Schema**: every response has the documented fields and types.
- **Echo**: the response repeats what was sent, for example `data["item"] == body["item"]` and `data["quantity"] == body["quantity"]`.
- **Round trip**: what you create with `POST` you can read back with `GET`.

## When to use it — and when not

- **Use it** to probe an API for inputs that crash it or break its contract, before users find them.
- **Use it** with a stated input range, taken from the specification or the API documentation, so that generated requests are valid unless you mean them not to be.
- **Do not use it** to measure reliability: the generator, not real traffic, decides what is sent (see [Fundamentals](fundamentals.md)).

## Limitations

Network calls make each example slow, so far fewer examples fit in a run than for a pure function. A service with state can make a failure impossible to replay: Hypothesis shrinks by re-running smaller inputs, which only works if each run starts from the same state.

## How solid is this?

- **Where it comes from.** The method is property-based testing {% cite claessen2000quickcheck maciver2019hypothesis %}; differential and round-trip properties are the kinds practitioners use most {% cite goldstein2024pbt %}.
- **What we do not hold.** The literature review has no study of property-based testing against web services specifically; this page applies the general method.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
