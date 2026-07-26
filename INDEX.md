# CS221 — Artificial Intelligence: Principles and Techniques | Autumn 2025 — Percy Liang

Stanford's introductory AI course. Covers perception, reasoning, action, and learning under resource constraints, drawing on symbolic, neural, and statistical AI traditions. This knowledge base covers lectures 1–2 of 20, with edited transcripts, slide transcriptions, and interlinked wiki pages.

## Lectures

- **[Lecture 1 — Course Overview and AI Foundations](wiki/01-course-overview.md)** — What is AI? The four ingredients of intelligence (perceive, reason, act, learn), resource constraints, alignment and societal goals, history of AI through three traditions (symbolic, neural, statistical), and an introduction to tensors and einops.
- **[Lecture 2 — Learning I](wiki/02-learning-i.md)** — Einops review, gradients and partial derivatives, computation graphs and backpropagation (building a mini-PyTorch), and linear regression as the first complete ML pipeline: hypothesis class → loss function → optimization algorithm.

## Topic Pages

- **[Tensors](wiki/tensors.md)** — Multi-dimensional arrays, the atoms of modern ML. Tensor order, shapes in different domains (vision, language, tabular), creating and viewing tensors, matrix multiplication and broadcasting, and why tensor ops are efficient.
- **[Einops](wiki/einops.md)** — Named-axis tensor operations for readable, self-documenting code. `einsum` for generalized matrix multiplication, `reduce` for axis aggregation, and `rearrange` for splitting/merging dimensions (essential for multi-head attention).
- **[Backpropagation](wiki/backpropagation.md)** — Reverse-mode automatic differentiation. Computation graphs, the chain rule, forward and backward passes, node-level gradient rules (Add, Multiply, Squared), and how PyTorch works under the hood.
- **[Linear Regression](wiki/linear-regression.md)** — The simplest complete ML pipeline. Hypothesis class (parameterized linear functions), squared loss, and training loss minimization via gradient descent. Demonstrates the general ML template.
- **[Gradient Descent](wiki/gradient-descent.md)** — Iterative optimization by moving in the negative gradient direction. Learning rate tradeoffs, convex vs. non-convex convergence, and variants (SGD, Adam).

## Raw Materials

- **[`raw/transcripts/`](raw/transcripts/)** — Edited lecture transcripts with `[MM:SS]` timestamps. The **edited** versions (not the originals in `original/`) are the ones to read — auto-caption errors (names, notation, punctuation) have been corrected while preserving all content and timestamps.
- **[`raw/slides/`](raw/slides/)** — Full text transcription of every lecture's slide content. CS221 uses executable Python lectures rather than PDFs; each slide file documents the sections and content from the source `.py` files.
- **[`sources.md`](sources.md)** — Every course document and external paper with its canonical URL. Slide and handout PDFs are linked (not committed) to keep the repo small — see `sources.md` for provenance.

---

Built with the Cairn KB skill. Lectures 1–2 of 20. See [`TODO.md`](TODO.md) for build progress.
