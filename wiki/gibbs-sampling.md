---
title: Gibbs Sampling — Markov Chain Monte Carlo Inference in Bayesian Networks
---

# Gibbs Sampling

**Gibbs sampling** is an approximate [probabilistic inference](probabilistic-inference.md) algorithm for [Bayesian networks](bayesian-networks.md). It keeps one full assignment of the variables that already satisfies the evidence, and repeatedly changes **one variable at a time**, sampling it from its conditional distribution given all the other variables. The estimate of $P(\text{query} \mid \text{evidence})$ is the fraction of the visited assignments in which the query takes each value ([lecture 13](13-gibbs-sampling.md), [≈16:23](../raw/transcripts/13-gibbs-sampling.md), [≈17:08](../raw/transcripts/13-gibbs-sampling.md), [≈35:47](../raw/transcripts/13-gibbs-sampling.md)). It is the course's second sampling method, introduced as a potentially faster alternative to [rejection sampling](rejection-sampling.md) ([≈0:05](../raw/transcripts/13-gibbs-sampling.md)).

Notation, as in the lecture: uppercase letters are random variables and lowercase letters their values; lowercase $p$ is a local conditional probability from the network's definition, and uppercase $P$ a probability derived from the joint distribution.

## Why Not Rejection Sampling?

Rejection sampling has one strength: every sample is independent. It has two weaknesses: every sample starts from scratch, learning nothing from the previous ones, and samples are generated without looking at the evidence, so rare evidence means almost every sample is rejected ([≈15:37](../raw/transcripts/13-gibbs-sampling.md), [≈16:23](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). Gibbs sampling turns this around. It builds the evidence into the very first assignment and never changes the evidence variables, so "there's no rejection in Gibbs sampling." The price is that consecutive samples are **not independent**: each is a small modification of the last, so ten Gibbs samples carry less information than ten independent ones, and more are needed ([≈17:55](../raw/transcripts/13-gibbs-sampling.md)).

Gibbs sampling is a special case of **Markov chain Monte Carlo** (MCMC): it produces a sequence of samples, each depending on the previous one, which is what makes the sequence a Markov chain ([≈17:08](../raw/transcripts/13-gibbs-sampling.md), [≈17:55](../raw/transcripts/13-gibbs-sampling.md)).

## The Update

To resample a variable, hold every other variable at its current value, evaluate the joint probability of each value the chosen variable could take, normalize, and draw. For the telephone network $A \to B \to C$, resampling $B$ with $A = a$ and $C = c$ fixed ([≈24:06](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

$$P(B = b \mid A = a, C = c) = \frac{P(A = a, B = b, C = c)}{\sum_{b'} P(A = a, B = b', C = c)}$$

where $b'$ ranges over the values of $B$, and each joint probability is the product of the network's local conditionals, here $p(a) \thinspace p(b \mid a) \thinspace p(c \mid b)$. Only the domain of the one variable being sampled is summed over, and the full joint table is **never built**: the algorithm only looks up the probability of the current assignment and its neighbours ([≈24:54](../raw/transcripts/13-gibbs-sampling.md)).

Changing one variable at a time is what keeps this tractable. With everything else frozen you can afford to try every value of that variable, "brute forcing that variable," and sample from the result ([≈37:20](../raw/transcripts/13-gibbs-sampling.md)).

## The Algorithm

From the lecture ([≈29:30](../raw/transcripts/13-gibbs-sampling.md)–[≈32:38](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

```python
def gibbs_sampling(init_x, vars, query, joint_prob, num_iterations):
    x = dict(init_x)                   # copy the initial assignment
    counts = defaultdict(int)          # counts of query values
    for _ in range(num_iterations):
        for var in vars:               # every non-evidence variable, round-robin
            probs = {value: joint_prob(x, var, value) for value in [0, 1]}
            probs = normalize_dict(probs)
            x[var] = sample_dict(probs)
            counts[query(x)] += 1
    return normalize_dict(counts)
```

Here `init_x` is a full assignment that satisfies the evidence, `vars` lists the variables to resample (the evidence variables are left out, since they are conditioned on and must not change), `query` extracts the value of interest from an assignment, and `joint_prob(x, var, value)` scores the assignment `x` with `var` set to `value`. `normalize_dict` divides each value by the total and `sample_dict` draws a key with those probabilities. The query is counted after every single-variable update ([≈30:16](../raw/transcripts/13-gibbs-sampling.md), [≈31:03](../raw/transcripts/13-gibbs-sampling.md)). The initialization generally does not matter too much, though for some problems it can speed up or slow down convergence ([≈23:20](../raw/transcripts/13-gibbs-sampling.md)).

On the board, the lecturer draws the run as a grid: one row per variable, one column per time step, each column a joint sample that differs from the previous one in exactly one variable, the evidence row constant throughout. The estimate is the fraction of ones in the query's row ([≈34:14](../raw/transcripts/13-gibbs-sampling.md)–[≈36:34](../raw/transcripts/13-gibbs-sampling.md)).

**Running time**: iterations × variables × domain size × the cost of one evaluation. Evaluating the full joint touches every variable, so as first written that last factor is the number of variables, `O(#iterations * #variables * |domain| * #variables)` ([≈38:08](../raw/transcripts/13-gibbs-sampling.md)). Since only the factors that involve the sampled variable affect its conditional distribution, the evaluation can be restricted to its [Markov blanket](markov-blanket.md), giving `O(#iterations * #variables * |domain| * |markov_blanket|)`. This is "an optimization," not a fix for anything fundamental, "but why not do it" ([≈47:24](../raw/transcripts/13-gibbs-sampling.md), [≈48:10](../raw/transcripts/13-gibbs-sampling.md)).

## Results from the Lecture

All from the execution trace in the [slide file](../raw/slides/13-gibbs-sampling.md); the class saw the same numbers.

| Network | Query | Run | Estimate | Exact |
| --- | --- | --- | --- | --- |
| Telephone, $A \to B \to C$ (copy 0.8, flip 0.2) | $P(A = 1 \mid C = 1)$ | 100 iterations, full joint | 0.68 | 0.68 (see note) |
| Telephone | $P(A = 1 \mid C = 1)$ | 100 iterations, Markov blanket | 0.63 | 0.68 |
| Alarm | $P(B = 1 \mid A = 1)$ | 100 iterations | 0.65 | 0.513 |
| Alarm | $P(B = 1 \mid A = 1)$ | 200 iterations | 0.335 | 0.513 |
| $A \to B$ with $B = A$ | $P(A = 1)$, no evidence | 50 iterations from $(0, 0)$ | 0.0 | 0.5 |

For comparison, rejection sampling on the telephone with 100 samples gave 0.653 ([≈21:49](../raw/transcripts/13-gibbs-sampling.md), [≈33:24](../raw/transcripts/13-gibbs-sampling.md)). The lecture does not compute the telephone's exact answer; 0.68 is lecture 12's select–sum–divide applied to the tables, since $C$ equals $A$ with probability $0.8 \times 0.8 + 0.2 \times 0.2 = 0.68$ and $A$ is a fair coin. The alarm runs, which the lecturer called "not working super well," are compared against the exact $0.5128$ computed in the same lecture ([≈49:43](../raw/transcripts/13-gibbs-sampling.md), [≈50:30](../raw/transcripts/13-gibbs-sampling.md)).

## When It Works and When It Does Not

The lecture's two complementary examples ([≈51:17](../raw/transcripts/13-gibbs-sampling.md)–[≈57:31](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

- **Rare evidence is easy for Gibbs sampling, hard for rejection sampling.** In $A \to B$ with $p(B = 1 \mid A)$ only 0.0001 or 0.0002, rejection sampling keeps about one sample in 10,000. Gibbs sampling conditions on $B = 1$ from the start, so it works on the two relevant rows directly and gets the answer, 2/3 for $A = 1$, without trouble.
- **Highly correlated variables are hard for Gibbs sampling, easy for rejection sampling.** If $B = A$ always, then from $A = 0, B = 0$ every proposed single change has probability zero, the chain never moves, and the estimate of $P(A = 1)$ is 0 instead of 0.5. It is like a three-legged race: neither can move until the other does. Near-determinism, such as 0.9999, is almost as bad.

"The bad news is that in most real-world problems both are true," so both samplers will struggle; knowing why helps ([≈57:31](../raw/transcripts/13-gibbs-sampling.md)).

## Beyond Gibbs Sampling

A more general MCMC algorithm, **Metropolis–Hastings**, uses a proposal distribution to guide the search more smartly. The theory of how fast Markov chains run uses **mixing times**, which measure the effective running time in terms of how correlated the samples are. In practice, Gibbs sampling is fairly commonly used because it is simple and effective, though it can be slow ([≈57:31](../raw/transcripts/13-gibbs-sampling.md), [≈58:16](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

## Related Pages

- [Lecture 13](13-gibbs-sampling.md) — the lecture, with the telephone, alarm and failure-case examples worked step by step
- [Markov blanket](markov-blanket.md) — why each update needs only the factors that touch the sampled variable
- [Rejection sampling](rejection-sampling.md) — the simpler sampler Gibbs sampling is compared against
- [Probabilistic inference](probabilistic-inference.md) — the exact computation both samplers approximate
- [Bayesian networks](bayesian-networks.md) — the models being sampled
