---
title: Laplace Smoothing — Pseudocounts Against Zero Probabilities
---

# Laplace Smoothing

**Laplace smoothing** is the fix for [maximum likelihood](maximum-likelihood.md) estimation's habit of giving probability exactly zero to anything not seen in training. Before counting, pretend you have already seen every possible value $\lambda$ times: add a **pseudocount** $\lambda$ to every count, then normalize as usual. It is introduced in [lecture 14](14-bayesian-networks-learning.md) for learning the local conditional distributions of a [Bayesian network](bayesian-networks.md) ([≈59:26](../raw/transcripts/14-bayesian-networks-learning.md)–[≈1:04:50](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)).

Notation, as in the lecture: $R \in \lbrace 1, 2, 3, 4, 5 \rbrace$ is a movie rating, $p_R(r)$ the estimated probability of rating $r$, and $\lambda \ge 0$ the smoothing amount, or pseudocount.

## The Problem: Maximum Likelihood Overfits

Suppose you have seen only two ratings, a 1 and a 4. Count and normalize gives $p_R(1) = p_R(4) = 0.5$, and therefore $p_R(2) = 0$. "Do we really believe that?" Putting zero probability on a rating of 2 after two data points "seems a little bit close-minded." It is probably because you did not have enough data, not because a 2 is impossible. With many samples maximum likelihood is fine; with few, it is **overfitting** to the data ([≈59:26](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:00:13](../raw/transcripts/14-bayesian-networks-learning.md)).

## The Fix: Add $\lambda$ to All the Counts

"A very simple idea": add a smoothing factor $\lambda$ to all the counts ([≈1:01:00](../raw/transcripts/14-bayesian-networks-learning.md)). Think of it as pretending you saw a 1, a 2, a 3, a 4 and a 5 before any real data. You did not actually see them, but you add these pseudocounts to the real counts and then normalize. In the lecture's code, the learning function simply starts its counts from the pseudocounts instead of from zero.

On the board, with $\lambda = 1$ and the data 1 and 4, the counts become 2, 1, 1, 2, 1 for ratings 1 to 5, out of a total of 7 ([≈1:01:46](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:02:32](../raw/transcripts/14-bayesian-networks-learning.md)):

$$p_R = \left( \tfrac{2}{7}, \tfrac{1}{7}, \tfrac{1}{7}, \tfrac{2}{7}, \tfrac{1}{7} \right)$$

There is still more weight on the ratings you did see, but the others are no longer zero, just smaller.

## Three Properties

The lecture demonstrates three properties by rerunning the estimate with different $\lambda$ ([≈1:02:32](../raw/transcripts/14-bayesian-networks-learning.md)–[≈1:04:04](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). The traced values:

| $\lambda$ | data | $p_R(1)$ | $p_R(2)$ | $p_R(3)$ | $p_R(4)$ | $p_R(5)$ |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | ratings 1, 4 | 0.2857 | 0.1429 | 0.1429 | 0.2857 | 0.1429 |
| 0 | ratings 1, 4 | 0.5 | 0 | 0 | 0.5 | 0 |
| 20 | ratings 1, 4 | 0.2059 | 0.1961 | 0.1961 | 0.2059 | 0.1961 |
| 20 | rating 4, 1,000 times | 0.0182 | 0.0182 | 0.0182 | 0.9273 | 0.0182 |

1. **As $\lambda \to 0$** you get back the original maximum likelihood estimate.
2. **As $\lambda \to \infty$** you approach the uniform distribution. At $\lambda = 20$ the estimate is "pretty much uniform," with a little more weight on the two ratings seen ($21/102$ against $20/102$).
3. **More data washes it out**, whatever $\lambda$ is. With a thousand ratings of 4, still at $\lambda = 20$, $p_R(4)$ is $1020/1100 \approx 0.93$ and heading toward 1, and the rest toward 0.

## How Much to Smooth

The lecturer's advice: "probably Laplace smoothing with one, or even 0.1, is right. You wouldn't use like 20" ([≈1:04:04](../raw/transcripts/14-bayesian-networks-learning.md)). You can think of the pseudocounts as almost like an initialization of the counts. In the lecture's summary, Laplace smoothing is what to use "if you're worried about overfitting" ([≈1:18:52](../raw/transcripts/14-bayesian-networks-learning.md)); the slide puts it as adding pseudocounts to prevent overfitting (zeros).

The lecture demonstrates smoothing only on the one-variable case, through the general learning function's `pseudocounts` argument ([≈47:42](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). That argument is keyed the same way as the counts it initializes, by table name, parents' value and value, so the function accepts pseudocounts for any table in a network.

## Related Pages

- [Lecture 14](14-bayesian-networks-learning.md) — the lecture that introduces it
- [Maximum likelihood](maximum-likelihood.md) — the estimate being smoothed, and why it is count and normalize
- [Expectation maximization](expectation-maximization.md) — the other refinement of count and normalize in the same lecture
- [Bayesian networks](bayesian-networks.md) — the models whose tables are being estimated
