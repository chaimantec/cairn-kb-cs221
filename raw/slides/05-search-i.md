---
title: Lecture 5 — Search I (Slides)
lecture: 5
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_file: https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/main/search.py
rendered: https://stanford-cs221.github.io/autumn2025-lectures/?trace=search
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. This file transcribes the
  content of `search.py`. There are no traditional slide numbers; sections
  correspond to function calls in the source. Demo outputs (states explored,
  solution costs) were reproduced by executing the lecture's own code with the
  same call sequence. FIGURE CAVEAT: no model in the build session could
  perceive images, so figure descriptions are grounded in the accompanying
  source text only, not in the actual visuals. A vision pass over the lecture's
  figures is pending.
---

# Lecture 5 Slides — Search I

Lecture 5 introduces search as a form of reasoning. The lecture defines the **search problem** abstraction, then works through exact methods (exhaustive search, dynamic programming) and approximate methods (best-of-n, beam search), ending with test-time compute in language models as a search problem. Lecture 6 continues with uniform-cost search and A*.

---

## Section: Recap and Motivation (`main`)

**Last week: machine learning**
- Learning algorithm: training data {(input, output)} → predictor
- Predictor: input → output (number for regression, class for classification)

**Recall the ingredients of intelligence** (the perceive–reason–act–learn cycle shown as a diagram):
- A predictor reflexively maps **percepts** to **actions** (and we're **learning** it).
- But many problems in the real world require reasoning (thinking, problem solving, planning).

**This week: search** (one form of reasoning, when the world is deterministic)

- Example: finding a sequence of moves to solve a Rubik's cube (photo of a Rubik's cube).
- Example: finding the shortest path from point A to point B (photo of a map with a route).

Recall: (symbolic) AI started in the 1950s with search, and that didn't pan out. So is this still relevant today?

**Rich Sutton's *The Bitter Lesson* essay (2019)** — [article](http://www.incompleteideas.net/IncIdeas/BitterLesson.html)
- *...general methods that leverage computation are ultimately the most effective, and by a large margin.*
- *The two methods that seem to scale arbitrarily in this way are **search and learning**.*

Search is increasingly important (e.g., test-time compute in language models)! You just also need learning too.

---

## Section: Search Problems (`search_problem`)

Let us formalize search using an abstraction called a **search problem**. Two examples follow.

In general, the **state** contains any information that's needed to evaluate actions, costs, and successors.

Example: if we can't take the tram twice in a row, the state becomes: (location, number of tickets, whether the last action was taking the tram).

**Why not just include everything in the state?** As we'll see later, some algorithms (dynamic programming) scale in the number of states — so we want to keep the number of states small.

So far, we have focused on the **modeling** (representing the problem formally). With all these constraints, it's not obvious what the solution is... but we don't care! (OK, now we have to care about it...)

### The Travel Problem (`example_travel_problem`)

(Diagram: `walk-tram.png` — illustrates the street with blocks numbered 1 to n, the walk transition i → i+1 and the tram transition i → 2i. Exact visual layout not yet described — vision pass pending.)

- Street with blocks numbered 1 to n.
- Walking from i to i+1 takes 1 minute.
- Taking a magic tram from i to 2·i takes 2 minutes.
- How to travel from 1 to n in the least time?

**Mindset: don't solve it!** Formalize the problem first — because we want general methods that can solve **any** search problem.

A search problem has the following components:
- `start_state()`: the initial state.
- `successors(state)`: specifies the actions one can take in `state`, their costs, and the resulting states.
- `is_end(state)`: whether `state` is an end state.

**Objective**: find a solution (**sequence of actions**) that minimizes the total cost.

One possible solution for n = 10 (shown as a worked example):

```python
solution = Solution(steps=[
    Step(action="walk", cost=1, state=2),
    Step(action="tram", cost=2, state=4),
    Step(action="walk", cost=1, state=5),
    Step(action="tram", cost=2, state=10),
])  # total cost 6
```

This is only one possible solution... is this the best solution? Let's see...

### The Building Blocks (classes)

```python
@dataclass(frozen=True)
class Step:
    """Represents taking an `action`, incurring some `cost` and ending up in a new `state`."""
    action: Any
    cost: float
    state: Any

class SearchProblem:
    """Formally and fully represents a search problem."""
    def start_state(self) -> Any: ...
    def successors(self, state: Any) -> list[Step]: ...
    def is_end(self, state: Any) -> bool: ...
```

