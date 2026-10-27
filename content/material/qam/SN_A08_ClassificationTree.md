---
title: "Study Notes: Classification Tree Method"
parent: QAM Study Materials
grand_parent: Selected Materials
nav_order: 15
redirect_from: /content/verif/domain/SN_A08_ClassificationTree.html
layout: default
page_type: study-notes
---

# Study Notes: Classification Tree Method

The lecture asks how to design tests for an object whose inputs have many interacting aspects, without drowning in combinations or relying on intuition. Its answer is the classification tree method (CTM): a tree of aspects and classes, and a table that picks one class per aspect for each test. The [revision questions](RQ_A08_ClassificationTree.md) test this lecture. It is a master's (QAM) lecture, optional for bachelor students, who meet the method only briefly in the input domain lecture; it is not examined in their course.

## 1. CTM replaced a textual method with a picture

The category-partition method lists categories, choices and constraints as text, in its Test Specification Language (TSL), and generates every permitted combination {% cite ostrand1988category %}. CTM addresses three of its problems: TSL syntax is hard to learn, the flat list of categories hides how aspects depend on each other, and the generated lists are long and implicit. Grochtmann and Grimm drew the same information as a tree and specified tests directly in a combination table, starting from few rows and adding rows until the test is sufficient {% cite grochtmann1993classification %}. The tree shows hierarchy: an aspect that matters only for some inputs hangs below the class it depends on.

→ [Classification Tree Method](../../verif/domain/classification-tree.md)

## 2. Two kinds of inner node carry the meaning

Below the root, the test object, a **classification** splits an aspect into disjoint, complete classes ("is a kind of"), and a **composition** groups aspects that exist together ("consists of") {% cite buechner2009classification %}. Only the leaves, the **classes**, are chosen in test cases. A car consists of wheels, motor and chassis; a vehicle type is a bus, a truck or a sedan. Confusing the two is a common modelling error, and the classification step itself is [equivalence partitioning](../../verif/domain/equivalence.md): the classes under one classification must be disjoint and complete, or a test case could fall into two classes or into none. Values are first split into valid and invalid {% cite buechner2009classification %}. The lecture's rule is to put invalid values in a **separate branch** of the tree, listing concrete invalid exemplars ("small hexagon", "wrong colour"), next to the branch of valid aspects, and to test them one at a time: if they sat inside every valid classification, they would be combined with every valid class of the others and multiply the table without adding information, since processing usually stops at the first invalid input. Only when the application's logic itself combines valid and invalid inputs is mixing them worth it.

→ [Classification Tree Method](../../verif/domain/classification-tree.md)

## 3. The process is: draw the tree, fill the table, add constraints

The first decision is the test object: one function or screen gives a tree small enough to review, while a whole application gives one too large to check. The original method is detailed {% cite grochtmann1993classification %}; Buechner reduces it to drawing the tree and filling the combination table, with constraints and concrete values as optional steps, calling CTM "guidance for thinking, not replacement of thinking" {% cite buechner2009classification %}. For example, a city charges non-residents 1% of gross pay; single residents pay 1% up to 30,000, 5% from 30,001 to 50,000 and 15% above; residents with a family pay 1% up to 50,000 and 5% above, each rate applying to the whole pay. In its tree, status and gross pay hang under "resident", so a non-resident needs only one test, and the combination table has seven rows: one for non-residents and six for residents (two statuses × three pay classes). Two family rows share the 1% rate but sit in different pay classes, and both are kept: only the row inside 30,001–50,000 checks that the family limit is at 50,000 and not at 30,000. Boundary values are then added from boundary value analysis where the rate changes: 30,000/30,001 and 50,000/50,001 for single residents, 50,000/50,001 for families.

→ [Classification Tree Method](../../verif/domain/classification-tree.md)

## 4. Constraints remove impossible rows, and "avoid" works best

Real trees produce impossible combinations. For `count(array, value)`, classifications for array size (0, 1, more), occurrences (none, one, more than one) and order (sorted, unsorted) give 3 × 3 × 2 = 18 combinations, but an empty array allows only "none" and has no order, and a one-element array cannot hold more than one occurrence. Constraint handling grew from category-partition's text annotations {% cite ostrand1988category %} to Boolean dependency rules in CTE XL {% cite lehmann2000ctexl %} and numerical constraints solved with SAT {% cite kruse2012numerical %}. Grindal et al. compared four ways to handle conflicts over 3,854 generated suites: **avoid** conflicting combinations during generation, **replace** a conflicting value afterwards, merge the conflicting inputs into one **abstract parameter**, or split the model into conflict-free **sub-models**. Avoid gave the smallest suites, because it never generates a conflicting row that later has to be removed or altered; replace came second {% cite grindal2007conflicts %}.

→ [Classification Trees in Practice](../../verif/domain/classification-tree-practice.md)

## 5. A tree's quality can be measured

Chen et al. measure a tree by its effectiveness E[T]: the number of legitimate combinations, those possible in the real input domain, divided by all the combinations the tree can generate {% cite chen2000integrated %}. Duplicated subtrees waste combinations; restructuring raised this effectiveness from 0.17 to 0.40 in their bonus-program example {% cite chen1999restructuring chen2002restructuring %}. Different testers also build different trees from one specification {% cite chen2000integrated %}; a quarter of the full-time students in Yu et al.'s controlled study (section 7) named this as a disadvantage {% cite yu2004novicetesters %}.

→ [Classification Trees in Practice](../../verif/domain/classification-tree-practice.md)

## 6. Tools turned CTM into test automation

The Classification Tree Editor grew from a prototype into CTE XL, with dependency rules and generation of minimal, maximal and *n*-wise tables {% cite lehmann2000ctexl %}. The **minimality** criterion asks that every class appear in at least one test; the **maximality** criterion asks for every permitted combination. Numerical constraints checked with a SAT solver came next, still in CTE XL {% cite kruse2012numerical %}; its successor TESTONA adds a built-in constraint solver, test oracles from rules and test scripts {% cite kruse2016oracles %}. In some industrial uses, CTE XL's rules cut the theoretically possible test cases by more than 90%, in one example from 70,000 to 5,560 {% cite lehmann2000ctexl %}; that baseline is all combinations, not a suite anyone would write.

→ [Classification Trees in Practice](../../verif/domain/classification-tree-practice.md)

## 7. The evidence is promising but thin

The one controlled study taught CTM to 162 students, 104 full-time and 58 part-time, in three hours {% cite yu2004novicetesters %}. Before, most of them tested from the code; afterwards, 70% of the full-time students preferred CTM, 20% had no clear preference and 10% preferred their own methods, and the part-time students' own suites had detected on average only 55% of the faulty programs, while the suite the authors derived with CTM detected all of them; only one student suite matched it. Their main complaint, named by 60% of the full-time students, was infeasible combinations. Industrial reports, such as 263 tests for an airfield lighting system of about 2,700 modules {% cite grochtmann1993classification %}, come from the method's developers.

→ [Classification Trees in Practice](../../verif/domain/classification-tree-practice.md)

## Where this goes next

The combination table connects CTM to [combinatorial testing](../../verif/combinatorial/index.md): pairwise and *t*-way strategies decide how many rows a tree needs, which the [combinatorial testing lecture](SN_A07_Combinatorial.md) covers in depth. Boundary values for the classes come from [boundary value analysis](../../verif/domain/boundary.md).

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
