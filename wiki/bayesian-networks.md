---
title: Bayesian Networks — Defining a Joint Distribution Node by Node
---

# Bayesian Networks

A **Bayesian network** is a compact, intuitive way to define a **joint distribution** over a set of random variables: draw a directed acyclic graph over the variables, give each variable a local conditional distribution given its parents, and define the joint distribution as the product of those local distributions ([lecture 12](12-bayesian-networks-i.md), [≈57:40](../raw/transcripts/12-bayesian-networks-i.md), [≈58:26](../raw/transcripts/12-bayesian-networks-i.md)). Once the joint distribution exists, you can ask it any question of the form $P(\text{query} \mid \text{evidence})$, which is [probabilistic inference](probabilistic-inference.md). In CS221 they open the course's third unit, after the state-based models (search, MDPs, games), as an answer to the question "how should we represent the state of the world?": as a set of random variables, each an attribute of the world, reasoned about under uncertainty ([≈3:59](../raw/transcripts/12-bayesian-networks-i.md), [≈4:46](../raw/transcripts/12-bayesian-networks-i.md)).

Notation, as in the lecture: uppercase letters ($X_i$, $B$) are random variables and lowercase ($x_i$, $b$) their values. Lowercase $p$ is a local conditional probability, **defined** by hand; uppercase $P$ is a probability **derived** from the joint distribution by the laws of probability ([≈35:50](../raw/transcripts/12-bayesian-networks-i.md)–[≈37:25](../raw/transcripts/12-bayesian-networks-i.md), [≈45:56](../raw/transcripts/12-bayesian-networks-i.md)).

## Why Not Just Write the Table?

Probabilistic inference works on any joint distribution, so nothing about it needs a Bayesian network. What the network buys is an easier way to **define** the joint distribution ([≈23:21](../raw/transcripts/12-bayesian-networks-i.md), [≈24:06](../raw/transcripts/12-bayesian-networks-i.md)). The full table has one entry per assignment. For the alarm's three binary variables that is only $2^3 = 8$, but nobody writes the table out for a large number of variables, and filling it in "slavishly" is neither intuitive nor modular ([≈27:16](../raw/transcripts/12-bayesian-networks-i.md), [≈46:42](../raw/transcripts/12-bayesian-networks-i.md), [≈1:04:34](../raw/transcripts/12-bayesian-networks-i.md)). A Bayesian network instead asks for one small table per variable, each about something you can reason about directly, such as how likely the alarm is to sound given whether there was a burglary and whether there was an earthquake.

## The Four Steps

