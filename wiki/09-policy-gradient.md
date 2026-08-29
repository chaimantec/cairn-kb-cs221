---
title: Lecture 9 — Policy Gradient
---

# Lecture 9 — Policy Gradient

Lecture 9 extends [reinforcement learning](reinforcement-learning.md) in two directions: first from the tabular Q-tables of lecture 8 to **function approximation** — parameterizing $Q_\theta(s, a)$ so RL works when the state is an image or a sentence ([≈17:43](../raw/transcripts/09-policy-gradient.md)) — and then past Q-values altogether to **policy-based methods**: treating the policy as a probabilistic classifier $\pi_\theta(a \mid s)$ and maximizing expected utility directly by gradient descent ([≈34:53](../raw/transcripts/09-policy-gradient.md)). The payoff is the **policy gradient theorem** and REINFORCE (Williams, 1992), plus the bias–variance tradeoff that motivates baselines, returns-to-go, and actor-critic methods.

## Function Approximation: Q-Values for Huge State Spaces

So far every RL algorithm stored one number per (state, action) pair — the **tabular setting**, literally a lookup table ([≈16:58](../raw/transcripts/09-policy-gradient.md)). Real-world states break it: for a robot policy the state is an image, for a language model the state is a sentence and the action is the next token, and you cannot index a table by "all possible images" ([≈16:58](../raw/transcripts/09-policy-gradient.md)). (A student suggests reducing the state space, which the lecturer endorses as one option before moving to the stronger one ([≈17:43](../raw/transcripts/09-policy-gradient.md)).)

**Function approximation** imports the ML machinery from lectures 2–4: instead of storing Q-values, define $Q_\theta(s, a)$ with parameters $\theta$ and *learn* $\theta$ ([≈19:18](../raw/transcripts/09-policy-gradient.md)). The three familiar design decisions return ([≈20:05](../raw/transcripts/09-policy-gradient.md)):

1. **Hypothesis class** — first map (state, action) to a feature vector $\phi(s, a)$, then either a linear function $Q_\theta(s, a) = \phi(s, a) \cdot \theta$ or an MLP ([≈20:51](../raw/transcripts/09-policy-gradient.md)).
2. **Loss function** — squared loss between the predicted Q-value and a *target*.
3. **Optimization algorithm** — SGD on the loss ([≈27:05](../raw/transcripts/09-policy-gradient.md)).

The demo (`ParameterizedQLearning`) simulates the tabular setting with one-hot features — one feature per (state, action), so index `(state−1)·2 + (0 for walk, 1 for tram)` selects it ([≈23:12](../raw/transcripts/09-policy-gradient.md), [slides](../raw/slides/09-policy-gradient.md)). The target in `incorporate_feedback` is exactly Q-learning's: the immediate reward plus the bootstrapped value of the greedy next action,

$$\text{target} = r + \gamma \cdot Q_\theta\big(s', \pi_\theta(s')\big)$$

(or just $r$ at the end of an episode), and one SGD step follows ([≈26:18](../raw/transcripts/09-policy-gradient.md)). This split is the lecture's key architectural point: **the RL part of the algorithm is specifying the target**; the loss, the model, and the optimizer are plain machine learning ([≈27:51](../raw/transcripts/09-policy-gradient.md)) — the same target plugs in for SARSA or model-free Monte Carlo.

Run for 100 rollouts on the 6-location tram, the agent scores **−5.08** and learns the policy walk, walk, tram, walk, walk — matching the optimal policy everywhere except the end state, where it keeps a meaningless estimate ($V_\theta(6) = -2.67$ against true $V^*(6) = 0$) ([slides](../raw/slides/09-policy-gradient.md)). The live demo got a worse draw — the lecturer notes he "got unlucky" with the stochastic learning, and that it had come out right in a previous run ([≈29:25](../raw/transcripts/09-policy-gradient.md)). Two student questions close the section: **why squared loss?** — because the target is a noisy version of the value you want, and squared loss is regression toward it ([≈31:45](../raw/transcripts/09-policy-gradient.md)); **does this converge?** — tabular Q-learning converges under conditions (exploration policy touches everything), but with function approximation there are no guarantees in general, only perhaps a local optimum ([≈33:19](../raw/transcripts/09-policy-gradient.md), [≈34:07](../raw/transcripts/09-policy-gradient.md)).

