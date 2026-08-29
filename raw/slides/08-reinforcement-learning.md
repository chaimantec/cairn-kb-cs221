---
title: Lecture 8 — Reinforcement Learning (Slides)
lecture: 8
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_file: https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/main/reinforcement_learning.py
rendered: https://stanford-cs221.github.io/autumn2025-lectures/?trace=reinforcement_learning
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. This file transcribes the
  content of `reinforcement_learning.py`. There are no traditional slide numbers;
  sections correspond to function calls in the source. All demo outputs (successor
  tables, rollout sequences, leaderboard values, estimated-MDP internals, Q-value
  updates) were read on 2026-08-29 from the published execution trace committed in
  the lecture repo at `var/traces/reinforcement_learning.json` — the same trace the
  rendered lecture UI displays. The stochastic values depend on `np.random.seed(1)`,
  set at reinforcement_learning.py:113, 233, 415, 552 and 623, so the trace is
  exactly reproducible. The figure `flaky_tram_graph.png` is described by the
  vision pass in raw/slides/07-markov-decision-processes.md (same MDP and graph
  code); `rl-framework.png`, `policy_evaluation_recurrence.png` and
  `value_iteration_recurrence.png` are described by a vision pass over the PNGs
  (see the Figures section at the end of this file).
---

# Lecture 8 Slides — Reinforcement Learning

Lecture 8 turns MDPs into a learning problem: the agent no longer knows the transition probabilities $T$ (or even the rewards), and must discover good behavior by interacting with the environment. An **RL algorithm** is an agent with two methods — `get_action` (produce an action) and `incorporate_feedback` (absorb the resulting reward and next state). The lecture builds four increasingly direct approaches on the flaky tram MDP: **model-based value iteration** (estimate the MDP from feedback, then value-iterate), **model-free Monte Carlo** (estimate Q-values by averaging rollout utilities), **SARSA** (bootstrap the estimate mid-rollout, on-policy) and **Q-learning** (bootstrap off-policy, directly estimating the optimal Q-values). Each is run for real on the tram and its utility reported on the leaderboard.

---

## Section: Recap and This Time (`main`)

**Last time: Markov decision processes**

- MDP: start state, successors (action, probability, reward, next state), is_end, discount.
- Policy: maps state to action; value of policy = expected utility; policy evaluation and value iteration compute them.

**This time: reinforcement learning**

- RL: learn the optimal policy from interacting with the environment.
- Agent (RL algorithm): `get_action` and `incorporate_feedback`.
- Model-based value iteration: estimate the MDP then compute the optimal policy.
- Model-free Monte Carlo: estimate Q-values directly from rollouts (on-policy).
- SARSA: estimate Q-values of the current policy as you rollout (on-policy, bootstrapping).
- Q-learning: estimate Q-values of the optimal policy (off-policy, bootstrapping).

**Next time:** how do we deal with huge state spaces? (policy gradient)

---

## Section: MDP Recap (`review_mdp`)

The lecture opens with a condensed recap of lecture 7, all on `FlakyTramMDP(num_locs=10, failure_prob=0.4)`:

- `start_state()` = 1.
- `successors(1)`:

| action | prob | reward | state |
| ------ | ---- | ------ | ----- |
| walk   | 1    | −1     | 2     |
| tram   | 0.6  | −2     | 2     |
| tram   | 0.4  | −2     | 1     |

- `is_end(2)` = False.

(Diagram: `flaky_tram_graph.png` — graphviz rendering of `draw_graph(FlakyTramMDP(num_locs=10, failure_prob=0.4))`; described in the Figures section of raw/slides/07-markov-decision-processes.md.)

