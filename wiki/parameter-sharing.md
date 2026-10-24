---
title: Parameter Sharing — One Table, Several Nodes
---

# Parameter Sharing

In a [Bayesian network](bayesian-networks.md), every node needs a local conditional distribution, but nothing says each node needs its **own**. **Parameter sharing** lets several nodes be powered by the same table. It is introduced in [lecture 14](14-bayesian-networks-learning.md), where it is the first time the course separates the network's *variables* from its *parameters*: "up until now we've kind of thought about the variables in a network and the distributions as kind of one to one. But now for the first time we're splitting them" ([≈34:19](../raw/transcripts/14-bayesian-networks-learning.md)).

Notation, as in the lecture: uppercase letters are random variables and lowercase their values. A subscripted lowercase $p$ is a local conditional distribution, and the subscript names the **table**, not the node: $p_R$ is one table even when it powers both $R_1$ and $R_2$.

## The Example: Two Users' Ratings

A movie's genre $G \in \lbrace \text{drama}, \text{comedy} \rbrace$ influences two users' ratings, $R_1 \leftarrow G \to R_2$ ([≈26:23](../raw/transcripts/14-bayesian-networks-learning.md)–[≈35:06](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). Without sharing, each rating gets its own table:

$$P(G = g, R_1 = r_1, R_2 = r_2) = p_G(g) \thinspace p_{R1}(r_1 \mid g) \thinspace p_{R2}(r_2 \mid g), \qquad \theta = (p_G, p_{R1}, p_{R2})$$

With sharing, the same network uses one rating table for both:

$$P(G = g, R_1 = r_1, R_2 = r_2) = p_G(g) \thinspace p_R(r_1 \mid g) \thinspace p_R(r_2 \mid g), \qquad \theta = (p_G, p_R)$$

**Why you would want it.** Imagine a thousand users, $R_1$ through $R_{1000}$, each with its own table. A user who has rated one movie gives you almost nothing to estimate their table from. Sharing estimates one distribution over rating given genre instead of a thousand ([≈29:37](../raw/transcripts/14-bayesian-networks-learning.md), [≈30:22](../raw/transcripts/14-bayesian-networks-learning.md)).

## The Mental Picture

So far the probabilities have been "sort of implicit, like in the edges or something." For learning, it matters where they come from ([≈31:57](../raw/transcripts/14-bayesian-networks-learning.md)):

> The real way to think about this is that you have these local conditional tables that are floating out. And these tables are connected. You hook these tables up to nodes to power them, to give them probabilistic life.

The slide's figure draws exactly this: the network $G \to R_1$, $G \to R_2$ in the middle; on the left a $p_G$ table (comedy $2/5$, drama $3/5$) with a red dashed arrow to $G$; on the right a single $p_R(r \mid g)$ table with **two** red dashed arrows, one to $R_1$ and one to $R_2$ ([slides](../raw/slides/14-bayesian-networks-learning.md), "Figures"). The slide's one-line intuition: each node is powered by some local conditional distribution.

## What Changes When You Learn

**Counts belong to parameters, not variables** ([≈32:44](../raw/transcripts/14-bayesian-networks-learning.md), [≈33:32](../raw/transcripts/14-bayesian-networks-learning.md)). With sharing there are two tables, so two sets of counts, and both users' ratings are added into the same genre–rating counts. On the lecture's five examples (drama 4/5, drama 4/4, drama 5/3, comedy 1/2, comedy 5/4, giving user 1's and user 2's ratings), drama ends up with six ratings and comedy with four:

| | without sharing | with sharing |
| --- | --- | --- |
| tables normalized | 5 ($p_G$; $p_{R1}$ and $p_{R2}$ for each genre) | 3 ($p_G$; $p_R$ for each genre) |
| rating given drama | user 1: 4 (2/3), 5 (1/3); user 2: 5, 4, 3 (1/3 each) | 3 (1/6), 4 (3/6), 5 (2/6) |
| rating given comedy | user 1: 1, 5 (1/2 each); user 2: 2, 4 (1/2 each) | 1, 2, 4, 5 (1/4 each) |

It is "almost like" having twice as many examples for each table ([≈33:32](../raw/transcripts/14-bayesian-networks-learning.md), [≈35:06](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)).

**Reading versus writing** ([≈35:06](../raw/transcripts/14-bayesian-networks-learning.md), [≈35:53](../raw/transcripts/14-bayesian-networks-learning.md)). [Probabilistic inference](probabilistic-inference.md) only reads the tables, so it does not care whether $p(r_1 \mid g)$ and $p(r_2 \mid g)$ are the same distribution; they are just numbers. Learning writes to them, so it matters. The lecturer's analogy is passing an object by reference rather than by value: with a reference, mutations in one place show up in the other.

**It is not the same as merging the nodes.** Asked why you would not just combine the two ratings into one variable: they are still two users, and the data still records user 1's rating and user 2's rating, which one node would have nowhere to put ([≈33:32](../raw/transcripts/14-bayesian-networks-learning.md), [≈34:19](../raw/transcripts/14-bayesian-networks-learning.md)).

## When to Share

It is a modeling decision with a trade-off ([≈34:19](../raw/transcripts/14-bayesian-networks-learning.md), [≈36:38](../raw/transcripts/14-bayesian-networks-learning.md), [≈37:24](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)):

- **Fewer parameters need fewer examples to learn.** A thousand bespoke per-user distributions need a lot of data from each user.
- **More parameters give greater flexibility.** If the users really are very different, you want separate tables to capture the nuances of each one's preferences.
- So ask whether the users are similar, and how much data you have. With a lot of data you can afford many parameters; with little, "you need to be very stingy with your parameters."

## Sharing Everywhere: HMMs and the General Form

The [hidden Markov model](hidden-markov-models.md) shares parameters across time: one transition table for every step after the first and one emission table for every step, so a chain of 100 steps still has only three local conditional distributions ([≈39:42](../raw/transcripts/14-bayesian-networks-learning.md)–[≈41:15](../raw/transcripts/14-bayesian-networks-learning.md)).

In general, let $D$ be the set of table types (for the HMM, $\lbrace \text{start}, \text{trans}, \text{emit} \rbrace$), with parameters $\theta = \lbrace p_d : d \in D \rbrace$. Each variable $X_i$ has a type $d_i \in D$ that says which table to "go fetch" ([≈43:39](../raw/transcripts/14-bayesian-networks-learning.md)–[≈45:17](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)):

$$P(X_1 = x_1, \ldots, X_n = x_n) = \prod_i p_{d_i}(x_i \mid x_{\text{parents}(X_i)})$$

Parameter sharing is just two variables with the same type, say $d_3 = d_7$. The learning code takes a `network_structure` mapping each variable to its type and its parents, and counts into `counts[type][parents_value][value]`, so shared tables accumulate automatically ([≈46:08](../raw/transcripts/14-bayesian-networks-learning.md)–[≈50:52](../raw/transcripts/14-bayesian-networks-learning.md)). See [maximum likelihood](maximum-likelihood.md) for why counting and normalizing is the right estimate.

The lecture's [expectation maximization](expectation-maximization.md) example also uses the shared model: one $p_R(r \mid g)$ for both ratings ([≈1:10:16](../raw/transcripts/14-bayesian-networks-learning.md)).

## Related Pages

- [Lecture 14](14-bayesian-networks-learning.md) — where parameter sharing is introduced
- [Hidden Markov models](hidden-markov-models.md) — sharing across time steps
- [Maximum likelihood](maximum-likelihood.md) — count and normalize, the estimate shared tables are learned with
- [Expectation maximization](expectation-maximization.md) — learning the shared model with the genre hidden
- [Bayesian networks](bayesian-networks.md) — the four-step definition, where every node gets a table
