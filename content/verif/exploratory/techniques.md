---
title: Charters, Heuristics and Tours
parent: Exploratory Testing
nav_order: 1
layout: default
---

# Charters, Heuristics and Tours

**A charter, a heuristic and a tour are three tools that tell an exploratory tester what to try next:** the charter sets the mission of a session, a heuristic suggests the next input, and a tour names a strategy for moving through the product. None of them lists test steps; they direct attention.

## 1. The charter: a mission, not a script

Hendrickson's charter template has three slots {% cite hendrickson2013explore %}:

> **Explore** *target* **using** *resources* **to discover** *information*.

For example, a session on an online shop might have the charter *Explore the coupon system using the Obsessive-Compulsive tour (repeat the same action) and the Never-and-Always heuristic (a price is never negative) to discover pricing errors.* On the [five-level scale](session-based.md), a charter with only a goal is level 2; naming starting points, such as a tour or heuristic, moves it to level 3, medium exploration, where this charter sits. The **target** is the part of the product to explore; the **resources** are what the tester brings to it: tools, test data, a configuration, or a named heuristic or tour; the **information** names what counts as a finding. A second charter, with a tour as its resource: *Explore checkout using the Saboteur tour (cut the network during payment) to discover lost or double payments.* It names a starting point too, so it is also level 3. A charter listing steps is a script.

## 2. Heuristics: where bugs tend to live

A heuristic is a rule of thumb for choosing inputs. Hendrickson's catalogue includes the following {% cite hendrickson2013explore %}:

- **Some, None, All** tries a set with some, zero and every element: no permissions, some, all.
- **Goldilocks** tries values too big, too small and just right, as boundary value analysis does.
- **Beginning, Middle, End** edits or inserts at each position of a list or text.
- **CRUD** creates, reads, updates and deletes every kind of entity, in unusual orders.
- **Follow the Data** traces one value from entry through storage to every screen and report that shows it.
- **Never and Always** challenges what the system should never or always do ("an order total is never negative").

The **Nightmare Headline Game** asks what headline a failure would produce, and points the next session there {% cite hendrickson2013explore %}.

Roughly: input fields suit Goldilocks; lists, Beginning, Middle, End; prices, Never and Always.

Observations of eleven industrial testers found them applying equivalence partitioning, boundary values and combinations *during* execution, without writing them down first {% cite itkonen2009manual %}. The heuristics are the same test design ideas, used at the moment of testing.

## 3. Tours: a strategy for a whole session

A **tour** sets one strategy for a whole session, where a heuristic suggests the next single input. Whittaker groups his tours into six districts of a city; they are listed and explained on [Exploratory Testing Tours](tours.md) {% cite whittaker2009exploratory %}.

For a full session, from charter to report, see [Exploratory Session Report](session-report.md).

## When to use it — and when not

Use these tools when:

- a feature is new or changed and the specification is thin;
- you need test ideas fast.

Do not rely on them alone when:

- a regulator requires a predefined, traceable test record;
- the tester does not know the domain: heuristics produce inputs, but judging the output needs domain knowledge.

## Limitations

Heuristics and tours make a tester's choices explicit, but they do not guarantee any coverage: two testers with the same charter will try different things. Coverage is known only from the session report ([Session-Based Test Management](session-based.md)).

## How solid is this?

- **Where it comes from.** Practitioner books {% cite hendrickson2013explore whittaker2009exploratory %}; the observation that testers apply design techniques dynamically comes from an industrial study of eleven testers {% cite itkonen2009manual %}.
- **What is contested.** Which heuristics work best has not been compared experimentally; the catalogues are distilled experience.
- **What we do not hold.** We have summaries of the two books, not their full text.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
