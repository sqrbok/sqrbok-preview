---
title: Secure Design Principles
parent: Security
grand_parent: Quality Attributes
nav_order: 9
layout: default
---

# Secure Design Principles

**Secure design principles** are rules for structuring a system so that whole classes of vulnerability cannot arise, rather than fixing them one by one; the classic set is Saltzer and Schroeder's eight principles from 1975 {% cite saltzer1975protection %}.

## 1. Saltzer and Schroeder's eight principles

Saltzer and Schroeder's principles reduce the number and seriousness of flaws, since flawless protection is out of reach {% cite saltzer1975protection %}. They are one vocabulary; Howard and LeBlanc's SD3 commitments in §2 are the other:

| Principle | Saltzer and Schroeder's definition, shortened | A present-day example (ours) |
|---|---|---|
| Economy of mechanism | Keep the design as simple and small as possible | One small, reviewed authorization module instead of checks scattered through the code |
| Fail-safe defaults | Base access on permission, not exclusion | A firewall that denies all traffic except listed ports |
| Complete mediation | Check every access to every object for authority | Validate the session token on every request, not only at login |
| Open design | The design should not be secret; security rests on keys | Published encryption algorithms with secret keys |
| Separation of privilege | Require two keys where feasible | A merge needs both a passing pipeline and a second reviewer |
| Least privilege | Give every program and user the least privilege they need | The web shop's database account can read orders but not drop tables |
| Least common mechanism | Minimise mechanisms shared by all users | Separate containers per service instead of one shared server process |
| Psychological acceptability | Make protection easy to use correctly | A password manager instead of a 20-character rotation rule |

Their reason for fail-safe defaults still holds: a mistake in a mechanism that grants permission fails by refusing access, noticed at once; one in a mechanism that excludes access fails by allowing it, unnoticed {% cite saltzer1975protection %}. Least privilege also limits damage after a break-in: browsers render pages in low-privilege sandboxes, and a non-root container on a read-only file system leaves an intruder little.

## 2. Secure by design, by default, in deployment

Howard and LeBlanc turned such principles into three commitments for product teams, **SD3** {% cite howard2006writing %}. *Secure by design*: the architecture rules out vulnerability classes, guided by a threat model. *Secure by default*: the product as installed has a small **attack surface** (the set of entry points an attacker can send input to), with optional features off and no default passwords. *Secure in deployment*: operators get guidance and patches.

## 3. All input is evil until proven otherwise

Howard and LeBlanc's best-known rule: treat all external input as hostile until validated {% cite howard2006writing %}. Three habits follow:

- **Allowlist, not blocklist.** Define what is valid and reject everything else. A blocklist fails on the first variant it did not anticipate: block `--` and the attacker sends `alice' /*`, since SQLite reads an unclosed `/*` as a comment to the end (tested on the shop).
- **Canonicalise first.** Decode and normalise input to one form, then validate it; otherwise an apostrophe sent percent-encoded as `%27`, or a Unicode look-alike, passes a check for apostrophes and is decoded into one afterwards.
- **Validate at a chokepoint.** Put validation where input enters, in one place: one routine is easy to review and test, and no code path can skip it.

## 4. Example: the shop module after the fixes

The shop module from [Static Security Analysis](static-analysis.md), with its four Bandit findings fixed:

```python
import hashlib
import os
import sqlite3

SECRET_KEY = os.environ["SHOP_SECRET_KEY"]


def hash_password(password, salt):
    return hashlib.pbkdf2_hmac("sha256", password.encode(), salt, 600_000).hex()


def find_user(db, name, password):
    row = db.execute("SELECT id, salt, pw FROM users WHERE name = ?", (name,)).fetchone()
    if row and row[2] == hash_password(password, row[1]):
        return (row[0],)
    return None


DISCOUNTS = {"none": 1.0, "student": 0.9, "black_friday": 0.7}


def apply_discount(price, rule):
    return price * DISCOUNTS[rule]
```

The `?` placeholder keeps input as data, never SQL, so injection is impossible by construction. The discount rule is looked up in an **allowlist**: any other value raises an error instead of reaching `eval` (all input is evil until it matches). The secret moves out of the code (open design: security rests on the key, not on hiding the source). Passwords get a random per-user **salt** (defeating precomputed tables) and a deliberately slow hash. Bandit reports no issues, and the exploit test on [Dynamic Security Testing](dynamic-testing.md) passes.

## When to use it — and when not

Use the principles as a review checklist for designs and for changes to authentication, authorization or input handling. They reduce flaws but prove nothing, so they work alongside [Threat Modeling](threat-modeling.md) and testing, not instead of them.

## Limitation

The principles conflict. Separation of privilege hurts psychological acceptability, and complete mediation costs performance; Saltzer and Schroeder warn that caching an authority check needs scepticism {% cite saltzer1975protection %}. Applying them takes judgement.

## How solid is this?

- **Where it comes from.** The eight principles and their definitions are from Saltzer and Schroeder {% cite saltzer1975protection %}; SD3 and the input rules from Howard and LeBlanc's practitioner book {% cite howard2006writing %}. The code and its Bandit result were run as shown.
- **What is contested.** Saltzer and Schroeder also listed two principles they considered only partly applicable to computers, work factor and compromise recording; most modern lists keep the eight.
- **What we do not hold.** Empirical studies measuring how much each principle reduces vulnerabilities.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
