---
title: "SN A06: Static Analysis"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 11
redirect_from: /content/verif/static/SN_A06_Static.html
layout: default
page_type: study-notes
---

# SN A06: Static Analysis

The lecture asks how tools find defects without running code, and why each must sometimes be wrong; it then covers pattern, type and taint checks, dataflow analysis, model checking with CEGAR, symbolic execution, and tools in practice. [RQ A06: Static Analysis](RQ_A06_Static.md) tests this lecture.

## 1. Static analysis finds bugs that tests rarely reach

**Static analysis** examines code without executing it and reports code that may break a rule. **Dynamic analysis**, testing included, runs the program on concrete inputs: its findings are real, but it sees only the paths those inputs take {% cite chess2007static %}.

In Apple's "goto fail" bug (CVE-2014-1266) a duplicated `goto fail;` skipped the TLS signature check, so forged server keys were accepted {% cite nist_cve_2014_1266 %}. Tests with valid signatures pass; clang flags the dead code at once. For example, it also finds a resource not freed on an untested error path.

→ [Static Analysis](../../verif/static/index.md), [Pattern, Type and Taint Checks](../../verif/static/techniques.md)

## 2. No analysis is sound, complete and always terminating

**Rice's theorem** (1953): every non-trivial question about what a program does is undecidable {% cite rice1953classes %}. So a terminating analysis must approximate. It is **sound** if it never misses a violation (it may raise false alarms) and **complete** if every report is real (it may miss bugs). Reports fall into four cells:

| | Bug exists | No bug |
|---|---|---|
| **Reported** | true positive | false positive (false alarm) |
| **Not reported** | false negative (missed bug) | true negative |

**Precision** is true positives over all reports. A false positive costs review time; a false negative, the bug plus "a false sense of security" {% cite chess2007static %}. Verification tools lean toward soundness; industrial bug finders toward few false alarms.

→ [Soundness and Completeness](../../verif/static/limits.md)

## 3. Abstraction makes every path checkable, at a price

An analysis replaces concrete values with a few **abstract values**. Zero analysis keeps three per variable (zero, not zero, maybe zero), so three 32-bit variables shrink from 2<sup>96</sup> concrete states to 3<sup>3</sup> = 27 abstract ones. Detail lost where paths merge is where false alarms come from. Cheap checks scale to millions of lines; model checking and theorem proving (machine-checked mathematical proof) prove properties of small parts {% cite chess2007static %}.

→ [Static Analysis](../../verif/static/index.md)

## 4. Patterns, types and taint catch different faults

A parser turns code into an **abstract syntax tree (AST)**: one node per statement or expression, with its parts as children ("abstract" here means punctuation is dropped, not the abstraction of §3). Python's `ast.dump` of `while x: pass`:

```text
While(test=Name(id='x', ctx=Load()), body=[Pass()], orelse=[])
```

Pattern checkers match rules against such trees: "a variable assigned and never read", or PMD's empty-loop rule, which matches any While node with no real statement in its body. A **type checker** such as mypy infers or reads each value's type and rejects operations the type does not allow, such as indexing a value typed `Optional` (it may be `None`) before checking it. A constructed 21-line Python file has three planted faults:

```python
query = "SELECT id, name FROM users WHERE name = '" + name + "'"
...
return "Hello, " + user[1]     # user may be None
...
tax = p * 0.2                  # never used
```

pylint and flake8 found only the unused variable, mypy only the value that may be `None` (a genuine type error; it crashes only if `user` is `None` at run time), and Bandit only the concatenated query: B608, Medium severity (how bad if real), Low confidence (how sure the check is). No tool found all three; pattern checks see syntax only, not values, paths or input sources.

**Taint analysis** marks values from untrusted **sources** (a request parameter), follows them through assignments and concatenation, and reports when one reaches a **sink** (a database query, a shell command, an HTML page) without passing a **sanitizer** (parameter binding, escaping) {% cite chess2007static %}. For `query = "SELECT ... WHERE id=" + user_id; db.execute(query)` with `user_id` read from the request, the request is the source, `db.execute` the sink, and the fix is a bound parameter. Taint analyzers need a specification of every source and sink; missing ones make them miss bugs.

→ [Pattern, Type and Taint Checks](../../verif/static/techniques.md), [Triaging Static Analysis Warnings](../../verif/static/warnings.md)

## 5. Dataflow analysis computes facts until they stop changing

A **control flow graph (CFG)** has a node per **basic block** (statements always executed together) and an edge per possible jump; a loop's **loop head** is the block testing its condition. **Dataflow analysis** attaches a fact to each variable in each block and pushes facts along the edges. Zero analysis looks for division by zero. Its values form a **lattice**: Z (zero) and NZ (not zero) sit below MZ (maybe zero), and a value can only move up. Where paths meet (a merge point), the analysis takes the **join** of their values: Z join NZ = MZ. The transfer rules:

