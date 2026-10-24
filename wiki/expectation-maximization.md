---
title: Expectation Maximization — Learning When Some Variables Are Hidden
---

# Expectation Maximization

**Expectation maximization (EM)** learns the parameters of a [Bayesian network](bayesian-networks.md) when some variables are never observed in the training data. It alternates two steps. The **E-step** guesses the hidden variables, as a probability distribution over their values, which turns each example into weighted complete examples. The **M-step** runs count and normalize on those weighted examples. EM is taught in [lecture 14](14-bayesian-networks-learning.md), where the lecturer calls it "one of my favorite algorithms, because it sort of is a bit magical" ([≈1:04:50](../raw/transcripts/14-bayesian-networks-learning.md)). [Lecture 1](01-course-overview.md) lists it, beside gradient descent and Q-learning, as one of the course's learning methods ([slides](../raw/slides/01-course-overview.md)). The slide cites it as Dempster et al., 1977.

Notation, as in the lecture: $G \in \lbrace \text{drama}, \text{comedy} \rbrace$ is a movie's genre, which is hidden; $R_1, R_2 \in \lbrace 1, 2 \rbrace$ are two users' ratings, which are observed; $\theta$ is all the parameters, here $p_G$ and a single rating table $p_R(r \mid g)$ shared by both users.

## The Problem: You Cannot Count What You Cannot See

With fully observed data, learning is count and normalize, the closed-form [maximum likelihood](maximum-likelihood.md) estimate. For the network $R_1 \leftarrow G \to R_2$ with examples (drama, 1, 1) and (comedy, 2, 2), that gives an even split between genres, dramas always rated 1 and comedies always rated 2 ([≈1:04:50](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:05:40](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)).

Now remove the genre, so the data is only the rating pairs $(1, 1)$ and $(2, 2)$. You cannot count $G$, and every table depends on $G$, "so I can't even estimate anything" ([≈1:05:40](../raw/transcripts/14-bayesian-networks-learning.md)).

## Back to the Principle

"The idea of having principles is that you can always go back to your principle and think, what would maximum likelihood do?" It says to maximize the likelihood of the **observed** data, whatever that is. Because the Bayesian network defines the full joint distribution, the unseen variable can be marginalized out ([≈1:06:28](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:07:15](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)):

$$\max_\theta \sum_{(r_1, r_2)} \log P(R_1 = r_1, R_2 = r_2; \theta) = \max_\theta \sum_{(r_1, r_2)} \log \sum_g P(G = g, R_1 = r_1, R_2 = r_2; \theta)$$

with the outer sums running over the training examples. The players: observed variables $R_1$ and $R_2$, the unobserved variable $G$, and the parameters $\theta$. We need to figure out both $G$ and $\theta$.

## A Chicken-and-Egg Problem

If you knew $\theta$, you could compute the distribution over the hidden variable, $P(G = g \mid R_1 = r_1, R_2 = r_2; \theta)$, by [probabilistic inference](probabilistic-inference.md). If you knew $G$, you would be back in the fully observed case and could count and normalize. "Whenever you have a chicken and egg problem, what you typically do is you just bootstrap" ([≈1:08:00](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:08:45](../raw/transcripts/14-bayesian-networks-learning.md)): initialize $\theta$, then iterate.

- **E-step.** For each example, compute the distribution over every value the hidden variable could take, given the observed values and the current $\theta$. Replicate the example once per value, and give each copy that probability as its weight. The output looks like fully observed data, with weights:

$$q(g) = P(G = g \mid R_1 = r_1, R_2 = r_2; \theta) = \frac{p_G(g) \thinspace p_R(r_1 \mid g) \thinspace p_R(r_2 \mid g)}{\sum_{g'} p_G(g') \thinspace p_R(r_1 \mid g') \thinspace p_R(r_2 \mid g')}$$

- **M-step.** Count and normalize on the weighted examples, where "the only thing we do is instead of adding one, we add the weight" ([≈1:14:58](../raw/transcripts/14-bayesian-networks-learning.md)). The slide phrases it as computing the $\theta$ that maximizes the expected log likelihood of the weighted assignments.

That this procedure maximizes the likelihood, the lecturer asks the class to take on trust: "I'm not going to formally show this" ([≈1:08:00](../raw/transcripts/14-bayesian-networks-learning.md)).

## A Worked Run

The lecture's `expectation_maximization` function is written for this one model, not in general, and uses [parameter sharing](parameter-sharing.md): one table $p_R(r \mid g)$ for both ratings ([≈1:09:30](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:10:16](../raw/transcripts/14-bayesian-networks-learning.md)). It starts from $p_G = 0.5$ for each genre, $p_R(\cdot \mid \text{drama}) = \lbrace 1: 0.6, 2: 0.4 \rbrace$ and $p_R(\cdot \mid \text{comedy}) = \lbrace 1: 0.4, 2: 0.6 \rbrace$.

