# SQRBOK Page Standard

**What this site is.** SQRBOK accompanies the SQR (bachelor) and QAM (master) lectures. A page
gives a student **brief extra information** around what was taught — enough to understand the idea
without the lecturer in the room, and not one word more. It is not a textbook and not a literature
review, but it is **built from one**: every area has a literature review behind it (§5c).

Target: a method page is read in **two to four minutes**, the evening after the lecture.

Adapted from the MSDBOK page standard. Run the checks with:

```bash
python scripts/check_pages.py            # budgets and style caps (add a path to check one area)
python scripts/check_citations.py        # every {% cite %} key resolves
bundle exec jekyll build                 # then read the page in the rendered HTML
```

---

## 1. Page types and budgets

| Type | Body words | Purpose |
|---|---|---|
| **Hub / index** | 100–250 | Orientation and links only. No teaching content |
| **Topic hub** | 250–500 | One paragraph of framing + a comparison table + links to detail pages |
| **Method / concept** | **400–800** (aim ~600) | The default page. One method, one page |
| **Deep dive** | ≤1,200 | Evidence-carrying. **Must be declared** — see below |
| — | **>1,500 → split** | Hard cap. See §2 |

Three further types exist for **Materials** only. They accompany a lecture rather than explain a
method, and all three **must be declared** in the front matter (`page_type:`).

| Type | `page_type` | Body words | Purpose |
|---|---|---|---|
| **Study notes** | `study-notes` | 800–2,200 | One lecture summarised for revision (SN). Prose; the style caps in §4 apply |
| **Question set** | `question-set` | 200–2,200 | Revision questions (RQ). Lists are the content, so the bullet, example and lead-in rules do not apply |
| **Case material** | `case` | 300–1,600 | A case description or assignment brief |

Body words exclude front matter, code fences, the References block, Acknowledgments and the
Disclaimer.

**Claiming the deep-dive budget is a deliberate act.** Put `page_type: deep-dive` in the front
matter with a short comment saying why. **Prefer splitting to declaring.** If a page is over budget
because it covers two topics, it is two pages (§2).

---

## 2. Long topics: summary page + one page per method

**Do not put eight methods on one page.** When a topic covers several comparable methods, use
hub-and-spoke: an `index.md` topic hub with a comparison table (*What it finds · Best fit · Main
cost · Where to read more*) and one 400–800-word page per method. **Each spoke is self-contained**:
a reader arriving from a search engine must not have to visit the hub first.

Splitting is preferred to cutting: the material is usually fine, it is the packaging that fails.

---

## 3. Required elements — a method page

1. **A one-sentence definition in the first 40 words.** Before any history, caveat or list.
2. **When to use it — and when not.** Two short lists, or two sentences.
3. **At least one concrete example, in software.** A worked instance with real nouns: a function, a
   test suite, a defect count, a coverage number. A page without one is incomplete.
4. **At least one limitation or pitfall.** What the method does *not* do, or where it misleads.
5. **A source.** A `{% cite %}` from the area's review (§5c). If nothing is held, say so plainly.
6. **The standard footer** (§6).

---

## 4. Style caps

| Rule | Why |
|---|---|
| **Bullets ≤60% of body words** | A wall of bullets is notes, not explanation |
| **Every bullet list needs a lead-in sentence** | Say what the list *is* before listing it |
| **A bullet is a full clause** | "Branch coverage" is a label; "Branch coverage subsumes statement coverage" is information |
| **≤2 callouts** per page | When everything is flagged, nothing is |
| **Heading depth ≤ H3** | H4 means the page should have been split |
| **Exactly one H1**, matching `title` | The sidebar and search use it |

---

## 5. Evidence goes at the bottom, not in the flow

Teaching text stays lean and confident. Everything about *how well established* the material is —
sample sizes, contested figures, results that did not replicate — goes into one section,
second-to-last, with the same heading everywhere:

```markdown
## How solid is this?

- **Where it comes from.** …
- **What is contested.** …
- **What we do not hold.** …
```

**Inline exception:** where a specific number on the page would mislead if quoted alone, flag it in
one short sentence where it appears and put the detail below.

## 5a. No course mechanics on a handbook page

A handbook page must not mention **assignments, labs, quizzes, lecture codes or dates**. That
belongs in the slides, Moodle and the study notes. **The test:** would the sentence make sense to a
practitioner who found the page through a search engine?

## 5b. A page that is mostly pointers is not a page

If most of a page's substance is *"see the X page"*, fold its one finding into the page that owns
the source, and delete it.

## 5c. Built from the literature review

Each area has a review in `sqr_lit_review/review/[domain]/[topic]/`: `insights.md` (cross-paper
themes), `summaries/` (one per paper) and `references.bib`.

- **Write from the review, not from memory or the old page.** Read `insights.md` before rewriting
  an area; every claim that has a reviewed source cites it.
- **The review's best findings belong on the site.** A key insight that appears in no page is a
  gap — usually in *How solid is this?* or as the page's example.
- **Numbers are checked against the summary** (and the paper when the summary is unclear). A
  figure that cannot be traced is removed or flagged, not kept "because the slide had it".
- New keys go into `_bibliography/references.bib` only — never a local `references.bib`.

---

## 5d. Figures

