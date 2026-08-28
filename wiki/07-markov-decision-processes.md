---
title: Lecture 7 — Markov Decision Processes
lecture: 7
video: https://www.youtube.com/watch?v=2ZtF1j3n6XE
slides: ../raw/slides/07-markov-decision-processes.md
transcript: ../raw/transcripts/07-markov-decision-processes.md
---

# Lecture 7 — Markov Decision Processes

Lecture 7 generalizes [search](05-search-i.md) from deterministic actions to stochastic ones: a **Markov decision process** is a search problem in which an action leads to a *distribution* over next states ([≈0:51](../raw/transcripts/07-markov-decision-processes.md)). That one change forces a new notion of solution — a **policy** mapping states to actions — and a new objective: expected discounted reward. The lecture defines the framework, then builds the two exact algorithms that will recur for the rest of the course: **policy evaluation** (the value of a given policy) and **value iteration** (the value of the optimal policy, and the optimal policy itself), which differ by a single max ([≈1:09:34](../raw/transcripts/07-markov-decision-processes.md)). Concepts with their own pages: [Markov decision processes](markov-decision-processes.md), [Monte Carlo evaluation](monte-carlo-evaluation.md), [policy evaluation](policy-evaluation.md), [value iteration](value-iteration.md).

## Why Search Isn't Enough

Last week's search problems assumed **deterministic** actions: take an action, land in one particular state ([≈0:05](../raw/transcripts/07-markov-decision-processes.md)). The motivating question — walk, bike, or drive to the grocery store? — has no such determinism: there's traffic, there's parking time ([≈1:36](../raw/transcripts/07-markov-decision-processes.md)). An MDP adds exactly the missing ingredient: actions with stochastic outcomes, like dice rolls ([≈0:51](../raw/transcripts/07-markov-decision-processes.md)).

The name has a 1950s operations-research pedigree ([≈2:22](../raw/transcripts/07-markov-decision-processes.md), [≈3:09](../raw/transcripts/07-markov-decision-processes.md)): *Markov* — from Markov chains, where past and future are independent given the state; *decision* — an agent takes actions; *process* — things happen sequentially over time.

## The Flaky Tram

The running example mutates the familiar travel problem ([≈3:54](../raw/transcripts/07-markov-decision-processes.md)): locations 1 to 10, walking $i \to i+1$ costs 1 minute, the tram $i \to 2i$ costs 2 minutes — **but breaks down with probability 0.4**, leaving you stuck where you are, still out two minutes ([≈4:42](../raw/transcripts/07-markov-decision-processes.md)). The goal is now "least time **in expectation**" — with randomness there is no single path whose cost is the answer, only an average over outcomes ([≈3:54](../raw/transcripts/07-markov-decision-processes.md)).

From state 1 ([≈5:29](../raw/transcripts/07-markov-decision-processes.md), [≈6:15](../raw/transcripts/07-markov-decision-processes.md), [slides](../raw/slides/07-markov-decision-processes.md)):

- walk: probability 1, reward −1, state 2. (Probability 1 is just the probabilistic way of saying deterministic; reward −1 is cost 1 by convention.)
- tram: probability 0.6, reward −2, state 2 (success) — and probability 0.4, reward −2, state 1 (failure).

One action, two successors — and a **cycle** (1 → 1), which is fine: the algorithms ahead handle cycles ([≈7:01](../raw/transcripts/07-markov-decision-processes.md)). In the graph, the dotted nodes are **chance nodes** — "I've taken an action, and here's where I might land" ([≈7:49](../raw/transcripts/07-markov-decision-processes.md)).

