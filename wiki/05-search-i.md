---
title: Search I — Search Problems, Exhaustive Search, Dynamic Programming, Best-of-n, Beam Search
lecture: 5
video: https://www.youtube.com/watch?v=fPESauMaJYA
slides: ../raw/slides/05-search-i.md
transcript: ../raw/transcripts/05-search-i.md
---

# Lecture 5 — Search I

This lecture introduces **search** as the second ingredient of intelligence: reasoning, as opposed to the reflexive percept-to-action mapping that a predictor learns. The centerpiece is the [search problem](search-problems.md) abstraction — a state space, a successor function, and an end predicate — which turns word problems into code that general algorithms can solve. Liang then builds four algorithms on it, in order: **exhaustive search** (the exact answer, exponential time), [dynamic programming](dynamic-programming.md) (exhaustive search + caching, exact and fast when states fit in memory), **best-of-n** (random rollouts of a policy), and [beam search](beam-search.md) (keep the best `beam_width` partial solutions). The lecture closes by casting test-time compute in language models as a search problem — the first taste of the "search and learning" synergy the [Bitter Lesson](search-problems.md) points to.

## Why Search? The Bitter Lesson

A predictor maps percepts to actions reflexively — even deep networks are feed-forward and fast. But many problems (a hard math problem, a programming challenge) require **reasoning**: thinking, problem solving, planning, where you look ahead rather than answer instantly ([≈0:50](../raw/transcripts/05-search-i.md)). Search is one form of reasoning, defined for the case where **the world is deterministic** ([≈1:38](../raw/transcripts/05-search-i.md)).

Symbolic AI in the 1950s was all search, and it didn't pan out — so why bother in 2025? Liang cites Rich Sutton's 2019 essay **"The Bitter Lesson"** ([≈2:24](../raw/transcripts/05-search-i.md)):

> *...general methods that leverage computation are ultimately the most effective, and by a large margin.* The two methods that seem to scale arbitrarily in this way are **search and learning**. ([≈3:10](../raw/transcripts/05-search-i.md))

Search alone failed in the '50s; you also need learning. But now that learning works, coupling it with search — e.g., **test-time compute** in language models, where you organize a bunch of computation at inference time — is a powerful combination ([≈3:57](../raw/transcripts/05-search-i.md)).

## The Search Problem Abstraction

A **search problem** has three components ([slides](../raw/slides/05-search-i.md); [≈9:24](../raw/transcripts/05-search-i.md)):

| Component | Meaning |
|---|---|
| `start_state()` | the initial state |
| `successors(state)` | the actions available in `state`, their costs, and the resulting states |
| `is_end(state)` | whether `state` is an end state |

Each successor is a **Step**: `(action, cost, next state)` — the action is just a label, the cost is how expensive it is, and the step lands you in a new state ([≈7:51](../raw/transcripts/05-search-i.md)). The **objective**: find a **solution** — a sequence of actions — that minimizes the **total cost** (the sum of the step costs) ([≈9:24](../raw/transcripts/05-search-i.md)).

### The Travel Problem

Running example ([≈5:31](../raw/transcripts/05-search-i.md)): a street with blocks 1 through n. Walking from i to i+1 takes 1 minute; a magic tram from i to 2i takes 2 minutes. How do you travel from 1 to n in the least time?

The lecture's `TravelSearchProblem` implements this in ~10 lines: `start_state()` returns 1; `successors(state)` returns walk (cost 1, state+1) if in bounds, and tram (cost 2, 2·state) if in bounds; `is_end(state)` checks `state == num_locs` ([slides](../raw/slides/05-search-i.md)).

**Mindset: don't solve it — formalize it.** The point of the abstraction is that general methods can then solve *any* search problem you hand them ([≈6:16](../raw/transcripts/05-search-i.md)).

### What Goes in the State

The **state contains any information needed to evaluate future actions, costs, and successors** ([≈15:39](../raw/transcripts/05-search-i.md)). Two upgrade examples:

1. **Tram tickets**: tram costs a ticket, and you start with a fixed number. The state becomes a composite `TravelState(loc, tickets)` — walk keeps the tickets, tram decrements them ([≈11:46](../raw/transcripts/05-search-i.md)). A subtle bug the lecture calls out: you must check `tickets > 0` before adding the tram successor, or the algorithm finds a way to cheat (minus three tickets) — "reward hacking" ([≈14:06](../raw/transcripts/05-search-i.md)).
2. **No tram twice in a row**: the state must also record whether the last action was a tram ([≈16:25](../raw/transcripts/05-search-i.md)).

