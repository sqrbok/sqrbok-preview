---
parent: Verification by Analysis
nav_order: 2
title: Analysis Tools by Family
layout: default
---

# Analysis Tools by Family

**An analysis tool belongs to a family, the technique it uses, and the family decides what its silence means: a linter that reports nothing has proved nothing, while a program verifier that accepts a function has proved it meets its specification.** Choose the family first, then a tool for your language {% cite chess2007static %}.

## 1. Current tools

The table lists maintained tools as of 2026. Static-analysis families are compared in detail on [Comparing Static Analysis Tools](../../static/tools.md); this table adds the proof-oriented families.

| Family | Python | Java | C and C++ | Other or multi-language |
|---|---|---|---|---|
| Style and lint rules | pylint, flake8 | Checkstyle, PMD | Clang-Tidy, Cppcheck | ESLint (JavaScript) |
| Type checking | mypy | the compiler | the compiler | TypeScript |
| Bug patterns and security | Bandit | SpotBugs | Coverity, CodeSonar, Klocwork | CodeQL, Semgrep, SonarQube, Fortify |
| Sound abstract interpretation | | | Polyspace, Astrée, Frama-C (Eva) | |
| Symbolic execution and contract search | CrossHair | | KLEE | |
| Model checking | | Java PathFinder | CBMC | SPIN, TLA+ (TLC), Alloy |
| Program verification | | KeY, OpenJML | Frama-C (WP) | Dafny, Isabelle, Rocq (formerly Coq) |

The family names in brief:

- A **sound** analysis reports every violation of its property, so silence means the error cannot occur ([Soundness and Completeness](../../static/limits.md)).
- **Abstract interpretation** computes with abstract values such as "zero, not zero, maybe zero" instead of numbers, covering every path ([Dataflow Analysis](../../static/dataflow.md)).
- **Taint** analysis follows untrusted input to dangerous calls ([Pattern, Type and Taint Checks](../../static/techniques.md)).
- **Symbolic execution** runs the code on unknowns and asks a solver for an input that reaches each path ([Symbolic Execution](../../static/symbolic-execution.md)).
- A **contract** is a function's precondition (what callers must supply) and postcondition (what it promises).
- **State explosion** is the growth of reachable states with each added process or variable ([Model Checking](../../static/model-checking.md)).

Verifiers such as Dafny hand their proof obligations to an SMT solver, Z3, a program that decides whether a logical formula over numbers and arrays can be satisfied. KLEE is described on [Symbolic Execution](../../static/symbolic-execution.md) {% cite cadar2008klee %}; SPIN and Java PathFinder on [Model Checking](../../static/model-checking.md).

## 2. Example: one function, three families

The function below has a fault: an empty list divides by zero.

```python
def average(xs: list[float]) -> float:
    """Return the arithmetic mean of xs."""
    return sum(xs) / len(xs)
```

Run on it in October 2026, the tools gave three different answers:

- **Lint and types.** pylint 4.1.3 rated the file 10.00/10, and `mypy --strict` (2.4.1) reported "no issues". Neither checks whether a value can be zero.
- **Contract search.** CrossHair 0.0.112 searches for inputs that break a function's contract. Given the postcondition `min(xs) <= __return__ <= max(xs)`, it reported `ZeroDivisionError: when calling average([])`.
- **After a fix.** With the precondition `pre: len(xs) > 0` added, CrossHair reported nothing within a 30-second limit per condition. That silence was not a proof: in Python, `average([0.1, 0.1, 0.1])` returns `0.10000000000000002`, which is larger than the maximum, so the postcondition is false for real floating-point numbers.

A program verifier such as Dafny does not accept a division until a precondition rules out a zero divisor, so it would have caught the first fault. It proves properties of the number types it models, so floating-point rounding must be part of the specification before it can be checked: that care is the family's cost.

## 3. Retired tools

Several tools named in older course material are no longer maintained. FindBugs (last release 2015) continues as **SpotBugs**; ESC/Java continues as **OpenJML**; Frama-C's Jessie plugin was replaced by **WP**; the BLAST and JLint checkers and the PIPE Petri-net editor have had no recent releases. Use the successors.

## When to use it — and when not

- Run a linter, a type checker and a security analyzer on every commit; they are cheap.
- Reserve abstract interpretation, model checking and program verification for code where a failure is costly, such as concurrency, protocols and safety-critical functions.
- Do not compare tools only by the number of warnings: on the same Java projects, six tools agreed on fewer than 0.4% of their warnings {% cite lenarduzzi2021comparison %}.

## Limitations

The table is a snapshot; tools are renamed, bought and retired every few years. Placing a tool in one family also simplifies: SonarQube and CodeQL span several families, and several model checkers use solvers that also power verifiers.

## How solid is this?

- **Where it comes from.** The families follow Chess and West {% cite chess2007static %}; the tool runs above were made for this page; maintenance status was checked against each project's releases in October 2026.
- **What is contested.** Which families count as "analysis" and which as "testing": symbolic execution and contract search run code paths symbolically and sit between the two.
- **What we do not hold.** No held source compares the proof-oriented families on the same programs.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
