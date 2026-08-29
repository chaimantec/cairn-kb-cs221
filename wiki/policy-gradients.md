---
title: Policy Gradients — Learning the Policy Directly
---

# Policy Gradients

**Policy gradients** are the third family of RL methods after model-based and value-based: instead of estimating the MDP, or estimating Q-values and deriving a policy from them, you **parameterize the policy $\pi_\theta(a \mid s)$ directly and gradient-ascent its expected utility**. The lecture introduces the whole family through the policy gradient theorem, REINFORCE (its simplest member), and the variance-reduction tricks that make it usable ([lecture 9](09-policy-gradient.md)).

## Why Learn the Policy Directly?

Model-based methods estimate the MDP and value-iterate it; value-based methods estimate Q-values and take the argmax. But the Q-values were always a means to the policy — "wait a minute, we don't even need the Q values" ([≈34:53](../raw/transcripts/09-policy-gradient.md)). The obstacles to optimizing the policy directly are the familiar ones from classification: a deterministic policy is a hard classifier, and hard classifiers have 0-1 loss whose gradient is zero almost everywhere. The fix is the same one: optimize a **probabilistic classifier** $\pi_\theta(a \mid s)$ — a distribution over actions, which is continuous and therefore differentiable ([≈35:39](../raw/transcripts/09-policy-gradient.md)). A bonus: the stochastic policy doubles as the exploration mechanism, so no epsilon-greedy exploration policy is needed ([≈53:30](../raw/transcripts/09-policy-gradient.md)).

## The Objective and the Theorem

A rollout $\tau = (s_0, a_1, r_1, s_1, a_2, r_2, s_2, \dots)$ has probability ([≈41:51](../raw/transcripts/09-policy-gradient.md))

$$p(\tau) = p(s_0) \cdot \pi_\theta(a_1 \mid s_0) \cdot T(s_0, a_1, s_1) \cdot \pi_\theta(a_2 \mid s_1) \cdot T(s_1, a_2, s_2) \cdots$$

— alternating policy terms and environment transition terms. The objective is expected utility:

$$V(\theta) = \mathbb{E}_\theta[\text{utility}(\tau)] = \sum_\tau p_\theta(\tau) \cdot \text{utility}(\tau)$$

Differentiating, moving the gradient inside the sum, and applying the log-derivative trick $\nabla_\theta p_\theta(\tau) = p_\theta(\tau) \nabla_\theta \log p_\theta(\tau)$ gives the **policy gradient theorem** ([≈45:42](../raw/transcripts/09-policy-gradient.md), [≈48:06](../raw/transcripts/09-policy-gradient.md)):

$$\nabla_\theta V(\theta) = \mathbb{E}_\theta\big[\nabla_\theta \log p_\theta(\tau) \cdot \text{utility}(\tau)\big]$$

The lecturer calls it an *identity* rather than a theorem — the derivation is elementary ([≈48:06](../raw/transcripts/09-policy-gradient.md)). Because the expectation over all trajectories is intractable, it is replaced by a sample: run one rollout $\tau$, and step along

$$\nabla_\theta J(\theta, \tau) = \text{utility}(\tau) \cdot \sum_t \nabla_\theta \log \pi_\theta(a_t \mid s_{t-1})$$

where the start and transition probabilities have dropped out because they don't depend on $\theta$ ([≈48:52](../raw/transcripts/09-policy-gradient.md), [≈50:26](../raw/transcripts/09-policy-gradient.md)).

## REINFORCE

**REINFORCE** (Williams, 1992) is this sampled update: collect a rollout under the *current* policy (on-policy), then for each (state, action) in it, adjust $\theta$ to raise the log probability of that action, weighted by the rollout's utility ([≈51:12](../raw/transcripts/09-policy-gradient.md)). The intuition is the cleanest statement of the method: it is **imitation learning on the policy's own demonstrations, weighted by utility** ([≈51:57](../raw/transcripts/09-policy-gradient.md)). If utilities were binary (success/failure), it would literally be imitation learning on the successful trajectories; positive utilities reinforce, negative utilities push away, fractional utilities scale ([≈51:57](../raw/transcripts/09-policy-gradient.md)).