## The Policy as a Classifier

The progression ([≈34:53](../raw/transcripts/09-policy-gradient.md)):

- **Model-based**: estimate the MDP, then compute the optimal policy.
- **Value-based**: estimate Q-values, then derive the policy by argmax.
- **Policy-based**: *directly estimate the policy* — "wait a minute, we don't even need the Q values."

A policy is just a classifier mapping inputs (states) to outputs (actions) ([≈34:53](../raw/transcripts/09-policy-gradient.md)). But as the linear classification lecture showed, a hard classifier is hard to optimize — the 0-1 loss has zero gradient almost everywhere. The fix is the same one: optimize a **probabilistic classifier** $\pi_\theta(a \mid s)$, a distribution over actions, and distributions are continuous, hence optimizable ([≈35:39](../raw/transcripts/09-policy-gradient.md)).

## Imitation Learning: What We'd Do with Demonstrations

If an expert were available, the problem collapses to supervised learning ([≈36:25](../raw/transcripts/09-policy-gradient.md)): the demonstration rollout (walk, walk, tram) becomes training examples — (1, walk), (2, walk), (3, tram) — and the objective is

$$J(\theta) = \sum_t \log \pi_\theta\big(a_t \mid s_{t-1}\big)$$

maximizing the log probability of the demonstrated actions ([≈37:12](../raw/transcripts/09-policy-gradient.md)). This is **imitation learning**, and it is genuinely used: teleoperated robot demonstrations in robotics, human-written math solutions in math ([≈37:58](../raw/transcripts/09-policy-gradient.md)).

## The Policy Gradient Theorem

RL gives no demonstrations — only a reward function ([≈38:47](../raw/transcripts/09-policy-gradient.md)). The *zeroth-order* idea that foreshadows the real algorithm: roll out your policy, keep the high-reward trajectories, and do imitation learning on those ([≈39:33](../raw/transcripts/09-policy-gradient.md)). The rest of the lecture is the principled version.

For a rollout $\tau = (s_0, a_1, r_1, s_1, a_2, r_2, s_2, a_3, r_3, s_3)$ ([≈40:20](../raw/transcripts/09-policy-gradient.md)), the **probability of the trajectory** is the chain rule over alternating agent and environment turns ([≈41:51](../raw/transcripts/09-policy-gradient.md)):

$$p(\tau) = p(s_0) \cdot \pi_\theta(a_1 \mid s_0) \cdot T(s_0, a_1, s_1) \cdot \pi_\theta(a_2 \mid s_1) \cdot T(s_1, a_2, s_2) \cdot \pi_\theta(a_3 \mid s_2) \cdot T(s_2, a_3, s_3)$$

with $p(s_0)$ the start distribution, $\pi_\theta$ the policy, and $T$ the transition distribution ([≈42:38](../raw/transcripts/09-policy-gradient.md)). The objective is the most direct statement of RL there is ([≈43:24](../raw/transcripts/09-policy-gradient.md)):

$$V(\theta) = \mathbb{E}_\theta[\text{utility}(\tau)] = \sum_\tau p_\theta(\tau) \cdot \text{utility}(\tau)$$

To optimize it, take the gradient ([≈44:10](../raw/transcripts/09-policy-gradient.md)). The sum over trajectories is linear, so the gradient passes inside; the awkward part is $\nabla_\theta p_\theta(\tau)$, which the **log-derivative trick** rewrites using

$$\nabla_\theta p_\theta(\tau) = p_\theta(\tau) \cdot \nabla_\theta \log p_\theta(\tau)$$

([≈45:42](../raw/transcripts/09-policy-gradient.md), [≈46:28](../raw/transcripts/09-policy-gradient.md) — the identity comes from differentiating $\log p$ and rearranging). The result is the **policy gradient theorem**:

