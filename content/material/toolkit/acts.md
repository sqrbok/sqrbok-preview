---
title: ACTS
parent: Practice Toolkit
grand_parent: Selected Materials
nav_order: 19
layout: default
page_type: tool-card
tested_with: ACTS Basic 1.0 (jar of 2023-10), Java 21
---

# ACTS

**ACTS** (Automated Combinatorial Testing for Software) is NIST's free generator of combinatorial tests: from a model of parameters and values it builds a *t*-way test set, in which every combination of values of any *t* parameters appears in at least one test, for *t* from 1 to 6 {% cite nist_acts_guide lei2007ipog %}. It has a graphical interface, a command line and a Java API.

The quality question it answers: *which few tests cover every interaction among two, three or more inputs?* The method is on [Combinatorial Testing](../../verif/combinatorial/index.md).

*Model and commands from the ACTS user guide; the run for this card is pending.*

## Two versions

| | Basic | Advanced |
|---|---|---|
| How to get it | direct download from NIST's Downloadable Tools page; no licence | by e-mail request to NIST |
| *t*-way generation, IPOG algorithm | yes | yes |
| Constraints between parameters | no | yes |
| Mixed strength (relations), base choice, other algorithms | no | yes |

Both industrial studies on [Combinatorial Testing in Industry](../../verif/combinatorial/effectiveness.md) used ACTS; Daoud et al. used version 3.2, an Advanced release, for its mixed-strength arrays.

## Install and quick start

ACTS needs a current Java runtime; there is nothing to install beyond the jar.

```bash
java -version
java -jar acts.jar                                  # opens the graphical interface
java -Ddoi=2 -Doutput=csv -jar acts.jar machine_acts.txt machine.csv
```

The ticket-machine model from the [PICT card](pict.md), in ACTS's text format, `machine_acts.txt` (constructed example). Two differences: `Member` is a boolean (`true`, `false`), and the cash-and-email constraint is left out because the Basic version cannot read it, so all 78 pairs are required and cash-with-email rows may appear:

```text
[System]
Name: TicketMachine

[Parameter]
Payment (enum) : cash, card, app
Member (boolean) : true, false
Stay (enum) : short, hours, overnight
Language (enum) : en, de, fr
Receipt (enum) : paper, email, none
```

Options are Java system properties, written before `-jar` {% cite nist_acts_guide %}:

| Option | Values | Meaning |
|---|---|---|
| `-Ddoi=` | 1–6 (default 2) | the strength *t* |
| `-Doutput=` | `nist` (default), `csv`, `numeric`, `excel` | output format |
| `-Dcheck=on` | | verify coverage after generating |
| `-Dalgo=null` | | check the coverage of an existing test set without generating |
| `-Dmode=extend` | | extend the tests listed in the model's `[Test Set]` section |

## Reading the output

{: .note }
The output of a run made for this card is still to be added.

With `-Doutput=csv`, each row is one test and each column a parameter, as in PICT's table. With `-Dcheck=on`, ACTS also reports whether every *t*-way combination is covered.

## Common mistakes

- **Writing constraints for the Basic version.** The `[Constraint]` section of the model is read only by the Advanced version. With Basic, either request Advanced, or generate the table in PICT, which has constraints.
- **Options after the jar name.** `-D` options belong between `java` and `-jar`. Anything written after `acts.jar` is passed to ACTS as an argument, not set as an option.
- **Spaces in parameter names.** Names follow Java variable rules: `Receipt_Type`, not `Receipt Type`.
- **Running out of memory on large models.** Raise the Java heap: `java -Xmx2g … -jar acts.jar …`.

## In CI

Keep the model file in version control and generate the table in the build, as with PICT; Java must be on the runner (GitHub's Ubuntu images include it).

## Go deeper

- [Covering Array Generation](../../verif/combinatorial/algorithms.md): how IPOG builds the array.
- [Combinatorial Testing Tools](../../verif/combinatorial/tools.md): ACTS, PICT and CASA compared, and what seed tests are worth.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
