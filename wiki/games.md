---
title: Games — Zero-Sum Game Playing
---

# Games

**Games** extend the state-based models of search and MDPs with an **adversary**: the agent still maximizes utility, but the environment contains an **opponent whose strategy is unknown** ([≈1:37](../raw/transcripts/10-games-i.md), [lecture 10](10-games-i.md)). Where MDPs only ever max (the agent) and average (the environment), games add a **min** — the opponent actively minimizing your utility. The lecture restricts to two-player zero-sum games: the agent's utility is the negative of the opponent's, so the two sum to zero ([≈3:56](../raw/transcripts/10-games-i.md)).

## The Game Formalism

The key object is the **game tree**: the root is the starting state, edges are actions, and every root-to-leaf path is a rollout — one possible outcome of the game ([≈2:24](../raw/transcripts/10-games-i.md), [≈3:56](../raw/transcripts/10-games-i.md)). Formally, a game is any object with five methods ([≈4:43](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)):

- `start_state()` — where the game begins
- `is_end(state)` — is the game over (is this a leaf)?
- `player(state)` — whose turn it is; **a property of the state** ([≈5:31](../raw/transcripts/10-games-i.md))
- `successors(state)` — a mapping from each action to its successor state
- `utility(state)` — the agent's utility at an end state

Two things are special about games ([≈7:04](../raw/transcripts/10-games-i.md)): **all the utility is at the end state** — a sparse-reward problem in RL terms, since nothing tells you how you're doing until the very end ([≈7:51](../raw/transcripts/10-games-i.md)) — and **different players control different states**, which is what distinguishes a game from an MDP's agent/chance alternation ([≈7:51](../raw/transcripts/10-games-i.md)). Because everything passes through the shared state, game trees require **perfect information**; imperfect-information games like poker, non-zero-sum games like the prisoner's dilemma, and simultaneous games like rock-paper-scissors lie outside the framework ([≈50:43](../raw/transcripts/10-games-i.md), [≈51:30](../raw/transcripts/10-games-i.md)).

Policies are per player, as in MDPs: deterministic $\pi_p(s)$ names an action, stochastic $\pi_p(a \mid s)$ gives a distribution over actions ([≈8:36](../raw/transcripts/10-games-i.md)). Discounting plays no role here — utility is a single number at the end, and discounting only matters when accumulating rewards over time ([≈10:10](../raw/transcripts/10-games-i.md)).

The running example is **game 1**: the agent picks bin A, B, or C, the opponent picks a number from that bin, and the utilities are A: −50, 50; B: 1, 3; C: −5, 15 ([≈3:10](../raw/transcripts/10-games-i.md)). The opening poll — most of the class chose C — is the lecture's thesis in miniature: what you should play **depends on what you assume about the opponent** ([≈0:51](../raw/transcripts/10-games-i.md)).

## The Value of a Game

Given a game and a policy for each player, the **value** is the expected utility over all possible rollouts ([≈10:56](../raw/transcripts/10-games-i.md)). Two ways to get it:

- **Monte Carlo**: simulate rollouts and average the utilities ([≈13:21](../raw/transcripts/10-games-i.md)). With the agent always choosing A and the opponent random, 20 rollouts averaged **15** — pure sampling error; a million would converge to the true value **0**, because half the rollouts give −50 and half give 50 ([≈14:10](../raw/transcripts/10-games-i.md)).
- **The evaluation recurrence** $V_{eval}$, computed exactly but in exponential time ([≈14:55](../raw/transcripts/10-games-i.md), [≈17:17](../raw/transcripts/10-games-i.md)):

$$V_{eval}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \sum_{a} \pi_{agent}(s, a) \, V_{eval}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \sum_{a} \pi_{opp}(s, a) \, V_{eval}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

On game 1 the opponent nodes fill in bottom-up as 0, 2, 5 and the root — the agent's policy always picks A — as $1 \cdot 0 + 0 \cdot 2 + 0 \cdot 5 = 0$ ([≈18:06](../raw/transcripts/10-games-i.md), [≈18:52](../raw/transcripts/10-games-i.md)). This is exactly [policy evaluation](policy-evaluation.md) in game clothing — the opponent's known policy plays the role of the environment's transition probabilities ([≈19:37](../raw/transcripts/10-games-i.md)).

## Expectimax: Optimal Against a Known Opponent

**Expectimax** keeps the opponent's policy fixed and optimizes the agent's policy against it ([≈20:24](../raw/transcripts/10-games-i.md)). The recurrence differs from $V_{eval}$ in one line: at the agent's turn, take the **max** over successor values ([≈21:12](../raw/transcripts/10-games-i.md), [≈21:58](../raw/transcripts/10-games-i.md)):

$$V_{exptmax}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \max_a V_{exptmax}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \sum_{a} \pi_{opp}(s, a) \, V_{exptmax}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

Node shapes do the bookkeeping: upward triangles are max nodes (agent), circles are fixed-policy nodes (opponent, chance) ([≈21:12](../raw/transcripts/10-games-i.md)). Against a random opponent, game 1's values are A: 0, B: 2, C: 5 → **choose C, value 5** ([≈22:44](../raw/transcripts/10-games-i.md)). The MDP analogue is [value iteration](value-iteration.md) ([≈25:06](../raw/transcripts/10-games-i.md)). Note that expectimax values **cannot** be estimated by naive Monte Carlo: sampling gives unbiased estimates of sums, but a max of sampled estimates is biased ([≈21:58](../raw/transcripts/10-games-i.md)).

