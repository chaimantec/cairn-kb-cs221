---
title: Lecture 3 — Learning II (Slides)
lecture: 3
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_file: https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/main/linear_classification.py
rendered: https://stanford-cs221.github.io/autumn2025-lectures/?trace=linear_classification
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. This file transcribes the
  content of `linear_classification.py`. There are no traditional slide numbers;
  sections correspond to function calls in the source.
---

# Lecture 3 Slides — Learning II

Lecture 3 covers linear classification through a single executable Python file: `linear_classification.py`.

---

## Section: Prediction Task (`prediction_task`)

**Last unit: linear regression**
- Prediction task (regression): input → output: real number
- Hypothesis class: linear functions

**This unit: linear classification**
- Prediction task: input → output (class, label): one of K discrete choices
- Hypothesis class: (thresholded) linear functions

### Example Task: Image Classification
- **Input:** an image
- **Output:** what kind of object it is (e.g., cat)

### Example Task: Sentiment Classification
- **Input:** a document
- **Output:** the sentiment of the document (e.g., positive)

### What's the type of the input?
- Image: width × height × 3 (RGB) tensor
- Text: a string (not a tensor... this is addressed later)

### What's the type of the output?
- Binary classification (two choices): usually {−1, 1}
- Multiclass classification (K choices): usually {0, 1, ..., K−1}

A **predictor** is a function that takes an input and produces a predicted output.

Example predictor for binary classification:
```python
def simple_binary_classifier(x: np.ndarray) -> int:
    logit = x[0] - x[1] - 1
    if logit > 0:
        predicted_y = 1
    else:
        predicted_y = -1
    return predicted_y
```

Given inputs `x = [1, 2]` → predicts −1; `x = [2, 0]` → predicts 1.

The points where `logit = x[0] − x[1] − 1 = 0` form the **decision boundary**.

Plot: decision boundary is a line in 2D input space, dividing positive and negative predictions.

**But how do we get the predictor?**

---

## Section: Machine Learning Problem (`machine_learning_problem`)

The **training data** is a set of examples that demonstrate the task.

Each **example** consists of an (input x, target output y) pair.

Training data used:
- `x = [1, 2]`, `target_y = -1`
- `x = [2, 0]`, `target_y = 1`
- `x = [0, 0]`, `target_y = -1`

A **learning algorithm** takes the training data and produces a predictor.

**Key questions:**
1. Which predictors are possible? → **hypothesis class**
2. How good is a predictor? → **loss function**
3. How do we compute the best predictor? → **optimization algorithm**

---

## Section: Hypothesis Class (`hypothesis_class`)

Which predictors (classifiers) are possible?

As before, we parameterize our predictors. For linear classifiers, each set of parameters has a **weight vector** and a **bias**.

```python
params = Parameters(weight=np.array([1, -1]), bias=-1)
x = np.array([1, 1])
predicted_y = binary_classifier(params, x)  # → 1
```

Decision boundary: `weight · x + bias = 0`, i.e., `x₀ − x₁ − 1 = 0`.

Another predictor: `weight = [1, −1]`, `bias = 1` → boundary: `x₀ − x₁ + 1 = 0`.

The **hypothesis class** is the set of all predictors you can get by choosing parameters (weight, bias). The decision boundaries are any "straight-line cuts" of the input space.

Binary classifier implementation:
```python
def binary_classifier(params: Parameters, x: np.ndarray) -> float:
    logit = params.weight @ x + params.bias
    if logit > 0:
        predicted_y = 1
    else:
        predicted_y = -1
    return predicted_y
```

---

## Section: Zero-One Loss Function (`zero_one_loss_function`)

The next design decision is how to judge each of the infinitely many possible predictors.

Recall that for regression, we used the **squared loss**. Intuition: how far away the prediction is from the target.
- Squared loss: `(predicted − target)²`
- This loss is okay (is 0 when predicted = target), but we're classifying, not precise values...

For binary classification, we use the **zero-one loss**.
- Intuition: whether the prediction has the same sign as the target.

Rewriting in terms of the **margin**:

```python
def zero_one_loss(example, params):
    logit = example.x @ params.weight + params.bias
    margin = logit * example.target_y  # sign: correct (+) or not (−)
    loss = int(margin <= 0)  # 1 if wrong, 0 if right
    return loss
```

Zero-one loss plot: step function — 0 when margin > 0, 1 when margin ≤ 0.

The **training loss** is the average of the per-example losses of the training examples (the error rate).

**Summary:**
- **Logit:** the raw score from the linear model (sign is prediction, magnitude is confidence)
- **Margin (logit × target):** sign is whether the prediction is correct or not
- **Zero-one loss:** 1 if wrong, 0 if right
- **Train loss:** average over training examples (error rate)

---

## Section: Zero-One Loss Optimization (`zero_one_loss_optimization`)

Recall that for every set of parameters, we can compute the training loss.

In linear regression we optimized the parameters using gradient descent. Let's do the same here.

We want to find the parameters that yield the lowest training loss — an optimization problem.

