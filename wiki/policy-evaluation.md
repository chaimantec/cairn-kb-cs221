---
title: Policy Evaluation — Computing V_π Exactly by Bootstrapping
---

# Policy Evaluation

[Monte Carlo estimation](monte-carlo-evaluation.md) evaluates a policy by simulating it and averaging — accurate only as $1/\sqrt{n}$. Policy evaluation computes the same quantity **exactly**, by writing down the recurrence the value must satisfy and iterating it to convergence ([≈42:47](../raw/transcripts/07-markov-decision-processes.md)). Structurally it is the same move [dynamic programming](dynamic-programming.md) made for search — a recurrence over states — with probabilities added ([≈42:47](../raw/transcripts/07-markov-decision-processes.md)).

## Q-values: the Recurrence's Building Block

The key quantity is the **Q-value** — the value of taking a specific action, then obtaining value $V$ in whatever state results ([≈43:34](../raw/transcripts/07-markov-decision-processes.md), [≈44:22](../raw/transcripts/07-markov-decision-processes.md)):

$$Q(s, a, V) = \sum_{s'} T(s, a, s') \Big[ \text{Reward}(s, a, s') + \gamma\, V(s') \Big]$$

For each possible successor, take the immediate reward plus the discounted value of wherever you land, then average over successors weighted by their probabilities ([≈47:28](../raw/transcripts/07-markov-decision-processes.md), [≈48:16](../raw/transcripts/07-markov-decision-processes.md)). Unlike Monte Carlo, which *samples one* successor per step, the Q-value looks at **all** of them at once — no sampling noise ([≈49:50](../raw/transcripts/07-markov-decision-processes.md)).

## Bootstrapping

The values have to start somewhere. The initialization is "just terminate": 0 at end states, −100 elsewhere (arbitrary, meaning "invalid" — you haven't figured out how to get there) ([≈45:55](../raw/transcripts/07-markov-decision-processes.md)). Then iterate ([≈52:13](../raw/transcripts/07-markov-decision-processes.md)):

- after 0 steps, values = value of terminating;
- after 1 step, values = value of following the policy for 1 step, then terminating;
- after 2 steps, values = value of following the policy for 2 steps, then terminating;
- … and so on.

Each iteration uses the previous values to compute the next — **bootstrapping**, the pattern that recurs across reinforcement learning algorithms ([≈1:07:59](../raw/transcripts/07-markov-decision-processes.md)).

## The Algorithm

Policy evaluation for policy $\pi$ on an MDP with discount $\gamma$ ([≈55:22](../raw/transcripts/07-markov-decision-processes.md)–[≈56:55](../raw/transcripts/07-markov-decision-processes.md), [slides](../raw/slides/07-markov-decision-processes.md)):

1. Initialize values: 0 for end states, −100 elsewhere.
2. Repeat: for every non-end state $s$, set the new value to the Q-value of the **policy action**:

$$V^{(t)}_\pi(s) = Q\Big(s, \pi(s), V^{(t-1)}_\pi\Big) = \sum_{s'} T(s, \pi(s), s') \Big[ R(s, \pi(s), s') + \gamma\, V^{(t-1)}_\pi(s') \Big]$$

3. End states stay at 0.
4. Measure the **distance** between successive value vectors — the maximum absolute change over all states, i.e. the $L_\infty$ distance ([≈53:00](../raw/transcripts/07-markov-decision-processes.md)). Taking the *max* matters: one state still far off means you're not done, even if the others look converged ([≈53:46](../raw/transcripts/07-markov-decision-processes.md)).
5. Stop when the distance drops below the tolerance ($10^{-5}$); otherwise replace values and iterate.

## Convergence: Two Phases

The distance sequence has a characteristic shape, visible in both demos ([≈1:04:45](../raw/transcripts/07-markov-decision-processes.md)–[≈1:06:24](../raw/transcripts/07-markov-decision-processes.md)):

1. **Reaching the states.** While some state's value is still at the invalid −100, the distance stays high (99 for the tram example, for four iterations — each iteration propagates information one step further from the end state).
2. **Exponential refinement.** Once every state has a real estimate, the distance decays geometrically.

**Flaky tram demo** — policy: tram if possible. Converges after **27 iterations** as the lecturer counts it ([≈1:00:04](../raw/transcripts/07-markov-decision-processes.md)) — 28 sweeps of the loop in the code, iterations 0–27 ([slides](../raw/slides/07-markov-decision-processes.md)):

| state | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
| ----- | - | - | - | - | - | - | - | - | - | -- |
| $V_\pi$ | −12.0 | −8.67 | −7.33 | −5.33 | −3.33 | −4 | −3 | −2 | −1 | 0 |

The value from state 1 is **−12**: the always-tram policy costs 12 minutes in expectation. (The Monte Carlo estimate was −11.7 — ballpark but not exact ([≈1:00:04](../raw/transcripts/07-markov-decision-processes.md)).)

The first iteration already shows the structure ([≈56:55](../raw/transcripts/07-markov-decision-processes.md), [≈57:41](../raw/transcripts/07-markov-decision-processes.md)): state 9 becomes −1 (walk to 10: reward −1 plus 0); state 5 becomes **−42** — the tram with a 60% chance of reaching 10, but a 40% chance of being stuck in state 5, whose value is still −100. The lecturer's mental arithmetic ($0.6 \cdot -2 + 0.4 \cdot -100 = -41.2$) neglected the −2 reward on the failure branch; the code's exact value is $-42$ ([≈58:29](../raw/transcripts/07-markov-decision-processes.md), [slides](../raw/slides/07-markov-decision-processes.md)).

**Dice game demo** — policy: always stay. Two states only; the value of `"in"` converges to **12** ([≈1:07:13](../raw/transcripts/07-markov-decision-processes.md)). The fixed point is exact: $V = 4 + \frac{2}{3} V \Rightarrow V = 12$, and the distance sequence decays by exactly $2/3$ each iteration (the probability of staying in the game) ([slides](../raw/slides/07-markov-decision-processes.md)).

## Related Pages

- [Value iteration](value-iteration.md) — the same algorithm with a max over actions
- [TD learning](td-learning.md) — estimates the same $V_\pi$ from sampled experience with a learned function, for state spaces too large for this exact recurrence (lecture 11)
- [Games](games.md) — the same recurrence appears in lecture 10 as game evaluation ($V_{eval}$): the opponent's known policy plays the role of the transition probabilities
- [Markov decision processes](markov-decision-processes.md) — the framework, including the $Q$-value's place in it
- [Monte Carlo evaluation](monte-carlo-evaluation.md) — the estimate this replaces
- [Dynamic programming](dynamic-programming.md) — the same recurrence idea for deterministic search
- [Lecture 7](07-markov-decision-processes.md)
