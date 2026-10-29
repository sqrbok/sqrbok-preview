---
title: Boundary Testing Strategies
parent: Input Domain Testing
nav_order: 3
layout: default
---

# Boundary Testing Strategies

A **boundary testing strategy** decides how many ON and OFF points to test per boundary, and so which [boundary faults](boundary.md) it can detect.

## 1. The strategies

They differ in points per boundary {% cite tian2005sqe %}.

| Strategy | Points | Detects |
|---|---|---|
| Weak 1×1 | 1 ON + 1 OFF per boundary condition | closure, shift, missing; tilt only sometimes |
| Weak N×1 | for a boundary over N inputs: N ON points spread along it, plus 1 OFF point beside their centre | also tilt |
| Extreme point combinations (corners) | under, min, max, over for every input, all combined, plus one nominal point (the range midpoint of every input) | 4<sup>*n*</sup> + 1 tests; misses slanted boundaries |

**Min** and **max** are an input's smallest and largest valid values, **under** and **over** lie one step outside. For a parcel whose width and height are each valid from 1 to 100 cm, they are 0, 1, 100 and 101; one extreme-point row is (width 101, height 0). These combinations test only corners, never a slanted boundary such as `x + y ≤ 10`, and combine invalid values, against the advice on [Combining Techniques](integration.md).

Two common textbook schemes test one input at a time while the others stay nominal, plus one all-nominal test. **Basic BVA** takes min, min+, max− and max (one step inside: 2 and 99): 4*n* + 1 tests for *n* inputs. **Robust BVA** adds under and over: 6*n* + 1 {% cite jorgensen2013software %}, so 9 and 13 tests for the parcel. Neither tests two edges together or an edge *inside* a range, such as a fare change at age 18; the [worked case](worked-case.md) shows a fault there that both miss.

## 2. Worked example: a boundary over two inputs

A delivery service accepts parcels with `width + height ≤ 100` cm, a line in the width–height plane.

- **Weak 1×1:** an ON point on the line, (50, 50), and an OFF point one step to the rejected side, (50, 51). It detects a shift such as `≤ 99`, but not the tilted boundary `width + 2·height ≤ 150`: its constant keeps it through (50, 50), a pure tilt, and it still rejects (50, 51).
- **Weak N×1:** two ON points far apart on the line, (10, 90) and (90, 10), and one OFF point beside their centre, (50, 51); for three inputs the boundary is a plane, and its three ON points must not lie on one line. The tilted code rejects (10, 90), since 10 + 180 > 150: the tilt is found.

## 3. Non-numeric and decimal inputs

A name field that must hold 1–50 characters has ON points at lengths 1 and 50 and OFF points at 0 and 51; a list that must hold at most 10 items is tested with 10 and 11. For decimal values the step ε is the smallest unit the specification uses: for prices in euros, ε = 0.01, so a discount "from 100.00" is tested with 100.00 (ON: discount applies) and 99.99 (OFF: no discount).

**Example: a REST request.** `POST /ticket` takes `age` (integer, 0–120) and `ticketType` (`single` or `return`) and answers with `fareClass`, the oracle. The 14 age tests of [Boundary Value Analysis](boundary.md) go through the API: −1 and 121 expect 400 Bad Request, the others 200 OK. Add three format tests for `age` (`"abc"`, `null`, field missing) and three for `ticketType` (an unknown value, `null`, missing), each with the other field valid; one valid-age test carries `return`. In all, 20 tests. If inclusiveness is unspecified, ask, and meanwhile test both neighbours.

**Example: a boundary that moves with other inputs.** In `is_valid_date(year, month, day)` the day's upper limit is 31, 30, 29 or 28, depending on the month and on whether the year is a leap year. Each limit gets its own ON/OFF pair, valid then invalid: (2024, 1, 31)/(2024, 1, 32), (2024, 4, 30)/(2024, 4, 31), (2024, 2, 29)/(2024, 2, 30) and (2023, 2, 28)/(2023, 2, 29), plus the century rule, (2000, 2, 29) valid but (1900, 2, 29) invalid. Testing `day` alone at 1 and 31 misses all of them.

**Where the boundaries come from.** Real limits sit in the API schema (`minimum`, `maxLength`, `maxItems`), the database column types and the validation code.

## When to use which — and when not

- **Use Weak 1×1** for single-input boundaries.
- **Use Weak N×1** for multi-input boundaries.
- **Do not rely on extreme point combinations**: they cost 4<sup>*n*</sup> and miss slanted boundaries.

## How solid is this?

- **Where it comes from.** Weak 1×1, Weak N×1 and extreme point combinations follow Tian, chapter 9 {% cite tian2005sqe %}; basic and robust BVA follow Jorgensen {% cite jorgensen2013software %}.
- **What we do not hold.** No measured comparison of the strategies on real faults.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
