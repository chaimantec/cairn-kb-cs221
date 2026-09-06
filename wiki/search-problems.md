---
title: Search Problems — States, Actions, Costs, and the Modeling Game
---

# Search Problems

A **search problem** is the formal abstraction that turns a word problem ("how do I get from location 1 to location n the fastest?") into a structure general algorithms can solve. It is the unit of lecture 5, and the foundation everything after it builds on: dynamic programming, best-of-n and beam search in [lecture 5](05-search-i.md), [uniform-cost search](uniform-cost-search.md) and [A*](a-star-search.md) in lecture 6, and — via the same state/action/cost vocabulary — [Markov decision processes](markov-decision-processes.md) and [reinforcement learning](reinforcement-learning.md).

## The Three Components

A search problem is fully specified by three functions ([≈9:24](../raw/transcripts/05-search-i.md)):

| Component | Meaning |
|---|---|
| `start_state()` | the initial state |
| `successors(state)` | the actions available in `state`, each with a cost and a resulting state |
| `is_end(state)` | whether `state` is an end state |

A successor is a **Step**: an `(action, cost, next state)` triple. The action is just a label; the cost is what taking it costs; the step dumps you into the next state ([≈7:51](../raw/transcripts/05-search-i.md)).

**The objective**: find a **solution** — a sequence of actions — that minimizes the **total cost**, the sum of the step costs ([≈9:24](../raw/transcripts/05-search-i.md)):

$$\text{cost}(\text{solution}) = \sum_{\text{step} \in \text{solution}} \text{step.cost}$$

A solution with minimum cost is not necessarily unique — the travel problem has several cost-6 solutions — but the minimum cost itself is unique ([≈31:10](../raw/transcripts/05-search-i.md)).

## What the State Is

**The state contains any information needed to evaluate future actions, costs, and successors** ([≈15:39](../raw/transcripts/05-search-i.md)). Nothing more is required, and the modeling game is figuring out exactly what is:

- **Tram tickets** (tram costs a ticket): state = `(loc, tickets)`, a composite data structure — states can be any hashable object ([≈11:46](../raw/transcripts/05-search-i.md)).
- **No tram twice in a row**: state must additionally record whether the last action was a tram ([≈16:25](../raw/transcripts/05-search-i.md)).
- **Multiple end states are fine**: `is_end` checks location only, so ending with 0, 1, or 2 tickets left are all end states ([≈14:52](../raw/transcripts/05-search-i.md)).

Two modeling cautions from the lecture:

1. **Get the definition right, or the algorithm cheats you.** Forget the `tickets > 0` check and the solver happily rides the tram with minus three tickets — "reward hacking" ([≈14:06](../raw/transcripts/05-search-i.md)).
2. **Keep the number of states small.** You *could* dump the entire history into the state, but algorithms like dynamic programming scale in the number of states — an exponential state space is an intractable algorithm ([≈17:11](../raw/transcripts/05-search-i.md)).

## Why Search (the Bitter Lesson)

Symbolic AI of the 1950s was built on search and stalled. Rich Sutton's 2019 essay **"The Bitter Lesson"** argues that general methods leveraging computation win by a large margin, and that the two methods that scale arbitrarily are **search and learning** ([≈3:10](../raw/transcripts/05-search-i.md)). Search alone failed; learning alone was missing for decades; the modern bet is that you need both — e.g., test-time compute, where the costs are learned (a language model's token probabilities) and search organizes the compute ([≈3:57](../raw/transcripts/05-search-i.md), [≈1:20:52](../raw/transcripts/05-search-i.md)).

## Where the Abstraction Goes Next

- [Exhaustive search and dynamic programming](05-search-i.md) solve the minimum-cost objective exactly — one with exponential time, one with caching (see [dynamic programming](dynamic-programming.md)).
- [Best-of-n and beam search](beam-search.md) approximate it when exact search is intractable.
- [Lecture 6](06-search-ii.md) lifts the no-cycles assumption with [uniform-cost search](uniform-cost-search.md) and [A*](a-star-search.md) ([≈33:32](../raw/transcripts/05-search-i.md)).
- Next week the same state vocabulary reappears in **MDPs**, where transitions become probabilistic and value iteration handles cycles ([≈15:39](../raw/transcripts/05-search-i.md)).
- In **games** (lecture 10) the FutureCost idea returns as the [evaluation function](evaluation-functions.md): search a few moves deep, then approximate the rest — the lecturer draws the analogy explicitly ([≈1:10:54](../raw/transcripts/10-games-i.md)).
