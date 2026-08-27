---
title: A* Search — Uniform Cost Search with a Heuristic
---

# A* Search

**A\*** (Hart, Nilsson, Raphael 1968) is [uniform cost search](uniform-cost-search.md) run on a search problem whose costs have been shifted by a **heuristic** — a function $h(s)$ that estimates the future cost of state $s$ ([≈33:40](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)). UCS explores uniformly in all directions because it knows nothing about the end state; A* gives it that knowledge without giving up the optimality guarantee. Introduced in [lecture 6](06-search-ii.md).

## The Idea: Order by PastCost + h(s)

UCS orders states by increasing $\text{PastCost}(s)$. Ideally you would order by $\text{PastCost}(s) + \text{FutureCost}(s)$ — states on a minimum-cost path always win that ordering ([≈32:53](../raw/transcripts/06-search-ii.md)). But the future cost of the start state *is* the problem, so that is circular. A* settles for an approximation ([≈33:40](../raw/transcripts/06-search-ii.md)):

$$\text{explore in order of } \text{PastCost}(s) + h(s)$$

## The Implementation: Modified Costs

A* never touches UCS; it wraps the problem ([≈39:00](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)). `ModifiedSearchProblem` keeps the same states, actions, and successors, and replaces each cost by

$$\text{Cost}'(s, a) = \text{Cost}(s, a) + \big[\, h(\text{Succ}(s,a)) - h(s) \,\big]$$

The bracket penalizes actions that move away from the end state: it is negative when the action moves closer, positive when it moves farther ([≈35:11](../raw/transcripts/06-search-ii.md)). Along any path, the modified costs telescope to PastCost$'$(s) = PastCost(s) + h(s) − h(start) ([≈53:52](../raw/transcripts/06-search-ii.md)), which orders states exactly like PastCost(s) + h(s) — so plain UCS on the modified problem explores in the desired order.

One bookkeeping step: the returned solution's costs are the *modified* ones, so `astar_search` converts each step back with $\text{Cost}(s,a) = \text{Cost}'(s,a) - h(\text{Succ}(s,a)) + h(s)$ ([≈42:58](../raw/transcripts/06-search-ii.md)). The actions were never modified.

**Worked example** ([≈35:58](../raw/transcripts/06-search-ii.md)–[≈41:25](../raw/transcripts/06-search-ii.md)): a line of states $-2 \dots 2$, end at 2, unit costs, heuristic $h(s) = 2 - s$. UCS explores 5 states, wandering left to −2; A* explores **3**. From state 0 the modified costs are left: $1 + h(-1) - h(0) = 1 + 3 - 2 = 2$, right: $1 + h(1) - h(0) = 1 + 1 - 2 = 0$ — the heuristic tilts the landscape toward the goal.

## Not Any Heuristic Works

A heuristic can break A* entirely ([≈44:34](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)). In the counterexample (A → C → D costs 2 + 1 = 3; A → B → D costs 1 + 5 = 6), setting $h(C) = 1000$ makes the modified cost of C → D equal $1 + 0 - 1000 = -999$, and **UCS does not work with negative costs** ([≈45:21](../raw/transcripts/06-search-ii.md)).

## Consistency

A heuristic is **consistent** when ([≈46:09](../raw/transcripts/06-search-ii.md))

$$\text{Cost}(s, a) + h(\text{Succ}(s,a)) - h(s) \geq 0 \text{ for all } s, a \qquad \text{and} \qquad h(\text{end}) = 0$$

In other words: exactly the condition that **the modified costs are non-negative**, plus the triviality that the future cost of an end state is 0 ([≈46:54](../raw/transcripts/06-search-ii.md), [≈47:41](../raw/transcripts/06-search-ii.md)). It is both necessary and sufficient for A* to work.

**Correctness** (telescoping sums, [≈48:30](../raw/transcripts/06-search-ii.md)): along any path with original costs $c_1, \dots, c_{k-1}$ from $s_1 = \text{start}$ to $s_k = \text{end}$,

$$\sum_i \text{Cost}'(s_i, a_i) = \sum_i c_i + \sum_i [h(s_{i+1}) - h(s_i)] = \sum_i c_i - h(\text{start}) + h(\text{end}) = \sum_i c_i - h(\text{start})$$

Every $h(s_{i+1})$ cancels the following $-h(s_{i+1})$. The modified cost of *every* start-to-end path equals the original cost minus the same constant, so minimizing one minimizes the other ([≈50:01](../raw/transcripts/06-search-ii.md)–[≈51:34](../raw/transcripts/06-search-ii.md)). The heuristic distorts each edge, but in a coordinated way that un-distorts when summed along a path ([≈52:19](../raw/transcripts/06-search-ii.md)).

**Efficiency** ([≈53:06](../raw/transcripts/06-search-ii.md), [≈53:52](../raw/transcripts/06-search-ii.md)): UCS explores every state with $\text{PastCost}(s) \leq \text{PastCost}(\text{end})$; A* explores only states with

$$\text{PastCost}(s) + h(s) \leq \text{PastCost}(\text{end})$$

The bigger $h$, the more states pruned. The extremes ([≈54:38](../raw/transcripts/06-search-ii.md)): $h = 0$ gives UCS; $h = \text{FutureCost}$ explores only states on a minimum-cost path — but computing that heuristic is as hard as the problem itself. Real heuristics sit between, and designing them is the game.

## Admissibility

**Admissibility**: $h(s) \leq \text{FutureCost}(s)$ — the heuristic never overestimates ([≈55:23](../raw/transcripts/06-search-ii.md)). **Consistency implies admissibility.** The course uses consistency for A* in *graph* search (the setting of lecture 6); admissibility alone suffices for *tree* search, which the course does not treat ([≈56:09](../raw/transcripts/06-search-ii.md)).

## Related Pages

- [Uniform cost search](uniform-cost-search.md) — the algorithm A* runs on
- [Heuristics from relaxation](heuristics.md) — the recipe that guarantees consistency
- [Search problems](search-problems.md) — the abstraction
- [Lecture 6](06-search-ii.md) — the line demo, counterexample, and propositions
