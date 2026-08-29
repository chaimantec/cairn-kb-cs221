---
title: Lecture 9 — Policy Gradient (Slides)
lecture: 9
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_file: https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/main/policy_gradient.py
rendered: https://stanford-cs221.github.io/autumn2025-lectures/?trace=policy_gradient
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. This file transcribes the
  content of `policy_gradient.py`. There are no traditional slide numbers;
  sections correspond to function calls in the source. All demo outputs (agent
  interactions, learned policies, value tables, estimator means and variances) were
  read on 2026-08-29 from the published execution trace committed in the lecture
  repo at `var/traces/policy_gradient.json` — the same trace the rendered lecture
  UI displays. The stochastic values depend on `set_random_seed(1)` at
  policy_gradient.py:43, `set_random_seed(2)` at policy_gradient.py:121, and
  `set_random_seed(1)` at policy_gradient.py:349, so the trace is exactly
  reproducible (the `variance_reduction` estimators run unseeded on torch's default
  RNG). The figure `rl-framework.png` is described by the vision pass in
  raw/slides/08-reinforcement-learning.md; the two remote photographs
  (`robot_kitchen.jpg` from the Berkeley Bridge dataset, `mobile_aloha.jpg` from
  Stanford News) are described by a vision pass over the copies cached in the
  lecture repo at `var/files/` (see the Figures section at the end of this file).
---

# Lecture 9 Slides — Policy Gradient

Lecture 9 is the second RL lecture: it generalizes from tabular Q-learning to **huge state spaces** (states as images or sentences) and from estimating Q-values to **learning the policy directly**. The path: function approximation (parameterize $Q_\theta(s, a)$ and gradient-descent it), then policy gradient — treat the policy as a probabilistic classifier $\pi_\theta(a \mid s)$ and climb the gradient of expected utility via the **policy gradient theorem**. REINFORCE (Williams, 1992) is the resulting algorithm; the lecture closes with the bias–variance tradeoff of gradient estimators and the three standard variance-reduction tricks (baselines, returns-to-go, bootstrapped value estimates).

---

## Section: Recap and This Time (`main`)

**Last time: reinforcement learning** — the RL setting, Q-learning and SARSA on the flaky tram.

**This time: generalize to large state spaces, learn policy directly.**

**Summary:**

- Model-based: estimate the MDP
- Value-based: estimate Q-values
- Policy-based: estimate policy

**Form of algorithm** (the general shape of everything in this lecture):

1. Generate rollouts from exploration policy
2. Form some loss function (parameters of MDP, Q-values, or policy)
3. Update parameters using gradient step

**In MDPs and RL, we're maximizing expected utility.**

**Next time:** what if there are adversaries in the environment? (Games I)

---

## Section: Review: Reinforcement Learning (`review_rl`)

- **RL setting** (figure: `rl-framework.png` — described in the Figures section of raw/slides/08-reinforcement-learning.md): the agent takes actions, the environment returns rewards and observations, the agent updates its behavior.
- **Environment**: Markov decision process (MDP). Demo MDP this lecture: `FlakyTramMDP(num_locs=6, failure_prob=0.1)`, goal state 6, walk i → i+1, tram i → 2i with 10% failure probability. `set_random_seed(1)`.
- **Agent**: RL algorithm. Demo agent: `QLearning(exploration_policy=walk_tram_policy, epsilon=0.4, discount=1, learning_rate=0.1)`.

**An example interaction (what the agent sees)** — three hand-fed steps, with what the epsilon-greedy agent actually returned on each `get_action`:

| `get_action` | agent's answer | hand-fed feedback |
| ------------ | -------------- | ----------------- |
| state 1 | `"tram"` | (1, walk, −1, 2, not end) |
| state 2 | `"tram"` | (2, walk, −1, 3, not end) |
| state 3 | `"walk"` | (3, tram, −2, 6, **end**) |

