---
title: Lecture 4 — Learning III (Slides)
lecture: 4
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_file: https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/main/deep_learning.py
rendered: https://stanford-cs221.github.io/autumn2025-lectures/?trace=deep_learning
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. This file transcribes the
  content of `deep_learning.py`. There are no traditional slide numbers;
  sections correspond to function calls in the source. Figure descriptions were
  written by a vision pass: a vision-capable model read each rendered figure
  and described what it shows.
---

# Lecture 4 Slides — Learning III

Lecture 4 covers deep neural networks through a single executable Python file: `deep_learning.py`.

---

## Section: PyTorch Basics (`pytorch_basics`)

**Last unit:** linear regression/classification

**This unit:** non-linear regression/classification

So far, we've used NumPy and built our own computation graph library — to really understand what's going on under the hood. In practice, you want to use **PyTorch** (or JAX), which is:
- Much more efficient and industrial grade
- Already implements the many common operations

---

## Section: NumPy vs. PyTorch (`compare_numpy_and_pytorch`)

### NumPy + Custom Library
```python
x = Input("x", np.array([1, 2, 3]))
y = Input("y", np.array([4, 5, 6]))
z = DotProduct("z", x, y)
backpropagation(z)
```

### Same Computation in PyTorch
```python
x = torch.tensor([1., 2, 3], requires_grad=True)
y = torch.tensor([4., 5, 6], requires_grad=True)
z = x @ y
z.backward()
```

### In PyTorch:
- `torch.tensor` are actually nodes in the computation graph
- Operations (`@`) are parallel to NumPy
- Some minor naming differences (`torch.tensor` vs `np.array`)
- Values are computed eagerly during node construction (no `forward()` call)
- Call `.backward()` to backpropagate gradients (`.grad`) recursively
- Specify `requires_grad=True` to specify what to compute gradients for (parameters)

---

## Section: Node vs. Value (`node_or_value`)

There are two ways to use a node:
- **Use the node directly:** new values will backprop through the node
- **Use the node's value:** new values will **not** backprop through the node

### In PyTorch
- By default, PyTorch references by node (gradients flow through)
- To reference by value, call **`detach()`**
- `y.detach()` breaks the computation graph — gradients don't flow through

```python
x = torch.tensor(1., requires_grad=True)
y = x ** 2
z = y ** 2
u = torch.tensor(3., requires_grad=True)
l2 = y.detach() ** 2 + u
l2.backward()
# u.grad is computed, but x.grad is not (detach broke the chain)
```

### `torch.no_grad()`
Sometimes you want to just compute values with no gradients. Common use case: prediction at test-time (not updating parameters).

```python
with torch.no_grad():
    y = x ** 2
    z = y ** 2
# Cannot call z.backward() — gradients were never recorded
```

---

## Section: Linear Models in PyTorch (`linear_models`)

PyTorch has built-in:
- **Models** (e.g., `nn.Linear`)
- **Loss functions** (e.g., `nn.CrossEntropyLoss`)
- **Optimizers** (e.g., `torch.optim.SGD`)
- ...and much more

### Complete Training Loop
```python
# Data
x = torch.tensor([1., 2, 3, 4])
target_y = torch.tensor([0., 1, 0])

# Linear model
model = nn.Linear(4, 3)
logits = model(x)

# Loss function
cross_entropy = nn.CrossEntropyLoss()
loss = cross_entropy(logits, target_y)
loss.backward()

# Optimizer (SGD = stochastic gradient descent)
optimizer = torch.optim.SGD(model.parameters(), lr=0.1)
optimizer.step()  # Updates the parameters
```

### Summary
1. **Define a model** (e.g., linear): inputs → logits
2. **Define a loss** (e.g., cross entropy): logits, targets → loss
3. **Define an optimizer** (e.g., SGD): updates parameters using gradients

---

## Section: Non-Linear Motivation (`nonlinear_motivation`)

**So far:** linear classifiers → decision boundaries are straight cuts of input space.

Or in linear regression:

(Chart: `linear-regressors.png` — two straight-line regressors on axes x (0 to 5) and y (0 to 4): a red line rising from (0, 1) to about (5, 3.9), slope ≈ 0.6, and a purple line rising gently from (0, 2) to about (5, 3), slope ≈ 0.2; the two lines cross around x ≈ 2.5–3.)

**But data sometimes looks like this:**

