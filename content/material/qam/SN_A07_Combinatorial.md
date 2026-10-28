---
title: "SN A07: Combinatorial Testing"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 13
redirect_from: /content/verif/combinatorial/SN_A07_Combinatorial.html
layout: default
page_type: study-notes
---

# SN A07: Combinatorial Testing

The lecture asks how to test software whose inputs can interact when trying every combination is impossible, and how few tests are enough to cover every combination that is likely to matter. [RQ A07: Combinatorial Testing](RQ_A07_Combinatorial.md) tests this lecture.

## 1. Most failures need only a few interacting inputs

An **interaction fault** causes a failure only when several inputs take particular values together. The number of inputs that must take such values is the failure's **FTFI number** (failure-triggering fault interaction). For example, `if balance <= 0 and overdraft:` hides a fault with FTFI number 2: it needs one value of each of two inputs in the same test: a **value pair**. A suite has **t-way coverage** when every combination of values of any *t* inputs appears in some test (with *t* = 2, every value pair); *t* = 2 is called **pairwise**. Such a suite finds every fault whose FTFI number is at most *t*, provided the triggering values are among those tested.

Kuhn, Wallace and Gallo, at the US National Institute of Standards and Technology (NIST) and NASA, counted FTFI numbers in four systems {% cite kuhn2004fault %}: 329 error reports from a NASA data-management system in development, 109 FDA recalls of medical-device software, and bug reports of early Mozilla and Apache releases (the paper does not give their counts).

| Cumulative % of failures with FTFI number ≤ | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|
| NASA system | 68 | 93 | 98 | 100 | | |
| Medical devices | 66 | 97 | 99 | 100 | | |
| Browser (Mozilla) | 29 | 76 | 95 | 97 | 99 | 100 |
| Server (Apache) | 42 | 70 | 89 | 96 | 96 | 100 |

No failure needed more than six inputs. Hence the field's central claim: if every fault is triggered by at most *t* inputs, testing all *t*-way combinations is **effectively exhaustive**. Because each input is reduced to a few classes of values first, the authors call it **pseudo-exhaustive**. Four systems are a small sample, and sequence-dependent software, such as real-time control, may behave differently.

→ [Interaction Faults](../../verif/combinatorial/interaction-faults.md)

## 2. Industrial studies find what intuition misses

Three studies give the practical case {% cite hu2020realworld daoud2025effectiveness bures2017effectiveness %}. The first two generated their suites with NIST's ACTS tool (§7).

