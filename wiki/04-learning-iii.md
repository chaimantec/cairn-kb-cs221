---
title: Learning III — Deep Neural Networks, PyTorch, and the Training Tricks
lecture: 4
video: https://www.youtube.com/watch?v=89NND-Ca0yY
slides: ../raw/slides/04-learning-iii.md
transcript: ../raw/transcripts/04-learning-iii.md
---

# Lecture 4 — Learning III

This lecture bridges from linear models to deep learning. Liang first ports everything to **PyTorch**, showing how the computation-graph concepts from lecture 2 map to `.backward()`, `.detach()`, and `torch.no_grad()`. Then, the main arc: how to build nonlinear classifiers by stacking linear layers with activation functions — discovering that pure linear stacks don't help (they collapse back to linear), but adding a **ReLU** makes them genuinely more expressive. The second half is a survival guide: vanishing/exploding gradients and five techniques to fight them — activation function choice, residual connections, layer normalization, careful initialization, and better optimizers.

## PyTorch — NumPy + Autodiff

The computation graph library built in lecture 2 was educational. In practice: **PyTorch** (or JAX) — more efficient, industrial-grade, with pre-built modules ([≈1:36](../raw/transcripts/04-learning-iii.md)).

### Key Mapping: Our Library → PyTorch

| Concept | Our Library | PyTorch |
|---|---|---|
| Create a node | `Input("x", value)` | `torch.tensor(value, requires_grad=True)` |
| Dot product | `DotProduct("z", x, y)` | `x @ y` |
| Backpropagation | `backpropagation(root)` | `root.backward()` |
| Access gradient | `node.grad` | `tensor.grad` |

([≈3:08](../raw/transcripts/04-learning-iii.md))

**Important mental model:** `torch.tensor` are **nodes in a computation graph**, not just values. PyTorch deliberately makes tensors look like NumPy arrays, but you should think of them as graph nodes — this makes debugging and understanding much easier ([≈4:43](../raw/transcripts/04-learning-iii.md)).

Differences:
- Values are computed **eagerly** during construction (no separate `forward()` call)
- Use `.backward()` on the root to backpropagate to all nodes with `requires_grad=True`
- Use `requires_grad=True` for parameters, not data (unless doing adversarial examples) ([≈7:00](../raw/transcripts/04-learning-iii.md))
- Tensors must be floating-point to take gradients — `1` vs `1.` matters ([≈3:56](../raw/transcripts/04-learning-iii.md))

### Node vs. Value — `.detach()` and `torch.no_grad()`

A tensor can be used two ways ([≈7:46](../raw/transcripts/04-learning-iii.md)):

1. **By node** (default): gradients flow through. `y = x**2; z = y**2` — z depends on x through y.
2. **By value** (detached): gradients don't flow. `y.detach()**2` — the computation using y's value starts a new graph, disconnected from x.

**Mental model:** pass-by-reference (node) vs. pass-by-value (detached) in programming languages ([≈10:06](../raw/transcripts/04-learning-iii.md)).

**`torch.no_grad()`:** Context manager that disables gradient tracking entirely. Use at test time — no need to compute gradients when you're not updating parameters. Also reduces memory overhead and provides a safety net (trying to backprop through it throws an error) ([≈11:41](../raw/transcripts/04-learning-iii.md)).

### Linear Models in PyTorch — The Full Loop

PyTorch provides pre-built modules for all three ML questions:

```python
# 1. Model (hypothesis class)
model = nn.Linear(input_dim, output_dim)
logits = model(x)           # Forward: x → logits

# 2. Loss function
loss_fn = nn.CrossEntropyLoss()
loss = loss_fn(logits, targets)

# 3. Optimizer
optimizer = torch.optim.SGD(model.parameters(), lr=0.1)
```

**Training loop ([≈20:15](../raw/transcripts/04-learning-iii.md)):**
```python
for step in range(num_steps):
    logits = model(x)              # Forward pass
    loss = loss_fn(logits, targets) # Compute loss
    optimizer.zero_grad()           # Clear old gradients ← DON'T FORGET
    loss.backward()                 # Populate .grad on all parameters
    optimizer.step()                # Update weights using gradients
```

