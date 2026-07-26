---
title: Learning I — Backpropagation and Linear Regression
lecture: 2
video: https://www.youtube.com/watch?v=ypZJaTqrNdk
slides: ../raw/slides/02-learning-i.md
transcript: ../raw/transcripts/02-learning-i.md
---

# Lecture 2 — Learning I

This lecture lays the foundation for how machines learn. Liang covers three building blocks that every ML pipeline needs: **(1) einops** for readable tensor operations, **(2) computation graphs and backpropagation** for computing gradients automatically, and **(3) linear regression** as the first concrete example of the hypothesis-class / loss-function / optimization-algorithm template.

## Einops Review

See the dedicated [einops](einops.md) topic page for a full reference.

Recall: einops gives every tensor axis a name, making operations self-documenting ([≈0:05](../raw/transcripts/02-learning-i.md)).

**Tensor order:**
- Order 0 = scalar, Order 1 = vector, Order 2 = matrix ([≈0:52](../raw/transcripts/02-learning-i.md)).

**Axis naming:** Like choosing variable names in code — name axes by what they represent (e.g., `batch`, `seq`, `hidden`) ([≈1:38](../raw/transcripts/02-learning-i.md)).

**`einsum` reference table** (from [slides](../raw/slides/02-learning-i.md#section-einops-review)):

| Operation | einsum Pattern |
|---|---|
| Identity | `"i -> i"` |
| Sum all elements | `"i ->"` |
| Element-wise product | `"i, i -> i"` |
| Dot product | `"i, i ->"` |
| Outer product | `"i, j -> i j"` |
| Matrix transpose | `"i j -> j i"` |
| Matrix-vector product | `"i j, j -> i"` |
| m · mᵀ | `"i k, j k -> i j"` |
| mᵀ · m | `"k i, k j -> i j"` |

**General rule:** For every assignment of input axes, multiply corresponding tensor elements, accumulate into the output entry. Output axes that don't appear in the input are summed over. "It's all just additions and multiplications with bookkeeping!" ([≈15:39](../raw/transcripts/02-learning-i.md)).

## Gradients and Partial Derivatives

The **gradient** of a function tells us the direction that **decreases** the function value the most — so gradient-based optimization moves in the *negative* gradient direction ([≈22:33](../raw/transcripts/02-learning-i.md)).

### Key Definitions

- **Partial derivative** `∂f/∂xᵢ`: if we change input element `xᵢ` by an infinitesimal amount, how much does `f` change? ([≈31:49](../raw/transcripts/02-learning-i.md))
- **Gradient** `∇f`: the full tensor of partial derivatives, with the **same shape** as the input ([≈32:35](../raw/transcripts/02-learning-i.md)).
- Graphically, in 1D, the derivative is the slope of the tangent line ([≈26:26](../raw/transcripts/02-learning-i.md)).

### Worked Example

For $f(x_1, x_2) = (x_1 + x_2)^2$:

$$\frac{\partial f}{\partial x_1} = 2(x_1 + x_2) \quad\quad \frac{\partial f}{\partial x_2} = 2(x_1 + x_2)$$

At $(1, 2)$, the gradient is $(6, 6)$. Moving in direction $(6, 6)$ increases $f$ the most; moving in $(-6, -6)$ decreases it the most ([≈27:12](../raw/transcripts/02-learning-i.md)).

For vector input $f(\mathbf{x}) = (\sum_i x_i)^2$, the analytical gradient is $2 \cdot \text{sum}(\mathbf{x}) \cdot \mathbf{1}$ — a vector of the same shape as the input ([≈30:17](../raw/transcripts/02-learning-i.md)).

## Computation Graphs and Backpropagation

See the dedicated [backpropagation](backpropagation.md) topic page.

Complex ML functions are composed of a handful of basic operations (add, multiply, square, exp, log). Rather than deriving gradients by hand for every new function, we can build a **computation graph** and run **backpropagation** to compute all gradients automatically ([≈33:22](../raw/transcripts/02-learning-i.md)). This is reverse-mode automatic differentiation (autodiff), popularized by Werbos (1974) and Rumelhart, Hinton & Williams (1986).

### Building a Computation Graph

For $f(x_1, x_2) = (x_1 + x_2)^2$:

```python
x1 = Input("x1", 2.0)       # leaf nodes: fixed values
x2 = Input("x2", 3.0)
sum = Add("sum", x1, x2)    # intermediate node: x1 + x2
y = Squared("y", sum)       # root node: (sum)²
```

- **Leaf (input) nodes** hold fixed values ([≈36:26](../raw/transcripts/02-learning-i.md)).
- **Non-input nodes** represent primitive operations on their dependencies ([≈37:57](../raw/transcripts/02-learning-i.md)).
- **`forward()`** computes each node's value from its dependencies ([≈38:43](../raw/transcripts/02-learning-i.md)).

### The Backpropagation Algorithm

1. **Forward pass:** Topological sort from leaves → root, call `forward()` on each node ([≈51:10](../raw/transcripts/02-learning-i.md)).
2. **Initialize gradients:** All zeros, except root gradient = 1 (changing root by $\epsilon$ changes root by $\epsilon$) ([≈45:01](../raw/transcripts/02-learning-i.md)).
3. **Backward pass:** Reverse topological order from root → leaves, call `backward()` on each node. Each `backward()` pushes credit/blame to dependencies using the chain rule ([≈52:42](../raw/transcripts/02-learning-i.md)).

**Chain rule in action** for `y = (x₁ + x₂)²`:
$$\frac{\partial y}{\partial x_1} = \underbrace{\frac{\partial y}{\partial \text{sum}}}_{2 \cdot \text{sum}} \cdot \underbrace{\frac{\partial \text{sum}}{\partial x_1}}_{1}$$

The `Squared.backward()` computes `2 * value * self.grad` and adds it to its dependency's gradient. Then `Add.backward()` passes its gradient through to both `x₁` and `x₂` ([≈45:46](../raw/transcripts/02-learning-i.md)).

### Node Gradient Rules

| Node | Forward | Backward contribution to dependency `x` |
|---|---|---|
| `Add(x, y)` | `x + y` | `x.grad += self.grad` |
| `Subtract(x, y)` | `x - y` | `x.grad += self.grad`; `y.grad -= self.grad` |
| `Multiply(x, y)` | `x @ y` | `x.grad += self.grad @ y.T`; `y.grad += x.T @ self.grad` |
| `Squared(x)` | `x²` | `x.grad += 2 * x * self.grad` |

([slides](../raw/slides/02-learning-i.md#section-node-implementations))

**Important:** A node's `.grad` is always with respect to the **root** — the final output of the computation ([≈49:38](../raw/transcripts/02-learning-i.md)). Topological sort is needed (not naive tree traversal) because the same input can appear in multiple places in a real graph ([≈55:02](../raw/transcripts/02-learning-i.md)).

This is essentially what PyTorch does under the hood — building a computation graph and running forward/backward passes ([≈54:15](../raw/transcripts/02-learning-i.md)).

## Linear Regression — The ML Pipeline

Linear regression is the first concrete instantiation of the three-part ML template: **hypothesis class → loss function → optimization algorithm** ([≈56:40](../raw/transcripts/02-learning-i.md)).

See the dedicated [linear regression](linear-regression.md) topic page.

### The Prediction Task

**Input:** number of hours studied. **Output:** exam score. A **predictor** is any function $f: \mathcal{X} \to \mathcal{Y}$. Example: $f(x) = 2x + 1$ ([≈57:27](../raw/transcripts/02-learning-i.md)).

**The ML problem:** Given training data (input-output pairs demonstrating desired behavior), produce a predictor. This requires answering three questions ([≈58:13](../raw/transcripts/02-learning-i.md)):

### 1. Hypothesis Class — Which predictors are possible?

Define a **parameterized** family of functions. For linear regression:

$$\mathcal{F} = \{f_{\mathbf{w}, b}(\mathbf{x}) = \mathbf{w} \cdot \mathbf{x} + b \mid \mathbf{w} \in \mathbb{R}^d, b \in \mathbb{R}\}$$

The **hypothesis class** is the set of all predictors obtainable by varying the parameters. It constrains the search space — these are all linear functions; you can't have squiggly lines ([≈59:46](../raw/transcripts/02-learning-i.md)).

In deep learning: hypothesis class = **model architecture**; a specific predictor = **model** (architecture + trained weights) ([≈1:01:17](../raw/transcripts/02-learning-i.md)).

### 2. Loss Function — How good is a predictor?

The **squared loss** for one example $(x, y)$ with parameters $(w, b)$:

$$\text{loss}(w, b; x, y) = \big(f_{w,b}(x) - y\big)^2$$

The residual $f_{w,b}(x) - y$ is the vertical distance from the prediction line to the data point ([≈1:02:49](../raw/transcripts/02-learning-i.md)).

The **training loss** is the average over all $n$ training examples:

$$\text{TrainLoss}(w, b) = \frac{1}{n} \sum_{i=1}^{n} \big(f_{w,b}(x^{(i)}) - y^{(i)}\big)^2$$

Lower loss = better fit. Loss is bad — you want to minimize it ([≈1:05:07](../raw/transcripts/02-learning-i.md)).

### 3. Optimization — Gradient Descent

We can compute the training loss for any parameters. Finding the *minimum* is an optimization problem. Gradient descent solves it approximately:

**Per-example gradient** (weight $w$, bias $b$):
$$\frac{\partial \text{loss}}{\partial w} = 2 \cdot (f_{w,b}(x) - y) \cdot x \quad\quad \frac{\partial \text{loss}}{\partial b} = 2 \cdot (f_{w,b}(x) - y)$$

**Batch gradient:** average per-example gradients across all training points ([≈1:07:26](../raw/transcripts/02-learning-i.md)).

**Update rule:**
$$w := w - \eta \cdot \frac{\partial \text{TrainLoss}}{\partial w} \quad\quad b := b - \eta \cdot \frac{\partial \text{TrainLoss}}{\partial b}$$

where $\eta$ (eta) is the **learning rate** — how far we step per iteration ([≈1:08:12](../raw/transcripts/02-learning-i.md)).

After one step, the training loss decreases. Iterating this is **gradient descent** ([≈1:08:58](../raw/transcripts/02-learning-i.md)).

### Gradient Descent Notes

- **Learning rate tradeoff:** Too high → divergence (crash). Too low → slow convergence ([≈1:10:29](../raw/transcripts/02-learning-i.md)).
- **Convexity:** Guaranteed to find the global minimum for convex functions (like linear regression). Deep learning is non-convex — no such guarantee, but works well in practice ([≈1:11:15](../raw/transcripts/02-learning-i.md)).
- **Variants:** Stochastic gradient descent (SGD), Adam optimizer — covered later in the course.

## The General ML Template

This three-part structure — hypothesis class, loss function, optimization algorithm — is the template for approaching any ML problem ([≈1:12:01](../raw/transcripts/02-learning-i.md)).
