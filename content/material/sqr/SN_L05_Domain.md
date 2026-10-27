---
title: "SN L05: Input Domain Testing"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 9
redirect_from: /content/verif/domain/SN_L05_Domain.html
layout: default
page_type: study-notes
---

# SN L05: Input Domain Testing

The lecture asks how to choose a handful of test inputs when the full input space is far too large to try. Its answer is a set of black-box techniques that divide inputs by what the specification says should happen, then test chosen points from each part. The [revision questions](RQ_L05_Domain.md) test this lecture.

## 1. A test case is an input with an expected result, and testers avoid failing inputs

A **test input** is the set of values given to the program. A **test case** is a test input together with its **expected output**. The **oracle** decides whether the actual output matches: a person, an assertion or a reference implementation. A **test suite** collects test cases. Black-box techniques derive both from the specification; white-box techniques use the code. Testers tend to pick inputs they expect to succeed, a *positive test strategy* or positive test bias {% cite teasley1994positive %}, so invalid inputs must be planned: otherwise invalid classes and OFF points (§3) stay untested, and a forgotten check has no branch of its own, so coverage can reach 100% without it. Testing with invalid inputs is called **negative testing**.

→ [Input Domain Testing](../../verif/domain/index.md)

## 2. Equivalence partitioning tests one representative per class

The input domain is split into classes the specification treats alike. A partition must be **complete** (every input belongs to a class) and **disjoint** (to one class only), with invalid classes listed next to valid ones. Three heuristics find classes: **ranges** (age groups 0–12, 13–19, 20–64, 65–120), **enumerations** (one class per listed value, such as user roles admin, manager, user and guest), and **error types** (one class per kind of error, such as "file not found"). For example, for an age-based fare with child 0–12, teen 13–19, adult 20–64 and senior 65–120, six classes, including "below 0" and "above 120", need six tests.

With several inputs, **weak EP** uses each class at least once, which needs as many tests as the largest class count of any input, while **strong EP** tries every combination, the product of the counts: for inputs with 3, 4 and 2 classes, 4 tests against 24. Both counts use **valid** classes only; each invalid class adds one test of its own, with all other inputs valid, so that a rejection can be traced to one input. For example, if the age range has 4 valid and 2 invalid classes and ticket type has 2 valid and 1 invalid class, weak EP needs max(4, 2) + 3 = 7 tests and strong EP 4 × 2 + 3 = 11. Format errors (not a number, missing, null) add one test each: with three for age and two (missing, null) for ticket type, weak EP needs 7 + 5 = 12 and strong EP 11 + 5 = 16. Weak EP assumes a fault depends on one input at a time; strong EP pays for catching faults that need two classes together. The category-partition method turns this into a procedure with categories, *choices* (its word for classes) and constraints, written in a Test Specification Language (TSL) {% cite ostrand1988category %}.

→ [Equivalence Partitioning](../../verif/domain/equivalence.md)

## 3. Boundary value analysis tests on and beside every class edge

One representative per class never looks at a class edge, where typical faults hide {% cite tian2005sqe %}:

- a **closure** fault puts the boundary point on the wrong side: `if age > 65` where the specification says `age ≥ 65`;
- a **shift** fault moves the boundary: `if age ≥ 70`;
- a **missing** boundary is not checked at all: no upper limit on age.

