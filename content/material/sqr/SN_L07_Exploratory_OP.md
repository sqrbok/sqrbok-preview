---
title: "Study Notes: Exploratory Testing & Operational Profile"
parent: SQR Study Materials
grand_parent: Selected Materials
nav_order: 13
redirect_from: /content/verif/exploratory/SN_L07_Exploratory_OP.html
layout: default
page_type: study-notes
---

# Study Notes: Exploratory Testing & Operational Profile

The lecture asks: *how do you test well when nobody has written the test cases?* and *which tests matter most, given how people actually use the system?* Exploratory testing (ET) answers the first, the operational profile (OP) the second. The difference: in ET the tester's judgement during the session decides what to test next; in OP-based testing, measured or estimated usage frequencies decide it before testing starts. [Revision questions](RQ_L07_Exploratory_OP.md) test it.

## 1. Scripted and exploratory testing find different defects

Scripted testing designs tests before running them: repeatable, traceable, suited to regression. Exploratory testing designs each test while running the previous one: suited to new features and thin specifications. Neither replaces the other: Shah et al. compared exploratory, scripted and hybrid testing (test cases plus open missions), and exploration found the most defects but covered the fewest specified functions {% cite shah2014hybrid %}. Both hold because an explorer goes deep where trouble appears and skips some functions, while a script visits every function but probes each lightly.

→ [Exploratory Testing](../../verif/exploratory/index.md)

## 2. Exploratory testing is learning, design and execution at once

Exploratory testing is "simultaneous learning, test design and test execution" {% cite itkonen2005exploratory %}. The tester loops: learn how the system behaves, design the next test, execute it, steer by the result {% cite hendrickson2013explore %}. Ad hoc testing has no mission, no time box and no report. An exploratory session has at least a mission (even if only the product to test, §7), a time box and a report; session-based test management (SBTM, §5) adds a debriefing.

→ [Exploratory Testing](../../verif/exploratory/index.md)

## 3. A session follows the system's answers, not a list