**Critical: `optimizer.zero_grad()`.** PyTorch accumulates gradients (for efficiency when iterating over examples). You must zero them between training iterations. This is a feature, not a bug ([≈21:01](../raw/transcripts/04-learning-iii.md)).

**`.backward()` vs `optimizer.step()`:** `.backward()` populates `.grad` on parameters. `optimizer.step()` reads those `.grad` values and updates the parameter weights. Two separate steps — you could inspect or modify gradients between them ([≈23:21](../raw/transcripts/04-learning-iii.md)).

The optimizer is called SGD but is actually agnostic to where gradients come from: if gradients come from a mini-batch, it's SGD; if from all examples, it's gradient descent ([≈1:09:40](../raw/transcripts/04-learning-iii.md)).

## Why Nonlinear? — The Feature Map Insight

Linear classifiers can only make straight cuts. Nonlinear data (e.g., concentric circles) needs nonlinear models. But here's the twist: **even linear models can be nonlinear** — by first applying a nonlinear feature map ([≈28:02](../raw/transcripts/04-learning-iii.md)).

**Example — Quadratic classifier:**
```python
def quadratic_classifier(x):
    logit = (x[0] - 1)**2 + (x[1] - 1)**2 - 2
    return 1 if logit > 0 else -1
```
Decision boundary is a circle — definitely nonlinear.

**Making it linear via feature map:**
Define φ(x) = [x₀, x₁, x₀² + x₁²]. Then:
```python
def predictor(x):
    phi = feature_map(x)
    logit = -2*phi[0] - 2*phi[1] + phi[2]   # Linear in φ!
    return 1 if logit > 0 else -1
```

A **linear classifier in the higher-dimensional φ-space** is a **nonlinear classifier in the original x-space** ([≈31:52](../raw/transcripts/04-learning-iii.md)). This is the insight behind kernel methods.

**Simple algorithm:**
1. Preprocess data with a fixed feature map φ
2. Learn a linear predictor on φ(x)

**Limitation:** φ is fixed. Can we **learn** it too? This is what neural networks do ([≈33:24](../raw/transcripts/04-learning-iii.md)).

## Multi-Layer Perceptrons (MLPs)

### Two Linear Layers = Still Linear

First attempt: stack two linear layers. W₁ maps input → hidden, W₂ maps hidden → logits:

```python
class LinearMLP(nn.Module):
    def forward(self, x):
        hidden = self.w1(x)       # First linear layer (learned feature map)
        logits = self.w2(hidden)  # Second linear layer (predictor)
        return logits
```

**Problem:** This is still a linear classifier! Matrix multiplication is associative: (x·W₁)·W₂ = x·(W₁·W₂) = x·W. The two matrices collapse into one — no added expressivity ([≈38:01](../raw/transcripts/04-learning-iii.md)).

Caveat: requires hidden_dim ≥ input_dim and output_dim, otherwise it's low-rank and genuinely different (but still not more expressive in a useful way) ([≈39:33](../raw/transcripts/04-learning-iii.md)).

### Adding a Nonlinear Activation — ReLU

To break the linearity, insert a **nonlinear activation function** between the layers:

$$\mathbf{x} \xrightarrow{\text{linear}} \mathbf{z} \xrightarrow{\text{ReLU}} \mathbf{h} \xrightarrow{\text{linear}} \text{logits}$$

**ReLU (Rectified Linear Unit):**
$$\text{ReLU}(x) = \max(0, x)$$

```python
class MultiLayerPerceptron(nn.Module):
    def forward(self, x):
        x_transformed = self.w1(x)     # Linear
        hidden = torch.relu(x_transformed)  # ← Nonlinearity!
        logits = self.w2(hidden)        # Linear
        return logits
```

([≈42:39](../raw/transcripts/04-learning-iii.md))

This tiny change — a `max(0, x)` — makes the model genuinely nonlinear and much more expressive.

[Lecture 17](17-language-models.md) asks what goes wrong if this MLP is applied directly to language modeling: its parameter count grows with the sequence length and the vocabulary, its weights cannot favour different positions depending on the input, and it cannot reuse computation, the three problems a Transformer is designed around ([slides 76–79](../raw/slides/17-language-models.md)).

