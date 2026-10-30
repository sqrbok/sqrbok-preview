---
title: GitHub Actions
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 2
layout: default
page_type: tool-card
tested_with: actions/checkout v7, actions/setup-python v7, actionlint 1.7.12
---

# GitHub Actions

**GitHub Actions** runs a workflow, a YAML file in `.github/workflows/`, on GitHub's machines whenever an event such as a push or a pull request happens {% cite gha_docs %}. A failing step marks the commit red, so the checks a team agreed on are run on every change, not only when someone remembers.

The quality question it answers: *does every change pass the same checks, on a clean machine?*

*Run for this card with actions/checkout v7, actions/setup-python v7, actionlint 1.7.12, October 2026.*

## Quick start

Save this as `.github/workflows/ci.yml` and push. It installs the project with Poetry, runs the linter, then runs the tests with a coverage floor. The commands and the floor are explained on the [pytest](pytest.md), [coverage](coverage.md) and [flake8](flake8-ruff.md) cards; push it once the project has reached the [flake8 card](flake8-ruff.md), so that every tool it runs is installed. **Expect the first build to be red**: the receipt module still has its style warnings, so the lint step fails and the test step after it is skipped; once lint is green, the coverage floor fails next, because the receipt module has no tests. That is the workflow doing its job; the build turns green by fixing the code and adding tests, not by loosening the steps.

```yaml
name: ci
on: [push, pull_request]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7            # get the code
      - uses: actions/setup-python@v7
        with:
          python-version: "3.12"             # quoted: see the mistakes below
      - run: pip install poetry
      - run: poetry install
      - run: poetry run flake8 src tests
      - run: poetry run pytest --cov=parking --cov-branch --cov-fail-under=85
```

## Reading the output

Each `run:` step is a shell command, and its **exit code** decides the result: 0 is green, anything else is red, and the steps after it are skipped. So a tool works as a gate only if it exits non-zero on failure. On the example project, `poetry run flake8 src tests` exited 1 on the receipt module's warnings (flake8 card), and `pytest --cov-fail-under=90` exited 1 at 88% coverage (coverage card); either would turn the build red. In the run's log on GitHub, open the red step to see the tool's own output.

## Common mistakes

- **Unquoted version numbers.** YAML reads `python-version: 3.10` as the number 3.1, which is a different Python. A YAML parser gave `{'python-version': 3.1}` for the unquoted line and `'3.10'` for the quoted one; actionlint 1.7.12 did not flag it. Always quote versions.
- **Old action versions.** `actions/checkout@v2` and `setup-python@v2` still appear in tutorials; the current major versions are v7. Old versions run on deprecated runtimes and are eventually switched off.
- **A check that cannot fail.** A tool run in report-only mode, or with `|| true`, always exits 0, so the build stays green whatever it finds.
- **A floor measured on a different project.** Coverage is a share of the whole package: once the untested receipt, lookup, export and app modules were added, the same tests covered under 20%, and the floor of 85 failed. Add tests with each module, or measure only what is meant to be tested.
- **Checking locally with a different command.** If CI runs `flake8 src tests`, run exactly that before pushing.

Before pushing a workflow, `actionlint` (`brew install actionlint`, or a binary from its GitHub releases) catches syntax errors, unknown keys and wrong expression types.

## Go deeper

- [Continuous Integration](../../organization/05-practice/13-continuous-integration.md): what CI is for and what the evidence says.
- [Worked Case: Quality Gates for a Python Repository](../../verif/static/worked-case.md) and [Worked Case: Basis Paths and a Coverage Gate for a Checkout Function](../../verif/coverage/worked-case.md): gates chosen and set from real output.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
