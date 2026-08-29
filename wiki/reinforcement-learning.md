---
title: Reinforcement Learning — Learning Policies by Interaction
---

# Reinforcement Learning

**Reinforcement learning (RL)** is [Markov decision processes](markov-decision-processes.md) with the one piece removed that MDP algorithms depend on: the agent does **not** know the transition probabilities $T$ or the rewards $R$ ([≈10:59](../raw/transcripts/08-reinforcement-learning.md)). Everything must be learned by **interacting with the environment** — taking actions and observing what comes back. The setting is a loop: the **agent** produces an action, the **environment** produces a reward and an observation, forever ([≈11:47](../raw/transcripts/08-reinforcement-learning.md)).

Where the MDP lecture asked "what is the optimal policy given the model?", RL asks "what is the optimal policy given only experience?" — and the name captures the strategy: try actions, and when one leads to good rewards, *reinforce* it, i.e. keep doing it ([≈12:32](../raw/transcripts/08-reinforcement-learning.md)).

## The Agent Interface

An **RL algorithm** — the lecture uses "agent" interchangeably — is any object with two methods ([≈15:39](../raw/transcripts/08-reinforcement-learning.md)):

- `get_action(state)` — produce an action for the environment.
- `incorporate_feedback(state, action, reward, next_state, is_end)` — consume the environment's response.

Everything RL amounts to is *what those two methods do*. The trivial baseline, `StaticAgent`, holds a fixed policy: `get_action` calls it, `incorporate_feedback` does nothing — it never learns ([≈15:39](../raw/transcripts/08-reinforcement-learning.md)).

**Policy vs. agent** ([≈21:00](../raw/transcripts/08-reinforcement-learning.md)): a *policy* is static — a fixed state→action map. An *agent* maps state to action too, but can change over time; the point of RL is that `incorporate_feedback` updates the policy inside the agent ([≈23:26](../raw/transcripts/08-reinforcement-learning.md)). In MDPs the policy was the whole story; in RL the policy is a mutable internal state of a learning process.

**What the agent doesn't get**: in this lecture the observation is the full next state, and the environment is backed by an MDP. Real life gives only partial observations — the **partially observable** (POMDP) case — which the course mentions and sets aside ([≈14:06](../raw/transcripts/08-reinforcement-learning.md)).

## The Four Algorithms

Lecture 8 covers four RL algorithms, all on the flaky tram. They sit on two axes ([≈24:13](../raw/transcripts/08-reinforcement-learning.md)):

| | estimates the MDP | estimates Q directly |
| --- | --- | --- |
| **waits for episode end** | — | model-free **Monte Carlo** |
| **bootstraps** | model-based **value iteration** (then value-iterates) | **SARSA** (on-policy) · **Q-learning** (off-policy) |

- **Model-based value iteration** ([≈24:59](../raw/transcripts/08-reinforcement-learning.md)): keep a running estimate of the MDP from feedback (rewards + transition counts), and periodically run [value iteration](value-iteration.md) on it. Stages: explore with a random policy → compute the estimated MDP's optimal policy → exploit it ([≈25:46](../raw/transcripts/08-reinforcement-learning.md)).
- **Model-free Monte Carlo** ([≈47:27](../raw/transcripts/08-reinforcement-learning.md)): never estimate the MDP; average rollout utilities into Q-values for each (state, action). Because $\pi^*(s) = \arg\max_a Q^*(s, a)$, Q-values are all you need to get a policy.
- **SARSA** ([≈1:06:40](../raw/transcripts/08-reinforcement-learning.md)): like Monte Carlo but **bootstraps** — update Q from the immediate reward plus the current Q-estimate of the future — so it learns before the rollout ends. On-policy: it estimates $Q_\pi$ for the policy it is actually following.
- **Q-learning** ([≈1:12:47](../raw/transcripts/08-reinforcement-learning.md)): SARSA with the bootstrapped next action chosen greedily (`pi`) instead of behaviorally (`get_action`), so it estimates $Q^*$ while still exploring — off-policy.

Details of the Q-estimation side live in [q-learning](q-learning.md).

## Exploration vs. Exploitation

The central tension of RL: the agent must **explore** to discover what works, but **exploit** what it has already learned to score well ([≈12:32](../raw/transcripts/08-reinforcement-learning.md)). The lecture's tool is **epsilon-greedy** ([≈50:33](../raw/transcripts/08-reinforcement-learning.md)): with probability $\varepsilon$ take a random action from an exploration policy, with probability $1 - \varepsilon$ take the current best action. $\varepsilon$ (small) dials the exploration amount.

Exploration is an *investment*: the model-based agent scores −9.0 while exploring and −6.6 after switching to the policy learned from that exploration ([≈41:13](../raw/transcripts/08-reinforcement-learning.md), [≈42:51](../raw/transcripts/08-reinforcement-learning.md)). The two-phase version (pure explore, then pure exploit) is a simplification — in practice, keep refining the model and shift the balance gradually ([≈42:51](../raw/transcripts/08-reinforcement-learning.md)).

## Model-Based vs. Model-Free

Why skip the MDP when you know how to value-iterate one? Two answers from the lecture:

- Q-values are a *compiled* version of the MDP: they say what actions are worth, not what will happen — and for acting, that's enough ([≈1:00:33](../raw/transcripts/08-reinforcement-learning.md)).
- Often you know what to do without being able to predict outcomes: a tennis player has a policy for hitting the ball but cannot simulate the whole scene ([≈1:01:20](../raw/transcripts/08-reinforcement-learning.md)). When prediction is intractable, learning a policy (or Q-values) directly can be the only tractable thing.

The counterpoint is **world models**: with foundation models making world simulation feasible again, model-based RL is having a revival — the lecturer calls the model-free vs. model-based debate an ongoing one ([≈1:02:06](../raw/transcripts/08-reinforcement-learning.md)).

## Related Pages

- [Q-learning](q-learning.md) — the Q-estimation side: Monte Carlo, bootstrapping, on- vs. off-policy
- [Lecture 8](08-reinforcement-learning.md) — the lecture page, with the worked demos and leaderboard
- [Markov decision processes](markov-decision-processes.md) — the formalism RL relaxes
- [Policy evaluation](policy-evaluation.md), [value iteration](value-iteration.md) — the exact algorithms the model-based approach reuses on its estimated MDP
- [Monte Carlo evaluation](monte-carlo-evaluation.md) — the averaging idea, first seen in lecture 7