| Statement | Result |
|---|---|
| `x = 0` / `x = c`, c ≠ 0 / `x = y` | Z / NZ / y's value |
| `x = y + c` or `x = y - c`, c ≠ 0 | Z gives NZ; NZ or MZ gives MZ |
| `x = a * b` | Z if either is Z; NZ if both NZ; else MZ |
| `x = a / b` | Z if a is Z; else MZ (integer division: 1 / 2 is 0) |

A division warns when its divisor is not NZ. For `x = 10; y = x; z = 0; while (y > -1) { x = x / y; y = y - 1; z = 5; }`, the blocks are B1 (the three assignments), B2 (the loop head, testing `y > -1`), B3 (the loop body) and B4 (the exit), with edges B1→B2, B2→B3 (true), B3→B2 (back edge) and B2→B4 (false) (CFG figure on the dataflow page). Pass 1 enters the loop head with x NZ, y NZ, z Z (no warning) and leaves B3 with x MZ, y MZ, z NZ; at the loop head the joins give x MZ, y MZ, z MZ, so pass 2 warns at `x / y` and changes nothing: a **fixed point**. The warning is real: y reaches 0 while `y > -1` holds. Values only rise in a finite lattice, so it terminates.

**Null analysis** works the same way with None, not None and maybe None, plus **branch refinement**: inside `if user is not None:` the value is not None; after the join, None join not None = maybe None.

→ [Dataflow Analysis](../../verif/static/dataflow.md)

## 6. Model checking explores every state and returns a counterexample

**Model checking** explores every reachable state of a finite model and either proves a property or returns a **counterexample**, the exact steps that break it {% cite benari2010model %}. A model has **states** (program point plus variable values), **actions** (statements), **transitions** (state to state by an action), an **initial state** and **end states**, where a run finishes normally; an **error state** is one that breaks the property. Each state is taken just before a statement runs, one state per statement; declarations and initialisations are folded into the initial state, which is the state before the first loop test. For `i = 0; s = 0; while (i < 2) { s = s + i; i++; }` the states (next statement, i, s) are (loop test, 0, 0) → (`s = s + i`, 0, 0) → (`i++`, 0, 0) → (loop test, 1, 0) → (`s = s + i`, 1, 0) → (`i++`, 1, 1) → (loop test, 2, 1) → (end, 2, 1): initial (loop test, 0, 0), end (end, 2, 1). Unlike dataflow analysis it never merges paths, so a bad interleaving appears as a concrete trace a developer can replay.

Ben-Ari's example: two processes each add 1 to a shared `n` ten times through a local copy. SPIN 6.5.2 refutes `assert(n > 2)` with a run ending at n = 2: P reads 0 and pauses, Q does nine increments, P writes 1, Q reads 1 and pauses, P finishes, Q writes 2. Random simulation picks one interleaving per step, so this run is "highly unlikely"; the model checker visits all {% cite benari2010model %}.

Properties over runs use **linear temporal logic (LTL)**: **G** p (always), **F** p (eventually), **X** p (next), p **U** q (p until q). A **safety** property says nothing bad happens, G ¬deadlock; a **liveness** property says something good eventually happens, G(request → F granted).

The limit is **state explosion**: with each process incrementing three times, the model checker SPIN stored 23 states for one process and 22,565,621 for four. It "cannot be removed", only managed by abstraction, bounded search and partial-order reduction (skipping interleavings that change nothing) {% cite mansourisamani2008program %}. It is a poor fit for whole large programs (use lighter static analysis), for properties of exact data values (use testing or symbolic execution), or without a faithful model.

→ [Model Checking](../../verif/static/model-checking.md)

## 7. CEGAR tells real counterexamples from spurious ones

A locking rule as an automaton: P0 unlocked, P1 locked, and an error state reached by locking in P1, unlocking in P0, or finishing in P1. Model checking a program against it explores the **product** of program points and lock states.

SLAM's running example, with the driver calls shortened {% cite ball2001slam %}:

```c
do {
    lock(&devExt->writeListLock);        // S0
    nPacketsOld = nPackets;
    request = devExt->WriteListHeadVa;
    if (request && request->status) {    // S1
        devExt->WriteListHeadVa = request->Next;
        unlock(&devExt->writeListLock);  // S2
        nPackets++;
    }
} while (nPackets != nPacketsOld);       // S3
unlock(&devExt->writeListLock);          // S4
```

