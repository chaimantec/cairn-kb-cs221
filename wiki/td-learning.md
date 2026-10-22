---
title: TD Learning — Learning State Values by Bootstrapping, and Self-Play
---

# TD Learning

**TD learning** estimates the value function $V_\pi(s)$ (the expected utility of following policy $\pi$ from state $s$) from experience, by **bootstrapping**: after each step it nudges the value of the state it just left toward the reward it received plus its own estimate of the state it landed in ([lecture 11](11-games-ii.md), [≈14:04](../raw/transcripts/11-games-ii.md)). The course's one-line summary is an analogy:

> TD learning : $V_\pi$ :: SARSA : $Q_\pi$

[SARSA](q-learning.md#step-2-sarsa--bootstrap-so-you-can-learn-mid-rollout) estimates the Q-value of each action from a state; TD learning estimates only how good each state is ([≈14:04](../raw/transcripts/11-games-ii.md), [≈14:50](../raw/transcripts/11-games-ii.md)). In CS221 it is the tool that **learns a game's evaluation function** rather than writing it by hand, the question lecture 10 ended on ([≈2:25](../raw/transcripts/11-games-ii.md), [evaluation functions](evaluation-functions.md)).

## When State Values Are Enough

A policy needs to compare actions, and $V_\pi$ scores states, not actions. To act from $V_\pi$ you must turn it back into Q-values ([≈7:53](../raw/transcripts/11-games-ii.md)–[≈9:25](../raw/transcripts/11-games-ii.md)):

$$\pi_{new}(s) = \arg\max_a \sum_{s'} T(s, a, s') \left( R(s, a, s') + \gamma V_\pi(s') \right)$$

where $T(s, a, s')$ is the transition probability, $R(s, a, s')$ the reward and $\gamma$ the discount. That needs the MDP. In general [reinforcement learning](reinforcement-learning.md) the agent does not know $T$, and knowing that a state is good says nothing about which action reaches it, which is why lectures 8 and 9 estimated Q-values instead ([≈10:14](../raw/transcripts/11-games-ii.md), [≈11:01](../raw/transcripts/11-games-ii.md)).

**Games are the exception.** The rules are known, and in games like chess and Go they are deterministic and fully observed. With a successor function $\text{Succ}(s, a)$, transition probability 1, no reward until the end and a discount of 1, the formula collapses to ([≈11:01](../raw/transcripts/11-games-ii.md), [≈11:47](../raw/transcripts/11-games-ii.md), [≈14:04](../raw/transcripts/11-games-ii.md)):

$$\pi_{new}(s) = \arg\max_a V_\pi(\text{Succ}(s, a))$$

That raises the obvious objection: if the MDP is known, why not just run [value iteration](value-iteration.md)? Because outside toy games the number of states is exponential. Reinforcement learning is used here "not because we don't know the MDP" but because function approximation lets it generalize across a state space too large to enumerate ([≈12:33](../raw/transcripts/11-games-ii.md), [≈13:18](../raw/transcripts/11-games-ii.md)).

## The Update

TD learning assumes **function approximation**: $V_\pi(s) = V(s; \mathbf{w})$, a parameterized function with weights $\mathbf{w}$ rather than a table with an independent entry per state. In deep RL this is called a **value network** ([≈14:50](../raw/transcripts/11-games-ii.md), [≈15:36](../raw/transcripts/11-games-ii.md)). On each piece of experience $(s, a, r, s')$ ([≈16:22](../raw/transcripts/11-games-ii.md)–[≈17:54](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

- the **prediction** is $V(s; \mathbf{w})$;
- the **target** is $r + \gamma V(s'; \mathbf{w})$, the immediate reward plus the discounted value of the successor under the same weights;
- the loss is the squared difference $L(\mathbf{w}) = \left( V(s; \mathbf{w}) - (r + \gamma V(s'; \mathbf{w})) \right)^2$, and one gradient step on it gives

$$\mathbf{w} \leftarrow \mathbf{w} - \eta \left[ V(s; \mathbf{w}) - (r + \gamma V(s'; \mathbf{w})) \right] \nabla_{\mathbf{w}} V(s; \mathbf{w})$$

with learning rate $\eta$. The target also depends on $\mathbf{w}$, but it is **held constant**: only the prediction is differentiated, the *detach* operation from the computation graphs of lecture 2. "Your model prediction, move it towards the target. The target is fixed" ([≈18:40](../raw/transcripts/11-games-ii.md), [≈19:25](../raw/transcripts/11-games-ii.md)). Compared with SARSA there is no next action $a'$, because the target needs only a state value ([≈17:07](../raw/transcripts/11-games-ii.md)).

In the tabular case, with one value per state, the update reads simply: move $V(s)$ toward the target, away from the prediction, by a fraction $\eta$. On the flaky tram (6 locations, failure probability 0.1, discount 1, $\eta = 0.1$) the agent at state 1 computes walk −1 and tram −2 from the current values, all still 0, so it walks; the feedback (reward −1, next state 2) gives prediction 0 and target −1, so $V(1)$ becomes **−0.1** ([≈21:42](../raw/transcripts/11-games-ii.md), [≈22:30](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). The demo's `get_action` is epsilon-greedy over a policy computed from $V$ and the MDP's transitions, which is the only place the code uses its knowledge of the MDP ([≈20:11](../raw/transcripts/11-games-ii.md), [≈20:56](../raw/transcripts/11-games-ii.md)).

Two properties to remember ([≈23:16](../raw/transcripts/11-games-ii.md), [≈24:01](../raw/transcripts/11-games-ii.md)):

- **On-policy.** Rollouts follow $\pi$ and no other actions are considered. The lecturer's rule of thumb: a max over actions in the update usually signals off-policy, as in [Q-learning](q-learning.md); no max, probably on-policy.
- **Bootstrapping.** The target contains the model's own estimate, the idea first met in [policy evaluation](policy-evaluation.md).

## Self-Play in Games

What is genuinely new about games is the second player ([≈24:47](../raw/transcripts/11-games-ii.md)). TD learning handles it by letting agent and opponent **share one value function**, which is **self-play**, with the agent maximizing and the opponent minimizing ([≈25:33](../raw/transcripts/11-games-ii.md), [≈26:22](../raw/transcripts/11-games-ii.md)):

$$\pi_{agent}(s) = \arg\max_a V_\pi(\text{Succ}(s, a)) \qquad \pi_{opp}(s) = \arg\min_a V_\pi(\text{Succ}(s, a))$$

"If you know what's good and what's bad, you can choose to do the good thing or you can do the bad thing" ([≈26:22](../raw/transcripts/11-games-ii.md)).

Chance enters as a player with a fixed policy. In **backgammon** the dice roll before each move, since the move depends on the roll, so turns run $\pi_{dice} \to \pi_{agent} \to \pi_{dice} \to \pi_{opp} \to \dots$, and self-play learns $\pi_{agent}$ and $\pi_{opp}$ while $\pi_{dice}$ stays fixed ([≈29:33](../raw/transcripts/11-games-ii.md), [≈30:19](../raw/transcripts/11-games-ii.md)), as chance nodes did in [expectiminimax](games.md#expectiminimax-chance-in-the-game). The value function starts from a feature vector $\phi(s)$ of the board, such as how many pieces sit in a column, how many are on the bar, the fraction removed and whose turn it is. It is then either linear, $V(s; \mathbf{w}) = \phi(s) \cdot \mathbf{w}$, or a multi-layer perceptron $\text{MLP}_ {\mathbf{w}}(\phi(s))$ ([≈31:07](../raw/transcripts/11-games-ii.md), [≈31:53](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)).

## In History

The lecture's three programs show the trend: with more data and compute, less feature engineering ([≈32:39](../raw/transcripts/11-games-ii.md)–[≈37:18](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)).

| Program | Year | Self-play | Features | Value function | Rewards | Result |
| --- | --- | --- | --- | --- | --- | --- |
| Samuel's checkers | 1959 | yes | smart, hand-designed | linear | intermediate rewards added | human amateur level, on an IBM 701 with 9K of memory |
| Tesauro's TD-Gammon | 1992 | 1 million games | dumb | neural network | none | human expert level; new insights into openings |
| AlphaGo Zero | 2017 | 4.9 million games | dumb (stone positions) | neural network, plus Monte Carlo tree search | none | beat AlphaGo, which beat Lee Sedol in 2016 |

Samuel's intermediate rewards were a response to sparse reward: win or lose at the end is a hard, delayed signal to learn from ([≈33:27](../raw/transcripts/11-games-ii.md), [≈34:12](../raw/transcripts/11-games-ii.md)). AlphaGo Zero's Monte Carlo tree search is not covered in the course beyond being described as a less greedy way to get a policy than taking the best next state ([≈36:32](../raw/transcripts/11-games-ii.md)).

## Related Pages

- [Lecture 11](11-games-ii.md) — the lecture page
- [Q-learning](q-learning.md) — SARSA and Q-learning, the Q-value counterparts of this update
- [Evaluation functions](evaluation-functions.md) — the hand-written version TD learning replaces
- [Minimax](minimax.md) and [games](games.md) — the max/min structure self-play inherits
- [Policy evaluation](policy-evaluation.md) — exact computation of $V_\pi$ when the state space is small enough
- [Reinforcement learning](reinforcement-learning.md) — the general setting and the on-/off-policy distinction