- **Rebuild, do not screenshot.** A diagram from a paper or a slide is redrawn.
- **Diagrams are draw.io, designed by hand.** Use real shapes (actors, documents, tables,
  containers), the site palette (green `#019546` / `#2D6E2A`, card `#f0f8f0`, highlight `#fff3cd`,
  defect red `#b42318`), Tahoma, 14–18 px labels, and colour that carries meaning. An inline
  ` ```mermaid ` block is a draft only: replace it when the page is rewritten.
- **Charts are Vega-Lite**: an inline ` ```vega-lite ` block (set `vegalite: true` in the front
  matter) or a `name.vl.json` rendered to SVG.
- **draw.io files:** keep the editable `name.drawio` beside `name.svg` in the page's folder, and
  export with the diagram XML embedded so the SVG reopens in draw.io:
  `drawio -x -f svg -e -b 10 --embed-svg-fonts false -o name.svg name.drawio`.
- **A rebuilt figure keeps the source's structure and labels** so a reader of the paper recognises
  it, and is cited in its caption.
- **Name files for what they show** (`subsumption-hierarchy.svg`), never `image-1.png`.
- **Caption pattern:** a blank line, `![alt text](file.svg)`, then `_Caption._ {% cite key %}`.
- Photographs and screenshots of real tools stay raster (`.png`/`.jpg`) and are captioned.

---

## 6. Structure and footer

```markdown
---
parent: [Section]
title: [Page Title]
nav_order: [N]
layout: default
---

# [Page Title]

[One-sentence definition. Then the body — numbered H2 sections per method-template.md.]

## How solid is this?

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified
all facts and claims. In case of an error, feel free to file an issue.
```

- **Titles are unique across the site.** just-the-docs attaches children by the parent's *title*;
  two pages titled "Testing" put one page's children under the other. Prefer a qualified title
  ("Performance Testing") to `grand_parent`.
- **Sibling `nav_order` values are unique.**
- **References always use `{% bibliography --cited %}`** — never a hand-numbered list.
  jekyll-scholar fails *silently* on an unknown key, so run `check_citations.py`.

---

## 7. Study notes (SN)

A study note is **not** a lecture note. It is written for the student, follows what the lecture
argued **in the order it argued it**, and links to the handbook page that carries the detail and the
sources — it does not reproduce them. Lecture notes (for whoever delivers the session) stay in
`sqr_lit_review/lectures/`.

- **Front matter:** `page_type: study-notes`, title `Study Notes: [Topic]`.
- **Opening paragraph** (no "Purpose" heading): what the lecture answers, in two or three sentences.
- **Numbered H2 sections, each stating a claim** ("2. Branch coverage subsumes statement coverage"),
  not a topic label.
- **Each section ends with a `→` link** to the handbook page that holds the detail:
  `→ [Basis Path](../../verif/coverage/basis-path.md)`.
- **Closing section `Where this goes next`**: how the topic connects to the next lecture and the lab.
- No exam tips, practice questions or `<details>` blocks — those belong to the RQ page, which links
  back to its SN. No delivery instructions or build history.
- 800–2,200 words.

---

## 8. Student review gate

Every page or study note **rewritten to this standard** passes a student review before it ships.

- **Who reads:** a fresh reviewer who knows only what the earlier lectures taught (state the
  persona: *knows / does not know / will do next*), reading the **rendered** page, not the source.
- **What they do:** recompute every number, apply every rule to every example, and separate
  **blocking** doubts (cannot follow, reproduce or apply) from minor ones.
- **Passing:** after the last fix, a *fresh* reviewer reports **no blocking doubts**. Three to five
  rounds is normal. A doubt that keeps returning from different sides is closed as a stated
  judgement, not with ever-sharper rules.
- **Log** every round in `sqr_lit_review/review/[domain]/[topic]/status.md`: round, page, doubt,
  resolution.
- **Cross-check with a second model.** Once the Claude reviewers pass an area, run
  `sqr_lit_review/scripts/gemini_review.sh <base-url> <page> <name> "<knows>" "<topic>"` on each
  page. It sends Gemini 3.1 Pro one continuous screenshot of the page, with no print page breaks,
  and the same brief the Claude reviewers get. Treat its output as a lead: verify every finding
  against the source before acting. In the first trial it found real gaps that the Claude
  reviewers had missed, but it also misread numbers.

## 8a. Content-preservation check

A rewrite must not silently drop correct material. Before committing an area, compare it with
the pre-revision version (`git show <base>:<path>`). List each concept, model, worked example,
sourced finding, figure and tool in the old pages. For each one, record where it now lives, or
why it was removed (wrong or unverifiable, logged in `status.md`). Restore anything correct and
useful that has no new home: put it in the existing page when the budget allows, or on a new page
when a cluster is shared by several areas (for example, a case library).

---

## 9. Checklist before committing a page

- [ ] Definition in the first 40 words
- [ ] At least one concrete software example
- [ ] At least one limitation or pitfall
- [ ] Claims and numbers traced to the area's review (§5c)
- [ ] Word count inside the budget for its type
- [ ] Bullets under 60%; every list has a lead-in
- [ ] No H4; at most two callouts; exactly one H1
- [ ] Evidence in *How solid is this?*, not scattered inline
- [ ] Figures rebuilt, named and captioned (§5d)
- [ ] `python scripts/check_pages.py` and `python scripts/check_citations.py` clean
- [ ] `bundle exec jekyll build` succeeds, and the page was **read in the rendered HTML**
- [ ] Student review: no blocking doubts (§8); Gemini cross-check verified
- [ ] Content-preservation check against the pre-revision pages (§8a)
