---
title: Exploratory Session Report
parent: Exploratory Testing
nav_order: 4
layout: default
---

# Exploratory Session Report

**A session report is the written result of one exploratory testing session: the charter, how the time was spent, what was covered, the bugs and open issues found, and what the next session should explore.** It is the "reviewable result" of [Session-Based Test Management](session-based.md), read at the debriefing {% cite bach2000session %}.

## 1. Worked example: the session

This constructed session's charter is *explore the coupon system using the Obsessive-Compulsive tour and the Never-and-Always heuristic to discover pricing errors*; the time box is 90 minutes. Scope: valid, repeated, combined and expired coupons, with sale prices and cart changes; the shop allows one coupon per order, and the first one applied should stay. The specification's test cases each applied one coupon; none combined coupons with each other, with sales or with cart changes.

1. A valid coupon gives 10% off. The Obsessive-Compulsive tour says repeat it: applied twice, the discount doubles: **bug 1**.
2. *Never and Always* says a price is never negative, so try the fixed coupon WELCOME5 (5.00 off) on an item on sale at 4.50. The price shows −0.50: **bug 2**.
3. Apply WELCOME5 (fixed 5.00 off), then remove items: the discount stays and the subtotal drops below zero: **bug 3**.
4. In two browser tabs, apply a different coupon in each: both apply: **bug 4**.

Each test came from the previous result, from the tour or heuristic named in the charter, or from an idea the session prompted.


## 2. Notes during the session

Raw notes, with times:

> 09:50 start; set-up: test account, cart, coupon codes · 09:59 testing: SAVE10 ok, 10% off · 10:12 SAVE10 twice → 20%! bug: reproduce, file #101 · 10:18 testing · 10:30 WELCOME5 (5.00 off) on sale item at 4.50 → −0.50, bug #102 · 10:35 testing · 10:48 removed items, discount kept, bug #103 · 10:52 testing · 11:05 two tabs, both coupons applied, bug #104 · 11:08 testing · 11:20 end

The times give the split. **Set-up** is everything before testing can start: accounts, data, environment. Bug time starts when the triggering test shows the bug and runs to the next testing entry; a bug investigated mid-test counts as bug time, as in SBTM {% cite bach2000session %}:

- set-up: 09:50–09:59 = 9 min, 9 ÷ 90 = 10%;
- bug investigation and reporting: 6 + 5 + 4 + 3 = 18 min, 18 ÷ 90 = 20%;
- testing: the rest, 90 − 9 − 18 = 63 min, 63 ÷ 90 = 70%.

## 3. The report

Each field has one rule; the third column is this session's entry.

| Field | Rule | This session |
|---|---|---|
| Charter | The mission as given, all three parts | Explore the coupon system using the Obsessive-Compulsive tour and the Never-and-Always heuristic to discover pricing errors |
| Tester, date, type | By total session length, set-up and bug time included: **normal** 60 to 120 minutes, **short** under 60, **long** over 120 (this handbook's convention); a short session is reported with the same fields, debrief outcome and next charter included | A. Tester, 12 March, normal (90 min) |
| Time split | Shares of testing, bug investigation and reporting, and set-up, from the clock times; they sum to 100% | 70%, 20%, 10% |
| Areas covered | What you actually exercised, specifically enough to repeat | Valid and repeated coupons; sale items; removing items after a coupon; two tabs on one cart |
| Bugs | One line each with the bug report number; the bug report holds title, steps, expected and actual result, severity (scale below the table), environment | #101 discount doubles (major); #102 negative price on sale item (critical); #103 discount kept, subtotal below zero (critical); #104 two coupons on one order (major) |
| Issues | Open questions and blockers that are not defects; ideas outside the charter's scope (inside the scope, they go to Next charter) | May coupons combine with sale prices? Idea: gift cards |
| Next charter | What this charter included but was not reached, all three parts | Explore expired coupons using the Goldilocks heuristic on expiry dates to discover pricing errors |
| Debrief outcome | Filled in after the debriefing | #102 and #103 fixed first; next charter approved; a regression test for each of the four bugs |

Severity: **critical**, the shop charges an impossible amount (a negative price or total); **major**, a wrong amount, but the total stays at or above zero (#104: a 40.00 cart with SAVE10 and WELCOME5 costs 31.00 instead of 36.00, too low by one extra discount); **minor**, a wrong message or display; **cosmetic**, layout only.

## When to use it — and when not

Write one for every chartered session.

## Limitations

Areas covered are the tester's own account, not measured coverage.

## How solid is this?

- **Where it comes from.** Bach's session sheet {% cite bach2000session %}, adapted.
- **What we do not hold.** Bach's original sheet format.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
