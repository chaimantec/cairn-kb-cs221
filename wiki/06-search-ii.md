---
title: Search II — Uniform Cost Search, A*, and Heuristics from Relaxation
lecture: 6
video: https://www.youtube.com/watch?v=4Iu4KPVbnAY
slides: ../raw/slides/06-search-ii.md
transcript: ../raw/transcripts/06-search-ii.md
---

# Lecture 6 — Search II

Lecture 5's exact algorithms required acyclic search problems. This lecture removes that restriction: [uniform cost search](uniform-cost-search.md) (UCS) handles cycles by computing **past costs** and processing states in **increasing past cost** order, and [A*](a-star-search.md) is UCS run on a problem whose costs are shifted by a **heuristic** — an approximation of future cost. The second half of the lecture solves the practical question A* raises: how to construct a heuristic that is guaranteed to work. The answer is [relaxation](heuristics.md): solve an easier version of the problem and use its future cost.

## Why Cycles Break Dynamic Programming

Every state has two idealized quantities ([≈2:21](../raw/transcripts/06-search-ii.md)):

$$\text{PastCost}(s) = \min \text{ cost from the start state to } s \qquad \text{FutureCost}(s) = \min \text{ cost from } s \text{ to an end state}$$

Their sum is the cost of the best solution that goes *through* $s$ ([≈3:07](../raw/transcripts/06-search-ii.md)). [Dynamic programming](dynamic-programming.md) computed FutureCost with a recurrence, working backward from the end state — structurally the same idea as [backpropagation](backpropagation.md), computing gradients from the loss backward ([≈3:53](../raw/transcripts/06-search-ii.md)). But the recurrence needs an order: FutureCost($s$) depends on FutureCost($s'$) for successors $s'$, which works only if there is a **topological order** — i.e., **no cycles** ([≈4:40](../raw/transcripts/06-search-ii.md)). With a cycle, the code loops forever and the quantity is mathematically undefined, because each state's future cost depends on the other's.

The diamond graph makes the problem concrete: A ↔ B (cost 1), A ↔ C (cost 100), B ↔ C (cost 1), B ↔ D (cost 100), C ↔ D (cost 1), with undirected edges ([≈4:40](../raw/transcripts/06-search-ii.md)). Should DP compute B before C, or C before B? There is no topological ordering of the nodes ([≈5:27](../raw/transcripts/06-search-ii.md)). The answer: UCS.

## Uniform Cost Search

[Uniform cost search](uniform-cost-search.md), due to **Dijkstra (1956)**, trades one assumption for another: it handles cycles but requires **non-negative costs** (negative edges plus cycles is Bellman–Ford, which the course doesn't cover) ([≈6:13](../raw/transcripts/06-search-ii.md)). Two differences from dynamic programming ([≈6:59](../raw/transcripts/06-search-ii.md), [≈7:45](../raw/transcripts/06-search-ii.md)):

- Compute **PastCost** instead of FutureCost.
- Process states in **order of increasing past cost**, instead of topological order — the graph structure no longer dictates the order.

### The Three Buckets

The high-level strategy partitions all states ([≈7:45](../raw/transcripts/06-search-ii.md)):

- **Explored** — states whose minimum-cost path is already known.
- **Frontier** — states seen so far, whose best path is still undetermined. Implemented as a **priority queue** keyed by the best cost found so far ([≈10:06](../raw/transcripts/06-search-ii.md)).
- **Unexplored** — everything else.

The algorithm repeatedly removes the minimum-priority state from the frontier into explored, then updates its successors' priorities ([≈10:53](../raw/transcripts/06-search-ii.md)). Each state also keeps a **backpointer** — the previous state and action that achieved the current priority — so the *solution* (the action sequence) can be reconstructed by walking backward from the end state; the priority alone only gives the cost ([≈11:40](../raw/transcripts/06-search-ii.md)).

### Worked Trace: the Diamond

On the diamond problem (start A, end D) ([≈8:31](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)):

1. Frontier = {A: 0}; explore A, which adds B at 1 and C at 100 ([≈11:40](../raw/transcripts/06-search-ii.md)–[≈13:13](../raw/transcripts/06-search-ii.md)).
2. Explore B (priority 1). Its successors: A is already explored, so it is skipped — the cycle is harmless ([≈13:58](../raw/transcripts/06-search-ii.md)). C gets improved to 1 + 1 = **2** (its backpointer changes from A to B), and D enters at 1 + 100 = **101** ([≈14:45](../raw/transcripts/06-search-ii.md)).
3. Explore C (priority 2). D improves to 2 + 1 = **3** via C ([≈15:34](../raw/transcripts/06-search-ii.md), [≈16:22](../raw/transcripts/06-search-ii.md)).
4. Explore D (priority 3). D is the end state; walking the backpointers yields the solution **A → B → C → D, cost 3** ([≈17:08](../raw/transcripts/06-search-ii.md)).

The trace highlights the two subtle mechanisms: a state already on the frontier gets its **priority updated** in place (C: 100 → 2, D: 101 → 3) rather than duplicated, and the stale heap entry (D at 101) is discarded by a `DONE` marker when it surfaces ([slides](../raw/slides/06-search-ii.md)). Executing the lecture's code confirms: **4 states explored** for 4 states — no repeats ([slides](../raw/slides/06-search-ii.md)).

### The Grid Demo

On the 5×5 grid problem from the slides — walls marked `#`, start S at (0,0), end E at (4,4), all actions cost 1 — UCS finds the optimal path of **14 steps, cost 14** (e.g., right, right, right, down, ...), exploring **17 states** (every non-wall cell) ([≈18:01](../raw/transcripts/06-search-ii.md), [≈18:51](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)).

The [UCS video](https://www.youtube.com/watch?v=z6lUnb9ktkE) shows the behavior on a much larger grid: green = explored, yellow = frontier, white = unexplored. The explored region expands **uniformly** in all directions — hence the name — because UCS has no idea where the end state is ([≈20:27](../raw/transcripts/06-search-ii.md), [≈21:13](../raw/transcripts/06-search-ii.md)).

### Correctness

The theorem ([≈21:58](../raw/transcripts/06-search-ii.md), [≈22:45](../raw/transcripts/06-search-ii.md)): **when UCS moves a state $s$ from frontier to explored, $\text{priority}(s) = \text{PastCost}(s)$** — priority is the algorithm's bookkeeping, PastCost the mathematical quantity; the theorem says they coincide. Since this holds for every state including the first end state popped, UCS returns the minimum-cost solution ([≈23:33](../raw/transcripts/06-search-ii.md)).

Proof by induction on the exploration order ([≈24:18](../raw/transcripts/06-search-ii.md)–[≈28:12](../raw/transcripts/06-search-ii.md)). Base case: the start gets priority = PastCost = 0. Inductive step: when $s$ is popped, its blue path has cost priority($s$). Take any alternative red path to $s$; it must cross from the explored set to the frontier at some edge $t \to u$ (with $t$ explored, $u$ in frontier or equal to $s$). Then

$$\text{cost(red)} \;\geq\; \text{PastCost}(t) + \text{Cost}(t,u) \;\geq\; \text{priority}(t) + \text{Cost}(t,u) \;\geq\; \text{priority}(u) \;\geq\; \text{priority}(s) \;=\; \text{cost(blue)}$$

The four inequalities: the red path to $t$ costs at least PastCost($t$), and the tail $u \to s$ costs at least 0 (non-negative costs, which is where the assumption enters); PastCost($t$) = priority($t$) by the inductive hypothesis; when $t$ was explored its expansion updated $u$'s priority to at most priority($t$) + Cost($t,u$); and $s$ was chosen over $u$ precisely because it had minimum priority ([≈25:53](../raw/transcripts/06-search-ii.md)–[≈28:12](../raw/transcripts/06-search-ii.md)). So no red path beats the blue one, and priority($s$) is the true PastCost.

The upshot: UCS **computes past costs in non-decreasing order**, uses a priority queue for efficiency, and provably finds minimum-cost solutions ([≈29:00](../raw/transcripts/06-search-ii.md)).

## A*: UCS with a Heuristic

The problem with UCS is visible in the video: it explores uniformly in every direction, "without regards for where you're going" ([≈29:48](../raw/transcripts/06-search-ii.md)). Ideally you would explore in order of **PastCost(s) + FutureCost(s)** — states on a minimum-cost path always win that ordering ([≈32:53](../raw/transcripts/06-search-ii.md)). But FutureCost of the start state *is* the problem you're trying to solve, so that's circular ([≈33:40](../raw/transcripts/06-search-ii.md)).

**A*** ([Hart, Nilsson, Raphael 1968]) approximates: order by **PastCost(s) + h(s)**, where the **heuristic** $h(s)$ is a function from states to numbers that estimates FutureCost ($s$) ([≈33:40](../raw/transcripts/06-search-ii.md)). Concretely, A* = UCS run on a **modified search problem** with the same states and actions but shifted costs ([≈34:25](../raw/transcripts/06-search-ii.md)):

$$\text{Cost}'(s, a) = \text{Cost}(s, a) + \big[\, h(\text{Succ}(s,a)) - h(s) \,\big]$$

The bracket is a **penalty for moving away from the end state**: moving closer makes it negative, moving farther positive ([≈35:11](../raw/transcripts/06-search-ii.md)). On the line problem (states −2…2, end at 2, heuristic $h(s) = 2 - s$): UCS explores 5 states (−1 and −2 included) while A* explores **3**, heading straight right, with the modified costs becoming left: 1 + 3 − 2 = **2**, right: 1 + 1 − 2 = **0** ([≈35:58](../raw/transcripts/06-search-ii.md)–[≈41:25](../raw/transcripts/06-search-ii.md)). One bookkeeping detail: the modified solution's costs must be converted back by inverting the shift, $\text{Cost}(s,a) = \text{Cost}'(s,a) - h(\text{Succ}(s,a)) + h(s)$ ([≈42:58](../raw/transcripts/06-search-ii.md)).

### Not Any Heuristic Works

A bad heuristic can break A* outright ([≈44:34](../raw/transcripts/06-search-ii.md)). The counterexample slide: a diamond where the optimal path A → C → D costs 2 + 1 = 3, versus A → B → D costing 1 + 5 = 6 — but $h(C) = 1000$ "actively messes things up": the modified cost of C → D becomes $1 + 0 - 1000 = -999$, and UCS **does not work with negative costs** ([≈45:21](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)).

The fix is **consistency** ([≈46:09](../raw/transcripts/06-search-ii.md)): a heuristic $h$ is consistent when

$$\text{Cost}(s, a) + h(\text{Succ}(s,a)) - h(s) \geq 0 \quad \text{for all } s, a \qquad \text{and} \qquad h(\text{end}) = 0$$

— i.e., exactly the condition that the modified costs are non-negative (plus the triviality that the future cost of an end state is 0) ([≈46:54](../raw/transcripts/06-search-ii.md), [≈47:41](../raw/transcripts/06-search-ii.md)). It's "almost too convenient": the student question "what if modified costs are negative?" is answered by defining them to be non-negative, and that turns out to be **necessary and sufficient** for A* to work.

**Correctness** follows from **telescoping sums** ([≈48:30](../raw/transcripts/06-search-ii.md)): along any path $s_1 \to s_2 \to \dots \to s_k$ with costs $c_1, \dots, c_{k-1}$,

$$\sum_i \text{Cost}'(s_i, a_i) = \sum_i c_i + \sum_i \big[ h(s_{i+1}) - h(s_i) \big] = \sum_i c_i - h(s_1) + h(s_k) = \sum_i c_i - h(\text{start})$$

because every $h(s_{i+1})$ cancels the next $-h(s_{i+1})$, and $h(\text{end}) = 0$ ([≈50:01](../raw/transcripts/06-search-ii.md)–[≈51:34](../raw/transcripts/06-search-ii.md)). The modified cost of every start-to-end path differs from the original cost by the **same constant**, so minimizing one minimizes the other. Intuition: the heuristic twists every edge, but in a coordinated way that un-twists when summed along a path ([≈52:19](../raw/transcripts/06-search-ii.md)).

**Efficiency** ([≈53:06](../raw/transcripts/06-search-ii.md), [≈53:52](../raw/transcripts/06-search-ii.md)): UCS explores all states with $\text{PastCost}(s) \leq \text{PastCost}(\text{end})$; A* explores only those with

$$\text{PastCost}(s) + h(s) \leq \text{PastCost}(\text{end})$$

so a larger $h$ prunes more states. The extremes: $h = 0$ degenerates to UCS; $h = \text{FutureCost}$ explores **only nodes on a minimum-cost path** — but computing that is as hard as the original problem. Real heuristics sit in between ([≈54:38](../raw/transcripts/06-search-ii.md)).

**Admissibility** (mentioned for completeness): $h(s) \leq \text{FutureCost}(s)$, i.e., $h$ never overestimates ([≈55:23](../raw/transcripts/06-search-ii.md)). **Consistency implies admissibility**; consistency is the stronger notion needed for graph search, while admissibility suffices for tree search ([≈56:09](../raw/transcripts/06-search-ii.md)).

## Heuristics from Relaxation

Consistency is a property you'd rather not verify by hand for every new heuristic. The lecture's answer is a construction that gives consistency **for free** ([≈56:55](../raw/transcripts/06-search-ii.md), [≈57:41](../raw/transcripts/06-search-ii.md)):

1. Define a **relaxed problem** by removing some constraints.
2. Compute FutureCost$_\text{relaxed}(s)$ in the relaxed problem — which must be *easier* to solve than the original.
3. Use $h(s) = \text{FutureCost}_\text{relaxed}(s)$ as the A* heuristic.

Formally, a relaxation keeps the states, actions, and successors identical and only satisfies $\text{Cost}_\text{relaxed}(s,a) \leq \text{Cost}(s,a)$ — removing a constraint *is* reducing a cost, often from infinity (a wall is "cost infinity to enter") to a finite value ([≈1:14:01](../raw/transcripts/06-search-ii.md), [≈1:14:48](../raw/transcripts/06-search-ii.md)). The theorem that $h$ = FutureCost$_\text{relaxed}$ is consistent is a three-line proof: triangle inequality, then $\text{Cost}_\text{relaxed} \leq \text{Cost}$ ([≈1:15:34](../raw/transcripts/06-search-ii.md), [≈1:16:19](../raw/transcripts/06-search-ii.md)).

Three worked relaxations, each making the problem easier in a different way:

| Relaxation | How it becomes easier | Demo result |
|---|---|---|
| **Knock down the walls** (grid) | Closed-form solution: $h$ = **Manhattan distance** to the end ([≈59:13](../raw/transcripts/06-search-ii.md), [≈59:59](../raw/transcripts/06-search-ii.md)) | Heuristic values: (0,0) → 8, (0,1) → 7, (2,4) → 2, (3,0) → 5. A* and UCS both explore 17 states — **no benefit here** ([≈1:01:34](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)) |
| **Free tram** (limited travel) | Fewer states: the ticket count leaves the state, so DP solves the relaxed problem in **10 states** ([≈1:03:52](../raw/transcripts/06-search-ii.md), [≈1:04:37](../raw/transcripts/06-search-ii.md)) | UCS explores 23; A* explores **8**; adding the 10 for the relaxed problem: **18 vs 23** ([≈1:05:25](../raw/transcripts/06-search-ii.md), [≈1:06:13](../raw/transcripts/06-search-ii.md)) |
| **Tiles may overlap** (8-puzzle) | Independent subproblems: each tile moves alone; the heuristic is the **sum of per-tile Manhattan distances** ([≈1:11:43](../raw/transcripts/06-search-ii.md)) | The demo state scores 1+1+3+1+1+1+1+3 = **12** ([≈1:11:43](../raw/transcripts/06-search-ii.md), [slides](../raw/slides/06-search-ii.md)) |

Two subtleties worth remembering:

- The heuristic maps original states to relaxed states by **projection** — (loc, tickets) → loc — since the relaxed problem dropped the ticket dimension ([≈1:04:37](../raw/transcripts/06-search-ii.md)).
- A heuristic is only as honest as the comparison. When you claim A* explores 8 vs 23, you must **add the cost of solving the relaxed problem** (8 + 10 = 18) — the lecturer nearly forgot, and a student called it out ([≈1:06:13](../raw/transcripts/06-search-ii.md), [≈1:07:46](../raw/transcripts/06-search-ii.md)).
- If the relaxed problem has cycles, DP can't solve it either. The trick: **reverse** the relaxed problem's edges, then PastCost in the reversed problem equals FutureCost in the original — so UCS on the reversed problem computes the heuristic ([≈1:09:23](../raw/transcripts/06-search-ii.md), [≈1:10:10](../raw/transcripts/06-search-ii.md)).

The general principle: a relaxation isn't automatically easier — costs must be reduced in a **structured** way that yields closed forms, fewer states, or independent subproblems ([≈1:17:06](../raw/transcripts/06-search-ii.md)). Finally, if you have two consistent heuristics you don't have to pick: **$h = \max(h_1, h_2)$ is consistent** ([≈1:17:54](../raw/transcripts/06-search-ii.md)).

## Summary

UCS and A* are **exact** algorithms that **allow cycles** (but require non-negative costs); they order states by increasing past cost, whether under the original costs or the modified ones ([≈1:18:42](../raw/transcripts/06-search-ii.md)). You can't do better than UCS in general; A*'s contribution is a channel for **domain knowledge** — heuristics — that speeds up the search while keeping the optimality guarantee ([≈1:18:42](../raw/transcripts/06-search-ii.md)). The recipe for heuristics is to think of them as **future costs of some relaxed problem**, obtained by lowering costs in a structured way ([≈1:18:42](../raw/transcripts/06-search-ii.md)).

**Next time**: actions with non-deterministic outcomes — dice rolls — and the Markov decision process framework ([≈1:19:28](../raw/transcripts/06-search-ii.md)).