**Historical note:** "Perceptron" comes from Rosenblatt (1958). "Multi-layer perceptron" (MLP) comes from stacking them in the 60s–80s. "Neural network" is synonymous ([≈41:53](../raw/transcripts/04-learning-iii.md)).

**Terminology:**
- Activations = hidden units = neurons = intermediate values computed in the forward pass ([≈44:11](../raw/transcripts/04-learning-iii.md))

### Dead Neurons and Activation Function Choice

ReLU's gradient is zero for x ≤ 0. If a neuron is always ≤ 0 for every training example, it's **dead** — its weights never update, wasting model capacity ([≈44:58](../raw/transcripts/04-learning-iii.md)).

**Fixes:** Leaky ReLU (small slope instead of zero), GeLU, Swish — all avoid exactly-zero gradients.

**The Activation Function Tradeoff:**
| Property | Linear (y = x) | ReLU | Sigmoid |
|---|---|---|---|
| Gradient quality | Perfect (always 1) | Good (x > 0), dead (x ≤ 0) | Near-zero far from origin |
| Expressivity | None (collapses) | Good | Good |

You're balancing **gradient flow** (want near-linear) against **expressivity** (need nonlinear) ([≈47:16](../raw/transcripts/04-learning-iii.md)). ReLU is popular because the x > 0 region is perfectly linear (great gradients) while the x ≤ 0 region provides nonlinearity.

## Deep Neural Networks

A single MLP layer may not be enough. The universal approximation theorem says a two-layer network with infinite hidden units can represent any function — but infinite width is impractical. Instead: **stack multiple MLP layers** ([≈48:49](../raw/transcripts/04-learning-iii.md)).

**Intuition:** Each layer learns progressively more abstract features:
- Early layers: edges, textures
- Middle layers: object parts, patterns
- Late layers: whole objects, concepts

This is empirical — trained CNNs actually develop this hierarchy. It's why we say deep learning "learns the feature map" ([≈49:35](../raw/transcripts/04-learning-iii.md)).

```python
class DeepNeuralNetwork(nn.Module):
    def forward(self, x):
        x = relu(self.w1(x))
        x = relu(self.w2(x))
        x = self.w3(x)       # Final layer → logits
        return x
```

**Problem:** Training gets slower with more layers, especially at the beginning. This used to be considered essentially impossible ([≈51:07](../raw/transcripts/04-learning-iii.md)).

## The Vanishing/Exploding Gradient Problem

Each layer is multiplicative. For a deep network with weight w applied at every layer:

- **Vanishing:** w = 0.5, 20 layers → gradient ≈ 0.5¹⁹ ≈ 10⁻⁶. Update is negligible — no progress ([≈52:40](../raw/transcripts/04-learning-iii.md)).
- **Exploding:** w = 2, 20 layers → gradient ≈ 2¹⁹ × 20 ≈ 10⁷. Update is enormous — overflow ([≈53:25](../raw/transcripts/04-learning-iii.md)).
- **Stable:** w ≈ 1. The only multiplier you can apply many times without disaster ([≈54:12](../raw/transcripts/04-learning-iii.md)).

For matrices, the condition is on eigenvalues of the weight matrix — want them close to 1.

## Five Techniques for Training Deep Networks

### 1. Residual Connections (Skip Connections)

Instead of x → f(x), compute **x → x + f(x)** ([≈55:43](../raw/transcripts/04-learning-iii.md)).

```python
# Without residual
x = relu(self.w1(x))
x = relu(self.w2(x))

# With residual
x = relu(self.w1(x))
x = x + relu(self.w2(x))    # ← Skip connection
```

**Why it works:**
- Provides an "escape hatch" for gradients — even if f(x) has zero gradient, the skip path lets gradients flow backward ([≈57:14](../raw/transcripts/04-learning-iii.md)).
- For f(x) = w·x, the combined operation is (1 + w)·x — centered around 1, our friend. Even if w ≈ 0, the multiplier ≈ 1.