### TravelSearchProblem

```python
class TravelSearchProblem(SearchProblem):
    """An instance of a `SearchProblem` where you try to go from 1 to n in the least time."""
    def __init__(self, num_locs: int):
        self.num_locs = num_locs

    def start_state(self) -> int:
        return 1  # Where we start (location 1)

    def successors(self, state: int) -> list[Step]:
        """Return possible actions and their costs and resulting states."""
        successors = []
        if state + 1 <= self.num_locs:  # Stay within bounds?
            successors.append(Step(action="walk", cost=1, state=state + 1))
        if 2 * state <= self.num_locs:  # Stay within bounds?
            successors.append(Step(action="tram", cost=2, state=2 * state))
        return successors

    def is_end(self, state: int) -> bool:
        return state == self.num_locs  # Have we reached the destination?
```

### The Limited Travel Problem (`example_limited_travel_problem`)

Let's make the problem more complex. Suppose the magic tram requires tickets and we only have a fixed number of tickets.

How do we modify our formal search problem to incorporate this constraint? The state so far is where we are, but we also need to track the number of tickets we have.

```python
@dataclass(frozen=True)
class TravelState:
    """Represents the state of the `LimitedTravelSearchProblem`: at `loc` with `tickets` left."""
    loc: int
    tickets: int
```

```python
class LimitedTravelSearchProblem(SearchProblem):
    def __init__(self, num_locs: int, starting_tickets: int):
        self.num_locs = num_locs
        self.starting_tickets = starting_tickets

    def start_state(self) -> TravelState:
        """Start at location 1 with `self.starting_tickets` tickets."""
        return TravelState(loc=1, tickets=self.starting_tickets)

    def successors(self, state: TravelState) -> list[Step]:
        successors = []
        if state.loc + 1 <= self.num_locs:  # Can always walk
            successors.append(Step(action="walk", cost=1,
                                   state=TravelState(loc=state.loc + 1, tickets=state.tickets)))
        if state.tickets > 0 and 2 * state.loc <= self.num_locs:  # Tram only with a ticket
            # Remember to decrement the number of tickets
            successors.append(Step(action="tram", cost=2,
                                   state=TravelState(loc=2 * state.loc, tickets=state.tickets - 1)))
        return successors

    def is_end(self, state: TravelState) -> bool:
        # Have we reached the destination? Don't care about how many tickets we have
        return state.loc == self.num_locs
```

### Solution

```python
@dataclass
class Solution:
    """Represents a solution to a search problem (sequence of actions that produces a cost)."""
    steps: list[Step]
    cost: float

    def __init__(self, steps: list[Step]):
        self.steps = steps
        # The cost of a solution is the sum of the costs of the actions
        self.cost = sum(step.cost for step in steps)
```

---

## Section: Exhaustive Search (`introduce_exhaustive_search`)

**Objective**: given a search problem, find a sequence of actions that minimizes the total cost.

**Exhaustive search**: simply try all possible solutions (sequences of actions). There are many ways to enumerate solutions; we'll choose a particular formulation — one that will generalize to dynamic programming and eventually reinforcement learning.

### Key definition: **future cost**

`future_cost(state)`: the cost of the minimum cost solution from `state` to an end state.

(Diagram: `future_cost.png` — illustrates the recurrence below. Exact visual layout not yet described — vision pass pending.)

How to compute this?
- Consider a first step (`successor.cost`)
- Consider the optimal rest of the solution (`future_cost(successor.state)`)
- Minimize over all possible successors from `state`

Recurrence:

```
future_cost(state) = min_{successor} (successor.cost + future_cost(successor.state))
```

### The Algorithm

