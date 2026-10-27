---
title: Measurement Pitfalls
parent: Measurement
nav_order: 2
layout: default
---

# Measurement Pitfalls

A **measurement pitfall** is a way in which a correctly computed number leads to a wrong conclusion or to wrong behaviour. The pitfalls fall into three groups: **bad statistics** (the number is misread), **bad decisions** (the number answers a different question from the one asked) and **bad incentives** (people change what they do to move the number).

## 1. Bad statistics: relative numbers without a baseline

In October 1995 British regulators warned that third-generation contraceptive pills *doubled* the risk of thrombosis, a 100% increase. The underlying figures were about 1 in 7,000 women on the older pills and 2 in 7,000 on the newer ones: a relative increase of 100%, an absolute increase of 1 in 7,000, or about 0.014 percentage points {% cite gigerenzer2014risk %}. Many women stopped taking the pill, and the scare was later linked to a rise in abortions.

**In software.** "Production incidents doubled this quarter" may mean 1 → 2. "Defect density fell by 40%" says nothing until you know from what. Always ask for the absolute numbers behind a percentage, and for the size of the base.

## 2. Bad decisions: the McNamara fallacy and the streetlight effect

The **McNamara fallacy**, named after the US Secretary of Defense who ran the Vietnam war on body counts, has four steps: measure what is easy to measure; disregard what is not; assume that what is not measured is unimportant; and finally assume that it does not exist. Systems thinker Jay Forrester made the same point about models: leaving a hard-to-quantify variable out is the same as estimating its effect at exactly zero, which is almost certainly wrong {% cite forrester1961industrial %}.

**In software.** A manager ranks developers by commits per week, lines added and tickets closed. The developer who mentors new hires and removes technical debt scores low on all three, because none of the three counts that work.

The **streetlight effect** is the selection step of the same mistake: a man looks for his lost keys under the streetlight, not where he dropped them, because that is where the light is. Teams track the metrics their tools report for free, such as line coverage or number of commits, and not the ones their question needs. The antidote is to start from the goal and only then look for measures: see [Developing Metrics with GQM](development.md).

## 3. Bad incentives: when the measure becomes a target

People respond to what is measured. Goodhart's law, in its usual phrasing, says that when a measure becomes a target it ceases to be a good measure. Tickets counted as "closed" get closed and reopened.

**Example.** A company requires 80% line coverage for every release. Six months later the report says 88%, but production defects have not fallen, and developers say they write tests "to hit the number". Each group of pitfalls shows up once:

- **Bad statistics.** The 88% is one line-weighted average over very different modules: the UI has 6,000 lines at 95% covered, the API 3,000 at 90%, and the payment module 1,000 at 40%, so (5,700 + 2,700 + 400) / 10,000 = 88%. The average hides that the riskiest module is the least tested.
- **Bad decision.** Coverage stood in for test quality. The gate measures which lines ran, not whether any test checked the result, so it cannot answer "are our tests good enough?".
- **Bad incentive.** A test that runs code without asserting anything raises coverage as much as one that checks results, and that is the cheaper test to write when the number is the target.

[Using Coverage in Practice](../../verif/coverage/practice.md) shows how to use coverage as a clue instead.

## When to use it — and when not

- **Use** the three groups as a checklist before a metric goes on a dashboard: how could it be misread, what question does it really answer, and what will people do to move it?
- **Use** absolute numbers next to every percentage change.
- **Do not** attach individual rewards or penalties to a single metric; that is where gaming starts.
- **Do not** drop an important attribute because it is hard to measure: measure it roughly, or record it as a judgement.

## How solid is this?

- **Where it comes from.** The pill figures are those used by Gigerenzer to teach risk literacy; later studies put the relative risk between about 1.7 and 2.3, and some disputed any increase. The three-way grouping follows the course lecture, which draws on Hoffman's talk *The Darker Side of Metrics* {% cite hoffman_darker_2000 %}.
- **What we do not hold.** We have not been able to check Hoffman's text or the exact wording of the Forrester passage, so both are paraphrased here, not quoted. The four steps of the McNamara fallacy are usually credited to the sociologist Daniel Yankelovich (1972).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