(Chart: `nonlinear-points.png` — a scatter plot, x from 0 to 5, y from 0 to 4, of 20 green points tracing a wavy hump: rising from (0, 2.4) to a peak around (2.1, 4.2), plateauing at y ≈ 3.2–3.9 between x ≈ 2.7 and 4.0, then falling to about (5, 2.6). The trend rises then falls, so no single straight line would fit it well.)

For these cases, we need **non-linear** models.

### Non-Linear Models Include:
- Decision trees, nearest neighbors, neural networks
- ...and even linear models! (Wait, what?)

### Quadratic Classifier Example
```python
def quadratic_classifier(x):
    logit = (x[0] - 1) ** 2 + (x[1] - 1) ** 2 - 2
    if logit > 0:
        predicted_y = 1
    else:
        predicted_y = -1
    return predicted_y
```
The decision boundary is a **circle** — definitely non-linear.

(Chart: `quadratic-classifier.png` — the circular decision boundary in 2D input space: a thick purple circle centered at (1, 1) with radius ≈ 1.4 (the boundary $(x_0-1)^2 + (x_1-1)^2 = 2$, i.e. radius $\sqrt{2}$), drawn on axes x1 and x2 both running −3 to 3 with horizontal gray gridlines; four short purple arrows just inside the circle — top pointing down, bottom pointing up, left pointing right, right pointing left — mark the inside as one predicted class and the outside as the other.)

### Making it Linear via Feature Maps
Define a fixed non-linear feature map:
```python
def feature_map(x):
    return np.array([x[0], x[1], x[0] ** 2 + x[1] ** 2])
```

Then define a linear predictor on the transformed features:
```python
def predictor(x):
    phi = feature_map(x)
    logit = -2 * phi[0] - 2 * phi[1] + phi[2]
    ...
```

A **linear classifier in a higher-dimensional space** leads to a **non-linear classifier in the original space**.

(Video: `svm-polynomial-kernel.mp4` — a 43-second animation of an SVM with a polynomial kernel, credited to Udi Aharoni, with no axis labels or scales. It starts from a flat 2D plane holding a cluster of blue points ringed by a glowing white circle with red points scattered outside. The view lifts into 3D and the surface deforms into a cyan bowl (paraboloid): blue points sit inside the bowl's hollow, red points on the plane and the bowl's outside. A purple plane appears behind the bowl, then the animation returns to the top-down 2D view showing the glowing circle around the blue cluster — the non-linear boundary in the original space that corresponds to a linear separator in the lifted space.)

### Simple Algorithm:
1. Preprocess data by applying `feature_map`
2. Learn a linear predictor on the processed data

**Drawback:** `feature_map` is fixed — can we learn it as well?

---

## Section: Multi-Layer Perceptron — Linear Version (`multi_layer_perceptron_linear`)

Let's try to make the function more expressive by defining two layers:
- The first layer is a feature map
- The second layer is the linear predictor

```python
class LinearMLP(nn.Module):
    def __init__(self, input_dim, hidden_dim, num_classes):
        self.w1 = nn.Linear(input_dim, hidden_dim)   # First layer
        self.w2 = nn.Linear(hidden_dim, num_classes)  # Second layer

    def forward(self, x):
        hidden = self.w1(x)   # Learned feature map
        logits = self.w2(hidden)  # Linear predictor
        return logits
```

**Claim:** This is actually the same as training a linear classifier.

**Why?** Matrix multiplication is associative:
$$(x W_1) W_2 = x (W_1 W_2) = x W$$

A composition of linear functions is still linear. Collapsing $W_1 W_2$ into a single matrix $W$ gives us back a linear classifier.

**So how do we actually go beyond linear classifiers?**

---

## Section: Multi-Layer Perceptron — With Non-Linearity (`multi_layer_perceptron`)

**Problem:** linear networks aren't more expressive (though they are useful for studying training dynamics).

**Solution:** add a non-linear **activation function**.

### Activation Functions
There are many choices: sigmoid, tanh, ReLU, GeLU, Swish, etc.

We will use the **Rectified Linear Unit (ReLU)** for simplicity:

$$\text{ReLU}(x) = \max(0, x)$$

Plot: flat at 0 for x ≤ 0, identity for x > 0.

**Where does the name "multi-layer perceptron" come from?**
- Perceptrons came from Frank Rosenblatt's 1958 paper (linear classifier)
- 1970s: multi-layer perceptrons (neural networks)

