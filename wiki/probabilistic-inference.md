---
title: Probabilistic Inference — Marginalization, Conditioning, and Queries on a Joint Distribution
---

# Probabilistic Inference

**Probabilistic inference** is answering questions about a joint distribution: given **evidence** (some variables observed at particular values), what is the distribution of a **query** (some other variables)? Written $P(\text{query} \mid \text{evidence})$ ([lecture 12](12-bayesian-networks-i.md), [≈21:03](../raw/transcripts/12-bayesian-networks-i.md)–[≈23:21](../raw/transcripts/12-bayesian-networks-i.md)). The analogy the course offers: the joint distribution is a **SQL database**, the source of truth with a probability for every state of the world, and probabilistic inference is running **queries** against it ([≈21:49](../raw/transcripts/12-bayesian-networks-i.md)). A [Bayesian network](bayesian-networks.md) is a convenient way to define the database; inference is how you use it.

Notation: uppercase letters are random variables and lowercase letters their values. $P$ is a probability derived from the joint distribution.

## The Two Operations

Everything is built from two operations on a joint distribution, reviewed in lecture 12 on two binary variables, sunshine $S$ and rain $R$, with joint distribution $P(S = 0, R = 0) = 0.20$, $P(S = 0, R = 1) = 0.08$, $P(S = 1, R = 0) = 0.70$ and $P(S = 1, R = 1) = 0.02$ ([≈5:33](../raw/transcripts/12-bayesian-networks-i.md)–[≈8:38](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

**Marginalization** ignores a variable by collapsing all assignments that differ only in it and adding their probabilities ([≈9:26](../raw/transcripts/12-bayesian-networks-i.md), [≈10:11](../raw/transcripts/12-bayesian-networks-i.md)):

$$P(S = s) = \sum_r P(S = s, R = r)$$

so $P(S = 0) = 0.20 + 0.08 = 0.28$ and $P(S = 1) = 0.72$.

**Conditioning** on evidence, such as $R = 1$, is three steps ([≈13:17](../raw/transcripts/12-bayesian-networks-i.md)–[≈15:39](../raw/transcripts/12-bayesian-networks-i.md)):

1. **Select** the assignments consistent with the evidence: $P(S = 0, R = 1) = 0.08$, $P(S = 1, R = 1) = 0.02$.
2. **Compute the probability of the evidence** by summing them: $P(R = 1) = 0.1$.
3. **Divide** by it: $P(S = s \mid R = 1) = P(S = s, R = 1) / P(R = 1)$, giving $0.8$ and $0.2$.

The division renormalizes: the selected probabilities are small because the evidence itself is unlikely, and dividing scales them up so they sum to one. A conditional distribution sums to one over the variables to the **left** of the bar, not over those to the right ([≈15:39](../raw/transcripts/12-bayesian-networks-i.md), [≈39:44](../raw/transcripts/12-bayesian-networks-i.md)).

## A General Query

In general, given a joint distribution $P(X_1, \ldots, X_n)$, evidence $E = e$ for a subset $E \subseteq X$, and a query $Q \subseteq X$, the output is the table $P(Q \mid E = e)$, a probability for every assignment to $Q$ ([≈59:12](../raw/transcripts/12-bayesian-networks-i.md), [≈59:58](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). **Every variable that is in neither the evidence nor the query is marginalized out.** With sunshine $S$, rain $R$, traffic $T$ and autumn $A$, "what is the probability of rain given traffic in autumn?" is $P(R \mid T = 1, A = 1)$, with $S$ summed out ([≈21:03](../raw/transcripts/12-bayesian-networks-i.md)–[≈23:21](../raw/transcripts/12-bayesian-networks-i.md)).

The recipe is the same every time ([≈46:42](../raw/transcripts/12-bayesian-networks-i.md)): select the rows that match the evidence (summing out the variables you do not care about), compute the probability of the evidence, then divide.

## Inference as einsum

"Probability tables are just tensors," so the course writes every step with [einops](einops.md)' `einsum`; "all the laws of probability are essentially all covered by einops" ([≈10:57](../raw/transcripts/12-bayesian-networks-i.md)). An axis left off the output is summed over, which is marginalization. Evidence is an **indicator tensor**, `[0, 1]` for "this variable equals 1": multiplying it in zeroes out every row that disagrees with the evidence, which is selection ([≈16:24](../raw/transcripts/12-bayesian-networks-i.md), [≈17:11](../raw/transcripts/12-bayesian-networks-i.md)). One einsum can therefore select and marginalize at once, "which is why einsum is so powerful" ([≈38:12](../raw/transcripts/12-bayesian-networks-i.md)). For burglary given alarm in the alarm network ([slides](../raw/slides/12-bayesian-networks-i.md)):

```python
a1 = np.array([0, 1])                               # evidence A = 1
P_BA1 = einsum(P_BEA, a1, "b e a, a -> b")          # select A = 1, sum out E: P(B = b, A = 1)
P_A1 = einsum(P_BA1, "b ->")                        # probability of evidence: 0.0975
P_B_given_A1 = P_BA1 / P_A1                         # divide: P(B = 1 | A = 1) ≈ 0.513
```

The last step is a plain division, since the probability of the evidence is a scalar ([≈18:44](../raw/transcripts/12-bayesian-networks-i.md)). Everything in the course uses discrete variables. With continuous ones, marginalization becomes integration, and conditioning gets "dicey" because you can condition on an event of probability zero ([≈18:44](../raw/transcripts/12-bayesian-networks-i.md)).

## Worked Answers from Lecture 12

| Network | Query | Answer | Source |
| --- | --- | --- | --- |
| Alarm | $P(B = 1)$ | 0.05 | [≈37:25](../raw/transcripts/12-bayesian-networks-i.md) |
| Alarm | $P(B = 1 \mid A = 1)$ | 0.05 / 0.0975 ≈ 0.513 | [≈39:44](../raw/transcripts/12-bayesian-networks-i.md) |
| Alarm | $P(B = 1 \mid A = 1, E = 1)$ | 0.0025 / 0.05 = 0.05 | [≈41:17](../raw/transcripts/12-bayesian-networks-i.md) |
| Medical diagnosis | $P(C = 1 \mid H = 1)$ | 0.09 / 0.324 ≈ 0.278 | [≈53:48](../raw/transcripts/12-bayesian-networks-i.md) |
| Medical diagnosis | $P(C = 1 \mid H = 1, I = 1)$ | 0.0234 / 0.1764 ≈ 0.133 | [≈55:22](../raw/transcripts/12-bayesian-networks-i.md) |

The exact numbers are from the traced tables in the [slide file](../raw/slides/12-bayesian-networks-i.md). The drops in the third and fifth rows are [explaining away](explaining-away.md).

## Cost, and Approximate Inference

Writing out the whole joint table is something no one really does for a large number of variables ([≈1:04:34](../raw/transcripts/12-bayesian-networks-i.md)), and in general computing these quantities can be NP-hard ([≈57:40](../raw/transcripts/12-bayesian-networks-i.md)). Going "backwards" through a language model, from a response to the prompt that produced it, is the extreme case: "extremely computationally expensive" and "in general exponentially hard" ([≈1:03:49](../raw/transcripts/12-bayesian-networks-i.md)). Lecture 12 ends with one approximate method, [rejection sampling](rejection-sampling.md), which estimates a query from samples and is "very flexible and very slow," and promises better inference algorithms next time ([≈1:17:06](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

Lecture 13 puts the cost in numbers: with 100 binary variables the joint has $2^{100}$ assignments, "and you definitely don't want to be writing that down," so exact inference by forming the joint is exponential in the number of nodes ([≈9:23](../raw/transcripts/13-gibbs-sampling.md), [≈1:14:40](../raw/transcripts/13-gibbs-sampling.md)). It adds a second approximate method, [Gibbs sampling](gibbs-sampling.md), which starts from an assignment satisfying the evidence and resamples one variable at a time, never building the joint table. The two samplers fail in complementary ways: rare evidence is hard for rejection sampling, highly correlated variables are hard for Gibbs sampling ([lecture 13](13-gibbs-sampling.md), [≈56:44](../raw/transcripts/13-gibbs-sampling.md), [≈57:31](../raw/transcripts/13-gibbs-sampling.md)). Lecture 13 also orders the exact recipe slightly differently, as four steps on the alarm: slice the joint at the evidence, marginalize out the other variables, sum to get the probability of the evidence, and divide ([≈6:18](../raw/transcripts/13-gibbs-sampling.md)–[≈9:23](../raw/transcripts/13-gibbs-sampling.md)).

## Related Pages

- [Lecture 12](12-bayesian-networks-i.md) — the probability review and both worked networks
- [Bayesian networks](bayesian-networks.md) — how the joint distribution being queried is defined
- [Explaining away](explaining-away.md) — what the queries above reveal
- [Rejection sampling](rejection-sampling.md) — approximate inference by sampling
- [Gibbs sampling](gibbs-sampling.md) — approximate inference one variable at a time (lecture 13)
- [Conditional independence](conditional-independence.md) — which variables a query can ignore, read off the graph
- [Einops](einops.md) — the einsum notation
