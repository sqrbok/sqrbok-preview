---
title: Design Diversity and Voting
parent: Reliability
grand_parent: Quality Attributes
nav_order: 6
layout: default
vegalite: true
page_type: deep-dive   # carries the voting arithmetic and the Knight-Leveson data; both must be reproducible on the page
---

# Design Diversity and Voting

**Design diversity** builds the same function several times, by separate teams or with different algorithms, so that the versions are unlikely to fail on the same input; a **voter** then selects the answer the versions agree on {% cite avizienis2004basic pullum2001fault %}.

## 1. Voting schemes

A voter compares the outputs of N versions. Three schemes are common {% cite pullum2001fault %}:

- **Majority voting** accepts an output when at least m = ⌈(N + 1)/2⌉ versions agree (this m is unrelated to the m(t) of growth models): 2 of 3, 3 of 5, 3 of 4.
- **Consensus voting** accepts the majority if there is one; otherwise it accepts the largest group of agreeing versions if that group is unique, and picks at random among tied groups.
- **2-of-N voting** accepts any output that two versions agree on. It suits large output spaces, where two independent wrong answers rarely coincide.

For numeric outputs that differ slightly, a **median** voter takes the middle value.

## 2. Reliability of a majority system

Assume a perfect voter, versions that fail **independently**, each working with probability R<sub>c</sub>, and wrong outputs that never agree with each other. Then a majority system works when at least m of the N versions work:

R<sub>system</sub> = Σ<sub>i=m..N</sub> C(N, i) R<sub>c</sub><sup>i</sup> (1 − R<sub>c</sub>)<sup>N−i</sup>

C(N, i), the binomial coefficient, counts the ways to choose which i of the N versions work; the formula is given here for majority voting only.

**Example: majority 2 of 3, R<sub>c</sub> = 0.95.** R = 3 × 0.95<sup>2</sup> × 0.05 + 0.95<sup>3</sup> = 0.1354 + 0.8574 = 0.9928 ≈ 0.993. The failure probability drops from 5% for one version to 0.7%.

**3 of 5, R<sub>c</sub> = 0.9.** R = 10 × 0.9<sup>3</sup> × 0.1<sup>2</sup> + 5 × 0.9<sup>4</sup> × 0.1 + 0.9<sup>5</sup> = 0.073 + 0.328 + 0.590 = 0.991.

**The same majority 2-of-3 system with a common cause.** Suppose some failures are shared: with probability 0.01 an input makes all three versions fail together; on all other inputs each version fails on its own with probability 0.04. Each version still fails about 5% of the time (0.01 + 0.99 × 0.04 ≈ 0.05), as before. The system fails on a shared failure, or when at least two versions fail independently:

P(fail) = 0.01 + 0.99 × (3 × 0.04<sup>2</sup> × 0.96 + 0.04<sup>3</sup>) = 0.01 + 0.0046 = 0.0146

R falls from 0.993 to 0.985: the shared failures alone make up two thirds of the system's failures, and no voter can outvote them. This constructed example shows why the independence assumption decides the result.

## 3. The Knight and Leveson experiment

Knight and Leveson tested the independence assumption directly {% cite knight1986experimental %}. Students at two universities wrote 27 versions of the same program, a launch interceptor from an anti-missile specification, in Pascal. Each version ran one million random test cases against a reference ("gold") program.

The versions were individually very good: six never failed, and 23 of the 27 succeeded on more than 99.9% of the tests. But in **1,255 tests two or more versions failed on the same input**, in two tests as many as eight. Computed from each version's own failure rate, independence predicts about 127 such tests: roughly ten times fewer. You do not need the statistics: the authors' z = 100.51 just says the gap is far too large to be chance (2.33 would already have sufficed at the 99% level), so independence was rejected.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 420, "height": 200,
  "data": {"values": [
    {"k": "2", "n": 551}, {"k": "3", "n": 343}, {"k": "4", "n": 242}, {"k": "5", "n": 73},
    {"k": "6", "n": 32}, {"k": "7", "n": 12}, {"k": "8", "n": 2}]},
  "layer": [
    {"mark": {"type": "bar", "color": "#b42318"},
     "encoding": {"x": {"field": "k", "type": "ordinal", "title": "Versions failing on the same input", "axis": {"labelAngle": 0}},
                  "y": {"field": "n", "type": "quantitative", "title": "Test cases", "scale": {"domain": [0, 600]}}}},
    {"mark": {"type": "text", "dy": -8, "fontSize": 12},
     "encoding": {"x": {"field": "k", "type": "ordinal"}, "y": {"field": "n", "type": "quantitative"}, "text": {"field": "n"}}}
  ],
  "config": {"font": "Tahoma, sans-serif", "axis": {"labelFontSize": 12, "titleFontSize": 13}, "view": {"stroke": null}}
}
```

_Test cases in which several of the 27 versions failed together: 1,255 in total, against about 127 expected under independence._ {% cite knight1986experimental %}

**Why they fail together.** Brilliant, Knight and Leveson analysed the faults and found 93 pairs of faults whose failures were statistically correlated {% cite brilliant1990faults %}. Some were the same logical slip made by different people: several programmers forgot that three collinear points can form an angle of 0 as well as π. Others came from limited-precision arithmetic near zero. Even different algorithms failed on the same hard inputs.

## 4. Models of shared difficulty

Littlewood, Popov and Strigini review the theory {% cite littlewood2001diversity %}. In the Eckhardt–Lee model, some inputs are hard for every team, so failures are positively correlated even when teams work independently. The Littlewood–Miller model lets difficulty differ between methods: what is hard for one approach may be easy for another, so forcing different methods can help. Their conclusion: diversity improves reliability over a single version, but by less than independence predicts, and by an amount that is hard to prove.

## When to use it — and when not

Use diversity where a failure is catastrophic and the cost of several versions is justified: the Airbus A320 flight controls use two types of computer with independently developed software packages {% cite dubrova2013fault %}. Make the versions differ in method, not only in team. Do not use the independent-failure formula to claim a reliability figure for such a system; Knight and Leveson's data show it is optimistic.

## Limitations

All versions share one specification, so a fault in it reaches every version; identical copies share every fault, as the two Ariane 5 units did ([Dependability and the Fault Chain](dependability.md)). Measuring the benefit of diversity needs failure data that a highly reliable system rarely produces.

## How solid is this?

- **Where it comes from.** The voting schemes follow Pullum {% cite pullum2001fault %}; the experiment and its numbers are from Knight and Leveson's paper {% cite knight1986experimental %}, and the expected count of about 127 was recomputed from the per-version failure counts in its Table 1.
- **What is contested.** How much diversity helps. The students were not professional teams, but later analyses kept the conclusion that independence cannot be assumed {% cite littlewood2001diversity %}.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
