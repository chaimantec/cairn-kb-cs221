---
title: Beam Search and Best-of-n — Approximate Search
---

# Beam Search and Best-of-n

When exact search is intractable — the state is a set of locations, or a sequence of words generated so far — the lecture's fallback is to heuristically look at only a subset of the actions, accepting that the result **might miss the optimum, with no guarantees** ([≈50:30](../raw/transcripts/05-search-i.md)). Two algorithms fill this role in [lecture 5](05-search-i.md): **best-of-n** and **beam search**. Both reappear throughout the course — in language-model test-time compute (lecture 5's finale), generation in the language-model lecture, and game tree search.

## Best-of-n

**Best-of-n**: choose random actions until you reach an end state; do this n times; take the best solution ([≈51:16](../raw/transcripts/05-search-i.md)). Its vocabulary is deliberately the RL vocabulary ([≈52:02](../raw/transcripts/05-search-i.md)):

- **Policy**: a function from state to action — deterministic or random.
- **Rollout**: running a policy from the start state to an end state and collecting the steps ([≈52:48](../raw/transcripts/05-search-i.md)).

The lecture's simplest policy, the **uniform policy**, picks a successor uniformly at random. On the n = 10 travel problem, three rollouts scored costs 8, 6, and 9; best-of-n with 10 candidates found the cost-6 optimum ([≈53:36](../raw/transcripts/05-search-i.md), [≈54:22](../raw/transcripts/05-search-i.md)).

Properties ([≈55:08](../raw/transcripts/05-search-i.md), [≈55:56](../raw/transcripts/05-search-i.md)):

- **Guarantee**: as n → ∞ the solution converges to the optimum, *provided the policy gives every action positive probability* — but n may need to be exponentially large ("centillion").
- **Embarrassingly parallel**: n independent machines, no communication, gather results at the end.
- **The policy is a prior**: a random policy is a weak one; a language-model policy is informed, and then best-of-n becomes a strong baseline — this is the test-time-compute story ([≈1:13:08](../raw/transcripts/05-search-i.md)).

## Beam Search

**Beam search** keeps a set of `beam_width` partial solutions from the start state; expands all of them; sorts all extended candidates by cost; keeps the best `beam_width` ([≈58:21](../raw/transcripts/05-search-i.md), [slides](../raw/slides/05-search-i.md)). The name is the driving-at-night analogy: your headlight beam shows a narrow slice ahead and shifts as you advance; exhaustive search is daylight ([≈58:21](../raw/transcripts/05-search-i.md)).

Worked trace, width 2, travel problem ([≈1:04:38](../raw/transcripts/05-search-i.md), [≈1:05:24](../raw/transcripts/05-search-i.md), [≈1:06:10](../raw/transcripts/05-search-i.md)):

1. From state 1: walk (cost 1) and tram (cost 2) — both fit in the beam.
2. Expand both: four candidates costing **2, 3, 3, 4** → sort, prune to the two cheapest (2 and 3).
3. Repeat; at the end two finished candidates remain, costs **6 and 9**; return the best (6 — optimal).

The lecture's implementation notes:

- A candidate that reaches an end state early is **held in the list without expansion** — you might still prefer to throw it out later, since finishing early doesn't make it best ([≈1:01:24](../raw/transcripts/05-search-i.md), [≈1:07:43](../raw/transcripts/05-search-i.md)).
- With **non-negative costs** you can prune early: drop any candidate whose cost already exceeds a finished solution's cost. With negative costs you can't — a bad-looking prefix might still drop below ([≈1:09:16](../raw/transcripts/05-search-i.md)).
- `max_steps` caps the loop, which also sidesteps cycles ([≈1:02:18](../raw/transcripts/05-search-i.md)).
- **Side effect**: you get approximations of the 2nd-best, 3rd-best, ... solutions up to the beam width, for free ([≈1:06:10](../raw/transcripts/05-search-i.md)).

## Beam Width — the Extremes and the Middle

- width **1** = **greedy search** ([≈1:10:02](../raw/transcripts/05-search-i.md)).
- width **∞** = exhaustive search — with memory scaling exponentially, "terrible idea."
- In language settings, widths on the **order of 10** are typical; in particle filtering, thousands of particles ([≈1:08:31](../raw/transcripts/05-search-i.md)).

## Beam Search vs Best-of-n

| | Best-of-n | Beam search |
|---|---|---|
| Selection | policy as a **prior** | costs only |
| Determinism | stochastic | deterministic |
| Parallelism | embarrassingly parallel, no communication | sequential expansion, harder to parallelize |
| Stochastic cousin | — | particle filtering |
| Simplicity | simpler | more machinery |

Use best-of-n when you have a good policy to guide the search; beam search when all you trust is the costs ([≈1:10:48](../raw/transcripts/05-search-i.md)).

## Related Pages

- [Search problems](search-problems.md) — the abstraction both operate on
- [Dynamic programming](dynamic-programming.md) — the exact alternative these approximate
- [Lecture 5](05-search-i.md) — introduction, worked demos, and the language-model test-time-compute application
