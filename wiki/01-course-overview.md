---
title: Course Overview and AI Foundations
lecture: 1
video: https://www.youtube.com/watch?v=yaLEGZuIIgE
slides: ../raw/slides/01-course-overview.md
transcript: ../raw/transcripts/01-course-overview.md
---

# Lecture 1 — Course Overview and AI Foundations

Percy Liang opens CS221 by defining what AI is — not as a single technology, but as the intersection of four capabilities an intelligent agent must have: **perception**, **reasoning**, **action**, and **learning** — all operating under resource constraints. He then traces the history of AI through three intellectual traditions (symbolic, neural, and statistical AI), and concludes with a hands-on introduction to **tensors** as the atomic data structure of modern machine learning.

## What is AI?

AI stands for artificial intelligence. The "artificial" part is straightforward — it runs on a computer or robot. The "intelligence" part is the subject of millennia of debate. Rather than defining intelligence in human terms, Liang proposes a definition from general principles: intelligence is what an agent does ([≈2:27](../raw/transcripts/01-course-overview.md)).

### The Four Ingredients of Intelligence

An intelligent agent — whether human or machine — must be able to do four things:

1. **Perceive** — process raw inputs from the world and turn them into understanding. Includes visual scene understanding, speech recognition, and natural language understanding ([≈3:14](../raw/transcripts/01-course-overview.md)).

2. **Reason** — use knowledge and percepts to draw inferences. Techniques covered in this course include uniform cost search, [value iteration](value-iteration.md) (for MDPs), minimax (for adversarial games), and probabilistic inference in Bayesian networks ([≈4:00](../raw/transcripts/01-course-overview.md)).

3. **Act** — output actions that affect the world: generate text, synthesize speech, control a robot ([≈5:32](../raw/transcripts/01-course-overview.md)).

4. **Learn** — update beliefs or state based on experience. Methods include [gradient descent](gradient-descent.md), Q-learning, and expectation maximization ([≈6:20](../raw/transcripts/01-course-overview.md)).

These four capabilities must operate under **resource constraints** — limited computation (time, memory, communication) and limited information (data, experience, available inputs). The challenge is doing something sensible with what you have ([≈7:07](../raw/transcripts/01-course-overview.md)).

### Goals and Alignment

An agent encodes values, goals, or utility functions — either explicitly stated or implicitly built in. **Alignment** is the problem of making sure the agent's values correspond to what the developer actually wants. The canonical example: OpenAI wants ChatGPT to be informative, avoid hallucinations, and refuse harmful queries. **Misalignment** occurs when, despite best intentions, the AI does something unintended ([≈8:39](../raw/transcripts/01-course-overview.md)).

There is a broader question: what impact do we want AI to have on society? This involves privacy, copyright, jobs, inequality, and geopolitics — a deep **sociotechnical** problem with fundamental tradeoffs between different people's values ([≈10:10](../raw/transcripts/01-course-overview.md)).

## History of AI: Three Traditions

