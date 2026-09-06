---
title: Evaluation Functions — Approximate Speedup for Games
---

# Evaluation Functions

An **evaluation function** is the approximate half of the minimax-speedup pair (the exact half is [alpha-beta pruning](alpha-beta-pruning.md)): instead of searching to the end of the game tree, search to a fixed depth and then **take a wild guess** about how good the state is ([≈1:06:14](../raw/transcripts/10-games-i.md), [lecture 10](10-games-i.md)). It is prior knowledge — a set of features, really — about what tends to matter, applied where search stops.

## What an Evaluation Function Looks Like

In a game, the only thing that ultimately matters is who wins at the end — and yet, looking at a chess position, you often know who's doing well ([≈1:07:01](../raw/transcripts/10-games-i.md)). The evaluation function captures that prior knowledge as a sum of terms, each encoding an intuitive property ([≈1:07:47](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)):

$$\text{Eval}(s) = \text{material} + \text{mobility} + \text{king-safety} + \text{center-control}$$

with Deep-Blue-style weights on material — unprimed letters are the agent's pieces, primed the opponent's ([≈1:07:47](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)):

$$\text{material} = 10^{100}(K - K') + 9(Q - Q') + 5(R - R') + 3(B - B' + N - N') + 1(P - P')$$

Kings are worth essentially everything ($10^{100}$), queens 9, rooks 5, bishops and knights 3, pawns 1; mobility is a 0.1-weighted difference in legal moves ([≈1:07:47](../raw/transcripts/10-games-i.md)).

## Depth-Limited Search

Greedily playing the move with the best Eval would be a terrible chess player — too greedy, hoarding pieces ([≈1:08:33](../raw/transcripts/10-games-i.md)). The right use is **depth-limited search**: minimax with a depth budget $d$, and when the budget runs out, call Eval instead of recursing ([≈1:08:33](../raw/transcripts/10-games-i.md), [≈1:09:20](../raw/transcripts/10-games-i.md)):

$$V_{minmax}(s, d) = \begin{cases} \text{Utility}(s) & \text{IsEnd}(s) \\ \text{Eval}(s) & d = 0 \\ \max_a V_{minmax}(\text{Succ}(s, a), d) & \text{Player}(s) = \text{agent} \\ \min_a V_{minmax}(\text{Succ}(s, a), d - 1) & \text{Player}(s) = \text{opp} \end{cases}$$

The depth decrements only at the opponent's turn, so that **one unit of depth corresponds to both the agent and the opponent going** — a full round, rather than a single player's move ([≈1:09:20](../raw/transcripts/10-games-i.md), [≈1:10:07](../raw/transcripts/10-games-i.md)).

**No optimality guarantee** — this is a complete heuristic, and its effectiveness depends on the evaluation function's quality ([≈1:10:07](../raw/transcripts/10-games-i.md)). The depth gives a control knob: the **deeper you search, the less sensitive you are to a bad evaluation function**; a shallow search had better have a good one ([≈1:10:07](../raw/transcripts/10-games-i.md)).

## Where the Idea Leads

The analogy to [search problems](search-problems.md) is **FutureCost**: look a step forward, then approximate the rest ([≈1:10:54](../raw/transcripts/10-games-i.md)). And the pointer into [reinforcement learning](reinforcement-learning.md): an evaluation function is a **Q-value in disguise** — an approximation of the true value of a state — which means it *can be learned*: from supervised data, or from RL. That is exactly what the next lecture does, via **TD learning** ([≈1:11:40](../raw/transcripts/10-games-i.md), [≈1:13:14](../raw/transcripts/10-games-i.md)).