Here a **boundary** is one class limit, closed or open relative to the class whose limit it is: "child ≤ 12" and "teen ≥ 13" are two boundaries that happen to be neighbours. Tian calls these the ON/OFF points {% cite tian2005sqe %}: his formalism places an **ON point** on the boundary and an **OFF point** one step ε away, outside a closed boundary (`≤`, `≥`) and inside an open one (`<`, `>`). The step is 1 for integers and, for decimals, the smallest unit used, such as 0.01 for prices. The pair detects closure and shift faults: with `age > 65` or with `age ≥ 70`, the ON point 65 wrongly gets the adult fare. The fault type is read from the code: on integers, `age > 65` accepts exactly the same values as `age ≥ 66`, so a closure fault and a one-step shift cannot be told apart by any test, and the same ON/OFF pair catches both. It also detects a missing boundary, because the ON and OFF points should be treated differently and are not. For the age fare, the ON points are the eight class limits 0, 12, 13, 19, 20, 64, 65 and 120, and each one's OFF point is the nearest value outside its class: 0 → −1, 12 → 13, 13 → 12, and so on up to 120 → 121. Neighbouring limits share their values, so ten values (−1, 0, 12, 13, 19, 20, 64, 65, 120, 121) cover all eight limits. For an open range such as 1 < x < 100, the ON points are the limits themselves, 1 and 100, which must be rejected, and the OFF points lie inside, 2 and 99, which must be accepted. This strategy, one ON and one OFF point per boundary, is **Weak 1×1**; its "weak" (one set of points per boundary) is unrelated to weak EP.

Other schemes spend more points. **Weak N×1** (N = the number of inputs in the boundary condition) places N ON points on the boundary and one OFF point just outside, next to the midpoint of the ON points; it also detects a tilted boundary. For an open boundary such as `width + height < 100`, the ON points lie on the line and are rejected, and the OFF point lies one step inside. While one boundary is tested, all other inputs stay at valid, nominal values. For `width + height ≤ 100`, the ON points (10, 90) and (90, 10) lie far apart on the line and the OFF point (50, 51) lies just outside ((51, 50) would do as well); a tilted implementation such as `width + 2·height ≤ 150` wrongly rejects (10, 90). **Extreme point combinations** try under, min, max and over for every input, all combined (an explicit exception to the rule of testing invalid values one at a time), which tests the corners of the input box, plus one nominal point: 4<sup>*n*</sup> + 1 tests. A textbook alternative varies one input at a time through min, min+ (one step above min), max− (one step below max) and max, with the others at a nominal value from the middle of the range, plus one all-nominal test: 4*n* + 1 tests. The first only tests corners of the input box and misses slanted boundaries; the second checks each range alone and never two edges together.

For a REST service, a name of 1–50 characters is tested with ON lengths 1 and 50 and OFF lengths 0 and 51, and next to those boundaries come the classes "field missing", "null" and "wrong type", such as `"abc"` where a number is expected. These are classes without an edge: each needs one test, with the other fields valid, and no ON or OFF points. For a REST service, "rejected" means a 4xx status, usually 400, with an error body that names the field.

Boundary testing has limits {% cite tian2005sqe %}: it assumes case-like processing without input-changing loops, coincidental correctness can hide a wrong boundary, and ε is hard to choose for floating point.

→ [Boundary Value Analysis](../../verif/domain/boundary.md) · [Boundary Testing Strategies](../../verif/domain/boundary-strategies.md)

## 4. Decision tables make every combination of conditions explicit

When several conditions decide the outcome together, a **decision table** has four parts: condition rows, action rows, rule columns, and the entries that fill them, with a dash for "don't care" {% cite beizer1995black %}. Beizer's eight steps are: list the actions; list the conditions with their values; calculate the number of combinations, the product of the conditions' value counts (2<sup>*n*</sup> for *n* yes/no conditions); fill the columns with all combinations; specify the expected outputs per rule; identify common actions and consolidate, merging two rules that have the same actions and differ in exactly one condition, which becomes a dash, until no merge is possible; verify the checksum; develop one test case per rule. The **checksum** is the sum of the original combinations each merged rule covers; it must equal the number of combinations from step 3.

In the lecture's login example (username known, password correct, account active), only one combination logs in and one shows "account inactive"; a wrong password gives "invalid credentials" whatever the account state, and an unknown username does so whatever else is true. For yes/no conditions, a merged rule with *k* dashes covers 2<sup>*k*</sup> combinations (with multi-valued conditions, multiply the dashed conditions' value counts), and the merged rules cover 1 + 1 + 2 + 4 = 8, so four tests suffice. The consolidated table:

