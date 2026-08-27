---
title: Heuristics from Relaxation — How to Build Consistent Heuristics for A*
---

# Heuristics from Relaxation

An [A* search](a-star-search.md) heuristic $h$ must be **consistent**, which is a property you'd rather not verify by hand for every new idea. This page holds the lecture-6 recipe that makes consistency **automatic**: build $h$ as the **future cost of a relaxed problem** ([≈56:55](../raw/transcripts/06-search-ii.md), [≈57:41](../raw/transcripts/06-search-ii.md)). Intuition first: $h$ wants to be $\text{FutureCost}$, but that is as hard as the original problem — so compute the future cost of an *easier* problem instead ([≈58:28](../raw/transcripts/06-search-ii.md)).

## The Winning Recipe

1. Define a **relaxed problem** by getting rid of some constraints.
2. Compute $\text{FutureCost}_\text{relaxed}(s)$ for the relaxed problem — it must be easier to solve than the original.
3. Run A* with $h(s) = \text{FutureCost}_\text{relaxed}(s)$.

## What a Relaxation Is

Formally, a **relaxation** of a search problem keeps the states, actions, and successors identical and only reduces the costs ([≈1:14:01](../raw/transcripts/06-search-ii.md), [≈1:14:48](../raw/transcripts/06-search-ii.md)):

$$\text{Cost}_\text{relaxed}(s, a) \leq \text{Cost}(s, a)$$

Removing a constraint *is* reducing a cost — often from infinity to a finite value. A wall in the grid problem is "cost infinity to enter"; knocking the wall down sets that cost to 1 ([≈1:14:48](../raw/transcripts/06-search-ii.md)). Restating relaxation as cost reduction is what unifies the examples below.

**The theorem** ([≈1:15:34](../raw/transcripts/06-search-ii.md)): $h(s) = \text{FutureCost}_\text{relaxed}(s)$ is consistent. Three-line proof:

$$h(s) \;\leq\; \text{Cost}_\text{relaxed}(s,a) + h(\text{Succ}(s,a)) \;\leq\; \text{Cost}(s,a) + h(\text{Succ}(s,a))$$

The first inequality is the triangle inequality for the relaxed future cost (going through action $a$ can only cost more than the optimal relaxed path); the second is the definition of relaxation. So consistency comes free with every relaxed problem — "if you can interpret [a heuristic] as solving an easier problem then you get consistency for free and you don't have to do math" ([≈1:01:34](../raw/transcripts/06-search-ii.md)).

## Three Ways Relaxation Makes a Problem Easier

The relaxed problem must actually be *easier*. Three structured ways ([≈1:17:06](../raw/transcripts/06-search-ii.md)):

### 1. Closed-form solution

**Knock down the walls** in the grid problem. Without walls the future cost is the **Manhattan distance** to the end state ([≈59:13](../raw/transcripts/06-search-ii.md)):

$$h((r, c)) = |r_\text{end} - r| + |c_\text{end} - c|$$

No search needed at all ([≈59:59](../raw/transcripts/06-search-ii.md)). On the 5×5 demo: $h((0,0)) = 8$, $h((0,1)) = 7$, $h((2,4)) = 2$, $h((3,0)) = 5$ ([slides](../raw/slides/06-search-ii.md)). The heuristic is imperfect: (2,4) *looks* close but the walls force a detour, while (3,0) looks farther than it is — but the point is it consistently favors states nearer E ([≈1:00:48](../raw/transcripts/06-search-ii.md)). Honest demo result: A* and UCS both explore 17 states — on this easy problem the heuristic buys nothing ([≈1:01:34](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)).

### 2. Fewer states

**Free tram**: in the limited travel problem the state is (loc, tickets) — the ticket count multiplies the state space (1,000 locations × 100 tickets = 100× states) ([≈1:03:06](../raw/transcripts/06-search-ii.md)). Make the tram free and the tickets disappear from the state; dynamic programming solves the relaxed problem in **10 states** ([≈1:03:52](../raw/transcripts/06-search-ii.md)). The heuristic maps an original state to a relaxed one by **projection** — (loc, tickets) → loc ([≈1:04:37](../raw/transcripts/06-search-ii.md)). Result: UCS explores 23 states, A* explores **8** — and adding the 10 for the relaxed solve, 18 vs 23, still a win ([≈1:05:25](../raw/transcripts/06-search-ii.md), [≈1:06:13](../raw/transcripts/06-search-ii.md)).

### 3. Independent subproblems

**Tiles may overlap**: in the 8-puzzle, tiles cannot share a cell; relaxing that constraint lets every tile move as if alone, so the problem breaks into 8 independent single-tile problems, each solvable in closed form ([≈1:11:43](../raw/transcripts/06-search-ii.md)). The heuristic is the **sum of the per-tile Manhattan distances** to their goal positions — for the demo state, 1+1+3+1+1+1+1+3 = **12** ([≈1:11:43](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)). This pattern applies generally whenever the state is a collection of objects with constraints between them ([≈1:13:16](../raw/transcripts/06-search-ii.md)).

## Practical Details

- **Account for the relaxed solve.** "A* explores 8 vs UCS's 23" is only fair after adding the 10 states spent solving the relaxed problem: 18 vs 23 ([≈1:06:13](../raw/transcripts/06-search-ii.md)). A student asked exactly this; the lecturer had done it and nearly forgotten to show it ([≈1:07:46](../raw/transcripts/06-search-ii.md)).
- **Relaxed problem with cycles?** Dynamic programming can't solve it either. The trick is to **reverse** the relaxed problem's edges: past costs in the reversed problem equal future costs in the original, so UCS on the reversed problem computes the heuristic ([≈1:09:23](../raw/transcripts/06-search-ii.md), [≈1:10:10](../raw/transcripts/06-search-ii.md)).
- **Not any cost reduction works.** Wiggling costs arbitrarily doesn't make a problem easier; reduce them *structurally* so the relaxed problem has fewer states, a closed form, or independent subproblems ([≈1:17:06](../raw/transcripts/06-search-ii.md)). When it works, you get "99% of the way there" from a cheap relaxed solve and let A* finish the job ([≈1:12:29](../raw/transcripts/06-search-ii.md)).
- **Two heuristics, no choosing.** If $h_1$ and $h_2$ are consistent, so is $h(s) = \max(h_1(s), h_2(s))$ — use all your domain knowledge at once ([≈1:17:54](../raw/transcripts/06-search-ii.md)). The proof (skipped in lecture): consistency gives $h_i(s) \leq \text{Cost}(s,a) + h_i(\text{Succ}(s,a))$, and the max of two things each bounded by the same quantity is bounded by it ([slides](../raw/slides/06-search-ii.md)).

## Related Pages

- [A* search](a-star-search.md) — the consumer of heuristics, with consistency and admissibility
- [Uniform cost search](uniform-cost-search.md) — the engine underneath
- [Search problems](search-problems.md) — the abstraction being relaxed
- [Lecture 6](06-search-ii.md) — all three worked relaxations with demo numbers
