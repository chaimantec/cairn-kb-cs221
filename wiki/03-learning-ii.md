---
title: Learning II — Linear Classification, Logistic Regression, and Multiclass
lecture: 3
video: https://www.youtube.com/watch?v=Mbe5ICIUw5Q
slides: ../raw/slides/03-learning-ii.md
transcript: ../raw/transcripts/03-learning-ii.md
---

# Lecture 3 — Learning II

This lecture extends the ML pipeline from regression to classification. Liang walks through the same three questions — hypothesis class, loss function, optimization algorithm — but discovers that the natural loss for classification (zero-one) has zero gradients everywhere, forcing a detour through probability, logistic functions, and maximum likelihood. The result is **logistic regression** for binary classification and **softmax + cross entropy** for multiclass. The lecture ends with the practical problem of representing text as tensors.

## Linear Classification — The Prediction Task

In linear regression, the output is a real number. In **linear classification**, the output is one of K discrete choices — a class or label ([≈0:54](../raw/transcripts/03-learning-ii.md)).

**Example tasks:**
- **Image classification:** input = image (width × height × 3 tensor), output = object label (e.g., "cat")
- **Sentiment classification:** input = text document (string → tensor), output = sentiment (positive/negative)

**Output spaces:**
- **Binary classification** (K = 2): y ∈ {−1, +1} (convention in this course)
- **Multiclass classification** (K choices): y ∈ {0, 1, …, K−1}

A **predictor** is any function f: 𝒳 → 𝒴. For binary classification, it computes a **logit** (raw score) and thresholds:

```python
def binary_classifier(params, x):
    logit = params.weight @ x + params.bias
    if logit > 0:
        return 1
    else:
        return -1
```

([≈3:11](../raw/transcripts/03-learning-ii.md))

## The Three Questions (Review)

Every ML problem is specified by answering ([≈7:52](../raw/transcripts/03-learning-ii.md)):

1. **Hypothesis class** — which predictors are possible?
2. **Loss function** — how good is a predictor?
3. **Optimization algorithm** — how do we compute the best predictor?

## 1. Hypothesis Class — Linear Classifiers

For linear classifiers, the parameters are a **weight vector** w and a **bias** b. The hypothesis class is all predictors obtained by varying (w, b):

$$\mathcal{F} = \{f_{\mathbf{w}, b}(\mathbf{x}) = \text{sign}(\mathbf{w} \cdot \mathbf{x} + b) \mid \mathbf{w} \in \mathbb{R}^d, b \in \mathbb{R}\}$$

The **decision boundary** is where the logit is exactly zero: w·x + b = 0. In 2D, this is a line; in higher dimensions, a hyperplane. All decision boundaries are straight-line cuts of the input space — no curves allowed ([≈11:44](../raw/transcripts/03-learning-ii.md)).

**Why linear?** Linearity is a fragile notion, and most real tasks are not linear. But linear classifiers are simple, have few parameters (less overfitting), are convex (unique optimum), and become surprisingly expressive in high dimensions — with 10,000-dimensional input, a linear cut can capture a lot ([≈12:32](../raw/transcripts/03-learning-ii.md)).

## 2. Loss Functions — The Zero-One Problem

### Squared Loss (Review)

For regression: $\text{loss} = (f_{\mathbf{w},b}(\mathbf{x}) - y)^2$. This type-checks for classification (targets are ±1, predictions are numbers), but it's philosophically wrong — it penalizes confident correct predictions more heavily than barely-wrong ones, and the idea of a "residual" doesn't make sense when there are only two output values ([≈16:24](../raw/transcripts/03-learning-ii.md)).

### Zero-One Loss

The natural loss for classification: did we get it right?

$$\text{loss}_{0/1} = \begin{cases} 0 & \text{if prediction = target} \\ 1 & \text{otherwise} \end{cases}$$

Rewritten in terms of the **margin** (logit × target y):

$$\text{margin} = (\mathbf{w} \cdot \mathbf{x} + b) \cdot y$$

$$\text{loss}_{0/1}(\text{margin}) = \mathbf{1}[\text{margin} \leq 0]$$

The sign of the margin tells you if the prediction is correct (+ = correct, − = wrong). The zero-one loss is 1 when margin ≤ 0, and 0 otherwise ([≈21:46](../raw/transcripts/03-learning-ii.md)).

The **training loss** using zero-one is just the **error rate** — the fraction of misclassified examples ([≈24:54](../raw/transcripts/03-learning-ii.md)).

### The Zero-Gradient Problem

The zero-one loss is a step function: flat everywhere with a discontinuity at margin = 0. Its gradient is **zero almost everywhere**. Gradient descent on the zero-one loss literally does nothing — the parameters never update ([≈27:14](../raw/transcripts/03-learning-ii.md)).

