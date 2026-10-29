---
title: Dynamic Analysis and the Probe Effect
parent: Verification by Analysis
nav_order: 3
layout: default
---

# Dynamic Analysis and the Probe Effect

**Dynamic analysis** learns about a program by running it and recording information about each execution, such as which lines ran, how much memory was used, or which threads touched a variable. Its findings are exact for the runs observed, but it sees only the paths those inputs take {% cite chess2007static %}.

## 1. How execution information is collected

A dynamic analysis has five parts: the property of interest (say, no read of uninitialised memory), the information that reveals it, a mechanism that collects that information, the test inputs, and a way to draw a conclusion from what was collected. The collection mechanism is usually one of three kinds.

- **Instrumented code.** Probes are inserted into the source or bytecode, or through hooks the interpreter provides: logging statements, aspects, coverage tools such as coverage.py. Inserting the probes is a static step, but the analysis is still dynamic, because the conclusions come from running the program.
- **An instrumenting virtual machine.** Valgrind translates the program's machine code as it runs and adds analysis code to it; its Memcheck tool keeps a "shadow" bit for every bit of memory, recording whether it has been defined {% cite nethercote2007valgrind %}.
- **A monitor attached at runtime.** Profilers such as VisualVM connect to a running Java virtual machine through its monitoring interfaces (tool documentation).

## 2. Example: the probe changes the run

Observation is not free. Valgrind measured its own cost on SPEC CPU2000: the program ran 4.3 times slower with no analysis at all and 22.2 times slower under Memcheck, and Valgrind runs only one thread at a time {% cite nethercote2007valgrind %}. A slower, serialised program can schedule its threads differently, so the probe itself can change what is observed. This is the **probe effect**: an attempt to observe a concurrent program may change its behaviour {% cite mcdowell1989debugging %}. A bug that disappears or changes when someone looks for it is informally called a **Heisenbug**.

The effect is easy to reproduce. Two threads below each add 1 to a shared counter a million times, with no lock:

```python
counter = 0
def work(n):
    global counter
    for _ in range(n):
        v = counter
        counter = v + 1
# two threads run work(1_000_000), then counter is printed
```

Each version was run five times with CPython 3.12.9 in October 2026:

| How it was run | Updates lost per run | Median time |
|---|---|---|
| plain `python3 race.py` | 0 in all five runs | 0.06 s |
| `coverage run race.py` (coverage.py 7.17.0) | 0 in all five runs | 0.22 s |
| `python3 -m trace --count race.py` | 398,524 to 524,560 | 0.92 s |

The code is wrong in every run: reading and writing `counter` in two steps is a race. The plain interpreter happened to switch threads only between loop iterations, so the race never showed. The standard `trace` module calls a Python function on every line, which lets threads switch between the read and the write, and 20 to 26% of the two million updates were lost. A developer who added `print` calls to debug a lost update could see the opposite: the bug vanishing under observation.

**Where the inputs come from.** The test inputs are the existing test suite, replayed production logs, or inputs built to reproduce one failure, such as a memory leak. The more they resemble real use, the more the findings say about real use.

## When to use it — and when not

- Use it for properties that depend on real executions: memory errors, performance, coverage, races seen in practice.
- Do not use a clean run as proof: an input not run, or a thread schedule not taken, is not checked. Ask, too, whether the monitor can run in production and whether it disturbs the property being measured.

## Limitations

Results depend on the inputs. Logging every event in a long-running program produces more data than can be processed, so most tools sample, and sampling can miss a rare event. Timing-sensitive properties such as races and latency are exactly the ones a probe disturbs most.

## How solid is this?

- **Where it comes from.** Valgrind's design and its slowdown figures are from Nethercote and Seward {% cite nethercote2007valgrind %}; the probe effect from McDowell and Helmbold's survey {% cite mcdowell1989debugging %}; the race runs were made for this page.
- **What is contested.** The race result depends on the interpreter version; another CPython release may switch threads at other points and lose updates in the plain run too.
- **What we do not hold.** A primary source for the term "Heisenbug".

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
