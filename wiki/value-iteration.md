---
title: Value Iteration — Computing the Optimal Policy by Taking Maxes
---

# Value Iteration

[Policy evaluation](policy-evaluation.md) computes the value of a policy you already have. **Value iteration** computes the value of the *best* policy — and produces the optimal policy itself along the way ([≈1:09:34](../raw/transcripts/07-markov-decision-processes.md)).

That sounds strictly harder: computing $f(x)$ is trivial while finding $\min_x f(x)$ can be intractable ([≈1:09:34](../raw/transcripts/07-markov-decision-processes.md)). For MDPs the two turn out to be nearly identical, and the difference is literally one line: where policy evaluation plugs in the policy action $\pi(s)$, value iteration takes a **max over all actions** ([≈1:10:19](../raw/transcripts/07-markov-decision-processes.md), [≈1:11:06](../raw/transcripts/07-markov-decision-processes.md)). The formal foundation is Richard Bellman's work on dynamic programming ([Bellman, 1957](https://gwern.net/doc/statistics/decision/1957-bellman-dynamicprogramming.pdf)).

## The Recurrence

The two recurrences side by side ([≈1:10:19](../raw/transcripts/07-markov-decision-processes.md), [slides](../raw/slides/07-markov-decision-processes.md)):

Policy evaluation: $\quad V_\pi(s) = \sum_{s'} T(s, \pi(s), s') \Big[ R(s, \pi(s), s') + \gamma V_\pi(s') \Big]$

Value iteration: $\quad V^*(s) = \max_a \sum_{s'} T(s, a, s') \Big[ R(s, a, s') + \gamma V^*(s') \Big]$

**Key difference: optimize over actions $a$ instead of taking the policy action $\pi(s)$.**

In Q-value notation, $V^*(s) = \max_a Q(s, a, V^*)$ — a fixed-point equation; the iterative version runs $V^{(t)}(s) = \max_a Q(s, a, V^{(t-1)})$ ([≈1:11:54](../raw/transcripts/07-markov-decision-processes.md), [≈1:13:34](../raw/transcripts/07-markov-decision-processes.md)).

## The Algorithm

Exactly policy evaluation's sweep, with per-state maximization ([≈1:14:20](../raw/transcripts/07-markov-decision-processes.md), [slides](../raw/slides/07-markov-decision-processes.md)):

1. Initialize values (0 for end states, −100 elsewhere) and a table $\pi$ recording the best action per state.
2. Repeat: for every non-end state, compute the Q-value of **every** action, set the new value to the max, and record the argmax action in $\pi$.
3. End states stay at 0.
4. Stop when the maximum change between iterations drops below $10^{-5}$ (the same $L_\infty$ convergence test as [policy evaluation](policy-evaluation.md) ([≈53:00](../raw/transcripts/07-markov-decision-processes.md))).

## The Flaky Tram Result

Running value iteration on `FlakyTramMDP(num_locs=10, failure_prob=0.4)` converges in 22 sweeps (iterations 0–21, final distance 9.96×10⁻⁶) ([slides](../raw/slides/07-markov-decision-processes.md)):

| state | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
| ----- | - | - | - | - | - | - | - | - | - | -- |
| $V^*$ | −7.33 | −6.33 | −5.33 | −4.33 | −3.33 | −4 | −3 | −2 | −1 | 0 |
| $\pi^*$ | walk | walk | walk | walk | **tram** | walk | walk | walk | walk | — |

Three values worth comparing ([≈1:15:06](../raw/transcripts/07-markov-decision-processes.md)): always walking achieves −9, tram-if-possible achieves −12, and the optimal policy achieves **−7.33** — better than both, as it must be.

The optimal policy is memorable: **walk until 5, then take the tram**. The intuition ([≈1:16:40](../raw/transcripts/07-markov-decision-processes.md)): the tram is flaky, so taking it early wastes two minutes at a time on failures that compound; at state 5, though, you're close enough that a 60% chance of jumping straight to 10 pays off — and failures at 5 just keep you at 5, where you can try again. The arithmetic confirms it: $V^*(5) = -2 + 0.6 \cdot 0 + 0.4\, V^*(5)$ gives $V^*(5) = -3.\overline{3}$, versus walking $-1 + V^*(6) = -5$ ([slides](../raw/slides/07-markov-decision-processes.md)). Which action is optimal at which state is *not* obvious a priori for a general problem — that's why the algorithm exists ([≈1:16:40](../raw/transcripts/07-markov-decision-processes.md)).

The convergence has the same two phases as policy evaluation ([≈1:15:52](../raw/transcripts/07-markov-decision-processes.md)): distance stuck high (99, 98, 97, 96 — information propagating one step per iteration) and then geometric decay with ratio 0.4, the tram failure probability ([slides](../raw/slides/07-markov-decision-processes.md)).

## The Optimal Policy Is Deterministic

A question from the floor: is the resulting policy always deterministic? For MDPs, **yes** ([≈1:17:28](../raw/transcripts/07-markov-decision-processes.md)). The value is a max over actions, and a max is attained by some action — a randomized policy can tie the optimum when two actions share the same value, but it can never beat a deterministic one. The lecturer flags the contrast ahead: in games (next week) randomness genuinely helps, but for MDPs a deterministic policy is always sufficient ([≈1:17:28](../raw/transcripts/07-markov-decision-processes.md)).

## Related Pages

- [Policy evaluation](policy-evaluation.md) — the one-line difference, in full detail
- [Games](games.md) — expectimax is value iteration in game clothing (opponent policy fixed); [minimax](minimax.md) swaps averaging for a min, which has no MDP analogue
- [Markov decision processes](markov-decision-processes.md) — the framework and notation
- [Dynamic programming](dynamic-programming.md) — Bellman's idea in its deterministic form
- [Lecture 7](07-markov-decision-processes.md)
