---
title: Markov Decision Processes — Search with Stochastic Actions
---

# Markov Decision Processes

A **Markov decision process (MDP)** is a [search problem](search-problems.md) generalized in exactly one way: where a search action deterministically leads to one next state, an MDP action leads to a **distribution over next states** ([≈0:51](../raw/transcripts/07-markov-decision-processes.md)). Everything else — start state, successors, end test — carries over. That one change is deep enough that the *solution* changes shape too: instead of a sequence of actions, you need a **policy**.

The name decomposes ([≈2:22](../raw/transcripts/07-markov-decision-processes.md), [≈3:09](../raw/transcripts/07-markov-decision-processes.md)): the formalism dates to operations research in the 1950s; "Markov" is from Markov chains, where the past and future are independent given the state; "decision" means an agent taking actions; "process" means things happening sequentially over time.

## The Formal Definition

An MDP is specified by ([≈10:06](../raw/transcripts/07-markov-decision-processes.md), [slides](../raw/slides/07-markov-decision-processes.md)):

- **States** $s$, with a start state and an `is_end` test — same as search.
- **Actions** $\text{Actions}(s)$ available from state $s$.
- A **transition function** $T(s, a, s')$: the probability of ending up in $s'$ after taking action $a$ from $s$. For every state-action pair, $\sum_{s'} T(s, a, s') = 1$ — a sanity check visible in the graph: the outgoing arrows from every chance node sum to one ([≈11:42](../raw/transcripts/07-markov-decision-processes.md), [≈12:29](../raw/transcripts/07-markov-decision-processes.md)).
- A **reward function** $\text{Reward}(s, a, s')$ for taking that step.
- A **discount factor** $\gamma \in [0, 1]$.

Each $(s, a, s')$ triple bundles two decisions ([≈10:56](../raw/transcripts/07-markov-decision-processes.md)): $s \to a$ is the **agent** deciding, and $a \to s'$ is **nature** deciding — the agent does not control whether the tram succeeds. In code this shows up as a `Step` with fields `(action, prob, reward, state)`, where `prob` is the new field relative to search ([slides](../raw/slides/07-markov-decision-processes.md)).

**Rewards vs costs** are a pure convention: a reward of −2 is a cost of 2, the way error rate and accuracy are two sides of one coin. The deep difference is the distribution over next states ([≈16:21](../raw/transcripts/07-markov-decision-processes.md)).

**Why the reward depends on $s'$**: some formulations use $R(s)$ or $R(s, a)$. They're all equivalent — you can absorb the difference with dummy states — but $R(s, a, s')$ is the most natural to define and doesn't change the algorithms ([≈29:29](../raw/transcripts/07-markov-decision-processes.md), [≈30:16](../raw/transcripts/07-markov-decision-processes.md)).

**Where the probabilities come from**: they are part of the problem definition — you specify them ([≈9:21](../raw/transcripts/07-markov-decision-processes.md)). If you don't know them, that's reinforcement learning territory — the subject of next lecture ([≈10:06](../raw/transcripts/07-markov-decision-processes.md)). *(Page not built yet; link to follow.)*

## The Two Running Examples

**Flaky tram** (`FlakyTramMDP(num_locs=10, failure_prob=0.4)`) ([≈3:54](../raw/transcripts/07-markov-decision-processes.md), [≈4:42](../raw/transcripts/07-markov-decision-processes.md)): locations 1–10; walk $i \to i+1$ costs 1 minute; tram $i \to 2i$ costs 2 minutes but **breaks down with probability 0.4**, in which case you stay put (and still pay the 2). Goal: reach 10 in the least time **in expectation** — the wording changes from search because there is no single path to cost, only an average over outcomes ([≈3:54](../raw/transcripts/07-markov-decision-processes.md)). From state 1:

$$T(1, \text{walk}, 2) = 1, \ R = -1; \qquad T(1, \text{tram}, 2) = 0.6, \ R = -2; \qquad T(1, \text{tram}, 1) = 0.4, \ R = -2$$

The failure self-loop is a **cycle** — the first algorithm to handle cycles was [uniform cost search](uniform-cost-search.md) last week, and the MDP algorithms also handle them with no problem ([≈7:01](../raw/transcripts/07-markov-decision-processes.md)).

**Dice game** ([≈13:18](../raw/transcripts/07-markov-decision-processes.md)): each round, choose quit ($10, game ends) or stay ($4, then a die roll: 1 or 2 ends the game, otherwise continue). Two states only: `"in"` and `"end"`. From `"in"`: quit → end with probability 1, reward 10; stay → end with probability 1/3, reward 4; stay → in with probability 2/3, reward 4.

## Policies

A solution to an MDP **cannot** be a sequence of actions: you can't pre-plan when each action may land you in one of several states ([≈17:53](../raw/transcripts/07-markov-decision-processes.md), [≈18:39](../raw/transcripts/07-markov-decision-processes.md)). The solution is a **policy** — a function $\pi: \text{state} \to \text{action}$ — which works no matter where the randomness lands you ([≈18:39](../raw/transcripts/07-markov-decision-processes.md)).

Two examples from the lecture ([≈19:26](../raw/transcripts/07-markov-decision-processes.md)): *always walk* ignores the state; *tram if possible* returns tram when $2s \le 10$ (so state 3 → tram, state 6 → walk).

## Rollouts, Utility, and Value

A **rollout** is a simulation: the policy picks the action at each state, then the MDP draws the successor according to the probabilities ([≈20:12](../raw/transcripts/07-markov-decision-processes.md), [≈20:58](../raw/transcripts/07-markov-decision-processes.md)). The same policy produces different rollouts each time ([≈32:39](../raw/transcripts/07-markov-decision-processes.md)).

Each rollout has a **utility**, the discounted sum of rewards:

$$\text{utility} = \sum_{i \ge 0} \gamma^i \, r_i$$

The **discount factor** $\gamma$ captures how much the future matters ([≈23:18](../raw/transcripts/07-markov-decision-processes.md)): $\gamma = 1$ treats every step equally (the walk rollout: nine rewards of −1, utility −9); $\gamma = 0$ zeroes out everything after the first step (utility −1); $\gamma = 0.5$ says a dollar today is worth 50 cents tomorrow (utility −1.99609375) ([≈24:04](../raw/transcripts/07-markov-decision-processes.md), [≈24:50](../raw/transcripts/07-markov-decision-processes.md), [≈25:36](../raw/transcripts/07-markov-decision-processes.md)). In practice $\gamma$ is 1 or very close to 1 — heavy discounting defeats the point of long-term decision-making ([≈26:23](../raw/transcripts/07-markov-decision-processes.md)). A discount slightly below 1 (e.g. 0.99) acts as a **length penalty**: it pressures the policy to finish sooner ([≈31:03](../raw/transcripts/07-markov-decision-processes.md), [≈31:50](../raw/transcripts/07-markov-decision-processes.md)).

The **value** of a policy is the expected utility:

$$V_\pi(s) = \text{expected utility of starting in state } s \text{ and following } \pi$$

— the generalization of search's future cost to stochastic outcomes ([≈33:25](../raw/transcripts/07-markov-decision-processes.md), [≈34:13](../raw/transcripts/07-markov-decision-processes.md)). It can be *estimated* by [Monte Carlo](monte-carlo-evaluation.md) (average many rollouts) or computed *exactly* by [policy evaluation](policy-evaluation.md); the optimal policy's value is computed by [value iteration](value-iteration.md).

## Related Pages

- [Policy evaluation](policy-evaluation.md) — computing $V_\pi$ exactly via the recurrence
- [Value iteration](value-iteration.md) — computing $V^*$ and the optimal policy
- [Lecture 7](07-markov-decision-processes.md) — the lecture page, with the worked demos
- [Search problems](search-problems.md) and [dynamic programming](dynamic-programming.md) — what MDPs generalize, and the recurrence pattern they share
