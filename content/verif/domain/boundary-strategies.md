---
title: Boundary Testing Strategies
parent: Input Domain Testing
nav_order: 3
layout: default
---

# Boundary Testing Strategies

A **boundary testing strategy** decides how many ON and OFF points to test per boundary, and so which [boundary faults](boundary.md) it can detect. It matters most for multi-input boundaries, and also applies to texts, lists and decimals.

## 1. The strategies

They differ in points per boundary {% cite tian2005sqe %}.

| Strategy | Points | Detects |
|---|---|---|
| Weak 1×1 | 1 ON + 1 OFF per boundary condition | closure, shift, missing; tilt only sometimes |
| Weak N×1 | for a boundary over N inputs: N ON points spread along it, plus 1 OFF point beside their centre | also tilt |
| Extreme point combinations (corners) | under, min, max, over for every input, all combined, plus one nominal point (a typical mid-range value for every input) | 4<sup>*n*</sup> + 1 tests; misses slanted boundaries |

Extreme point combinations only test corners of the input box, so a slanted boundary such as `x + y ≤ 10` is never touched. They deliberately combine invalid values, against the advice on [Combining Techniques](integration.md).

The extreme-point values are defined per input: **min** and **max** are the smallest and largest valid values, **under** and **over** lie one step outside them. For a parcel whose width and height are each valid from 1 to 100 cm, they are 0, 1, 100 and 101 (under, min, max, over); one row of the extreme-point combinations is (width 101, height 0).

A common textbook alternative tests each input at min, min+ and max− (one step inside min and max, here 2 and 99) and max while the others stay nominal, plus one all-nominal test: 4*n* + 1 tests for *n* inputs. It checks each range on its own but never two edges together.

## 2. Worked example: a boundary over two inputs

A delivery service accepts parcels with `width + height ≤ 100` cm, each side 1–100 cm: a line in the width–height plane.

- **Weak 1×1:** one ON point anywhere on the line, here (50, 50), and one OFF point one step away from it on the side the specification rejects, here (50, 51). It detects a shift such as `≤ 99`, but not the tilted boundary `width + 2·height ≤ 150`: its constant keeps it through (50, 50), a pure tilt, and it still rejects (50, 51).
- **Weak N×1:** with N = 2 inputs, two ON points far apart on the line (for three inputs the boundary is a plane, and the three ON points must not lie on one line), (10, 90) and (90, 10), and one OFF point beside their centre, (50, 51). The tilted code rejects (10, 90), since 10 + 180 > 150, so the tilt is found.

## 3. Non-numeric and decimal inputs

A name field that must hold 1–50 characters has ON points at lengths 1 and 50 and OFF points at 0 and 51, plus the separate classes "field missing", "null" and "wrong type" in a request; a list that must hold at most 10 items is tested with 10 and 11. For decimal values the step ε is the smallest unit the specification uses: for prices in euros, ε = 0.01, so a discount "from 100.00" is tested with 100.00 (ON: discount applies) and 99.99 (OFF: no discount).

**Example: a REST request.** `POST /fare` takes `age` (integer, 0–120) and `ticketType` (`single` or `return`), and answers with `fareClass` (`child`, `teen`, `adult`, `senior`), the oracle for the fare edges. With `ticketType` valid, the age tests are 0 and 120 (ON, expect 200 OK) and −1 and 121 (OFF, expect 400 Bad Request), plus `"abc"`, `null` and a request without `age` (each expect 400). The fare edges inside the range, 12/13, 19/20 and 64/65, are tested through the API in the same way, plus the class representatives 6, 16, 40 and 80: the 14 age tests of [Boundary Value Analysis](boundary.md). They are sent with `ticketType` = `single`, except one valid-age test sent with `return`, so both valid ticket types appear without an extra test. `ticketType` then gets its invalid tests, each with a valid age: an unknown value (its invalid value class, as on [Equivalence Partitioning](equivalence.md)) and its two format tests, missing and `null` (each expect 400). In all: 14 age tests + 3 age format tests + 3 `ticketType` tests = 20. If inclusiveness is unspecified, ask, and meanwhile test both neighbours.

## When to use which — and when not

- **Use Weak 1×1** for single-input boundaries.
- **Use Weak N×1** for multi-input boundaries.
- **Do not rely on extreme point combinations**: they cost 4<sup>*n*</sup> and miss slanted boundaries.

## How solid is this?

- **Where it comes from.** All strategies follow Tian, chapter 9 {% cite tian2005sqe %}; the 4*n* + 1 scheme is common textbook practice.
- **What we do not hold.** No measured comparison of the strategies on real faults.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