```python
def exhaustive_search(problem: SearchProblem) -> tuple[Solution | None, int]:
    """Perform exhaustive search on `problem` to find the minimum cost solution."""
    num_explored = 0  # Keep track of how many states we've explored (time complexity)

    def future_solution(state: Any) -> Solution:
        """Return the best solution from `state` (its cost is the future cost)."""
        nonlocal num_explored
        num_explored += 1

        if problem.is_end(state):
            # Base: already at the end, don't need to take any more actions
            best_solution = Solution(steps=[])
        else:
            successors = problem.successors(state)
            # Flesh each successor out recursively into a solution
            solutions = []
            for first_step in successors:
                future_steps = future_solution(first_step.state).steps
                solutions.append(Solution(steps=[first_step] + future_steps))
            # Pick the best one
            best_solution = min(solutions, key=lambda x: x.cost)
        return best_solution

    state = problem.start_state()
    solution = future_solution(state)
    return solution, num_explored
```

### Demo Run

On `TravelSearchProblem(num_locs=4)`: solution cost 3 (walk, walk, walk), and **9 states explored** — larger than the number of states (4). This means we're exploring some states more than once. We'll come back to this point later.

Let's try some larger problems:

| n (locations) | cost of solution | states explored |
| ------------- | ---------------- | --------------- |
| 4 | 3 | 9 |
| 10 | 6 | 59 |
| 17 | 8 | 237 |

Note: the n = 10 solution has the same cost (6) that we had before (different actions though — walk, walk, walk, walk, tram).

Oh no, the number of states explored is growing **exponentially** with the number of locations! So the **time complexity** of exhaustive search is worst case **exponential** in the number of states.

**What about the memory complexity?** Good news: it is **linear** in the length of a solution (the stack in the recurrence).

### Cycles

**Assumption: there cannot be cycles** (e.g., A → B → C → A) — or else the recurrence is not well-defined (infinite loop). Next week, we'll see how value iteration for MDPs gets around this.

In the meantime (workarounds):
- Add number of steps into the state (no cycles since always increment by 1).
- Define `is_end(state)` to be true when `state.num_steps > threshold`.
- Define an infinite cost to enter states `state.num_steps > threshold` (to prune).

Can we improve on the efficiency of exhaustive search?

---

## Section: Dynamic Programming (`introduce_dynamic_programming`)

Originates from **Richard Bellman (1950s)**:
- *dynamic* means multiple actions over time
- *programming* means optimization

**Dynamic programming = exhaustive search + caching** (also known as *memoization*).

Recall that exhaustive search explores some states more than once. Dynamic programming: if already saw a state, don't explore it again.

```python
def dynamic_programming(problem: SearchProblem) -> tuple[Solution | None, int, dict[Any, Solution]]:
    """Perform dynamic programming on `problem` to find the minimum cost solution."""
    num_explored = 0
    cache: dict[Any, Solution] = {}  # NEW: cache solutions for each state

    def future_solution(state: Any) -> Solution:
        """Return the best solution from `state` (its cost is the future cost)."""
        if state in cache:  # NEW: check cache first
            return cache[state]

        num_explored += 1

        if problem.is_end(state):
            best_solution = Solution(steps=[])
        else:
            successors = problem.successors(state)
            solutions = []
            for first_step in successors:
                future_steps = future_solution(first_step.state).steps
                solutions.append(Solution(steps=[first_step] + future_steps))
            best_solution = min(solutions, key=lambda x: x.cost)

        cache[state] = best_solution  # NEW: cache the solution
        return best_solution

    state = problem.start_state()
    solution = future_solution(state)
    return solution, num_explored, cache
```

### Demo Run

| n (locations) | cost | states explored |
| ------------- | ---- | --------------- |
| 10 | 6 | **10** (equals the number of states: 10) |
| 17 | 8 | 17 |
| 100 | 13 | 100 |

Note that the number of states explored (10) = number of states (10). Dynamic programming scales to much larger problems — the lecture runs n = 100.

### When Can You Even Use Dynamic Programming?

- In general, **memory is more precious than time**. You can always run a program for longer, but memory doesn't grow.
- So run dynamic programming only when the **number of states fits in memory**.

### When Does DP Give a Speedup Over Exhaustive Search?

- Intuition: DP is useful when there are **a lot of ways to reach a state**.
- If every action takes you to a new state, might as well do exhaustive search (no cache hits).

### Summary

- Dynamic programming = exhaustive search + caching
- Use when the number of states fits in memory and there are lots of ways to go between the same states

---

## Section: Approximate Search — the Motivation

So far: compute the minimum cost solution. **Time complexity: at least O(number of states).**

But what if the state is:
- A set of locations?
- A sequence of words generated so far?

Exact search will be intractable.

