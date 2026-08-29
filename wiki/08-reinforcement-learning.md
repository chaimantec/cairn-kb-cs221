---
title: Lecture 8 — Reinforcement Learning
---

# Lecture 8 — Reinforcement Learning

Lecture 8 turns [Markov decision processes](markov-decision-processes.md) into a learning problem: the agent no longer knows the transition probabilities $T$ or the rewards $R$, and must discover good behavior by **interacting with the environment** ([≈10:59](../raw/transcripts/08-reinforcement-learning.md)). An **RL algorithm** (an *agent*) exposes two methods — `get_action` to produce an action, and `incorporate_feedback` to absorb the resulting reward and next state ([≈15:39](../raw/transcripts/08-reinforcement-learning.md)). The lecture then builds four increasingly direct algorithms on the flaky tram: **model-based value iteration** (estimate the MDP, then value-iterate it), **model-free Monte Carlo** (average rollout utilities into Q-values), **SARSA** (bootstrap mid-rollout, on-policy), and **Q-learning** (bootstrap off-policy, directly estimating the optimal Q-values).

## The RL Setting

Reinforcement learning is MDPs where you don't know the MDP ([≈10:59](../raw/transcripts/08-reinforcement-learning.md)). The setting is a loop between two entities ([≈11:47](../raw/transcripts/08-reinforcement-learning.md), [slides](../raw/slides/08-reinforcement-learning.md)): the **agent** produces an action, the **environment** produces a reward and an observation, and the pair repeats. A good agent tries various actions to find ones that lead to good rewards, then *learns* to keep doing them — those actions are **reinforced**, which is where the name comes from ([≈12:32](../raw/transcripts/08-reinforcement-learning.md)).

In an MDP you don't know the outcomes, but at least you know their probabilities; in RL you don't even know those — "that's real life" ([≈13:19](../raw/transcripts/08-reinforcement-learning.md)). This lecture assumes the environment is an MDP and the observation is the full next state; observing only part of the state is the **partially observable** (POMDP) case, mentioned but not covered ([≈14:06](../raw/transcripts/08-reinforcement-learning.md)). The lecturer also warns not to confuse the RL sense of "agent" with language-model agents ([≈14:52](../raw/transcripts/08-reinforcement-learning.md)).

**Policy vs. agent** ([≈21:00](../raw/transcripts/08-reinforcement-learning.md)): a *policy* maps state to action and never changes; an *agent* (RL algorithm) does the same via `get_action` but can change over time — `incorporate_feedback` is where it updates. As one student put it, the agent changes the policy dynamically ([≈23:26](../raw/transcripts/08-reinforcement-learning.md)). The simplest agent, `StaticAgent`, just holds a fixed policy and ignores all feedback ([≈15:39](../raw/transcripts/08-reinforcement-learning.md)) — `simulate` runs it for 10 trials on the tram and it scores **−11.6** ([slides](../raw/slides/08-reinforcement-learning.md)), a value it never improves.

## Model-Based Value Iteration

Since we already know value iteration, the natural idea is: **learn the MDP from feedback, then value-iterate the estimated MDP** ([≈24:59](../raw/transcripts/08-reinforcement-learning.md)). Three stages ([≈25:46](../raw/transcripts/08-reinforcement-learning.md)):

1. **Exploration**: follow an exploration policy — here `walk_tram_policy`, which samples a uniformly random valid action ([≈26:34](../raw/transcripts/08-reinforcement-learning.md)) — and record feedback.
2. **Estimation → value iteration**: build the estimated MDP, run value iteration on it to get its optimal policy.
3. **Exploitation**: follow that policy.

The **estimated MDP** starts completely empty and fills in from feedback ([≈28:53](../raw/transcripts/08-reinforcement-learning.md)): a start state, rewards keyed by $(s, a, s')$ triples, and **transition counts** — a counter for each witnessed $(s, a, s')$. The counts become probabilities when `successors` is called: after 10 exploration rollouts, state 1's tram counts are 1→1 once and 1→2 twice, so the estimated probabilities come out **0.333 and 0.6667** against the true 0.4 and 0.6 ([≈37:23](../raw/transcripts/08-reinforcement-learning.md)). "The more the agent explores, the closer the estimated MDP will be to the true MDP" — assuming the exploration policy actually tries all valid actions ([≈38:54](../raw/transcripts/08-reinforcement-learning.md)).

