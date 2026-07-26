---
title: Linear Regression
lectures:
  - 02 — full treatment: hypothesis class, squared loss, gradient descent
---

# Linear Regression

Linear regression is the simplest complete machine learning pipeline. It predicts a real-valued output as a linear function of the input, fitted to training data by minimizing squared error. Liang uses it in [Lecture 2](02-learning-i.md) to introduce the three-part ML template: **hypothesis class → loss function → optimization algorithm** ([≈56:40](../raw/transcripts/02-learning-i.md)).

## The Prediction Task

Given an input $x$ (e.g., hours studied), predict an output $y$ (e.g., exam score). A **predictor** is any function $f: \mathcal{X} \to \mathcal{Y}$. The question: how do we get a good predictor from data? ([≈57:27](../raw/transcripts/02-learning-i.md))

## 1. Hypothesis Class

We don't hand-pick one predictor. We define a **parameterized family** and let the learning algorithm choose the best one. For linear regression:

$$f_{w,b}(x) = w \cdot x + b$$

The parameters are $\theta = (w, b)$ — a weight (slope) and a bias (intercept). The **hypothesis class** $\mathcal{F}$ is the set of all predictors obtainable by varying $\theta$. All are linear — no squiggly lines ([≈59:46](../raw/transcripts/02-learning-i.md)).

In deep learning: hypothesis class = model architecture; a specific predictor = a model ([≈1:01:17](../raw/transcripts/02-learning-i.md)).

## 2. Loss Function

The loss measures how badly a given predictor fits the training data. For linear regression, use **squared loss**:

$$\text{loss}(\theta; x, y) = \big(f_\theta(x) - y\big)^2$$

where $f_\theta(x) - y$ is the **residual** — the vertical distance from the predicted line to the actual data point ([≈1:02:49](../raw/transcripts/02-learning-i.md)).

The **training loss** averages over all $n$ examples:

$$\text{TrainLoss}(\theta) = \frac{1}{n} \sum_{i=1}^{n} \big(f_\theta(x^{(i)}) - y^{(i)}\big)^2$$

Loss is always bad — we want to **minimize** it ([≈1:05:07](../raw/transcripts/02-learning-i.md)).

## 3. Optimization: Gradient Descent

The gradient of the loss tells us which direction increases it most → move in the **opposite direction** to decrease it.

### Per-Example Gradient

For one example $(x, y)$ with parameters $w, b$:

$$\frac{\partial \text{loss}}{\partial w} = 2 \cdot (wx + b - y) \cdot x$$

$$\frac{\partial \text{loss}}{\partial b} = 2 \cdot (wx + b - y)$$

### Batch Gradient

Average the per-example gradients across the full training set ([≈1:07:26](../raw/transcripts/02-learning-i.md)):

$$\nabla_\theta \text{TrainLoss} = \frac{1}{n} \sum_{i=1}^{n} \nabla_\theta \text{loss}(\theta; x^{(i)}, y^{(i)})$$

### Update Rule

$$w := w - \eta \cdot \frac{\partial \text{TrainLoss}}{\partial w}$$

$$b := b - \eta \cdot \frac{\partial \text{TrainLoss}}{\partial b}$$

where $\eta$ (eta) is the **learning rate** ([≈1:08:12](../raw/transcripts/02-learning-i.md)).

## The ML Template

Linear regression demonstrates the general template for all supervised ML ([≈1:12:01](../raw/transcripts/02-learning-i.md)):

1. **Hypothesis class** — define the space of possible predictors via parameters
2. **Loss function** — measure fit to training data
3. **Optimization algorithm** — find parameters that minimize the loss (e.g., [gradient descent](gradient-descent.md))

## Resources

- [Lecture 2 transcript](../raw/transcripts/02-learning-i.md) — full coverage (~56:40–1:12:01)
- [Lecture 2 slides](../raw/slides/02-learning-i.md#part-2-linear-regression-linear_regressionpy)
- [Gradient descent](gradient-descent.md) — the optimization algorithm used
- [Backpropagation](backpropagation.md) — how gradients are computed automatically
