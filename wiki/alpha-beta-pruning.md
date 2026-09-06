---
title: Alpha-Beta Pruning — Exact Speedup for Minimax
---

# Alpha-Beta Pruning

**Alpha-beta pruning** speeds up [minimax](minimax.md) search **exactly**: don't visit states that are known not to be optimal ([≈52:16](../raw/transcripts/10-games-i.md), [lecture 10](10-games-i.md)). The underlying idea is **branch and bound**, used heavily in combinatorial optimization: maintain intervals that bound where values could lie, and prune anything an interval proves strictly worse ([≈53:05](../raw/transcripts/10-games-i.md), [≈53:52](../raw/transcripts/10-games-i.md)).

## Why It Works: Bounds

The one-move intuition: action A will pay somewhere in [3, 5], action B somewhere in [5, 100] — B is chosen without computing either exactly, because no matter what they turn out to be, B is at least as good ([≈53:05](../raw/transcripts/10-games-i.md)). In tree form: suppose minimax search has found the left subtree gives 3. The root computes $\max(3, \min(2, X)) = 3$ **no matter what X is** — whether X is a million or minus a million, the right min node is ≤ 2 and the root stays 3 — so X is never explored ([≈54:38](../raw/transcripts/10-games-i.md), [≈55:25](../raw/transcripts/10-games-i.md)).

The general bookkeeping ([≈56:11](../raw/transcripts/10-games-i.md), [≈1:00:05](../raw/transcripts/10-games-i.md)):

- **α (alpha)** — at a **max node**, a **lower bound** on its value: a discovered value can only increase ([≈56:11](../raw/transcripts/10-games-i.md), [≈1:00:05](../raw/transcripts/10-games-i.md)).
- **β (beta)** — at a **min node**, an **upper bound** on its value: a discovered value can only decrease ([≈56:11](../raw/transcripts/10-games-i.md), [≈1:00:52](../raw/transcripts/10-games-i.md)).

The minimax value must come from some leaf, and the **optimal path** — the path the minimax policies take to that leaf — must lie within every ancestor's bounds ([≈56:57](../raw/transcripts/10-games-i.md)). The pruning criterion follows: **prune a node if its bounds don't overlap every ancestor's bounds** — then it can't be on the optimal path ([≈57:44](../raw/transcripts/10-games-i.md)). Example: a node known ≥ 6 sitting under an ancestor known ≤ 5 — the intervals don't overlap, so prune ([≈58:32](../raw/transcripts/10-games-i.md)).

## The Worked Example

The lecturer works a board example through depth-first ([≈59:17](../raw/transcripts/10-games-i.md)–[≈1:01:37](../raw/transcripts/10-games-i.md); tree confirmed against the lecture figure [slides](../raw/slides/10-games-i.md)): a max root over three min nodes with leaves 9, 7 | 6, [max: 3, 4], [max: 7, 9] | 8, 3.

1. Enter the left min node: discover 9 → "at most 9"; discover 7 → "at most 7" — actually equal to 7, all children seen. The root's α becomes **≥ 7** ([≈59:17](../raw/transcripts/10-games-i.md), [≈1:00:05](../raw/transcripts/10-games-i.md)).
2. Middle min node: discover 6 → "at most 6". 6 ≤ 7 does **not** overlap the root's α = 7 — the entire rest of the middle subtree is **pruned** without looking ([≈1:00:52](../raw/transcripts/10-games-i.md), [≈1:01:37](../raw/transcripts/10-games-i.md)).
3. Right min node: discover 8 → "at most 8", which still overlaps 7 — must explore; discover 3 → "at most 3". Final value: **7** ([≈1:01:37](../raw/transcripts/10-games-i.md)).

(The unpruned minimax values would be min(9,7) = 7, min(6, max(3,4), max(7,9)) = 4, min(8,3) = 3 — root max = 7, and the pruning skipped exactly the subtrees that couldn't beat 7.)

A student question at this point: *how do you get the lower bound "≥ 7"?* — by updating the max as you return from each child, bottom-up: once a subtree is fully explored, its value is exact and becomes the running bound ([≈1:03:09](../raw/transcripts/10-games-i.md)).

## Ordering Determines How Much You Prune

The order in which children are visited changes what gets pruned ([≈1:03:55](../raw/transcripts/10-games-i.md)). With a min node's children visited as 3, 5, 2, 10, the 10 gets pruned; visited as 2, 10, 3, 5, nothing does — the first child 2 sets a weak bound ([≈1:03:55](../raw/transcripts/10-games-i.md), [slides](../raw/slides/10-games-i.md)). The practical rule ([≈1:04:41](../raw/transcripts/10-games-i.md), [≈1:05:27](../raw/transcripts/10-games-i.md)):

- at **max nodes**, visit children in **decreasing** expected value,
- at **min nodes**, visit children in **increasing** expected value —

so bounds shrink as fast as possible, and in practice a heuristic (an [evaluation function](evaluation-functions.md)) supplies the ordering ([≈1:05:27](../raw/transcripts/10-games-i.md)). The parallel to A* [heuristics](heuristics.md) is deliberate: a heuristic makes an exact algorithm fast when it's good, and merely slow when it's bad — never wrong ([≈1:05:27](../raw/transcripts/10-games-i.md)).

## What It Is and Isn't

Alpha-beta pruning keeps the exact minimax answer while visiting fewer nodes — a lucky ordering can skip **entire subtrees**, not just single nodes ([≈1:02:23](../raw/transcripts/10-games-i.md)). But the worst case remains exponential time — it has not "solved P = NP" ([≈1:06:14](../raw/transcripts/10-games-i.md)). And it does **not** work for [expectimax](games.md#expectimax-optimal-against-a-known-opponent): an expectation must consider every possible outcome, though bounded-utility versions may allow limited pruning ([≈1:02:23](../raw/transcripts/10-games-i.md)).
