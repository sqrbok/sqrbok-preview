---
title: "Worked Case: Threat Model and SAST Triage for a Web Shop"
parent: Security
grand_parent: Quality Attributes
nav_order: 13
layout: default
page_type: worked-case
---

# Worked Case: Threat Model and SAST Triage for a Web Shop

This worked case takes one small web application from source code to a release decision. The methods are on [Threat Modeling](threat-modeling.md), [CVSS Scoring](risk-rating.md), [From Severity to Fix Order](cvss-context.md) and [Static Security Analysis](static-analysis.md).

**Example: the shop before its first release.** A Flask shop (constructed example): `app/web.py` holds the routes, `app/shop.py` is the module from [Static Security Analysis](static-analysis.md), `app/reports.py` counts orders, and `tests/test_shop.py` has three pytest checks. These are the exact files Bandit scanned in §3, so its line numbers match. `app/web.py`:

```python
import random
import sqlite3
import string

import requests
from flask import Flask, abort, request, session

from app import shop

app = Flask(__name__)
app.secret_key = shop.SECRET_KEY
PAYMENT_URL = "https://pay.example.com/v1/charges"


def db():
    return sqlite3.connect("shop.db")


def audit(event):
    with open("audit.log", "a") as log:
        log.write(event + "\n")


@app.post("/login")
def login():
    user = shop.find_user(db(), request.form["name"], request.form["password"])
    if user is None:
        abort(401)
    session["user_id"] = user[0]
    return "ok"


@app.post("/checkout")
def checkout():
    if "user_id" not in session:
        abort(401)
    price = shop.apply_discount(float(request.form["price"]), request.form["code"])
    charge = requests.post(PAYMENT_URL, json={"amount": price})
    audit(f"order user={session['user_id']} amount={price}")
    return charge.json()


@app.post("/admin/refund/<int:order_id>")
def refund(order_id):
    if "user_id" not in session:
        abort(401)
    db().execute("UPDATE orders SET refunded = 1 WHERE id = ?", (order_id,))
    audit(f"refund order={order_id} by={session['user_id']}")
    return "refunded"


def order_reference():
    return "".join(random.choice(string.ascii_uppercase) for _ in range(8))


if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

`app/shop.py`:

```python
import hashlib
import sqlite3

SECRET_KEY = "shop-admin-2024"


def hash_password(password):
    return hashlib.md5(password.encode()).hexdigest()


def find_user(db, name, password):
    query = f"SELECT id FROM users WHERE name = '{name}' AND pw = '{hash_password(password)}'"
    return db.execute(query).fetchone()


def apply_discount(price, rule):
    return eval(rule, {"price": price})
```

`app/reports.py`, and `tests/test_shop.py`, whose `assert` lines are 5, 9 and 13:

```python
ORDERS_TABLE = "orders"


def order_count(db):
    return db.execute(f"SELECT COUNT(*) FROM {ORDERS_TABLE}").fetchone()[0]
```

```python
from app import shop


def test_discount_none():
    assert shop.apply_discount(10.0, "price") == 10.0


def test_discount_student():
    assert shop.apply_discount(10.0, "price * 0.9") == 9.0


def test_hash_is_stable():
    assert shop.hash_password("a") == shop.hash_password("a")