The demo's numbers ([slides](../raw/slides/08-reinforcement-learning.md)): exploration scores **−9.0**; value iteration on the estimated MDP yields the policy walk everywhere except tram from 5 — which on this seed happens to be *exactly* the true optimal policy, though with a different seed the lecturer had seen it come out wrong ([≈39:41](../raw/transcripts/08-reinforcement-learning.md)); exploiting that policy scores **−6.6** ([≈41:13](../raw/transcripts/08-reinforcement-learning.md)).

Two practical notes: the exploration phase is suboptimal **by design** — you invest in learning ([≈42:51](../raw/transcripts/08-reinforcement-learning.md)) — and you don't have to do two clean phases; in practice you keep refining the estimated MDP and gradually shift from exploration to exploitation ([≈42:51](../raw/transcripts/08-reinforcement-learning.md)). Also, the estimated MDP may not have seen every state: running the learned policy can hit unseen states, where you'd fall back to a random action (or the code just crashes) ([≈45:08](../raw/transcripts/08-reinforcement-learning.md)).

## Model-Free Monte Carlo

The MDP was a means to an end: all we really want is the policy, and $\pi^*(s) = \arg\max_a Q^*(s, a)$ — **if you know Q, you know the optimal policy** ([≈46:40](../raw/transcripts/08-reinforcement-learning.md)). So skip the MDP entirely: roll out the policy and **average the utilities** for each (state, action) pair ([≈47:27](../raw/transcripts/08-reinforcement-learning.md)).

The twist is computing a utility for **every step** of a rollout, not just the first ([≈48:14](../raw/transcripts/08-reinforcement-learning.md)). For the rollout walk (−1), tram (−2), tram (−2) with discount 1:

- step 0: $-1 + (-2) + (-2) = -5$
- step 1: $-2 + (-2) = -4$
- step 2: $-2 + 0 = -2$

with the recurrence $u_i = r_i + \gamma u_{i+1}$ ([≈49:01](../raw/transcripts/08-reinforcement-learning.md)). The agent keeps `sum_utilities` and `counts` per (state, action) — a running average — and, at the end of each episode, walks **backwards** over the rollout to fill them in ([≈52:04](../raw/transcripts/08-reinforcement-learning.md), [≈54:25](../raw/transcripts/08-reinforcement-learning.md), [≈55:12](../raw/transcripts/08-reinforcement-learning.md)). Each such utility is an *unbiased* estimate of the utility of that (state, action) under the rollout policy ([≈55:57](../raw/transcripts/08-reinforcement-learning.md)).

**Which policy to roll out?** A purely random one wastes what you've learned, so use **epsilon-greedy** ([≈50:33](../raw/transcripts/08-reinforcement-learning.md)): with probability $\varepsilon$ take a random exploration action, with probability $1 - \varepsilon$ take the best action under the current Q-values. $\varepsilon$ is small and controls the exploration/exploitation balance ([≈50:33](../raw/transcripts/08-reinforcement-learning.md)).

Two important caveats ([≈1:03:37](../raw/transcripts/08-reinforcement-learning.md)): this estimates $Q_\pi$ for the *current* (exploration-softened) policy, **not** $Q^*$ — the lecturer admits to "pulling a fast one" there when a student catches it. And the demo score, **−9.45** ([slides](../raw/slides/08-reinforcement-learning.md)), is again exploration-phase behavior. The path from $Q_\pi$ to better policies is **policy improvement**: take the argmax of $Q_\pi$ to get a slightly better policy, estimate its Q-values, repeat ([≈1:05:08](../raw/transcripts/08-reinforcement-learning.md)).

**Model-based vs. model-free** got two student questions: Q is like a *compiled* version of the MDP — it tells you the value of actions, not what will happen ([≈1:00:33](../raw/transcripts/08-reinforcement-learning.md)) — and why model-free might win: often you know what to do without being able to predict what happens (a tennis player can hit the ball without simulating the scene) ([≈1:01:20](../raw/transcripts/08-reinforcement-learning.md)). The lecturer notes the modern counterpoint — **world models** built on foundation models have revived the model-based side, and the debate is ongoing ([≈1:02:06](../raw/transcripts/08-reinforcement-learning.md)).

## SARSA and Q-Learning: Bootstrapping

Monte Carlo's problem: **in life you only get one rollout** — waiting until the end to update is too late ([≈1:05:53](../raw/transcripts/08-reinforcement-learning.md)). The fix is **bootstrapping** ([≈1:06:40](../raw/transcripts/08-reinforcement-learning.md)): instead of the full discounted sum $u = r_0 + \gamma r_1 + \gamma^2 r_2 + \dots$, substitute your current estimate of the future:

$$u = r + \gamma \, Q(s', a')$$