## Expectiminimax: Chance in the Game

Add randomness and the tree gains a third node: the **chance node**, played by a fixed policy $\pi_{coin}$ ([≈46:52](../raw/transcripts/10-games-i.md), [≈47:38](../raw/transcripts/10-games-i.md)). Game 2 is game 1 plus a coin flip: heads shifts the chosen bin one position left, wrapping around (A→C, B→A, C→B), tails stays put ([≈46:52](../raw/transcripts/10-games-i.md)). The recurrence gains a third case — an expectation over $\pi_{coin}$ at chance nodes ([≈48:25](../raw/transcripts/10-games-i.md), [≈49:11](../raw/transcripts/10-games-i.md)):

$$V_{exptminmax}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \max_a V(\text{Succ}(s, a)) & \text{agent} \\ \min_a V(\text{Succ}(s, a)) & \text{opp} \\ \sum_{a} \pi_{coin}(s, a) \, V(\text{Succ}(s, a)) & \text{coin} \end{cases}$$

The chance nodes average to −27.5, −24.5, −2, so the agent chooses C for **−2** — the coin makes the game much worse than game 1 even with optimal play ([slides](../raw/slides/10-games-i.md)). The framework generalizes freely — more than two players (the Pac-Man assignment: one Pac-Man, several ghosts), extra turns, choosing who goes next — because the player whose turn it is is baked into the state, and any transition rule is representable ([≈49:11](../raw/transcripts/10-games-i.md), [≈49:57](../raw/transcripts/10-games-i.md)).

## Face-off: Optimality Is Relative

Every "optimal" policy is optimal **with respect to** an assumption about the other player ([≈36:47](../raw/transcripts/10-games-i.md)). Write $V(\pi_{agent}, \pi_{opp})$ for the value when the row policy plays the column policy; with $\pi_{max}, \pi_{min}$ from [minimax](minimax.md) and $\pi_{exptmax(7)}, \pi_7$ from expectimax against a fixed opponent $\pi_7$, game 1 gives ([≈37:34](../raw/transcripts/10-games-i.md), [≈44:33](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)):

| | $\pi_{min}$ | $\pi_7$ |
| --- | --- | --- |
| $\pi_{max}$ | **1** | 2 |
| $\pi_{exptmax(7)}$ | −5 | **5** |

The three properties ([≈39:07](../raw/transcripts/10-games-i.md)–[≈43:46](../raw/transcripts/10-games-i.md)):

1. $\pi_{max}$ is the best policy against $\pi_{min}$: $V(\pi_{exptmax(7)}, \pi_{min}) = -5 \leq 1 = V(\pi_{max}, \pi_{min})$ ([≈39:07](../raw/transcripts/10-games-i.md), [≈39:55](../raw/transcripts/10-games-i.md)).
2. $\pi_{min}$ is the best policy against $\pi_{max}$: $V(\pi_{max}, \pi_{min}) = 1 \leq 2 = V(\pi_{max}, \pi_7)$ — playing $\pi_{max}$ guarantees at least the minimax value against *any* opponent ([≈40:41](../raw/transcripts/10-games-i.md), [≈41:26](../raw/transcripts/10-games-i.md)).
3. $\pi_{exptmax(7)}$ is optimal against $\pi_7$: $V(\pi_{max}, \pi_7) = 2 \leq 5 = V(\pi_{exptmax(7)}, \pi_7)$ — **you can beat minimax if you know your opponent** ([≈43:01](../raw/transcripts/10-games-i.md), [≈43:46](../raw/transcripts/10-games-i.md)).

The four values chain as **−5 ≤ 1 ≤ 2 ≤ 5** ([≈44:33](../raw/transcripts/10-games-i.md), [≈45:20](../raw/transcripts/10-games-i.md)). The lecturer's preferred reading of minimax follows from property 2: not "the opponent is the worst possible," but "minimax gives a **lower bound** against any unknown opponent" — the interpretation for when you want guarantees ([≈45:20](../raw/transcripts/10-games-i.md), [≈46:06](../raw/transcripts/10-games-i.md)).

## Beyond Turn-Based Zero-Sum Games

[Lecture 11](11-games-ii.md) takes up two of the cases lecture 10 set aside. In [simultaneous games](simultaneous-games.md) both players move at once, so the game tree breaks down; the analysis uses a payoff matrix and mixed strategies, and von Neumann's minimax theorem shows that with mixed strategies going first or second gives the same value ([≈38:53](../raw/transcripts/11-games-ii.md), [≈1:02:24](../raw/transcripts/11-games-ii.md)). In non-zero-sum games such as the prisoner's dilemma the solution concept becomes the [Nash equilibrium](nash-equilibrium.md) ([≈1:08:39](../raw/transcripts/11-games-ii.md)). The same lecture also learns a game's value function by [TD learning](td-learning.md) with self-play.

---

See also [minimax](minimax.md), [alpha-beta pruning](alpha-beta-pruning.md), [evaluation functions](evaluation-functions.md), [TD learning](td-learning.md), [simultaneous games](simultaneous-games.md), and [Nash equilibrium](nash-equilibrium.md).
