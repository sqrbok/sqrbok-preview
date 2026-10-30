---
title: "Worked Case: Measuring a Team's Cost of Quality"
parent: Cost of Quality
nav_order: 9
layout: default
page_type: worked-case
vegalite: true
---

# Worked Case: Measuring a Team's Cost of Quality

This worked case collects cost-of-quality (CoQ) data for one team over three releases, from the time log and the bug tracker to a table, a chart, a defect removal efficiency per release and a recommendation to management. The categories are explained on [Cost of Software Quality](01-cosq-foundations.md), the matrix and DRE on [Defect Origin and Detection](03-defect-classification.md).

**Example team.** Four people (a tech lead, two developers and a tester) run a room-booking API for a small hotel chain, the service of the [quality planning worked case](../02-plan/worked-case.md). Each release takes six weeks: 4 people × 6 weeks × 35 hours = 840 logged hours. Release 1.0 ran the plan's automated gates but under-applied its review rule: under deadline pressure, class B changes were approved without the inspection checklist (14 review hours in six weeks), and stories were never reviewed with the product owner. Its other prevention hours were the plan's week-1 items: the CI setup (8 hours, tech lead) and one 90-minute training session for all four (6 hours). It shipped a refund rule that guests met in production. The team wants to know what quality costs it and whether enforcing the inspections, and adding a story review, paid off. _(Constructed example: the logs below were generated for this page, and the script was run on them with Python 3.12.9.)_

## 1. Set up three data sources before the release

CoQ cannot be reconstructed afterwards from memory with any accuracy, so the team decided the inputs before release 1.0. Three sources are enough:

- **Time reports with a category tag.** Every logged hour gets one tag in the time-tracking tool (Jira work logs, a timesheet, or one line per day in a shared sheet). Sessions that mix work are split to the half hour.
- **The bug tracker.** Every defect is a ticket with a severity, and every hour spent fixing it is logged against the ticket key.
- **Two classification fields per defect:** **origin** (story, design or code) and **where found** (review, CI test, staging, production). In GitHub Issues they can be labels such as `origin:story` and `found:production`; in Jira, two custom fields.

**Decision point: who decides whether fix time is internal or external failure?** Criterion: whether a user met the failure, as the foundations page defines it. A developer does not choose; the fix hours inherit the category from the ticket's *where found* field: `production` means a user met the failure, so external; anything else is internal. This removes the most common booking dispute.

## 2. The activity-to-category checklist

The artifact to copy: one row per tag, following the categories of Houston and Keats and this handbook's booking convention (reviews are prevention, the rework they cause is internal failure, automated checks of the product are appraisal) {% cite houston1999cost knox1993modeling %}.

| Tag | What it covers | Category |
|---|---|---|
| `review` | code review, inspection of class B changes, story review with the product owner | prevention |
| `training` | courses, workshops on testing or the domain | prevention |
| `process` | writing checklists, the definition of done, setting up quality tools, collecting metrics | prevention |
| `test` | writing and running tests, test data and environments, triaging static-analysis findings | appraisal |
| `fix` (with a ticket key) | debugging, fixing and re-testing a defect, including rework after a review finding | internal failure, or external failure if the ticket says `found:production` |
| `support` | answering users about a failure, incident handling, rollback, notifying customers | external failure |
| `dev` | features, refactoring not caused by a failure, meetings | not CoQ |

Three rules keep the trend meaningful: the same table is used for every release; production defects are counted for a fixed **30 days after each release**, later ones reported separately, because the field count is never final; and the `release` column of a `fix` row is the release that shipped the defect, so a 1.0 defect fixed during 1.1 counts against 1.0. A release's hours are therefore its six weeks plus the fixes and support on its defects in the 30 days after shipping.

## 3. Export the raw data

The time log and the tracker export are two CSV files. Their first lines:

```text
timelog.csv                              defects.csv
release,person,tag,hours,ticket          key,release,severity,origin,where_found
1.0,ana,review,4,                        BOOK-101,1.0,2,story,ci-test
1.0,ben,review,4,                        BOOK-102,1.0,3,story,staging
1.0,ana,fix,4,BOOK-101                   BOOK-103,1.0,2,story,staging
```

## 4. Compute the CoQ table

