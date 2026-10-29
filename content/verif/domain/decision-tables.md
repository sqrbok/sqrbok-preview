---
title: Decision Tables
parent: Input Domain Testing
nav_order: 4
layout: default
---

# Decision Table Testing

A **decision table** lists every combination of a set of conditions as a column, called a **rule**, and states the actions each rule must trigger; testing it means one test per rule. It forces completeness: a combination the specification forgot shows up as a column with no action {% cite beizer1995black %}.

## 1. Structure

Conditions form the upper rows, actions the lower rows, and each column is one rule. The table below is already merged; steps 6–7 of §2 and the worked example in §3 show how it is built. A dash (–) means "don't care": the rule applies whatever that condition is.

| Login | R1 | R2 | R3 | R4 |
|---|:-:|:-:|:-:|:-:|
| Username known? | Y | Y | Y | N |
| Password correct? | Y | Y | N | – |
| Account active? | Y | N | – | – |
| **Log in** | ✓ | | | |
| **Show "account inactive"** | | ✓ | | |
| **Show "invalid credentials"** | | | ✓ | ✓ |
| *Combinations covered* | 1 | 1 | 2 | 4 |

## 2. Building a table

Beizer's procedure has eight steps {% cite beizer1995black %}:

1. List the actions.
2. List the conditions.
3. Count the combinations: the product of each condition's number of values, 2<sup>*n*</sup> for *n* yes/no conditions.
4. Fill in every combination.
5. Decide the actions for each rule.
6. Merge two rules when they have the same actions **and differ in exactly one condition**; that condition becomes "–". Repeat until no merge is possible. The merged table is not unique: merging in a different order can give a different, equally valid set of rules.
7. Check the total: each merged rule covers 2<sup>*k*</sup> combinations for *k* dashes, and the total must equal the count from step 3.
8. Write one test per rule.

## 3. Worked example: consolidating the login table

Three yes/no conditions give 2<sup>3</sup> = 8 combinations. Only (Y, Y, Y) logs in and only (Y, Y, N) is "inactive". Every combination with a wrong password is "invalid credentials" whatever the account state, so (Y, N, Y) and (Y, N, N) merge into R3 = (Y, N, –). An unknown username fails whatever else is true, so its four combinations merge into R4 = (N, –, –).

R3 and R4 trigger the same action but stay separate: they differ in the username and also in the password, which R3 fixes to N and R4 leaves open, so no single condition can become a dash. Merging them anyway would need a rule that covers (Y, Y, –), which logs in or is inactive, not invalid.

**Check:** 1 + 1 + 2 + 4 = 8, so no combination was lost and none is counted twice. Four tests cover the table, one per rule; T4 uses a correct password of another user, to check that a known password cannot log in an unknown username; for a dash, pick any value, ideally the one most likely to expose a wrong "don't care", for example:

| Test | Username | Password | Active | Expected |
|---|---|---|---|---|
| T1 | `alice` | correct | yes | logged in |
| T2 | `alice` | correct | no | "account inactive" |
| T3 | `alice` | wrong | yes | "invalid credentials" |
| T4 | `nobody` | correct (Alice's) | yes | "invalid credentials" |

The merge is not unique: (–, N, –) for every wrong password, plus (N, Y, –) for an unknown user with a correct password, gives an equally correct four-rule table (1 + 1 + 4 + 2 = 8).

A careless merge is the classic error. Writing R2 as (Y, –, N) would cover (Y, N, N) too, a wrong password on an inactive account, and give it the "inactive" message that R3 says it must not get. The check in step 7 catches this: R2 would then cover 2 combinations, and the total would be 9.

**Where the rules come from.** Real conditions and actions sit in business-rule documents, permission matrices or configuration files; the code's `if` chain shows what was implemented, not what was specified.

## When to use it — and when not

- **Use it** for business rules, permissions, pricing and configuration logic, where several conditions together decide the outcome.
- **Use it** with [equivalence partitioning](equivalence.md) to define each condition's values and [boundary value analysis](boundary.md) to test the thresholds inside conditions such as "amount > 100".
- **Do not use it** when the order of events matters (use state-based testing, which tests sequences of events and states) or when outputs vary continuously with an input.

## Limitations

The table grows as 2<sup>*n*</sup> before merging, so beyond five or six conditions it needs splitting or a [combination strategy](integration.md). One test per merged rule tries only one value for each don't-care entry; if the "don't care" is wrong in the code, that single value may not show it.

## How solid is this?

- **Where it comes from.** The eight-step method and the merging rules are from Beizer's *Black-Box Testing* {% cite beizer1995black %}.
- **What we do not hold.** The literature review contains no empirical study of decision-table testing, so no fault-detection figure is given here.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