$$\nabla_\theta V(\theta) = \mathbb{E}_\theta\big[\nabla_\theta \log p_\theta(\tau) \cdot \text{utility}(\tau)\big]$$

The lecturer admits the name overstates it: "it's sort of elementary, so I just call it the identity… it doesn't deserve to be called a theorem" ([≈48:06](../raw/transcripts/09-policy-gradient.md)).

Since the expectation over *all* trajectories is intractable, replace it with a sample $\tau \sim p_\theta(\tau)$: define the per-trajectory objective $J(\theta, \tau) = \log p_\theta(\tau) \cdot \text{utility}(\tau)$ and step $\theta$ along its gradient ([≈48:52](../raw/transcripts/09-policy-gradient.md)). Expanding $\log p_\theta(\tau)$, the start and transition probabilities don't depend on $\theta$ and drop out, leaving

$$\nabla_\theta J(\theta, \tau) = \text{utility}(\tau) \cdot \sum_t \nabla_\theta \log \pi_\theta\big(a_t \mid s_{t-1}\big)$$

— the **REINFORCE** algorithm, [Williams, 1992](https://link.springer.com/article/10.1007/BF00992696) ([≈50:26](../raw/transcripts/09-policy-gradient.md), [≈51:12](../raw/transcripts/09-policy-gradient.md)). The intuition, exactly the zeroth-order idea: imitation learning on the policy's *own* demonstrations, weighted by utility. If utilities were only 0/1 (success/failure), REINFORCE would literally be imitation learning on the successful trajectories; with other values it scales the update by how good the trajectory was, and negative utilities push *away* ([≈51:57](../raw/transcripts/09-policy-gradient.md)).

## REINFORCE in Code

The `Reinforce` agent drops the exploration policy entirely: **the stochastic policy is the exploration mechanism** ([≈53:30](../raw/transcripts/09-policy-gradient.md), [≈55:01](../raw/transcripts/09-policy-gradient.md)). `get_action` is classification in miniature — one-hot the state, pass through a linear model to get logits, softmax, then *sample* with `torch.multinomial` ([≈55:47](../raw/transcripts/09-policy-gradient.md)). `incorporate_feedback` records the rollout; only at the end does it update, adding each step's cross-entropy against the one-hot of the action actually taken, weighted by the rollout utility, then one SGD step ([≈56:34](../raw/transcripts/09-policy-gradient.md), [≈58:10](../raw/transcripts/09-policy-gradient.md)).

The live demo has a memorable bug: the lecturer realizes mid-run "I should be multiplying the loss by the utility" ([≈58:10](../raw/transcripts/09-policy-gradient.md)) — he had forgotten the weighting, yet it still learned the right qualitative policy (walk, walk, tram) ([≈58:55](../raw/transcripts/09-policy-gradient.md), "weighting by utility which I didn't do but you should do" at [≈59:40](../raw/transcripts/09-policy-gradient.md)). The published trace runs the fixed code for 50 rollouts, scoring **−16.88** and learning $\pi_\theta(\text{tram} \mid 3) = 0.49$ — the most tram probability exactly at the state where the optimal policy says tram ([slides](../raw/slides/09-policy-gradient.md)). Two student questions: the algorithm is **online** (though you may batch rollouts, the spirit is update-as-you-go) ([≈59:40](../raw/transcripts/09-policy-gradient.md)), and **initialization matters** — because the initial $\pi_\theta$ governs the data you collect, so keep $\theta$ small for a near-uniform starting distribution ([≈1:00:26](../raw/transcripts/09-policy-gradient.md)).

## Variance Reduction: Baselines, Returns-to-Go, Actor-Critic

REINFORCE is *unbiased* but high-variance. Can we get a better estimate? ([≈1:01:11](../raw/transcripts/09-policy-gradient.md)). The detour is a mean-estimation problem: estimate $\mu = \mathbb{E}[f(i)]$ over four points −4, −6, 6, 8 ([≈1:01:58](../raw/transcripts/09-policy-gradient.md)). An **estimator** is any random variable aiming at $\mu$, graded by bias, variance, and cost ([≈1:02:47](../raw/transcripts/09-policy-gradient.md)):

| estimator | idea | variance |
| --------- | ---- | -------- |
| 1 | sample one point | 36 |
| 2 | sample two points, average (costlier) | much smaller |
| 3 | sample one point, add noise | worse than 1 |
| 4 | sample one point, subtract a **mean-zero offset** | tiny |

Estimator 4's trick is a **control variate** ([≈1:06:44](../raw/transcripts/09-policy-gradient.md)): if offset has mean zero, then by linearity of expectation $\mathbb{E}[f(i) - \text{offset}(i)] = \mathbb{E}[f(i)]$ ([≈1:05:58](../raw/transcripts/09-policy-gradient.md)); subtracting it pulls the low points up and the high points down, collapsing variance without biasing the mean ([≈1:07:29](../raw/transcripts/09-policy-gradient.md)). (Live numbers: variance 36 → ≈1; the published trace with different probabilities: 38.29 → 1.01.)

The RL analogue is the **key identity**: for *any* function $b(s)$ of the state alone,

$$\mathbb{E}_\theta\big[\nabla_\theta \log \pi_\theta(a \mid s) \cdot b(s)\big] = 0$$

([≈1:08:17](../raw/transcripts/09-policy-gradient.md)) — so a state-dependent **baseline** can be subtracted from the utility without changing the expected gradient. The proof is the policy gradient theorem run backwards: $\mathbb{E}_\theta[b(s)]$ doesn't depend on $\theta$, so its gradient is 0; expanding the sum over actions gives the identity ([≈1:09:49](../raw/transcripts/09-policy-gradient.md), sketched on the slides: [slides](../raw/slides/09-policy-gradient.md)). The lecturer's analogy: **baselines are to policy gradient what heuristics are to A\* search** — domain knowledge that makes the same correct algorithm much faster ([≈1:09:02](../raw/transcripts/09-policy-gradient.md)).

Three variance-reduction enhancements follow ([≈1:10:34](../raw/transcripts/09-policy-gradient.md), [slides](../raw/slides/09-policy-gradient.md)):

1. **Baselines** — subtract $b(s_{t-1})$ from the utility: still unbiased.
2. **Returns-to-go** — weight step $t$ by $r_t + \gamma r_{t+1} + \gamma^2 r_{t+2} + \dots$ instead of the whole utility; valid because the past doesn't affect the future ([≈1:11:19](../raw/transcripts/09-policy-gradient.md)).
3. **Bootstrapped value estimates** — replace utility with an estimate of $Q(s_{t-1}, a_t)$: reduces variance at the cost of some bias. Methods that estimate both a value and a policy this way are **actor-critic** methods, and in deep learning the two can share parameters ([≈1:11:19](../raw/transcripts/09-policy-gradient.md)).

The game, in one line: find a **low-bias, low-variance** estimate of the policy gradient ([≈1:12:05](../raw/transcripts/09-policy-gradient.md)).

## The Three-Way Map of RL

The lecture closes by uniting the whole MDP/RL arc ([≈1:12:52](../raw/transcripts/09-policy-gradient.md)): model-based estimates the MDP, value-based estimates Q-values, policy-based estimates the policy — and all three share one algorithm shape: roll out with an exploration policy, form a loss, update parameters with a gradient step. Next time: adversaries, where expectation is replaced by worst case ([≈1:13:39](../raw/transcripts/09-policy-gradient.md)).

## Related Pages

- [Policy gradients](policy-gradients.md) — the method as a topic: the theorem, REINFORCE, and the variance-reduction toolkit
- [Reinforcement learning](reinforcement-learning.md) — the setting and the four-algorithm map this lecture extends
- [Q-learning](q-learning.md) — the value-based baseline; the target here is its bootstrapped target in parameterized form
- [Markov decision processes](markov-decision-processes.md) — the environment formalism throughout
- [Backpropagation](backpropagation.md) and [gradient descent](gradient-descent.md) — the optimization machinery the lecture reuses
- [Transcript](../raw/transcripts/09-policy-gradient.md) · [Slides](../raw/slides/09-policy-gradient.md)
