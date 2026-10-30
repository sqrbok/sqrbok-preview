---
title: flake8 and Ruff
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 8
layout: default
page_type: tool-card
tested_with: flake8 7.5.0, Ruff 0.17.3
---

# flake8 and Ruff

**flake8** is a Python linter: it checks code against the PEP 8 style rules (codes `E`, `W`), reports likely mistakes such as unused imports (`F`), and, when asked, flags functions whose cyclomatic complexity is too high (`C901`) {% cite flake8_docs %}. **Ruff** implements the same rules and hundreds more, much faster, and can fix many of them itself {% cite ruff_docs %}.

The quality question it answers: *does the code follow the agreed rules, and is any function too complex to test well?*

*Run for this card with flake8 7.5.0, Ruff 0.17.3, October 2026.*

## Install and quick start

```bash
poetry add --group dev flake8           # or: ruff
poetry run flake8 src/
poetry run flake8 --max-complexity 10 src/
poetry run ruff check src/              # add --fix to apply the safe fixes
```

## Reading the output

A receipt function written in a hurry, `src/parking/receipt.py` (constructed example):

```python
import os, sys
from datetime import datetime


def receipt(plate, minutes, member=False, lang="en", currency=None):
    lines = []
    if lang == "en":
        title = "Parking receipt"
    elif lang == "de":
        title = "Parkbeleg"
    elif lang == "fr":
        title = "Reçu de stationnement"
    else:
        title = "Receipt"
    lines.append(title)
    if plate == None:
        plate = "UNKNOWN"
    lines.append("Plate: " + plate)
    if minutes > 60*24:
        lines.append("Warning: more than one day")
    for m in [15, 60, 120]:
        if minutes > m and member:
            lines.append(f"over {m} min (member)")
        elif minutes > m:
            lines.append(f"over {m} min")
    if currency:
        unit = currency
    else:
        unit = "EUR"
    lines.append("Currency: "+unit)
    return "\n".join(lines)
```

flake8 printed one line per finding, `file:line:column: code message` (trimmed: the F401s for `sys` and `datetime` are left out):

```text
src/parking/fee.py:1:80: E501 line too long (88 > 79 characters)
src/parking/receipt.py:1:1: F401 'os' imported but unused
src/parking/receipt.py:1:10: E401 multiple imports on one line
src/parking/receipt.py:16:14: E711 comparison to None should be 'if cond is None:'
```

The letter gives the family: **E/W** style (pycodestyle), **F** probable bugs (pyflakes), **C9** complexity (mccabe). The exit code is 1 when anything is reported, so the command works as a CI gate.

With a complexity limit, the same function is measured:

```text
$ flake8 --max-complexity 9 src/
src/parking/receipt.py:5:1: C901 'receipt' is too complex (10)
```

With `--max-complexity 10` nothing was reported: flake8 flags a function only when its complexity **exceeds** the limit.

Ruff explains each finding with the code and a suggested fix, and prints `[*]` when it can apply the fix itself:

```text
F401 [*] `os` imported but unused
 --> src/parking/receipt.py:1:8
Found 4 errors.
[*] 4 fixable with `--fix`.
```

## Common mistakes

- **Assuming two linters check the same rules.** On the same file Ruff 0.17.3 reported 4 findings and flake8 7.5 reported 5: Ruff's default rule set included import sorting (`I001`) but not `E401` or `E711`, and its default line length is 88, not 79. Older Ruff documentation lists a smaller default set (`E4`, `E7`, `E9`, `F`); `ruff check --show-settings` prints the rules your version enables. Write the rules you want into the configuration instead of relying on defaults.
- **Configuring flake8 in `pyproject.toml`.** flake8 does not read it: with `[tool.flake8] max-line-length = 100` there, it still reported `E501 (88 > 79)`. Use a `.flake8`, `setup.cfg` or `tox.ini` file. Ruff reads `[tool.ruff]` in `pyproject.toml`.
- **Linting generated folders.** `flake8 .` also checks build and tool folders; on the example project, most of its warnings came from mutmut's `mutants/` copy. Add `extend-exclude = mutants` (or list them in `exclude`).
- **Silencing instead of fixing.** `# noqa` without a code hides every finding on that line; write `# noqa: E501`.

A `.flake8` file for the example:

```ini
[flake8]
max-line-length = 100
max-complexity = 10
extend-exclude = mutants
```

## In CI

```yaml
- run: poetry run flake8 src tests
```

## Go deeper

- [Size and Complexity Metrics](../../define/measurement/examples.md): what cyclomatic complexity counts.
- [Radon](radon.md): the same metric with grades, and why it gave 11 where flake8 gave 10.
- [Worked Case: Quality Gates for a Python Repository](../../verif/static/worked-case.md): choosing which rules gate a build.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
