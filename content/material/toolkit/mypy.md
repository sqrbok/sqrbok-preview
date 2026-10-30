---
title: mypy
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 10
layout: default
page_type: tool-card
tested_with: mypy 2.4.1, Python 3.12
---

# mypy

**mypy** is a static type checker for Python: it reads the type hints in your code (`minutes: int`, `-> str`) and, without running anything, reports every place where the types do not fit together {% cite mypy_docs %}.

The quality question it answers: *can a value of the wrong type reach this line?* It finds a class of bugs that a test finds only if a test happens to pass that value.

*Run for this card with mypy 2.4.1, Python 3.12, October 2026.*

## Install and quick start

```bash
poetry add --group dev mypy
poetry run mypy src/
poetry run mypy --strict src/
```

## Reading the output

A lookup module, `src/parking/lookup.py`, with two type bugs and one untyped copy of the second (constructed example):

```python
RATES = {"standard": 2, "member": 1}


def rate(kind: str) -> int:
    return RATES.get(kind)


def label(minutes: int) -> str:
    return "Parked " + minutes + " min"


def untyped(minutes):
    return "Parked " + minutes + " min"
```

```text
$ mypy src/
src/parking/lookup.py:5: error: Incompatible return value type (got "int | None", expected "int")  [return-value]
src/parking/lookup.py:9: error: Unsupported operand types for + ("str" and "int")  [operator]
Found 2 errors in 1 file (checked 4 source files)
```

(The count of files checked depends on what else is in `src/`.)

Each line gives the file, the line, the problem and, in brackets, the **error code**. Line 5: `dict.get` returns `None` for an unknown key, and the function promises an `int`. Line 9: adding a string and a number raises `TypeError` at run time.

## Common mistakes

- **Expecting mypy to check untyped code.** Line 13 has the same bug as line 9, but mypy said nothing: by default it does not check the body of a function without annotations. `--strict` reported the function on line 12 as `Function is missing a type annotation [no-untyped-def]`, also flagged the untyped receipt function in another file, and added `no-any-return` on line 9: 5 errors in 2 files.
- **Silencing with `Any` or `# type: ignore`.** Both switch the check off. Write `# type: ignore[return-value]` with the code, so the next reader knows what was accepted.
- **Hiding the `None` case.** The fix for line 5 is a decision, not a cast: raise for an unknown kind, return a default (`RATES.get(kind, 2)`), or declare `-> int | None` and make the callers handle it.
- **Turning on `--strict` for an old code base at once.** Hundreds of errors get ignored. Start with the default, add `--strict` per module, and keep the count falling.

## In CI

```yaml
- run: poetry run mypy src/
```

mypy exits 1 when it reports an error.

## Go deeper

- [Pattern, Type and Taint Checks](../../verif/static/techniques.md): what a type check can prove, and what it cannot.
- [Soundness and Completeness](../../verif/static/limits.md): why a checker either misses bugs or reports false alarms.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