```

Customers pay on the payment provider's hosted page, so card numbers never reach the shop. _(Bandit 1.9.4 on Python 3.12.9 was run for this page; CVSS 3.1 scores were computed and checked against [CVSS Scoring](risk-rating.md).)_

## 1. Draw the system

On a real system the diagram comes from three sources, not from memory: the route list (`flask routes` prints every endpoint), the deployment configuration (what runs where, which ports are open), and the places the code reads and writes (database URLs, files, outgoing HTTP calls; `grep -rn "requests\.\|open(\|connect(" app` finds them). Number every element, as Shostack recommends {% cite shostack2014threat %}, so that each threat can name its element.

![Data flow diagram: customer E1 and admin E2 send flows F1 and F2 across the shop-server trust boundary to web shop process P1, which writes to shop database DS1 and audit log DS2 and sends charges F5 to payment provider E3 in its own boundary; threat IDs T1 to T10 are marked in red on their elements](shop-threat-model.svg)

_The shop's DFD with numbered elements and the threats found on each (letters are STRIDE)._ {% cite shostack2014threat %}

**Decision point: what is in scope?** Criterion: every element that holds or moves data whose loss would harm customers or the business, and every flow that crosses a trust boundary. The provider's internals are out of scope; the flow to it, F5, is in.

## 2. Walk STRIDE per element and score each threat

Apply the letters each element type is open to (external entities S, R; processes all six; flows and stores T, I, D, and a store that is a log also R, because it must prove who did what {% cite shostack2014threat %}) and ask one question per letter: "how could someone do this here?" Record S on the entity whose identity is faked, following [Threat Modeling](threat-modeling.md). The walk's coverage, with "–" for a letter considered where nothing was found and "·" for a letter that does not apply:

| Element | S | T | R | I | D | E |
|---|:-:|:-:|:-:|:-:|:-:|:-:|
| E1 Customer | T1, T5, T6 | · | – | · | · | · |
| E2 Admin | T5 | · | – | · | · | · |
| E3 Payment provider | – | · | – | · | · | · |
| P1 Web shop | – | T4 | – | – | – | T2, T3 |
| F1, F2 (cross the boundary; TLS at the proxy) | · | – | · | – | – | · |
| F3, F4 (inside the server) | · | – | · | – | – | · |
| F5 Charge | · | – | · | T9 | T10 | · |
| DS1 Shop database | · | T1 | · | T1 | T8 | · |
| DS2 Audit log | · | T7 | T7 | – | – | · |

Set the environmental requirements once, for the whole shop: **CR:M** (names, addresses and order history, no card data), **IR:H** (orders and refunds move money), **AR:M** (an outage loses sales, not safety). Then score each threat with procedure (b) on [CVSS Scoring](risk-rating.md), the rule for a team's own findings: scope stays unchanged unless the impact lands in a component under another security authority. Every impact here stays inside the shop's own authority, so all are S:U. The environmental score uses the formulas on [From Severity to Fix Order](cvss-context.md). "CVSS Low, Medium, High" are rating bands; "Bandit severity" is the tool's own scale.

**Decision rule** (fixed before scoring): an open threat with environmental score 7.0 or higher **blocks the release**. A CVSS Medium (4.0–6.9) is mitigated by a named date or accepted by the product owner with an expiry date and a compensating control. A CVSS Low may be accepted with an owner. A threat whose handling belongs to another party by contract is **transferred**, and the contract is checked.

This is the deliverable; copy the columns:

| ID | Element | STRIDE | Threat | CVSS 3.1 base vector | Base | Env | Owner | Decision |
|---|---|---|---|---|:-:|:-:|---|---|
| T1 | DS1; E1 | I, T; S | SQL injection in login reads and changes any account; the attacker acts as its owner | `AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N` | 9.1 | 9.8 | Backend | Mitigate: parameterised query. **Blocks** |
| T2 | P1 | E | Discount code reaches `eval`: any customer runs code in the shop | `AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H` | 8.8 | 8.8 | Backend | Mitigate: allowlist. **Blocks** |
| T3 | P1 | E | Refund route checks login, not admin role: any customer refunds any order | `AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:H/A:N` | 6.5 | 8.3 | Backend | Mitigate: role check, 403. **Blocks** |
| T5 | E1, E2 | S | Session key in source: whoever reads the repository forges a session for any user, admin included | `AV:N/AC:H/PR:N/UI:N/S:U/C:H/I:H/A:N` | 7.4 | 8.1 | Ops | Mitigate: key from environment, rotate. **Blocks** |
| T6 | E1 | S | No rate limit on login: reused passwords from other breaches open accounts | `AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:L/A:N` | 6.5 | 7.0 | Ops | Mitigate: rate limit at the proxy. **Blocks** |
| T4 | P1 | T | Price taken from the request: a customer pays what they type | `AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:L/A:N` | 4.3 | 5.0 | Backend | Mitigate now: server-side price list (one line) |
| T10 | F5 | D | Payment call has no timeout: while the provider is slow, a customer starting many checkouts ties up every worker | `AV:N/AC:H/PR:L/UI:N/S:U/C:N/I:N/A:H` | 5.3 | 5.3 | Backend | Mitigate by 2026-12-01: `timeout=10`; Bandit baseline until then |
| T8 | DS1 | D | Scripted fake orders fill the database | `AV:N/AC:L/PR:L/UI:N/S:U/C:N/I:N/A:L` | 4.3 | 4.3 | Product owner | Accept until 2026-12-01; alert on order rate |
| T7 | DS2 | T, R | An admin with a shell edits the audit log to hide a refund | `AV:L/AC:L/PR:H/UI:N/S:U/C:N/I:L/A:N` | 2.3 | 3.0 | Ops | Accept until 2026-12-01; then ship the log off the server |
| T9 | F5 | I | Card data exposed in the payment flow | not scored | – | – | Product owner | Transfer: hosted payment page, provider's compliance |

T5 is AC:H because the attacker must first obtain the source code, and T10 because it needs the provider to be slow; both are conditions outside the attacker's control. The dates are constructed. Two rows show why the environmental score matters. T3 is Medium on its base score (6.5) but High (8.3) once integrity is weighted for money; T6 lands exactly on the gate at 7.0. Recompute one row to check: for T3, the modified impact sub-score MISS = 1 − (1 − 1.5 × 0.56) = 0.84, Modified Impact = 6.42 × 0.84 = 5.393, plus exploitability 8.22 × 0.85 × 0.77 × 0.62 × 0.85 = 2.835, gives roundup(8.228) = 8.3, where roundup rounds up to one decimal.

## 3. Run the static analyser

The input is the source tree as it will ship. Bandit over everything, one line per finding:

```text
$ bandit -q -r app tests -f custom --msg-template '{relpath}:{line} {test_id} {severity} {confidence}'
app/reports.py:5 B608 MEDIUM MEDIUM
app/shop.py:4 B105 LOW MEDIUM
app/shop.py:8 B324 HIGH HIGH
app/shop.py:12 B608 MEDIUM LOW
app/shop.py:17 B307 MEDIUM HIGH
app/web.py:38 B113 MEDIUM LOW
app/web.py:53 B311 LOW HIGH
app/web.py:57 B201 HIGH MEDIUM
app/web.py:57 B104 MEDIUM MEDIUM
tests/test_shop.py:5 B101 LOW HIGH
tests/test_shop.py:9 B101 LOW HIGH
tests/test_shop.py:13 B101 LOW HIGH
```

## 4. Triage every finding

**Decision point: what is each finding?** Criterion: one of four verdicts per finding. **Real**: it maps to a threat row, or opens a new one. **Hardening**: a weakness with no reachable attack today; fix when cheap. **False positive**: the pattern is there, the danger is not; suppress with a reason. **Deferred**: real, mapped to a threat row below the gate; record it in the baseline until that row's date.

| Finding | Verdict | Action |
|---|---|---|
| `shop.py:12` B608, `shop.py:17` B307 | Real: T1, T2 | Fix (blocks) |
| `shop.py:4` B105 | Real: T5. Bandit severity Low; the key signs sessions, so it is CVSS 8.1 (High) | Fix (blocks) |
| `shop.py:8` B324 MD5 | Real (part of T1: injected reads expose the hashes) | Fixed with T1 (salted slow hash) |
| `web.py:57` B201, B104 | Hardening: the debugger allows code execution, but this block runs only under `python app/web.py`, never when gunicorn imports the module in production | `app.run()` with defaults |
| `reports.py:5` B608 | False positive: constant table name | `# nosec B608` |
| `web.py:53` B311 | False positive: an order number, not a secret | `# nosec B311` |
| `web.py:38` B113 | Deferred: T10 (CVSS 5.3) | Baseline until 2026-12-01 |
| `tests/*` B101 | False positive: `assert` is how pytest checks | `exclude_dirs = ["tests"]` under `[tool.bandit]` in `pyproject.toml` |

