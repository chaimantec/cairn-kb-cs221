---
title: Lecture 7 — Markov Decision Processes (Slides)
lecture: 7
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_file: https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/23c10936/mdp.py
rendered: https://stanford-cs221.github.io/autumn2025-lectures/?trace=mdp
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. This file transcribes the
  content of `mdp.py`. There are no traditional slide numbers; sections correspond
  to function calls in the source. Demo outputs (rollout sequences, Monte Carlo
  estimates, policy evaluation and value iteration results) were reproduced by
  executing the lecture's own code with `python -m edtrace.execute -m mdp` on
  2026-08-28 and reading the `@inspect` values from the execution trace. The
  Monte Carlo estimates depend on `np.random.seed(1)` at mdp.py:162; re-running
  the identical call sequence reproduces the trace exactly (tram −11.7, stay 12.8).
  The two graphviz figures (flaky_tram_graph, dice_game_graph) are described by a
  vision pass over the rendered PNGs cross-checked against the `draw_graph` source.
  The two distance plots are described from their exact data (blue points,
  x = iteration, y = distance), which the trace records; they render as vega-lite
  charts in the lecture UI and produce no PNG.
---

# Lecture 7 Slides — Markov Decision Processes

Lecture 7 generalizes search to **Markov decision processes (MDPs)**, where an action no longer leads deterministically to one state but to a *distribution* over next states. A solution is a **policy** — a function from state to action — whose quality is measured by the expected discounted sum of rewards (its **value**). The lecture introduces **rollouts** and Monte Carlo estimation, then two exact algorithms built on the same recurrence: **policy evaluation** (value of a given policy) and **value iteration** (value of the optimal policy, and the optimal policy itself). Everything runs on two running examples: the *flaky tram* (locations 1–10, walk vs. an unreliable tram) and a *dice game* (quit for $10, or stay for $4 and risk ending on a 1 or 2).

---

## Section: Recap and This Time (`main`)

**Last week: search**

- Search problem: start state, successors (action, cost, next state), is_end.
- An action from a state **deterministically** leads to a new state.

Demo recap on `TravelSearchProblem(num_locs=10)`: `start_state()` = 1; `successors(1)` = walk → 2 (cost 1), tram → 2 (cost 2); `is_end(2)` = False.

**This week: Markov Decision Processes (MDPs)**

- MDPs generalize search problems.
- Key difference: actions may have stochastic outcomes (e.g., rolling a dice).

Motivating example: how would you go to the grocery store — walk, bike or drive? Key: need to take into account uncertainty (traffic, time finding parking, etc.).

**Lecture summary (as given at the end of `main`):**

- MDPs generalize search problems (action results in a distribution over next states).
- Policy (solution): maps state to action.
- MDP + policy → rollout → utility.
- Value of policy = expected utility of policy.
- Policy evaluation: computes value of a given policy.
- Value iteration: computes value of the optimal policy.

---

## Section: MDPs (`introduce_mdp`)

**Origins of MDPs**

- Developed in operations research in the 1950s.
- "Markov" comes from Markov chains, where the past and future are independent given the state.
- "decision" means an agent taking actions.
- "process" means things happen sequentially over time.

**Example: flaky tram problem**

- Locations 1 to n (same as the search version).
- Walking from i to i+1 takes 1 minute (same).
- Taking a tram from i to 2i takes 2 minutes (same).
- But the tram breaks down with probability p (different).
- Goal: get from 1 to n in the least time **in expectation** (on average).

Demo on `FlakyTramMDP(num_locs=10, failure_prob=0.4)`: `start_state()` = 1; `successors(1)` returns three `Step`s:

| action | prob | reward | state |
| ------ | ---- | ------ | ----- |
| walk   | 1    | −1     | 2     |
| tram   | 0.6  | −2     | 2     |
| tram   | 0.4  | −2     | 1     |

`is_end(2)` = False. Note the tram failure transition 1 → 1 is a **cycle**.

(Diagram: `flaky_tram_graph.png` — graphviz rendering of `draw_graph(FlakyTramMDP(num_locs=10, failure_prob=0.4))`. See the vision-pass description at the end of this file.)