The formal notation ([≈10:06](../raw/transcripts/07-markov-decision-processes.md), [≈10:56](../raw/transcripts/07-markov-decision-processes.md)): $\text{Actions}(s)$, $\text{Reward}(s, a, s')$, and the transition function $T(s, a, s')$ with $\sum_{s'} T(s,a,s') = 1$ for every state-action pair. The $(s, a, s')$ triple is two decisions in one: $s \to a$ is the *agent's*, $a \to s'$ is *nature's* — nobody controls whether the tram succeeds ([≈10:56](../raw/transcripts/07-markov-decision-processes.md)). Asked where the probabilities come from: they're part of the problem definition — you specify them (or, next lecture, learn them) ([≈9:21](../raw/transcripts/07-markov-decision-processes.md)).

## The Dice Game

The second example is a game ([≈13:18](../raw/transcripts/07-markov-decision-processes.md)): each round, **quit** (take $10, game over) or **stay** (take $4, roll a die; 1 or 2 ends the game, otherwise play another round). Formally it's an MDP with just two states, `"in"` and `"end"` ([≈14:04](../raw/transcripts/07-markov-decision-processes.md)): quit → end with probability 1, reward 10; stay → end with probability 1/3, reward 4; stay → in with probability 2/3, reward 4.

The audience poll sets up a puzzle ([≈15:36](../raw/transcripts/07-markov-decision-processes.md)): should you stay, quit, or play some hybrid like "stay three rounds then quit"? The lecturer promises an answer: for MDPs there's no point in hybrid strategies — the optimal policy is deterministic — a fact justified properly at the end of the lecture ([≈16:21](../raw/transcripts/07-markov-decision-processes.md), [≈1:17:28](../raw/transcripts/07-markov-decision-processes.md)).

## Policies

A search solution was a *sequence* of actions — pre-plannable because everything is deterministic. With stochastic outcomes you can't pre-plan: you might land in state 3 or state 5, and different futures need different actions ([≈17:53](../raw/transcripts/07-markov-decision-processes.md), [≈18:39](../raw/transcripts/07-markov-decision-processes.md)). The solution to an MDP is a **policy** — a function from state to action — which tells you what to do *no matter where you end up* ([≈18:39](../raw/transcripts/07-markov-decision-processes.md)).

Two example policies ([≈19:26](../raw/transcripts/07-markov-decision-processes.md)): **always walk** (ignores the state), and **tram if possible** (take the tram while $2s \le 10$; at state 3 that's tram, at state 6 it's walk).

## Rollouts, Utility, and Discounting

A **rollout** is a simulation: at each state the policy picks the action, then the MDP draws the successor per its probabilities ([≈20:12](../raw/transcripts/07-markov-decision-processes.md), [≈20:58](../raw/transcripts/07-markov-decision-processes.md)). The walk rollout (seed 1) runs nine steps to state 10; its **utility** is the discounted sum of rewards $\sum_i \gamma^i r_i$ ([≈22:32](../raw/transcripts/07-markov-decision-processes.md)):

- $\gamma = 1$ (no discounting): all nine −1 rewards count equally → utility **−9** ([≈23:18](../raw/transcripts/07-markov-decision-processes.md)).
- $\gamma = 0$ (full discounting): everything after the first step is zeroed → utility **−1** ([≈24:50](../raw/transcripts/07-markov-decision-processes.md)).
- $\gamma = 0.5$: a dollar today is worth 50 cents tomorrow → utility **−1.99609375** ([≈25:36](../raw/transcripts/07-markov-decision-processes.md)).

In practice $\gamma$ is 1 or close to it — heavy discounting contradicts the whole point of long-term decision-making ([≈26:23](../raw/transcripts/07-markov-decision-processes.md)); a slight discount (say 0.99) instead acts as a **length penalty**, pressuring the policy to finish sooner ([≈31:03](../raw/transcripts/07-markov-decision-processes.md), [≈31:50](../raw/transcripts/07-markov-decision-processes.md)). Asked whether the discount must be geometric: it's the only form used in this class, chosen for mathematical convenience, though others exist ([≈25:36](../raw/transcripts/07-markov-decision-processes.md), [≈26:23](../raw/transcripts/07-markov-decision-processes.md)).

## Value: Expected Utility

Four tram rollouts with the same policy produced utilities −10, −10, −16, −10 — the same policy, different outcomes ([≈32:39](../raw/transcripts/07-markov-decision-processes.md)). So a policy is evaluated by its **expected** utility:

$$V_\pi(s) = \text{expected utility of starting in } s \text{ and following } \pi$$

— the stochastic cousin of future cost from [dynamic programming](dynamic-programming.md) ([≈33:25](../raw/transcripts/07-markov-decision-processes.md), [≈34:13](../raw/transcripts/07-markov-decision-processes.md)).

**Monte Carlo estimation** is the direct computation: rollout $n$ times, average the utilities ([≈34:59](../raw/transcripts/07-markov-decision-processes.md)). Walk needs one rollout (deterministic) → −9. Tram-if-possible averaged 20 rollouts → **−11.7** ([≈35:46](../raw/transcripts/07-markov-decision-processes.md)). The estimate converges at rate $1/\sqrt{n}$ — 100 rollouts buys you error ≈ 0.1 ([≈36:33](../raw/transcripts/07-markov-decision-processes.md)) — and the coin-flip analogy from a student question: individual flips never converge, the *fraction* of heads does ([≈37:21](../raw/transcripts/07-markov-decision-processes.md)–[≈38:54](../raw/transcripts/07-markov-decision-processes.md)).

The dice game resolves the poll: always quit is worth 10; always stay averaged 12.8 over 20 rollouts (with one lucky 60 in there) ([≈39:41](../raw/transcripts/07-markov-decision-processes.md), [≈40:30](../raw/transcripts/07-markov-decision-processes.md)). If you trust the simulation, stay ([≈41:15](../raw/transcripts/07-markov-decision-processes.md)).

## Q-values and Bootstrapping

To compute values *exactly*, the lecture introduces the **Q-value** — the value of taking a particular action and then obtaining value $V$ in the resulting state ([≈43:34](../raw/transcripts/07-markov-decision-processes.md)):

$$Q(s, a, V) = \sum_{s'} T(s, a, s') \Big[ \text{Reward}(s, a, s') + \gamma V(s') \Big]$$

Unlike Monte Carlo — which *samples* one successor per step — the Q-value sums over **all** successors, eliminating sampling noise ([≈49:50](../raw/transcripts/07-markov-decision-processes.md)). The warmup: values initialized to 0 at end states and −100 elsewhere (a placeholder for "invalid") ([≈45:55](../raw/transcripts/07-markov-decision-processes.md)); at state 9 the tram policy's action is walk (tram impossible), and $Q(9, \text{walk}, V) = 1 \cdot (-1 + 1 \cdot 0) = -1$ ([≈46:42](../raw/transcripts/07-markov-decision-processes.md)).

**Bootstrapping** ([≈51:24](../raw/transcripts/07-markov-decision-processes.md), [≈52:13](../raw/transcripts/07-markov-decision-processes.md)): start with the value of terminating, and each iteration computes the value of following the policy one step longer before terminating. The stopping rule is the **$L_\infty$ distance** between successive value vectors — maximum absolute change over all states — with tolerance $10^{-5}$ ([≈53:00](../raw/transcripts/07-markov-decision-processes.md), [≈53:46](../raw/transcripts/07-markov-decision-processes.md)). The max matters: one far-off state means you're not done, however good the others look.

## Policy Evaluation

Policy evaluation assembles those parts ([≈54:33](../raw/transcripts/07-markov-decision-processes.md), [≈55:22](../raw/transcripts/07-markov-decision-processes.md)): initialize, then repeatedly set $V^{(t)}_\pi(s) = Q(s, \pi(s), V^{(t-1)}_\pi)$ for every state, until the distance drops below tolerance. One iteration on the tram already teaches the whole algorithm ([≈56:55](../raw/transcripts/07-markov-decision-processes.md)–[≈58:29](../raw/transcripts/07-markov-decision-processes.md)): state 9 becomes −1 (walk to 10), state 5 becomes **−42** (0.6 chance of the tram reaching 10 — but 0.4 chance of staying in state 5, whose value is still −100), states 1–4 become −102. The lecturer's on-the-fly arithmetic ($-41.2$) dropped the −2 reward on the failure branch; the code's −42 is the exact number.

After **27 iterations**, the value of tram-if-possible from state 1 is **−12** — against the Monte Carlo estimate −11.7, in the ballpark but not exact ([≈59:16](../raw/transcripts/07-markov-decision-processes.md), [≈1:00:04](../raw/transcripts/07-markov-decision-processes.md)). The distance sequence shows the promised two phases ([≈1:04:45](../raw/transcripts/07-markov-decision-processes.md)–[≈1:06:24](../raw/transcripts/07-markov-decision-processes.md)): stuck at 99 while information propagates to unreached states, then geometric decay. On the dice game, always-stay converges to exactly **12** — the fixed point of $V = 4 + \frac{2}{3}V$ ([≈1:07:13](../raw/transcripts/07-markov-decision-processes.md)). Full details, including the value tables: [policy evaluation](policy-evaluation.md).

## Value Iteration

With ten minutes left, the lecturer's pitch: finding the *optimal* policy sounds much harder — like $f(x)$ versus $\min_x f(x)$ — but here it's a **one-line change** ([≈1:08:46](../raw/transcripts/07-markov-decision-processes.md), [≈1:09:34](../raw/transcripts/07-markov-decision-processes.md)). Where policy evaluation plugs in $\pi(s)$, value iteration takes the max over actions ([≈1:10:19](../raw/transcripts/07-markov-decision-processes.md)):

$$V^*(s) = \max_a Q(s, a, V^*)$$

grounded, like everything here, in [Bellman's 1957 dynamic programming](https://gwern.net/doc/statistics/decision/1957-bellman-dynamicprogramming.pdf). The per-state routine computes every action's Q-value, takes the max as the new value, and records the argmax as the optimal action ([≈1:12:42](../raw/transcripts/07-markov-decision-processes.md), [≈1:14:20](../raw/transcripts/07-markov-decision-processes.md)).

The result on the flaky tram ([≈1:15:06](../raw/transcripts/07-markov-decision-processes.md), [slides](../raw/slides/07-markov-decision-processes.md)): $V^*(1) = -7.33$, beating both always-walk (−9) and tram-if-possible (−12). The optimal policy is **walk until state 5, then take the tram**: early on, the flaky tram wastes two minutes at a time and failures compound; at 5, a 60% shot straight to 10 is worth it, and a failure just lets you try again ([≈1:16:40](../raw/transcripts/07-markov-decision-processes.md)). That this is the answer is *not* obvious by inspection — which is the argument for having the algorithm ([≈1:16:40](../raw/transcripts/07-markov-decision-processes.md)).

One closing theorem, from a student question: the optimal policy for an MDP is **always deterministic** — the value is a max, and a max is attained by some action; a randomized policy can tie but never beat it. (In games, next week, randomness genuinely helps; in MDPs it doesn't.) ([≈1:17:28](../raw/transcripts/07-markov-decision-processes.md))

## Summary

An MDP generalizes a search problem by letting actions produce a **distribution** over next states; its solution is a **policy**, and a policy's worth is its **value** — the expected discounted utility over rollouts ([≈1:18:14](../raw/transcripts/07-markov-decision-processes.md), [≈1:18:59](../raw/transcripts/07-markov-decision-processes.md)). Monte Carlo estimates the value by averaging; policy evaluation computes it exactly for a given policy; value iteration computes it for the optimal policy — both by iterating a recurrence and stopping on the $L_\infty$ distance. The lecturer's parting advice: study the recurrences, not the algorithms ([≈1:19:46](../raw/transcripts/07-markov-decision-processes.md)).

**Next time**: reinforcement learning — finding policies when you *don't* know the transitions or rewards ([≈1:19:46](../raw/transcripts/07-markov-decision-processes.md)).
