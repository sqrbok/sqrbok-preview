---
title: Continuous Deployment at Facebook
parent: Industry Practices
nav_order: 11
layout: default
---

# Continuous Deployment at Facebook

**Continuous deployment** is the practice of releasing each change to production as soon as it passes automated testing, with the decision to deploy left to developers rather than a separate test or release team. Facebook's studies of its own data show it can scale without a loss of quality, and where mobile apps force a slower variant {% cite savor2016facebook rossi2016mobilefb %}.

## 1. Web: deploy every day

Savor and colleagues compared Facebook with OANDA, a smaller currency-trading company, using seven years of Facebook data {% cite savor2016facebook %}:

| Measure | Facebook | OANDA |
|---|---|---|
| Updates released per developer per week | 3.5 | about 1 |
| Median update size | 33 lines | 57 lines |
| Engineers working on deployment and test tooling | about 5% | 15% |

Over the period, Facebook's engineering team grew 20-fold and its code base 50-fold, yet productivity per developer did not drop, and the number of high-priority production failures grew far more slowly than the number of deployments (a trend-line slope of 0.0004). Neither company used a separate testing team: developers wrote the tests and were responsible for their code in production. Management mattered: at OANDA, the average number of deployments fell 23% after a new management team took over, although the engineers were largely the same.

## 2. Mobile: a release train

Mobile apps cannot be updated silently: users must install each version, app stores review releases, a fix cannot be rolled back easily, and the code ships as one binary to hundreds of device types. Facebook therefore runs a **release train** for its apps {% cite rossi2016mobilefb %}:

1. Developers keep pushing changes to the main branch.
2. A release branch is cut once a week for Android and every two weeks for iOS.
3. For **five days** the branch is stabilized; only fixes are cherry-picked in, and builds are installed on employees' phones ("dogfooding") three times a day.
4. The code is frozen for a **three-day soak**, accepting only launch-blocking fixes.
5. The release goes to the app stores.

**Example.** For iOS, the branch is cut every two weeks and released on the second Monday after the cut. A change committed just before a cut reaches users about two weeks later; one committed just after a cut waits up to two weeks for the next cut, so about four. A web change at Facebook could reach production the same day. The difference is the cost of a bad release: on the web it can be replaced in hours; in an app it stays on phones until users update.

## 3. Automated testing at diff time

Facebook's **Sapienz** generates test sequences for Android apps with a multi-objective search algorithm and reports crashes as comments on the developer's change (a "diff") in code review, before it lands. Developers fixed at least an estimated 75% of the crashes it reported {% cite alshahwan2018sapienz %}. Like the static analyzer Infer before it, Sapienz succeeded by delivering its findings where developers already work.

## When to use it — and when not

Continuous deployment fits services under the team's control, with automated tests, monitoring and fast rollback. A release train fits products that users install. Neither replaces testing; both depend on it, and Facebook and OANDA invested 5–15% of their engineers in the tooling.

## Limitations

- **Companies studying themselves.** All three papers are written by or with the companies' engineers.
- **Context.** Facebook's results come with its scale, tooling budget and culture; OANDA shows a smaller company can follow, at a higher relative tooling cost.

## How solid is this?

- **Where it comes from.** Studies of continuous deployment at Facebook and OANDA, of Facebook's mobile release process, and of Sapienz's deployment {% cite savor2016facebook rossi2016mobilefb alshahwan2018sapienz %}.
- **What is contested.** Whether to keep a separate testing team; the authors call it one of the more controversial questions.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
