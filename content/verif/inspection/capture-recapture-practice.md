---
title: Capture-Recapture in Practice
parent: Inspection
nav_order: 6
layout: default
math: katex
---

# Capture-Recapture in Practice

**Capture-recapture in practice** is the procedure that turns an inspection's logs into the inputs of a [capture-recapture estimate](capture-recapture.md), and the estimate into an exit decision. The sources assume this procedure without describing it {% cite briand2000capture %}, so the rules below are the course's practice; they are kept as few as possible.

## 1. Five steps

1. **If you use PBR, assign perspectives** ([perspective-based reading](reading-techniques.md)), with at least four inspectors.
2. **Prepare independently.** Each inspector, including both members of a doubled perspective, writes an own list; no notes are shared before the lists are handed in.
3. **Match duplicates.** One defect is one needed fix in the document: reports that need the same fix are one defect, whoever found them and from whichever perspective. The moderator matches them and gives each defect one identifier. Two reports of one defect counted as two would lower the overlap and inflate the estimate.
4. **Confirm major defects.** The meeting confirms which issues are major defects (would probably cause a failure or a wrong implementation if left in) and drops the minor ones. Each confirmed defect is credited to every inspector whose list held it.
5. **Compute.** Count *S* and *f*<sub>1</sub> from the preparation lists and apply the jackknife. A defect first raised in the meeting is a found defect but not an input, because nobody found it independently. ([Defect seeding](defect-seeding.md), by contrast, counts meeting finds, because its ratio uses all finds.) So, with *M* meeting-only defects:

$$\text{remaining} = \max(0,\ \hat{N} - S - M)$$

## 2. The decision

**Example.** In the [worked example](capture-recapture.md), four inspectors found *S* = 16 major defects in a 20-page document, none of them first raised in the meeting (*M* = 0), and the jackknife gives $$\hat{N}$$ = 20.5. Remaining: 20.5 − 16 − 0 = 4.5, or 4.5 / 20 = 0.225 per page. Malotaux's exit criterion allows at most 0.25 major defects per page {% cite malotaux2008onepage %}.

So the document **passes, narrowly**: at most 0.25 remaining major defects per page is the only pass line. The estimate is uncertain and tends to be low (see Limitations), so record a result this close to the limit as a narrow pass and watch the defects found later. A page is a printed page; the held sources do not define it further. With one more single-inspector defect, *S* = 17, *f*<sub>1</sub> = 7 and $$\hat{N}$$ = 22.25: 5.25 remain, 0.26 per page, a fail.

## 3. With perspective-based reading

Under PBR, detection probability varies both by inspector (each perspective has its own focus) and by defect (some are harder): model M<sub>th</sub>. The course still uses M<sub>h</sub> with the jackknife, which Briand found best, with modelling inspector differences adding little {% cite briand2000capture %}. Two effects now pull in opposite directions. Inspectors who look for *different* defects, as PBR intends, overlap less, which pushes the estimate up; defects that differ in difficulty push it down. The net bias is uncertain; this is the course's reading of the theory, as the held sources report no result for PBR.

With three perspectives and four inspectors, **double one**: two people read from the same perspective, each with an own list, and both count as separate inspectors. Their shared focus restores some overlap, which keeps the estimate from breaking down when the perspectives barely overlap. The course's choice is to double the perspective that matters most for the document; for requirements, the tester.

## When to use it — and when not

Use these steps whenever a capture-recapture estimate will decide whether a document passes. Do not use them for a review where inspectors worked together on one list: there is no overlap to measure.

## Limitations

- **The rules are the course's, not the sources'.** Another team may match duplicates or classify majors differently, and the estimate moves with those choices.
- **Underestimation.** All capture-recapture models tend to estimate too few defects {% cite briand2000capture %}; with PBR the direction is uncertain (§3).

## How solid is this?

- **Where it comes from.** The model choice and the four-inspector minimum from Briand and colleagues; the exit criterion from Malotaux {% cite briand2000capture malotaux2008onepage %}.
- **What we do not hold.** A source that describes how to match duplicates, or how to combine PBR with capture-recapture.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
