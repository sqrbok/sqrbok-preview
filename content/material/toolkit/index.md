---
title: Practice Toolkit
parent: Selected Materials
nav_order: 3
has_children: true
layout: default
---

# Practice Toolkit

The **Practice Toolkit** is a set of tool cards: one page per command-line tool that the handbook's methods rely on, read with a terminal open. A card says what the tool is for, how to run it, how to read its output, which mistakes are common, and how to run it in CI. It teaches the tool, not the method; each card links to the method pages that explain why.

| Stage | Tool card | The question it answers |
|---|---|---|
| Project and pipeline | [Poetry and uv](poetry.md) | Which dependencies, in which versions, does this project run with? |
| | [GitHub Actions](github-actions.md) | Does every push pass the same checks? |
| | [Podman and Docker](containers.md) | Does the service run the same way on every machine? |
| Testing | [pytest](pytest.md) | Do the examples I wrote still pass? |
| | [Coverage.py and pytest-cov](coverage.md) | Which lines and branches did the tests execute? |
| | [Hypothesis](hypothesis.md) | Does a rule hold for inputs I did not think of? |
| | [mutmut](mutmut.md) | Would my tests notice if the code were wrong? |
| Static and security checks | [flake8 and Ruff](flake8-ruff.md) | Does the code follow the style rules, and is any function too complex? |
| | [Radon](radon.md) | How complex and how maintainable is each function and file? |
| | [mypy](mypy.md) | Do the types fit together? |
| | [Bandit and pip-audit](bandit.md) | Does the code or one of its dependencies contain a known security weakness? |
| | [Trivy](trivy.md) | Does the container image carry known vulnerabilities or an unsafe configuration? |
| Generated inputs and tests | [Atheris](atheris.md) | Does any input make the code fail unexpectedly? |
| | [CrossHair](crosshair.md) | Can any input break this contract, or tell two versions apart? |
| | [Pynguin](pynguin.md) | Which tests follow from the code alone, and what do they miss? |
| Performance | [Locust](locust.md) | How do response time and failures behave under load? |
| | [Prometheus](prometheus.md) | What did the service itself measure while under load? |
| Combinatorial | [PICT](pict.md) | Which few tests cover every pair of input values? |
| | [ACTS](acts.md) | The same, with NIST's generator and its higher strengths |

The table follows the tools' place in a project, not a reading order: GitHub Actions needs the tools from the testing and static-check cards, and the container, Trivy and Prometheus cards need the service printed on the Locust card. Locally, Atheris and Pynguin run in containers.

Every card uses one small example project: a parking-fee calculator in Python. (Constructed example.) Each card prints the files it adds, so the project grows as you go; counts such as "files checked" change with it.

```text
parking/
├── pyproject.toml   poetry.lock   .flake8   .github/workflows/ci.yml
├── locustfile.py    machine.txt   machine_acts.txt   prometheus.yml
├── Containerfile    Containerfile.fixed   Containerfile.monitor
├── Containerfile.fuzz   Containerfile.pynguin
├── src/parking/     fee.py   receipt.py   lookup.py   export.py   app.py   monitored.py
│                    ticket.py   contracts.py   fee_v2.py
├── fuzz/            fuzz_ticket.py
└── tests/           test_fee.py   test_fee_props.py
```

The tools also create files (`mutants/`, `.hypothesis/`, `htmlcov/`, `results_*.csv`, `machine.tsv`, `machine.csv`, `parking.tar`, `fuzz/crashes/`, `generated/`); keep them out of version control.

Each card states the tool version it was run with, and its output comes from that run (the ACTS run is still to be added). Tools change, so compare versions before trusting a flag or a configuration key.

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
