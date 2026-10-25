---
title: Tensors
lectures:
  - 01 — introduced as atoms of modern ML
  - 02 — einsum review, tensor operations for backprop
---

# Tensors

Tensors are the **atoms of modern machine learning** — multi-dimensional arrays that generalize scalars, vectors, and matrices to arbitrary numbers of dimensions. In CS221, everything is a tensor: data points, model parameters, gradients, and intermediate activations.

Percy Liang introduces tensors in [Lecture 1](01-course-overview.md) ([≈38:03](../raw/transcripts/01-course-overview.md)) and reviews tensor operations with einops in [Lecture 2](02-learning-i.md).

## Tensor Order (Rank)

| Order | Name | Example | Shape |
|---|---|---|---|
| 0 | Scalar | `42` | `()` |
| 1 | Vector | `[1, 2, 3]` | `(3,)` |
| 2 | Matrix | `[[1,2,3],[4,5,6]]` | `(2, 3)` |
| 3+ | Tensor | 3D array | `(2, 2, 3)` |

Liang prefers "order" over "rank" to avoid confusion with the rank of a matrix in linear algebra ([≈0:52](../raw/transcripts/02-learning-i.md)).

## Common ML Tensor Shapes

| Domain | Shape | Meaning |
|---|---|---|
| Data point | `(D,)` | D-dimensional feature vector |
| Batch | `(N, D)` | N examples, each D-dimensional |
| Sequence (language) | `(N, L, D)` | N sequences, length L, D-dim per position |
| Images (vision) | `(N, H, W, C)` | N images, height × width × channels |
| Weight matrix | `(D_in, D_out)` | Linear transformation |

From [Lecture 1 slides](../raw/slides/01-course-overview.md#section-tensor-examples-in-ml).

## Creating Tensors

```python
import numpy as np

# Structured creation
np.zeros((2, 3))          # all zeros
np.ones((2, 3))           # all ones
np.random.randn(2, 3)     # Gaussian random
np.eye(3)                 # identity matrix
np.diag([1, 2, 3])        # diagonal matrix

# From disk
np.save("var/x.npy", x)
x = np.load("var/x.npy")
```

## Views vs. Copies

Operations like `x[0]`, `x[:, 1]`, and `x.transpose(1, 0)` return **views** — they point to the same underlying storage. Mutating a view mutates the original. Be careful with mutation ([≈44:59](../raw/transcripts/01-course-overview.md)).

## Element-wise Operations

```python
np.power(x, 2)     # square each element
np.sqrt(x)          # square root each element
x + x               # add same-shaped tensors
x * 3               # multiply by scalar
```

## Matrix Multiplication (`@`)

The bread and butter of deep learning:

```python
x @ w    # (M, K) @ (K, N) → (M, N)
```

**Batched:** When `x` has shape `(B, M, K)` and `w` has shape `(K, N)`, `w` is **broadcast** — copied conceptually for each of the `B` batch items — producing `(B, M, N)` ([≈48:52](../raw/transcripts/01-course-overview.md)).

## Why Tensors Are Efficient

NumPy tensor operations are written in C and heavily optimized. The same operation as a Python triple-loop is orders of magnitude slower. On GPUs, the gap widens further. The principle: **express computations using few tensor operations** ([≈52:01](../raw/transcripts/01-course-overview.md)). See also [einops](einops.md) for readable tensor manipulation.

## Model Parameters as Tensors

The parameters of any neural network are just a collection of tensors. For example, DeepSeek V3 has 670 billion parameters — a set of large matrices like `(7168, 16384)` stored as tensors ([≈44:13](../raw/transcripts/01-course-overview.md)).

## Language Models as Tensors (Lecture 17)

[Lecture 17](17-language-models.md) describes a [language model](language-models.md) entirely by its tensor shapes, with $T$ the sequence length, $D$ the embedding dimension, $V$ the vocabulary size and $B$ the batch size ([slides 22–33](../raw/slides/17-language-models.md)). Token IDs have shape $(T,)$; embedding each ID as a $D$-dimensional vector gives $(T, D)$; predicting a distribution over the vocabulary at every position gives $(T, V)$. With a batch of $B$ sequences the shapes become $(B, T)$, $(B, T, D)$ and $(B, T, V)$, "standard batching" from homework 2 ([≈14:41](../raw/transcripts/17-language-models.md)). The same lecture shows a real model's weights as named tensors with their shapes, such as an embedding matrix of shape [151 936, 4 096] ([slide 9](../raw/slides/17-language-models.md)): "all of this for some giant matrices of numbers."

## Resources

- [Lecture 1 transcript](../raw/transcripts/01-course-overview.md) — introduction to tensors (~38:03–1:05:58)
- [Lecture 1 slides](../raw/slides/01-course-overview.md#part-3-tensors-tensorspy)
- [Lecture 2 transcript](../raw/transcripts/02-learning-i.md) — einops review (~0:05–17:12)
- [Einops tutorial](https://einops.rocks/1-einops-basics/)
