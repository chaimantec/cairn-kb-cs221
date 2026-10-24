---
title: Hidden Markov Models — Tracking a Hidden State Through Noisy Readings
---

# Hidden Markov Models

A **hidden Markov model** (HMM) is a [Bayesian network](bayesian-networks.md) with two sequences of variables: hidden states $H_1, H_2, \ldots$, such as an object's position at each time, and observations $E_1, E_2, \ldots$, such as a noisy sensor reading at each time. The hidden states form a chain, each depending on the one before, and each observation depends only on the hidden state at its own time. It is "hidden Markov" because the hidden states form a Markov chain ([lecture 14](14-bayesian-networks-learning.md), [≈38:10](../raw/transcripts/14-bayesian-networks-learning.md)). The course uses HMMs twice: in [lecture 12](12-bayesian-networks-i.md) as a probabilistic program queried by [rejection sampling](rejection-sampling.md), and in lecture 14 as the main example of learning with [parameter sharing](parameter-sharing.md).

Notation: $H_t$ is the hidden state at time $t$ and $E_t$ the observation (also called evidence or emission) at time $t$. In lecture 14, $p_{\text{start}}$, $p_{\text{trans}}$ and $p_{\text{emit}}$ are the model's three local conditional distributions.

HMMs "used to be used in speech recognition and computational biology," and can be used for object tracking ([≈38:10](../raw/transcripts/14-bayesian-networks-learning.md)). Lecture 1's history of AI records the moment they were displaced in speech: in 2009 neural networks beat hidden Markov models in speech recognition, which "transformed speech community" ([slides](../raw/slides/01-course-overview.md)).

## The Structure

Both lectures draw the same shape: a chain $H_1 \to H_2 \to H_3 \to \cdots$ along the top, and an arrow from each $H_t$ straight down to its $E_t$. Lecture 12's figure runs to $H_5$ and shades the $E_t$ to mark them observed; lecture 14's shows three steps with the chain cut off at the edge ([slides 12](../raw/slides/12-bayesian-networks-i.md), [slides 14](../raw/slides/14-bayesian-networks-learning.md)). As in any Bayesian network, the joint distribution is each node given its parents. For three steps, in lecture 14's notation ([≈38:57](../raw/transcripts/14-bayesian-networks-learning.md), [≈39:42](../raw/transcripts/14-bayesian-networks-learning.md)):

$$P(H = h, E = e) = p_{\text{start}}(h_1) \thinspace p_{\text{emit}}(e_1 \mid h_1) \thinspace p_{\text{trans}}(h_2 \mid h_1) \thinspace p_{\text{emit}}(e_2 \mid h_2) \thinspace p_{\text{trans}}(h_3 \mid h_2) \thinspace p_{\text{emit}}(e_3 \mid h_3)$$

(Lecture 14's slide, as taught, prints the first emission factor as $p_E(e_1 \mid h_1)$; the course later corrected it to $p_{\text{emit}}$.)

There are only **three tables**, whatever the length: $p_{\text{start}}$ gives the distribution of $H_1$, $p_{\text{trans}}$ gives each hidden state given the previous one, and $p_{\text{emit}}$ gives each reading given the hidden state at the same time. Every step reuses the same transition and emission tables. That is [parameter sharing](parameter-sharing.md), and it is where the difference between parameters and variables becomes stark: "this is only three steps, but this could be 100 steps... I still only have these three local conditional distributions" ([≈41:15](../raw/transcripts/14-bayesian-networks-learning.md), [≈42:02](../raw/transcripts/14-bayesian-networks-learning.md)).

## Lecture 12: Inference by Sampling

Lecture 12 writes the HMM as a five-step probabilistic program ([≈1:11:33](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:13:59](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). The object starts at position 0 and at each step advances by one with probability 0.5; the sensor reports the true position plus 0 or 1, each with probability 0.5. The query is where the object was at time 3, given that the sensor read 2 at time 5: $P(H_3 \mid E_5 = 2)$. Computing this exactly would be "fairly complicated," but [rejection sampling](rejection-sampling.md) treats the program as a black box. With 200 samples (53 accepted), the estimate is about 0.70 for position 1, 0.23 for position 0 and 0.08 for position 2. The point of the example is how succinct a probabilistic program can be.

## Lecture 14: Learning by Counting

Lecture 14 learns the three tables from data, in the fully observed case where the hidden states are seen in training ([≈40:28](../raw/transcripts/14-bayesian-networks-learning.md)–[≈43:39](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). Here $H_t \in \lbrace 0, 1 \rbrace$ and $E_t \in \lbrace 0, 1 \rbrace$, and the data is two sequences:

| sequence | $H_1$ | $E_1$ | $H_2$ | $E_2$ | $H_3$ | $E_3$ |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 0 | 1 | 1 | 0 | 0 |
| 2 | 0 | 1 | 0 | 1 | 0 | 1 |

Keep one set of counts per table, and walk each sequence: $H_1$ counts toward start, each consecutive pair $(H_{t-1}, H_t)$ toward trans, each pair $(H_t, E_t)$ toward emit. Then normalize each table. The traced estimates:

- $p_{\text{start}}(0) = 1$: both sequences start at 0.
- $p_{\text{trans}}$: from state 0, to 0 with probability $2/3$ and to 1 with $1/3$; from state 1, always back to 0.
- $p_{\text{emit}}$: in state 0, reading 0 with probability 0.4 and 1 with 0.6; in state 1, always reading 1.

The counts are sparse, so the numbers are not very interesting. What matters is that once you have the counts, "normalize doesn't look at the structure of the Bayesian network anymore"; it just normalizes each local conditional distribution ([≈42:48](../raw/transcripts/14-bayesian-networks-learning.md)). The same numbers come out of the general `supervised_learning` function, given a `network_structure` that maps $H_1$ to start, $H_2$ and $H_3$ to trans with the previous state as parent, and each $E_t$ to emit with $H_t$ as parent ([≈46:08](../raw/transcripts/14-bayesian-networks-learning.md)–[≈50:52](../raw/transcripts/14-bayesian-networks-learning.md)). See [maximum likelihood](maximum-likelihood.md) for why counting is the right estimate.

If the hidden states were not observed in training, you could not count them, and learning would need [expectation maximization](expectation-maximization.md). Lecture 14 runs EM on a simpler model (a hidden genre and two ratings), not on the HMM.

## Related Pages

- [Lecture 12](12-bayesian-networks-i.md) — the HMM as a probabilistic program, queried by rejection sampling
- [Lecture 14](14-bayesian-networks-learning.md) — learning the HMM's three tables by count and normalize
- [Parameter sharing](parameter-sharing.md) — why three tables suffice for any length
- [Rejection sampling](rejection-sampling.md) — the inference method lecture 12 uses on it
- [Bayesian networks](bayesian-networks.md) — the general framework
- [Expectation maximization](expectation-maximization.md) — learning when hidden variables are unobserved