### MLP Architecture
```python
class MultiLayerPerceptron(nn.Module):
    def __init__(self, input_dim, hidden_dim, num_classes):
        self.w1 = nn.Linear(input_dim, hidden_dim)
        self.w2 = nn.Linear(hidden_dim, num_classes)

    def forward(self, x):
        x_transformed = self.w1(x)
        hidden = relu(x_transformed)    # ← Non-linearity!
        logits = self.w2(hidden)
        return logits
```

**Flow:** x → [linear] → [ReLU] → hidden → [linear] → logits

### Terminology
- Activations = hidden units = neurons

### Caution: Dead Neurons
ReLU has zero gradient when x ≤ 0; can result in "dead neurons" that never activate.

**Fix:** Use activation functions that don't have (near-)zero gradients: Leaky ReLU, GeLU, Swish, etc.

Balance tradeoff between **linear** (better gradients) and **non-linear** (better expressivity).

---

## Section: Deep Neural Networks (`deep_neural_networks`)

**Problem:** a single MLP layer might not be expressive enough.

**Solution:** stack multiple MLP layers.

(Image: `more-layers.png` — a two-panel meme cartoon contrasting research styles. Top panel, "STATISTICAL LEARNING": a stick figure points at an x–y loss graph with a jagged green line and says "Gentlemen, our learner overgeneralizes because the VC-Dimension of our Kernel is too high. Get some experts and minimize the structural risk in a new one. Rework our loss function, make the next kernel stable, unbiased and consider using a soft margin." Bottom panel, "NEURAL NETWORKS": a stick figure whose head is a scribbled tangle of lines says "STACK MORE LAYERS", next to a graph of LAYERS vs performance rising from bottom-left to top-right. The joke: the statistical side elaborates kernels while the neural side just stacks layers.)

Intuition: each layer learns more abstract features of the input.
- Early layers: edges, textures
- Middle layers: parts, patterns
- Later layers: objects, concepts

(Diagram: `feature-hierarchy.png` — a vertical four-level hierarchy with blue double-headed arrows between levels. Bottom to top: "Pixels" — a 4×4 tile of colored dots; "1st layer 'Edges'" — a grid of patches showing striped and blob-like patterns; "2nd layer 'Object parts'" — a grid of patches each showing a partial face part (an eye, an ear, a mouth, a nose); "3rd layer 'Objects'" — a grid of complete blurry front-facing faces. Each level is a wider grid than the one below it: features grow more abstract from pixels to edges to object parts to whole objects.)

### Deep Neural Network Architecture
```python
class DeepNeuralNetwork(nn.Module):
    def __init__(self, input_dim, hidden_dim, num_classes):
        self.w1 = nn.Linear(input_dim, hidden_dim)
        self.w2 = nn.Linear(hidden_dim, hidden_dim)
        self.w3 = nn.Linear(hidden_dim, num_classes)

    def forward(self, x):
        x = relu(self.w1(x))
        x = relu(self.w2(x))
        x = self.w3(x)
        return x
```

**Observation:** Training is slower with more layers, especially in the beginning.

---

## Section: Vanishing/Exploding Gradient Problem (`vanishing_exploding_gradient_problem`)

Historically, it has been extremely hard to train deep neural networks — due to the **vanishing/exploding gradient problem**.

### Vanishing Gradient
```python
x = torch.tensor(1.)
w = torch.tensor(0.5, requires_grad=True)
for layer in range(20):
    x = x * w  # Repeated multiplication by 0.5
x.backward()  # w.grad is tiny!
```

Chaining 20 multiplications by 0.5 → gradient = $0.5^{19} \approx 1.9 \times 10^{-6}$.

### Exploding Gradient
```python
x = torch.tensor(1.)
w = torch.tensor(2., requires_grad=True)
for layer in range(20):
    x = x * w  # Repeated multiplication by 2
x.backward()  # w.grad is huge!
```

Chaining 20 multiplications by 2 → gradient = $2^{19} \cdot 20 \approx 10$ million.

**Ideally:** you want w close to 1 for stability. For matrices: want eigenvalues of W to be close to 1.

---

## Section: Residual Connections (`residual_connections`)

Training deep neural networks is challenging because of vanishing gradients.

**Solution:** residual connections (skip connections, highway networks).

Idea appears in many places:
- McCulloch/Pitts 1943, Rosenblatt 1961
- LSTMs for sequence modeling (1997)
- Residual networks (ResNets) for computer vision (2015)

