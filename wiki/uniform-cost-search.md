---
title: Uniform Cost Search — Dijkstra's Algorithm for Cyclic Search Problems
---

# Uniform Cost Search

**Uniform cost search (UCS)** is the exact search algorithm that handles **cycles** — the case where [dynamic programming](dynamic-programming.md)'s recurrence is undefined because there is no topological order in which to compute future costs ([≈4:40](../raw/transcripts/06-search-ii.md)). It is also known as **Dijkstra's algorithm** (Dijkstra, 1956) ([slides](../raw/slides/06-search-ii.md)). Introduced in [lecture 6](06-search-ii.md), it is the engine that [A* search](a-star-search.md) runs on.

## The Two Key Ideas

UCS makes two swaps relative to dynamic programming ([≈6:13](../raw/transcripts/06-search-ii.md), [≈6:59](../raw/transcripts/06-search-ii.md)):

1. Compute **past costs** instead of future costs: $\text{PastCost}(s)$ is the minimum cost of getting from the start state to $s$ ([≈2:21](../raw/transcripts/06-search-ii.md)).
2. Process states in **increasing past cost order** instead of topological order.

It trades DP's no-cycle assumption for a different one: **all costs must be non-negative**. With negative edges and cycles, a path that goes far out and comes back could be cheaper than any direct route, and UCS's "declare the frontier minimum done" step would be premature ([≈22:45](../raw/transcripts/06-search-ii.md)). The algorithm for negative edge weights with cycles is Bellman–Ford, which the course mentions but does not cover ([≈6:13](../raw/transcripts/06-search-ii.md)).

## The Algorithm

States are partitioned into **explored** (minimum-cost path known), **frontier** (seen, best path unknown), and **unexplored** ([≈7:45](../raw/transcripts/06-search-ii.md)). The frontier is a **priority queue** keyed by the best cost found so far ([≈10:06](../raw/transcripts/06-search-ii.md)). The loop ([slides](../raw/slides/06-search-ii.md)):

```python
while True:
    state, past_cost = frontier.remove_min()   # lowest priority first
    if state is None: return None, num_explored # frontier empty: no solution
    num_explored += 1
    if problem.is_end(state):
        return walk_back_backpointers(state)    # reconstruct the action sequence
    for successor in problem.successors(state):
        if frontier.update(successor.state, past_cost + successor.cost):
            backpointers[successor.state] = Backpointer(state, successor.action, successor.cost)
```

Two mechanisms deserve attention:

- **Updating in place**: when a successor is already on the frontier and a cheaper path to it is found, its priority is *updated*, not duplicated — and its **backpointer** changes to record the new predecessor ([≈14:45](../raw/transcripts/06-search-ii.md)). In the diamond example, C's priority goes 100 → 2 (via B) and D's goes 101 → 3 (via C) ([≈15:34](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)).
- **Stale heap entries**: the priority queue marks removed states with a `DONE` sentinel and skips any heap entry whose current priority disagrees, so an outdated entry (like D at 101) is discarded when it surfaces ([slides](../raw/slides/06-search-ii.md)).

The **backpointers** are what turn a cost into a *solution*: walking backward from the end state reconstructs the sequence of actions ([≈11:40](../raw/transcripts/06-search-ii.md), [≈17:08](../raw/transcripts/06-search-ii.md)). Without them you'd know the optimal cost but not how to achieve it.

## Correctness

**Theorem** ([≈22:45](../raw/transcripts/06-search-ii.md)): when UCS moves $s$ from frontier to explored, $\text{priority}(s) = \text{PastCost}(s)$ — the algorithm's bookkeeping equals the mathematical quantity. Because an end state is explored exactly when it leaves the frontier, the first end state popped is reached at minimum cost.

The proof is by induction on exploration order. When $s$ is popped, consider any alternative red path to $s$; it must cross from explored to frontier at some edge $t \to u$ ([≈24:18](../raw/transcripts/06-search-ii.md), [≈25:05](../raw/transcripts/06-search-ii.md)). Then

$$\text{cost(red)} \;\geq\; \text{PastCost}(t) + \text{Cost}(t,u) \;\geq\; \text{priority}(t) + \text{Cost}(t,u) \;\geq\; \text{priority}(u) \;\geq\; \text{priority}(s)$$

Step by step: the red path's prefix to $t$ costs at least $\text{PastCost}(t)$, and its tail costs at least 0 by non-negativity; $\text{PastCost}(t) = \text{priority}(t)$ by the inductive hypothesis; $t$'s expansion updated $u$'s priority to at most $\text{priority}(t) + \text{Cost}(t,u)$; and $s$ was chosen over $u$ for having the minimum priority in the frontier ([≈26:39](../raw/transcripts/06-search-ii.md)–[≈28:12](../raw/transcripts/06-search-ii.md)). So every alternative path costs at least as much as the blue path that set $\text{priority}(s)$ — priority($s$) is the true past cost.

## Behavior and Cost

The name is descriptive: the explored region expands **uniformly** in all directions from the start, because UCS has no knowledge of the end state — the [UCS video](https://www.youtube.com/watch?v=z6lUnb9ktkE) shows green explored, yellow frontier, white unexplored spreading in a circle until the goal is reached ([≈20:27](../raw/transcripts/06-search-ii.md), [≈21:13](../raw/transcripts/06-search-ii.md)).

On the 5×5 grid demo (17 free cells, unit costs), UCS explores **all 17 states** to find the cost-14 solution ([≈18:01](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)); on the diamond, 4 explored for 4 states ([slides](../raw/slides/06-search-ii.md)). UCS is optimal in the general case — "you can't do better" — because without domain knowledge, any algorithm that skips a state could be skipping the cheap route ([≈1:18:42](../raw/transcripts/06-search-ii.md)). The way to beat it is [A* search](a-star-search.md), which adds a heuristic — knowledge about the end state — on top of the same machinery.

## Related Pages

- [A* search](a-star-search.md) — UCS with modified costs
- [Heuristics from relaxation](heuristics.md) — where good heuristics come from
- [Search problems](search-problems.md) — the abstraction UCS operates on
- [Dynamic programming](dynamic-programming.md) — the acyclic counterpart
- [Lecture 6](06-search-ii.md) — introduction, worked diamond trace, correctness proof
