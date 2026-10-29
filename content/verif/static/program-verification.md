---
title: Hoare Logic
parent: Static Analysis
nav_order: 7.5
layout: default
math: katex
---

# Hoare Logic

**Hoare logic** proves that a program meets its specification, written as a **Hoare triple** $$\{P\}\ C\ \{Q\}$$: if command $$C$$ starts in a state where the **precondition** $$P$$ holds and terminates, it ends in a state where the **postcondition** $$Q$$ holds {% cite hoare1969axiomatic %}.

Hoare wrote $$P\ \{C\}\ Q$$ and proved only this **partial correctness**: a program that never stops satisfies every triple. **Total correctness** also proves termination {% cite hoare1969axiomatic %}.

## 1. The rules

If the triples above a line are proved, the one below follows.

$$\{Q[E/x]\}\ x := E\ \{Q\} \qquad \frac{\{P\}\ C_1\ \{R\} \quad \{R\}\ C_2\ \{Q\}}{\{P\}\ C_1; C_2\ \{Q\}}$$

$$\frac{\{P \land B\}\ C_1\ \{Q\} \quad \{P \land \lnot B\}\ C_2\ \{Q\}}{\{P\}\ \text{if } B \text{ then } C_1 \text{ else } C_2\ \{Q\}} \qquad \frac{\{I \land B\}\ C\ \{I\}}{\{I\}\ \text{while } B \text{ do } C\ \{I \land \lnot B\}}$$

$$\frac{P \Rightarrow P' \quad \{P'\}\ C\ \{Q'\} \quad Q' \Rightarrow Q}{\{P\}\ C\ \{Q\}}$$

Read them as follows.

- **Assignment** (the axiom). To get the precondition, take the postcondition and replace every $$x$$ by $$E$$.
- **Sequence.** The first command's postcondition is the second's precondition.
- **If.** Both branches must reach $$Q$$, each knowing which way the test went. (Hoare's paper has no conditional rule; this is the later standard form.)
- **While.** $$I$$ is the **loop invariant**: true before the loop and preserved by every pass of the body. At exit, the invariant still holds and the test is false.
- **Consequence.** A precondition may be strengthened and a postcondition weakened {% cite hoare1969axiomatic %}. It is used at loop entry and exit and in the conditional example below.

## 2. Example: three triples and one that fails

Each triple is checked by substituting into $$Q$$ and comparing with $$P$$.

| Triple | After substitution | Verdict |
|---|---|---|
| $$\{x = y\}\ x := x + 3\ \{x = y + 3\}$$ | $$x + 3 = y + 3$$, that is $$x = y$$ | proved |
| $$\{x > -1\}\ x := 2x + 3\ \{x > 1\}$$ | $$2x + 3 > 1$$, that is $$x > -1$$ | proved |
| $$\{\text{true}\}\ x := 5\ \{x = 5\}$$ | $$5 = 5$$, that is true | proved |
| $$\{x \ge 0\}\ x := x - 1\ \{x \ge 0\}$$ | $$x - 1 \ge 0$$, that is $$x \ge 1$$ | **fails** at $$x = 0$$ |

For a conditional, by the if rule and consequence, $$\{P\}$$ if $$x > 0$$ then $$y := z$$ else $$y := -z$$ $$\{y > 5\}$$ holds for $$P = (x > 0 \Rightarrow z > 5) \land (x \le 0 \Rightarrow z < -5)$$.

## 3. A loop needs an invariant

(Constructed example.) The loop below sums $$1 + 2 + \dots + n$$:

```text
{ n ≥ 0 }
i := 0; s := 0;
while i < n do
  i := i + 1; s := s + i
{ 2s = n(n + 1) }
```

Choose the invariant $$I:\ 2s = i(i + 1) \land i \le n$$. Three obligations follow.

1. **Initialisation** (consequence). With $$i = s = 0$$: $$0 = 0$$, and $$0 \le n$$ from the precondition.
2. **Preservation.** Working back through the body, $$I$$ after it needs $$2(s + i + 1) = (i + 1)(i + 2)$$ and $$i + 1 \le n$$ before it. The first follows from $$2s = i(i+1)$$ by adding $$2(i + 1)$$ to both sides; the second from the loop test $$i < n$$.
3. **Exit** (consequence). $$i \le n$$ and $$\lnot(i < n)$$ give $$i = n$$, so $$2s = n(n + 1)$$.

Without $$i \le n$$, exit gives only $$i \ge n$$ and the proof fails. For total correctness, $$n - i$$ decreases on every pass and stays at least 0, so the loop stops.

## 4. Where it is used

Verifiers turn the rules into **verification conditions**, plain formulas that a solver checks; the developer writes only the specification and the invariants. In Dafny these are `requires`, `ensures` and `invariant` clauses, checked with the Z3 solver {% cite leino2010dafny %}:

```dafny
method Sum(n: int) returns (s: int)
  requires n >= 0
  ensures 2 * s == n * (n + 1)
{
  var i := 0; s := 0;
  while i < n
    invariant i <= n && 2 * s == i * (i + 1)
  { i := i + 1; s := s + i; }
}
```

KeY does the same for Java with JML contracts, Frama-C's WP plugin for C, and SPARK, an annotated subset of Ada with its own proof tools, for safety-critical software {% cite mansourisamani2008program %}. [Analysis Tools by Family](../overview/analysis/tools.md) lists current tools.

**Where the inputs come from.** Pre- and postconditions come from the requirements or the function's documented contract; the developer writes the invariants.

## When to use it — and when not

- Use it for small, critical functions with an exact specification: a sort, an access-control check, a control law.
- Do not use it where the specification is vague or changing; writing it can cost more than the code.

## Limitations

A proof is only as good as its specification: what the postcondition omits is unchecked. The logic proves properties of mathematical integers unless machine arithmetic is modelled; with 32-bit integers, $$n(n + 1)$$ overflows when $$n$$ reaches 46,341.

## How solid is this?

- **Where it comes from.** The triple and the assignment, consequence, composition and iteration rules are Hoare's {% cite hoare1969axiomatic %}; Dafny is Leino's {% cite leino2010dafny %}; SPARK from the NASA guide {% cite mansourisamani2008program %}. All verdicts and obligations were checked with the Z3 solver for this page.
- **What we do not hold.** A Dafny run of the listing; the original source of the conditional rule.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