In code, `get_action` is sampling — one-hot state, linear logits, softmax, `torch.multinomial` — and `incorporate_feedback` accumulates the rollout and, at the end, takes one gradient step on $\sum_t \text{utility}(\tau) \cdot \text{CE}(\text{logits}(s_{t-1}), a_t)$ ([≈55:47](../raw/transcripts/09-policy-gradient.md), [≈56:34](../raw/transcripts/09-policy-gradient.md)). The lecture's live demo famously forgot the utility weighting and still learned the right qualitative policy — a reminder that the cross-entropy term alone is imitation learning ([≈58:10](../raw/transcripts/09-policy-gradient.md)).

## Bias, Variance, and the Reduction Toolkit

REINFORCE is an **unbiased** estimate of the policy gradient, but a high-variance one — single rollouts swing wildly ([≈1:01:11](../raw/transcripts/09-policy-gradient.md)). The lecture's mean-estimation detour makes the tradeoff concrete: among estimators of $\mu = \mathbb{E}[f(i)]$, sampling two points and averaging lowers variance at a cost; adding noise keeps bias but raises variance; subtracting a **mean-zero offset** (a *control variate*) lowers variance at zero bias cost, because by linearity of expectation $\mathbb{E}[f(i) - \text{offset}(i)] = \mathbb{E}[f(i)]$ ([≈1:02:47](../raw/transcripts/09-policy-gradient.md), [≈1:05:58](../raw/transcripts/09-policy-gradient.md)). In the live demo the offset cut the variance from 36 to ≈1 ([≈1:06:44](../raw/transcripts/09-policy-gradient.md)).

The RL version of the offset is the **key identity**: for any function $b(s)$ of the state alone,

$$\mathbb{E}_\theta\big[\nabla_\theta \log \pi_\theta(a \mid s) \cdot b(s)\big] = 0$$

so subtracting a state-dependent **baseline** from the utility leaves the expected gradient unchanged while offering a chance to shrink its variance ([≈1:08:17](../raw/transcripts/09-policy-gradient.md)). The proof is the policy gradient theorem in reverse ([≈1:09:49](../raw/transcripts/09-policy-gradient.md)). The lecturer's analogy: a baseline is to policy gradient what a heuristic is to A\* search — domain knowledge that speeds up an already-correct algorithm ([≈1:09:02](../raw/transcripts/09-policy-gradient.md)).

The three standard enhancements ([≈1:10:34](../raw/transcripts/09-policy-gradient.md)):

1. **Baselines** — replace $\text{utility}(\tau)$ with $\text{utility}(\tau) - b(s_{t-1})$: still unbiased.
2. **Returns-to-go** — weight step $t$ by only the rewards from $t$ onward, $r_t + \gamma r_{t+1} + \gamma^2 r_{t+2} + \dots$; valid because the past doesn't affect the future ([≈1:11:19](../raw/transcripts/09-policy-gradient.md)).
3. **Bootstrapped value estimates** — replace utility with an estimate of $Q(s_{t-1}, a_t)$, trading a little bias for less variance. Methods that estimate both the value function and the policy are **actor-critic** methods, and in deep learning the two can share parameters ([≈1:11:19](../raw/transcripts/09-policy-gradient.md)).

The whole game is finding a **low-bias, low-variance** estimate of the policy gradient ([≈1:12:05](../raw/transcripts/09-policy-gradient.md)).

## Where This Sits in the Course

Policy gradients are introduced and fully worked in [lecture 9](09-policy-gradient.md) (function approximation first, then the theorem, REINFORCE, and variance reduction). They rest on:

- [Markov decision processes](markov-decision-processes.md) — the environment model; expected utility comes from lecture 7
- [Reinforcement learning](reinforcement-learning.md) — the setting, and the model-based/value-based baseline this method is contrasted against
- [Q-learning](q-learning.md) — the value-based family; its bootstrapped target reappears here as the biased-but-low-variance option
- [Gradient descent](gradient-descent.md) and [backpropagation](backpropagation.md) — the optimizer doing the ascent on $V(\theta)$