(Note the agent's answers differ from the hand-fed actions — the demo feeds a fixed script while the agent answers from its own epsilon-greedy policy; the transcript dismisses the mismatches as "out of date".) Each rollout generates a utility (discounted sum of rewards): here −1 −1 −2 = **−4**.

- **Generate rollouts, value is the mean utility.** `simulate(mdp, rl, num_trials=100)` → value **−4.92**.

**Various flavors of values (expected utility):**

- $V_\pi(s)$: value of following policy $\pi$ from state $s$
- $Q_\pi(s, a)$: value of taking action $a$ in state $s$, and then following policy $\pi$
- $V^*(s)$: value of following the optimal policy from state $s$
- $Q^*(s, a)$: value of taking action $a$ in state $s$, and then following the optimal policy

**To model or not to model?**

- Model-based: estimate the MDP, then compute the optimal policy
- Value-based (model-free): don't estimate the MDP, just estimate Q-values directly

**General form for model-free methods** (for model-free Monte Carlo, SARSA, Q-learning):

- Current model estimate: $Q(s, a)$
- Target (estimate of utility)
- Update: $Q(s, a) \leftarrow Q(s, a) + \text{learning\_rate} \cdot (\text{target} - Q(s, a))$

**On-policy ($Q_\pi$) vs. off-policy ($Q^*$):** follow the exploration policy $\pi$ to generate rollouts, to estimate Q-values of an *estimation* policy.

- On-policy: exploration policy = estimation policy
- Off-policy: exploration policy ≠ estimation policy

**What to use as targets?**

- Full rollouts: use actual utility (discounted sum of rewards)
- Bootstrapping: immediate reward + estimate of future reward

**Algorithms:**

- Model-based value iteration: estimate the MDP then compute the optimal policy
- Model-free Monte Carlo: estimate $Q_\pi(s, a)$ from full rollouts (on-policy): target = utility
- SARSA: estimate $Q_\pi(s, a)$ as you rollout (on-policy, bootstrapping): target = $r + \gamma \cdot Q(s', a')$, $a' = \pi(s')$
- Q-learning: estimate $Q^*(s, a)$ (off-policy, bootstrapping): target = $r + \gamma \cdot \max_{a'} Q^*(s', a')$

**So far, we are estimating a Q-value for each state/action (s, a) pair. This is the tabular setting** (a lookup table for each state/action pair).

**Examples of states in real-world settings:**

- State is an image (e.g., for learning a robot policy) — figure: `robot_kitchen.jpg` (see Figures section)
- State is a sentence (e.g., for theorem proving): *"Let $x, y$ and $z$ be positive real numbers that satisfy the following system of equations…"*

**How do we handle these huge state spaces** (all possible images or sentences)?

---

## Section: Function Approximation (`function_approximation`)

- Tabular setting: lookup table for $Q(s, a)$
- **Function approximation: $Q_\theta(s, a)$ is a function with parameters $\theta$**

**Design decisions** (explicitly flagged as familiar from the ML lectures):

1. What are the possible functions $Q_\theta(s, a)$? (hypothesis class)
2. How do we determine whether $Q_\theta(s, a)$ is good? (loss function)
3. How do we reduce the loss function? (optimization algorithm)

**1. What are the possible functions $Q_\theta(s, a)$?**

- First, map the state and action to a vector of features $\phi(s, a)$
- Then, we could use a linear function: $Q_\theta(s, a) = \phi(s, a) \cdot \theta$
- Or a multi-layer perceptron (MLP): $Q_\theta(s, a) = \text{MLP}_\theta(\phi(s, a))$

**Demo** (`FlakyTramMDP(num_locs=6, failure_prob=0.1)`, `set_random_seed(2)`, agent `ParameterizedQLearning(num_locs=6, actions=["walk", "tram"], epsilon=0.4, discount=1, learning_rate=0.1)` — a parameterized Q-function that simulates the tabular setting):

**Feature vector $\phi(s, a)$** — one-hot of length `num_locs × len(actions) = 12`, index `(state−1)·2 + (0 for walk, 1 for tram)`:

| call | index | one-hot (length 12) |
| ---- | ----- | ------------------- |
| φ(1, walk) | 0 | [1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0] |
| φ(1, tram) | 1 | [0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0] |
| φ(2, walk) | 2 | [0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0] |
| φ(2, tram) | 3 | [0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0] |
| φ(6, tram) | 11 | [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1] |

**Q-value function $Q_\theta(s, a) = \phi(s, a) \cdot \theta$:** $Q_\theta(1, \text{walk}) = 0.00274$ (the randomly initialized `nn.Linear(12, 1)` produces near-zero values).

**Policy $\pi(s) = \arg\max_a Q_\theta(s, a)$:** at state 1, $Q_\theta(1, \text{walk}) = 0.00274$ vs $Q_\theta(1, \text{tram}) = -0.13218$ → $\pi(1) = \text{"walk"}$.

**2, 3. Loss function and optimization algorithm** are defined in `incorporate_feedback`: for the end step target = reward (traced: −2); for non-end steps target = $r + \gamma \cdot Q_\theta(s', \pi_\theta(s'))$ (bootstrap with the agent's own greedy next action — Q-learning style). The loss is the squared loss $(\text{value} - \text{target})^2$ and the update is one SGD step through it.

**Run for real:** `simulate(mdp, rl, num_trials=100)` → mean utility **−5.08**.

**Extract the current optimal policy $\pi_\theta(s)$ of the agent:**

| state | 1 | 2 | 3 | 4 | 5 | 6 |
| ----- | --- | --- | --- | --- | --- | --- |
| $\pi_\theta$ | walk | walk | tram | walk | walk | walk |

**And the corresponding values $V_\theta(s) = Q_\theta(s, \pi_\theta(s))$:**

| state | 1 | 2 | 3 | 4 | 5 | 6 |
| ----- | --- | --- | --- | --- | --- | --- |
| $V_\theta$ | −3.906 | −2.980 | −2.002 | −1.949 | −0.981 | −2.674 |

**Compare with the true values by solving the MDP** (`value_iteration`):

| state | 1 | 2 | 3 | 4 | 5 | 6 |
| ----- | --- | --- | --- | --- | --- | --- |
| $V^*$ | −4.2222 | −3.2222 | −2.2222 | −2 | −1 | 0 |
| $\pi^*$ | walk | walk | tram | walk | walk | None |

**"It's in the ballpark, and more accurate for more visited states."** The learned policy matches the optimal one everywhere except the end state (where $\pi_\theta(6)$ = walk with $V_\theta(6) = −2.674$ against the true $V^*(6) = 0$ — the agent keeps estimating a value for a state it should never act in). Also note the true $V^*(1) = −4.2222$ is *lower* than the learned $V_\theta(1) = −3.906$: the walk/tram costs do not change, but the learned policy is being evaluated by the learned Q-function, which is only approximate.

**Summary:**

- Function approximation: parameterize $Q_\theta(s, a)$ by some $\theta$
- Map states and actions into a vector of features $\phi(s, a)$
- Define either a linear function or an MLP to map $\phi(s, a) \to Q_\theta(s, a)$
- Define squared loss between $Q_\theta(s, a)$ and target ($r + \gamma \cdot \max_{a'} Q_\theta(s', a')$)
- Q-learning: take a gradient of the loss with respect to $\theta$ and take a step

---

## Section: The Code — ParameterizedQLearning (`ParameterizedQLearning`)

- Holds `num_locs`, `actions`, `num_features = num_locs × len(actions)`, the exploration policy, `epsilon`, `discount`, `learning_rate`, an `nn.Linear(num_features, 1)` model and an `SGD` optimizer.
- `phi(state, action)`: maps (state, action) to an integer index `(state−1)·len(actions) + actions.index(action)`, then `one_hot(index, num_features)`.
- `Q(state, action)`: `phi(state, action)` passed through the linear model.
- `pi(state)`: computes `q_values = {a: Q(state, a)}` for all actions and takes the argmax.
- `get_action(state)`: epsilon-greedy — with probability `epsilon` return the exploration policy's action, else `pi(state)`.
- `incorporate_feedback(...)`: computes the target (reward if ended, else $r + \gamma \cdot Q(s', \pi(s'))$), the current value $Q(s, a)$, the squared loss, and takes one SGD step (zero_grad, backward, step).

---

## Section: Policy Gradient (`policy_gradient`)

- Model-based: estimate the MDP → policy $\pi(s)$
- Value-based: estimate Q-values $Q(s, a)$ → policy $\pi(s)$

**Why not directly estimate the policy $\pi(s)$?**

- Think of the policy as a classifier (input $s$ → output $a$)
- Recall that a classifier is a hard function to optimize directly
- …so we optimize over a **probabilistic classifier** $\pi(a \mid s)$.

---

## Section: Imitation Learning (`imitation_learning`)

**If we had demonstrations of the policy, it'd be easy.**

Suppose we have a rollout $\tau$ (walk → walk → tram, ending at state 6). We create the following examples:

| input (state) | output (action) |
| ------------- | --------------- |
| 1 | walk |
| 2 | walk |
| 3 | tram |

$$J(\theta) = \sum_t \log \pi_\theta(a_t \mid s_{t-1})$$

This is called **imitation learning**.

**Examples:**

- Robotics: teleoperation — figure: `mobile_aloha.jpg` (see Figures section)
- Math: human-written solution to a math problem

---

## Section: Policy Gradient Math (`policy_gradient_math`)

**But in reinforcement learning, we don't have demonstrations. We have a reward function… So what do we do?**

Suppose we have a rollout (trajectory, episode):

$$\tau = (s_0, a_1, r_1, s_1, a_2, r_2, s_2, a_3, r_3, s_3)$$

(the same walk/walk/tram rollout, with utilities −1, −1, −2). Each rollout produces a utility.

**What is the probability of a rollout?**

$$p(\tau) = p(s_0) \cdot \pi_\theta(a_1 \mid s_0) \cdot T(s_0, a_1, s_1) \cdot \pi_\theta(a_2 \mid s_1) \cdot T(s_1, a_2, s_2) \cdot \pi_\theta(a_3 \mid s_2) \cdot T(s_2, a_3, s_3)$$

Components:

- $p(s_0)$: probability of starting in state $s_0$
- $\pi_\theta(a_t \mid s_{t-1})$: policy
- $T(s_{t-1}, a_t, s_t)$: transition distribution

**Our goal is to maximize the expected utility of a rollout:**

$$V(\theta) = \mathbb{E}_\theta[\text{utility}(\tau)] = \sum_\tau p_\theta(\tau) \cdot \text{utility}(\tau)$$

**Let's just take the gradient of $V(\theta)$ with respect to $\theta$:**

$$\nabla_\theta V(\theta) = \nabla_\theta \mathbb{E}_\theta[\text{utility}(\tau)] = \nabla_\theta \sum_\tau p_\theta(\tau) \cdot \text{utility}(\tau) = \sum_\tau \nabla_\theta p_\theta(\tau) \cdot \text{utility}(\tau)$$

The derivative of a product is unpleasant, but the log-derivative trick rewrites it — using $\nabla_\theta p_\theta(\tau) = p_\theta(\tau) \cdot \nabla_\theta \log p_\theta(\tau)$:

$$\nabla_\theta V(\theta) = \sum_\tau p_\theta(\tau) \cdot \nabla_\theta \log p_\theta(\tau) \cdot \text{utility}(\tau) = \mathbb{E}_\theta[\nabla_\theta \log p_\theta(\tau) \cdot \text{utility}(\tau)]$$

**This is the policy gradient theorem (identity).**

- Whenever you see $\mathbb{E}_\theta[\dots]$, you can replace it with a sample $\tau \sim p_\theta(\tau)$
- …define the objective function $J(\theta, \tau) = \log p_\theta(\tau) \cdot \text{utility}(\tau)$
- …and update $\theta$ by taking a step in the direction of $\nabla_\theta J(\theta, \tau)$.

**Breaking down the gradient** (the transition and start-state probabilities do not depend on $\theta$, so they drop out of $\nabla_\theta \log p_\theta(\tau)$):

$$\nabla_\theta J(\theta, \tau) = \text{utility}(\tau) \cdot \sum_t \nabla_\theta \log \pi_\theta(a_t \mid s_{t-1})$$

**This is the REINFORCE algorithm** — [Williams, 1992](https://link.springer.com/article/10.1007/BF00992696).

**Intuition:**

- Just performing imitation learning on demonstrations from own policy weighted by utility.
- If utility($\tau$) ∈ {0, 1} (success/failure), then this is just imitation learning on own successful demonstrations.

**Summary:**

- Objective: optimize policy directly to maximize expected utility
- Due to policy gradient theorem, can compute unbiased gradient
- Algorithm: sample a rollout (on-policy), update using (state, action) in the rollout $\tau$, weighted by utility($\tau$)

---

## Section: REINFORCE in Action (`policy_gradient_implementation`)

Demo (`FlakyTramMDP(num_locs=6, failure_prob=0.1)`, `set_random_seed(1)`, `Reinforce(num_locs=6, actions=["walk", "tram"], discount=1, learning_rate=0.01)`):

**Note that we don't need an explicit exploration policy… because the stochastic policy should give us exploration.**

**Let's try a rollout** — hand-fed: (1, walk, −1, 2), (2, tram, −2, 2), (2, tram, −2, 4, **end**). The agent's own `get_action(2)` in this rollout sampled from $\pi_\theta(\cdot \mid 2)$ with logits [−0.031, 0.049], probabilities [0.480, 0.520], and drew index 0 → `"walk"` (again, the hand-fed actions differ from the agent's own samples — the demo feeds a fixed script). The utility accumulates to −1, then −3, then **−5**. At the end of the rollout, the loss is

$$\text{loss} = \sum_{t} \text{utility}(\tau) \cdot \text{CE}\big(\text{logits}_\theta(s_{t-1}),\, a_t\big)$$

(traced: −8.724 for this rollout — utility −5 times the cross-entropies), followed by one SGD step.

**Run for real:** `simulate(mdp, rl, num_trials=50)` → mean utility **−16.88**.

**Extract the current optimal policy $\pi_\theta(s)$ of the agent** (softmax over the learned 2×6 weight matrix plus bias, after 50 rollouts):

| state | $\pi_\theta$("walk") | $\pi_\theta$("tram") |
| ----- | -------------------- | -------------------- |
| 1 | 0.9451 | 0.0549 |
| 2 | 0.7854 | 0.2146 |
| 3 | 0.5098 | 0.4902 |
| 4 | 0.9915 | 0.0085 |
| 5 | 0.9967 | 0.0033 |
| 6 | 0.9426 | 0.0574 |

The learned stochastic policy puts the most tram probability at state 3 (≈0.49), exactly where the optimal deterministic policy says tram — and it got there with no explicit exploration policy, purely by sampling its own stochastic policy and weighting updates by utility.

Final model parameters (traced): weight = [[0.199, −0.625, −1.279, 1.166, 1.430, 0.245], [−0.073, 0.652, 1.256, −1.024, −1.701, 0.020]], bias = [1.282, −1.292].

**Summary:**

- `get_action`: sample from the policy $\pi_\theta(a \mid s)$
- `incorporate_feedback`: update parameters on (state, action) in the rollout $\tau$, weighted by utility($\tau$)

---

## Section: The Code — Reinforce (`Reinforce`)

- Holds an `nn.Linear(num_locs, len(actions))` model (logits for each action given a one-hot state), an `SGD` optimizer, and the current rollout bookkeeping: `start_state`, `rollout` (list of steps), `utility`.
- `phi(state)`: one-hot of the state (length `num_locs`).
- `pi(state)`: softmax over logits, returned as a dict `{action: probability}`.
- `get_action(state)`: one-hot the state → logits → softmax → sample one action with `torch.multinomial`. This is the exploration mechanism — sampling the stochastic policy directly, no epsilon needed.
- `incorporate_feedback(...)`: appends the step to the rollout and adds `reward · discount^len(rollout)` to the utility. When `is_end`, it computes the loss by looping over the rollout: for each step, the cross-entropy of the model's logits against the one-hot target for the *action that was actually taken*, summed with weight `utility`; then one SGD step, and the rollout bookkeeping resets.

---

## Section: Variance Reduction (`variance_reduction`)

**Consider the simple mean estimation problem:**

$$\mu = \mathbb{E}[f(i)] = \sum_i p(i) \cdot f(i)$$

with `probs = [0.1, 0.1, 0.1, 0.1]` and `points = [−4, −6, 6, 8]`, so the true mean is $\mu = 0.1 \cdot (-4 - 6 + 6 + 8) = 0.1 \cdot 4 =$ **0.4** (unknown to the estimators).

**An estimator is any random variable that tries to get close to μ. Each estimator has a bias and a variance (and a cost).** All results below are from 100 samples each:

| estimator | what it does | mean | variance |
| --------- | ------------ | ---- | -------- |
| 1 | sample a single $i \sim p$, return $f(i)$ | 0.46 | 38.29 |
| 2 | sample 2 points and average (more expensive) | 0.93 | 12.39 |
| 3 | sample 1 point, add noise (worse, but still unbiased) | 1.26 | 35.28 |
| 4 | sample 1 point, subtract a magic `offset(i)` with mean 0 | 0.94 | 1.01 |

Estimator 4 uses `offsets = [−6, −6, 6, 6]` (mean 0: $0.1 \cdot (-6 - 6 + 6 + 6) = 0$), so $f(i) - \text{offset}(i)$ becomes [2, 0, 0, 2] — same expectation, drastically smaller spread. **Subtracting a mean-zero quantity changes the variance without changing the bias.**

Note: the lecturer's live demo used uniform probabilities 0.25 (not 0.1), with true mean 1, estimator-1 mean ≈1 and variance ≈36 — the published trace above (0.1, μ = 0.4) is a later fix, and the numbers quoted in the transcript are the live ones. The qualitative story is identical: offset subtraction collapses the variance (36 → ≈1 live; 38.29 → 1.01 in the trace) without biasing the mean.

**Suppose we have a magic function offset(i) that has mean 0. Then:**

$$\mathbb{E}[f(i) - \text{offset}(i)] = \mathbb{E}[f(i)] - \mathbb{E}[\text{offset}(i)] = \mathbb{E}[f(i)]$$

---

## Section: Policy Gradient Enhancements (`policy_gradient_enhancements`)

Recall the objective: $V(\theta) = \mathbb{E}_\theta[\text{utility}(\tau)] = \sum_\tau p_\theta(\tau) \cdot \text{utility}(\tau)$, and the policy gradient theorem $\nabla_\theta V(\theta) = \mathbb{E}_\theta[\nabla_\theta \log p_\theta(\tau) \cdot \text{utility}(\tau)]$.

**REINFORCE is one particular (unbiased) estimate of $\nabla_\theta V(\theta)$:** sample $\tau \sim p_\theta(\tau)$ and compute $\nabla_\theta J(\theta, \tau) = \nabla_\theta \log p_\theta(\tau) \cdot \text{utility}(\tau)$. However, can we get a better estimate?

**So how do we find an offset(s, a) with $\mathbb{E}[\text{offset}(s, a)] = 0$?**

**Key identity:** let $b(s)$ be *any* function that only depends on state $s$ (not action $a$). Then

$$\mathbb{E}_\theta[\nabla_\theta \log \pi_\theta(a \mid s) \cdot b(s)] = 0 \quad \text{for all } s,\ \text{where } a \sim \pi_\theta(a \mid s)$$

**Analogy: heuristics in A\* search** (use domain knowledge to improve the algorithm).

**Proof:**

- $\mathbb{E}_\theta[b(s)] = \text{constant}$
- $\nabla_\theta \mathbb{E}_\theta[b(s)] = 0$
- $\nabla_\theta \sum_a \pi_\theta(a \mid s) \, b(s) = 0$
- $\sum_a \nabla_\theta \pi_\theta(a \mid s) \, b(s) = 0$
- $\sum_a \pi_\theta(a \mid s) \, \nabla_\theta \log \pi_\theta(a \mid s) \, b(s) = 0$
- $\mathbb{E}_\theta[\nabla_\theta \log \pi_\theta(a \mid s) \cdot b(s)] = 0$

This is basically the reverse direction of the policy gradient theorem.

**Enhancements to reduce variance:**

1. **Baselines:** subtract off a baseline $b(s)$ from the utility
   $$J(\theta, \tau) = \sum_t \log \pi_\theta(a_t \mid s_{t-1}) \cdot \big(\text{utility}(\tau) - b(s_{t-1})\big)$$
2. **Use returns-to-go instead of utility**
   $$J(\theta, \tau) = \sum_t \log \pi_\theta(a_t \mid s_{t-1}) \cdot \big(r_t + \gamma r_{t+1} + \gamma^2 r_{t+2} + \dots - b(s_{t-1})\big)$$
3. **Use a biased estimate of the value function $Q(s, a)$ instead of utility($\tau$)** (bootstrapping)
   $$J(\theta, \tau) = \sum_t \log \pi_\theta(a_t \mid s_{t-1}) \cdot \big(Q(s_{t-1}, a_t) - b(s_{t-1})\big)$$

**Summary:**

- The game: find a low-bias, low-variance estimate of $\mathbb{E}_\theta[\nabla_\theta \log \pi_\theta(\tau) \cdot \text{utility}(\tau)]$
- Baselines: a general-purpose strategy to reduce variance, still unbiased
- Bootstrapping: reduce variance but introduce a bit of bias

---

## Figures (vision pass)

**`rl-framework.png`** — reuse of the figure from lecture 8: two-node loop diagram, gray boxes "agent" and "environment", top arrow agent → environment labeled `action`, bottom arrow environment → agent labeled `reward, observation`. Full description in raw/slides/08-reinforcement-learning.md, Figures section.

**`robot_kitchen.jpg`** — the photograph shown for "State is an image (e.g., for learning a robot policy)", from the Berkeley Bridge Data v1 dataset. Canonical URL: `https://rail.eecs.berkeley.edu/datasets/bridge_release/raw/bridge_data_v1/berkeley/toykitchen2/put_potato_in_pot_cardboard_fence/2022-01-28_11-25-48/raw/traj_group0/traj13/images0/im_0.jpg`; local copy cached in the lecture repo at `var/files/`. (Vision pass on the cached copy, 2026-08-29.)

The photo is a close-up, downward-looking view of a section of a miniature toy kitchen, the frame filled mostly by a raised wooden tray-like board with a low rim. On the board sit four objects: an amber-orange glass bowl, a bright yellow curved banana, a yellow roundish object (most likely a small yellow pepper or a lemon — ambiguous), and an amber-orange glass lid with a central knob. Black clip-like clamps are attached along the edges of the wooden board, and black angled hardware near the top edge and at the top-left corner of the frame appears to be part of a robot arm or camera mount, though nearly all of the equipment is cropped out of view. Behind the board runs a dark, brownish-purple tiled backsplash, and to the right is a white counter surface with a grey, slotted metallic rack set into it; above sits a white shelf holding a tangled black cable or cords. No human appears in the image. In the lecture this view illustrates the "state is an image" bullet: the raw camera observation of the scene — a bowl, banana, and lid lying on a board — serves as the state input for learning a robot manipulation policy. It is not clear whether the banana and the yellow object are real food or props, and any labels or text that might exist in the frame are not readable at this resolution.

**`mobile_aloha.jpg`** — the photograph shown for "Examples: Robotics: teleoperation", from a Stanford News article on Mobile ALOHA. Canonical URL: `https://news.stanford.edu/__data/assets/image/0028/134857/mobilealoha_2.jpg`; local copy cached in the lecture repo at `var/files/`. (Vision pass on the cached copy, 2026-08-29.)

The photo shows a home-style kitchen repurposed as a robotics lab, with light wooden cabinets above and below white countertops, a stainless-steel range hood over the back counter, a sink with a chrome faucet at the right, and a grey floor partly covered by a dark mat. In the center stands a tall mobile robot tower: a vertical black mast with angled aluminum-frame supports, an open laptop near its base, and a horizontal rail near the top carrying two cylindrical black robot-arm bases. From this structure, two black robotic arms run toward the camera; the left-hand arm extends out over the counter, ending in a dark two-finger gripper with a small camera-like module near the top of the wrist. On the right side of the image, a man wearing glasses and a dark athletic zip-up jacket stands gripping the ends of the two arms, one in each hand, holding them out in front of his chest in a teleoperation pose; near his left hand there is a small yellow-and-black object, possibly a wrench-shaped handle or a tool, but it is too small to identify. Wiring runs along the arms and down the mast, and near the far right, beside the sink, a small white robotic arm with a gripper stands on a narrow-legged stand — its make or model cannot be identified from this view. On the wall behind the operator is a white poster or printed page whose text is unreadable, and the laptop screen appears dark or blank. In the lecture this image accompanies the "Robotics: teleoperation" example, illustrating a human demonstrator teleoperating the two-arm robot in a kitchen setting so that expert demonstrations can be collected for learning.
