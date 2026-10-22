---
title: Lecture 2 — Learning I (Slides)
lecture: 2
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content. This file transcribes the content of the two source files that
  comprise Lecture 2. There are no traditional slide numbers; sections correspond
  to function calls in the source.
---

# Lecture 2 Slides — Learning I

Lecture 2 comprises two executable Python files: `backpropagation.py` and `linear_regression.py`.

---

## Part 1: Backpropagation (`backpropagation.py`)
**Source:** <https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/4f1dc062/backpropagation.py>
**Rendered:** <https://stanford-cs221.github.io/autumn2025-lectures/?trace=backpropagation>

### Section: Einops Review

**Tensor Order** (also called rank, but clashes with rank of matrix):
- Order 0 = scalars
- Order 1 = vectors
- Order 2 = matrices

In einops, we name axes — "Choose name based on what that axis represents (just like variable names in code)." Example: for a data matrix, axis names: `example, feature`.

**Vector examples** using `x = np.array([0, 1, 10])`:

| Operation | einsum |
|---|---|
| Identity | `einsum(x, "i -> i")` |
| Sum | `einsum(x, "i ->")` |
| Elementwise product | `einsum(x, x, "i, i -> i")` |
| Dot product | `einsum(x, x, "i, i ->")` |
| Outer product | `einsum(x, x, "i, j -> i j")` |
| Triple outer | `einsum(x, x, x, "i, j, k -> i j k")` |

**Matrix examples** using `m = np.array([[0, 1, 2], [1, 10, 0]])`:

| Operation | einsum |
|---|---|
| Sum all entries | `einsum(m, "i j ->")` |
| Row sums | `einsum(m, "i j -> i")` |
| Column sums | `einsum(m, "i j -> j")` |
| Transpose | `einsum(m, "i j -> j i")` |
| Matrix-vector | `einsum(m, x, "i j, j -> i")` |
| m·mᵀ | `einsum(m, m, "i k, j k -> i j")` |
| mᵀ·m | `einsum(m, m, "k i, k j -> i j")` |

**General setup:** For each assignment of input axes: multiply corresponding tensor elements, add to corresponding output element. Output axes are a subset of input axes. "It's all just additions and multiplications with bookkeeping!"

### Section: Motivation (Linear Regression Preview)

Uses linear regression as a motivating example:

```python
x = np.array([[1, 2, 0], [0, -1, 1]])  # n × d
y = np.array([0, 3])                    # n vector of targets
w = np.array([1, 0, 1])                 # d vector of weights
```

Operations: `predictions = x @ w` → `residuals = predictions - y` → `losses = residuals²` → `total_loss = sum(losses)`

Defines objective function: takes a vector input, returns a scalar output.

```python
def objective(w: np.ndarray) -> float:
    loss = np.sum((x @ w - y) ** 2)
    return loss
```

Ultimate goal: find `w` that minimizes `objective(w)`. Given a fixed `w`, how should we tweak `w` to improve `objective(w)`?

### Section: Gradients

The **gradient** of a function tells us the direction that decreases the function the most.

Use cases beyond model parameters:
- Optimizing the input (an image) that maximizes error (adversarial examples)
- Optimizing the relative proportions over datasets

Summary:
- Functions that take an input tensor and output a scalar
- Partial derivative measures how much the function changes when an element of the tensor changes
- Gradient is the full tensor of partial derivatives (same shape as input)

### Section: 1D Example

`f(x) = x²`. Numeric derivative: `(f(x+dx) - f(x))/dx`. Analytical: `df/dx = 2x`. Graphically, the derivative is the slope of the tangent line at `x`.

### Section: 2D Example

`f(x₁, x₂) = (x₁ + x₂)²`. Partial derivatives:
- `∂f/∂x₁ = 2(x₁ + x₂)`
- `∂f/∂x₂ = 2(x₁ + x₂)`

From `(1, 2)`, moving in direction of `(6, 6)` will increase `f` the most. Moving in `(-6, -6)` decreases `f` the most.

### Section: Vector Example

`f(x) = (∑ᵢ xᵢ)²`. Analytical gradient: `∇f = 2 · sum(x) · ones_like(x)`. Works for any number of dimensions.

### Section: Computation Graphs

For any function, we can compute the gradient manually — but this is tedious and error-prone. Complex functions are composed of basic operations: addition, multiplication, exp, log, etc.

**Autodiff (reverse-mode automatic differentiation):** Werbos 1974.
- Build an explicit computation graph of the function
- Compute partial derivatives recursively by traversing the graph

"We will implement a mini-PyTorch today."

Key points:
- Computation graphs represent complex functions in terms of primitive operations
- Computing function values and gradients with respect to inputs
- Key mathematical property: **chain rule**
- Backpropagation is a general algorithm to organize the computation of gradients (and values)

### Section: Computation Graph Example

Function: `f(x₁, x₂) = (x₁ + x₂)²`

```python
x1 = Input("x1", np.array(2.0))
x2 = Input("x2", np.array(3.0))
sum = Add("sum", x1, x2)
y = Squared("y", sum)
```

- Input (leaf) nodes represent fixed values
- Non-input nodes represent primitive computations on dependencies
- `forward()` computes the `value` of a node
- The result is at the root node (e.g., `y`)

**Chain rule for `dy/dx₁`:** `∂y/∂sum · ∂sum/∂x₁ = 2·sum · 1`

Backward pass:
```python
y.grad = np.ones_like(y.value)       # root gradient = 1
sum.backward()  # pushes 2*sum.value * y.grad to sum.grad
x1.backward()   # pushes sum.grad to x1.grad (for Add)
```

