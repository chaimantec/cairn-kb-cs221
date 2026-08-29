---
title: Q-Learning — Estimating Q-Values without the MDP
---

# Q-Learning

**Q-learning** is the end of the chain lecture 8 builds: estimate the **optimal** Q-values $Q^*(s, a)$ directly from interaction, without ever estimating the MDP, and without waiting for a rollout to finish. The name of the page covers the whole Q-estimation chain — Monte Carlo, bootstrapping, SARSA, and Q-learning itself — because they are one idea refined in three steps.

## Why Q?

The recurrences from [value iteration](value-iteration.md) say that the optimal policy follows from the optimal Q-values alone:

$$\pi^*(s) = \arg\max_a Q^*(s, a)$$

— if you know Q, you know what to do ([≈46:40](../raw/transcripts/08-reinforcement-learning.md)). The model-based approach got to Q the long way: estimate $T$ and $R$, then compute Q from them. The model-free idea is to estimate Q **directly** ([≈47:27](../raw/transcripts/08-reinforcement-learning.md)).

Precision matters here, and the lecturer is explicit ([≈1:03:37](../raw/transcripts/08-reinforcement-learning.md)): $Q_\pi(s, a)$ is the value of taking action $a$ in state $s$ and *then following policy* $\pi$. That is not what you ultimately want — it tells you how well you're doing under your current policy. Getting from $Q_\pi$ to $Q^*$ is what the rest of the lecture is about.

## Step 1: Monte Carlo — average utilities

The first estimate of Q uses the same idea as [Monte Carlo policy evaluation](monte-carlo-evaluation.md), applied per (state, action) ([≈48:14](../raw/transcripts/08-reinforcement-learning.md)): for every step of a rollout, compute the utility **from that point on** — the discounted sum of the remaining rewards — and add it to a running average for the (state, action) that produced the step. With the example rollout walk (−1), tram (−2), tram (−2) and discount 1, the per-step utilities are −5, −4, −2, related by the recurrence

$$u_i = r_i + \gamma u_{i+1}$$

([≈49:01](../raw/transcripts/08-reinforcement-learning.md)). Implementation: two tables, `sum_utilities` and `counts`, filled in by walking **backwards** over the rollout when the episode ends ([≈52:04](../raw/transcripts/08-reinforcement-learning.md), [≈55:12](../raw/transcripts/08-reinforcement-learning.md)); Q is the ratio of the two ([≈57:30](../raw/transcripts/08-reinforcement-learning.md)). Each stored utility is an **unbiased** estimate of the true $Q_\pi(s, a)$ ([≈55:57](../raw/transcripts/08-reinforcement-learning.md)).

Two problems: you must wait until the end of the episode to learn anything ([≈1:05:53](../raw/transcripts/08-reinforcement-learning.md)), and the utilities come from whatever policy you used to roll out — so you get $Q_\pi$ for the **current** policy, not $Q^*$ ([≈1:03:37](../raw/transcripts/08-reinforcement-learning.md)). The remaining two steps fix one problem each.

## Step 2: SARSA — bootstrap so you can learn mid-rollout

**Bootstrapping** replaces the unknown tail of the utility with your current estimate of it ([≈1:06:40](../raw/transcripts/08-reinforcement-learning.md)):

$$\underbrace{u = r_0 + \gamma r_1 + \gamma^2 r_2 + \dots}_{\text{Monte Carlo}} \qquad \text{vs.} \qquad \underbrace{u = r_0 + \gamma Q(s_1, a_1)}_{\text{SARSA}}$$

— the immediate reward you just got, plus the Q-value of where you ended up. Then a **gradient update** moves the stored Q toward this target ([≈1:08:12](../raw/transcripts/08-reinforcement-learning.md)):

$$Q(s, a) \leftarrow Q(s, a) + \eta \left( u - Q(s, a) \right)$$

If the target matches the estimate, nothing moves; otherwise Q steps toward it by a fraction $\eta$ (the learning rate) ([≈1:10:30](../raw/transcripts/08-reinforcement-learning.md)). SARSA gets its name from the tuple it uses — **s**tate, **a**ction, **r**eward, **s**tate, **a**ction — where the second action is drawn by `get_action`: the action the agent would *actually* take next, exploration noise included ([≈1:09:43](../raw/transcripts/08-reinforcement-learning.md)). So SARSA estimates $Q_\pi$ for its current behavior — an **on-policy** algorithm ([≈1:12:01](../raw/transcripts/08-reinforcement-learning.md)).

## Step 3: Q-learning — bootstrap off-policy to target $Q^*$

We want the optimal policy, but the policy we *follow* must keep exploring — so estimate the optimal policy without following it: **off-policy** learning ([≈1:12:01](../raw/transcripts/08-reinforcement-learning.md)). Q-learning is SARSA with exactly one character of code changed: the bootstrapped next action comes from `pi` (the greedy argmax under the current Q-table) instead of `get_action` ([≈1:13:33](../raw/transcripts/08-reinforcement-learning.md)):

$$Q_\pi(s, a) \leftarrow Q_\pi(s, a) + \eta \left( r + \gamma Q_\pi(s', a') - Q_\pi(s, a) \right) \quad \text{(SARSA, } a' \sim \text{behavior)}$$

$$Q^*(s, a) \leftarrow Q^*(s, a) + \eta \left( r + \gamma \max_{a'} Q^*(s', a') - Q^*(s, a) \right) \quad \text{(Q-learning)}$$

In the lecture's code, `QLearning` subclasses `SARSA` and overrides only `incorporate_feedback` ([≈1:12:47](../raw/transcripts/08-reinforcement-learning.md)).

**Worked demo** (the lecture's hand-fed feedback on the flaky tram, $\eta = 0.1$, discount 1, [slides](../raw/slides/08-reinforcement-learning.md)): after (1, walk, −1, 2), (2, tram, −2, 2), (2, tram, −2, 4, end), both algorithms end at Q(1, walk) = −0.1 and Q(2, tram) = −0.38 — identical values on this tiny example, because the difference is *which* next action enters the bootstrap, and with a near-empty Q-table the greedy and behavioral choices coincide early on. In SARSA the third update's bootstrap uses an action drawn from the current policy; in Q-learning it uses `pi(4)` = None (nothing known about 4), so the target is just −2. The full-rollout demos score −9.45 (Monte Carlo), −9.6 (SARSA), −9.9 (Q-learning) on the leaderboard.

## From $Q_\pi$ to Better Policies: Policy Improvement

Estimating $Q_\pi$ is not wasted even though you want $Q^*$: the argmax of $Q_\pi$ is a **one-step improvement** over $\pi$ ([≈1:05:08](../raw/transcripts/08-reinforcement-learning.md)). Estimate Q for a policy, take its argmax to get a slightly better policy, estimate again, repeat — a path to the optimum that the course mentions without naming it as an algorithm ([≈1:05:08](../raw/transcripts/08-reinforcement-learning.md)).

## Related Pages

- [Reinforcement learning](reinforcement-learning.md) — the setting and the four-algorithm map
- [Policy gradients](policy-gradients.md) — the alternative to estimating Q at all: learn the policy directly (lecture 9)
- [Lecture 8](08-reinforcement-learning.md) — the lecture page, with the worked demos and leaderboard
- [Policy evaluation](policy-evaluation.md) — Q-values first appear there, inside the exact recurrence
- [Value iteration](value-iteration.md) — the source of the $Q^*$ target Q-learning estimates
- [Monte Carlo evaluation](monte-carlo-evaluation.md) — the averaging idea Q-estimation starts from
