---
title: DSM Operations
parent: Maintainability
grand_parent: Quality Attributes
nav_order: 7
layout: default
---

# DSM Operations

**DSM operations** reorder or mark a [design structure matrix](dsm.md) to reveal its structure: partitioning finds a build order and the cycles, tearing chooses which dependencies to break, clustering groups elements into modules, and banding shows what can be done in parallel {% cite yassine2004dsm %}. Rows depend on columns here.

## 1. Partitioning

**Partitioning** reorders rows and columns so that the matrix becomes lower triangular, or block lower triangular when there are cycles {% cite yassine2004dsm %}. The algorithm is a topological sort: place first every element that depends on nothing left to place, remove it, and repeat. Elements on a cycle cannot be separated; they are placed together as one **block**, keeping their original order inside it. By hand, when no element is free, follow marks until you revisit an element: that loop is a block. When several elements are ready at the same step, any order among them is valid; the examples break ties alphabetically.

## 2. Worked example: an acyclic DSM

Seven elements A to G, in alphabetical order, have 11 dependencies; 6 marks sit above the diagonal.

| | A | B | C | D | E | F | G |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **A** | . | | X | | | | |
| **B** | | . | | | | X | |
| **C** | | | . | X | | | X |
| **D** | | X | | . | | X | |
| **E** | X | | X | X | . | | X |
| **F** | | | | | | . | |
| **G** | | X | | | | | . |

F depends on nothing, so it goes first; then B (needs F), D (B, F), G (B), C (D, G), A (C) and E, which needs four others. In the order F B D G C A E every mark is below the diagonal:

| | F | B | D | G | C | A | E |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **F** | . | | | | | | |
| **B** | X | . | | | | | |
| **D** | X | X | . | | | | |
| **G** | | X | | . | | | |
| **C** | | | X | X | . | | |
| **A** | | | | | X | . | |
| **E** | | | X | X | X | X | . |

This order and the block result below were computed with a short script (Tarjan's strongly connected components) and checked by hand.

## 3. Cycles and tearing

Now take seven elements 1 to 7 where 1 → 6, 6 → 1, 6 → 5, 5 → 6 and 5 → 1 form one cycle, 2 → 3 and 3 → 2 form another, 4 → 5, 2 → 4, 3 → 4 and 7 → 2, 3, 4. Partitioning gives the order 1 5 6 4 2 3 7: two blocks, {1, 5, 6} and {2, 3}, and the single elements 4 and 7. Inside the blocks, three marks stay above the diagonal (1 → 6, 5 → 6, 2 → 3); their count cannot reach zero by reordering, only by tearing.

| | 1 | 5 | 6 | 4 | 2 | 3 | 7 |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **1** | . | | X | | | | |
| **5** | X | . | X | | | | |
| **6** | X | X | . | | | | |
| **4** | | X | | . | | | |
| **2** | | | | X | . | X | |
| **3** | | | | X | X | . | |
| **7** | | | | X | X | X | . |

**Tearing** picks the marks to remove so that re-partitioning leaves no mark above the diagonal {% cite yassine2004dsm %}. Removing 6 → 1, 6 → 5 and 3 → 2 is enough: the order becomes 6 1 5 4 3 2 7 with no marks above the diagonal. In code a tear is a dependency to break, for example by inverting it behind an interface. The tear set is a choice: tearing 1 → 6, 5 → 6 and 2 → 3 instead also works, giving the order 1 5 4 2 3 6 7. A default rule: tear as few marks as possible, and among equal choices tear the dependency that violates the intended layering or is cheapest to put behind an interface.

## 4. Clustering and banding

**Clustering** reorders the matrix into clusters, also called chunks, that are mutually exclusive or minimally interacting {% cite yassine2004dsm %}. Unlike partitioning, it does not need cycles: it groups elements with many dependencies among them, so that most marks fall in blocks on the diagonal. MacCormack et al. use clustering to compute the **clustered cost**, which charges dependencies across clusters more than those inside one {% cite maccormack2006exploring %}. **Banding** adds alternating light and dark bands to show elements that do not depend on each other and so can be worked on in parallel {% cite yassine2004dsm %}. In the acyclic example the bands are F, then B, then D and G together (neither needs the other), then C, A and E, one band each; D and G could be built by two teams at once. Clustering and clustered cost need a search algorithm and are computed by tools, not by hand.

## When to use it — and when not

Partition to find cycles and a safe build order; tear to choose which cycle to break; cluster to propose modules. Do not accept a clustering as the architecture: algorithms differ, and MacCormack et al. set buses aside before clustering so that shared libraries do not pull everything into one cluster {% cite maccormack2006exploring %}.

## Limitations

The operations come from project planning {% cite yassine2004dsm %}. In software, removing a mark means changing code, so the cheapest tear on paper may cost most.

## How solid is this?

- **Where it comes from.** The DSM method tutorial by Yassine {% cite yassine2004dsm %} and its use on software by MacCormack et al. {% cite maccormack2006exploring %}.
- **What is contested.** Clustering: there is no single correct clustering, so results depend on the algorithm and its cost function.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