- **Policy**: maps state to action. `tram_if_possible_policy(state=1)` = `"tram"`.
- **Rollout** with `np.random.seed(1)`: tram→1 (0.4, failed), tram→2 (0.6), tram→2 (0.4), tram→2 (0.4), tram→2 (0.4), tram→2 (0.4), tram→4 (0.6), tram→8 (0.6), walk→9 (1), walk→10 (1) — 10 steps, utility **−18** (all tram rides cost 2, the last two walks cost 1 each; with no discounting the tram's repeated failures pile up).
- **Policy evaluation** computes the value of the given policy. For `tram_if_possible_policy` (discount 1):

| state | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
| ----- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| $V_\pi$ | −12.00 | −8.67 | −7.33 | −5.33 | −3.33 | −4 | −3 | −2 | −1 | 0 |

- **Value iteration** computes the value of the optimal policy:

| state | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
| ----- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| $V^*$ | −7.33 | −6.33 | −5.33 | −4.33 | −3.33 | −4 | −3 | −2 | −1 | 0 |
| $\pi^*$ | walk | walk | walk | walk | tram | walk | walk | walk | walk | — |

**The recurrences** (reviewed via the two recurrence figures; see the Figures section):

- Policy evaluation: $V_\pi(s) = Q_\pi(s, \pi(s))$; $Q_\pi(s, a) = \sum_{s'} T(s, a, s') \left( R(s, a, s') + \gamma V_\pi(s') \right)$.
- Value iteration: $V^*(s) = \max_a Q^*(s, a)$; $Q^*(s, a) = \sum_{s'} T(s, a, s') \left( R(s, a, s') + \gamma V^*(s') \right)$; $\pi^*(s) = \arg\max_a Q^*(s, a)$.

**But can we find the optimal policy if we don't know the MDP?**

---

## Section: The RL Setting (`introduce_rl`)

**Reinforcement learning setting** (figure `rl-framework.png`):

Repeat:
- **Agent** produces action.
- **Environment** produces reward and observation.

Intuitively: a good agent should try various actions to find ones that lead to good rewards; then it should *learn* to keep doing those actions (those actions are *reinforced*).

- "Reinforcement learning really is the metaphor for life." In MDPs we don't know what outcomes will be, but at least know their probabilities; in RL we don't even know the probabilities — that's real life.
- This lecture assumes the environment is backed by an MDP and the observation is the next state. (In real life you only observe part of the state — partially observable MDPs.)

**Demo — a static agent on the tram** (`np.random.seed(1)`): an agent that always follows `tram_if_possible_policy` but never learns. `simulate(mdp, rl, num_trials=10)` returns a mean utility of **−11.6**, which goes on the leaderboard:

| method | value |
| ------ | ----- |
| tram_if_possible_policy | −11.6 |

The simulation yields some estimated value (expected utility) — but the agent doesn't do anything with the feedback.

**Difference between policy and agent:**

- Policy: maps state to action, doesn't change over time (static).
- Agent (RL algorithm): maps state to action, can change over time (dynamic).

Intuition: the RL algorithm uses the feedback to improve its internal policy. How should we perform this update?

---

## Section: The Code — the RL Interface (`RLAlgorithm`, `StaticAgent`, `simulate`, `sample_transition`, `walk_tram_policy`)

- `RLAlgorithm`: abstract class with two methods — `get_action(state)` (sends an action to the environment) and `incorporate_feedback(state, action, reward, next_state, is_end)` (absorbs the environment's response).
- `StaticAgent`: implements `RLAlgorithm` by holding a fixed `policy`; `get_action` returns `self.policy(state)` and `incorporate_feedback` does nothing.
- `simulate(mdp, rl, num_trials)`: runs the agent on the environment for `num_trials` rollouts. Each trial: start at `mdp.start_state()`, loop while not end — agent sends `action`, environment responds via `sample_transition`, feedback is passed to `rl.incorporate_feedback`, the step is appended — then the trial's `Rollout(steps, discount).utility` is appended to `utilities`. Returns `np.mean(utilities)`.
- `sample_transition(mdp, state, action)`: filters `mdp.successors(state)` to the given action, then `np.random.choice` over the successors with probabilities `p`, returning the chosen `Step`.
- `walk_tram_policy(num_locs, state)`: exploration policy — if `2 * state <= num_locs`, choose `"walk"` or `"tram"` at random; otherwise only `"walk"` is valid.

---

## Section: Model-Based Value Iteration (`introduce_model_based`)

**What makes RL hard:** we don't know the MDP — otherwise we could just use value iteration. **Idea: estimate (learn) the MDP from feedback.**

**Model-based value iteration:**

1. Exploration: estimate the MDP using an exploration policy (random).
2. Compute the optimal policy of the estimated MDP.
3. Exploitation: follow this policy.

**Demo** (`FlakyTramMDP(num_locs=10, failure_prob=0.4)`, `np.random.seed(1)`):

- Exploration policy `walk_tram_policy` tries all valid actions — three draws at state 1 give `"tram"`, `"tram"`, `"walk"`; at state 6 (tram unavailable) it must walk: `"walk"`.
- Agent = `ModelBasedValueIteration(exploration_policy, discount=1)`. Before exploration, `get_action(state=1)` = `"walk"` (a random valid action from the exploration policy).

**Stage 1 — explore.** `simulate(mdp, rl, num_trials=10)` → mean utility **−9.0** (suboptimal, but we're learning!). Leaderboard:

| method | value |
| ------ | ----- |
| tram_if_possible_policy | −11.6 |
| model_based_value_iteration.explore | −9.0 |

The internals of the estimated MDP after these 10 trials (true MDP vs. estimated):

- `start_state`: 1 vs. 1.
- `successors(1)`: walk → 2 (p = 1, r = −1) in both; tram → 2 with true p = 0.6 vs. estimated p = **0.667** (2/3), tram → 1 with true p = 0.4 vs. estimated p = **0.333** (1/3). The estimated probabilities come from transition counts — the exploration phase happened to see the tram fail once in three tries at state 1.
- `is_end(9)`: False in both.

The more the agent explores, estimated MDP → true MDP (assuming the exploration policy tries all valid actions).

**Stage 2 — compute the optimal policy of the estimated MDP.** `rl.run_value_iteration()` runs value iteration on the estimated MDP and stores the resulting policy as the exploitation policy: {1: walk, 2: walk, 3: walk, 4: walk, 5: tram, 6: walk, 7: walk, 8: walk, 9: walk, 10: None}. Compared with the optimal policy of the true MDP (from `value_iteration(mdp)`): on this seed the two are **identical** — both walk everywhere except tram from 5. (The lecture's point stands generally: the two are "similar but not quite the same", and the more the agent explores, the closer they get.)

**Stage 3 — exploit.** `simulate(mdp, rl, num_trials=10)` → mean utility **−6.6**:

| method | value |
| ------ | ----- |
| tram_if_possible_policy | −11.6 |
| model_based_value_iteration.explore | −9.0 |
| model_based_value_iteration.exploit | −6.6 |

**Notes:**

- The utility of the exploration phase is suboptimal, but we're learning!
- In practice, don't restrict to two phases: always continue refining the estimated MDP, and gradually move the policy from full exploration to full exploitation.

**Can we estimate the optimal policy more directly?**

---

## Section: The Code — Estimating the MDP (`ModelBasedValueIteration`, `EstimatedMDP`)

- `EstimatedMDP(discount)`: an MDP whose start state, rewards, transitions and end states are learned from feedback. Holds `start_state_`, `rewards` (a `(state, action, next_state) → reward` dict), `transitions` (a `state → action → next_state → count` nested defaultdict), `end_states` (a set), and `discount_`.
  - `start_state()` returns the first state ever seen.
  - `successors(state)`: for each action and next state seen from `state`, `prob = count / total_count` and `reward = self.rewards[(state, action, next_state)]`; returns the `Step`s.
  - `is_end(state)`: membership in `end_states`.
  - `incorporate_feedback(state, action, reward, next_state, is_end)`: sets `start_state_` if unset, records the reward, increments the transition count, and adds `next_state` to `end_states` if `is_end`.
- `ModelBasedValueIteration(exploration_policy, discount)`: holds an `EstimatedMDP`; `exploitation_policy` starts as `None`.
  - `get_action`: follows the exploration policy until `exploitation_policy` exists, then looks it up.
  - `incorporate_feedback`: forwards to `self.mdp.incorporate_feedback`.
  - `run_value_iteration()`: `result = value_iteration(self.mdp)`; `self.exploitation_policy = result.pi`.

---

## Section: Model-Free Monte Carlo (`introduce_model_free_monte_carlo`)

**Previously:** model-based value iteration estimates the MDP first, then value-iterates it. The optimal policy is $\pi^*(s) = \arg\max_a Q^*(s, a)$ where $Q^*(s, a) = \sum_{s'} T(s, a, s') \left( R(s, a, s') + \gamma V^*(s') \right)$.

**Can we estimate $Q^*(s, a)$ directly? Yes!** Key idea: just rollout the policy and average the utilities.

**The utility calculation** (`example_utility_calculation`): the utility at each step is the discounted sum of rewards from that point on. For the rollout walk (−1) → tram (−2) → tram (−2), with discount 1:

- step 0: −1 + (1·−2) + (1²·−2) = **−5**
- step 1: −2 + (1·−2) = **−4**
- step 2: −2 + (1·0) = **−2**

There is a nice recurrence between the utilities: $u_0 = r_0 + \gamma u_1$, $u_1 = r_1 + \gamma u_2$ (both asserted in the code).

**Which policy should we use to rollout?** For model-based VI the exploration phase used a purely random policy. Here, something more sophisticated: **epsilon-greedy**.

- With probability $\epsilon$, choose a random action according to the exploration policy.
- With probability $1 - \epsilon$, choose the best action according to the current estimated Q-values.

**Demo** (`ModelFreeMonteCarlo(exploration_policy, epsilon=0.4, discount=1)`, `np.random.seed(1)`):

- First `get_action(state=1)` = `"tram"` — no action has been tried yet, so a random action.
- Then three pieces of feedback are fed in by hand — walk: 1→2 (−1), tram: 2→2 (−2), tram: 2→4 (−2, end). At the end of the episode the algorithm walks backwards: utilities −5 (step 0), −4 (step 1), −2 (step 2), accumulating into `sum_utilities`: (1, walk) → −5, (2, tram) → −6, with counts (1, walk) → 1, (2, tram) → 2.
- Subsequent `get_action(state=1)` calls are now epsilon-greedy over Q(1, walk) = −5/1 = **−5.0**: the next four calls return `"walk"`, `"walk"`, `"tram"`, `"walk"` (the greedy best, with some exploration noise).

**Run for real:** `simulate(mdp, rl, num_trials=20)` → mean utility **−9.45**:

| method | value |
| ------ | ----- |
| tram_if_possible_policy | −11.6 |
| model_based_value_iteration.explore | −9.0 |
| model_based_value_iteration.exploit | −6.6 |
| model_free_monte_carlo | −9.45 |

**Summary:**

- Model-free Monte Carlo: estimate Q-values of the current policy.
- Directly uses rollouts, bypassing estimating the MDP.
- Use epsilon-greedy to balance exploration and exploitation.

**Problem: in life, you only get one rollout.** Can we update the Q-values before the rollout is over?

---

## Section: The Code — Model-Free Monte Carlo (`ModelFreeMonteCarlo`)

- State: `sum_utilities` (state → action → running sum of utilities seen from that (state, action)) and `counts` (state → action → visitation count), plus the current `rollout` list of `Step`s.
- `Q(state, action)` = `sum_utilities[state][action] / counts[state][action]`.
- `get_action(state)`: if no action has ever been tried from `state`, return a random action from the exploration policy; otherwise epsilon-greedy — with probability `epsilon` explore, otherwise return `self.pi(state)` (the argmax action under current Q).
- `incorporate_feedback(...)`: append the step to the current rollout. At the end of an episode (`is_end`), walk backwards over the rollout computing `utilities[i] = step.reward + discount * utilities[i + 1]`, adding each utility into `sum_utilities` and incrementing `counts` for the (state, action) that produced that step; then reset the rollout.

---

## Section: SARSA (`introduce_sarsa`)

**Previously:** model-free Monte Carlo estimates Q-values directly from rollouts. **SARSA: update Q-values as you rollout!**

If we don't rollout completely, how do we get the utility (which requires going towards the end)?

**Key insight: bootstrapping!** Combine the immediate reward with a model estimate of the future:

- Monte Carlo: $u = r_0 + \gamma r_1 + \gamma^2 r_2 + \dots + \gamma^n r_n$.
- Bootstrapping (SARSA): $u = r_0 + \gamma Q_\pi(s_1, a_1)$.

Then perform a **gradient update** to move $Q_\pi(s, a)$ towards $u$:

$$Q_\pi(s, a) \leftarrow Q_\pi(s, a) + \eta \left( u - Q_\pi(s, a) \right)$$

where $\eta$ is the learning rate (`learning_rate=0.1` in the demo).

**Demo** (`SARSA(exploration_policy, epsilon=0.4, discount=1, learning_rate=0.1)`, `np.random.seed(1)`):

- First `get_action(state=1)` = `"tram"` (no Q-values yet → random).
- Hand-fed feedback: (1, walk, −1, 2), (2, tram, −2, 2), (2, tram, −2, 4, end). After each step the Q-value is updated immediately:
  - After step 1: next_action from the *current policy* (`get_action`, on-policy — a random action since Q(2, ·) is empty); utility = −1 + 1·0 = −1; Q(1, walk) ← 0 + 0.1·(−1 − 0) = **−0.1**.
  - After step 2: utility = −2 + 0 = −2; Q(2, tram) ← 0 + 0.1·(−2 − 0) = **−0.2**.
  - After step 3 (end): next_action is still drawn from the current policy; utility = −2 + 0 = −2; Q(2, tram) ← −0.2 + 0.1·(−2 − (−0.2)) = **−0.38**.
- Final Q: Q(1, walk) = −0.1, Q(2, tram) = −0.38; final `get_action(state=1)` = `"tram"`.

**Run for real:** `simulate(mdp, rl, num_trials=20)` → mean utility **−9.6**:

| method | value |
| ------ | ----- |
| tram_if_possible_policy | −11.6 |
| model_based_value_iteration.explore | −9.0 |
| model_based_value_iteration.exploit | −6.6 |
| model_free_monte_carlo | −9.45 |
| sarsa | −9.6 |

**Summary:**

- SARSA: estimates Q-values of the current policy $Q_\pi(s, a)$ as you rollout.
- Bootstrapping: estimate utility using model estimate of the future.
- Gradient update: move Q-values towards the estimated utility.

But we are only computing Q-values of the current policy $Q_\pi(s, a)$ (**on-policy**). Can we directly estimate Q-values of the optimal policy $Q^*(s, a)$?

---

## Section: The Code — SARSA (`SARSA`)

- Holds a Q-table `self.Q` (state → action → Q-value), plus `exploration_policy`, `epsilon`, `discount`, `learning_rate`.
- `get_action(state)`: if Q has no entry for the state, a random exploration action; otherwise epsilon-greedy (explore with probability `epsilon`, else `self.pi(state)`).
- `pi(state)`: the argmax action under the current Q-values (`None` if no actions known).
- `incorporate_feedback(state, action, reward, next_state, is_end)`: the update that defines SARSA —
  `next_action = self.get_action(next_state)` (on-policy: the action the current policy would actually take),
  `utility = reward + self.discount * self.Q[next_state].get(next_action, 0)`,
  `self.Q[state][action] += self.learning_rate * (utility - self.Q[state][action])`.
  Note the comment in the source: it is important to use `get_action` (not `pi`) to get on-policy behavior.

---

## Section: Q-Learning (`introduce_q_learning`)

- SARSA: estimate Q-values of the current policy $Q_\pi(s, a)$.
- **Q-learning: estimate Q-values of the optimal policy $Q^*(s, a)$.**

But we don't know the optimal policy… (the resolution: bootstrap with the *max*, i.e. the policy that the Q-table itself implies — see the code below).

**Demo** (`QLearning(exploration_policy, epsilon=0.4, discount=1, learning_rate=0.1)`, `np.random.seed(1)`):

- First `get_action(state=1)` = `"tram"` (no Q-values yet → random).
- Same hand-fed feedback sequence. This time `next_action = self.pi(next_state)` — the *greedy* action under the current Q-table, not the exploration policy's action:
  - After step 1: pi(2) = None (nothing known about 2); utility = −1; Q(1, walk) ← 0.1·(−1 − 0) = **−0.1**.
  - After step 2: pi(2) = "tram" (Q(2, tram) = 0 exists); utility = −2 + 0 = −2; Q(2, tram) ← 0.1·(−2 − 0) = **−0.2**.
  - After step 3 (end): pi(4) = None; utility = −2; Q(2, tram) ← −0.2 + 0.1·(−2 − (−0.2)) = **−0.38**.
- Final Q: Q(1, walk) = −0.1, Q(2, tram) = −0.38 (identical values to SARSA on this tiny demo — the difference is in *which* next action enters the bootstrap); final `get_action(state=1)` = `"walk"`.

**Run for real:** `simulate(mdp, rl, num_trials=20)` → mean utility **−9.9**:

| method | value |
| ------ | ----- |
| tram_if_possible_policy | −11.6 |
| model_based_value_iteration.explore | −9.0 |
| model_based_value_iteration.exploit | −6.6 |
| model_free_monte_carlo | −9.45 |
| sarsa | −9.6 |
| q_learning | −9.9 |

**Summary:**

- Q-learning: estimates Q-values of the optimal policy $Q^*(s, a)$ (**off-policy**).
- Like SARSA, uses bootstrapping and gradient updates.

---

## Section: The Code — Q-Learning (`QLearning`)

`QLearning` subclasses `SARSA` and overrides only `incorporate_feedback`:

```python
def incorporate_feedback(self, state, action, reward, next_state, is_end):
    # Important: use `self.pi` (not `self.get_action`) to get off-policy
    next_action = self.pi(next_state)
    utility = reward + self.discount * self.Q[next_state].get(next_action, 0)
    self.Q[state][action] += self.learning_rate * (utility - self.Q[state][action])
```

The one-line difference from SARSA: `self.pi` instead of `self.get_action` for choosing the bootstrapped next action — SARSA estimates the Q-values of the *current* (exploration-softened) policy; Q-learning estimates those of the *optimal* policy as implied by the Q-table itself, regardless of how the agent actually behaves.

---

## Figures (vision pass)

**`flaky_tram_graph.png`** — graphviz rendering of `draw_graph(FlakyTramMDP(num_locs=10, failure_prob=0.4))`, identical to the figure in lecture 7 (see raw/slides/07-markov-decision-processes.md, Figures section): 19 circular nodes for the 10 states plus one dotted action node per action at each non-end state, node 10 double-circled; from state i, edges run through dotted node `i,walk` to i+1 labeled `p=1.00,r=-1`, and through `i,tram` to 2i labeled `p=0.60,r=-2` and back to i labeled `p=0.40,r=-2` for i ≤ 5; for i in 6–9 the tram edge goes to 10 labeled `p=1.00,r=-100`.

**`rl-framework.png`** — a two-node loop diagram on white: light-gray boxes "agent" (left) and "environment" (right); a top arrow agent → environment labeled `action`, a bottom arrow environment → agent labeled `reward, observation`. Black text/arrows, gray fill. Depicts exactly the RL repeat loop from the source text. (Vision pass on the PNG, 2026-08-29; no discrepancies with the source.)

**`policy_evaluation_recurrence.png`** — a left-to-right computational graph of the policy-evaluation recurrence: blue text $V_\pi(s)$ beside a blue-outlined circle `s`; an arrow labeled $\pi(s)$ to a red dashed circle `s, π(s)` with red text $Q_\pi(s, \pi(s))$ above it; from there two arrows fan out to the right, the upper labeled $T(s, \pi(s), s')$ ending at a blue-outlined circle `s'` beside blue text $V_\pi(s')$; the lower arrow ends at a second, unlabeled blue circle (the "other successor" branch). Blue = V's and state circles, red = Q and the (state, action) dashed circle, black = arrows and labels. The reward $R$ and discount $\gamma$ terms of the Q equation are not drawn — the figure shows the transition structure only, which matches the recurrence as written. (Vision pass on the PNG, 2026-08-29.)

**`value_iteration_recurrence.png`** — same computational-graph style for the value-iteration recurrence: blue text $V_{\text{opt}}(s)$ beside a blue-outlined circle `s`; two arrows leave it, the upper labeled `a` ending at a red dashed circle `s, a` with red text $Q_{\text{opt}}(s, a)$ above it, the lower ending at a second, unlabeled red dashed circle (the "other action" branch); from the labeled dashed circle an arrow labeled $T(s, a, s')$ goes to a blue-outlined circle `s'` beside blue text $V_{\text{opt}}(s')$, with crossing arrows from each dashed circle to each successor circle. One notation discrepancy with the lecture's equations: the figure writes $V_{\text{opt}}$ / $Q_{\text{opt}}$ where the source text uses $V^*$ / $Q^*$ — same quantities, different glyphs. The argmax policy equation is not drawn. (Vision pass on the PNG, 2026-08-29.)
