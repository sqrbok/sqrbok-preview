---
parent: Testing Overview
nav_order: 7
title: Test-Driven Development
layout: default
---

# Test-Driven Development

**Test-driven development (TDD) is a way of programming in which a developer writes a small failing test first, then just enough code to make it pass, then cleans up the code: red, green, refactor** {% cite beck2003tdd %}. It is a development practice that produces unit tests as a by-product, not a test design technique.

## 1. The cycle

Beck states the three steps as {% cite beck2003tdd %}:

1. **Red.** Write a little test that does not work, and perhaps does not even compile.
2. **Green.** Make the test work quickly, "committing whatever sins necessary".
3. **Refactor.** Remove the duplication created in getting the test to work.

Each loop should take minutes, so the code is never far from a passing state.

## 2. Example: a password rule

(Constructed example.) The requirement: a password is strong if it has at least 8 characters and contains a digit. The developer writes three tests before any code:

```python
from password import is_strong

def test_short_password_is_weak():
    assert not is_strong("abc1")

def test_long_password_with_digit_is_strong():
    assert is_strong("correcthorse9")

def test_long_password_without_digit_is_weak():
    assert not is_strong("correcthorse")
```

**Red:** pytest stops with `ModuleNotFoundError: No module named 'password'`. **Green, first try:** `return len(pw) >= 8` passes two tests and fails `test_long_password_without_digit_is_weak`. **Green:** `return len(pw) >= 8 and any(c.isdigit() for c in pw)` passes all three. **Refactor:** nothing to clean up yet; the next rule (a capital letter, say) starts the next loop with a new failing test.

## 3. What the evidence says

The results depend on who uses TDD and on what task.

**Industrial teams.** At Microsoft (three teams) and IBM (one), pre-release defect density fell by 40% to 90% compared with similar projects without TDD, and the teams estimated a 15% to 35% increase in initial development time {% cite nagappan2008tdd %}. The comparison projects were not randomly assigned.

**TDD newcomers in controlled experiments.** A family of 12 experiments with 411 students and professionals, none of whom had used TDD before, found no significant quality difference {% cite santos2021tdd %}. Quality is the share of acceptance tests passed; TDD minus iterative test-last was −7.09 percentage points, with a 95% confidence interval from −14.48 to +0.30. The interval includes zero, so the difference is not significant: TDD was at best 0.3 points better and possibly up to 14.5 points worse. Among these newcomers, professionals lost more quality with TDD than students did.

**Task matters.** In an experiment with 24 professionals at one company, TDD on a simple new (greenfield) task reached 76.0% external quality, measured as acceptance tests passed, against 53.9% for test-last, a difference that was not significant; on a complex task extending existing (brownfield) code, TDD reached only 39.1%, and no test-last group did that task, so there is no test-last figure to compare {% cite tosun2017tdd %}. The authors conclude that task complexity obscured the effect of TDD.

## When to use it — and when not

- Use TDD for new code with clear, testable requirements, by developers who have practised it.
- Do not expect a quality gain from a team that has just been told to start: the controlled experiments with novices found none.

## Limitations

TDD tests only what the developer thought of; the tests share the developer's misunderstanding of the requirement. In the example, if the requirement really meant "8 characters *excluding* digits", all three tests would still pass.

## How solid is this?

- **Where it comes from.** The cycle from Beck {% cite beck2003tdd %}; the evidence from one industrial case study {% cite nagappan2008tdd %}, one family of experiments {% cite santos2021tdd %} and one industrial experiment {% cite tosun2017tdd %}.
- **What is contested.** Almost everything about the size of the effect. The industrial gains come from motivated, experienced teams without random assignment; the experiments use short, small tasks and TDD newcomers. In Tosun's experiment the task and the treatment are confounded.
- **What we do not hold.** Long-term studies of TDD on large codebases.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