Intuition: the gradient is a local notion — "if I tweak the parameters a tiny bit, can I reduce the loss?" With the 0-1 loss, a tiny parameter change can't flip a misclassification to correct, so the gradient gives up ([≈28:49](../raw/transcripts/03-learning-ii.md)).

## The Logistic Fix — Probabilistic Classifiers

The fix requires two conceptual moves:

1. **Output a probability distribution, not a single label.** Probabilities are continuous — small parameter changes cause small probability changes — so gradient descent has signal to work with ([≈31:06](../raw/transcripts/03-learning-ii.md)).
2. **Use a smooth loss that approximates the zero-one loss.**

### Logistic Function

The **logistic function** (sigmoid) maps a logit z ∈ (−∞, +∞) to a probability ∈ (0, 1):

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

Properties ([≈32:39](../raw/transcripts/03-learning-ii.md)):
- σ(0) = 0.5
- σ(z) → 0 as z → −∞; σ(z) → 1 as z → +∞
- σ(z) + σ(−z) = 1
- Derivative: $\frac{d}{dz}\sigma(z) = \sigma(z)(1 - \sigma(z))$ — simple and elegant
- The logistic function is the inverse of the **log-odds** mapping: probability p → odds p/(1−p) → logit log(p/(1−p)) ([≈34:14](../raw/transcripts/03-learning-ii.md))

Historically: used in statistics as **logistic regression** [Berkson, 1944] ([≈38:10](../raw/transcripts/03-learning-ii.md)).

### From Logit to Class Probabilities

Given a logit:
- $p(y = +1 \mid \mathbf{x}) = \sigma(\text{logit})$
- $p(y = -1 \mid \mathbf{x}) = \sigma(-\text{logit}) = 1 - \sigma(\text{logit})$
- $p(y = y_{\text{target}} \mid \mathbf{x}) = \sigma(\text{margin})$ where margin = logit × y_target ([≈38:57](../raw/transcripts/03-learning-ii.md))

### Logistic Loss — Maximum Likelihood

**Maximum likelihood principle:** maximize the probability of the training targets under the model ([≈40:29](../raw/transcripts/03-learning-ii.md); see [maximum likelihood](maximum-likelihood.md), which lecture 14 returns to for Bayesian networks). For multiple examples, multiply probabilities → equivalently, sum log probabilities. To turn into a loss (minimize instead of maximize), negate:

$$\text{loss}_{\text{logistic}}(\mathbf{w}, b; \mathbf{x}, y) = -\log \sigma(y \cdot (\mathbf{w} \cdot \mathbf{x} + b)) = -\log \sigma(\text{margin})$$

The logistic loss is **smooth** and has **non-zero gradient everywhere**: even when you're classifying correctly (margin > 0), the gradient nudges you to increase the probability further ([≈43:34](../raw/transcripts/03-learning-ii.md)).

**Plot comparison:**
- Zero-one loss: step at margin = 0, flat everywhere → zero gradient
- Logistic loss: smooth decay as margin increases, gradient never zero → gradient descent works

Taking the log prevents numerical underflow — probabilities like 10⁻²⁰⁰ are common, but log probabilities stay manageable ([≈45:54](../raw/transcripts/03-learning-ii.md)).

## 3. Optimization — Gradient of Logistic Loss

### Per-Example Gradient

Starting from $\text{loss} = -\log \sigma(\text{margin})$ where margin = (w·x + b)·y:

$$\frac{\partial \text{loss}}{\partial \text{logit}} = -\sigma(-\text{margin})$$

By the chain rule:
- **Weight gradient:** $\frac{\partial \text{loss}}{\partial \mathbf{w}} = y \cdot \mathbf{x} \cdot (-\sigma(-\text{margin}))$
- **Bias gradient:** $\frac{\partial \text{loss}}{\partial b} = y \cdot (-\sigma(-\text{margin}))$

([≈47:26](../raw/transcripts/03-learning-ii.md))

### Training Gradient and Gradient Descent

Average the per-example gradients across all training examples. Then gradient descent:

$$\mathbf{w} := \mathbf{w} - \eta \cdot \frac{\partial \text{TrainLoss}}{\partial \mathbf{w}}$$
$$b := b - \eta \cdot \frac{\partial \text{TrainLoss}}{\partial b}$$

**Key detail:** the gradient is the same shape as the parameters — gradient for weight has the same dimensions as weight ([≈49:44](../raw/transcripts/03-learning-ii.md)).

**Initialization:** For linear classification, initializing weights and bias to zero is fine. Learning rate of 1 works for small examples but is generally too high. Monitor the learning curve — loss should go down. If it doesn't, you have a bug ([≈50:30](../raw/transcripts/03-learning-ii.md)).

The resulting classifier doesn't just barely separate the data — it pushes the decision boundary away from points to maximize the margin and increase probabilities ([≈52:03](../raw/transcripts/03-learning-ii.md)).

## Multiclass Classification

