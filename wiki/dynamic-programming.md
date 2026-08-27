---
title: Dynamic Programming — Exhaustive Search Plus Caching
---

# Dynamic Programming

**Dynamic programming = exhaustive search + caching** ([≈38:52](../raw/transcripts/05-search-i.md)). In lecture 5 it is introduced as a three-line patch to exhaustive search that turns exponential time into time proportional to the number of states. The same idea returns in later lectures as value iteration for MDPs, and in games.

## Where the Name Comes From

Richard Bellman coined the term in the 1950s (he also pioneered the Markov decision process). Neither word means what you'd guess ([≈38:52](../raw/transcripts/05-search-i.md)):

- **dynamic** = sequential decision-making — multiple actions over time;
- **programming** = optimization.

It is not "programming in Python." An equivalent name is **memoization**: remember what you've computed so you don't compute it again ([≈39:40](../raw/transcripts/05-search-i.md)).

## The Recurrence It Caches

Both exhaustive search and DP rest on the **future cost** recurrence ([≈24:58](../raw/transcripts/05-search-i.md)):

$$\text{future\_cost}(s) = \min_{s' \in \text{successors}(s)} \left[ \text{cost}(s \to s') + \text{future\_cost}(s') \right]$$

with base case $\text{future\_cost}(s) = 0$ at end states ([≈28:00](../raw/transcripts/05-search-i.md)). Exhaustive search recomputes this for the same state over and over — on a 4-state problem it explored 9 states ([≈29:37](../raw/transcripts/05-search-i.md)) — because a state reachable by several paths is expanded once per path. DP's cache (a dict from state → best solution) makes each state's future cost computed exactly once.

## The Three-Line Diff

The DP function is identical to exhaustive search except for three changes ([≈40:25](../raw/transcripts/05-search-i.md)):

1. **Introduce** a `cache: dict[state, Solution]`.
2. **Check** at the top of the recurrence: `if state in cache: return cache[state]`.
3. **Store** after computing: `cache[state] = best_solution`.

The pattern is common enough to have a built-in: a Python `@memoize` decorator wraps any function and caches its results — the lecture inlines it instead, for transparency ([≈41:11](../raw/transcripts/05-search-i.md)).

**Bonus side effect**: when DP finishes, the cache contains the optimal solution from *every* state, not just the start — in the n = 10 travel problem, the future cost from 2 is 5, and from 5 it's just one tram ([≈41:11](../raw/transcripts/05-search-i.md)).

## What It Costs, and When It's Worth It

Measured on the travel problem by the lecture's own runs ([≈43:31](../raw/transcripts/05-search-i.md), [slides](../raw/slides/05-search-i.md)):

| n | exhaustive: states explored | DP: states explored | solution cost |
| --- | --- | --- | --- |
| 10 | 59 | 10 | 6 |
| 17 | 237 | 17 | 8 |
| 100 | — | 100 | 13 |

DP's time is roughly the **number of states**; exhaustive search's is exponential in it. The two conditions for DP to be the right tool ([≈45:03](../raw/transcripts/05-search-i.md), [≈45:49](../raw/transcripts/05-search-i.md)):

1. **The number of states fits in memory.** Memory is more precious than time: you can always run a program longer, but memory doesn't grow. A trillion states means no DP.
2. **There are many ways to reach a state.** The lattice picture — few states, exponentially many paths — is where the cache pays for itself. If every action leads to a fresh state (a tree), every lookup is a cache miss and DP degenerates to exhaustive search. Language-model generation looks like the tree case: every generated sequence is nearly unique ([≈47:22](../raw/transcripts/05-search-i.md)).

Memory comparison ([≈48:55](../raw/transcripts/05-search-i.md)): exhaustive search holds just the current path (memory = solution length); DP holds every state (memory = number of states). Both "linear", but in different quantities — and it's the time gap that makes DP the default choice when both fit.

## Related Pages

- The search-problem abstraction it operates on: [search problems](search-problems.md)
- [Lecture 5](05-search-i.md) — the introduction, with the worked travel-problem demos
- [Beam search](beam-search.md) — what to use when exact search (with or without caching) is intractable
