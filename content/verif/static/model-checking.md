---
title: Model Checking
parent: Static Analysis
nav_order: 6
layout: default
vegalite: true
page_type: deep-dive  # SPIN run with its counterexample, temporal logic for the RQ, and the measured state explosion
---

# Model Checking

**Model checking** verifies a property by exploring every reachable state of a finite model of the system. If the property holds in all of them, it is proved for the model; if not, the checker returns a **counterexample**, the exact sequence of steps that breaks it {% cite benari2010model %}.

## 1. How it works

The engineer supplies two things: a **model**, a set of states and the transitions between them (processes, their variables, their interleavings), and a **property** that every run must satisfy. The checker searches the state graph, depth-first or breadth-first, remembering every state it has visited. Unlike testing, it does not sample runs; unlike dataflow analysis, it does not merge paths, so its verdict is exact for the model. Ben-Ari puts it as analysing the program's state space with software instead of proving it correct by hand {% cite benari2010model %}.

## 2. Example: SPIN finds a result nobody expected

Ben-Ari's example has two processes that each add 1 to a shared `n` ten times, but through a local copy, as a processor does with a register {% cite benari2010model %}. In SPIN's modelling language, Promela:

```promela
byte n = 0, finish = 0;

active [2] proctype P() {
  byte temp, counter = 0;
  do
  :: counter == 10 -> break
  :: else -> temp = n; temp++; n = temp; counter++
  od;
  finish++
}

active proctype WaitForFinish() {
  finish == 2;
  printf("n = %d\n", n);
  assert(n > 2)
}
```

In Promela, `do :: … od` repeats one of its options forever until a `break`; each option starts with a guard, and `->` separates the guard from the statements it allows. A statement that is just a condition, such as `finish == 2`, blocks the process until it becomes true. Run one after the other, the processes give 20; alternating one statement at a time, so that each read and write interleaves, 10. Running SPIN 6.5.2 in verification mode on this model (the local variable is renamed from Ben-Ari's `register`, a reserved word in C) answers within a second:

```text
pan:1: assertion violated (n>2) (at depth 106)
pan: wrote count.pml.trail
...
		n = 2
		finish = 2
```

The **depth** is the number of steps from the initial state to the violation; the trail file records those steps for replay. The final value can be **2**. In one such run, P reads `n = 0` and pauses; Q completes nine increments; P writes 1; Q reads that 1 and pauses; P completes its last nine increments; Q writes 2 and stops. Ben-Ari taught for years that the answer lay between 10 and 20 until a student's random simulation produced 9; the model checker finds the extreme case directly, together with the steps that lead to it {% cite benari2010model %}.

## 3. Properties in temporal logic

Assertions check one state. Properties over whole runs are written in **linear temporal logic (LTL)**:

| Operator | Reads | Holds on a run when… |
|---|---|---|
| **G** p (`[]p` in SPIN) | globally, always | p holds in every state |
| **F** p (`<>p`) | finally, eventually | p holds in some later state |
| **X** p | next | p holds in the next state |
| p **U** q | until | p holds in every state until q holds |

The counter's claim was checked as an assertion; written in LTL it would be G(finish = 2 → n > 2): whenever both workers have finished, n exceeds 2. A **safety** property says nothing bad ever happens: G ¬deadlock, "the system never deadlocks". A **liveness** property says something good eventually happens: G(request → F granted), "every request is eventually granted". A safety violation has a finite counterexample; a liveness violation is a run that loops forever without the good event.

## 4. State explosion

Every added process or variable multiplies the states. The same model, with each process incrementing only 3 times, was checked with 1 to 4 processes (changing `active [2]` and the `finish == 2` test in WaitForFinish to the number of processes, and the assertion to `true`, so every state is explored); every search completed:

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 400, "height": 190,
  "data": {"values": [
    {"p": "1", "s": 23}, {"p": "2", "s": 1358}, {"p": "3", "s": 150845}, {"p": "4", "s": 22565621}
  ]},
  "encoding": {
    "x": {"field": "p", "type": "ordinal", "title": "Processes", "axis": {"labelAngle": 0, "labelFontSize": 14}},
    "y": {"field": "s", "type": "quantitative", "title": "States stored (log scale)", "scale": {"type": "log", "domain": [10, 100000000]}, "axis": {"format": "~s"}}
  },
  "layer": [
    {"mark": {"type": "line", "color": "#019546", "strokeWidth": 3, "point": {"filled": true, "size": 90, "color": "#019546"}}},
    {"mark": {"type": "text", "dy": -14, "fontSize": 13}, "encoding": {"text": {"field": "s", "type": "quantitative", "format": ","}}}
  ],
  "config": {"font": "Tahoma", "axis": {"titleFontSize": 13, "labelFontSize": 12}, "view": {"stroke": null}}
}
```

_States SPIN 6.5.2 stored for the counter model with each process looping 3 times; measured for this page._

Each added process multiplies the states by roughly 60 to 150. The NASA practitioner's guide calls state explosion "a fundamental problem that cannot be removed", only managed {% cite mansourisamani2008program %}. The ways to manage it are abstraction (keep only variables the property needs, the idea behind [CEGAR](cegar.md)), bounded or guided search, and partial-order reduction, which skips interleavings that cannot change the result; SPIN applied it in the runs above.

Widely used checkers include SPIN for concurrent protocols, Java PathFinder for Java bytecode (from NASA), and SLAM and BLAST for C, both built on abstraction refinement {% cite mansourisamani2008program %}.

## 5. In industry

Chiari and colleagues interviewed seven engineers at ASML, Philips and Thermo Fisher who use Verum Dezyne, a tool that model-checks component designs and generates C++ from them {% cite chiari2025formal %}. Few saw scalability as a problem: 57% (four of the seven) described the approach as scaling well, and the others managed by decomposition, splitting a system into components small enough to verify. The learning curve, not scale, was the obstacle they named. And 71% (five of seven) said the quality of complex systems can be assessed without formal methods, against 42% in an earlier survey of experts.

## Model checking or dataflow analysis?

| | Dataflow analysis | Model checking |
|---|---|---|
| Paths | merged at every join | kept apart, every interleaving |
| Concurrency | weak | its main strength |
| Scale | millions of lines | a model of a component |
| Result | warnings with a location | proof, or a counterexample trace |

## When to use it — and when not

- Use it for concurrency and protocols: deadlocks, races, ordering rules, where one rare interleaving matters and testing will not find it.
- Do not use it for a whole large program, or for properties that need exact data values; the model grows too large, and building the model is itself work that can introduce errors ("rubbish in, rubbish out", one of Chiari's engineers said).

## How solid is this?

- **Where it comes from.** The SPIN outputs were produced with SPIN 6.5.2 on Ben-Ari's model {% cite benari2010model %}; the explosion quote is from the NASA guide {% cite mansourisamani2008program %}; the industry view from Chiari and colleagues {% cite chiari2025formal %}.
- **What is contested.** The Dezyne study has seven practitioners from one vendor's customers, chosen through that vendor; its percentages are indicative only.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
