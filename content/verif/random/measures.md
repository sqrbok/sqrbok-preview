---
title: E-measure and P-measure
parent: Random Testing
nav_order: 11
layout: default
---

# E-measure and P-measure

**The P-measure of a test strategy is the probability that its tests reveal at least one failure; the E-measure is the expected number of failures they reveal.** The analytical comparisons of [random and partition testing](partition.md) are stated in these two measures.

## 1. The model

The input domain is split into *k* disjoint subdomains. Subdomain *i* has a probability *p<sub>i</sub>* that a random input falls into it (its share of the domain, if every input is equally likely) and a failure rate *θ<sub>i</sub>*, the share of its inputs that fail. The program's overall failure rate is *θ* = Σ *p<sub>i</sub>θ<sub>i</sub>*. Random testing draws *N* tests independently from the whole domain; partition testing draws *n<sub>i</sub>* tests from subdomain *i*, with Σ *n<sub>i</sub>* = *N* {% cite ntafos2001comparisons %}:

$$
P_r = 1 - (1-\theta)^N \qquad P_p = 1 - \prod_{i=1}^{k} (1-\theta_i)^{n_i}
$$

$$
E_r = N\theta \qquad E_p = \sum_{i=1}^{k} n_i\theta_i
$$

## 2. When partition testing cannot lose

Three results, under that model:

- **Equal subdomains, equal tests.** Weyuker and Jeng showed that if all subdomains have the same size and each gets the same number of tests, partition testing has an equal or better chance of finding at least one failure: *P<sub>p</sub>* ≥ *P<sub>r</sub>* {% cite weyuker1991analyzing ntafos2001comparisons %}.
- **Proportional allocation.** Chen and Yu generalised this: whenever *n<sub>i</sub>* is proportional to *p<sub>i</sub>*, *P<sub>p</sub>* ≥ *P<sub>r</sub>*, whatever the failure rates {% cite chen1994relationship ntafos2001comparisons %}.
- **The E-measure follows from the formulas.** With *n<sub>i</sub>* = *Np<sub>i</sub>*, *E<sub>p</sub>* = *N* Σ *p<sub>i</sub>θ<sub>i</sub>* = *Nθ* = *E<sub>r</sub>*: proportional partition testing finds exactly as many failures on average as random testing. If all *θ<sub>i</sub>* are equal, *E<sub>p</sub>* = *E<sub>r</sub>* for any allocation. Otherwise partitioning gains on the E-measure only by testing more where failures are. Chen and Yu analysed the E-measure in detail {% cite chen1996expected %}.

## 3. Worked example

(Constructed example.) A domain of 1,000 equally likely inputs has two subdomains, 100 and 900 inputs, and 10 failing inputs, so *θ* = 0.01. Ten tests are run. Case A puts all failures in the small subdomain, case B in the large one. Computed in Python from the formulas (script and output below), rounded:

| Case | Allocation | *P<sub>r</sub>* | *E<sub>r</sub>* | *P<sub>p</sub>* | *E<sub>p</sub>* |
|---|---|---|---|---|---|
| A: failures in the small subdomain | equal, 5 + 5 | 0.096 | 0.100 | **0.410** | **0.500** |
| A | proportional, 1 + 9 | 0.096 | 0.100 | 0.100 | 0.100 |
| B: failures in the large subdomain | equal, 5 + 5 | 0.096 | 0.100 | **0.054** | **0.056** |
| B | proportional, 1 + 9 | 0.096 | 0.100 | 0.096 | 0.100 |

Equal allocation is a bet: it raises the chance of a find more than fourfold when the small subdomain holds the faults, and almost halves it when it does not. Proportional allocation never loses, by Chen and Yu's result in section 2: in case B, *P<sub>p</sub>* = 0.09567 against *P<sub>r</sub>* = 0.09562. But it never gains much either, and its *E<sub>p</sub>* equals *E<sub>r</sub>* in both cases.

```python
# Two subdomains of a 1,000-input domain, 10 tests in total, uniform use.
def measures(sizes, fails, alloc):
    N = sum(alloc); D = sum(sizes)
    theta = sum(fails) / D                      # overall failure rate
    th = [f / s for f, s in zip(fails, sizes)]  # failure rate per subdomain
    Pr, Er = 1 - (1 - theta) ** N, N * theta
    prod = 1.0
    for t, n in zip(th, alloc):
        prod *= (1 - t) ** n
    Pp, Ep = 1 - prod, sum(n * t for n, t in zip(alloc, th))
    return Pr, Er, Pp, Ep

sizes = [100, 900]
for case, fails in [("A: failures in the small subdomain", [10, 0]),
                    ("B: failures in the large subdomain", [0, 10])]:
    for name, alloc in [("equal 5/5", [5, 5]), ("proportional 1/9", [1, 9])]:
        Pr, Er, Pp, Ep = measures(sizes, fails, alloc)
        print(f"{case:36} {name:17} Pr={Pr:.3f} Er={Er:.3f}  Pp={Pp:.3f} Ep={Ep:.3f}")
```

```text
$ python3 measures.py
A: failures in the small subdomain   equal 5/5         Pr=0.096 Er=0.100  Pp=0.410 Ep=0.500
A: failures in the small subdomain   proportional 1/9  Pr=0.096 Er=0.100  Pp=0.100 Ep=0.100
B: failures in the large subdomain   equal 5/5         Pr=0.096 Er=0.100  Pp=0.054 Ep=0.056
B: failures in the large subdomain   proportional 1/9  Pr=0.096 Er=0.100  Pp=0.096 Ep=0.100
```

**Where the inputs come from.** The *p<sub>i</sub>* come from the [operational profile](../operational-profile/index.md) or from the specification's input ranges. The *θ<sub>i</sub>* are unknown before testing; on a real project they can only be guessed from defect history per module; the measures guide reasoning, not release decisions.

## When to use it — and when not

- **Use the measures** to argue how to split a test budget across subdomains, and to see why a partition that isolates failure-prone inputs pays off.
- **Do not use proportional allocation as a goal.** Ntafos showed that a subdomain hit once in a million runs would need 1,000,001 tests for a true proportional allocation, and that the guaranteed margin over random testing shrinks to insignificance {% cite ntafos2001comparisons %}.

## Limitations

The model assumes disjoint subdomains, uniform failure rates inside each one, independent draws and the same *N* for both strategies. Real partitions overlap, and random tests are usually far cheaper per test, which the measures ignore.

## How solid is this?

- **Where it comes from.** The formulas and the summary of the Weyuker–Jeng and Chen–Yu conditions follow Ntafos's review of the comparisons {% cite ntafos2001comparisons %}; the E-measure identities are derived on this page from the formulas.
- **What we do not hold.** The full texts of Weyuker and Jeng and of Chen and Yu; their results are stated here as Ntafos reports them.

---

### References

{% bibliography --cited %}

---

{: .highlight }
**Disclaimer:** AI is used for text summarization, polishing and explaining. Authors have verified all facts and claims. In case of an error, feel free to file an issue.
