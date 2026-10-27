---
title: Mutation Testing
parent: Random Testing
nav_order: 9
layout: default
page_type: deep-dive   # full worked mutmut run: every mutant accounted for, as reviewers asked
---

# Mutation Testing

**Mutation testing** judges a test suite by how many small deliberate faults it detects, and uses the survivors to show where tests are missing. The basics (operators, killed and surviving mutants, the mutation score) are on [Mutation Coverage](../coverage/mutation.md); this page covers why the method works, what it costs and how it is used.

## 1. Why small faults are enough

DeMillo, Lipton and Sayward rested the method on two hypotheses {% cite demillo1978hints %}:

- **The competent programmer hypothesis.** Programs written by competent programmers are close to correct, so real faults look like small syntactic slips.
- **The coupling effect.** Tests that detect all simple faults are so sensitive that they also detect most complex ones.

Their own evidence came from Hoare's FIND program. Test data built to kill the simple mutants was then run against more than 22,000 random higher-order mutants (several changes at once); only 19 survived, and all 19 turned out to be equivalent to FIND: they behaved exactly like it on every input (see section 4) {% cite demillo1978hints %}. Later studies agree: test data that killed first-order mutants killed over 99% of second- and third-order mutants {% cite jia2011mutation %}.

## 2. Worked example with mutmut

Setup takes two steps: `pip install mutmut`, and a section in `pyproject.toml` naming the code to mutate. This minimal configuration worked with mutmut 3.8:

```toml
[tool.mutmut]
source_paths = ["src/"]

[tool.pytest.ini_options]
pythonpath = ["src"]
```

mutmut writes the mutated copies to a `mutants/` folder and keeps the results there. `mutmut results` lists each survivor by name, for example `shipping.x_shipping_cost__mutmut_9: survived`; mutmut reports mutants that make the tests time out separately, to be checked by hand.

`mutmut` changed this function in 18 ways (operators, constants and the message string, or replaced a value with `None`), producing 18 mutants, and ran the three tests against each:

```python
def shipping_cost(weight, express):
    if weight <= 0:
        raise ValueError("weight must be positive")
    cost = 5 + 2 * weight
    if express:
        cost = cost * 2
    if weight > 20:
        cost = cost + 10
    return cost

def test_standard(): assert shipping_cost(1, False) == 7
def test_express():  assert shipping_cost(1, True) == 14
def test_invalid():
    with pytest.raises(ValueError): shipping_cost(0, False)
```

Nine were killed and nine survived: a mutation score of 9/18 = 50% (no mutant was judged equivalent), although the tests take 5 of the 6 branch outcomes (83% branch coverage), all except the true side of `weight > 20`. Per line, the tests killed both mutants of `weight <= 0` (`< 0`, `<= 1`), four of the five of `5 + 2 * weight` (`None`, `5 - 2 * weight`, `6 + 2 * weight`, `5 + 3 * weight`; the fifth, `2 / weight`, survived) and all three of `cost * 2`. All nine survivors sit on four lines: three change the error message, one changes `2 * weight`, two move the boundary (`>= 20`, `> 21`) and three change `cost + 10` (to `cost - 10`, `cost + 11` or `None`), a line no test reaches. Three of them, shown with `mutmut show`, tell three different stories:

```diff
-    cost = 5 + 2 * weight
+    cost = 5 + 2 / weight

-    if weight > 20:
+    if weight >= 20:

-        raise ValueError("weight must be positive")
+        raise ValueError(None)
```

- `2 / weight` survives because every test uses weight 1, where `2 * 1 == 2 / 1`: a missing test with another weight.
- `weight >= 20` survives because no test is near the boundary at 20.
- The changed message survives because no test checks it; whether that matters is a judgement.

The commands, run in the project folder:

```bash
mutmut run                 # create the mutants and run the tests against each
mutmut results             # list the surviving mutants by name
mutmut show <mutant-name>  # print one survivor as a diff
```

**Mutation needs the tests to run the mutated code.** mutmut changes the source files and reruns the tests in the same project, so the tests must call the code directly or through an in-process test client. Tests that send requests to a separately running server never execute the mutants, and every mutant survives, unless the server is restarted on each mutant.

## 3. Cutting the cost

A program with *n* statements and *k* operators yields on the order of *n × k* mutants, each needing a run of the suite. The survey by Jia and Harman, covering more than 390 papers, summarises three ways to cut that {% cite jia2011mutation %}:

- **Selective mutation.** Tests that kill all mutants from five operators kill 99.5% of the mutants from the full operator set: ABS (insert an absolute value), UOI (insert a unary operator such as `-x`), LCR (replace a logical connector, `and` ↔ `or`), AOR (replace an arithmetic operator) and ROR (replace a relational operator).
- **Sampling.** Tests built against a random 10% of the mutants were only 16% less effective, measured by mutation score, than tests built against all of them; the survey does not say whether the 16% is relative or in percentage points.
- **Weak mutation.** Compare the program state right after the mutated statement instead of the final output: cheaper, but it counts mutants whose effect would never reach the output.

## 4. The equivalent-mutant problem

An **equivalent mutant** changes the code but not its behaviour, so no test can kill it; a person must judge each suspect survivor, for example by reading it with `mutmut show`, and remove it from the score. Empirical studies put 10% to 40% of mutants in this category {% cite jia2011mutation %}, and deciding equivalence in general is undecidable. Recent work uses language models, judged by **F1-score**, which balances finding equivalent mutants against false alarms: on 3,302 Java mutant pairs, LLM-based techniques beat the earlier detection tools by 35.69% in F1-score on average (the paper does not say whether this is relative or in percentage points), and the best of them was a fine-tuned code model (UniXCoder) at 86.58% F1, ahead of prompting large general models such as GPT-4 {% cite tian2024llm_emd %}.

## When to use it — and when not

- **Use it** on critical modules, to find the tests a high coverage figure hides, as in the example.
- **Use it** to compare two test suites, or to check a test-generation tool.
- **Do not run all mutants on every commit** of a large system; restrict it to changed files, or sample.

## Limitations

In a study of developers using mutation analysis, it raised the statement coverage score by 2–9% in an hour (the paper does not say whether relative or in percentage points) and produced one new test about every six minutes; participants found it "effective but relatively expensive" {% cite smith2009mutation %}. Mutants killed by a test written to kill a different mutant are called **crossfire**; one new test often removes several survivors at once. Equivalent mutants still cost human time, and scores depend on the tool's operators.

## How solid is this?

- **Where it comes from.** DeMillo et al. 1978 {% cite demillo1978hints %} and the Jia and Harman survey {% cite jia2011mutation %}; practice from Smith and Williams {% cite smith2009mutation %}; equivalent-mutant detection from Tian et al. {% cite tian2024llm_emd %}.
- **What is contested.** The coupling effect is supported for higher-order mutants, which are themselves artificial; how well mutants stand in for real faults is studied on [Coverage and Fault Detection](../coverage/discussion.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
