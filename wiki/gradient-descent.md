---
title: Gradient Descent
lectures:
  - 01 — mentioned as an example of learning
  - 02 — implemented for linear regression
---

# Gradient Descent

Gradient descent is an iterative optimization algorithm that updates parameters in the direction of the **negative gradient** of a loss function, moving downhill towards a (local) minimum. Liang mentions it in [Lecture 1](01-course-overview.md) as the simplest example of learning ([≈6:20](../raw/transcripts/01-course-overview.md)) and implements it for [linear regression](linear-regression.md) in [Lecture 2](02-learning-i.md).

## Intuition

The gradient $\nabla f(\mathbf{x})$ at a point is the direction of **steepest ascent** — moving along it increases $f$ the fastest. To **minimize** $f$, move in the opposite direction ([≈22:33](../raw/transcripts/02-learning-i.md)).

In 1D: the derivative is the slope of the tangent line. If the slope is positive, moving left (negative direction) decreases the function ([≈26:26](../raw/transcripts/02-learning-i.md)).

## Algorithm

For parameters $\theta$, loss function $\mathcal{L}(\theta)$, and learning rate $\eta$:

$$\theta_{t+1} = \theta_t - \eta \cdot \nabla_\theta \mathcal{L}(\theta_t)$$

Repeat until convergence or for a fixed number of iterations.

### Step-by-Step (for Linear Regression)

1. Compute training loss $\mathcal{L}(\theta_t)$ for current parameters
2. Compute gradient $\nabla_\theta \mathcal{L}(\theta_t)$ (via [backpropagation](backpropagation.md))
3. Update: $\theta_{t+1} = \theta_t - \eta \cdot \nabla_\theta \mathcal{L}(\theta_t)$
4. Repeat — the loss decreases each step ([≈1:09:43](../raw/transcripts/02-learning-i.md))

## Learning Rate

$\eta$ controls step size — how far we move per iteration. **The driving analogy** ([≈1:10:29](../raw/transcripts/02-learning-i.md)):

| Learning Rate | Effect |
|---|---|
| Too high | Divergence — loss explodes, parameters go haywire (you crash) |
| Too low | Slow convergence — tiny progress per step |
| Just right | Steady decrease in loss |

There is a whole field of optimization research on choosing and adapting learning rates.

## Convergence Properties

- **Convex functions** (like linear regression): gradient descent is guaranteed to find the global minimum ([≈1:11:15](../raw/transcripts/02-learning-i.md)).
- **Non-convex functions** (basically all of deep learning): no global guarantee, but works remarkably well in practice. Gradients shrink in norm near local optima.

## Variants

- **Batch gradient descent:** Uses the full training set per step (what Liang implements in Lecture 2)
- **Stochastic gradient descent (SGD):** Uses one random example per step — noisier but faster per iteration
- **Adam:** Adaptive learning rates per parameter — performs better in deep learning; mentioned as coming later in the course ([≈1:11:15](../raw/transcripts/02-learning-i.md))

## Resources

- [Lecture 2 transcript](../raw/transcripts/02-learning-i.md) — gradient examples (~22:33–32:35) and implementation (~1:06:40–1:10:29)
- [Lecture 2 slides](../raw/slides/02-learning-i.md#section-optimization-algorithm)
- [Linear regression](linear-regression.md) — complete example using gradient descent
- [Backpropagation](backpropagation.md) — how gradients are computed