The core of a short script applies the checklist (the printing is omitted). Fix hours look up their ticket; everything else maps by tag:

```python
"""Cost of quality per release from a tagged time log and a defect export."""
import csv
from collections import defaultdict

TAG = {"review": "prevention", "training": "prevention",
       "process": "prevention", "test": "appraisal",
       "support": "external failure", "dev": None}

where = {r["key"]: r["where_found"] for r in csv.DictReader(open("defects.csv"))}
cost = defaultdict(lambda: defaultdict(float))
for row in csv.DictReader(open("timelog.csv")):
    tag = row["tag"]
    if tag == "fix":   # fix time inherits its category from the defect
        cat = ("external failure" if where[row["ticket"]] == "production"
               else "internal failure")
    else:
        cat = TAG[tag]
    cost[row["release"]][cat or "not CoQ"] += float(row["hours"])
```

Its totals, in hours:

| Release | Prevention | Appraisal | Internal failure | External failure | CoQ | Share of release hours (840) |
|---|---:|---:|---:|---:|---:|---:|
| 1.0 | 28 | 160 | 90 | 75 | 353 | 42.0% |
| 1.1 | 60 | 170 | 75 | 30 | 335 | 39.9% |
| 1.2 | 65 | 165 | 60 | 15 | 305 | 36.3% |

Appraisal is close to the plan's estimate: in 1.0 the tester logged 110 of the 128 hours planned for API and system tests, and the developers' unit tests, booked as development in the estimate but as appraisal here, add 50.

```vega-lite
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": 360, "height": 220,
  "data": {"values": [
    {"r": "1.0", "c": "Prevention", "o": 1, "h": 28}, {"r": "1.0", "c": "Appraisal", "o": 2, "h": 160},
    {"r": "1.0", "c": "Internal failure", "o": 3, "h": 90}, {"r": "1.0", "c": "External failure", "o": 4, "h": 75},
    {"r": "1.1", "c": "Prevention", "o": 1, "h": 60}, {"r": "1.1", "c": "Appraisal", "o": 2, "h": 170},
    {"r": "1.1", "c": "Internal failure", "o": 3, "h": 75}, {"r": "1.1", "c": "External failure", "o": 4, "h": 30},
    {"r": "1.2", "c": "Prevention", "o": 1, "h": 65}, {"r": "1.2", "c": "Appraisal", "o": 2, "h": 165},
    {"r": "1.2", "c": "Internal failure", "o": 3, "h": 60}, {"r": "1.2", "c": "External failure", "o": 4, "h": 15}]},
  "mark": {"type": "bar", "stroke": "#ffffff", "strokeWidth": 1},
  "encoding": {
    "x": {"field": "r", "type": "ordinal", "title": "Release", "axis": {"labelAngle": 0}},
    "y": {"field": "h", "type": "quantitative", "title": "Hours", "stack": "zero"},
    "order": {"field": "o"},
    "color": {"field": "c", "type": "nominal", "title": null,
      "sort": ["Prevention", "Appraisal", "Internal failure", "External failure"],
      "scale": {"domain": ["Prevention", "Appraisal", "Internal failure", "External failure"],
                "range": ["#019546", "#1f5fa8", "#c99700", "#b42318"]}},
    "tooltip": [{"field": "r", "title": "Release"}, {"field": "c", "title": "Category"}, {"field": "h", "title": "Hours"}]},
  "config": {"font": "Tahoma, sans-serif", "axis": {"labelFontSize": 13, "titleFontSize": 13}, "legend": {"labelFontSize": 13}, "view": {"stroke": null}}
}
```

_CoQ per release, stacked by category, from the constructed log. Total CoQ falls slowly; the mix shifts from failure to prevention._

**Reading it.** Each release's hours, its six weeks plus the 30-day fixes on its defects, total 840 in this constructed log; a real log would not come out even, and the share would use its actual total. Total CoQ fell only from 353 to 305 hours, but failure costs fell from 165 to 75 hours (90 + 75, then 60 + 15) while prevention rose from 28 to 65 and appraisal stayed between 160 and 170.