**Mathematical notation:**

- $\text{Actions}(s)$: all possible actions from state $s$.
- $\text{Reward}(s, a, s')$: reward obtained if we take action $a$ in state $s$ and end up in state $s'$.
- $T(s, a, s')$: probability of ending up in state $s'$ after taking action $a$ from state $s$.

Note that for each $(s, a)$: $\sum_{s'} T(s, a, s') = 1$.

**Second example: a dice game.** For each round:

- You choose quit or stay.
- If quit, you get $10 and the game ends.
- If stay, you get $4 and then the lecturer rolls a 6-sided dice.
- If the dice results in 1 or 2, the game ends.
- Otherwise, continue to the next round.

Demo on `DiceGameMDP`: `start_state()` = `"in"`; `successors("in")`:

| action | prob   | reward | state  |
| ------ | ------ | ------ | ------ |
| quit   | 1      | 10     | "end"  |
| stay   | 1/3    | 4      | "end"  |
| stay   | 2/3    | 4      | "in"   |

`is_end("end")` = True.

(Diagram: `dice_game_graph.png` — graphviz rendering of `draw_graph(DiceGameMDP())`. See the vision-pass description at the end of this file.)

**Comparison between search problems and MDPs:**

- Both have a `start_state` and `is_end` function.
- Both have a `successors` function that returns a list of possible actions and their consequences.
- Superficial difference: search problems use costs, MDPs use rewards.
- Deep difference: in search problems, each action has **one** next state; in MDPs, each action has a **distribution** over next states.

---

## Section: Policies (`introduce_policies`)

**What does a solution to an MDP look like?**

- In a search problem, a solution was a sequence of actions.
- This won't work for MDPs because actions have uncertain outcomes.
- Subsequent actions need to depend on the outcome of the previous action.
- In an MDP, a solution is a **policy**: a function that maps state to action.
- No matter what state you're in, the policy tells you what to do.

Demo with `FlakyTramMDP(num_locs=10, failure_prob=0.4)`:

- `always_walk_policy(3)` = `"walk"`.
- `tram_if_possible_policy(3)` = `"tram"` (2·3 = 6 ≤ 10).
- `tram_if_possible_policy(6)` = `"walk"` (2·6 = 12 > 10, tram unavailable).

**How do you evaluate a policy?** A **rollout** is a simulation of the policy in an MDP.

Demo with `np.random.seed(1)`, policy always walks: 9 steps, 1 → 2 → 3 → … → 10, each reward −1. Utility −9.

**Each rollout generates a utility, a discounted sum of rewards.**

**Discounting: how important is the future**

- Generally represented by a discount factor $0 \le \gamma \le 1$.
- Discount = 1 (no discounting): future is just as important as the present. Utility = −9 (all nine −1 rewards).
- Discount = 0 (full discounting): future doesn't matter at all. Utility = −1 (only the first reward counts).
- Discount = 0.5: next step matters half as much as the present step. Utility = −1.99609375 = $-(1 + 1/2 + 1/4 + \dots + 1/256)$.

**Rollouts of the policy that always takes the tram** (same seed, four rollouts):

| # | State sequence | Utility |
| - | -------------- | ------- |
| 1 | 1, 2, 4, 4, 8, 9, 10 | −10 |
| 2 | 1, 1, 2, 4, 8, 9, 10 | −10 |
| 3 | 1, 1, 2, 2, 2, 2, 4, 8, 9, 10 | −16 |
| 4 | 1, 2, 4, 4, 8, 9, 10 | −10 |

Note that the same policy yields different rollouts. (The repeated states are tram failures: probability 0.4 of staying put, at cost 2 each time.)

**So how do we evaluate a policy?** Rollout the policy many times and average the utilities. The expected utility (**value**) of a policy is defined as the average utility:

$$V_\pi(s) = \text{expected utility of starting in state } s \text{ and following policy } \pi.$$

Monte Carlo estimates (same seed):

- Always walk (1 rollout, deterministic): **−9.0**.
- Tram if possible (20 rollouts): **−11.7**.
- Dice game, always quit (1 rollout): **10.0**.
- Dice game, always stay (20 rollouts): **12.8**.

Note that these values are estimates — can we compute them exactly? (This motivates policy evaluation.)

---

## Section: Q-values (`introduce_q_values`)

First, we introduce **Q-values**.

$$Q(s, a, V) = \text{value of taking action } a \text{ in state } s, \text{ and obtaining some value } V.$$

Here, $V(s)$ is the value of some policy from state $s$.

**Flaky tram MDP example again.** We start with an initial set of values corresponding to terminating: `get_initial_values(mdp)` returns 0 for end states and **−100** otherwise (signaling invalid). (The function's docstring says −inf; the implementation uses −100.)

Warmup: compute the Q-value for just one state.

- Choose state (arbitrarily): 9.
- Policy: tram if possible. In state 9, 2·9 = 18 > 10, so the policy action is `"walk"`.
- Successors of that action: walk, prob 1, reward −1, state 10.
- Key step: the value of taking that action and then terminating:

$$Q(9, \text{walk}, V) = 1 \cdot (-1 + 1 \cdot V(10)) = -1 + 0 = -1.$$

**Two things to get to policy evaluation:**

- Do this for all states.
- Rinse and repeat with the values (instead of the terminating values).

This is called **bootstrapping**:

- After 0 steps: value of terminating.
- After 1 step: value of following the policy for 1 step, then terminating.
- After 2 steps: value of following the policy for 2 steps, then terminating.
- … and so on.

---

## Section: Convergence (`introduce_convergence`)

How do we know when an iterative algorithm is done? Values evolve: [0] values → [1] values → [2] values → [3] values → [4] values …

We look at the **maximum change** between iterations:

Demo: `values = {1: 0.2, 2: 1.0, 3: -1.0}`, `new_values = {1: 0.2, 2: 1.1, 3: 1.0}`. Distance = $\max(|0.2 - 0.2|, |1.0 - 1.1|, |-1.0 - 1.0|) = 2.0$.

When the distance is less than some tolerance (say $10^{-5}$), we terminate. This is used for both policy evaluation and value iteration.

---

## Section: Policy Evaluation (`introduce_policy_evaluation`)

So far: can perform many rollouts and average to estimate the value of a policy $V_\pi(s)$. Is there a way to compute $V_\pi(s)$ more efficiently? **Yes** — key: compute the recurrences (recall dynamic programming for search problems). The resulting algorithm is called **policy evaluation**.

Recurrence (given later in the value iteration section, also shown here):

$$V_\pi(s) = \sum_{s'} T(s, \pi(s), s') \cdot \Big( R(s, \pi(s), s') + \gamma\, V_\pi(s') \Big)$$

**The algorithm (`policy_evaluation`):**

1. Initialize values: 0 for end states, −100 elsewhere.
2. For each iteration (up to `max_iters = 100`):
   - For each non-end state, compute the Q-value of the **policy action** $\pi(s)$: new value = $\sum_{s'} T(s, \pi(s), s') (R(s, \pi(s), s') + \gamma \cdot V(s'))$.
   - End states keep value 0.
   - Compute the distance = max absolute change; append it.
   - Stop if distance < tolerance ($10^{-5}$).
   - Otherwise replace values with the new values and repeat.

**Flaky tram demo** — policy: tram if possible (`tram_if_possible_policy`). Converges in 28 sweeps (loop iterations 0–27; the lecturer's "exit after 27 iterations" refers to the last iteration index). Final values:

| state | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
| ----- | - | - | - | - | - | - | - | - | - | -- |
| value | −12.000008 | −8.666667 | −7.333333 | −5.333333 | −3.333333 | −4 | −3 | −2 | −1 | 0 |

Distance sequence per iteration:

```
99, 99, 99, 99, 58.97, 24.82, 20.03, 13.41, 8.07, 4.52, 2.42, 1.24, 0.62,
0.30, 0.15, 0.07, 0.03, 0.015, 0.0068, 0.0031, 0.0014, 0.0006, 0.00028,
0.00012, 5.4e-5, 2.4e-5, 1.0e-5, 4.5e-6  → stop (below 1e-5)
```

**Two phases:**

1. Trying to reach all the states (the first four iterations, distance stuck at 99 — each iteration propagates values one step further from the end state).
2. Distance decreases exponentially (observed ratios ≈ 0.42, approaching 0.4 in the limit — the probability of the tram-failure self-loop; the slow approach is a Jordan-block effect of states 1 and 2 both self-looping while 1 feeds 2).

Early-iteration values (the lecturer reads these off the UI in the lecture):

- After 1 iteration: V(1..4) = −102, **V(5) = −42**, V(6..8) = −101, V(9) = −1. State 5's −42 is 0.6·(−2 + V(10)=0) + 0.4·(−2 + V(5)=−100) = −1.2 + −40.8 = −42.0 (the lecturer's on-the-fly 0.6·−2 + 0.4·−100 = −41.2 drops the −2 reward on the failure branch).
- After 2 iterations: V(1) = −104, V(2) = −104.
- After 3 iterations: **V(1) = −106** — the value the lecturer quotes when pointing at the early table ("this is still minus 106": from state 1 you cannot reach state 10 in two steps).

(Distance plot: `policy evaluation` — blue points, x = iteration, y = distance, from the `result.distances` above. No PNG; rendered as a vega-lite chart in the lecture UI.)

Sanity checks on the fixed point: $V(5) = -2 + 0.6 \cdot V(10) + 0.4 \cdot V(5)$ gives $V(5) = -2/0.6 = -3.\overline{3}$ ✓; $V(1) = -2 + 0.6 \cdot V(2) + 0.4 \cdot V(1)$ gives $V(1) = (-2 + 0.6 \cdot (-8.\overline{6}))/0.6 = -12$ ✓.

**Dice game demo** — policy: always stay. Converges in 39 sweeps (iterations 0–38), final distance 7.6e-6. Final values: `"in"` → 11.999977, `"end"` → 0. (Recurrence: $V(\text{in}) = 4 + \frac{2}{3} V(\text{in})$, so $V(\text{in}) = 4 \cdot 3 = 12$.)

Distance sequence starts 37.33, 24.89, 16.59, 11.06, 7.37, 4.92, 3.28, … and shrinks by exactly $2/3$ each iteration — the probability of staying in, so the error decays exponentially to $< 10^{-5}$ in 39 sweeps.

(Distance plot: `policy evaluation` — blue points, x = iteration, y = distance.)

**Summary:**

- Policy evaluation: computes value of a given policy.
- Key quantity: Q-value $Q(s, a, V)$ = value of taking action $a$ in state $s$, and obtaining some value $V$.
- Bootstrapping: use values to compute new values using the recurrence.

---

## Section: Value Iteration (`introduce_value_iteration`)

- Policy evaluation: computes value of a given policy.
- **Value iteration: computes value of the optimal policy** … and in the process, constructs the optimal policy.

Finding the optimal policy might seem much harder than computing the value of a policy (… think computing $f(x)$ versus $\min_x f(x)$) — but the two are actually quite similar!

History: Richard Bellman's work on dynamic programming — [Bellman, 1957](https://gwern.net/doc/statistics/decision/1957-bellman-dynamicprogramming.pdf).

**The two recurrences:**

Policy evaluation:

$$V_\pi(s) = \sum_{s'} T(s, \pi(s), s') \Big( R(s, \pi(s), s') + \gamma V_\pi(s') \Big)$$

Value iteration:

$$V^*(s) = \max_a \sum_{s'} T(s, a, s') \Big( R(s, a, s') + \gamma V^*(s') \Big)$$

**Key difference: optimize over actions $a$ instead of taking the policy action $\pi(s)$.**

**Flaky tram warmup** — state 9, initial values (−100 except 0 at 10):

- Q(9, walk) = $1 \cdot (-1 + 1 \cdot V(10)) = -1$.
- Q(9, tram) = $1 \cdot (-100 + 1 \cdot V(10)) = -100$ (tram from 9 has 2·9 = 18 > 10, so it's the invalid action: prob 1, reward −100, state 10).
- $V^*(9) = \max(-1, -100) = -1$, optimal action = walk.

**The algorithm (`value_iteration`):**

1. Initialize values (0 end, −100 elsewhere) and the policy $\pi$ = all None.
2. For each iteration:
   - For each non-end state, compute $V^*(s) = \max_a Q(s, a, V)$ over all actions (via `value_iteration_for_state`: compute every action's Q-value, take `np.max`, record the argmax action in $\pi$).
   - End states keep value 0.
   - Distance = max absolute change; stop when < tolerance.
   - Otherwise replace values and repeat.

**Full run on the flaky tram.** Converges in 22 sweeps (iterations 0–21), final distance 9.96e-6. Final values:

| state | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
| ----- | - | - | - | - | - | - | - | - | - | -- |
| value | −7.33335 | −6.33334 | −5.33334 | −4.33333 | −3.33333 | −4 | −3 | −2 | −1 | 0 |

Optimal policy $\pi^*$: walk from 1, 2, 3, 4; **tram at 5**; walk from 6, 7, 8, 9; none at 10. (At 5, tram succeeds 0.6 of the time straight to 10 — worth the risk: $V^*(5) = -2 + 0.6 \cdot 0 + 0.4 V^*(5) = -3.\overline{3}$ vs. walking $-1 + V^*(6) = -5$.)

Distance sequence per iteration:

```
99, 98, 97, 96, 27.28, 19.52, 9.28, 3.71, 1.48, 0.59, 0.24, 0.095, 0.038,
0.015, 0.0061, 0.0024, 0.00097, 0.00039, 0.00016, 6.2e-5, 2.5e-5, 9.96e-6
→ stop (below 1e-5)
```

Again two phases: first the values propagate one step per iteration (distance drops by 1 each time), then decay exponentially with ratio exactly 0.4 (the tram failure probability — checked against a 1e-14-tolerance run: ratios 0.4002, 0.3999, 0.3995, …).

(Distance plot: `value iteration` — blue points, x = iteration, y = distance.)

Note the comparison: the always-tram policy evaluates at −12 from state 1, while the optimal policy achieves −7.33 — walking early on beats the tram because a failure early compounds, and the tram only becomes worth it at state 5.

**Summary:**

- Value iteration: computes value of the optimal policy.
- Takes max over actions compared to policy evaluation, which takes the policy action.
- Bootstrapping: use values to compute new values using the recurrence (same).

---

## Section: The Code — MDP Interface (`Step`, `MDP`, `FlakyTramMDP`, `DiceGameMDP`)

The interface mirrors the search problem interface deliberately, to highlight the parallel.

```python
@dataclass(frozen=True)
class Step:
    action: Any
    prob: float  # New: the probability that we ended up here
    reward: float
    state: Any

class MDP:
    def start_state(self) -> Any: ...
    def successors(self, state: Any) -> list[Step]: ...
    def is_end(self, state: Any) -> bool: ...
    def discount(self) -> float: ...
```

`FlakyTramMDP(num_locs, failure_prob)`:

- `start_state()` = 1; `is_end(s)` = `s == num_locs`; `discount()` = 1 (no discounting for now).
- `successors(state)`:
  - **Walk**: if `state + 1 <= num_locs`, append `Step("walk", prob=1, reward=-1, state=state+1)`.
  - **Tram**: if `2*state <= num_locs`, append success `Step("tram", prob=1-failure_prob, reward=-2, state=2*state)` and failure `Step("tram", prob=failure_prob, reward=-2, state=state)`.
  - Otherwise (**invalid action**): `Step("tram", prob=1, reward=-100, state=num_locs)` — just end, but penalize.

`DiceGameMDP`:

- `start_state()` = `"in"`; `is_end(s)` = `s == "end"`; `discount()` = 1.
- `successors("in")` = quit: (prob 1, reward 10, state "end"); stay: (prob 1/3, reward 4, state "end"); stay: (prob 2/3, reward 4, state "in").

## Section: The Code — Rollouts and Monte Carlo (`generate_rollout`, `compute_utility`, `monte_carlo_policy_evaluation`)

`compute_utility(steps, discount)`: rewards are weighted $\gamma^i$ — `rewards = [step.reward * discount**i for i, step in enumerate(steps)]` — then summed.

`generate_rollout(mdp, policy)`: loop from the start state until `is_end`: pick `action = policy(state)`; filter `mdp.successors(state)` to that action; draw one successor with `np.random.choice(len(successors), p=probs)`; append it and advance. Returns a `Rollout` whose utility is the discounted sum.

`monte_carlo_policy_evaluation(mdp, policy, num_rollouts)`: generate `num_rollouts` rollouts, return `np.mean` of their utilities.

## Section: The Code — Exact Evaluation (`get_initial_values`, `get_action_successors`, `compute_q_value`, `compute_distance`, `policy_evaluation`)

- `get_states(mdp)`: collect all states by traversing successors from the start state.
- `get_initial_values(mdp)`: `{state: 0 if is_end(state) else -100 for state in states}`.
- `get_action_successors(mdp, state)`: group the successor list into a dict `action -> list[Step]`.
- `compute_q_value(successors, discount, values)`: for each step, `utility = step.reward + discount * values[step.state]`; return $\sum_s \text{prob} \cdot \text{utility}$.
- `compute_distance(values, new_values)`: $\max_s |V(s) - V'(s)|$.
- `policy_evaluation(mdp, policy, max_iters=100, tolerance=1e-5)`: the iterative sweep described above; returns `PolicyEvaluationResult(values, distances)`.

## Section: The Code — Optimal Values (`value_iteration`, `value_iteration_for_state`)

- `value_iteration_for_state(mdp, state, values)`: for every action, `q_values.append(compute_q_value(...))`; return `(np.max(q_values), actions[np.argmax(q_values)])` — that is, $V^* = \max_a Q(s, a, V^*)$, and the argmax action is $\pi^*(s)$.
- `value_iteration(mdp, max_iters=100, tolerance=1e-5)`: same sweep, but each state's new value and the recorded policy action come from `value_iteration_for_state`; returns `ValueIterationResult(values, pi, distances)`.

## Section: The Code — Figure Generation (`draw_graph`)

`draw_graph(mdp)` traverses the MDP and builds a graphviz `Digraph`: end states are `doublecircle` nodes; other states are `circle` nodes. For each non-end state and each action, a **dotted** node labeled `"state,action"` is inserted, connected from the state by an unlabeled edge; from the action node, one edge per successor goes to the successor state with label `f"p={prob:.2f},r={reward}"`.

## Figures (vision pass)

**`flaky_tram_graph.png`** — 19 circular nodes for the 10 states plus one dotted action node per action at each non-end state, node 10 double-circled. From state i, edges run through dotted node `i,walk` to i+1 labeled `p=1.00,r=-1`, and through `i,tram` to 2i labeled `p=0.60,r=-2` and back to i labeled `p=0.40,r=-2` for i ≤ 5; for i in 6–9 the tram edge goes to 10 labeled `p=1.00,r=-100`. Black on white, default graphviz styling. (Vision pass on the rendered PNG, 2026-08-28: all labels confirmed readable — "p=1.00,r=-1", "p=0.60,r=-2", "p=0.40,r=-2", "p=1.00,r=-100" — all 18 action nodes dotted, edges from state to action nodes unlabeled, node 10 double-circled, strictly black-and-white; no discrepancies with the source definition.)

**`dice_game_graph.png`** — circle node `in`, double-circle node `end`; dotted nodes `in,quit` (edge to `end` labeled `p=1.00,r=10`) and `in,stay` (edges to `end` labeled `p=0.33,r=4` and back to `in` labeled `p=0.67,r=4`). Black on white. (Vision pass on the rendered PNG, 2026-08-28: labels read exactly "p=1.00,r=10", "p=0.33,r=4", "p=0.67,r=4" — 0.33/0.67, not rounded values — "end" double-circled, action nodes dotted, strictly black-and-white; no discrepancies with the source definition.)
