---
title: Why Process Improvement?
parent: Process Improvement
nav_order: 1
layout: default
---

# Why Process Improvement?

**Software process improvement (SPI)** is a systematic change of an organization's technical, organizational and cultural structures to get better results from software development {% cite kuhrmann2016spi %}. Organizations start it when fixing problems one by one has stopped working.

## 1. Three situations that trigger SPI

The same pattern appears in three typical business situations {% cite humphrey1989managing %}:

| Situation | The quick fix | The process question |
|---|---|---|
| A large code base keeps producing bugs that customers see | fix each bug | why do bugs of this kind keep appearing? |
| A competent firm is repeatedly beaten to market | ask for overtime | where does our delivery time actually go? |
| A contractor misses milestones on fixed-price work | promise harder | why are our estimates wrong? |

In each case the quick fix treats one occurrence; SPI treats the process that keeps producing them. IBM's defect-prevention work found the same thing in its own data: the root causes of defects were mostly gaps in communication, training and tools, not careless individuals {% cite mays1990defect %}.

## 2. Example: organizational failure comes first

The loss of the Space Shuttle Columbia in 2003 is the lecture's case of an organization failing before the hardware did {% cite widnall2003columbia %}. The investigation found that foam strikes on launch had been seen on earlier flights and gradually treated as a maintenance matter rather than a danger, under strong schedule pressure. Engineers who had concerns found no welcome for them; as Sheila Widnall, a member of the investigation board, put it, "if dissent and questioning are not welcomed, you stop doing it." The physical cause was a foam strike; the reason it was not acted on was organizational.

Software teams show the same mechanism on a smaller scale: a test that fails intermittently is marked "known flaky", the marking becomes normal, and a real failure in that test is later ignored.

## 3. The credibility gap

Watts Humphrey, whose work led to the Capability Maturity Model (CMM) and its successor, Capability Maturity Model Integration (CMMI), put the problem to managers as a gap between what they say and what they do {% cite humphrey1989managing %}. Most managers say that measurement matters, that learning pays off and that planning beats heroics, yet many organizations measure little, cut training first and rely on a few people working nights before each release. Humphrey's conclusion was that professional management should not depend on heroics but on defined, measured processes, so that a success can be repeated instead of hoped for.

## When to use this argument — and when not

Use it when the same class of problem keeps returning despite individual fixes, or when results cannot be predicted from one project to the next. Do not start an SPI program to fix a one-off failure, or when the organization is not prepared to spend time on it during a busy period: the [change-management](04-change-management.md) evidence shows such programs are abandoned during the first dip in performance.

## Limitations

- **The case is easier to make than to prove.** SPI benefits appear later than its costs, and a problem that was prevented leaves no record.
- **Most published evidence is positive by selection.** Organizations rarely report failed programs {% cite kuhrmann2016spi %}.
- **Columbia is an analogy, not a software study.** It shows a mechanism; it does not measure how often software organizations suffer from it.

## How solid is this?

- **Where it comes from.** Humphrey's management argument, the Columbia investigation as presented in the lecture, and IBM's defect-cause data {% cite humphrey1989managing widnall2003columbia mays1990defect %}.
- **What is contested.** Whether SPI pays off is rarely tested rigorously: of 769 SPI publications, only 14.8% evaluate an approach {% cite kuhrmann2016spi %}.
- **What we do not hold.** Humphrey's book and the Columbia board's report are cited from the lecture; the area's review does not hold them.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