**Decision point: is the trend real?** Criterion: same checklist, same 30-day production window and comparable release size in all three releases. All three hold here (840 hours each). Had the team booked reviews as appraisal in 1.2 only, prevention would have fallen to 10 hours and the chart would show an improvement collapsing that never happened.

## 5. Add the defect view: matrix and DRE

Hours say what quality cost; the tracker says where defects came from and where they were caught. The origin / where-found matrices for releases 1.0 and 1.2:

| Release 1.0: origin \ found in | Review | CI test | Staging | Production | Total |
|---|---:|---:|---:|---:|---:|
| Story | 0 | 1 | 2 | 4 | 7 |
| Design | 1 | 3 | 1 | 2 | 7 |
| Code | 3 | 14 | 3 | 2 | 22 |
| **Total** | **4** | **18** | **6** | **8** | **36** |

| Release 1.2: origin \ found in | Review | CI test | Staging | Production | Total |
|---|---:|---:|---:|---:|---:|
| Story | 3 | 1 | 0 | 0 | 4 |
| Design | 4 | 1 | 1 | 1 | 7 |
| Code | 9 | 10 | 2 | 1 | 22 |
| **Total** | **16** | **12** | **3** | **2** | **33** |

Six of the eight production defects came from stories or design, mostly misread refund rules, while the CI tests mainly caught code defects. That was the evidence behind the two changes made for release 1.1: a story review with the product owner before work starts, and enforcing the plan's inspection of every class B change.

The overall **defect removal efficiency** before release is the share of all counted defects found before production {% cite kan2002metrics %}:

| Release | Found before release | Found in production (30 days) | DRE |
|---|---:|---:|---:|
| 1.0 | 28 | 8 | 28 / 36 = 77.8% |
| 1.1 | 34 | 4 | 34 / 38 = 89.5% |
| 1.2 | 31 | 2 | 31 / 33 = 93.9% |

In release 1.2 the review column held 16 of 33 defects, against 4 of 36 in 1.0, and no story defect reached production.

## 6. Present the recommendation

Management gets one page: the stacked bar, the DRE row and a recommendation stated in their unit. For this team:

> **Keep the story review and the class B inspections.** Since release 1.0 they cost about 35 extra prevention hours a release and coincided with 90 fewer failure hours and a DRE that rose from 78% to 94%. **Next:** internal failure (60 hours) is now the largest failure cost, and 22 of the 33 defects in 1.2 originated in code; run a causal analysis on the code defects caught by CI tests before adding more testing.

**Decision point: recommend or wait?** Criterion: compared with release 1.0, the failure hours saved exceed the conformance hours (prevention plus appraisal) added, in two consecutive releases. Appraisal is counted because a practice can shift cost into testing; here it hardly moved. Release 1.1 saved 165 − 105 = 60 failure hours for (60 − 28) + (170 − 160) = 42 added, and 1.2 saved 90 for 37 + 5 = 42. The criterion holds, so the team recommends. Three releases do not prove cause: the same period also saw the team learn the domain, which is why the recommendation says "coincided with".

## Common mistakes

1. **Debugging booked as testing.** It hides internal failure inside appraisal. Spot it: appraisal grows exactly when defects rise, and `fix` hours are near zero.
2. **Changing the booking rule between releases.** A rule switch looks like a process change. Spot it: the tag table has no version or date.
3. **Fix hours without a ticket key.** They cannot be split into internal and external failure. Spot it: `fix` rows with an empty ticket column; reject them at entry.
4. **Comparing DRE at different ages.** A release measured one week after shipping looks better than one measured after a quarter. Spot it: no stated production window.
5. **Quoting a benchmark instead of a trend.** One team's percentages compared with a published "target" say little; its own trend says more. Spot it: a single release on the slide.

## How solid is this?

- **Where it comes from.** The categories and their typical costs from Houston and Keats and Knox, the booking convention from this handbook's [foundations page](01-cosq-foundations.md), the matrix and DRE from Kan {% cite houston1999cost knox1993modeling kan2002metrics %}. The three data sources and the advice to start with rework hours, escapes and origin follow the lecture's practitioner guidance.
- **What is contested.** Whether a small team should log time by category at all; the overhead is a few seconds per entry, but the numbers are only as honest as the logging.
- **What we do not hold.** Published CoQ data from small agile teams with which to compare these constructed figures.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