Bandit found the code-level half of the threat table, T1, T2 and T5, plus one weakness the walk had missed, B113, which opened T10. It found nothing for T3, T4, T6, T7, T8 or T9, because each is a missing check or a design decision, not a dangerous line: McGraw's bugs and flaws {% cite mcgraw2006software %}.

**The `# nosec` policy.** A suppression names its test ID, so it hides one rule, not the whole line, and its reason goes on the line above. Putting the reason after the ID does not work: Bandit reads every following word as a test name and prints a warning per word, such as `Test in comment: constant is not a test name or id, ignoring`. So:

```python
def order_count(db):
    # safe because: table name is a module constant; no input reaches the query
    return db.execute(f"SELECT COUNT(*) FROM {ORDERS_TABLE}").fetchone()[0]  # nosec B608
```

**The baseline.** After the fixes, the remaining deferred finding is frozen into a baseline, and the build compares against it, so only new findings fail the build {% cite bandit_docs %}:

```text
$ bandit -q -c pyproject.toml -r . -f json -o .bandit-baseline.json
$ bandit -q -c pyproject.toml -r . -b .bandit-baseline.json; echo "exit=$?"
exit=0
```

To check that the gate still bites, a new function with `subprocess.run(f"... > {path}", shell=True)` was added: the same command then reports B404 (Bandit severity Low) and B602 (High) and exits 1, while the baselined B113 stays silent.

