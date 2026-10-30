---
title: Radon
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 9
layout: default
page_type: tool-card
tested_with: radon 6.0.1
---

# Radon

**Radon** computes code metrics for Python: cyclomatic complexity per function with a letter grade, raw line counts, Halstead metrics and the Maintainability Index per file {% cite radon_docs %}.

The quality question it answers: *where is the code hardest to understand and change?* It ranks; it does not decide. A grade C function is a candidate to look at, not a defect.

*Run for this card with radon 6.0.1, October 2026.*

## Install and quick start

```bash
poetry add --group dev radon
poetry run radon cc -s -a src/       # complexity per function, with scores and the average
poetry run radon mi -s src/          # Maintainability Index per file
poetry run radon raw -s src/         # LOC, SLOC, comments, blank lines
poetry run radon cc -s -n C src/     # only functions graded C or worse
```

## Reading the output

On the parking-fee function from the [pytest card](pytest.md) and the receipt function from the [flake8 card](flake8-ruff.md) (constructed examples):

```text
$ radon cc -s -a src/
src/parking/fee.py
    F 5:0 fee - A (4)
src/parking/receipt.py
    F 5:0 receipt - C (11)

2 blocks (classes, functions, methods) analyzed.
Average complexity: B (7.5)
```

Read a line as: **F** (function; **M** method, **C** class), line 5, column 0, name, grade, score. The complexity grades are A 1–5, B 6–10, C 11–20, D 21–30, E 31–40, F 41 and above.

```text
$ radon mi -s src/
src/parking/__init__.py - A (100.00)
src/parking/fee.py - A (63.80)
src/parking/receipt.py - A (51.49)
```

The Maintainability Index is per file, on a 0–100 scale; A is above 19, so almost every real file is an A. Compare files with each other, and watch the trend over time, rather than reading the grade. The 63.80 is for `fee.py` as printed on the pytest card: after the mutmut card adds one comment, `# pragma: no mutate`, the same file scores 80.91.

## Common mistakes

- **Expecting flake8 and Radon to agree.** On the same function, Radon scored 11 and flake8's `C901` scored 10. Radon adds one for each boolean operator (`minutes > m and member`); mccabe, which flake8 uses, does not. A limit of "10" therefore means different things in the two tools.
- **Averaging away the problem.** `Average complexity: B (7.5)` hides the one C function. Gate on the worst function (`-n C`), not the average.
- **Treating MI as a measurement of maintainability.** It is a regression formula from the 1990s, sensitive to comment lines; the [Maintainability Index](../../define/measurement/maintainability-index.md) page shows how much one comment moves it.

## In CI

Radon itself always exits 0. To fail a build on complexity, use flake8's `--max-complexity`, or the companion tool `xenon` (`poetry add --group dev xenon`), which wraps Radon with thresholds. On the example, `xenon --max-absolute B src/` (fail if any single block is graded worse than B; xenon 0.9.3) printed `ERROR:xenon:block "src/parking/receipt.py:5 receipt" has a rank of C` and exited 1.

## Go deeper

- [Rating Python Code with Radon](../../attributes/maintainability/radon.md): the MI formula worked through, and two SIG-style ratings from Radon output.
- [Cognitive Complexity](../../attributes/maintainability/cognitive-complexity.md): a complexity measure that follows readers rather than paths.
- [Worked Case: Where to Refactor Click](../../attributes/maintainability/worked-case.md): Radon output turned into a refactoring decision.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
