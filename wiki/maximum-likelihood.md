---
title: Maximum Likelihood — and Why Count and Normalize Is the Answer
---

# Maximum Likelihood

The **maximum likelihood principle** says: choose the parameters under which the observed data is most probable. The course meets it twice. In [lecture 3](03-learning-ii.md) it is where the logistic loss comes from. In [lecture 14](14-bayesian-networks-learning.md) it is the justification for learning a [Bayesian network](bayesian-networks.md) by **count and normalize**, which turns out to be the exact, closed-form maximum likelihood estimate. The lecturer calls it "a very simple and powerful principle in statistics, and in all of machine learning really" ([≈52:27](../raw/transcripts/14-bayesian-networks-learning.md)).

Notation, as in lecture 14: $\theta$ is the collection of all the model's parameters, $x$ ranges over the training examples, and $P(X = x; \theta)$ is the probability the model with parameters $\theta$ gives to example $x$. A subscripted lowercase $p$, such as $p_R$, is one of a Bayesian network's local conditional distributions.

## The Principle

Find the parameters that maximize the likelihood of the data ([≈53:14](../raw/transcripts/14-bayesian-networks-learning.md), [≈54:02](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)):

$$\max_\theta \prod_x P(X = x; \theta) = \max_\theta \sum_x \log P(X = x; \theta)$$

The product multiplies together the probability the model gives each training example. Any particular setting of the parameters gives a number for it, so this is an optimization problem: pick the setting that drives the number as high as possible. Taking the log changes nothing about which $\theta$ wins, because log is monotonic, and it turns the product into a sum ([≈54:02](../raw/transcripts/14-bayesian-networks-learning.md)). Lecture 3 adds the practical reason: probabilities like $10^{-200}$ are common, and log probabilities stay manageable ([lecture 3](03-learning-ii.md), [≈45:54](../raw/transcripts/03-learning-ii.md)).

The sum of log probabilities is the negative **cross entropy**, so minimizing cross entropy is maximum likelihood. That includes training a large language model: minimizing cross entropy, or perplexity, "is exactly the same as maximum likelihood" ([≈53:14](../raw/transcripts/14-bayesian-networks-learning.md), [≈54:48](../raw/transcripts/14-bayesian-networks-learning.md)).

## In Lecture 3: Where the Logistic Loss Comes From

For a linear classifier that outputs a probability, $p(y \mid \mathbf{x}) = \sigma(\text{margin})$, maximum likelihood means maximizing the probability of the training targets ([lecture 3](03-learning-ii.md), [≈40:29](../raw/transcripts/03-learning-ii.md), [≈41:16](../raw/transcripts/03-learning-ii.md)). Over many examples that is a product of probabilities, equivalently a sum of log probabilities, and negating it to get something to minimize gives the logistic loss, $-\log \sigma(\text{margin})$. For several classes, lecture 3's cross entropy loss with a one-hot target is likewise the negative log probability of the target class ([≈1:02:56](../raw/transcripts/03-learning-ii.md)). There, the loss is minimized by gradient descent.

## In Lecture 14: Count and Normalize, in Closed Form

For a Bayesian network whose training examples assign every variable (the **fully observed** setting), no gradient descent is needed. The maximum likelihood estimate of each local conditional distribution is **count and normalize**: count how often each value of the node occurs with each assignment to its parents, and divide by the total for that parent assignment ([≈54:48](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). "Normally you think about optimization as, oh dear, I need to compute the gradient, I need to take steps, and what about the step size and what about convergence? No." There is a closed-form solution.

The lecture justifies this with two examples rather than a general proof.

**One variable** ([≈55:37](../raw/transcripts/14-bayesian-networks-learning.md)–[≈57:09](../raw/transcripts/14-bayesian-networks-learning.md)). A movie rating $R \in \lbrace 1, \ldots, 5 \rbrace$ with distribution $p_R$, and three ratings, 1, 5 and 5:

$$\max_\theta \thinspace p_R(1) \thinspace p_R(5) \thinspace p_R(5) \quad \text{subject to} \quad p_R(1) + p_R(2) + p_R(3) + p_R(4) + p_R(5) = 1$$

Introduce a Lagrange multiplier for the sum-to-one constraint, set the gradient to zero and solve: each probability is the number of times that rating occurred over the total, $p_R(1) = 1/3$ and $p_R(5) = 2/3$. Intuitively, you push up the ratings you saw, the twice-seen one more, and waste no probability mass on ratings you never saw.

**Two variables** ([≈57:09](../raw/transcripts/14-bayesian-networks-learning.md)–[≈59:26](../raw/transcripts/14-bayesian-networks-learning.md)). A genre $G \in \lbrace \text{drama}, \text{comedy} \rbrace$ with $G \to R$, and examples (drama, 4), (drama, 5), (comedy, 5). The likelihood is six factors, two per example. Regroup them by which parameters they involve:

$$[p_G(\text{drama}) \thinspace p_G(\text{drama}) \thinspace p_G(\text{comedy})] \thinspace [p_R(4 \mid \text{drama}) \thinspace p_R(5 \mid \text{drama})] \thinspace [p_R(5 \mid \text{comedy})]$$

The three groups share no parameters, so maximizing the product splits into three separate problems, each one the one-variable case, each solved by counting and normalizing. The lecture stops at these two examples, but they show the idea behind the earlier rule of thumb that each local distribution can be estimated by "ignoring the other variables" ([≈20:58](../raw/transcripts/14-bayesian-networks-learning.md)). "You can now sleep at night in comfort that all your intuitive counting and normalizing thing is actually principled" ([≈58:40](../raw/transcripts/14-bayesian-networks-learning.md)).

The general algorithm, the `supervised_learning` function with its nested counts keyed by parameter name, parents' values and the node's value, is on the [lecture 14 page](14-bayesian-networks-learning.md#the-general-algorithm). With [parameter sharing](parameter-sharing.md), several nodes add into the same counts.

## When It Goes Wrong, and When It Runs Out

**Too little data.** With ratings 1 and 4 only, maximum likelihood puts probability exactly zero on 2, 3 and 5, which is "a little bit close-minded." With few samples, maximum likelihood overfits; [Laplace smoothing](laplace-smoothing.md) adds pseudocounts to fix it ([≈59:26](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:00:13](../raw/transcripts/14-bayesian-networks-learning.md)).

**Hidden variables.** When some variables are never observed you cannot count them, but the principle still applies: maximize the likelihood of whatever *is* observed, marginalizing out the rest. "The idea of having principles is that you can always go back to your principle and think, what would maximum likelihood do?" ([≈1:06:28](../raw/transcripts/14-bayesian-networks-learning.md)). There is no longer a closed form; [expectation maximization](expectation-maximization.md) climbs to a local maximum instead.

## Related Pages

- [Lecture 14](14-bayesian-networks-learning.md) — learning Bayesian networks: count and normalize, and why it is maximum likelihood
- [Lecture 3](03-learning-ii.md) — maximum likelihood behind the logistic and cross entropy losses
- [Laplace smoothing](laplace-smoothing.md) — pseudocounts against maximum likelihood's zeros
- [Expectation maximization](expectation-maximization.md) — maximum likelihood with unobserved variables
- [Parameter sharing](parameter-sharing.md) — one table counted from several nodes
- [Bayesian networks](bayesian-networks.md) — the models whose parameters are being estimated
