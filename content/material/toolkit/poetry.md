---
title: Poetry and uv
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 1
layout: default
page_type: tool-card
tested_with: Poetry 2.1.2, uv 0.9.18, Python 3.12
---

# Poetry and uv

**Poetry** is a dependency manager for Python projects: it records which packages a project needs in `pyproject.toml`, pins the exact versions it resolved in `poetry.lock`, and runs your commands inside the project's own virtual environment {% cite poetry_docs %}. **uv** does the same job and is much faster; the commands map almost one to one {% cite uv_docs %}.

The quality question it answers: *does everyone, including the CI server, run the code with the same dependencies?* A test that passes on your laptop and fails in CI is often a version difference, not a code difference.

*Run for this card with Poetry 2.1.2, uv 0.9.18, Python 3.12, October 2026.*

## Install and quick start

```bash
pipx install poetry                      # pipx gives a CLI tool its own environment; or: pip install poetry
poetry new parking                       # creates src/parking/, tests/, pyproject.toml
cd parking
poetry add flask                         # a runtime dependency
poetry add --group dev pytest pytest-cov # a development-only dependency
poetry install                           # create the environment from the lock file
poetry run pytest                        # run a command inside it
```

## Reading the output

`poetry add flask` prints the version range it chose, then what it installed:

```text
Using version ^3.1.3 for flask

Updating dependencies
Resolving dependencies...

Package operations: 7 installs, 0 updates, 0 removals

  - Installing markupsafe (3.0.4)
  ...
  - Installing flask (3.1.3)

Writing lock file
```

`^3.1.3` means "3.1.3 or newer, below 4.0". Poetry writes that range into the `[project]` table of `pyproject.toml` as `"flask (>=3.1.3,<4.0.0)"`; the exact versions go into `poetry.lock`. **Commit both.** `poetry show --top-level` lists what you asked for, and `poetry show --tree flask` shows what it pulled in.

## Common mistakes

- **Running tools outside the environment.** Once a test imports the package, a plain `pytest` failed with `ModuleNotFoundError: No module named 'parking'`; `poetry run pytest` passed. The package is installed only inside Poetry's environment.
- **An open-ended Python range.** `poetry new` wrote `requires-python = ">=3.12"`. Adding mutmut then failed: one of its dependencies supports only Python below 4.0, so Poetry could not satisfy every Python the project claims. The fix is `requires-python = ">=3.12,<4.0"`.
- **Old instructions.** Since Poetry 2.0, `poetry shell` is not installed by default (`poetry env activate` prints the activation command; run it with `eval $(poetry env activate)`), and `poetry lock --no-update` fails with `The option "--no-update" does not exist`, because not updating is now the default.
- **Committing the tools' output.** `poetry new` writes no `.gitignore`. A start for the example project:

  ```text
  __pycache__/
  .venv/
  .hypothesis/
  .coverage
  htmlcov/
  mutants/
  results_*.csv
  machine.tsv
  machine.csv
  ```

- **Editing `poetry.lock` by hand, or not committing it.** Without the lock file, CI resolves the newest versions on the day it runs.

## The same steps with uv

| Poetry | uv |
|---|---|
| `poetry new parking` | `uv init --package parking` |
| `poetry add flask` | `uv add flask` |
| `poetry add --group dev pytest` | `uv add --dev pytest` |
| `poetry install` | `uv sync` |
| `poetry run pytest` | `uv run pytest` |
| `poetry.lock` | `uv.lock` |

Both write the standard `[project]` table in `pyproject.toml`. uv can also install Python itself (`uv python install 3.12`). On the test project, `uv init --package` also wrote a `.python-version` file and a `.gitignore`.

## In CI

```yaml
- run: pip install poetry
- run: poetry install
- run: poetry run pytest
```

## Go deeper

- [Continuous Integration](../../organization/05-practice/13-continuous-integration.md): why the build must be reproducible.
- [DevSecOps and Supply Chain Security](../../attributes/security/devsecops.md): lock files and dependency risk.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
