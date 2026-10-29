---
title: "Worked Case: Inspecting a Refund Change"
parent: Inspection
nav_order: 8
layout: default
page_type: worked-case
math: katex
---

# Worked Case: Inspecting a Refund Change

This worked case runs one code inspection end to end on a 147-line Python change: planning with size and rate limits, a checklist, individual preparation logged in a defect log, the meeting, a capture-recapture estimate of the defects still in the code, and the exit decision. The method pages explain each step: [Fagan Inspection Process](fagan-process.md), [Reading Techniques](reading-techniques.md), [Capture-Recapture Estimation](capture-recapture.md) and [Capture-Recapture in Practice](capture-recapture-practice.md). (Constructed example: the code and its defects are real and were run; the four inspectors' logs are constructed.)

## The case

A payments team adds **partial refunds** to its card service. The pull request adds one file, `refunds.py`, 147 lines (113 not blank or comment). The ticket says: *a captured payment can be refunded in one or more parts, within 30 days of capture, up to the captured amount.* Money moves on every call, so the team lead asks for a full inspection instead of the usual one-reviewer pull-request review. The heart of the change, with the file's line numbers:

```python
53      def refund(self, payment_id, amount, currency, idempotency_key):
54          """Refund part of a captured payment.
55
56          amount -- the amount to refund, in cents
57          """
58          payment = self.store.get_payment(payment_id)
59          if payment is None:
60              raise RefundError("unknown payment")
62          age = self.clock() - payment.captured_at
63          if age.days >= 30:
64              raise RefundError("refund window closed")
66          if amount > payment.amount:
67              raise RefundError("refund exceeds captured amount")
69          self._lock.acquire()
70          refund = Refund(
71              refund_id=self.store.next_id(),
72              payment_id=payment_id,
73              amount=amount,
74              currency=currency,
75              created_at=self.clock(),
76          )
77          if self.store.is_frozen(payment_id):
78              return None
79          payment.refunds.append(refund)
80          self.store.save_refund(refund)
81          self._lock.release()
83          log.info("refund %s for card %s amount %s",
84                   refund.refund_id, payment.card_number, amount)
86          try:
87              self.gateway.send_refund(payment.payment_id, refund.amount,
88                                       refund.currency, idempotency_key)
89          except Exception:
90              pass
91          refund.status = "sent"
92          self.store.save_refund(refund)
93          return refund
95      def refunded_total(self, payment_id):
97          payment = self.store.get_payment(payment_id)
98          tot = 0
99          for r in payment.refunds:
100             if r.status == "sent":
101                 tot = tot + r.amount
102         return tot
104     def fee_share(self, payment_id, refund_amount, fee_rate):
105         """Part of the card fee returned with a refund, rounded to cents."""
106         payment = self.store.get_payment(payment_id)
107         fee = float(payment.amount) * fee_rate
108         return round(fee * float(refund_amount) / float(payment.amount), 2)
```

Lines 1 to 52 hold the imports, two exception classes and the `Payment` and `Refund` data classes (`amount: Decimal`, in the payment currency); lines 111 to 147 an in-memory store and a fake gateway for tests. `self._seen_keys = {}` is set in `__init__` (line 51) and never used.

## Step 1. Planning: entry criteria, team, schedule

The moderator checks the **entry criteria** first: the existing tests pass and the project's linter is clean (`ruff check --select E,F refunds.py` printed `All checks passed!`). Inspection time is too expensive to spend on what a tool reports.

**Decision point: is the change small enough for one session?** Two limits apply: Fagan's preparation rate of 100 to 125 non-commentary statements (NCSS) per hour with a meeting of at most two hours {% cite fagan1976design fagan1986advances %}, and Cohen's 200 to 400 lines per session {% cite cohen2006secrets %}. At 113 NCSS the change passes both. For example, a 600-line change would be split by file before inspecting.

The team is five people. The author answers questions but does not inspect; four inspectors are needed anyway, because capture-recapture is not reliable with fewer {% cite briand2000capture %}. Inspector I1 is the moderator, I2 the reader, I3 takes the tester's viewpoint and I4 is a developer from the fraud team. The effort, at Fagan's rates:

| Activity | Rate | People | Staff-hours |
|---|---|---|---|
| Overview by the author | 500 NCSS/h: 113 / 500 = 0.23 h | 5 | 1.1 |
| Individual preparation | 125 NCSS/h: 113 / 125 = 0.9 h | 4 | 3.6 |
| Meeting | 100 NCSS/h: 113 / 100 = 1.1 h | 5 | 5.7 |
| **Before rework** | | | **10.4** |

## Step 2. The checklist

Each inspector reads with the team's code checklist. Cohen recommends 10 to 20 items aimed at the team's own common mistakes and at omissions {% cite cohen2006secrets %}; items 1, 2, 6 and 14 follow his categories, the rest from this team's past payment incidents. A checklist only works if every item is a question that can be answered by pointing at a line.

1. Is every parameter validated at the start (sign, range, type)?
2. Is the unit of every numeric value stated, and the same in the code and the docstring?
3. Is money kept in `Decimal` or integer cents, never `float`?
4. Is every limit checked against the cumulative state, not only the current request?
5. Is every error from an external call handled or re-raised, and does the stored status reflect what happened?
6. Is every lock or resource released on every path, including early returns and exceptions?
7. Is shared state changed under a lock read under the same lock?
8. Does a repeated request with the same idempotency key have no second effect?
9. Are card numbers, tokens and personal data kept out of logs?
10. Are currency, time zone and locale handled explicitly?
11. Does every boundary (`<` or `<=`) match the wording of the specification?
12. Can a caller tell failure from success (no silent `None`)?
13. Does every function handle an unknown ID?
14. Does the change do what the ticket asks, and nothing more?
15. Are constants named (minor)?
16. Do names and docstrings match the behaviour (minor)?

## Step 3. Individual preparation and the defect log

Each inspector works alone for about an hour and logs every issue with its location, the checklist item, a severity and their own label (I1 to I4). **Major** means it would probably cause a failure or a wrong implementation if left in; **minor** means it would not ([Fagan Inspection Process](fagan-process.md)). Nobody sees another's log before the meeting: overlap between independent logs is what the estimate measures.

## Step 4. The meeting: match, confirm, log

The reader paraphrases the code block by block; the moderator merges the four logs. **Decision point: are two reports one defect?** The criterion is one needed fix ([Capture-Recapture in Practice](capture-recapture-practice.md)). I2 logged "lock not released when the payment is frozen (line 78)" and I4 logged "lock leaks if `save_refund` raises (line 80)": one fix, `with self._lock:`, removes both, so they are one defect, D4, found by both. I3's question "is day 30 inside *within 30 days*?" (line 63) is not a defect but an open question for the product owner. The merged log:

| ID | Line | Defect | Item | Sev. | Found by |
|---|---|---|---|---|---|
| D1 | 66 | only this refund is compared with the captured amount; two refunds of 60.00 on 100.00 both pass | 4 | major | I1 I2 I3 I4 |
| D2 | 66 | zero and negative amounts are accepted | 1 | major | I1 I3 I4 |
| D3 | 74 | refund currency is never compared with the payment currency | 10 | major | I2 I3 |
| D4 | 69–81 | lock not released on the frozen path or on an exception | 6 | major | I1 I2 I4 |
| D5 | 89–91 | gateway errors are swallowed and the refund is still marked `sent` | 5 | major | I1 I2 I3 I4 |
| D6 | 51, 88 | idempotency key never checked locally; a retry creates a second refund | 8 | major | I3 I4 |
| D7 | 83–84 | full card number written to the log | 9 | major | I4 |
| D8 | 56 | docstring says cents; amounts are `Decimal` currency units, so a caller passing cents refunds 100 times too much | 2 | major | I2 |
| D9 | 107–108 | fee share computed in `float` | 3 | major | I1 I4 |
| D10 | 97–99 | `refunded_total` crashes on an unknown payment ID | 13 | major | meeting |
| m1 | 63 | magic number 30 | 15 | minor | I1 I3 |
| m2 | 98 | `tot` starts as `int`, name unclear | 16 | minor | I2 |

Four majors were checked by running them before the log was closed, so nobody had to argue about them. The script imports the full `refunds.py`, including the in-memory store and fake gateway in lines 111 to 147:

```python
# confirm.py
import threading
from datetime import datetime, timezone
from decimal import Decimal
from refunds import FakeGateway, MemoryStore, Payment, RefundService

def setup(fail=False):
    store = MemoryStore()
    store.add_payment(Payment("p1", Decimal("100.00"), "EUR", "4111111111111111",
                              datetime(2026, 10, 1, tzinfo=timezone.utc)))
    clock = lambda: datetime(2026, 10, 10, tzinfo=timezone.utc)
    return store, RefundService(FakeGateway(fail), store, clock)

store, svc = setup()                                   # D1: cumulative over-refund
svc.refund("p1", Decimal("60.00"), "EUR", "k1"); svc.refund("p1", Decimal("60.00"), "EUR", "k2")
print("D1 refunded total:", svc.refunded_total("p1"), "of 100.00")

store, svc = setup(fail=True)                          # D5: gateway failure hidden
print("D5 status after gateway timeout:", svc.refund("p1", Decimal("10.00"), "EUR", "k3").status)

store, svc = setup()                                   # D4: lock kept on the frozen path
store.frozen.add("p1"); svc.refund("p1", Decimal("10.00"), "EUR", "k4")
t = threading.Thread(target=svc.refund, args=("p1", Decimal("10.00"), "EUR", "k5"), daemon=True)
t.start(); t.join(timeout=2)
print("D4 second call still blocked after 2 s:", t.is_alive())

store, svc = setup()                                   # D9: float money, 0.015 rounds down
print("D9 fee share of a 1.00 refund at 1.5%:", svc.fee_share("p1", Decimal("1.00"), 0.015))
```

```text
$ python3 confirm.py
D1 refunded total: 120.00 of 100.00
D5 status after gateway timeout: sent
D4 second call still blocked after 2 s: True
D9 fee share of a 1.00 refund at 1.5%: 0.01
```

The exact fee share is 0.015, which rounds half-up to 0.02; in `float` it is 0.0149… and becomes 0.01. D10 raised `AttributeError: 'NoneType' object has no attribute 'refunds'`.

## Step 5. Estimate what is left

Capture-recapture uses only the preparation logs; D10, raised in the meeting, is a found defect but not an input. *N̂* estimates all defects the preparation logs point to, found or not; D10 is one of the unfound ones the estimate already allows for, now found in the meeting, so it is subtracted along with *S* rather than added. The first-order jackknife {% cite burnham1978estimation briand2000capture %} needs three counts from the table, with *k* = 4 inspectors:

- *S* = 9 distinct majors found in preparation (D1 to D9);
- *f*<sub>1</sub> = 2 found by exactly one inspector (D7 by I4, D8 by I2);
- *M* = 1 major found only in the meeting (D10).

$$\hat{N} = S + f_1 \times \frac{k-1}{k} = 9 + 2 \times \frac{3}{4} = 10.5$$

$$\text{remaining} = \max(0,\ \hat{N} - S - M) = 10.5 - 9 - 1 = 0.5$$

## Step 6. The exit decision

**Criterion:** at most 0.25 major defects per page estimated to remain {% cite malotaux2008onepage %}. The held sources do not define a page of code; this team counts a printed page as 50 lines, so the 147-line file is 3 pages and the limit is 3 × 0.25 = 0.75. The estimate is 0.5, or 0.17 per page.

**Decision: pass after rework, no reinspection.** The author fixes D1 to D10; the moderator checks every fix in follow-up, because about one fix in six is wrong or introduces a new defect {% cite fagan1986advances %}, and each confirmed major gets a regression test, starting from the four runs above. Had *f*<sub>1</sub> been 4, the estimate would have been 12 − 9 − 1 = 2 remaining, 0.67 per page, and the change would have been reinspected after rework.

**Causal analysis.** D5 is the kind a tool finds: `ruff check --select S110,BLE001 refunds.py` reports line 89 (`try`-`except`-`pass`, blind `except Exception`). The team adds both rules to its linter and drops item 5's first half from the checklist, keeping inspection time for what tools cannot see: D1, D6 and D8 are omissions and mismatches with the ticket.

## The finished artifact: two checklists

Use the sixteen items above for a full inspection. For everyday pull requests the same team uses a short version, read in the review tool, in one sitting of at most 60 to 90 minutes and under 400 changed lines at no more than 500 lines an hour {% cite cohen2006secrets %}; see [Modern Code Review](modern-code-review.md).

```markdown
## Reviewer checklist (paste into the PR)
- [ ] I read every changed line; diff is under 400 lines (otherwise ask to split)
- [ ] Inputs validated; units stated (cents or Decimal?)
- [ ] Money in Decimal or integer cents, never float
- [ ] Currency, time zone and locale explicit
- [ ] Limits checked against cumulative state, not just this request
- [ ] External call errors handled; stored status matches the outcome
- [ ] Locks, files, connections released on every path (`with`)
- [ ] Retries safe (idempotency key honoured)
- [ ] No card numbers, tokens or personal data in logs
- [ ] Unknown IDs and empty inputs handled
- [ ] Every new branch has a test, or I named the missing one in a comment
- [ ] Majors marked "blocking"; minors marked "nit"
```

The defect log template, one row per defect after matching:

| ID | File:line | Defect (one needed fix) | Checklist item | Major/minor | Found by (I1–I4, or meeting) | Fixed in commit | Checked by |
|---|---|---|---|---|---|---|---|
| D1 | `refunds.py:66` | refunds not checked against the cumulative total | 4 | major | I1 I2 I3 I4 | (hash) | I1 |

## Common mistakes

1. **Inspecting what the linter finds.** *Spot it:* style and unused-variable items in the log; make "linter clean" an entry criterion.
2. **Sharing notes before the meeting.** *Spot it:* almost every defect is found by all four; the overlap is then not independent and the estimate falls towards *S*.
3. **Counting one fix twice.** *Spot it:* two log rows that the same change would remove, like the two lock reports; counted separately they raise *f*<sub>1</sub> and inflate the estimate.
4. **Feeding meeting finds into the estimate.** *Spot it:* a defect with no inspector's initial in the *S* count; it belongs in *M*.
5. **Rushing a large change.** *Spot it:* preparation time below size ÷ 125 NCSS per hour, or more than 400 lines in one sitting; split it instead.

## How solid is this?

- **Where it comes from.** Rates and steps from Fagan {% cite fagan1976design fagan1986advances %}; session limits and checklist advice from Cohen {% cite cohen2006secrets %}; the estimator and the four-inspector minimum from Briand and colleagues {% cite briand2000capture %}; the exit rule from Malotaux {% cite malotaux2008onepage %}. The confirmation runs and the ruff runs (ruff 0.17.3, Python 3.12.9) were made for this page on 29 October 2026.
- **What is contested.** Fifty lines per page is this team's convention, not a sourced rule; with 40 lines per page the limit would be about 0.92 and the decision the same.
- **What we do not hold.** Perspective-based reading question sets for code: Basili's procedures are held for requirements documents only, so this case uses checklist reading.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