### How They Work
- **No residual:** each layer computes $x \to f(x)$
- **With residual:** each layer computes $x \to x + f(x)$

For $f(x) = wx$, each layer computes $x \to (1 + w)x$, which keeps the multiplier away from zero (though it can still explode if w is large).

### DNN with Residual Connections
```python
class DNNWithResidual(nn.Module):
    def forward(self, x):
        x = relu(self.w1(x))
        x = x + relu(self.w2(x))  # ← Residual connection
        x = self.w3(x)
        return x
```

**Result:** Training is much faster with residual connections.

---

## Section: Layer Normalization (`layer_normalization`)

**Motivation:** don't want the magnitude of activations to grow too big or small.

**Solution:** layer normalization (also see batch normalization [Ba, Kiros, Hinton 2016]).

### Basic Idea
```python
def layernorm(x):
    mean = x.mean()
    var = x.var()
    y = (x - mean) / torch.sqrt(var)
    return y
```

After normalization: `x = [1, 2, 3]` → `y = [-1, 0, 1]`, and `x = [100, 200, 300]` → same output `[-1, 0, 1]`.

### Real LayerNorm Adds Three Things

$$\text{LayerNorm}(x) = \gamma \cdot \frac{x - \mu}{\sqrt{\sigma^2 + \epsilon}} + \beta$$

- **$\epsilon$ (epsilon):** small constant to prevent division by zero ($10^{-5}$)
- **$\gamma$ (gamma):** learnable scaling parameters (initialized to 1)
- **$\beta$ (beta):** learnable shifting parameters (initialized to 0)

In PyTorch: `nn.LayerNorm(normalized_shape)`.

**Summary:** layer normalization keeps the magnitude of activations away from zero and infinity.

---

## Section: Initialization (`initialization`)

We have seen that the magnitude of activations can grow too big or small. We can avoid this by using proper initialization.

### The Problem
```python
input_dim = 16384
output_dim = 32
w = torch.randn(input_dim, output_dim)
x = torch.randn(input_dim)
y = x @ w  # Each element scales as sqrt(input_dim) ≈ 128
```

Large values can cause gradients to blow up and training to be unstable.

### The Solution
We want an initialization that is **invariant to input_dim**. Rescale by $1/\sqrt{\text{input\_dim}}$:

```python
w = torch.randn(input_dim, output_dim) / np.sqrt(input_dim)
```

Now each element of y stays constant regardless of input dimension.

Up to a constant, this is **Xavier initialization** [Glorot and Bengio 2010].

### Truncated Normal
To be extra safe, truncate the normal distribution to [−3, 3] to avoid any chance of outliers:

```python
w = nn.init.trunc_normal_(
    torch.empty(input_dim, output_dim),
    std=1 / np.sqrt(input_dim),
    a=-3, b=3
)
```

---

## Section: Optimizers (`optimizers`)

So far, we've used gradient descent (GD). Each gradient requires summing over **all** training examples. For large datasets, this is too much work to make just one update.

### Stochastic Gradient Descent (SGD)
Instead, we can use a **stochastic optimizer**. Each step, choose a random subset (mini-batch) of the training examples. This is an **unbiased estimate** of the gradient.

```python
grads = torch.tensor([[1., 2], [3, 4], [5, 6], [7, 8]])
batch_size = 2
indices = torch.randint(0, grads.shape[0], (batch_size,))
stochastic_grads = grads[indices]
expected_grad = torch.mean(stochastic_grads, axis=0)
# Expected value equals the true gradient
```

In practice, we **permute** the training examples each epoch and take consecutive chunks:
```python
random_perm = torch.randperm(grads.shape[0])
batches = [random_perm[i:i + batch_size] for i in range(0, len(random_perm), batch_size)]
```

**Fancier optimizer:** use **Adam** instead of SGD (adaptive learning rates per parameter, momentum).

---

## Lecture Summary (`main`)

- **PyTorch:** NumPy + automatic differentiation + pre-defined modules
- **More (non-linear) layers = more expressivity**
- **Don't vanish/explode:** choose activation functions to avoid dead neurons
- **Don't vanish/explode:** use residual connections
- **Don't vanish/explode:** use layer normalization
- **Don't vanish/explode:** use proper initialization
- **Don't vanish/explode:** use better optimizers (Adam)
