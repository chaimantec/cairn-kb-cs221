---
title: Markov Blanket — The Variables a Gibbs Update Needs
---

# Markov Blanket

The **Markov blanket** of a variable in a [Bayesian network](bayesian-networks.md) is the set of variables you need to look at to resample it in [Gibbs sampling](gibbs-sampling.md). The lecture defines it as **all its children and its parents** ([lecture 13](13-gibbs-sampling.md), [≈42:45](../raw/transcripts/13-gibbs-sampling.md), [≈43:31](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)), and in answer to a question in class it also includes the children's other parents (see below). Restricting each update to the blanket is a pure speedup: the conditional distribution being sampled is unchanged.

Notation: uppercase letters are random variables and lowercase letters their values; lowercase $p$ is a local conditional probability, uppercase $P$ a probability derived from the joint distribution.

## Why Most Factors Cancel

A Gibbs update samples one variable from its conditional distribution given all the others, computed as a ratio of joint probabilities, and the joint is a product of one local conditional per node. Only one variable changes, so any factor that does not mention it is the same number in every term ([≈39:41](../raw/transcripts/13-gibbs-sampling.md)–[≈41:58](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). In the telephone network $A \to B \to C$, resampling $A$ given $B = b$ and $C = c$:

$$P(A = a \mid B = b, C = c) = \frac{p(a) \thinspace p(b \mid a) \thinspace p(c \mid b)}{\sum_{a'} p(a') \thinspace p(b \mid a') \thinspace p(c \mid b)}$$

where $a'$ ranges over the values of $A$. The factor $p(c \mid b)$ does not depend on $a$; it multiplies every numerator and the denominator alike, so it cancels: "I'm basically multiplying and dividing by the same number." Dropping it leaves $p(a) \thinspace p(b \mid a)$, which involves only $A$ and $B$. So the variables that matter are the ones that **touch** the variable being sampled, those that interact with it directly ([≈42:45](../raw/transcripts/13-gibbs-sampling.md)).

## Which Variables Are in It

A node appears in exactly two kinds of factor: its own local conditional, which involves its **parents**, and the local conditional of each of its **children** ([≈43:31](../raw/transcripts/13-gibbs-sampling.md)–[≈45:07](../raw/transcripts/13-gibbs-sampling.md)). On the board the lecturer marks a node in a larger DAG, puts its parents and children in the blanket, and leaves the rest out, because "everything else doesn't depend on" it.

**Co-parents are in it too.** The question comes up in class: if a child $Y$ of the node $X$ also has another parent $Z$, does $Z$ have to be included? Yes. The child's factor is $p(y \mid x, z)$, and "the parents always act as a unit," so evaluating it while changing $X$ requires the value of $Z$ ([≈45:07](../raw/transcripts/13-gibbs-sampling.md), [≈45:52](../raw/transcripts/13-gibbs-sampling.md)). The lecture's printed definition says only "children and parents"; the evening after the lecture the course rewrote the sentence above it to "we need to only include local conditional probabilities that involve the variable we're sampling. Evaluating these local conditional probabilities requires knowing all involved variables" ([slides](../raw/slides/13-gibbs-sampling.md), "Changes after the lecture").

For the telephone network ([≈45:52](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

| Variable | Markov blanket | Factors kept when resampling it |
| --- | --- | --- |
| $A$ | $\lbrace B \rbrace$ | $p(a) \thinspace p(b \mid a)$ |
| $B$ | $\lbrace A, C \rbrace$ | $p(b \mid a) \thinspace p(c \mid b)$ |
| $C$ | $\lbrace B \rbrace$ | $p(c \mid b)$ |

## What It Buys

Gibbs sampling with the full joint costs `O(#iterations * #variables * |domain| * #variables)`, because each evaluation goes through every variable. With the blanket, the last factor becomes `|markov_blanket|` ([≈47:24](../raw/transcripts/13-gibbs-sampling.md), [≈48:10](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). It computes the same conditional distributions "up to floating-point precision," so it is an optimization, not a change of algorithm: it "doesn't resolve any sort of fundamental issues with Gibbs sampling," but "why not do it." It saves the most when blankets are small; for a node connected to many others it saves little, "but it doesn't hurt either" ([≈48:56](../raw/transcripts/13-gibbs-sampling.md)). On the telephone network, the blanket version estimated $P(A = 1 \mid C = 1)$ at 0.63 over 100 iterations, against 0.68 for the full-joint run, both random runs of the same sampler ([slides](../raw/slides/13-gibbs-sampling.md)).

The Markov blanket is also where the course first uses the network's **structure** for inference rather than treating it as a black box, the theme that continues with [conditional independence](conditional-independence.md) ([≈59:04](../raw/transcripts/13-gibbs-sampling.md)).

## Related Pages

- [Lecture 13](13-gibbs-sampling.md) — where the Markov blanket is introduced
- [Gibbs sampling](gibbs-sampling.md) — the algorithm it speeds up
- [Bayesian networks](bayesian-networks.md) — one local conditional per node, which is why only a few factors involve any one variable
- [Conditional independence](conditional-independence.md) — reading independence off the graph