**First E-step**, done on the board ([≈1:11:01](../raw/transcripts/14-bayesian-networks-learning.md)–[≈1:14:10](../raw/transcripts/14-bayesian-networks-learning.md)). For the example $(1, 1)$: drama gets $0.5 \times 0.6 \times 0.6 = 0.18$ and comedy $0.5 \times 0.4 \times 0.4 = 0.08$, which normalize to about 0.69 and 0.31 (exactly $9/13$ and $4/13$). The example $(2, 2)$ gets the mirror image. The weighted data set has four rows: $(1, 1)$ as drama with weight 0.69 and as comedy with 0.31, and $(2, 2)$ as drama with 0.31 and as comedy with 0.69.

**First M-step** ([≈1:14:58](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:15:46](../raw/transcripts/14-bayesian-networks-learning.md)). Counting with weights, $p_G$ stays at 0.5 each, and $p_R(1 \mid \text{drama})$ rises from 0.6 to 0.69.

**Iterating** sharpens the guess every time. Traced values, rounded ([slides](../raw/slides/14-bayesian-networks-learning.md)):

| iteration | weight on drama for $(1, 1)$ | $p_R(1 \mid \text{drama})$ | $p_R(2 \mid \text{comedy})$ |
| --- | --- | --- | --- |
| 0 | 0.6923 | 0.6923 | 0.6923 |
| 1 | 0.8351 | 0.8351 | 0.8351 |
| 2 | 0.9624 | 0.9624 | 0.9624 |
| 3 | 0.9985 | 0.9985 | 0.9985 |
| 4 | 1.0000 | 1.0000 | 1.0000 |

By iteration 4 the probabilities are "pretty close to one and zero" ([≈1:16:35](../raw/transcripts/14-bayesian-networks-learning.md)). The model never saw a genre, but it concluded that dramas always rate 1 and comedies always rate 2, which is exactly the fully observed data from before.

**A messier data set** ([≈1:17:21](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)): four $(1, 1)$, two $(2, 2)$, one $(1, 2)$ and one $(2, 1)$. After 10 iterations, $p_G(\text{drama}) = 0.5692$, $p_R(1 \mid \text{drama}) = 0.9124$ and $p_R(2 \mid \text{comedy}) = 0.7548$. These are not as sharp as 1 and 0, because the assignments are more heterogeneous.

## What EM Is Doing

The model "doesn't see the hidden variables, so it's kind of trying to set the hidden variables to drive up the probability of the data" ([≈1:16:35](../raw/transcripts/14-bayesian-networks-learning.md)). The slide's gloss: the model learns hidden variables to explain the data, which is **clustering**, and EM amplifies initial preferences. The run above shows both: the slight initial tilt (0.6 against 0.4) grows every iteration until each cluster explains one kind of rating pair.

In the summary's words: if you do not see a variable, guess it, weight each guess by its probability given the observed variables, count and normalize the now fully labeled weighted data as in the supervised setting, and iterate ([≈1:19:37](../raw/transcripts/14-bayesian-networks-learning.md)).

## Caveats

From the lecture's notes ([≈1:18:07](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:18:52](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)):

- **Local, not global.** Each iteration is guaranteed to increase the likelihood, and EM converges to a local maximum, but it is not guaranteed to converge to the global maximum; it can get stuck in a local optimum. (The captions have the lecturer saying EM "can't get stuck," which the surrounding sentence and the slide contradict.)
- **Break the symmetry.** Initialize at a non-uniform distribution: "that's why I put 0.4 and 0.6 instead of 0.5 and 0.5." From a perfectly symmetric start, the two genres would have no reason to differ.
- **Labels are arbitrary.** The hidden variable can only be recovered up to a permutation of its labels: swapping drama and comedy gives the same likelihood, because they are just labels. That suits clustering, where the labels do not necessarily have a semantic meaning.

## Related Pages

- [Lecture 14](14-bayesian-networks-learning.md) — the lecture that teaches EM, with the full worked example
- [Maximum likelihood](maximum-likelihood.md) — the principle EM optimizes, and count and normalize, its M-step
- [Parameter sharing](parameter-sharing.md) — the shared rating table the example uses
- [Laplace smoothing](laplace-smoothing.md) — the other refinement of count and normalize in the same lecture
- [Probabilistic inference](probabilistic-inference.md) — what the E-step computes
- [Hidden Markov models](hidden-markov-models.md) — a model whose hidden states would need EM if unobserved
- [Lecture 1](01-course-overview.md) — where EM is first listed among the course's learning methods