Binary: one logit per input, output ∈ {−1, +1}. Multiclass: K logits per input, output ∈ {0, 1, …, K−1} ([≈53:40](../raw/transcripts/03-learning-ii.md)).

**Architecture:** One weight vector per class. For K classes with d-dimensional input:

$$\mathbf{W} \in \mathbb{R}^{K \times d}, \quad \mathbf{b} \in \mathbb{R}^K$$

$$\text{logits} = \mathbf{W}\mathbf{x} + \mathbf{b}$$

Each class gets its own logit ([≈54:25](../raw/transcripts/03-learning-ii.md)).

### Softmax — From Logits to a Distribution

Applying the logistic function independently to each logit doesn't work because probabilities must sum to 1. The **softmax** generalizes the logistic function to K classes:

$$\text{softmax}(\mathbf{z})_i = \frac{e^{z_i}}{\sum_{j=1}^{K} e^{z_j}}$$

([≈55:57](../raw/transcripts/03-learning-ii.md))

**Intuition:** "The lazy way to make a distribution" — exponentiate (to make non-negative), then divide (to sum to 1) ([≈57:29](../raw/transcripts/03-learning-ii.md)).

**Key property:** Adding a constant to all logits doesn't change the softmax output. This is useful for numerical stability — shift logits to a nice range before exponentiating to avoid overflow ([≈58:16](../raw/transcripts/03-learning-ii.md)).

### Cross Entropy Loss

**Cross entropy** measures the difference between a target distribution p and a predicted distribution q:

$$H(p, q) = -\sum_{i=1}^{K} p_i \log q_i$$

([≈59:49](../raw/transcripts/03-learning-ii.md))

Intuition: penalizes when the target puts high probability on an outcome and the prediction puts low probability on it. Cross entropy is minimized when p = q — the minimum value is the entropy of p.

**Special case — one-hot targets:** When the target is a single class (represented as a one-hot vector), the cross entropy collapses to the negative log probability of the target class:

$$\text{loss}_{\text{cross-entropy}} = -\log(\text{softmax}(\text{logits})_{\text{target\_class}})$$

This generalizes the logistic loss — set K = 2 and you recover binary classification ([≈1:03:41](../raw/transcripts/03-learning-ii.md)).

### Multiclass Summary

| Concept | Binary | Multiclass |
|---|---|---|
| Logit | One scalar per input | K scalars per input (one per class) |
| Probability | σ(logit), 1−σ(logit) | softmax(logits) — K probabilities summing to 1 |
| Loss | −log σ(margin) | −log(softmax(logits)[target]) |
| Gradient | Non-zero, smooth | Non-zero, smooth |

## Representing Text as Tensors

Text input is a string — not a tensor. A two-step pipeline converts it ([≈1:05:57](../raw/transcripts/03-learning-ii.md)):

### Step 1 — Tokenization

Split the string into a sequence of tokens and map each token to an integer index using a **vocabulary**. For "the cat in the hat":
- Split on spaces → ["the", "cat", "in", "the", "hat"]
- Map to indices → [0, 1, 2, 0, 3] (same word = same index)

A `Vocabulary` class maintains bidirectional mapping: string ↔ index, assigning new indices when it encounters unseen tokens ([≈1:06:43](../raw/transcripts/03-learning-ii.md)).

**Beyond naive splitting:** Modern language models use **Byte-Pair Encoding (BPE)** [Sennrich et al., 2015], popularized by GPT-2. BPE automatically determines how to split strings into subword tokens, handling punctuation, compound words, and out-of-vocabulary tokens more gracefully than space-splitting ([≈1:08:16](../raw/transcripts/03-learning-ii.md)). [Lecture 17](17-language-models.md) explains how BPE builds its vocabulary by merging frequent pairs; see [tokenization](tokenization.md).

### Step 2 — One-Hot Encoding

Each token index i is represented as a one-hot vector of length V (vocabulary size) with a 1 at position i. The full text is a matrix of shape (sequence_length × vocab_size) ([≈1:09:03](../raw/transcripts/03-learning-ii.md)).

**In practice:** Store indices, not one-hot vectors, to save memory. Matrix-vector products can be done via direct indexing: `w[indices]` ≡ `one_hot_matrix @ w` — this is an **embedding lookup** ([≈1:09:49](../raw/transcripts/03-learning-ii.md)).

### Bag of Words (BoW)

Average all token one-hot vectors into a single fixed-dimensional vector:

$$\text{BoW}(\text{text}) = \frac{1}{L} \sum_{i=1}^{L} \text{one\_hot}(\text{token}_i)$$

Equivalently with indices: `np.mean(w[indices], axis=0)`.

| Pro | Con |
|---|---|
| Fixed-dimensional regardless of text length | Ignores word order ("dog bites man" = "man bites dog") |

([≈1:10:35](../raw/transcripts/03-learning-ii.md))