The lecture's constructed coupon session starts from a **charter**, a one-line mission (§4), and ends with a **session report** reviewed at a short **debriefing** (§5). The charter: *explore the coupon system using the Obsessive-Compulsive tour (repeat the same action) and the Never-and-Always heuristic (a price is never negative) to discover pricing errors*; its scope is valid, repeated, combined and expired coupons with sale prices and cart changes. The shop allows one coupon per order; the specification's tests each applied one coupon and none combined them. Its starting points (a tour, a heuristic) put it at level 3 (§7). Terms are defined in §4–6. Coupons: SAVE10 is 10% off, WELCOME5 is 5.00 off; when two are entered, the first one applied should stay. Applying a coupon twice doubles the discount; a coupon on a sale item drives the price negative; removing items keeps the discount (#103: a kept discount larger than the remaining subtotal); two tabs accept two coupons. The report, field by field (rule in brackets):

- **Charter**: as above.
- **Duration** (normal 60–120 min, short under 60, long over 120): normal, 90 min.
- **Time split** (testing; bug investigation, from noting a bug to resuming tests; set-up, everything before testing can start): set-up 09:50–09:59 = 9 min; bugs at 10:12–10:18, 10:30–10:35, 10:48–10:52, 11:05–11:08 = 18 min; testing the other 63 of 90 min. So 70%, 20%, 10%.
- **Areas covered**: repeated coupons, sale items, removed items, two tabs (combined coupons).
- **Bugs** (defects, one line each, with severity): #101 discount doubles (major); #102 negative price (critical); #103 subtotal below zero (critical); #104 two coupons on one order (major). Critical: an impossible amount (negative price or total); major: a wrong amount with the total still ≥ 0 (#104: a 40.00 cart with SAVE10 then WELCOME5 costs 40 − 4 − 5 = 31.00 instead of 40 − 4 = 36.00); minor: a wrong message; cosmetic: layout.
- **Issues** (questions and blockers, not defects): may coupons combine with sale prices?
- **Next charter** (in scope, not reached): explore expired coupons using the Goldilocks heuristic on expiry dates to discover pricing errors.
- **Debrief outcome** (filled in after the debriefing): #102 and #103 fixed first; a regression test for each of the four bugs.

→ [Exploratory Session Report](../../verif/exploratory/session-report.md)

## 4. Charters and heuristics give exploration a direction

Hendrickson's **charter**: *explore* target *using* resources *to discover* information, where resources are tools, data or a named heuristic or tour {% cite hendrickson2013explore %}. A tour sets the strategy for a whole session (§6); a heuristic suggests the next single input. Her heuristics include *Never and Always*: test what must never or always hold (a price is never negative). *Some, None, All*: no permissions, some, all. *Goldilocks*: a file too big, too small, just right. *Beginning, Middle, End*: insert at each position of a list. *CRUD*: create, read, update, delete each entity, in odd orders too. *Follow the Data*: check every screen showing a customer's name. They are the test design techniques of earlier lectures, applied during execution {% cite itkonen2009manual %}.

→ [Charters, Heuristics and Tours](../../verif/exploratory/techniques.md)

## 5. Time-boxed sessions make exploration auditable

Session-based test management (SBTM) was created to make exploration accountable: a manager must be able to see what was tested. It has four parts: a **charter**, a **time box** of about 90 minutes, a **reviewable result** (the session report of §3) and a **debriefing** that chooses the next charter {% cite bach2000session %}. Counts of sessions and bugs make it plannable.

→ [Session-Based Test Management](../../verif/exploratory/session-based.md)

## 6. Tours name a strategy for a session

Whittaker groups tours into six city districts. Business: core features users pay for (Money tour). Historical: legacy code, past bugs (Bad-Neighborhood). Entertainment: supporting, long-running features (All-Nighter). Tourist: quick visits, outputs (Collector's). Hotel: idle and cancelled work (Couch Potato). Seedy: hostile, destructive use (Saboteur; Obsessive-Compulsive, which repeats the same action) {% cite whittaker2009exploratory %}. 

→ [Exploratory Testing Tours](../../verif/exploratory/tours.md)

## 7. Exploration is a level you choose

Between freestyle (the charter names only the test object) and fully scripted (steps and test data) lie three levels: high exploration (a charter with goals), medium (plus starting points and known risks) and low (plus the activities to perform) {% cite ghazi2017levels %}. Typical uses: freestyle to learn a new product; high to find defects fast (see §16); medium to check known risks; low when traceability matters; fully scripted for conformance, reproduction or new testers. At Ericsson, practitioners recommended spreading test time over all five levels, with 34% of total test time at freestyle (16%) and high exploration (18%), instead of the 20% of total test time they currently got together {% cite ghazi2017decision %}.

→ [Session-Based Test Management](../../verif/exploratory/session-based.md)

## 8. ET matches scripted detection at a fraction of the effort

In Itkonen's experiment, 79 students found on average 7.04 defects exploring and 6.37 with their own test cases, not significantly different; but the test cases took 7 hours to design on top of the 1.5-hour session, 8.5 hours in all {% cite itkonen2007defect %}. A replication: again no significant difference; ET 4.58 hours per person, test-case-based 19.47 including design {% cite itkonen2014testcases %}. The lecture's "4–6 times less effort" is the ratio: 8.5 ÷ 1.5 ≈ 5.7 and 19.47 ÷ 4.58 ≈ 4.3. When both groups had the same 90 minutes including design, exploration found 8.34 defects per person against 1.83 {% cite afzal2015experiment %}.

→ [Exploratory Testing: Evidence](../../verif/exploratory/effectiveness.md)

## 9. Best for discovery, worst for repeatability

Explore new or unspecified features and usability, alongside scripted regression tests; not when a regulator needs a predefined record, exact repetition or coverage figures. Bugs found become scripted tests.

→ [Exploratory Testing: Evidence](../../verif/exploratory/effectiveness.md)

## 10. An operational profile lists operations with their probabilities

A **profile** is a set of mutually exclusive alternatives with probabilities; an **operational profile** lists a system's operations: "a quantitative characterization of how a system will be used" {% cite musa1993operational %}. For example, if a shop's logs, where each execution of an operation counts as one run, show browse 6,000, search 2,400, add to cart 1,200, check out 300 and admin report 100 operations per hour, the probabilities are 0.60, 0.24, 0.12, 0.03 and 0.01. They must sum to 1: above 1, operations overlap or are counted twice; below 1, one is missing.

→ [Operational Profile](../../verif/operational-profile/index.md)

## 11. Functions, operations and runs are different units

A **function** is a task seen from the requirements; an **operation** is a task as the design implements it; a **run** is one execution of an operation, such as removing line #4711 on 3 March. Musa's example: the function "relocate a telephone" may become two operations, removal and installation {% cite musa1993operational %}. An **operational mode** is a distinct pattern of use with its own profile: peak against night, novice against expert, normal against degraded.

→ [Developing an Operational Profile](../../verif/operational-profile/development.md)

## 12. Six steps lead from modes to probabilities

The steps: determine operational modes; identify initiators (users, external systems, the system itself); choose a table or a tree; list the operations; determine occurrence rates; divide each rate by the total {% cite musa2004sre %}. A tree suits operations combining attributes; its branch probabilities may differ under each parent (in Musa's example they do not). Musa's billing example classifies accounts by service (residential 0.80, business 0.20), calling plan (none 0.75, national 0.20, international 0.05) and payment (paid 0.99, delinquent 0.01), giving 2 × 3 × 2 = 12 operations. In a tree each probability is the product along its branch: residential, no plan, paid is 0.80 × 0.75 × 0.99 = 0.594; the rarest, business, international, delinquent, is 0.20 × 0.05 × 0.01 = 0.0001 {% cite musa1993operational %}.

→ [Developing an Operational Profile](../../verif/operational-profile/development.md)

## 13. Industrial projects reported large savings

At AT&T, the profile combined with other techniques cut customer-reported problems tenfold and halved system-test time; at Hewlett-Packard, profile-driven system test cut test time and cost by at least half {% cite musa1993operational %}. These are project reports, not controlled studies; AT&T's figure is not the profile's alone.

→ [Operational Profile: Industrial Evidence](../../verif/operational-profile/case-studies.md)

## 14. Not all code matters equally: weight coverage by use

In the lecture's example, four methods have 20 branches, 9 covered: plain coverage is 45%. **Usage-weighted coverage** weights by usage share (login 3/4 covered, 0.50; search 2/6, 0.30; checkout 4/4, 0.15; admin 0/6, 0.05): 3/4 × 0.50 + 2/6 × 0.30 + 4/4 × 0.15 + 0/6 × 0.05 = 62.5%. **Operational coverage**, the published version {% cite miranda2018coverage %}, cuts the ranked list of elements users run into three equal parts, weighted 1, 3, 9 (low, medium, high); here the elements are the methods, ranked by usage share, and each band's coverage is its covered ÷ total branches. Four methods do not divide by three, so the split is a judgement; two ways, with the branches of a two-method band pooled by count:

| Split | High (weight 9) | Medium (3) | Low (1) | Calculation | Result |
|---|---|---|---|---|---|
| A | login 3/4 | search 2/6 | checkout + admin 4/10 | (9 × 0.75 + 3 × 0.333 + 1 × 0.40) ÷ 13 | 62.7% |
| B | login + search 5/10 | checkout 4/4 | admin 0/6 | (9 × 0.5 + 3 × 1 + 1 × 0) ÷ 13 | 57.7% |

Either is accepted with the split stated. It is higher than plain coverage when the most used code is better covered than average, lower when worse; uncovered code that nobody uses has weight 0, so it does not pull the weighted figure down. It also serves as a stopping rule.

→ [Operational Coverage](../../verif/operational-profile/modern.md)

## 15. Usage profiles can come free from telemetry

Google Play records which methods real users execute in each Android app as a **Cloud Profile**: a list of methods without probabilities, unlike Musa's profile. 99.89% of the top 1,000 apps had one, listing on average 7.88% of an app's methods {% cite bleier2025profile %}. They differed from the lists developers declared: you cannot assume how users will use your software. **Profile coverage**, the unweighted share of these methods a test executes, showed a simple random generator covering more than smarter tools; no tool exceeded 70% on any app.

→ [Operational Profile: Industrial Evidence](../../verif/operational-profile/case-studies.md)

## 16. When the profile saturates, explore

Profile-based testing saturates: once the frequent paths are clean, more tests barely improve reliability {% cite bertolino2017adaptive %}. When usage data exists or is cheap (logs, telemetry), it comes first, because if testing must stop early, the most used operations have had the most testing {% cite musa1993operational %}. Without it a profile costs effort (about a staff-month, Musa estimated); under time pressure, start with high-exploration charters on the features users are expected to use most, and build the profile as data arrives. The **two-phase strategy** tests by profile first, then explores high-usage features and what a profile cannot see (rare critical operations, interrupts, usability); finds become regression tests. When to switch is a judgement; one signal is batches finding no new failures.

→ [From Profile to Exploration](../../verif/operational-profile/bridge.md)

## Where this goes next

[Software Reliability](SN_L08_Reliability.md) builds on the profile: test failure rates predict the field only when tests follow it. For the class session, the instructor sets the exploration level and the time box; a short session is fine, reported with the same fields.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