**Why not include everything in the state?** Because some algorithms (dynamic programming) scale in the number of states — an exponential state space means an intractably slow algorithm. Keep the number of states small ([≈17:11](../raw/transcripts/05-search-i.md)). Modeling is lossy: you translate a word problem into the formal structure and accept that you may not capture every constraint ([≈19:32](../raw/transcripts/05-search-i.md)).

## Exhaustive Search

**Exhaustive search**: try all possible sequences of actions, take the best. The formulation is chosen to generalize later to dynamic programming, MDPs, RL, and games ([≈21:06](../raw/transcripts/05-search-i.md)).

### Future Cost

The key definition ([≈21:53](../raw/transcripts/05-search-i.md)):

$$\text{future\_cost}(s) = \text{cost of the minimum-cost solution from } s \text{ to an end state}$$

Computed by recursion ([≈24:58](../raw/transcripts/05-search-i.md)):

$$\text{future\_cost}(s) = \min_{s' \in \text{successors}(s)} \left[ \text{cost}(s \to s') + \text{future\_cost}(s') \right]$$

In words: every solution has a first step. Take that step, then piggyback on the optimal solution from wherever you landed, and minimize over all first steps ([≈23:26](../raw/transcripts/05-search-i.md)). Base case: the future cost of an end state is **zero** — you're already done ([≈28:00](../raw/transcripts/05-search-i.md)). Applying future_cost to the start state gives the solution to the whole problem ([≈22:40](../raw/transcripts/05-search-i.md)).

The lecture implements `future_solution(state)`, which returns a full `Solution` (steps + cost) rather than just a number — you need the *actions*, not just the cost ("42 is a cost — what actions do I take?") ([≈26:28](../raw/transcripts/05-search-i.md)).

### Complexity, Measured by the Demo

The lecture tracks `num_explored` — how many states the recurrence visits — as a proxy for time complexity ([≈26:28](../raw/transcripts/05-search-i.md)):

| n (locations) | solution cost | states explored |
| ------------- | ------------- | --------------- |
| 4 | 3 | 9 |
| 10 | 6 | 59 |
| 17 | 8 | 237 |

The values are from executing the lecture's code ([slides](../raw/slides/05-search-i.md)). For n = 4, exploring 9 states when only 4 exist means **some states are explored more than once** ([≈29:37](../raw/transcripts/05-search-i.md)) — the seed of the next idea.

- **Time**: worst-case **exponential** in the number of states ([≈31:59](../raw/transcripts/05-search-i.md)).
- **Memory**: **linear** in the length of a solution — the recursion stack just holds the current path ([≈32:45](../raw/transcripts/05-search-i.md)).

### Cycles

The recurrence assumes **no cycles** (A → B → C → A), otherwise the recursion never terminates ([≈32:45](../raw/transcripts/05-search-i.md)). Workarounds for now ([≈33:32](../raw/transcripts/05-search-i.md)):

- Add the **number of steps** to the state — the state always advances, so cycles are impossible.
- Make `is_end` true when steps exceed a threshold.
- Give an **infinite cost** to entering over-threshold states, so the min never picks them (a hack, but the state–cost framework is flexible enough to absorb it) ([≈36:35](../raw/transcripts/05-search-i.md)).

Value iteration for MDPs (next week) and uniform-cost search (Wednesday) handle cycles properly ([≈33:32](../raw/transcripts/05-search-i.md)).

## Dynamic Programming

**Dynamic programming = exhaustive search + caching** ([≈38:52](../raw/transcripts/05-search-i.md)). The term is Richard Bellman's, 1950s: *dynamic* = sequential decision-making (multiple actions over time), *programming* = optimization — not "programming in Python" ([≈38:52](../raw/transcripts/05-search-i.md)).

The algorithm differs from exhaustive search by exactly **three lines**: a `cache` dictionary from state to solution; a check at the top of the recurrence (`if state in cache: return cache[state]`); and storing `best_solution` in the cache before returning ([≈40:25](../raw/transcripts/05-search-i.md)). The pattern is so common there's a Python `@memoize` decorator for it, though the lecture keeps it explicit for transparency ([≈41:11](../raw/transcripts/05-search-i.md)).

**Bonus**: the finished cache holds the future solution for *every* state, not just the start — e.g., in the n = 10 travel problem, the future cost from 2 is 5, from 5 it's just the tram ([≈41:11](../raw/transcripts/05-search-i.md)).

### Demo

| n | cost | states explored |
| --- | ---- | --------------- |
| 10 | 6 | 10 |
| 17 | 8 | 17 |
| 100 | 13 | 100 |

States explored equals the number of states — each state is visited once ([≈43:31](../raw/transcripts/05-search-i.md), [slides](../raw/slides/05-search-i.md)). The exponential blowup is gone.

### When to Use It — and When Not

- **Memory is more precious than time**: you can always run a program longer, but memory doesn't grow. DP stores every state, so use it only when the **number of states fits in memory** ([≈45:03](../raw/transcripts/05-search-i.md)).
- DP gives a speedup only when there are **many ways to reach a state** — the lattice picture, where the number of states is linear but the number of paths is exponential. If every action leads to a new state (a tree), the cache never hits and you might as well do exhaustive search ([≈45:49](../raw/transcripts/05-search-i.md)). Language-model generation looks like the tree case: every sequence is nearly unique, so caching doesn't help there ([≈47:22](../raw/transcripts/05-search-i.md)).
- Tradeoff summary: exhaustive search's memory is the solution length; DP's memory is the number of states. Both "linear", but DP turns exponential time into ~linear time ([≈48:55](../raw/transcripts/05-search-i.md)).

## Approximate Search

Exact search takes at least O(number of states) time — intractable when the state is a **set of locations** or the **sequence of words generated so far** ([≈49:45](../raw/transcripts/05-search-i.md)). Approximate search **heuristically looks at only a subset of the actions** — it might miss something, and there are **no guarantees** ([≈50:30](../raw/transcripts/05-search-i.md)).

## Best-of-n

**Best-of-n**: choose random actions until you reach an end state; do this n times; take the best solution ([≈51:16](../raw/transcripts/05-search-i.md)). Two pieces of forward-looking vocabulary (search is a special case of MDPs, so the terms transfer) ([≈52:02](../raw/transcripts/05-search-i.md)):

- **Policy**: a function mapping state → action; may be deterministic or random.
- **Rollout**: run a policy from the start state to an end state and collect the solution.

The lecture's **uniform policy** picks a successor uniformly at random ([≈52:48](../raw/transcripts/05-search-i.md)). Three rollouts on the n = 10 travel problem give costs **8, 6, 9** — the middle one lucks into the optimum ([≈53:36](../raw/transcripts/05-search-i.md)); best-of-n with 10 candidates finds the cost-6 optimum ([≈54:22](../raw/transcripts/05-search-i.md), [slides](../raw/slides/05-search-i.md)).

Properties ([≈55:08](../raw/transcripts/05-search-i.md), [≈55:56](../raw/transcripts/05-search-i.md)):

- **Guarantee**: as n → ∞, the solution converges to the optimum — provided the policy gives every action positive probability. But n might need to be astronomical.
- **Embarrassingly parallel**: a thousand machines, no communication, gather results at the end.
- **An informed policy changes everything**: if the policy is a language model, best-of-n becomes a strong baseline.

## Beam Search

**Beam search** keeps `beam_width` partial solutions: expand all of them, sort all extended solutions by cost, keep the best `beam_width` ([slides](../raw/slides/05-search-i.md); [≈58:21](../raw/transcripts/05-search-i.md)). The name is the driving-at-night analogy: your headlight beam only shows a little ahead; exhaustive search is daylight ([≈58:21](../raw/transcripts/05-search-i.md)).

Worked trace, beam width 2 on the travel problem ([≈1:04:38](../raw/transcripts/05-search-i.md), [≈1:05:24](../raw/transcripts/05-search-i.md), [≈1:06:10](../raw/transcripts/05-search-i.md)):

1. From state 1: candidates walk (cost 1) and tram (cost 2) — both fit in the width-2 beam.
2. Expand both: four candidates with costs **2, 3, 3, 4** — sort, prune to the two cheapest (2 and 3).
3. Continue; at the end two finished candidates remain, costs **6 and 9** — return the best (cost 6, optimal).

Properties ([≈1:10:02](../raw/transcripts/05-search-i.md)):

- Beam width 1 = **greedy search**; width → ∞ = exhaustive search (with exponentially growing memory — "terrible idea").
- Beam search is **deterministic**; best-of-n is stochastic (the stochastic version of beam search is **particle filtering**) ([≈1:10:48](../raw/transcripts/05-search-i.md)).
- **Best-of-n uses a policy as a prior**; beam search just uses the costs. With a good policy, use best-of-n; without one, beam search. Best-of-n is simpler and more parallelizable.
- When one candidate finishes early, the lecture's implementation just holds it in the candidate list without expanding it ([≈1:01:24](../raw/transcripts/05-search-i.md)). With non-negative costs you could also prune anything whose cost already exceeds a finished solution's ([≈1:09:16](../raw/transcripts/05-search-i.md)).
- Side effect: you get approximations of the 2nd- and 3rd-best solutions for free ([≈1:06:10](../raw/transcripts/05-search-i.md)).
- Typical widths: on the order of 10 in language settings, up to thousands of particles in tracking ([≈1:08:31](../raw/transcripts/05-search-i.md)).

## Test-Time Compute in Language Models

The finale: cast **test-time compute** as a search problem ([≈1:11:37](../raw/transcripts/05-search-i.md)). Setup ([≈1:12:22](../raw/transcripts/05-search-i.md)):

- A **language model** is just multiclass classification over next tokens — "probably a hundred thousand classes, but mathematically exactly the same thing we saw last week."
- A **verifier** takes a response and returns whether it is correct.
- Goal: produce a response that passes the verifier and is likely under the LM.

**Test-time compute**: instead of sampling one answer, expend more compute to get a better one — popularized by the OpenAI o1 series ([≈1:13:08](../raw/transcripts/05-search-i.md)). The paper **"Large Language Monkeys: Scaling Inference Compute with Repeated Sampling"** (arXiv 2407.21787) shows best-of-n with a small model can be competitive with a much larger model ([≈1:13:54](../raw/transcripts/05-search-i.md)).

Cast as a search problem ([≈1:14:41](../raw/transcripts/05-search-i.md)):

| Component | LM setting |
|---|---|
| State | prompt + prefix of the response generated so far |
| Action | next token |
| Cost | **negative log probability** of the token (minimize cost = maximize probability), minus 100 if the verifier succeeds |

The demo runs Qwen3-0.6B (a 0.6-billion-parameter model from Hugging Face) with prompt `(3 + 7 *` ([≈1:15:26](../raw/transcripts/05-search-i.md)). Successors are the top-k next tokens with cost −log prob; the −100 bonus lands when the state is a complete expression containing a number ([≈1:16:59](../raw/transcripts/05-search-i.md)). A rollout then appends tokens one at a time — space, `2`, closing paren — completes the expression and collects the bonus ([≈1:18:32](../raw/transcripts/05-search-i.md)). Best-of-n over 5 candidates returns `3 + 7 * 2` ([≈1:19:19](../raw/transcripts/05-search-i.md)). In practice you'd optimize inference heavily, but the point is the pipeline: **classic search + modern language models** ([≈1:19:19](../raw/transcripts/05-search-i.md)). [Lecture 17](17-language-models.md) covers language models themselves, and lists test-time scaling among the ideas that make them work; see [language models](language-models.md) and [scaling laws](scaling-laws.md).

## Summary

Search problems formally define states, actions, and costs; the objective is a minimum-cost sequence of actions ([≈1:20:04](../raw/transcripts/05-search-i.md)):

- **Exhaustive search** — exact, exponential time.
- **Dynamic programming** — exact, exponential speedups, but the states must fit in memory.
- **Best-of-n** — n darts in parallel; a policy prior.
- **Beam search** — the car-beam; keep the best few partial solutions.

The **synergy of search and learning** ([≈1:20:52](../raw/transcripts/05-search-i.md)): the costs are **learned from data** (the LM's token probabilities are supervised learning over huge corpora), and **search finds the best solution given those costs**. A good recipe for robust AI systems.

**Next time**: [lecture 6](06-search-ii.md) — what to do when there are cycles — [uniform-cost search](uniform-cost-search.md) and [A*](a-star-search.md) ([≈1:20:52](../raw/transcripts/05-search-i.md)).