**Taking the gradient of the training loss:**
```python
def gradient_zero_one_loss(example, params):
    logit = example.x @ params.weight + params.bias
    margin = logit * example.target_y
    # Zero everywhere except when margin = 0, where it's undefined
    return Parameters(weight=np.zeros_like(params.weight), bias=0)
```

**Problem: the gradient is zero everywhere!**

The zero-one loss is a step function — flat everywhere with a discontinuity at zero. Gradient descent won't update the parameters at all.

Intuition: if an example is wrong, moving parameters a tiny bit won't make it right, so the gradient gives up.

**So what do we do?**

---

## Section: Logistic Function (`logistic_function`)

A logit is a number between −∞ and +∞. We want to convert a logit into a probability (must be between 0 and 1).

There are many functions that do this, but the **logistic function** is a standard choice:

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

Plot: S-shaped curve (sigmoid), mapping (−∞, +∞) → (0, 1).

### Another Interpretation: Log Odds
- `prob = 0.2` → `odds = prob / (1 − prob) = 0.25` → `logit = log(odds) = log(0.25)`
- Applying logistic to the logit recovers the probability: $\sigma(\log(\frac{p}{1-p})) = p$

### Properties
- As logit → −∞, prob → 0
- As logit → +∞, prob → 1
- As logit → 0, prob → 0.5
- $\sigma(z) + \sigma(-z) = 1$

### Derivative
$$\frac{d}{dz}\sigma(z) = \sigma(z)(1 - \sigma(z))$$

The derivative is simple and elegant. As |logit| → ∞, gradient → 0 (saturation).

---

## Section: Logistic Loss Function (`logistic_loss_function`)

To solve the zero gradient problem, we have to rethink the loss function — and actually, even what our classifier outputs.

**So far:** predictor turns a logit into a single prediction. Thresholding is a very discrete operation.

**Instead:** have the classifier output a **probability distribution** (continuous) over labels. The key is the logistic function. The logistic function was used in statistics in **logistic regression** [Berkson, 1944].

Computing probabilities of labels:
- `logit = x · w + b`
- $p(y = 1 \mid x) = \sigma(\text{logit})$
- $p(y = -1 \mid x) = \sigma(-\text{logit})$
- $p(y = \text{target}_y \mid x) = \sigma(\text{margin})$ where `margin = logit × target_y`

**Maximum likelihood principle:** maximize the log probability of the training targets.

For multiple examples, multiply probabilities: $p(y_1|x_1) \cdot p(y_2|x_2)$. Equivalent to summing log probabilities: $\log p(y_1|x_1) + \log p(y_2|x_2)$.

To turn this into a loss, negate it (maximize likelihood = minimize loss):

$$\text{loss} = -\log \sigma(\text{margin})$$

```python
def logistic_loss(example, params):
    logit = example.x @ params.weight + params.bias
    margin = logit * example.target_y
    prob_target = logistic(margin)
    loss = -np.log(prob_target)
    return loss
```

**Comparison of loss functions:**
- Zero-one loss: sharp cliff at margin = 0, flat everywhere else → zero gradient
- Logistic loss: smooth, goes to 0 when margin grows large, non-zero gradient everywhere → gradient descent can work

The training loss is the average of the per-example logistic losses.

---

## Section: Logistic Loss Optimization (`logistic_loss_optimization`)

Now we are ready to optimize the logistic loss.

**Gradient of logistic loss for one example:**

```python
def gradient_logistic_loss(example, params):
    logit = example.x @ params.weight + params.bias
    margin = logit * example.target_y
    loss = -np.log(logistic(margin))
    grad_logit = -logistic(-margin)
    grad_weight = example.target_y * example.x * grad_logit
    grad_bias = example.target_y * grad_logit
    return Parameters(weight=grad_weight, bias=grad_bias)
```

The gradient of the training loss is the average of the gradients of the examples.

**Gradient descent loop:**
```python
params = Parameters(weight=np.array([0, 0]), bias=0)
learning_rate = 1
for step in range(20):
    train_loss = train_logistic_loss(params, training_data)
    grad = gradient_train_logistic_loss(params, training_data)
    params.weight -= learning_rate * grad.weight
    params.bias -= learning_rate * grad.bias
```

The learning curve shows loss decreasing over steps. Final decision boundary correctly separates the training data.

**Key formulas:**

Gradient of logit:
$$\frac{\partial \text{loss}}{\partial \text{logit}} = -\sigma(-\text{margin})$$

Gradient of weight (scalar case):
$$\frac{\partial \text{loss}}{\partial w} = \text{target}_y \cdot x \cdot (-\sigma(-\text{margin}))$$

---

## Section: Multiclass Classification (`multiclass_classification`)

Binary classification: output y ∈ {−1, 1}. Multiclass classification: output y ∈ {0, 1, ..., K−1}.

### Binary (Review)
- Compute a single logit for each input
- Sign of logit is the predicted class
- $p(y = 1) = \sigma(\text{logit})$, $p(y = -1) = 1 - \sigma(\text{logit})$

### Multiclass
- Define a weight vector **for each class**
- Compute a logit **for each class**
- Predict a distribution over classes

