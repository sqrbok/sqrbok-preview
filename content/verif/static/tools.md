---
title: Comparing Static Analysis Tools
parent: Static Analysis
nav_order: 8
layout: default
vegalite: true
---

# Comparing Static Analysis Tools

Static analysis tools differ in the technique they use, the languages they read and the faults they look for, so two tools run on the same code report largely different warnings. The largest comparison found fewer than 0.4% of warnings shared between tools, and precision from 18% to 86% {% cite lenarduzzi2021comparison %}.

## 1. The families

| Family | Python examples | Java and multi-language examples | Typical findings |
|---|---|---|---|
| Linters and style checkers | pylint, flake8 | Checkstyle | unused code, naming, layout |
| Type checkers | mypy | the Java compiler, TypeScript | type errors, unchecked `None` or `null` |
| Bug-pattern finders | pylint's warning rules | SpotBugs (FindBugs), PMD | known faulty idioms |
| Security analyzers | Bandit | CodeQL, Semgrep | injection, unsafe calls, weak cryptography |
| Quality platforms | (multi-language) | SonarQube | all of the above, plus maintainability ratings |

One tool can span several families: pylint checks both style and bug patterns, and SonarQube covers nearly all of them. The [Pattern, Type and Taint Checks](techniques.md) page runs four of the Python tools on one file: each found one of its three planted faults and missed the other two.

## 2. Example: six tools on 47 projects

Lenarduzzi and colleagues ran SonarQube, Better Code Hub, Coverity Scan, FindBugs, PMD and Checkstyle on 47 Java projects from the Qualitas Corpus. Together the tools raised 13,554,762 warnings of 936 types {% cite lenarduzzi2021comparison %}. To measure **precision**, the share of warnings that point to a real problem, the authors manually checked a sample of 367 to 384 warnings per tool:

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 420, "height": 180,
  "data": {"values": [
    {"t": "Checkstyle", "p": 86}, {"t": "FindBugs", "p": 57}, {"t": "PMD", "p": 52},
    {"t": "Coverity Scan", "p": 37}, {"t": "Better Code Hub", "p": 29}, {"t": "SonarQube", "p": 18}
  ]},
  "encoding": {
    "y": {"field": "t", "type": "nominal", "title": null, "sort": "-x", "axis": {"labelFontSize": 13}},
    "x": {"field": "p", "type": "quantitative", "title": "Precision (% of sampled warnings that were real)", "scale": {"domain": [0, 100]}}
  },
  "layer": [
    {"mark": {"type": "bar", "color": "#019546"}},
    {"mark": {"type": "text", "align": "left", "dx": 4, "fontSize": 12}, "encoding": {"text": {"field": "p", "type": "quantitative"}}}
  ],
  "config": {"font": "Tahoma", "axis": {"titleFontSize": 13, "labelFontSize": 12}, "view": {"stroke": null}}
}
```

_Precision of six Java tools on manually validated samples of their warnings._ {% cite lenarduzzi2021comparison %}

Checkstyle's rules are simple style checks, which explains much of its 86%; SonarQube, with the broadest rule set, had the lowest precision. At rule level SonarQube covered most of the issue types the other tools detect, but on actual lines the tools rarely agreed: overall overlap was below 0.4%, the closest pair (FindBugs and PMD) shared 9.4% of their warnings, Checkstyle and PMD 0.1%, and no line was flagged by more than two tools {% cite lenarduzzi2021comparison %}.

The practical reading: "our SonarQube report is clean" says nothing about what FindBugs or a security analyzer would report. Choose tools by the faults you need found, and expect to tune each one ([Deploying Static Analysis](deployment.md)).

## 3. LLMs as a helper

Taint analyzers miss vulnerabilities when they lack specifications for library calls: which method returns user input, which one runs a command. **IRIS** asks a large language model to infer those specifications and to filter the resulting alarms, then lets CodeQL do the actual data-flow analysis {% cite li2024iris %}. On CWE-Bench-Java, 120 confirmed vulnerabilities in real Java projects, with the **false discovery rate** being the share of alarms that are false (1 − precision):

| Approach | Vulnerabilities detected | Average false discovery rate |
|---|---|---|
| CodeQL alone | 27 | 90.03% |
| IRIS with GPT-4 | 55 (+28, or +103.7%) | 84.82% |

IRIS also found four previously unknown vulnerabilities in 30 projects. Even so, more than eight of ten alarms were false by the benchmark's measure; the authors' manual check of 50 alarms suggests about 46% in practice {% cite li2024iris %}. A survey of LLM-assisted program analysis lists the limits: non-deterministic answers, hallucinated findings, token limits on large code, and dependence on prompt engineering {% cite wang2025llmpa %}. The pattern that works is LLM plus a conventional analyzer, not an LLM alone.

**Where the inputs come from.** To compare tools on your own code, run each on the same commit, export the warnings (most tools write SARIF or JSON), and have two developers judge a random sample of each tool's warnings as real or false; that gives each tool's precision on that code base, the measure used in the study above.

## When to compare tools — and how

- Run two or three candidate tools on your own code and sample their warnings; published precision is for other code bases and other rule sets.
- Do not merge tools to "cover everything" without tuning: every untuned tool adds false alarms.

## How solid is this?

- **Where it comes from.** Lenarduzzi and colleagues' comparison of six tools {% cite lenarduzzi2021comparison %}; the IRIS evaluation {% cite li2024iris %}; Wang and colleagues' survey {% cite wang2025llmpa %}.
- **What is contested.** Precision was judged for technical-debt warnings on Java code, with each tool's default rules; another language, rule set or definition of "real problem" changes the numbers.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