| | R1 | R2 | R3 | R4 |
|---|:-:|:-:|:-:|:-:|
| Username known? | Y | Y | Y | N |
| Password correct? | Y | Y | N | – |
| Account active? | Y | N | – | – |
| Action | log in | "inactive" | "invalid" | "invalid" |
| Combinations covered | 1 | 1 | 2 | 4 |

Decision tables fit poorly when event order matters, outputs vary continuously, or 2<sup>*n*</sup> rules become unmanageable; non-functional properties such as performance are outside them altogether. A careless merge that lets the "inactive" rule also cover a wrong password would make the total 9, which the checksum exposes. The checksum cannot, however, tell an overlap plus a gap from a correct table, since both can give the right sum; only checking that every original combination falls under exactly one merged rule does.

→ [Decision Tables](../../verif/domain/decision-tables.md)

## 5. Most failures need only one or two interacting inputs

A **combination strategy** decides how many combinations of input values to test. **Each choice** uses every class of every input at least once (a *choice* is a class, as in category-partition); **pairwise** (2-way) makes every pair of values from any two inputs appear together in some test; **t-way** extends this to every combination of values of any *t* inputs, so 3-way covers all triples (in classification-tree tools these are the minimality criterion and *t*-wise generation); **all combinations** tries everything. Kuhn and colleagues traced real failures in four systems (a NASA distributed system, medical device software, the Mozilla browser and the Apache web server) to the number of interacting inputs (parameters) needed to trigger them: 97% for the medical devices, 93% for the NASA system, 76% for Mozilla and 70% for Apache needed only one or two, and the maximum any failure needed was six {% cite kuhn2004fault %}. That makes pairwise an effective default.

A pairwise suite needs at least as many tests as the product of the two largest value counts, because those two inputs alone have that many value pairs, and each test covers only one of them. For inputs with 4, 4, 3, 3 and 2 values, pairwise needs at least 4 × 4 = 16 tests instead of 288. For yes/no inputs the bound is 2 × 2 = 4, and with up to three inputs 4 tests really suffice: TTT, TFF, FTF and FFT give every pair of inputs all four of TT, TF, FT and FF. Why not more inputs with 4 tests? With 4 rows, every pair of columns must show TT, TF, FT and FF exactly once, so each column has two Ts and two Fs. There are only C(4, 2) = 6 such columns, and they form 3 complementary pairs. A column and its complement never give TT, and two identical columns never give TF, so at most one column from each pair fits: 3 inputs. With more inputs the minimum grows slowly, because N tests can handle at most C(N − 1, ⌊N/2⌋ − 1) yes/no inputs: 5 tests for 4 inputs, 6 for up to 10, 7 for up to 15 and 8 for up to 35 {% cite kleitman1973families %}, so even 34 switches need only 8 pairwise tests instead of 2<sup>34</sup>. Generators such as PICT or ACTS find suites close to these minimums.

Limits: wrong classes carry into every combination, no expected results are supplied, and invalid values need tests of their own.

→ [Combining Techniques](../../verif/domain/integration.md)

## 6. The techniques are used together, in order

Workflow: partition, add boundaries, tabulate combined conditions, choose a combination strategy. For inputs with many interacting aspects, the [Classification Tree Method](../../verif/domain/classification-tree.md) models all of this in one diagram: a tree of aspects and their classes, and a table that picks one class per aspect for each test. Bachelor students need only this idea.

→ [Combining Techniques](../../verif/domain/integration.md)

## Where this goes next

The next lecture drops the specification-based choice of inputs and generates them at random: [Random Testing](SN_L06_Random.md) asks what random inputs find that partitions do not. The input domain lab applies this lecture to the lab's REST API, a pricing service with planted boundary faults: partition its inputs, choose ON and OFF points, and record each test's request, expected and actual result, and verdict.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
