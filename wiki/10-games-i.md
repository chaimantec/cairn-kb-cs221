---
title: Lecture 10 — Games I
---

# Lecture 10 — Games I

Lecture 10 completes the tour of state-based models (search, MDPs, RL) with **games**: the agent still maximizes utility, but the environment now contains an **opponent whose strategy is unknown** ([≈1:37](../raw/transcripts/10-games-i.md)). The lecture builds the [game formalism](games.md), defines the **value** of a game under fixed policies and the exact recurrence that computes it, then introduces the two optimal-play recurrences — [expectimax](games.md#expectimax-optimal-against-a-known-opponent) (opponent policy known) and [minimax](minimax.md) (opponent plays optimally) — and their face-off: each "optimal" policy is optimal only against the assumption baked into it. It closes with the two speedups for minimax: [alpha-beta pruning](alpha-beta-pruning.md) (exact) and [evaluation functions](evaluation-functions.md) (approximate, depth-limited).

## The Game Formalism

The key object is the **game tree** ([≈2:24](../raw/transcripts/10-games-i.md)): the root is the starting state, each edge is an action, and every root-to-leaf path is a rollout — a possible outcome of the game ([≈3:56](../raw/transcripts/10-games-i.md)). The lecture restricts to **two-player zero-sum games**: utility of the agent = −utility of the opponent, so the two sum to zero ([≈3:56](../raw/transcripts/10-games-i.md), [≈4:43](../raw/transcripts/10-games-i.md)).

A game is formally an object with five methods ([≈4:43](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)): `start_state()`, `is_end(state)`, `player(state)` (whose turn it is — a property of the state), `successors(state)` (a mapping action → next state), and `utility(state)`. The running example throughout the lecture is **game 1**: the agent picks bin A, B, or C; the opponent picks a number from that bin; utilities are A: −50, 50; B: 1, 3; C: −5, 15 ([≈3:10](../raw/transcripts/10-games-i.md)). The opening poll — most of the class chose C — sets up the lecture's central question: the right choice **depends on what you assume about the opponent** ([≈0:51](../raw/transcripts/10-games-i.md), [≈23:30](../raw/transcripts/10-games-i.md)).

Two characteristics distinguish games from MDPs ([≈7:04](../raw/transcripts/10-games-i.md)): all the utility sits at the end state (a **sparse reward** problem, in RL terms — "you might seem like you're doing really well and then everything crumbles", [≈7:51](../raw/transcripts/10-games-i.md)), and different players control different states. Policies are as in MDPs, per player: deterministic $\pi_p(s)$ picks an action, stochastic $\pi_p(a \mid s)$ gives a distribution ([≈8:36](../raw/transcripts/10-games-i.md)). The second running example is the **halving game** (see [minimax](minimax.md)): players take turns decrementing $n$ or replacing it with $\lfloor n/2 \rfloor$; whoever is left with 0 wins ([≈8:36](../raw/transcripts/10-games-i.md), [≈9:23](../raw/transcripts/10-games-i.md)).

## Game Evaluation

Given a game and a policy for each player, the **value** of the game is the expected utility over all rollouts ([≈10:56](../raw/transcripts/10-games-i.md)). The `simulate` function plays a rollout exactly as in search and MDPs: while the game isn't over, sample an action from the current player's policy, advance through `successors`, and read the utility at the end ([≈12:33](../raw/transcripts/10-games-i.md)). With the agent always choosing A and the opponent flipping between 1 and 2, one traced rollout ends in A2 with utility 50 ([≈13:21](../raw/transcripts/10-games-i.md)); 20 rollouts average **15** — which is *not* the true value, just sampling error. A million rollouts would converge to the true value 0, since half the time you get −50 and half 50 ([≈14:10](../raw/transcripts/10-games-i.md)).

The exact computation is the **evaluation recurrence** ([≈14:55](../raw/transcripts/10-games-i.md), [≈15:44](../raw/transcripts/10-games-i.md)):

$$V_{eval}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \sum_{a} \pi_{agent}(s, a) \, V_{eval}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \sum_{a} \pi_{opp}(s, a) \, V_{eval}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

Filled in bottom-up on game 1: the opponent nodes get $0.5\cdot(-50) + 0.5\cdot 50 = 0$, $0.5\cdot 1 + 0.5\cdot 3 = 2$, $0.5\cdot(-5) + 0.5\cdot 15 = 5$, and the root gets $1\cdot 0 + 0\cdot 2 + 0\cdot 5 = 0$ ([≈18:06](../raw/transcripts/10-games-i.md), [≈18:52](../raw/transcripts/10-games-i.md)). The computation is exact but takes exponential time — two actions per node for 100 steps is $2^{100}$ ([≈17:17](../raw/transcripts/10-games-i.md)). The analogue in MDPs is [policy evaluation](policy-evaluation.md) ([≈19:37](../raw/transcripts/10-games-i.md)).

## Expectimax: Optimal Against a Known Opponent

**Expectimax** keeps the opponent's policy *fixed* and optimizes the agent's ([≈20:24](../raw/transcripts/10-games-i.md)). The recurrence changes one line from $V_{eval}$: at the agent's turn, take the **max** over successor values instead of a policy-weighted sum ([≈21:12](../raw/transcripts/10-games-i.md), [≈21:58](../raw/transcripts/10-games-i.md)). Max nodes are drawn as upward triangles, fixed-policy nodes as circles ([≈21:12](../raw/transcripts/10-games-i.md)).

Against a random opponent, the expectimax values are A: 0, B: 2, C: 5, so the root takes the max → **5, choose C** — the same C most of the class chose, assuming the lecturer would flip a coin and be lenient ([≈22:44](../raw/transcripts/10-games-i.md), [≈23:30](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)). Expectimax is analogous to [value iteration](value-iteration.md) in MDPs ([≈25:06](../raw/transcripts/10-games-i.md)).

One subtlety: expectimax cannot be estimated by naive Monte Carlo sampling, because of the max — sampling gives unbiased estimates of *sums*, but a max of sampled estimates is biased ([≈21:58](../raw/transcripts/10-games-i.md)).

## Minimax: Assume the Worst

The whole point of games is that the opponent policy is **not** known ([≈25:54](../raw/transcripts/10-games-i.md)). **Minimax** is the principle that assumes the opponent plays their best strategy — out to get you, minimizing your utility ([≈25:54](../raw/transcripts/10-games-i.md)). The recurrence: max at agent turns, min at opponent turns ([≈26:40](../raw/transcripts/10-games-i.md)):

$$V_{minmax}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \max_a V_{minmax}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \min_a V_{minmax}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

On game 1 the opponent nodes minimize to −50, 1, −5; the agent maximizes → **1, choose B** — the bin the lecturer was surprised more people didn't pick ([≈27:25](../raw/transcripts/10-games-i.md), [≈1:37](../raw/transcripts/10-games-i.md)). In code, $V_{minmax}$ also returns the associated action, not just the value, since you want to know what to *do* ([≈28:11](../raw/transcripts/10-games-i.md)).

The halving game from n = 11 shows minimax in action: the agent decrements to 10, and wins every one of 10 rollouts against a random opponent ([≈29:45](../raw/transcripts/10-games-i.md), [≈30:33](../raw/transcripts/10-games-i.md)). The traced minimax values for n = 1..11 are 1 for n ∈ {2, 3, 5, 7, 8, 9, 11} and −1 for n ∈ {1, 4, 6, 10} ([≈31:21](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)). Value 1 means the agent is **guaranteed** to win no matter what the opponent does; −1 means the opponent wins *if* they play optimally — a weaker opponent can still lose ([≈32:08](../raw/transcripts/10-games-i.md)). Full treatment, including **perfect play** and **solved games**, on the [minimax](minimax.md) page.

## Face-off: Optimal With Respect to What?

The recurrences produce four policies — $\pi_{max}$ and $\pi_{min}$ from minimax, $\pi_{exptmax(7)}$ and $\pi_7$ from expectimax against a fixed opponent policy $\pi_7$ — and each is "optimal" only against the assumption baked into it ([≈36:01](../raw/transcripts/10-games-i.md), [≈36:47](../raw/transcripts/10-games-i.md)). Letting $V(\pi_{agent}, \pi_{opp})$ be the value when the row policy plays the column policy, the face-off matrix on game 1 is ([≈37:34](../raw/transcripts/10-games-i.md), [≈44:33](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)):

| | $\pi_{min}$ | $\pi_7$ |
| --- | --- | --- |
| $\pi_{max}$ | **1** | 2 |
| $\pi_{exptmax(7)}$ | −5 | **5** |

Three properties ([≈39:07](../raw/transcripts/10-games-i.md)–[≈43:46](../raw/transcripts/10-games-i.md)):

1. **$\pi_{max}$ is the best policy against $\pi_{min}$** — substituting any other agent policy can only decrease the value ([≈39:07](../raw/transcripts/10-games-i.md), [≈39:55](../raw/transcripts/10-games-i.md)).
2. **$\pi_{min}$ is the best policy against $\pi_{max}$** — if you play $\pi_{max}$, the minimax opponent is the worst you will do; any other opponent can only help. This is the **lower-bound guarantee**: if the minimax value is 1, you are guaranteed to win no matter what the opponent does ([≈40:41](../raw/transcripts/10-games-i.md), [≈41:26](../raw/transcripts/10-games-i.md), [≈42:13](../raw/transcripts/10-games-i.md)).
3. **$\pi_{exptmax(7)}$ is optimal against $\pi_7$** — so minimax is *not* optimal if you know your opponent: against the known random $\pi_7$, expectimax plays C and gets 5, while minimax plays B and gets only 2 ([≈43:01](../raw/transcripts/10-games-i.md), [≈43:46](../raw/transcripts/10-games-i.md)). You can do better than minimax against a weak opponent — you just need to know they're weak ([≈44:33](../raw/transcripts/10-games-i.md)).

The four values chain neatly: **−5 ≤ 1 ≤ 2 ≤ 5** — "it's really cool how all these values are actually kind of related to each other" ([≈44:33](../raw/transcripts/10-games-i.md), [≈45:20](../raw/transcripts/10-games-i.md)).

## Expectiminimax: Add a Coin

Game 2 adds randomness: after the agent picks a bin, the opponent **flips a coin**; heads moves one bin to the left (wrapping around: A→C, B→A, C→B), tails stays ([≈46:06](../raw/transcripts/10-games-i.md), [≈46:52](../raw/transcripts/10-games-i.md)). The game tree gains a third node type — a **chance node** played by the fixed policy $\pi_{coin}$ (0.5/0.5) — and the recurrence gains a third case: expectation over $\pi_{coin}$ at chance nodes ([≈47:38](../raw/transcripts/10-games-i.md), [≈48:25](../raw/transcripts/10-games-i.md), [≈49:11](../raw/transcripts/10-games-i.md)). The chance nodes average to −27.5, −24.5, −2, so the value is **−2, choose C** — the coin makes game 2 far worse than game 1 even with optimal play ([slides](../raw/slides/10-games-i.md)).

The framework extends to multi-player games (the Pac-Man assignment: one Pac-Man, multiple ghosts) and games where players take extra turns or choose who goes next — the player whose turn it is lives in the state, so any transition rule is representable ([≈49:11](../raw/transcripts/10-games-i.md), [≈49:57](../raw/transcripts/10-games-i.md)). What game trees *cannot* represent: **imperfect information** (poker — different players knowing different things breaks the shared-state assumption), **non-zero-sum games** (prisoner's dilemma), and **non-turn-based games** (rock-paper-scissors — simultaneous moves) ([≈50:43](../raw/transcripts/10-games-i.md), [≈51:30](../raw/transcripts/10-games-i.md)). Those come [next lecture](11-games-ii.md).

## Alpha-Beta Pruning: Exact Speedup

Minimax takes exponential time ([≈52:16](../raw/transcripts/10-games-i.md)). **Alpha-beta pruning** skips states that cannot possibly be optimal, using **branch and bound** — the general combinatorial-optimization idea of maintaining intervals and pruning anything strictly worse ([≈53:05](../raw/transcripts/10-games-i.md), [≈53:52](../raw/transcripts/10-games-i.md)). The motivating one-move example: if A's value lies in [3, 5] and B's in [5, 100], B wins without ever computing either ([≈53:05](../raw/transcripts/10-games-i.md)). In tree form: once the left subtree gives 3, the root is $\max(3, \min(2, X)) = 3$ no matter what $X$ is — so $X$ is never explored ([≈54:38](../raw/transcripts/10-games-i.md), [≈55:25](../raw/transcripts/10-games-i.md)). The full mechanics — α lower bounds on max nodes, β upper bounds on min nodes, the pruning criterion that bounds must overlap every ancestor's, and the worked 9, 7 / 6, [3, 4] [7, 9] / 8, 3 board example — are on the [alpha-beta pruning](alpha-beta-pruning.md) page.

Two closing notes from the section: pruning can remove an **entire subtree**, not just one node ([≈1:02:23](../raw/transcripts/10-games-i.md)); and it does **not** work for expectimax, because expectations must consider every possibility ([≈1:02:23](../raw/transcripts/10-games-i.md)). (A student asks how the lower bound "≥ 7" was obtained — the answer: you update the max as you come back up from each child, bottom-up from the finished subtree ([≈1:03:09](../raw/transcripts/10-games-i.md)).)

**Ordering** determines how much you prune: with children 3, 5, 2, 10 the 10 gets pruned, but with 2, 10, 3, 5 nothing does ([≈1:03:55](../raw/transcripts/10-games-i.md)). The practical rule — order children by decreasing value at max nodes and increasing at min nodes, shrinking bounds fastest ([≈1:04:41](../raw/transcripts/10-games-i.md), [≈1:05:27](../raw/transcripts/10-games-i.md)) — echoes A*: a heuristic makes an exact algorithm fast, never wrong ([≈1:05:27](../raw/transcripts/10-games-i.md)).

## Evaluation Functions: Approximate Speedup

The second speedup stops the search early and **guesses**: an **evaluation function** captures prior knowledge about how good a state looks without playing to the end ([≈1:06:14](../raw/transcripts/10-games-i.md), [≈1:07:01](../raw/transcripts/10-games-i.md)). Chess is the case study: $\text{Eval}(s) = \text{material} + \text{mobility} + \text{king-safety} + \text{center-control}$, with Deep-Blue-style weights — kings $10^{100}$, queens 9, rooks 5, bishops/knights 3, pawns 1, mobility 0.1 ([≈1:07:47](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)). Greedily taking the move with the best Eval would be a terrible player; the right use is **depth-limited search**: run minimax to depth d, and when d hits 0, "vibe-check" the state with Eval instead of recursing ([≈1:08:33](../raw/transcripts/10-games-i.md), [≈1:09:20](../raw/transcripts/10-games-i.md)). No optimality guarantee — it's a complete heuristic, and the deeper you search the less sensitive you are to a bad evaluation function ([≈1:10:07](../raw/transcripts/10-games-i.md)). The analogy is **FutureCost** in [search problems](search-problems.md) ([≈1:10:54](../raw/transcripts/10-games-i.md)), and the pointer forward: an evaluation function is a Q-value in disguise, and it **can be learned** — from supervised data or RL — which is exactly [next lecture](11-games-ii.md) ([≈1:11:40](../raw/transcripts/10-games-i.md), [≈1:13:14](../raw/transcripts/10-games-i.md)). See [evaluation functions](evaluation-functions.md).

---

- **[Slides](../raw/slides/10-games-i.md)** — the full `games.py` content with every traced demo output, plus vision-pass descriptions of all 27 figures. Rendered lecture: <https://stanford-cs221.github.io/autumn2025-lectures/?trace=games>.
- **[Transcript](../raw/transcripts/10-games-i.md)** — edited, timestamped. Verbatim captions in `original/`.
- Topic pages: [games](games.md), [minimax](minimax.md), [alpha-beta pruning](alpha-beta-pruning.md), [evaluation functions](evaluation-functions.md).
