---
title: "SN L06: Random Testing"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 11
redirect_from: /content/verif/random/SN_L06_Random.html
layout: default
page_type: study-notes
---

# SN L06: Random Testing

The lecture asks what you gain by letting a program, not a person, choose test inputs. It covers four topics: random testing itself (its theory, and feedback-directed tools such as Randoop), fuzz testing, property-based testing, and mutation testing, which plants many small faults to measure how good tests are. The [revision questions](RQ_L06_Random.md) test this lecture.

## 1. Random testing needs an oracle; predicting reliability needs the operational profile

Random testing is not haphazard testing: inputs are drawn by a pseudorandom generator from a defined distribution. Any such sampling with an automatic oracle can find bugs. To turn passing tests into a **reliability figure**, Hamlet requires three conditions {% cite hamlet1984random %}: (1) a system-level test; (2) inputs drawn independently from the **operational profile**, how often real users supply each kind of input; and (3) an effective oracle that judges every output automatically. Without an automatic oracle, random inputs only produce outputs that someone must check. This is the **oracle problem**, and it is the bottleneck of every technique in the lecture. Oracles range from cheap and blind to precise and costly: crash or hang detection; **sanitizers** (compiler checks that turn hidden memory errors into crashes); contracts such as pre- and postconditions; properties that every correct output satisfies; and **differential** testing against another implementation {% cite manes2019fuzzing claessen2000quickcheck %}. Only plain random testing on the profile meets all three. The guided tools below (feedback-directed testing, coverage-guided fuzzing) break condition 2 because they choose inputs from earlier results, so draws are not independent. Property-based tools draw independently and have an automatic oracle, but they break condition 2 in another way: their distribution is deliberately skewed towards edge values, so any confidence figure describes the generator, not real use. All of them find bugs faster but lose the statistical claim of section 2.

→ [Random Testing Fundamentals](../../verif/random/fundamentals.md)

## 2. Passing tests buy a statistical claim, and 23,000 buy a strong one

If the failure rate is *F*, *N* independent tests reveal a failure with probability *C* = 1 − (1 − *F*)<sup>*N*</sup>, so *N* = ln(1 − *C*) / ln(1 − *F*). For example, 90% confidence that the failure rate is below 1 in 10,000 takes about 23,025 passing tests. The reasoning runs backwards: if *F* were 1/10,000 or more, 23,025 tests would have shown a failure with probability at least 90%; since all passed, you can be 90% confident that *F* < 1/10,000. As section 1 explains, property-based tools cannot give this figure; they are for finding bugs, not for estimating reliability. The claim holds only for the profile you sampled: if real use differs, the prediction can be arbitrarily optimistic {% cite hamlet1984random %}.

→ [Random Testing Fundamentals](../../verif/random/fundamentals.md)

## 3. Random testing misses rare inputs, but partitions gain less than expected

A uniform sample almost never hits one exact boundary value, which is what partition testing aims at. Yet the comparisons since 1984, summarised by Hamlet, found the gap small: Duran and Ntafos found random testing often more cost-effective {% cite duran1984evaluation %}, and roughly 20% more random tests wipe out any advantage of partition testing {% cite hamlet1984random %}. Partitions win when some subdomains carry much costlier failures {% cite tsoukalas1993reliability %}; random sampling is the only principled choice for large unstructured domains and for programs with state, where a test is a sequence of inputs from reset {% cite hamlet2006random %}.

→ [Random versus Partition Testing](../../verif/random/partition.md)

## 4. Feedback makes random generation productive

Randoop builds random sequences of method calls and executes each at once. A sequence is *valid* if it runs without an error and without being rejected as illegal input; valid sequences are kept and extended, and those that break a contract, such as a failed assertion, are reported. At Microsoft, on a .NET component tested for five years by about 40 testers, it found 30 previously unknown errors. The team spent 15 hours of its own time; the tool used 150 hours of CPU time, partly on many machines in parallel. Measured in the team's hours of using the tool, the rate fell and then hit a **plateau**: 10 errors in the first two hours, about 2 per hour for the next ten (30 in about 12 hours), then none. The cause was unfair selection: methods are picked uniformly, so classes with easy or many constructors are explored far more than classes whose objects are hard to build {% cite pacheco2007randoop %}. With AutoTest, the random seed alone changed the bugs found in 30 minutes from 5 to 23 {% cite meyer2007autotest %}.