then move the stored Q-value toward this target with a **gradient update** ([≈1:08:12](../raw/transcripts/08-reinforcement-learning.md)):

$$Q(s, a) \leftarrow Q(s, a) + \eta \left( u - Q(s, a) \right)$$

If the target equals the current estimate, nothing moves; if it's higher (or lower), Q steps toward it ([≈1:10:30](../raw/transcripts/08-reinforcement-learning.md)).

**SARSA** computes the next action the same way the agent actually behaves — via `get_action`, which includes the exploration noise — so it estimates the Q-values of the **current** policy $Q_\pi$: an **on-policy** algorithm ([≈1:09:43](../raw/transcripts/08-reinforcement-learning.md), [≈1:12:01](../raw/transcripts/08-reinforcement-learning.md)). Its name is the tuple it consumes: **s**tate, **a**ction, **r**eward, **s**tate, **a**ction ([≈1:09:43](../raw/transcripts/08-reinforcement-learning.md)). The demo's hand-fed updates ([slides](../raw/slides/08-reinforcement-learning.md)): after (1, walk, −1, 2), (2, tram, −2, 2), (2, tram, −2, 4, end) with $\eta = 0.1$, Q(1, walk) = **−0.1** and Q(2, tram) = **−0.38**. Run for real: **−9.6** ([≈1:11:15](../raw/transcripts/08-reinforcement-learning.md)).

**Q-learning** changes one thing: the bootstrapped next action is chosen by `pi` — the **greedy** policy implied by the current Q-table — instead of `get_action`. So it estimates $Q^*$ directly even while the agent keeps exploring: an **off-policy** algorithm ([≈1:12:47](../raw/transcripts/08-reinforcement-learning.md), [≈1:13:33](../raw/transcripts/08-reinforcement-learning.md)). Written out ([≈1:14:19](../raw/transcripts/08-reinforcement-learning.md), [≈1:15:05](../raw/transcripts/08-reinforcement-learning.md)):

$$\text{SARSA: } Q_\pi(s, a) \leftarrow Q_\pi(s, a) + \eta \left( r + \gamma Q_\pi(s', a') - Q_\pi(s, a) \right)$$

$$\text{Q-learning: } Q^*(s, a) \leftarrow Q^*(s, a) + \eta \left( r + \gamma \max_{a'} Q^*(s', a') - Q^*(s, a) \right)$$

In code, `QLearning` subclasses `SARSA` and overrides only `incorporate_feedback` — the single difference is `self.pi` instead of `self.get_action` for the next action ([≈1:13:33](../raw/transcripts/08-reinforcement-learning.md), [slides](../raw/slides/08-reinforcement-learning.md)). (The lecturer says this backwards once — "SARSA is now a subclass of Q-learning" — and the transcript notes it.) On the same hand-fed demo the Q-values happen to come out identical (−0.1 and −0.38); the difference is which next action enters the bootstrap. Run for real: **−9.9** ([slides](../raw/slides/08-reinforcement-learning.md)).

## The Leaderboard

All four agents plus the static baseline, on `FlakyTramMDP(num_locs=10, failure_prob=0.4)` with `np.random.seed(1)` ([slides](../raw/slides/08-reinforcement-learning.md)):

| method | mean utility |
| ------ | ------------ |
| tram_if_possible_policy (static) | −11.6 |
| model_based_value_iteration.explore | −9.0 |
| model_based_value_iteration.exploit | −6.6 |
| model_free_monte_carlo | −9.45 |
| sarsa | −9.6 |
| q_learning | −9.9 |

The three model-free methods all score near −9 because they were still exploring (and estimating $Q_\pi$, not $Q^*$) — the point of this lecture is the *machinery*, not beating the tram. Next time: huge state spaces, where tabular Q-values don't fit.

## Related Pages

- [Reinforcement learning](reinforcement-learning.md) — the setting, the agent interface, and the four-algorithm map
- [Q-learning](q-learning.md) — estimating Q-values without the MDP: Monte Carlo, bootstrapping, on- vs. off-policy
- [Markov decision processes](markov-decision-processes.md) — the formalism this lecture relaxes
- [Policy evaluation](policy-evaluation.md) and [value iteration](value-iteration.md) — the exact algorithms the model-based approach reuses
- [Monte Carlo evaluation](monte-carlo-evaluation.md) — the averaging idea from lecture 7, repurposed here to estimate Q
- [Transcript](../raw/transcripts/08-reinforcement-learning.md) · [Slides](../raw/slides/08-reinforcement-learning.md)