**History:** Appeared as highway networks, LSTMs (1997), residual networks / ResNets for vision (2015). In transformers, they're ubiquitous ([≈56:29](../raw/transcripts/04-learning-iii.md)).

**Result:** Training is dramatically faster. A DNN with residual connections trains much better than the same depth without ([≈58:00](../raw/transcripts/04-learning-iii.md)).

### 2. Layer Normalization

Don't let activation magnitudes grow too big or too small. Normalize them:

$$y = \frac{x - \mu}{\sqrt{\sigma^2 + \epsilon}}$$

The real LayerNorm adds learnable parameters:

$$\text{LayerNorm}(x) = \gamma \cdot \frac{x - \mu}{\sqrt{\sigma^2 + \epsilon}} + \beta$$

- ε: tiny constant (10⁻⁵) — prevents division by zero ([≈1:01:04](../raw/transcripts/04-learning-iii.md))
- γ (weight): learnable scaling, initialized to 1
- β (bias): learnable shifting, initialized to 0

In PyTorch: `nn.LayerNorm(normalized_shape)`. Layer norm keeps activations away from zero and infinity ([≈1:02:40](../raw/transcripts/04-learning-iii.md)).

### 3. Proper Initialization — Xavier

At initialization, activations shouldn't be huge or tiny. For a weight matrix W ∈ ℝ^{d_in × d_out}:

**The problem:** `x @ W` where x ~ 𝒩(0, 1) gives outputs scaling as √(d_in). For d_in = 16,384, outputs are ~128 — too large, causing unstable training ([≈1:03:25](../raw/transcripts/04-learning-iii.md)).

**The fix:** Scale by 1/√(d_in):

```python
w = torch.randn(input_dim, output_dim) / np.sqrt(input_dim)
```

This is **Xavier initialization** [Glorot & Bengio, 2010]. Now outputs stay O(1) regardless of input dimension ([≈1:04:58](../raw/transcripts/04-learning-iii.md)).

**Extra safety:** Truncate the normal at [−3, 3] to eliminate tail outliers:
```python
w = nn.init.trunc_normal_(torch.empty(d_in, d_out),
    std=1/np.sqrt(d_in), a=-3, b=3)
```

([≈1:05:44](../raw/transcripts/04-learning-iii.md))

### 4. Activation Function Choice

Prefer activation functions without zero-gradient regions — Leaky ReLU, GeLU, Swish over plain ReLU when dead neurons are a concern ([≈45:44](../raw/transcripts/04-learning-iii.md)).

### 5. Better Optimizers — SGD vs. Adam

**Gradient Descent (GD):** Compute gradient over all N examples → one update. For N = 10⁷, that's a lot of work per step ([≈1:06:31](../raw/transcripts/04-learning-iii.md)).

**Stochastic Gradient Descent (SGD):** Each step, randomly sample a mini-batch of B examples. The gradient on the mini-batch is an **unbiased estimate** of the true gradient. Much faster per step, and the noise can help escape bad local minima ([≈1:07:17](../raw/transcripts/04-learning-iii.md)).

**Implementation:** At each epoch, randomly permute the data, then take consecutive chunks as batches:

```python
perm = torch.randperm(N)
for i in range(0, N, batch_size):
    batch = perm[i : i + batch_size]
    # compute gradient on batch, update
```

([≈1:08:49](../raw/transcripts/04-learning-iii.md))

**Adam:** A more sophisticated optimizer (adaptive learning rates per parameter, momentum). Replacing SGD with Adam is often a one-line change that improves convergence ([≈1:10:26](../raw/transcripts/04-learning-iii.md)).

## Summary — The Deep Learning Stack

| Layer | Purpose | What to use |
|---|---|---|
| Model | Expressivity | Linear → ReLU → Linear → ReLU → … → Linear |
| Residual connections | Gradient flow | x → x + f(x) at each block |
| Layer norm | Activation stability | `nn.LayerNorm` after attention/linear |
| Initialization | Start stable | Xavier: divide by √(d_in) |
| Activation | Nonlinearity + gradient | ReLU / GeLU / Swish |
| Optimizer | Efficient updates | Adam (adaptive) or SGD (simpler) |