- **Hu et al., five industrial systems.** Pairwise suites (at most about 120 tests each, the sponsors' limit) found 28 of 30 faults needing several inputs. The in-house teams found 2 (93.3% against 6.7%). The authors' explanation: in-house teams chose values from experience and requirements, so triggering pairs rarely met in one test; a pairwise suite contains every pair by construction. For faults needing one input it was 25 of 28 against 20 of 28 (89.3% against 71.4%); that edge came from random and boundary values within classes. The barriers the authors report: the size of suite testers will run by hand, the need for detailed requirements to build the model, and masking (§9), which they counter by checking each test's output as it runs.
- **Daoud et al., Tricentis.** In a first study, on two input forms, pairwise tests found all 22 defects, the original tests 18. The defects were real past defects plus **seeded** ones, copies planted on purpose in nearby code. (Seeded defects have nothing to do with the seed tests of §8.) A second, separate study ran all of the product's commands on 12 pairwise configurations of operating system, browser and product version. Execution took 205 hours instead of 394 (48% less), and both test sets found the same 90 past defects, which makes the hours comparable.
- **Bures and Ahmed, JTrac.** They made 10 **mutants**: copies of the system in which the handling of the input fields was changed on purpose, for example a value or a logical operator altered. A 3-way suite of 322 tests found the fault in 3 mutants that the 48-test pairwise suite missed. Per test the two were alike: about 40% of tests in each found nothing.

Pairwise therefore finds most, not all, interaction faults: it is one layer of testing, not a replacement for the others.

→ [Combinatorial Testing in Industry](../../verif/combinatorial/effectiveness.md)

## 3. Covering arrays drop the balance that experiments need

An **orthogonal array** contains every combination of values of any *t* columns *exactly* the same number of times. Agricultural experiments need that balance to average out noise. A **covering array** of strength *t*, a table with one row per test and one column per input, only requires every such combination *at least once* {% cite cohen1997aetg %}. Software tests are repeatable, so one occurrence is enough, and dropping balance allows smaller arrays for any number of parameters. A suite built from a covering array of strength *t* has *t*-way coverage (§1). A 3-way array is also a 2-way array.

Two counts let you check any array. For *k* parameters with *v* values each it must cover C(*k*, *t*) · *v*<sup>*t*</sup> combinations: 7,020 pairs for 40 three-valued parameters. With different value counts, add *v*<sub>*i*</sub> · *v*<sub>*j*</sub> over every pair of parameters (72 pairs for the Find dialog in §5). And it needs at least as many rows as the product of the *t* largest value counts.

→ [Covering Arrays](../../verif/combinatorial/foundations.md)

## 4. Adding parameters is cheap; raising the strength is not

Cohen et al. proved that a greedy generator always covers at least 1/*v*<sup>2</sup> of the remaining pairs with its next test. After *r* tests at most *N*(1 − 1/*v*<sup>2</sup>)<sup>*r*</sup> of the *N* pairs remain, so about *v*<sup>2</sup> · ln *N* tests suffice; since *N* grows only with *k*<sup>2</sup>, the count grows with log *k*. That bound is loose (about 80 tests for 40 three-valued parameters). The paper's own **rule of thumb** for three-valued parameters, from the arrays it reports, is about 4 · log<sub>2</sub> *k*: 15 tests for 13 parameters and 21 for 40, where exhaustive testing needs 3<sup>40</sup> ≈ 1.2 × 10<sup>19</sup> {% cite cohen1997aetg %}.

Raising *t* costs far more. For 10 parameters with 5 values each, the IPOG generator (§7) needed 48 tests at *t* = 2, 308 at *t* = 3 and 1,843 at *t* = 4 {% cite lei2007ipog %}. The practical rule is: many parameters are cheap; many values and high strength are not. Spend strength where a missed failure costs most: pairwise by default, 3-way or more for safety-critical parts.

→ [Covering Arrays](../../verif/combinatorial/foundations.md)

## 5. A model turns a dialog into parameters and values

An **input model** lists the parameters and a few values for each, usually one per equivalence class. The lecture's Find dialog has FindWhat (8 classes: Empty, lowercase, MixedCase, UPPER, space, multiword, special, long), MatchCase (Yes, No), Direction (Up, Down) and File (None, Single, Multiple).

**Base choice** is the cheapest systematic strategy: start from one base test of the most likely values, then vary one parameter at a time through its other values, so it needs 1 + Σ(*v*<sub>*i*</sub> − 1) tests.

| Strategy | Tests | Guarantees |
|---|---|---|
| All combinations | 8 · 2 · 2 · 3 = 96 | everything |
| 3-way (PICT) | 49 (lower bound 8 · 2 · 3 = 48) | every triple |
| Pairwise (PICT) | 24 (lower bound 8 · 3 = 24) | every pair |
| Base choice | 1 + 7 + 1 + 1 + 2 = 12 | every value once, against a base test |

With four three-valued parameters, base choice's 9 tests cover only 30 of the 54 pairs {% cite cohen1997aetg %}. Yet in Grindal et al.'s survey, one experiment with seeded faults found that it found as many faults as pairwise; the authors advise base choice for less important software and both together for critical software {% cite grindal2005survey %}.

Each row names classes, so someone still chooses concrete values and expected results.

→ [Combinatorial Input Models](../../verif/combinatorial/practice.md)

## 6. The same idea reaches simulation and training data

Tao et al. modelled an autonomous emergency-braking function from the Euro NCAP test protocols as 37 parameters with 39 constraints. The IPOG generator (§7) produced 319 pairwise scenarios, each run in a driving simulator, a plan the authors called impossible with real vehicles {% cite tao2019industrial %}. The study had no automatic pass or fail check.

Lanus et al. turned coverage around to judge machine-learning data {% cite lanus2021metrics %}. Each satellite image was described by 12 features, each split into 3 bins, plus its label (plane or no plane). A **label-centred pair** is one feature bin together with one label, so there are 12 · 3 · 2 = 72 of them. The northern image set held 67 of the 72 (coverage 0.93), the southern set 60 (0.83), although the southern set had about twice as many images (21,151 against 10,849). Size is not the same as variety.

→ [Combinatorial Testing in Industry](../../verif/combinatorial/effectiveness.md)

## 7. AETG adds tests; IPOG adds parameters

Generators do not search for the smallest array; they use heuristics {% cite cohen1997aetg lei2007ipog %}.

- **AETG** builds one test at a time. It picks the value in the most uncovered pairs, orders the other parameters randomly, and gives each the value covering the most new pairs. It makes 50 such candidates and keeps the best.
- **IPOG** covers one parameter at a time. It starts with all combinations of the first *t* parameters. **Horizontal growth** gives each existing test the value of the new parameter that covers the most uncovered combinations. **Vertical growth** then covers the rest one combination at a time. A new test gets values only in the positions that combination needs; the others are **don't-care** positions, marked \*, which a later missing combination may fill. Any \* still left at the end takes any value.

For example, with P1 and P2 in {0, 1} and P3 in {0, 1, 2}, horizontal growth turns rows 00, 01, 10, 11 into 000, 011, 101, 110 (ties go to the smallest value). P3 = 2 is still unpaired, so vertical growth adds 0\*2 for (P1 = 0, P3 = 2), fills the \* with 0 to cover (P2 = 0, P3 = 2), and adds 112 for the last two pairs: 6 tests, the minimum, since P1 and P3 alone have 6 pairs. NIST's ACTS tool, used by the first two studies in §2, generates with IPOG by default {% cite daoud2025effectiveness %}.

→ [Covering Array Generation](../../verif/combinatorial/algorithms.md)

## 8. Search algorithms and tools

Search algorithms improve a whole array step by step. Zeb et al.'s review of 91 studies sorts them into five kinds: **standard** (one algorithm), **mix** (a search algorithm plus a greedy or algebraic method), **adaptive** (self-tuning), **hybrid** (several search algorithms) and **hyper-heuristic** (choosing among algorithms). **Simulated annealing** makes small random changes to an array, keeps the ones that help, and early on sometimes keeps a change that hurts, so the search does not get stuck. It stood out in the review: it was the one search algorithm used in the best-performing strategies of four of the five kinds {% cite zeb2025systematic %}. Three free tools cover most needs: **PICT** (text model, command line), **ACTS** (IPOG, the tool of the first two studies in §2) and **CASA** (annealing with a SAT solver for complex constraints). **Seed tests**, existing tests the output must contain, rarely make a suite smaller, as Bombarda and Gargantini found; use them to keep tests you must keep {% cite bombarda2025completion %}.

→ [Combinatorial Testing Tools](../../verif/combinatorial/tools.md)

## 9. Constraints and invalid values need their own rules

A **constraint** forbids combinations, such as Safari on anything but macOS. Deleting invalid rows from an unconstrained array loses coverage. For example, take OS (Windows, macOS, Linux), Browser (Chrome, Firefox, Safari) and Language (EN, DE). PICT's 9-row pairwise array, listed in RQ Q8, has two Safari rows on other systems; deleting them loses the valid pairs (Windows, EN) and (Safari, EN). With the constraint in the model, PICT gives 8 rows covering all 19 valid pairs {% cite microsoft_pict %}. Eight is the minimum: the 7 valid OS-browser pairs each need a row, and (macOS, Safari) needs two, because Safari must meet both languages and runs only on macOS. CASA, built for heavy constraints, produced arrays 25% smaller than a greedy tool on 35 constrained problems, and paid off when a test suite took over 21 seconds to run {% cite garvin2011casa %}.

An **invalid** value is one the system should reject. Two in one test cause **masking**: the first error stops processing, so the second value is never handled. More generally, anything that ends a test early hides what it would have checked afterwards. The rule is at most one invalid value per test, all others valid {% cite grindal2005survey %}. PICT applies it automatically: values marked with `~` in the model, such as `~0` for a quantity, appear at most once per generated test {% cite microsoft_pict %}.

**Mixed strength** gives different parameter groups different *t*. At Tricentis, Daoud et al. required pairs only among a few chosen sets of interacting parameters: on one form the plain pairwise suite had 58 tests, the mixed one 52, which missed one defect plain pairwise found {% cite daoud2025effectiveness %}.

→ [Constraints and Invalid Values](../../verif/combinatorial/advanced.md)

## Where this goes next

Combinatorial testing starts from an input model, but says little about how to find its parameters and classes. A08's classification trees build that model systematically and then use the combination strategies from this lecture to choose the tests.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