→ [Feedback-Directed Random Testing](../../verif/random/feedback.md)

## 5. Fuzzing turned random bytes into a security tool

Fuzzing differs from plain random testing in its inputs, its oracle and its goal: it feeds malformed or byte-level inputs, often outside what the program expects, watches for crashes, hangs or sanitizer reports rather than wrong answers, and mainly seeks robustness and security bugs {% cite manes2019fuzzing %}. Because fuzz inputs lie deliberately outside the operational profile, even blackbox fuzzing gives no reliability figure. In 1990, random input crashed or hung 24–33% of UNIX utilities on each system tested {% cite miller1990fuzz %}. Repeated studies found GUI applications worse, not better: 45% of Windows NT applications crashed or hung in 2000, and 73% of macOS GUI applications in 2006, while macOS command-line tools fell to 7% {% cite forrester2000windows miller2006macos %}. The same kinds of simple programming errors behind the 1990 failures were still present in 2006 {% cite miller2006macos %}.

Three generations of fuzzers followed: blackbox random bytes; whitebox fuzzing, which uses **symbolic execution**: it records the branch conditions along an executed path (the *path constraints*) and asks a solver for an input that takes a different branch (Microsoft's SAGE found one third of Windows 7 file-fuzzing bugs this way {% cite bounimova2010sage %}); and greybox fuzzing, which keeps any mutated input that reaches new coverage. Greybox gives up the ability to solve its way past a hard check in exchange for running thousands of inputs in the time one is analysed symbolically. Directed greybox fuzzing steers inputs towards a target by mutating inputs closer to it more often: AFLGo exposed Heartbleed, the 2014 OpenSSL bug that leaked server memory, in under 20 minutes, where a symbolic-execution tool, slowed by heavy program analysis for every input, failed in 24 hours {% cite bohme2017directed %}. Fuzzers must be evaluated carefully: 57,142 "unique" crashes turned out to be 9 bugs, and one fuzzer that led after 6 hours lost after 24, so comparisons need many runs and long campaigns {% cite klees2018evaluating %}.

→ [Fuzz Testing](../../verif/random/fuzzing.md) · [Coverage-Guided Fuzzing](../../verif/random/greybox.md)

## 6. Property-based testing replaces examples with rules

QuickCheck introduced properties, generators and shrinking {% cite claessen2000quickcheck %}; Hypothesis brought them to Python {% cite maciver2019hypothesis %}. A Hypothesis test states a rule and lets the tool choose the inputs, 100 by default:

```python
from hypothesis import given, strategies as st

@given(st.text())                    # generator: random strings
def test_round_trip(s):
    assert decode(encode(s)) == s    # property: encoding loses nothing
```

On a run-length encoder that drops the last run whenever there are two or more runs, Hypothesis reports the failing input `'01'`: `encode('01')` gives `[('0', 1)]`, which decodes to `'0'`, not `'01'`. It found a longer failing string first and **shrank** it: a one-run string such as `'0'` passes, so two different characters are the smallest failing case. Hypothesis can shrink any type of input because it stores every test case in one universal representation and shrinks that, which saves writing a separate shrinker for each type {% cite maciver2019hypothesis %}. For a web service, the generator builds request bodies and the property checks every response:

```python
import requests
from hypothesis import given, strategies as st

BASE_URL = "http://localhost:8000"   # the service under test

@given(st.fixed_dictionaries({                 # a dict with exactly these keys
    "item": st.sampled_from(["book", "pen"]),     # one of the listed values
    "quantity": st.integers(1, 1000)}))            # the valid range
def test_orders(body):
    r = requests.post(f"{BASE_URL}/orders", json=body)
    assert r.status_code < 500 and 200 <= r.status_code < 300
    data = r.json()
    assert data["item"] == body["item"] and data["quantity"] == body["quantity"]

@given(st.integers(max_value=0))                # the invalid range: 0 and below
def test_bad_quantity(q):                       # invalid input must be rejected
    r = requests.post(f"{BASE_URL}/orders", json={"item": "pen", "quantity": q})
    assert 400 <= r.status_code < 500
```

The errors QuickCheck found were split roughly evenly between generators, properties and programs, so writing a property is itself a check of the specification {% cite claessen2000quickcheck %}. At Jane Street, developers mostly wrote differential, round-trip and no-crash properties; 16 of 30 said writing specifications slowed them down, and one called "not knowing what properties to test" the most common failure mode {% cite goldstein2024pbt %}. Property tests run within budgets of 50 milliseconds to 30 seconds inside normal test runs, while fuzzing campaigns run separately for hours or days {% cite goldstein2024pbt %}. Coverage guidance helps here too. JQF is a Java tool whose Zest engine keeps generated inputs that reach new coverage, as greybox fuzzers do. JQF's example property says that a trie (a prefix tree) built from a map contains every key of the map; pure random generation found no failure in over 7 million executions, while the coverage-guided Zest found one after about 5,000 {% cite padhye2019jqf %}.

→ [Property-Based Testing](../../verif/random/property-based.md)

## 7. Mutation testing measures tests with planted faults

A mutant is a copy of the program with one small change made by a mutation operator; a good suite fails on it, and the mutant is *killed*. The **mutation score** is killed mutants ÷ (all mutants − equivalent mutants). The method rests on two hypotheses, as DeMillo et al. stated them {% cite demillo1978hints %}. The **competent programmer hypothesis** says real programs are close to correct, so real faults resemble small changes. The **coupling effect** says tests that catch simple faults also catch complex ones: test data that killed all single-change mutants killed over 99% of **higher-order mutants**, which combine two or three changes {% cite jia2011mutation %}.

With mutmut, the configuration is one section in `pyproject.toml` (`[tool.mutmut]` with `source_paths = ["src/"]`), and the workflow is three commands; mutants that make the tests time out are reported separately and checked by hand:

```bash
mutmut run                 # create the mutants and run the tests against each
mutmut results             # list the surviving mutants
mutmut show <mutant-name>  # print one survivor as a diff
```

For example, if the original line is `cost = 5 + 2 * weight` and the mutant is `cost = 5 + 2 / weight`, a suite whose tests all use weight 1 cannot tell them apart, so the mutant survives and points at the missing test. `mutmut run` shows killed and surviving counts as it goes. As a worked score: of 20 mutants, 3 equivalent and 15 killed, the score is 15 / (20 − 3) = 15/17 ≈ 88%.

The method is expensive, so five selected operators or a 10% sample keep most of its power {% cite jia2011mutation %}. Its most persistent problem is the **equivalent mutant**, a change that does not change behaviour: 10–40% of mutants are equivalent, and deciding equivalence is undecidable in general, so a person must judge each suspect survivor, for example by reading it with `mutmut show`, and set it aside {% cite jia2011mutation %}. Language models now help. Judged by F1-score, which balances finding equivalent mutants against false alarms, a fine-tuned code model of modest size (UniXCoder) reached 86.58%, ahead of prompting large general models such as GPT-4 {% cite tian2024llm_emd %}. Practitioners find mutation analysis "effective but relatively expensive": one new test about every six minutes, and some mutants killed in **crossfire**, by a test written for another mutant {% cite smith2009mutation %}. Crossfire matters because one well-chosen test can remove several survivors, so working through survivors one by one costs less than their number suggests.

Mutation testing and property-based testing attack the same weakness from two sides: mutation shows where the tests' assertions are too weak, and properties are a way to write stronger ones.

→ [Mutation Testing](../../verif/random/mutation.md) · [Mutation Coverage](../../verif/coverage/mutation.md)

## Where this goes next

The next lecture adds the human side of random exploration: [Exploratory Testing and Operational Profile](SN_L07_Exploratory_OP.md), where the operational profile behind the reliability formula is built from real usage. The random testing lab has two separate tasks. First, mutation testing with mutmut on a local Python project: mutmut changes the source and reruns the project's own tests, so the tests must call the code directly; tests against a separately running server would never execute the mutants. Second, property-based tests with Hypothesis against a web service, searching for inputs where it misbehaves. The [Property-Based Testing](../../verif/random/property-based.md) page has a full template.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
