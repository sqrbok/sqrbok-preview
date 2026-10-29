---
title: "SN L03: Verification Methods"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 5
redirect_from: /content/verif/overview/SN_L03_Verification.html
layout: default
page_type: study-notes
---

# SN L03: Verification Methods

The lecture asks *how do we gain confidence that software meets its requirements, when we cannot prove it?* Its answer is four verification methods (inspection, analysis, testing and demonstration), each with its own evidence and its own blind spots, used together. The lecture takes them in that order and ends with how to combine them. The [revision questions](RQ_L03_Verification.md) test this lecture.

## 1. Verification establishes confidence, not proof

Verification shows that a product meets its specified requirements: *did we build the product right?* Validation shows that it serves its intended purpose: *did we build the right product?* {% cite nasa2016sehandbook %} Neither proves a program correct. Verification establishes how much confidence the evidence justifies that the software does what it should and does not do what it should not. It is continuous, not a phase at the end: quality cannot be "tested in" after the fact, only checked. Exhaustive testing is out of reach for any real program; a function of two 64-bit integers already has 2<sup>128</sup> inputs.

→ [Verification Overview](../../verif/overview/index.md)

## 2. Four methods differ in whether the product runs and what evidence they produce

**Inspection** is examination by people; **analysis** is calculation or reasoning about a model; **testing** runs the product under controlled conditions and gathers detailed data; **demonstration** uses the product and confirms a capability without detailed data {% cite nasa2016sehandbook %}. Inspection and analysis do not need a running product, so they can start early; testing and demonstration need one. The method follows from what must be shown: a security analyzer checks on every commit that no SQL query is built by concatenation, a unit test checks that a 10% discount turns 40.00 into 36.00, and a demonstration checks that a first-time customer can complete a purchase.

→ [Four Verification Methods](../../verif/overview/techniques.md)

## 3. Inspection finds faults directly, and early

In an inspection, people other than the author examine a work product to find defects; it needs no running code, so it can check requirements and designs. Testing sees a failure and must then trace it back to a fault; inspection sees the fault itself, including omissions that no test would exercise. Reviews range, from most to least formal, through inspection, team review, walkthrough, pair programming, deskcheck and passaround to an ad hoc glance at a colleague's screen. In a walkthrough the author leads and explains the product; in an inspection a trained moderator who is not the author leads, and every participant prepares alone beforehand. In general, the more formal, the more defects found per line and the more effort {% cite wiegers2002peer %}. One telecommunications company found 16 to 20 defects per thousand lines by inspection against 3 with informal review {% cite wiegers2002peer %}.

The business case rests on cost: at the Jet Propulsion Laboratory, fixing a defect found in inspection cost 1/10 to 1/34 of fixing it in formal testing, and Hewlett-Packard estimated that inspections saved USD 21,454,000 a year {% cite dodd2003inspection %}. Inspection and testing are complements: in one industrial study about 90% of inspection faults were algorithm, documentation or checking faults, while testing mostly found function and algorithm faults {% cite zheng2006static %}.

→ [Inspection](../../verif/inspection/index.md), [Inspection Effectiveness](../../verif/inspection/effectiveness.md)

## 4. Analysis covers every path, so it must approximate

