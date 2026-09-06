---
title: Minimax — Optimal Play Against an Optimal Opponent
---

# Minimax

**Minimax** is the principle for games when you don't know the opponent's strategy: **assume the opponent plays their best possible strategy** — out to get you, minimizing your utility — and play optimally against that ([≈25:54](../raw/transcripts/10-games-i.md), [lecture 10](10-games-i.md)). It is the part of the games module with no analogue in MDPs: MDPs only max (agent) and average (environment), while minimax introduces a **min** ([≈34:28](../raw/transcripts/10-games-i.md)).

## The Recurrence

Upward triangles denote max nodes (agent's turn), downward triangles min nodes (opponent's turn) ([≈26:40](../raw/transcripts/10-games-i.md)):

$$V_{minmax}(s) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \max_a V_{minmax}(\text{Succ}(s, a)) & \text{Player}(s) = \text{agent} \\ \min_a V_{minmax}(\text{Succ}(s, a)) & \text{Player}(s) = \text{opp} \end{cases}$$

On game 1 (bins A, B, C with numbers −50/50, 1/3, −5/15), the opponent nodes minimize to −50, 1, −5 and the agent maximizes → **value 1, choose B** ([≈27:25](../raw/transcripts/10-games-i.md)). In the opening poll, the B-choosers were the ones running minimax in their heads ([≈27:25](../raw/transcripts/10-games-i.md)); C was the [expectimax](games.md#expectimax-optimal-against-a-known-opponent) answer, optimal only if you assume the opponent plays randomly ([≈23:30](../raw/transcripts/10-games-i.md)). The code returns the value **and the associated action**, since the point is to know what to do ([≈28:11](../raw/transcripts/10-games-i.md)). A minimax policy is then just: compute $V_{minmax}(s)$ and always play the associated action ([≈29:45](../raw/transcripts/10-games-i.md)).

## The Halving Game

In the **halving game**, two players take turns either decrementing $n$ or replacing it with $\lfloor n/2 \rfloor$; the player left with 0 wins ([≈8:36](../raw/transcripts/10-games-i.md), [≈9:23](../raw/transcripts/10-games-i.md)). From n = 11 the minimax agent decrements to 10 — and crushes a random opponent, winning all 10 simulated games ([≈29:45](../raw/transcripts/10-games-i.md), [≈30:33](../raw/transcripts/10-games-i.md)). The minimax values for every starting n from the agent's perspective, traced from the lecture ([≈31:21](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)):

| n | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| value | −1 | +1 | +1 | −1 | +1 | −1 | +1 | +1 | +1 | −1 | +1 |
| action | dec | dec | half | dec | dec | dec | dec | half | half | dec | dec |

Value +1 means the agent is **guaranteed to win no matter what the opponent does**; value −1 means the opponent wins *if they play optimally* — against a weaker opponent you may still win ([≈32:08](../raw/transcripts/10-games-i.md), [≈32:54](../raw/transcripts/10-games-i.md)). The guarantee is the formal content of the face-off properties: any opponent other than $\pi_{min}$ can only raise your value, so the minimax value is a lower bound against every opponent ([≈40:41](../raw/transcripts/10-games-i.md), [≈41:26](../raw/transcripts/10-games-i.md), [≈42:13](../raw/transcripts/10-games-i.md); full matrix on the [games](games.md#face-off-optimality-is-relative) page).

## Perfect Play and Solved Games

When both players play optimally, that is **perfect play** ([≈32:54](../raw/transcripts/10-games-i.md)). A game is **solved** when the outcome under perfect play — the minimax value of the root — is known ([≈33:40](../raw/transcripts/10-games-i.md)):

- **Strongly solved** — the minimax value is known for every state: tic-tac-toe, nim, connect four ([≈33:40](../raw/transcripts/10-games-i.md)).
- **Weakly solved** — known from the initial position: checkers, Othello ([≈33:40](../raw/transcripts/10-games-i.md)).
- **Unsolved** — chess and Go, even though computers beat humans: being better than humans is not the same as knowing the minimax value and optimal policy ([≈33:40](../raw/transcripts/10-games-i.md), [≈34:28](../raw/transcripts/10-games-i.md)).

## Limits

Minimax is optimal against the optimal opponent — which is exactly why it can be beaten in practice: if you *know* your opponent plays randomly, [expectimax](games.md#expectimax-optimal-against-a-known-opponent) plays C for 5 while minimax settles for B and 2 ([≈43:01](../raw/transcripts/10-games-i.md), [≈43:46](../raw/transcripts/10-games-i.md)). And the recurrence is exponential-time in the depth, which is what [alpha-beta pruning](alpha-beta-pruning.md) (exact) and [evaluation functions](evaluation-functions.md) (approximate) are for.
