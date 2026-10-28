---
title: Industry Case Studies
parent: Industry Practices
nav_order: 7
layout: default
---

# Industry Case Studies

**Industry case studies** are published analyses of a company's own engineering data: Google, Microsoft and Facebook engineers, often with academic co-authors, measuring how their practices work at scale. They are the best evidence available about large-scale practice, and also the most context-bound. This page compares them; the detail is on the company pages.

## 1. The studies

| Company | Study | Scale | Main finding | Page |
|---|---|---|---|---|
| Google | code review {% cite sadowski2018codereview %} | ~9 million reviewed changes | median 24 lines and 1 reviewer; review mainly for education and maintainability | [Google](08-google-engineering-practices.md) |
| Google | testing at scale {% cite memon2017testing %} | 5.5 million test targets in one month | only 63,000 ever failed; distance from the change predicts failure | [Google](08-google-engineering-practices.md) |
| Microsoft | review usefulness {% cite bosu2015codereview %} | 1.5 million review comments | prior experience with the file roughly doubles useful comments | [Microsoft](09-microsoft-quality-practices.md) |
| Microsoft | distributed development {% cite bird2009vista %} | Windows Vista binaries | distribution adds almost no failures once team size is controlled | [Microsoft](09-microsoft-quality-practices.md) |
| Microsoft | refactoring {% cite kim2014refactoring %} | 328 engineers, Windows 7 history | heavily refactored modules reduced their dependencies | [Microsoft](09-microsoft-quality-practices.md) |
| Microsoft | machine-learning engineering {% cite amershi2019mlse %} | 14 interviews plus a company-wide survey | nine-stage ML workflow; data, not code, drives quality | this page |
| Facebook | continuous deployment {% cite savor2016facebook %} | seven years of data | team ×20, code ×50, quality held | [Facebook](11-facebook-continuous-deployment.md) |
| Facebook | mobile release train {% cite rossi2016mobilefb %} | 2009–2016 | weekly or biweekly trains with stabilization and soak | [Facebook](11-facebook-continuous-deployment.md) |
| Facebook | Sapienz {% cite alshahwan2018sapienz %} | Android apps | at least about 75% of reported crashes fixed | [Facebook](11-facebook-continuous-deployment.md) |
| Netflix and others | chaos engineering {% cite owotogbe2024chaos %} | review of 96 sources | six-activity experiment lifecycle | [Chaos Engineering](12-chaos-engineering.md) |

## 2. Patterns across companies

Several patterns recur. **Small changes:** a median of 24 lines at Google and 33 at Facebook {% cite sadowski2018codereview savor2016facebook %}. **One expert reviewer** rather than a group meeting, at Google, and at Microsoft the best reviewer is one who knows the file. **Developers own quality:** Facebook and OANDA had no separate test team; Google's static analysis reports into developers' own reviews. **Tooling is an investment:** about 5% of Facebook's engineers worked on deployment and test tools. **Organization beats geography:** at Microsoft, how teams were organized mattered more than where they sat.

**Example: machine learning changes the picture.** Amershi and colleagues found that Microsoft teams building AI features follow a nine-stage workflow, from model requirements through data collection, cleaning and labeling to training, evaluation, deployment and monitoring {% cite amershi2019mlse %}. Quality here depends on data as much as on code: versioning data is harder than versioning code, and models can be "entangled", so improving one model can make another worse, which ordinary module-by-module testing does not catch.

## When to use these studies — and when not

Use them to see what is possible and which practices hold up at scale, and to argue for a change with evidence. Do not copy their numbers as targets: a median of 24 lines per change is a property of Google's monorepo and tools, not a law.

## Limitations

- **Self-studies.** Most authors work at the company they study.
- **Survivorship.** Practices that worked get published; abandoned ones rarely do.

## How solid is this?

- **Where it comes from.** Nine published studies of Google, Microsoft and Facebook data and one multivocal review {% cite sadowski2018codereview memon2017testing bosu2015codereview bird2009vista kim2014refactoring amershi2019mlse savor2016facebook rossi2016mobilefb alshahwan2018sapienz owotogbe2024chaos %}.
- **What is contested.** How far large-company findings transfer to small teams; Savor and colleagues note that continuous deployment was harder for the smaller company, OANDA.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