We will now turn to **approximate search**. Key idea: heuristically look at only a subset of the actions. Might miss something, but 🤷

---

## Section: Best-of-n (`introduce_best_of_n`)

The simplest idea is to **randomly choose actions until we reach the end state**. Do this `n` times and take the best solution.

How we choose actions is determined by a **policy**. A **policy** is a function that maps state to action (can be non-deterministic).

### Uniform Policy and Rollout

```python
def uniform_policy(problem: SearchProblem, state: Any) -> Step:
    """Chooses an action uniformly from the successors of `state`."""
    successors = problem.successors(state)
    return random.choice(successors)

def rollout(problem: SearchProblem, policy, max_steps: int = 10) -> Solution:
    """Sample a policy from the start state of `problem`."""
    state = problem.start_state()
    steps = []
    while not problem.is_end(state) and len(steps) < max_steps:
        step = policy(problem, state)   # Take a step
        steps.append(step)
        state = step.state              # Advance the state
    return Solution(steps=steps)
```

We can iteratively apply a policy until we reach the end state to get a solution. The lecture demos three rollouts of `uniform_policy` on `TravelSearchProblem(num_locs=10)` (with `random.seed(1)`): costs 8, 6, 9 — the middle one happens to be optimal.

### Best-of-n

Let's rollout the policy `n` times and take the best solution:

```python
def best_of_n(problem: SearchProblem, policy, num_candidates: int, max_steps: int = 10):
    """Perform best-of-n search to `problem`."""
    num_explored = 0
    solutions = []
    for _ in range(num_candidates):
        solution = rollout(problem, policy, max_steps=max_steps)
        solutions.append(solution)
        num_explored += len(solution.steps)
    best_solution = min(solutions, key=lambda x: x.cost)  # Choose the best solution
    return best_solution, num_explored
```

Demo: `best_of_n(problem, uniform_policy, num_candidates=10)` finds cost **6** (walk, walk, walk, walk, tram — optimal) after exploring 63 states.

- **Guarantee**: as n goes to infinity, the solution will converge to the minimum cost solution.
- It might take exponentially long though...
- **Embarrassingly parallel**: each of `n` paths can be computed independently.

---

## Section: Beam Search (`introduce_beam_search`)

(Image: `beam_car.jpeg` — a visual pun on the word "beam"; per the source text, the beam is the set of partial solutions at each step. Exact visual content not yet described — vision pass pending.)

**Beam search**:
- Keep track of a set of `beam_width` partial solutions (from the starting state).
- Consider all possible actions from each of the partial solutions.
- Evaluate the cost so far of all extended partial solutions.
- Keep only the `beam_width` best partial solutions.

```python
def beam_search(problem: SearchProblem, beam_width: int, max_steps: int):
    """Perform beam search on `problem` keeping `beam_width` candidates and `max_steps`."""
    num_explored = 0
    candidates = [Solution(steps=[])]

    for step in range(max_steps):
        # Given the existing candidates, expand them by one step
        new_candidates = []
        for candidate in candidates:
            state = candidate.steps[-1].state if candidate.steps else problem.start_state()
            if problem.is_end(state):  # If we've already reached the end, just keep
                new_candidates.append(candidate)
            else:
                # Try all possible actions from `state`
                for successor in problem.successors(state):
                    new_candidates.append(Solution(steps=candidate.steps + [successor]))
                    num_explored += 1

        # Take the `beam_width` best candidates (lowest cost)
        new_candidates.sort(key=lambda x: x.cost)
        candidates = new_candidates[:beam_width]  # Prune

    # Keep only candidates that are done
    candidates = [c for c in candidates if problem.is_end(c.steps[-1].state)]
    return candidates[0], num_explored
```

Demo: `beam_search(problem, beam_width=2, max_steps=10)` on `TravelSearchProblem(num_locs=10)` finds cost **6** (walk, walk, walk, walk, tram — optimal), exploring 20 states.

### Notes

- If beam width is 1, then beam search is equivalent to **greedy search**.
- As beam width goes to infinity, beam search becomes **exhaustive search**.
- Beam search is **deterministic** (stochastic version: particle filtering).
- Best-of-n incorporates a **policy as a prior**; beam search just uses the costs.
- Best-of-n is simpler and more parallelizable than beam search.