## 5. Decide

The release is blocked until T1, T2, T3, T5 and T6 are mitigated, since each scores 7.0 or more. It ships when three checks pass. White-box exploit tests for the three blockers fixed in code, T1, T2 and T3, must pass; they are written as on [Dynamic Security Testing](dynamic-testing.md). T5 and T6 are fixed in configuration (a rotated key, a proxy rule), so they are checked at deployment instead. Bandit against the baseline must exit 0. And the threat table must have no open row at 7.0 or above. T4 and the hardening fixes go in the same release because they are cheap. T10 is mitigated by its date. T7 and T8 are accepted, with an owner and an expiry. T9 is transferred.

The pipeline gate, added to the workflow on [DevSecOps and Supply Chain Security](devsecops.md):

```yaml
      - run: pip install bandit
      - run: bandit -c pyproject.toml -r . -b .bandit-baseline.json
      - run: |                       # every nosec names its test ID
          ! grep -rnE '#\s*nosec(\s*$|[^ ]|\s+[^B])' app
```

The last step fails on a bare `# nosec`, on `#nosec` and on `# nosec because …`; tested, it prints the offending line and exits 1.

## Common mistakes

1. **Reading Bandit's severity as risk.** The hard-coded key had Bandit severity Low and was the fourth-highest threat. Spot it: a release decision phrased in Bandit severities.
2. **A clean scan read as a secure app.** After the fixes Bandit is silent, yet without the walk T3 and T6 would still be open. Spot it: a threat table with no rows that lack a tool finding.
3. **A baseline used as an amnesty.** Spot it: High findings or entries without a ticket in `.bandit-baseline.json`. Keep High findings out of it.
4. **Reasons after `# nosec`.** Spot it: "Test in comment" warnings in the scan log.

## How solid is this?

- **Where it comes from.** The four questions, numbered DFD elements and STRIDE-per-element from Shostack {% cite shostack2014threat %}; CVSS 3.1 {% cite first_cvss31 %}; Bandit's documentation and this run {% cite bandit_docs %}. The decision rule is this page's choice, not a standard.
- **What is contested.** Completeness: students using STRIDE found 36% of the known threats, with 81% of what they reported being correct {% cite scandariato2015stride %}, so a filled table is a floor. Real projects took about 69 hours {% cite yskout2020tmmaturity %}; this shop is far smaller.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