The general definition ([≈57:40](../raw/transcripts/12-bayesian-networks-i.md), [≈58:26](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

1. Define a set of random variables $X = (X_1, \ldots, X_n)$.
2. Define any **directed acyclic graph** (DAG) over them. Any graph is allowed as long as it has no cycles. Edges suggest **direct influence**. It is natural to read them as "causes," and that is fine as intuition, but Bayesian networks do not automatically imply causality ([≈28:02](../raw/transcripts/12-bayesian-networks-i.md), [≈28:48](../raw/transcripts/12-bayesian-networks-i.md)).
3. For each node $X_i$, define a **local conditional distribution** $p(x_i \mid \text{parents}(x_i))$: a table giving, for every assignment to the parents, a distribution over $X_i$.
4. Define the joint distribution as the product:

$$P(X_1 = x_1, \ldots, X_n = x_n) = \prod_{i=1}^{n} p(x_i \mid \text{parents}(x_i))$$

Three mistakes the lecture warns against ([≈58:26](../raw/transcripts/12-bayesian-networks-i.md), [≈59:12](../raw/transcripts/12-bayesian-networks-i.md)):

- There is **one local conditional distribution per node, not per edge**. A node with no parents still needs one, such as $p(c)$ for a cold.
- A node's distribution conditions on **all its parents at once**: cough with parents cold and allergies needs $p(h \mid c, a)$, not $p(h \mid c) \thinspace p(h \mid a)$. "In the literature they call this: the parents are married."
- Keep $p$ and $P$ apart. In the alarm network the marginal $P(B)$ derived from the joint comes out equal to the local $p(b)$, but they are different objects, and it is "not maybe obvious at first sight that they should be the same" ([≈35:50](../raw/transcripts/12-bayesian-networks-i.md), [≈36:39](../raw/transcripts/12-bayesian-networks-i.md)).

Because each local distribution is a tensor indexed by its node and its parents, step 4 is a single `einsum`. In the course's convention each table's axes list the conditioning variables first and its own variable last ([≈32:44](../raw/transcripts/12-bayesian-networks-i.md), [≈33:32](../raw/transcripts/12-bayesian-networks-i.md), [≈52:13](../raw/transcripts/12-bayesian-networks-i.md); see [einops](einops.md)).

## Examples from the Course

**Alarm** (three variables). Burglary $B$ and earthquake $E$ are independent, each with probability $\epsilon = 0.05$; the alarm $A$ sounds if either happens. Edges $B \to A$ and $E \to A$ ([≈24:06](../raw/transcripts/12-bayesian-networks-i.md)–[≈34:18](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

$$P(B = b, E = e, A = a) = p(b) \thinspace p(e) \thinspace p(a \mid b, e)$$

with $p(a \mid b, e)$ equal to 1 when $a = b \lor e$ and 0 otherwise. The resulting joint distribution gives probability 0.9025 to nothing happening, 0.0475 each to a burglary alone and an earthquake alone, and 0.0025 to both.

**Medical diagnosis** (four variables). Cold $C$ and allergies $A$ are independent causes of cough $H$; allergies alone cause itchy eyes $I$. Edges $C \to H$, $A \to H$, $A \to I$ ([≈47:30](../raw/transcripts/12-bayesian-networks-i.md)–[≈52:13](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

$$P(C = c, A = a, H = h, I = i) = p(c) \thinspace p(a) \thinspace p(h \mid c, a) \thinspace p(i \mid a)$$

with $p(C = 1) = 0.1$, $p(A = 1) = 0.2$, a cough with probability 0.9 if cold or allergies and 0.1 otherwise, and itchy eyes with probability 0.9 if allergies and 0.1 otherwise.

**Autoregressive language models.** The tokens $X_1, \ldots, X_T$ are the variables, every token has edges from all earlier tokens, and the Transformer supplies the local conditional distribution, so ([≈1:00:43](../raw/transcripts/12-bayesian-networks-i.md), [≈1:01:30](../raw/transcripts/12-bayesian-networks-i.md)):

$$P(X_1, \ldots, X_T) = \prod_t p(x_t \mid x_1, \ldots, x_{t-1})$$

Sampling forward from a prompt is what language models normally do. Inference in the other direction, from a response back to the prompts likely to have produced it, has an application in jailbreaking; it is expensive and "in general exponentially hard" ([≈1:02:15](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:03:49](../raw/transcripts/12-bayesian-networks-i.md)).

**Hidden Markov model for object tracking.** Hidden positions $H_1 \to H_2 \to \cdots \to H_5$ form a chain, and each $H_t$ has a child $E_t$, a noisy sensor reading. In the lecture's version the object advances by one with probability 0.5 at each step, and the reading is the position plus 0 or 1 ([≈1:11:33](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:13:09](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

## Two Ways to Write a Network Down

The four steps write a network as tables. A **probabilistic program** writes the same network as code that draws one sample from the joint distribution, each variable sampled from its local distribution given the values already drawn for its parents. The alarm network is three lines: `B = Bernoulli(0.05)`, `E = Bernoulli(0.05)`, `A = B or E`. "This is actually a formal definition of a Bayesian network," just an unusual one ([≈1:04:34](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:06:07](../raw/transcripts/12-bayesian-networks-i.md)). Programs make approximate inference by [rejection sampling](rejection-sampling.md) natural. Lecture 13 adds a second sampler, [Gibbs sampling](gibbs-sampling.md), which works on the tables rather than the program: it changes one variable at a time, and needs only the local conditionals in that variable's [Markov blanket](markov-blanket.md) ([lecture 13](13-gibbs-sampling.md)).

## What Comes Out: Explaining Away

Defining the joint distribution carefully is what produces reasoning patterns that intuition gets wrong. The defining example is [explaining away](explaining-away.md): two independent causes of an observed effect become dependent, so learning that one cause occurred lowers the probability of the other. In general, observed evidence **propagates** through the network, raising and lowering the probabilities of other nodes, and computing these quantities exactly can be NP-hard ([≈43:37](../raw/transcripts/12-bayesian-networks-i.md), [≈56:54](../raw/transcripts/12-bayesian-networks-i.md), [≈57:40](../raw/transcripts/12-bayesian-networks-i.md)).

## Structure and Independence

A Bayesian network is at once a distribution and a graph, and lecture 13 reads independence facts off the graph ([≈58:16](../raw/transcripts/13-gibbs-sampling.md)–[≈1:13:50](../raw/transcripts/13-gibbs-sampling.md)). Two unconnected variables are independent, which is "generally the right intuition." Connected ones usually are not, with one special case: two parents of a common child, $A \to C \leftarrow B$, are independent as long as the child is unobserved, and become dependent once it is observed, which is explaining away in the alarm network. The opposite shape, a common parent $A \leftarrow C \to B$, makes $A$ and $B$ dependent until $C$ is observed. A four-step procedure (shade the evidence, prune unshaded leaves, marry parents, look for an unblocked path) decides the general case. See [conditional independence](conditional-independence.md).

## Why Use Them: A Shift from Classifiers

A classifier maps input to output, say cough to cold. A Bayesian network usually runs the other way, from output and hidden variables to the observed input: it describes how a cold generates symptoms, and inference runs the model backwards ([≈1:14:45](../raw/transcripts/12-bayesian-networks-i.md), [≈1:15:32](../raw/transcripts/12-bayesian-networks-i.md)). The lecture lists four advantages ([≈1:15:32](../raw/transcripts/12-bayesian-networks-i.md), [≈1:16:18](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

- They handle **missing information**, at training and at test time. If the doctor did not record whether you had itchy eyes, the model accounts for it automatically.
- They can incorporate **prior knowledge**: if you know what your alarm system reacts to, write it down instead of learning it. The slide's examples are Mendelian inheritance and the laws of physics.
- Every intermediate variable can be **interpreted**, which big end-to-end systems often lack.
- They are a precursor to **causal** models, which handle interventions and counterfactuals (beyond the scope of the class).

The lecturer's broader case: before uncertainty was "nailed down probabilistically," heuristic ways of reasoning about it "were all sort of inconsistent and kind of broken" ([≈43:37](../raw/transcripts/12-bayesian-networks-i.md)).

## Related Pages

- [Lecture 12](12-bayesian-networks-i.md) — the lecture that introduces Bayesian networks, with both worked examples
- [Probabilistic inference](probabilistic-inference.md) — the questions a network answers, and the select–sum–divide recipe
- [Explaining away](explaining-away.md) — the signature reasoning pattern, in the alarm and medical-diagnosis networks
- [Rejection sampling](rejection-sampling.md) — probabilistic programs, and approximate inference by sampling
- [Lecture 13](13-gibbs-sampling.md) — Gibbs sampling, the Markov blanket, and conditional independence
- [Gibbs sampling](gibbs-sampling.md) — approximate inference one variable at a time
- [Markov blanket](markov-blanket.md) — the variables a Gibbs update needs
- [Conditional independence](conditional-independence.md) — reading independence off the graph
- [Einops](einops.md) — the einsum notation that builds and queries the tables