---

## Section: Test-Time Compute in Language Models (`test_time_compute_in_language_models`)

**Motivation: test-time compute for language models**

Given:
- language model: prompt → distribution over next token
- verifier: response → boolean (is the response correct?)

**Goal**: produce a response that passes the verifier (and has high probability under the LM).

**Test-time compute**: rather than sampling one answer, expend more compute to get a better answer. Simple strategy: **best-of-n sampling**.

The lecture cites **"Large Language Monkeys: Scaling Inference Compute with Repeated Sampling"** ([arXiv 2407.21787](https://arxiv.org/pdf/2407.21787)), showing its main figure (`llm-monkeys.png`): repeated sampling — how the fraction of problems solved rises as more samples are drawn per problem. Axis labels and series not yet described — vision pass pending.

### Cast This as a Search Problem

- **State**: prompt + prefix of the response (so far)
- **Action**: next token
- **Cost**: negative log probability of the next token (and −100 if the verifier succeeds)

The lecture instantiates a `LanguageModelSearchProblem(prompt="(3 + 7 *")` built on Qwen/Qwen3-0.6B, whose successors are the **top-5 next tokens**:

```python
class LanguageModelSearchProblem(SearchProblem):
    def __init__(self, prompt: str, model_id: str = "Qwen/Qwen3-0.6B"):
        self.prompt = prompt
        self.tokenizer = AutoTokenizer.from_pretrained(self.model_id)
        self.model = AutoModelForCausalLM.from_pretrained(self.model_id, dtype=torch.float16).eval()

    def start_state(self) -> str:
        return self.prompt  # State starts with prompt

    def successors(self, state: str) -> list[Step]:
        # Tokenize the state (prompt + prefix of the response so far)
        input_ids = self.tokenizer(state, return_tensors="pt")["input_ids"]
        # Get probabilities over next token
        all_logits = self.model(input_ids=input_ids).logits
        next_token_logits = all_logits[0, -1, :]
        next_token_probs = F.softmax(next_token_logits, dim=-1)
        topk = torch.topk(input=next_token_probs, k=5)  # Keep only the top 5 tokens

        # Build a successor for each of the top 5 tokens
        successors = []
        for index, prob in zip(topk.indices, topk.values):
            action = index.item()
            # Maximize product of probabilities = minimize sum of log probabilities (costs)
            cost = -torch.log(prob).item()
            # Here's where we end up
            new_state = state + self.tokenizer.decode([action])
            # If the resulting state is the end and has a number, get a big reward (negative cost)
            if is_complete_sentence(new_state) and contains_number(new_state):
                cost -= 100
            successors.append(Step(action=action, cost=cost, state=new_state))
        return successors

    def is_end(self, state: str) -> bool:
        return is_complete_sentence(state)  # We're done once we get well-formed "JSON"
```

(`is_complete_sentence` checks the state ends with `)`, `contains_number` tries `eval(state)` — marked "Dangerous!!!" in the source.)

A policy that samples from the LM (`lm_policy`) converts successor costs back into probabilities via softmax and samples:

```python
def lm_policy(problem: LanguageModelSearchProblem, state: str) -> Step:
    successors = problem.successors(state)
    costs = [successor.cost for successor in successors]
    probs = torch.softmax(-torch.tensor(costs), dim=-1)  # Convert costs to probabilities
    index = torch.multinomial(probs, num_samples=1)[0]   # Sample from the distribution
    return successors[index]
```

Then best-of-n search runs on this problem: `best_of_n(problem, lm_policy, num_candidates=5, max_steps=10)`.

### Notes

- In practice, we would do many optimizations to speed up language model inference.

---

## Section: Summary (`main`)

- **Search problem**: formally defines the problem (state, actions, costs, etc.)
- **Objective**: find a solution (sequence of actions) that minimizes the total cost.
- **Exhaustive search**: find exact solution, but takes exponential time.
- **Dynamic programming**: find exact solution, exponentially faster (if the number of states is small).
- **Best-of-n**: find approximate solution by throwing `n` darts.
- **Beam search**: find approximate solution by keeping track of `beam_width` partial solutions.

**Synergy between learning and search**
- Costs are learned from data
- Search: find the best solution given those costs

**Next time**: what if there are cycles (A → B → C → A)?