```python
params = Parameters(weight=np.array([[1, -1], [1, -1], [0, 2]]), bias=np.array([1, 1, 0]))
x = np.array([2, 0])
logits = params.weight @ x + params.bias  # → [3, 3, 0]
```

**How to turn logits into probabilities?** → Softmax

---

## Section: Softmax (`introduce_softmax`)

Recall: the logistic function maps (−∞, +∞) → (0, 1).

The **softmax function** generalizes this to multiple classes:

$$\text{softmax}(\mathbf{z})_i = \frac{e^{z_i}}{\sum_j e^{z_j}}$$

```python
def softmax(logits):
    exp_logits = np.exp(logits)
    probs = exp_logits / np.sum(exp_logits)
    return probs
```

Example: `logits = [1, -1, 0]` → `probs = [0.58, 0.08, 0.34]`.

**Shifting up logits doesn't change the relative probabilities:** `softmax([1, -1, 0])` = `softmax([3, 1, 2])`. This is why we only need K−1 degrees of freedom for K classes.

---

## Section: Cross Entropy (`introduce_cross_entropy`)

**Cross entropy:** measures the difference between a target distribution and a predicted distribution.

$$H(p, q) = -\sum_i p_i \log q_i$$

Example: `target = [0.5, 0.2, 0.3]`, `predicted = [0.1, 0.5, 0.4]` → cross entropy computed as sum of `target × −log(predicted)`.

Penalized when target puts high probability on an outcome and predicted puts low probability on it.

Cross entropy is minimized when target = predicted. The minimum value is the entropy of the target.

### Special Case: Target is a Single Label (One-Hot)
`target = [0, 1, 0]` (one-hot), `predicted = [0.1, 0.5, 0.4]`

The cross entropy reduces to `−log(predicted[target_class])` — the negative log probability of the target class.

Cross entropy loss for classification:
```python
def cross_entropy_loss(params, example):
    num_classes = len(params.weight)
    logits = [example.x @ params.weight[y] + params.bias[y] for y in range(num_classes)]
    probs = softmax(logits)
    cross_entropy = -np.log(probs[example.target_y])
    return cross_entropy
```

### Summary
- **Softmax:** turn logits into probabilities
- **Cross entropy:** measures difference between target distribution and predicted distribution
- **Cross entropy loss:** generalizes logistic loss, predicted probability of target class

---

## Section: Representing Text (`representing_text`)

Prediction tasks involve text (strings), but machine learning operates on tensors.

**How do we represent a string as a tensor?**
1. **Tokenization:** convert a string into a sequence of integers
2. **Represent** each integer as a one-hot vector

### Tokenization

**Simple tokenization:** Split a string by space into words and convert them into integers.
```python
string = "the cat in the hat"
words = string.split()  # → ["the", "cat", "in", "the", "hat"]
indices = [vocab.get_index(word) for word in words]  # → [0, 1, 2, 0, 3]
```

The `Vocabulary` class maps strings ↔ integers, building the mapping on the fly as new words are encountered.

**Fancier tokenization:** Language models use more sophisticated tokenizers (Byte-Pair Encoding, BPE) [Sennrich et al., 2015]. The GPT-2 tokenizer splits differently — play with it at [tiktokenizer.vercel.app](https://tiktokenizer.vercel.app/?encoder=gpt2).

### One-Hot Representation

Represent each index as a one-hot vector. For a vocabulary of size V, index i is represented as a vector of length V with a 1 at position i and 0 elsewhere.

The string is then represented as a **sequence of vectors**, or a **matrix** of shape (sequence_length × vocab_size).

### Operations

In practice, we store the indices (not the one-hot vectors) to save memory. We can operate directly using the indices.

- Matrix-vector product: `matrix @ w` — equivalent to indexing: `w[indices]`
- This is an **embedding lookup** — each token index retrieves its corresponding row from the weight matrix.

### Bag of Words Representation

Represent each token as a (one-hot) vector. Represent each text as the **average** of the token vectors.

$$\text{BOW}(\text{text}) = \frac{1}{L} \sum_{i=1}^{L} \text{one\_hot}(\text{token}_i)$$

Equivalently with indices: `np.mean(w[indices])`.

**Bag of words:**
- **Pro:** doesn't depend on the length of the text (fixed-dimensional representation)
- **Con:** doesn't pay attention to word order (*dog bites man* = *man bites dog*)

### Summary
- **Problem:** convert strings to tensors for machine learning
- **Solution:** tokenization + one-hot encoding
- **Tokenization:** split strings into words and build up a vocabulary (string ↔ index)
- Mathematically work with one-hot vectors; in code, work with indices

---

## Lecture Summary (`main`)

- **Linear classification:** linear functions → one of K choices
- **Zero-one loss:** leads to zero-gradients almost everywhere
- **Logistic loss:** classifier outputs probabilities, leads to non-zero gradients
- **Multiclass classification:** one logit per class, convert to probabilities with softmax
- **Representing text as tensors:** tokenize + convert tokens to indices (one-hot vectors)
