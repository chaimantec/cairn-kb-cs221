---
title: Nash Equilibrium — Stable Strategies in Non-Zero-Sum Games
---

# Nash Equilibrium

In a **non-zero-sum** game the players' utilities are arbitrary rather than summing to zero, so one player's gain is not simply the other's loss ([lecture 11](11-games-ii.md), [≈1:05:33](../raw/transcripts/11-games-ii.md), [≈1:06:18](../raw/transcripts/11-games-ii.md)). Von Neumann's [minimax theorem](simultaneous-games.md#von-neumanns-minimax-theorem) no longer applies, but something weaker does: the **Nash equilibrium**, a pair of strategies neither player wants to move away from. The course covers it as "a brief overview, just to get you some exposure" ([≈1:05:33](../raw/transcripts/11-games-ii.md)).

## Competitive, Cooperative, and In Between

The lecture places games on a spectrum by how the players' utilities relate ([≈1:06:18](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). **Competitive** games are zero-sum ("if you win, I lose"), solved by [minimax](minimax.md) via linear programming or search. In **cooperative** games every player has the same utility, so "there's nothing really to be said there": it is pure maximization, just [search](search-problems.md). Real life is somewhere in between.

## The Prisoner's Dilemma

A prosecutor asks A and B separately whether each will testify against the other ([≈1:07:05](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)). If both testify, both get 5 years; if both refuse, both get 1 year; if only one testifies, that one goes free and the other gets 10 years. The payoff matrix now needs a utility per player, $V_p(\pi_A, \pi_B)$ for $p \in \lbrace A, B \rbrace$, here minus the years in jail ([≈1:07:54](../raw/transcripts/11-games-ii.md)):

| A \ B | testify | refuse |
| --- | --- | --- |
| testify | A: −5, B: −5 | A: 0, B: −10 |
| refuse | A: −10, B: 0 | A: −1, B: −1 |

## Definition and Existence

A **Nash equilibrium** is a pair of strategies $(\pi_A^\ast, \pi_B^\ast)$ such that no player has an incentive to change strategy ([≈1:08:39](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

$$V_A(\pi_A^\ast, \pi_B^\ast) \ge V_A(\pi_A, \pi_B^\ast) \quad \text{for all } \pi_A$$

$$V_B(\pi_A^\ast, \pi_B^\ast) \ge V_B(\pi_A^\ast, \pi_B) \quad \text{for all } \pi_B$$

Each line holds the other player fixed: if A alone changes, A does no better, and likewise for B.

**Nash's existence theorem** (1950): any game with a finite number of players and a finite number of actions has **at least one** Nash equilibrium ([≈1:09:27](../raw/transcripts/11-games-ii.md)). The strategies may be mixed, as the Morra example shows.

## Three Examples

From the lecture ([≈1:09:27](../raw/transcripts/11-games-ii.md)–[≈1:11:46](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

1. **Two-finger Morra** (zero-sum: A gets 2 or 4 on a match, and pays 3 on a mismatch). The minimax strategy $[7/12, 5/12]$ for both players is also a Nash equilibrium, since neither has an incentive to move off it.
2. **Collaborative two-finger Morra**, where both players receive A's Morra payoff (2 each on both-1, 4 each on both-2, −3 each otherwise). There are **two** equilibria: both play 1, or both play 2. Both-play-1 is not optimal, since both-play-2 is better for everyone, but it is stable, because a player who moves off it alone makes things worse. "So this is a local optimum."
3. **Prisoner's dilemma.** The only equilibrium is that **both testify**. Both refusing is not stable, since each player would rather switch to testifying and go free, and the off-diagonal cells are not stable either, since the player getting 10 years would switch.

The prisoner's dilemma is the cautionary case: "if you have agents that are only after their own selfish interest, you can get into situations which are suboptimal." Both refusing (−1 each) is clearly better for both than both testifying (−5 each), yet it is not an equilibrium ([≈1:10:59](../raw/transcripts/11-games-ii.md), [≈1:11:46](../raw/transcripts/11-games-ii.md)).

## Stability, Not Optimality

The lecture's contrast ([≈1:11:46](../raw/transcripts/11-games-ii.md), [slides](../raw/slides/11-games-ii.md)):

| | Simultaneous zero-sum | Simultaneous non-zero-sum |
| --- | --- | --- |
| Theorem | von Neumann's minimax theorem | Nash's existence theorem |
| Solutions | possibly several minimax strategies | possibly several Nash equilibria |
| Game value | a single value | possibly several values |

A Nash equilibrium is **locally stable**: no individual agent can improve by changing alone. It is not optimality in the sense the rest of the course pursues, and with players holding different utilities, "what does optimal with multi-objective even mean?" ([≈1:11:46](../raw/transcripts/11-games-ii.md), [≈1:12:32](../raw/transcripts/11-games-ii.md)). What the theory does guarantee is that equilibria exist for non-zero-sum games, though they need not be unique ([≈1:12:32](../raw/transcripts/11-games-ii.md)). The lecturer points to the "huge literature" in game theory and economics, and a game-theory course, for more ([≈1:13:19](../raw/transcripts/11-games-ii.md)).

## Related Pages

- [Lecture 11](11-games-ii.md) — the lecture page
- [Simultaneous games](simultaneous-games.md) — payoff matrices, mixed strategies, and the zero-sum minimax theorem
- [Games](games.md) — lecture 10's turn-based formalism, which flagged non-zero-sum games such as the prisoner's dilemma as outside what game trees model
