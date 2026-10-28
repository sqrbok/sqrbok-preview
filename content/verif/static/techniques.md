---
title: Pattern, Type and Taint Checks
parent: Static Analysis
nav_order: 2
layout: default
page_type: deep-dive  # four real tool runs on one file plus the goto-fail case; each technique needs its output shown
---

# Pattern, Type and Taint Checks

The everyday static checks look for **patterns** in the code's text or syntax tree, check **types**, follow **control flow**, and track **taint** from untrusted input to dangerous calls. They are cheap enough to run on every commit, and each sees a different slice of the faults {% cite chess2007static %}.

## 1. Example: one file, four tools

The file below is a constructed example with three planted faults: an unused variable, a value that may be `None`, and an SQL query built from user input.

```python
"""User lookup for a small shop."""
import sqlite3
from typing import Optional


def find_user(db: sqlite3.Connection, name: str) -> Optional[tuple]:
    query = "SELECT id, name FROM users WHERE name = '" + name + "'"
    return db.execute(query).fetchone()


def greeting(db: sqlite3.Connection, name: str) -> str:
    user = find_user(db, name)
    return "Hello, " + user[1]


def total(prices: list[float]) -> float:
    result = 0.0
    for p in prices:
        tax = p * 0.2
        result += p
    return result
```

Each tool, run with its default settings, reports a different fault (pylint's three missing-docstring messages and Bandit's summary lines omitted; long lines wrapped):

```text
$ pylint users.py
users.py:19:8: W0612: Unused variable 'tax' (unused-variable)
$ flake8 users.py
users.py:19:9: F841 local variable 'tax' is assigned to but never used
$ mypy users.py
users.py:13: error: Value of type "tuple[Any, ...] | None"
    is not indexable  [index]
$ bandit users.py
>> Issue: [B608:hardcoded_sql_expressions] Possible SQL injection
   vector through string-based query construction.
   Severity: Medium   Confidence: Low
   CWE: CWE-89
   Location: ./users.py:7:12
```

| Tool (version) | Technique | Found | Missed |
|---|---|---|---|
| pylint 4.1.3 | AST rules | unused `tax` (line 19) | the `None` index, the injection |
| flake8 7.4.1 | AST rules (pyflakes) | unused `tax` (line 19) | the `None` index, the injection |
| mypy 2.4.0 | type checking | `user` may be `None` (line 13) | the unused variable, the injection |
| Bandit 1.9.4 | AST security patterns | SQL built by concatenation (line 7) | the unused variable, the `None` index |

No single tool found all three faults; together they did. The same pattern holds at scale: six Java tools run on 47 projects agreed on fewer than 0.4% of their warnings ([Comparing Static Analysis Tools](tools.md)) {% cite lenarduzzi2021comparison %}. The fixed file, with a parameterised query, a `None` check and no unused variable, raises no warning from any of the four; only pylint's docstring reminders remain.

## 2. Patterns on the syntax tree

Pylint and flake8 parse the file into an **abstract syntax tree (AST)** and match rules against it: "a name assigned in a function and never read". Bandit matches a security pattern: a string that looks like SQL built by concatenation. It cannot know whether `name` comes from a user, which is why it rates its own confidence Low. The same technique finds known faulty idioms in other languages: FindBugs (now SpotBugs), for example, recognises the broken double-checked locking idiom in Java {% cite chess2007static %}. Pattern checks are fast and exact about syntax, but they see no values and no paths.

## 3. Types

`mypy` knows that `find_user` may return `None` and that `None` cannot be indexed, so it rejects `user[1]` until the code checks for `None`. Within the annotated code a type checker is sound for this property: no unchecked `Optional` slips through, even in a branch no test reaches. The guarantee stops where types stop: values typed `Any` and unannotated functions are not checked, which is why mypy's `--strict` mode also reports them. The cost is annotations, and reports where the programmer knows more than the types say ([Soundness and Completeness](limits.md)).

## 4. Control flow: the goto-fail bug

In 2014 Apple's TLS code accepted forged server keys because a duplicated `goto fail;` skipped the signature check (CVE-2014-1266) {% cite nist_cve_2014_1266 %}. A reduced C file, `gotofail.c`, shows the shape (line numbers added):

```c
 1  static int check(int a, int b) { return a - b; }
 2
 3  int verify(int a, int b, int c) {
 4      int err;
 5      if ((err = check(a, b)) != 0)
 6          goto fail;
 7          goto fail;          /* duplicated: always jumps, err == 0 */
 8      if ((err = check(b, c)) != 0)
 9          goto fail;
10      err = 0;                /* signature verified */
11  fail:
12      return err;
13  }
```

Apple clang 21 warns twice, once with `-Wall` and once with `-Wunreachable-code` (first line wrapped):

```text
gotofail.c:7:9: warning: misleading indentation; statement is not part
    of the previous 'if' [-Wmisleading-indentation]
gotofail.c:8:16: warning: code will never be executed [-Wunreachable-code]
```

The second warning comes from the **control flow graph** (explained on [Dataflow Analysis](dataflow.md)): no path reaches the second check, so its code is dead. A test that only sends valid signatures passes, because the function returns success either way.

## 5. Taint: from source to sink

**Taint analysis** marks values from untrusted **sources** (a web form, a file, the network), follows them through assignments and string operations, and reports when one reaches a **sink** such as `db.execute` without passing a **sanitizer** {% cite chess2007static %}. In a web shop, `name` would come from a request: tainted. It flows into `query`, then into `db.execute`, the sink. The sanitizer here is parameter binding, `db.execute("... WHERE name = ?", (name,))`, which keeps the value out of the SQL text. The same scheme covers the common injection faults {% cite chess2007static %}:

| Vulnerability | Typical source | Sink | Sanitizer |
|---|---|---|---|
| SQL injection | request parameter | database query | parameter binding |
| Cross-site scripting | form input | HTML page | HTML escaping |
| Command injection | user input | shell command | no shell; fixed argument list |

Unlike Bandit's pattern, a taint analyzer such as CodeQL reports the injection only when it can trace that flow, but it needs a specification of every source, sink and sanitizer in the libraries used. Missing specifications are its main source of missed bugs, the gap the LLM-assisted tools on [Comparing Static Analysis Tools](tools.md) try to close {% cite li2024iris %}.

## 6. What to do with a warning

Every warning still needs a decision: fix it, or record why it is a false alarm. [Triaging Static Analysis Warnings](warnings.md) works through both on this file.

## When to use them

- Run style, AST and type checks on every commit; they cost seconds.
- Add security patterns (Bandit) or taint analysis (CodeQL, Semgrep) for code that handles untrusted input.
- Do not read a clean run as "no bugs": each tool checks only its own properties.

## How solid is this?

- **Where it comes from.** The tool outputs above were produced on 28 October 2026 with the versions listed; the technique taxonomy follows Chess and West {% cite chess2007static %}.
- **What is contested.** Default rule sets change between releases, so another version may report more or fewer findings on the same file.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
