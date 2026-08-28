---
title: Monte Carlo Policy Evaluation — Estimating Values by Averaging Rollouts
---

# Monte Carlo Policy Evaluation

The most direct way to evaluate a [policy](markov-decision-processes.md) is to run it: generate a rollout, look at its utility, repeat, and average ([≈34:59](../raw/transcripts/07-markov-decision-processes.md)). That is Monte Carlo policy evaluation — estimating $V_\pi(s)$ by simulation instead of computation.

## The Idea

Each rollout of an MDP under policy $\pi$ produces a **utility** — the discounted sum of rewards along the simulated path ([≈22:32](../raw/transcripts/07-markov-decision-processes.md)). The same policy yields different rollouts every time, so no single number is "the" answer ([≈32:39](../raw/transcripts/07-markov-decision-processes.md)). The **value** $V_\pi(s)$ is defined as the *average* utility over rollouts — the expected utility ([≈33:25](../raw/transcripts/07-markov-decision-processes.md), [≈34:13](../raw/transcripts/07-markov-decision-processes.md)) — so averaging a finite number of rollouts estimates it:

$$\hat V_\pi = \frac{1}{n} \sum_{i=1}^{n} \text{utility}(\text{rollout}_i)$$

The code is exactly that: generate `num_rollouts` rollouts and take `np.mean` of their utilities ([slides](../raw/slides/07-markov-decision-processes.md)).

## The Lecture's Numbers

On the flaky tram (`num_locs=10, failure_prob=0.4`, seed 1) ([≈35:46](../raw/transcripts/07-markov-decision-processes.md), [slides](../raw/slides/07-markov-decision-processes.md)):

| Policy | Rollouts | Estimate | Exact value |
|---|---|---|---|
| always walk | 1 | −9.0 | −9 (deterministic, one rollout suffices) ([≈34:59](../raw/transcripts/07-markov-decision-processes.md)) |
| tram if possible | 20 | **−11.7** | −12.0 ([policy evaluation](policy-evaluation.md)) |
| dice: always quit | 1 | 10.0 | 10 |
| dice: always stay | 20 | **12.8** | 12.0 ([policy evaluation](policy-evaluation.md)) |

The estimates are in the ballpark but not exact — 20 rollouts of the tram policy gave −11.7 against the true −12 ([≈1:00:04](../raw/transcripts/07-markov-decision-processes.md)). The dice-game stay policy produced individual utilities like 4 (kicked out immediately, probability 1/3), 8 (two rounds, probability $\frac{2}{3} \cdot \frac{1}{3}$), and one lucky 60 ([≈40:30](../raw/transcripts/07-markov-decision-processes.md)) — the point of averaging is that no individual outcome needs to be representative.

## Convergence Rate

The estimate converges to the true value, but slowly: the error is roughly **$1/\sqrt{n}$** — with 100 rollouts, error around 0.1 ([≈36:33](../raw/transcripts/07-markov-decision-processes.md)). The coin-flip analogy explains the intuition: an individual sequence of flips never "converges" to anything, but the *fraction* of heads over many flips converges to 1/2 ([≈37:21](../raw/transcripts/07-markov-decision-processes.md)–[≈38:54](../raw/transcripts/07-markov-decision-processes.md)).

## When to Use It

Monte Carlo is the fallback: it needs no knowledge of the transition or reward functions — just the ability to run rollouts. The exact algorithms ([policy evaluation](policy-evaluation.md), [value iteration](value-iteration.md)) need the probabilities, which is exactly the knowledge reinforcement learning can't assume. If you only need to *compare* two policies, estimating both by Monte Carlo and picking the higher value is perfectly sound ([≈42:00](../raw/transcripts/07-markov-decision-processes.md)).

## Related Pages

- [Markov decision processes](markov-decision-processes.md) — the framework, rollouts and utility
- [Policy evaluation](policy-evaluation.md) — the exact computation Monte Carlo approximates
- [Lecture 7](07-markov-decision-processes.md)
