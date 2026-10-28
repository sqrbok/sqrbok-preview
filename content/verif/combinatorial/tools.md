---
title: Combinatorial Testing Tools
parent: Combinatorial Testing
nav_order: 6
layout: default
page_type: deep-dive  # carries a tested coverage-check script with its results and the seed-reuse comparison
---

# Combinatorial Testing Tools

A **combinatorial testing tool** reads an input model (parameters, values, constraints and a strength *t*) and writes a covering array, one row per test. Three free tools cover most needs: Microsoft's PICT, NIST's ACTS and the research tool CASA {% cite microsoft_pict lei2007ipog garvin2011casa %}.

| Tool | Generation | Constraints | Best fit |
|---|---|---|---|
| PICT | heuristic; same output on every run unless randomized | yes, `IF … THEN` rules; `~` marks invalid values | text models kept in version control, command line |
| ACTS | IPOG by default | yes | the most studied in industry; seeds, mixed strength |
| CASA | simulated annealing with a SAT solver | yes, complex ones | heavily constrained models with expensive tests |

## 1. PICT in a pipeline

PICT takes a plain text model, one parameter per line, and prints a tab-separated table: `pict model.txt` for pairwise, `pict model.txt /o:3` for 3-way. Its documentation says only that it packs combinations into rows with heuristics, because finding the smallest array is a hard problem. Unless told to randomize (option `/r`), it gives the same output on every run, so a regenerated suite does not change between builds. On the 34-switch model of the [hub page](index.md) it produced the 4-way array of 87 rows in about half a second {% cite microsoft_pict %}.

For example, `pict find.txt > find.tsv` saves the 24-row Find dialog array from [Combinatorial Input Models](practice.md). Before trusting an array, check it against the model. This script takes the model and the output file. It reads the values from the model, not the output, so a value missing from every row is also caught; it skips pairs that a constraint forbids and pairs of two `~` values, as PICT does; and it counts rows that break either rule. The constraint is written once more in Python, in `valid`:

```python
import csv, itertools, sys

model, output = sys.argv[1], sys.argv[2]
values = {}                                    # parameter -> values, from the model
for line in open(model):
    if ":" in line and not line.startswith("IF"):
        name, vals = line.split(":", 1)
        values[name.strip()] = [v.strip() for v in vals.split(",")]

def valid(row):                                # the model's constraints, rewritten in Python
    return not (row.get("Browser") == "Safari" and row.get("OS") != "macOS")

# a pair is required if some valid full test with at most one ~ value contains it
names = list(values)
required = set()
for combo in itertools.product(*values.values()):
    row = dict(zip(names, combo))
    if valid(row) and sum(v.startswith("~") for v in combo) <= 1:
        for a, b in itertools.combinations(names, 2):
            required.add((a, row[a], b, row[b]))

with open(output, newline="") as f:
    rows = list(csv.DictReader(f, delimiter="\t"))
found = {(a, r[a], b, r[b]) for r in rows for a, b in itertools.combinations(names, 2)}
bad = sum(not valid(r) or sum(v.startswith("~") for v in r.values()) > 1 for r in rows)
print(f"{len(rows)} tests, {len(required)} required pairs, "
      f"{len(required - found)} missing, {bad} invalid tests")
```

Run it as `python3 check_pairs.py find.txt find.tsv`. It enumerates every full combination, so it suits models of up to about a million combinations. Results on this section's models:

| Model | Output | Printed |
|---|---|---|
| Find dialog | 24 rows | `24 tests, 72 required pairs, 0 missing, 0 invalid tests` |
| OS, Browser, Language, with the Safari constraint | 8 rows | `8 tests, 19 required pairs, 0 missing, 0 invalid tests` |
| same, unconstrained rows with the invalid ones deleted | 7 rows | `7 tests, 19 required pairs, 2 missing, 0 invalid tests` |
| same, unconstrained rows kept | 9 rows | `9 tests, 19 required pairs, 0 missing, 2 invalid tests` |
| order model with `~` values | 10 rows | `10 tests, 24 required pairs, 0 missing, 0 invalid tests` |

The table then plugs directly into a test framework. This pytest file turns each row into one test case:

```python
import csv
import pytest

with open("find.tsv", newline="") as f:
    ROWS = list(csv.DictReader(f, delimiter="\t"))

@pytest.mark.parametrize("row", ROWS)
def test_find_dialog(row):
    # map each class to a concrete value, run the dialog, check the result
    assert row["Direction"] in {"Up", "Down"}
```

`pytest -q` reports `24 passed`. The assertion here only checks the input; a real test maps each class to a concrete value, runs the dialog and compares the result with the expected one.

## 2. ACTS and CASA

ACTS generates with IPOG and is the tool behind both large industrial studies on [Combinatorial Testing in Industry](effectiveness.md): Hu et al. used it on five systems, and Daoud et al. used ACTS 3.2 at Tricentis, including mixed-strength arrays {% cite hu2020realworld daoud2025effectiveness %}. CASA trades generation time for smaller arrays under complex constraints: on constrained problems its arrays had 25% fewer rows than a greedy tool's {% cite garvin2011casa %}.

## 3. Reusing existing tests as seeds

Most generators accept **seed tests**: existing tests that must appear in the output. (These are unrelated to the random-number seed that some generators use.) Bombarda and Gargantini compared ACTS, PICT and pMEDICI+, a research generator, on 50 benchmark models in three situations {% cite bombarda2025completion %}:

| Situation | What the seeds were | Finding |
|---|---|---|
| Completing a suite | a valid suite with some tests removed | seeds rarely helped: ACTS always produced larger suites with them; PICT got smaller only when over 70% of the suite had been removed |
| Raising the strength | a pairwise suite, extended to 3-way | no significant effect: ACTS averaged 864 tests with or without seeds; PICT grew |
| Completing tests | tests with some values blank | not recommended: the extra work cancelled any gain |

Across all three, ACTS gave the smallest suites fastest; in the completion experiment it averaged 2.3 seconds per model, against 14.3 for pMEDICI+ and 42.3 for PICT. Seeds are therefore for keeping tests you must keep, such as tests tied to past defects, not for saving tests. In PICT the seed tests go in a file in the output's own tab-separated format, passed with `/e`:

```text
pict find.txt /e:seeds.tsv
```

With a two-row seed file, the Find dialog array still has 24 rows, and its first two rows are the seeds.

## When to use it — and when not

- **Use** PICT or ACTS by default, with the model in version control next to the tests.
- **Use** CASA, or another search-based tool, when constraints are complex and each test is expensive.
- **Do not** expect the tool to produce expected results: it chooses inputs, and the oracle is your job.

## Limitations

Tools generate abstract rows. Hu et al. list the missing step from rows to executable test scripts among the barriers to industrial use {% cite hu2020realworld %}.

## How solid is this?

- **Where it comes from.** The IPOG and CASA papers, two industrial studies, Bombarda and Gargantini's comparison, and PICT 3.7.4 runs on this section's models {% cite lei2007ipog garvin2011casa hu2020realworld daoud2025effectiveness bombarda2025completion microsoft_pict %}.
- **What is contested.** Tool rankings depend on the benchmark; Bombarda and Gargantini tested only *t* = 2 and 3, and only valid seeds.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