Liang tells the history of AI as the story of three intellectual traditions. The full timeline with citations is in the [slides](../raw/slides/01-course-overview.md#part-2-history-of-ai-historypy); key landmarks below.

### Symbolic AI

- **1950**: Alan Turing asks "Can machines think?" and proposes the imitation game (the Turing test), grounding a philosophical question in objective measurement ([≈17:55](../raw/transcripts/01-course-overview.md)).
- **1956**: John McCarthy organizes the Dartmouth workshop and coins the term "artificial intelligence." Early successes include Samuel's checkers player and Newell & Simon's Logic Theorist ([≈20:13](../raw/transcripts/01-course-overview.md)).
- **1966**: The ALPAC report cuts MT funding → **first AI winter**. Problems: limited computation, limited information ([≈23:16](../raw/transcripts/01-course-overview.md)).
- **1970s–80s**: Expert systems (MYCIN, DENDRAL) encode domain knowledge as rules. First real industrial AI impact, but rules don't handle uncertainty and become too complex → **second AI winter** in 1987 ([≈24:50](../raw/transcripts/01-course-overview.md)).

### Neural AI

- **1943**: McCulloch & Pitts develop the theory of artificial neural networks ([≈26:21](../raw/transcripts/01-course-overview.md)).
- **1949**: Hebbian learning — "cells that fire together, wire together" ([≈27:06](../raw/transcripts/01-course-overview.md)).
- **1969**: Minsky & Papert's *Perceptrons* shows linear models can't represent XOR → kills neural net research ([≈27:52](../raw/transcripts/01-course-overview.md)).
- **1986**: Rumelhart, Hinton & Williams popularize [backpropagation](backpropagation.md) ([≈28:39](../raw/transcripts/01-course-overview.md)).
- **2006–2017**: Deep learning snowballs — Hinton's deep network training (2006), AlexNet (2012), seq2seq (2014), attention (2015), AlphaGo (2016), the Transformer architecture (2017) ([≈29:24](../raw/transcripts/01-course-overview.md)).

### Statistical AI

- Mathematical tools predating AI: linear regression (Gauss, 1801), stochastic gradient descent (1951), Dijkstra's algorithm (1956), [MDPs](markov-decision-processes.md) (Bellman, 1957) ([≈30:10](../raw/transcripts/01-course-overview.md)).
- 1980s–2000s: Bayesian networks, SVMs, variational inference, conditional random fields, topic models — rooted in mathematical principles ([≈30:56](../raw/transcripts/01-course-overview.md)).

### Foundation Models (2018–present)

- Pretrained language models: ELMo, BERT, T5. Then scaling: GPT-2, GPT-3, scaling laws, Llama 3, DeepSeek v3 ([≈31:44](../raw/transcripts/01-course-overview.md)).
- Reasoning models (OpenAI o1–o4, DeepSeek r1) produce "thoughts" before responding ([≈33:19](../raw/transcripts/01-course-overview.md)).
- Industrialization: GPT-4 (~1.8T parameters, ~$100M to train), xAI's Colossus (200K H100s), Stargate ($500B over 4 years). Frontier model details are no longer public ([≈34:08](../raw/transcripts/01-course-overview.md)).

**AI is a melting pot.** Symbolic AI had the vision, neural AI provided the architectures, and statistical AI provides the rigor. This class draws on all three ([≈37:15](../raw/transcripts/01-course-overview.md)).

## Tensors

See also the dedicated [tensors](tensors.md) topic page.

Tensors are the **atoms of modern machine learning** — used to represent data, model parameters, gradients, and intermediate computations ([≈38:03](../raw/transcripts/01-course-overview.md)).

### Tensor Basics

- **Scalar (rank 0):** a single number, shape `()`
- **Vector (rank 1):** 1D array, shape `(D,)`
- **Matrix (rank 2):** 2D array, shape `(M, N)`
- **Higher-rank tensors:** shape `(N, L, D)` for batched sequences, `(N, H, W, C)` for images

Tensors are created with `np.zeros`, `np.ones`, `np.random.randn`, `np.eye`, `np.diag`, or loaded from disk ([≈39:36](../raw/transcripts/01-course-overview.md), [slides](../raw/slides/01-course-overview.md#section-creating-tensors)).

### ML Tensor Shapes

| Domain | Shape | Meaning |
|---|---|---|
| Data point | `(D,)` | D-dimensional feature vector |
| Batch | `(N, D)` | N examples |
| Language | `(N, L, D)` | N sequences of length L, D-dim per position |
| Vision | `(N, H, W, C)` | N images: height × width × channels |
| Weights | `(D_in, D_out)` | Linear transformation matrix |

([≈41:54](../raw/transcripts/01-course-overview.md))

### Operations and Efficiency

- **Views:** `x[0]`, `x[:, 1]`, `x.transpose(1, 0)` — do not copy; mutations affect the original ([≈44:59](../raw/transcripts/01-course-overview.md)).
- **Element-wise:** `np.power`, `np.sqrt`, `+`, `*`, `/` — applied to every element ([≈45:47](../raw/transcripts/01-course-overview.md)).
- **Triangular masking:** `np.triu`, `np.tril` — useful for causal masks in Transformers ([≈46:34](../raw/transcripts/01-course-overview.md)).
- **Matrix multiplication (`@`):** the bread and butter of deep learning. Batched matmul: `x @ w` where `x` is `(B, M, K)` and `w` is `(K, N)` → `(B, M, N)` with `w` broadcast ([≈47:20](../raw/transcripts/01-course-overview.md)).
- **Efficiency:** Tensor ops (NumPy in C) are orders of magnitude faster than Python loops; GPUs accelerate further ([≈52:01](../raw/transcripts/01-course-overview.md)).

### Einops — Named-Axis Tensor Operations

[Einops](einops.md) makes tensor code readable by naming dimensions instead of using opaque position indices like `-2, -1` ([≈54:18](../raw/transcripts/01-course-overview.md)).

**`einsum`** — generalized matrix multiplication:
```python
# Batched attention: for each batch item, compute seq1 × seq2 similarity
einsum(x, y, "batch seq1 hidden, batch seq2 hidden -> batch seq1 seq2")
```
Axes not in the output (e.g., `hidden`) are summed over ([≈56:37](../raw/transcripts/01-course-overview.md)).

**`reduce`** — sum/min/max over named axes ([≈1:00:30](../raw/transcripts/01-course-overview.md)).

**`rearrange`** — split/merge dimensions (essential for multi-head attention) ([≈1:02:03](../raw/transcripts/01-course-overview.md)).

## Course Mechanics

- **Tensor-native:** All computations expressed as tensors, using NumPy and PyTorch ([≈14:04](../raw/transcripts/01-course-overview.md)).
- **Executable lectures:** The lecture slides are Python programs that execute to deliver content ([≈15:38](../raw/transcripts/01-course-overview.md)).
- **Changes:** Constraint satisfaction problems cut; deep dive into societal impact added ([≈14:51](../raw/transcripts/01-course-overview.md)).
- **Philosophy:** Learning by doing — homeworks involve building and coding things up ([≈13:16](../raw/transcripts/01-course-overview.md)).