Analysis reasons about the code, or a model of it, without running it, and so covers every path at once {% cite chess2007static %}. The lecture names three kinds: **static analysis** of source code or binaries (automated inspection), **model checking** (exhaustive exploration of a model's states) and **theorem proving** (mathematical proof that code meets a specification). Rice's theorem sets the limit: no algorithm decides every non-trivial question about what programs do {% cite rice1953classes %}. An analysis that always terminates must therefore approximate. A **sound** analysis reports every real violation but raises false alarms (false positives); a **complete** one reports only real violations but misses some (false negatives) {% cite chess2007static %}. Here "positive" means "the tool reported something", not "good news". Practical bug finders are neither sound nor complete: they accept some false alarms and some missed bugs to keep their warnings useful.

→ [Verification by Analysis](../../verif/overview/analysis/index.md), [Soundness and Completeness](../../verif/static/limits.md)

## 5. Analysis proves properties: safety, liveness, fairness, temporal

A property is a precise condition on a state or a sequence of states {% cite mansourisamani2008program %}. A **safety** property says nothing bad ever happens (no deadlock, no order shipped twice); a **liveness** property says something good eventually happens (every request is eventually answered); a **fairness** property is a liveness property whose good thing happens again and again, not just once (each process gets a turn again and again); a **temporal** property relates states along a path (every message sent is later received). In the linear temporal logic (LTL) notation of the SPIN model checker, `[]` means always, `<>` eventually and `U` until, so "every request is eventually granted" is `[](request -> <>granted)` {% cite mansourisamani2008program %}. A safety violation has a finite counterexample; a liveness violation is an infinite run that never reaches the good state, which a model checker shows as a cycle of states {% cite benari2010model %}. So a safety violation is easier to show: a finite prefix of steps is enough. An analysis proves only the properties someone wrote down, and it says nothing about response time under real load, usability or a wrong requirement.

→ [What Analysis Can Prove](../../verif/overview/analysis/analysis_properties.md)

## 6. Model checking explores every state of a model

A model checker takes a finite model and a property, explores every reachable state and either confirms the property for the model or returns a counterexample, the exact sequence of steps that breaks it {% cite benari2010model %}. It is strong on concurrency: two processes that each add 1 to a shared counter ten times through a local copy can end with the value 2, lower than random simulations had shown, and the checker finds it directly with the steps that lead to it. Each increment is `tmp = counter` then `counter = tmp + 1`. P reads 0; Q runs nine full increments (counter = 9); P writes 1; Q reads 1; P runs its last nine increments (counter = 10); Q writes 2. Its limit is **state explosion**: each added process multiplies the states, a problem that can be managed but not removed {% cite mansourisamani2008program %}. Tools fall into families with different guarantees: linters and type checkers (pylint, mypy) prove nothing when silent; sound analyzers (Polyspace, Astrée) rule out a class of runtime error; model checkers (SPIN, Java PathFinder) prove properties of a model; program verifiers (Dafny, KeY) prove that code meets its specification.

→ [Model Checking](../../verif/static/model-checking.md), [Analysis Tools by Family](../../verif/overview/analysis/tools.md)

## 7. Testing shows failures by running the program

Testing evaluates software by observing its execution {% cite ammann2016introduction %}. A **fault** is a static defect in the code, an **error** the wrong internal state it causes, a **failure** the wrong external behaviour. A failure is seen only if the test reaches the fault, the fault infects the state, and the infected state propagates to an output the test checks {% cite ammann2016introduction %}. In Ammann and Offutt's `num_zero` example the loop that counts zeros starts at index 1 instead of 0. For `[2, 7, 0]` the run reaches the fault and the wrong index is already an error state, but the skipped element is not a zero, so the count is right and nothing fails; for `[0, 7, 2]` the error propagates and the function returns 0 instead of 1. Deciding whether an output is right needs an **oracle**, and it must come from the requirement, not from the code under test: an expected value, a reference implementation, a property, or simply "no crash". And testing "can be used to show the presence of bugs, but never to show their absence" {% cite dijkstra1970notes %}. Tests can be classified by level, source (black or white box), purpose, targeted faults and execution (manual or automated).

→ [What Testing Is](../../verif/overview/testing/testing_definitions.md)

## 8. Each testing level checks against a different artifact

Unit tests are designed from the implementation, one function at a time, though their expected values must still come from what each function should do; module tests a class or file in isolation, integration tests the interfaces between modules, system tests the assembled system against its specification, and acceptance tests the finished product against what users want {% cite ammann2016introduction %}. In the V model, which draws development activities down the left side and the matching test levels up the right, each level's tests are designed alongside the matching development activity. Each level finds faults the others cannot. For example, an `Item` class returning cents to an `Order` expecting euros passes both unit tests and fails integration. Unit tests isolate the unit with **test doubles**: a stub returns canned answers, a fake is a lightweight working implementation, and a mock checks how a dependency was called {% cite winters2020swe %}. Google prefers real implementations, because a test is only as faithful as what it runs against, then fakes, and warns against overused stubs and **interaction tests** (tests that use a mock to check how a dependency was called): they are brittle, failing after a refactoring that left the behaviour unchanged.

→ [Testing Levels](../../verif/overview/testing/testing_levels.md), [Test Doubles](../../verif/overview/testing/test-doubles.md)

## 9. Black-box and white-box tests see different gaps

Black-box tests come from the specification; white-box tests from the code's statements and branches {% cite ammann2016introduction %}. Boundary-value testing is a typical black-box technique, branch coverage a typical white-box one; the next lectures teach both. Black-box tests show which requirements were tested but miss code the specification never mentions; white-box tests show which code ran but miss requirements that were never coded. Tests can also be classified by purpose: functional, regression (re-running tests after a change), performance (load and stress) and robustness. **Exploratory testing** designs tests while running them, guided by the tester's learning; it needs no written specification but is hard to repeat.

→ [Black-Box and White-Box Testing](../../verif/overview/testing/testing_bb_wb.md), [Testing by Purpose](../../verif/overview/testing/testing_purpose.md), [Exploratory Testing](../../verif/exploratory/index.md)

## 10. Most tests should be small, and TDD helps experienced teams on new code

Google's test pyramid recommends about 80% unit, 15% integration and 5% end-to-end tests, by test count; the inverted "ice cream cone" of mostly end-to-end tests is slow, unreliable and hard to work with {% cite winters2020swe %}. The ratio often quoted as 70/20/10 comes from an earlier Google blog post; both are rules of thumb. Speed matters at scale: Google's continuous integration makes 150 million test runs a day {% cite memon2017testing %}.

Test-driven development writes a failing test first (red), then the code to pass it (green), then refactors: red–green–refactor {% cite beck2003tdd %}. At Microsoft and IBM, teams using TDD saw 40% to 90% lower pre-release defect density, at an estimated 15% to 35% more initial development time {% cite nagappan2008tdd %}. Controlled experiments with TDD newcomers found no quality gain {% cite santos2021tdd %}, and in one industrial experiment TDD did well on a simple new task and poorly on a complex change to existing code {% cite tosun2017tdd %}.

→ [The Test Pyramid](../../verif/overview/testing/test-pyramid.md), [Test-Driven Development](../../verif/overview/testing/tdd.md)

## 11. Demonstration verifies use under real conditions

Demonstration shows that using the product achieves a requirement, without detailed data gathering {% cite nasa2016sehandbook %}. In software it covers user acceptance sessions, beta tests and trial operation in the target environment. Some properties can only be shown this way: whether a new customer can complete a purchase, or whether the system works with the real network and hardware. "It works on my machine" is not a demonstration.

→ [Four Verification Methods](../../verif/overview/techniques.md)

## 12. Combine methods, because each misses what another finds

No method finds every fault, and different methods find different kinds {% cite zheng2006static %}. The lecture's mapping puts maintainability with inspection and analysis, usability with demonstration and reliability with analysis and testing; it is a judgement, not a measured result. What reaches users shows why combining matters: across 109 projects, mean post-release defect density was 7.47 defects per thousand lines and the median 4.3, ranging from 0.05 to about 50 {% cite shah2012defect %}.

→ [Four Verification Methods](../../verif/overview/techniques.md)

## Where this goes next

The next lecture asks how much testing is enough: [SN L04: Adequacy Criteria](SN_L04_Coverage.md) measures a suite by the code and inputs it exercises, building on the black-box and white-box split above. The testing lab applies this lecture: unit tests with mocks for a small banking application, one end-to-end scenario, and a CI workflow that runs them on every commit.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
