---
title: "RQ A06: Static Analysis"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 12
redirect_from: /content/verif/static/RQ_A06_Static.html
layout: default
page_type: question-set
---

# RQ A06: Static Analysis

Revision questions for [SN A06: Static Analysis](SN_A06_Static.md). Answer in your own words and show your working; every question can be answered from the study notes and the handbook pages they link to.

**Format:** explain in your own words, with brief examples.

---

## Part 1: Foundations

## Question 1: Static vs Dynamic Analysis

Give an example of a situation in which you would prefer:

a) dynamic analysis (testing) over static analysis;
b) static analysis over dynamic analysis;
c) model checking over a lighter static analysis.

Explain each choice.

## Question 2: Soundness and Completeness

A tool reports "no bugs found" for a program that contains a null pointer dereference.

- Is the tool sound for null dereferences? Why or why not?
- Can you tell from this one result whether it is complete?
- Name a kind of tool that would report this dereference, and the price it pays for doing so.

## Question 3: Rice's Theorem

- What does Rice's theorem say about static analysis tools in practice?
- How do practical tools still work?
- Which of the three (soundness, completeness, termination) can a practical tool keep, and what does it give up?

## Question 4: Abstraction

Zero analysis abstracts each integer to {Z, NZ, MZ}.

- For three 32-bit variables, how many concrete states are there, and how many abstract ones?
- Where does the abstraction lose information, and what kind of report does that produce?
- Why does a finite set of abstract values guarantee that the analysis terminates?

---

## Part 2: Techniques

## Question 5: AST Pattern Matching

- What is an abstract syntax tree, and how do pattern checkers use it?
- How would a rule like PMD's "empty `while` body" work on the tree?
- What can a pattern check not see that dataflow analysis can?

## Question 6: Four Tools, One File

Four Python tools were run on the same file with three planted faults. Which tool found which fault, and what does the result tell a team that runs only one of them?

## Question 7: Taint Analysis

Define source, sink and sanitizer, then trace the taint through this code. Where should the sanitizer go, and what is it?

```python
user_id = request.args["id"]                       # ?
query = "SELECT * FROM users WHERE id=" + user_id
db.execute(query)                                  # ?
```

## Question 8: Zero Analysis Trace

Trace zero analysis to the fixed point. Is a divide-by-zero warning issued, and is it a real bug?

```c
a = 5;
b = 0;
if (condition) {
    b = a;
}
c = a / b;
```

## Question 9: Zero Analysis with a Loop (Practical)

```c
int a = 10;      // B1
int b = a - 10;  // B1
int c = 1;       // B1
while (b < 5) {  // B2 (loop header)
    c = c * b;   // B3
    b = b + 1;   // B3
}
int d = a / c;   // B4
```

a) Draw the control flow graph and mark the loop head.
b) Give the abstract values at each block on each pass until the fixed point.
c) What is the abstract value of `c` at B4, and is a warning issued?
d) Run the code in your head: is the warning a real bug or a false positive?

## Question 10: Null Analysis

Explain branch refinement with an example of your own. Why does a value become "maybe None" again after an `if`/`else` joins?

---

## Part 3: Model Checking and CEGAR

## Question 11: Program Model Construction (Practical)

Build a model for the code below: list the states, actions, transitions, initial state and end state. Fold the declarations into the initial state, the state before the first loop test.

```c
int x = 0;
int y = 3;
while (x < 3) {
    x++;
    y--;
}
```

## Question 12: LTL Properties

Let p be a formula. Write in LTL:

a) p holds in every state of a run;
b) p holds in some future state;
c) whenever a lock is acquired, it is eventually released.

Explain G, F, X and U in plain English, and say which of (a) and (c) is a safety property and which a liveness property.

## Question 13: The Counter That Ends at 2

Two processes each add 1 to a shared `n` ten times, through a local copy. Explain how the final value can be 2, and why random simulation rarely shows it while a model checker always finds it.

## Question 14: Product Automaton

```c
lock(m);
if (error) {
    return;
}
unlock(m);
```

Using the lock automaton (P0 unlocked, P1 locked, error), construct the product with the code's program points. Which path reaches an error, and which rule does it break?

## Question 15: Model Checking Locking (Practical)

```c
void process(int flag) {
    lock(m);           // S0
    do_work();         // S1
    if (flag) {
        lock(m);       // S2
    }
    unlock(m);         // S3
}
```

a) Draw the control flow graph.
b) Draw the product automaton (program points × lock states).
c) Identify every path that reaches an error state.
d) Is the counterexample real or spurious? How do you decide?

## Question 16: CEGAR

Explain the CEGAR loop. Why start from a coarse abstraction? Use the SLAM loop with `nPackets` (code and counterexamples in the study notes §7 and on [Abstraction Refinement (CEGAR)](../../verif/static/cegar.md)) to show what happens when a counterexample is spurious, and name the predicate that removes it.

## Question 17: Counterexamples

What is a counterexample in model checking, and why is it more useful to a developer than a warning? What is the difference between a real and a spurious counterexample?

## Question 18: Model Checking or Dataflow?

Thread A locks resource 1 and then waits for resource 2; thread B locks resource 2 and then waits for resource 1. Would you use dataflow analysis or model checking to find the deadlock? Explain.

## Question 19: When Model Checking Fails

Give three situations where model checking is not the right technique, and say what you would use instead.

---

## Part 4: Symbolic Execution and Practice

## Question 20: Symbolic Execution

```c
int f(int x, int y) {
    if (x > 0 && y > 0) {
        if (x + y > 100)
            return -1;  // error path
    }
    return 0;
}
```

What path condition must the engine solve to reach the error path? Give one input that satisfies it. What is path explosion, and which two search heuristics does KLEE alternate to cope with it?

## Question 21: Tool Disagreement

Lenarduzzi and colleagues found less than 0.4% agreement among six tools, and precision from 18% to 86%. What do these numbers mean for a team that relies on SonarQube alone? What would you recommend?

## Question 22: The 10% Rule (Practical)

In one week an analyzer's findings receive 12 NOT USEFUL, 90 PLEASE FIX and 18 APPLY FIX clicks.

a) Compute its not-useful rate as Tricorder defines it.
b) What happens to the analyzer, and who must act?
c) Why does Google measure developer reactions rather than true false-positive rates?

## Question 23: Tool Adoption

Describe two strategies for getting developers to act on static analysis findings, one from Google's Tricorder and one from eBay's FindBugs rollout.

## Question 24: Cost-Effectiveness (Practical)

In a release, static analysis found 31 faults; later, inspection found 20, testing 40 and customers 9.

a) Compute the fault-detection yield of static analysis.
b) Compare the result with the Nortel ranges for static analysis, inspection and testing.
c) Which fault types does each technique find best, and when would you invest in static analysis rather than more testing?

## Question 25: LLM-Assisted Analysis

IRIS combines an LLM with CodeQL and detects 55 instead of 27 of 120 known vulnerabilities.

- Why do conventional taint analyzers miss some of them?
- What does the LLM do, and what does CodeQL still do?
- What are the risks of relying on an LLM for security analysis?

---

## Topics Covered

| Topic | Questions |
|---|---|
| Foundations, soundness, Rice, abstraction | Q1–Q4 |
| Patterns, types, taint | Q5–Q7 |
| Dataflow analysis | Q8–Q10 |
| Model checking, LTL, CEGAR | Q11–Q19 |
| Symbolic execution | Q20 |
| Tools, adoption, evidence, LLMs | Q21–Q25 |

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
