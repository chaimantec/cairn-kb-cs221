---
title: Backpropagation
lectures:
  - 02 — full coverage: computation graphs, chain rule, forward/backward passes
---

# Backpropagation

Backpropagation is the algorithm that computes gradients of a scalar loss with respect to every parameter in a model by traversing a **computation graph**. It is the engine behind all modern deep learning training. Liang covers it in [Lecture 2](02-learning-i.md), building a mini-PyTorch from scratch to show how it works under the hood ([≈34:54](../raw/transcripts/02-learning-i.md)).

## Why Not Compute Gradients by Hand?

Any function composed of basic operations (add, multiply, square, exp, log — "I can count them on one hand") can have its gradients computed automatically by building a graph and applying the chain rule recursively. Doing it manually for something like a Transformer would be "a very annoying endeavor" ([≈33:22](../raw/transcripts/02-learning-i.md)).

This is **reverse-mode automatic differentiation** (autodiff), developed by Werbos (1974) and popularized by Rumelhart, Hinton & Williams (1986) ([≈34:54](../raw/transcripts/02-learning-i.md)).

## Computation Graphs

A computation graph represents a function as a DAG of nodes:

- **Leaf (input) nodes:** hold fixed values (data, parameters) ([≈36:26](../raw/transcripts/02-learning-i.md))
- **Intermediate nodes:** represent primitive operations on their dependencies ([≈37:57](../raw/transcripts/02-learning-i.md))
- **Root node:** the final output — typically a scalar loss

Every node stores two things:
- **`value`** — the result of the forward computation
- **`grad`** — the gradient of the **root** with respect to this node's value (how much changing this node changes the final output) ([≈44:16](../raw/transcripts/02-learning-i.md))

### Example

For $f(x_1, x_2) = (x_1 + x_2)^2$:

```
x1 (2.0) ──┐
            ├── Add ── Squared ── y (25.0)
x2 (3.0) ──┘
```

```python
x1 = Input("x1", np.array(2.0))
x2 = Input("x2", np.array(3.0))
sum = Add("sum", x1, x2)      # value becomes 5.0
y = Squared("y", sum)          # value becomes 25.0
```

## The Backpropagation Algorithm

### Step 1: Forward Pass

Topological sort from leaves → root, calling `forward()` on each node ([≈51:10](../raw/transcripts/02-learning-i.md)):

```python
for node in topological_sort(root):
    node.forward()
```

### Step 2: Initialize Gradients

All gradients set to zeros (same shape as each node's value). Root gradient set to 1 — because changing the root by $\epsilon$ changes the root by $\epsilon$ ([≈45:01](../raw/transcripts/02-learning-i.md)).

### Step 3: Backward Pass

Reverse topological order, calling `backward()` on each node. Each `backward()` uses the **chain rule** to push credit/blame to its dependencies ([≈52:42](../raw/transcripts/02-learning-i.md)):

```python
for node in reversed(topological_sort(root)):
    node.backward()
```

### The Chain Rule

For the example $y = ((x_1 + x_2))^2$:

$$\frac{\partial y}{\partial x_1} = \frac{\partial y}{\partial \text{sum}} \cdot \frac{\partial \text{sum}}{\partial x_1} = 2 \cdot \text{sum} \cdot 1 = 2 \cdot 5 \cdot 1 = 10$$

In code: `Squared.backward()` pushes $2 \cdot \text{sum} \cdot \text{self.grad}$ to `sum.grad`. Then `Add.backward()` copies `self.grad` to both `x1.grad` and `x2.grad` ([≈45:46](../raw/transcripts/02-learning-i.md)).

### Backward Rules for Common Nodes

| Node | Forward | Backward (pushing to dependency `x`) |
|---|---|---|
| `Add(x, y)` | `x + y` | `x.grad += self.grad` (same for `y`) |
| `Subtract(x, y)` | `x - y` | `x.grad += self.grad`; `y.grad -= self.grad` |
| `Multiply(x, y)` | `x @ y` | `x.grad += self.grad @ y.T`; `y.grad += x.T @ self.grad` |
| `Squared(x)` | `x²` | `x.grad += 2 * x * self.grad` |

([slides](../raw/slides/02-learning-i.md#section-node-implementations))

## Key Insights

- **`.grad` is always with respect to the root** — the final scalar output ([≈49:38](../raw/transcripts/02-learning-i.md)).
- **Topological sort is necessary**, not naive tree traversal — the same input can appear in multiple places in the graph, and you must accumulate contributions from all paths ([≈55:02](../raw/transcripts/02-learning-i.md)).
- **PyTorch does exactly this** — build a computation graph, run forward to compute values, run backward to compute gradients. It has more bells and whistles, but the core is the same ([≈54:15](../raw/transcripts/02-learning-i.md)).

## Connection to Gradient Descent

Backpropagation computes the gradients. [Gradient descent](gradient-descent.md) uses those gradients to update parameters:

$$\theta := \theta - \eta \cdot \nabla_\theta \mathcal{L}$$

See the [linear regression](linear-regression.md) page for a complete end-to-end example.

## Resources

- [Lecture 2 transcript](../raw/transcripts/02-learning-i.md) — full coverage (~33:22–55:02)
- [Lecture 2 slides](../raw/slides/02-learning-i.md#part-1-backpropagation-backpropagationpy)
- [Stanford CS221 backprop slides (2023)](https://stanford-cs221.github.io/autumn2023-extra/modules/learning/backpropagation.pdf)
