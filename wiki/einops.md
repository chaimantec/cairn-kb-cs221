---
title: Einops
lectures:
  - 01 — introduced as readable alternative to axis-position gymnastics
  - 02 — reviewed in detail with vector/matrix examples
  - 12 — probability tables as tensors: marginalization, conditioning and Bayesian-network joints as einsums
---

# Einops

Einops is a library for manipulating tensors where **dimensions are named** rather than referenced by position. It makes tensor code self-documenting and eliminates the confusion of cryptic axis indices like `-2, -1`. Liang introduces it in [Lecture 1](01-course-overview.md) ([≈54:18](../raw/transcripts/01-course-overview.md)) and gives a full review in [Lecture 2](02-learning-i.md).

The library is inspired by (but not identical to) Einstein summation notation (Einstein, 1916). Key functions: `einsum`, `reduce`, `rearrange`.

## Axis Naming

Name axes based on what they represent — like choosing variable names in code ([≈1:38](../raw/transcripts/02-learning-i.md)):

| Dimension | Name |
|---|---|
| Batch index | `batch` |
| Sequence position | `seq` |
| Hidden/feature dimension | `hidden` or `feature` |
| Attention heads | `heads` |

## `einsum` — Generalized Matrix Multiplication

`einsum` is the workhorse: it multiplies and sums over named axes ([≈56:37](../raw/transcripts/01-course-overview.md)).

**Syntax:** `einsum(tensor1, tensor2, "axes1, axes2 -> output_axes")`

### Vector Operations

| Operation | Pattern | Example |
|---|---|---|
| Identity | `"i -> i"` | Copy vector |
| Sum | `"i ->"` | Sum all elements |
| Element-wise product | `"i, i -> i"` | Multiply corresponding elements |
| Dot product | `"i, i ->"` | Inner product → scalar |
| Outer product | `"i, j -> i j"` | Every pair → matrix |

### Matrix Operations

| Operation | Pattern |
|---|---|
| Transpose | `"i j -> j i"` |
| Matrix-vector | `"i j, j -> i"` |
| m · mᵀ | `"i k, j k -> i j"` |
| mᵀ · m | `"k i, k j -> i j"` |

### Batched Operations

```python
# Attention: for each batch item, seq1 × seq2 similarity
einsum(x, y, "batch seq1 hidden, batch seq2 hidden -> batch seq1 seq2")
```

With ellipsis for unknown batching dimensions:
```python
einsum(x, y, "... seq1 hidden, ... seq2 hidden -> ... seq1 seq2")
```

### How It Works

For every assignment to all input axes: multiply the corresponding elements from each input tensor, then accumulate into the output entry identified by the output axes. **Axes that appear in the inputs but not in the outputs are summed over** ([≈58:10](../raw/transcripts/01-course-overview.md)). "It's all just additions and multiplications with bookkeeping" ([≈16:26](../raw/transcripts/02-learning-i.md)).

## `reduce` — Sum/Min/Max over Axes

```python
# Sum over the hidden dimension
reduce(x, "... hidden -> ...", "sum")

# Max over rows
reduce(m, "row col -> row", "max")
```

Any axis not named in the output is collapsed by the specified operation ([≈1:01:17](../raw/transcripts/01-course-overview.md)).

## `rearrange` — Split and Merge Dimensions

Essential for multi-head attention in Transformers. A dimension can represent two logical dimensions flattened together ([≈1:02:03](../raw/transcripts/01-course-overview.md)):

```python
# Unflatten: (seq, heads*hidden1) → (seq, heads, hidden1)
x = rearrange(x, "... (heads hidden1) -> ... heads hidden1", heads=2)

# Operate on heads dimension...
x = einsum(x, w, "... hidden1, hidden1 hidden2 -> ... hidden2")

# Flatten back: (seq, heads, hidden2) → (seq, heads*hidden2)
x = rearrange(x, "... heads hidden2 -> ... (heads hidden2)")
```

## Why Use Einops?

- **Readability:** Named axes tell you what's happening at a glance — like type hints for tensor dimensions ([≈58:57](../raw/transcripts/01-course-overview.md)).
- **No transposes:** You never need `.transpose(-2, -1)` — just rename the axes in the output ([≈8:33](../raw/transcripts/02-learning-i.md)).
- **Easier debugging:** The string pattern acts as a shape check.

## Probability Tables Are Tensors (Lecture 12)

Einops comes back in [Lecture 12](12-bayesian-networks-i.md): a probability table over $k$ variables is a $k$-axis tensor, so "all the laws of probability are essentially all covered by einops" ([≈10:57](../raw/transcripts/12-bayesian-networks-i.md)). Marginalization leaves an axis off the output, as in `einsum(P_SR, "s r -> s")` for $P(S = s) = \sum_r P(S = s, R = r)$ ([≈11:45](../raw/transcripts/12-bayesian-networks-i.md)). Conditioning multiplies in an indicator tensor such as `[0, 1]` for the evidence, then divides by the probability of the evidence, itself an einsum with no output axes (`"s ->"`) ([≈16:24](../raw/transcripts/12-bayesian-networks-i.md)–[≈17:58](../raw/transcripts/12-bayesian-networks-i.md)). A [Bayesian network](bayesian-networks.md)'s joint distribution is one einsum over its local tables, `"b, e, b e a -> b e a"` for the alarm network ([≈33:32](../raw/transcripts/12-bayesian-networks-i.md)). See [probabilistic inference](probabilistic-inference.md).

## Resources

- [Lecture 1 transcript](../raw/transcripts/01-course-overview.md) — introduction (~54:18–1:05:58)
- [Lecture 2 transcript](../raw/transcripts/02-learning-i.md) — detailed review (~0:05–17:12)
- [Lecture 12 transcript](../raw/transcripts/12-bayesian-networks-i.md) — einsum for probability (~10:57–18:44, 32:44–41:17)
- [Einops tutorial](https://einops.rocks/1-einops-basics/)