A state P · S means "lock in state P, before the statement at S". With the lock state only, the product shows two counterexamples: P0·S0 → P1·S1 → P1·S3 → P1·S0 → error (skip the `if`, loop back, lock again), and P0·S0 → P1·S1 → P1·S2 → P0·S3 → P0·S4 → error (take the `if`, exit, unlock again). A feasibility check asks if any input satisfies the path's condition. Both are **spurious**: skipping the `if` leaves `nPackets == nPacketsOld`, so the loop exits; taking it makes them differ, so the loop repeats. **Counterexample-guided abstraction refinement (CEGAR)** adds the predicate b ≡ (`nPackets == nPacketsOld`) and checks again: with b true after skipping the `if`, the edge from S3 back to S0 becomes infeasible; with b false after taking it, the edge from S3 to S4 does. No error state is reachable, so the code is correct. The loop abstracts, model-checks, tests whether the counterexample is feasible, and either reports a real bug or refines {% cite ball2001slam %}. Starting coarse keeps the first model small; detail is added only where needed.

→ [Abstraction Refinement (CEGAR)](../../verif/static/cegar.md)

## 8. Symbolic execution turns paths into tests

**Symbolic execution** runs code on symbols, collects a **path condition** along each path, and asks a solver for an input satisfying it {% cite bailey2025symexec %}. For `if (x > 0) if (y == x + 1) error();`, Z3 finds x = 1, y = 2 for the error path. **Concolic** execution runs a concrete input, whose run proves its path feasible, then asks the solver only for an input that flips one branch. The number of paths grows as 2<sup>n</sup> with n branches, called **path explosion**; remedies are scope reduction, guidance heuristics and hybrids with fuzzing {% cite bailey2025symexec %}. KLEE alternates random path selection with a search favouring uncovered code {% cite cadar2008klee %}.

→ [Symbolic Execution](../../verif/static/symbolic-execution.md)

## 9. Tools disagree, and false alarms decide adoption

Six Java tools on 47 projects had precision from 18% (SonarQube) to 86% (Checkstyle) and shared fewer than 0.4% of their warnings; no line was flagged by more than two {% cite lenarduzzi2021comparison %}. 

Google's Tricorder posts findings as review comments. Reviewers click PLEASE FIX (author must fix) or NOT USEFUL (feedback to the analyzer's writers); authors can APPLY FIX. Each analyzer's not-useful rate is NOT USEFUL / (NOT USEFUL + PLEASE FIX + APPLY FIX); at 10% or more it goes on probation and its writers must fix it; above 25% it may be switched off. Across all analyzers, about 5% of the clicks were NOT USEFUL (weekly rate, 2014). Google counts clicks because "developers will decide" what is useful and what a false positive is {% cite sadowski2015tricorder %}. At eBay, tuning FindBugs's checkers before rollout removed 75% of warnings, leaving an estimated 10% false positives; each priority got a policy (high: purge all; medium: no new ones; low: capped per release) {% cite jaspan2007understanding %}. **IRIS** has an LLM infer taint specifications for CodeQL, GitHub's taint analyzer: 55 of 120 known vulnerabilities found against CodeQL's 27, though most alarms were still false {% cite li2024iris %}. LLMs also hallucinate and vary between runs {% cite wang2025llmpa %}.

→ [Comparing Static Analysis Tools](../../verif/static/tools.md), [Deploying Static Analysis](../../verif/static/deployment.md)

## 10. Static analysis complements inspection and testing

At Nortel a phase's **fault-detection yield** was the share of the faults present at its start that it found: its faults over those it and all later phases found (usual order: inspection, testing, customers; static analysis ran before inspection in release C.1 and during testing in C.2). Automated static analysis found 23% (product A) to 37% (release C.2), close to inspection's 20% (C.1) to 40% (C.0), at 0.61 to 0.72 of inspection's cost per fault; testing found 63% to 98%. Testing's yield divided by static analysis's in the same release is 1.7 in C.2 (63 / 37) and 3.2 in C.1 (98 / 31). In IBM's Orthogonal Defect Classification, static analysis found mostly **Checking** faults (a missing or wrong validation) and **Assignment** faults (a wrong or missing value), testing mostly **Function** faults (wrong capability) and **Algorithm** faults (wrong logic) {% cite zheng2006static %}. So prefer static analysis for mechanical faults on every path, early and cheaply; prefer testing for wrong behaviour; prefer model checking over lighter analysis for concurrency and protocols where a counterexample is worth the modelling.

→ [Static Analysis Effectiveness](../../verif/static/effectiveness.md)

## Where this goes next

A07 returns to testing: few inputs covering interactions of many parameters.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