**General principle:** `node.backward()` updates the partial derivatives of the dependencies of `node`. Assumes `node.grad` is computed and all `value`s are computed.

### Section: Backpropagation Algorithm (General)

1. **Forward pass:** Traverse graph from inputs (leaves) to root, call `forward()`
2. **Backward pass:** Traverse graph from root to inputs (leaves), call `backward()`

Initialize all gradients to zero, except root which is set to 1 (changing root by ε changes root by ε).

**Linear regression computation graph:**
```python
x = Input("x", np.array([[1., 2, 0], [0, -1, 1]]))
y = Input("y", np.array([[0.], [3]]))
w = Input("w", np.array([[1.], [0], [1]]))
predictions = Multiply("predictions", x, w)
residuals = Subtract("residuals", predictions, y)
losses = Squared("losses", residuals)
ones = Input("ones", np.ones((1, 2)))
total_loss = Multiply("total_loss", ones, losses)
backpropagation(total_loss)
```

### Section: Node Implementations

| Node | Forward | Backward |
|---|---|---|
| `Input` | Value already set | No dependencies |
| `Add(x, y)` | `x.value + y.value` | `x.grad += self.grad; y.grad += self.grad` |
| `Subtract(x, y)` | `x.value - y.value` | `x.grad += self.grad; y.grad -= self.grad` |
| `Multiply(x, y)` | `x.value @ y.value` | `x.grad += self.grad @ y.value.T; y.grad += x.value.T @ self.grad` |
| `Squared(x)` | `x.value ** 2` | `x.grad += 2 * x.value * self.grad` |

**Topological sort:** Returns nodes where each node follows its dependencies.

**Full backpropagation(root):**
1. Forward pass: iterate nodes in topological order, call `node.forward()`
2. Initialize gradients: all `np.zeros_like(node.value)`, root gets `np.ones_like(root.value)`
3. Backward pass: iterate nodes in reverse topological order, call `node.backward()`

---

## Part 2: Linear Regression (`linear_regression.py`)
**Source:** <https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/17570e8f/linear_regression.py>
**Rendered:** <https://stanford-cs221.github.io/autumn2025-lectures/?trace=linear_regression>

This part covers the full machine learning pipeline for linear regression.

### Section: Prediction Task

Motivating scenario: "Predict how well you do on an exam given the number of hours you study."
- **Input**: number of hours you study (e.g., 3)
- **Output**: your score on the exam (e.g., 70)

A **predictor** is a function that takes an input and produces an output.

```python
def fixed_f(x: float) -> float:
    return 2 * x + 1
```

"But how do we get the predictor?"

### Section: The Machine Learning Problem

The learning problem starts with **training data**, which demonstrates the task.

- Training data is a set of **examples**
- Each example consists of an (input, output) pair
- A **learning algorithm** takes the training data and produces a predictor

Three key questions:
1. Which predictors are possible? → **hypothesis class**
2. How good is a predictor? → **loss function**
3. How do we compute the best predictor? → **optimization algorithm**

### Section: Hypothesis Class

"Which predictors are possible?" Define a set of *possible* predictors so the learning algorithm can choose the best one. This requires **parameters**.

For linear predictors, each set of parameters has a **weight** and a **bias**:

```python
@dataclass(frozen=True)
class Parameters1D:
    weight: float
    bias: float

def f(params: Parameters1D, x: float) -> float:
    return params.weight * x + params.bias
```

The **hypothesis class** is the set of all predictors you can get by choosing parameters (weight, bias) — infinite parameters, infinite predictors, but all linear.

Deep learning terminology mapping:
- Hypothesis class → **model architecture**
- Predictor → **model**
- Parameters are a collection of tensors (e.g., DeepSeek V3)

### Section: Loss Function

"How to judge each of the many possible predictors."

**Squared loss for one example:**
```python
residual = f(params, example.input) - example.output
loss = residual ** 2
```

**Training loss** = average of per-example losses across training data.

The loss tells us how well a given set of parameters fits the training data. Lower loss is better.

### Section: Optimization Algorithm

"For every set of parameters, we can compute the training loss." Goal: find parameters that yield the lowest training loss — an optimization problem.

The gradient provides the direction that increases the function the most → go in the opposite direction.

**Gradient of squared loss for one example:**
```python
residual = (params.weight * x + params.bias) - y
grad_weight = 2 * residual * x
grad_bias = 2 * residual * 1
```

**Batch gradient:** average per-example gradients across all training examples.

**Gradient descent update:**
```python
params.weight -= learning_rate * grad_weight
params.bias   -= learning_rate * grad_bias
```

After each update, the training loss decreases. Rinse and repeat.

### Section: Gradient Descent Algorithm

Loop of 10 steps:
1. Compute `train_loss` for current parameters
2. Compute gradient of train loss
3. Update: `params = params - learning_rate * grad`

Notes:
- **Learning rate** controls how fast you drive (tradeoff: speed vs stability)
- Guaranteed to converge for convex functions, not for deep learning (non-convex)
- Other algorithms: stochastic gradient descent, Adam

### Summary

This is linear regression in a nutshell — and the general template for ML:
1. **Hypothesis class** — define space of possible predictors
2. **Loss function** — measure fit to training data
3. **Optimization algorithm** — gradient descent finds parameters minimizing loss

A predictor maps input to output. Training examples demonstrate desired behavior. The learning algorithm turns training data into a predictor.
