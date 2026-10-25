---
title: Lecture 14 — Bayesian Networks and Learning
---

# Lecture 14 — Bayesian Networks and Learning

Lecture 14 closes the [Bayesian networks](bayesian-networks.md) unit. Up to now, every probability in a network was simply given: written down by hand, or "popped out of thin air." This lecture **learns** the local conditional distributions from data ([≈13:17](../raw/transcripts/14-bayesian-networks-learning.md)). When every training example assigns a value to every variable (the **fully observed** setting), learning is **count and normalize**: count how often each local configuration occurs, then divide by the totals. That simple recipe is exactly the [maximum likelihood](maximum-likelihood.md) estimate, with no iterative optimization needed. Two refinements follow. [Laplace smoothing](laplace-smoothing.md) adds pseudocounts so that values never seen in training do not get probability zero. [Expectation maximization](expectation-maximization.md) handles the **partially observed** setting, where some variables are never seen, by alternately guessing the hidden values and re-counting. Along the way, [parameter sharing](parameter-sharing.md) separates the network's *variables* from its *parameters*, and the [hidden Markov model](hidden-markov-models.md) returns as the main example of sharing.

Notation, as in the lecture: uppercase letters ($R$, $G$, $H_t$) are random variables and lowercase letters their values. A subscripted lowercase $p$, such as $p_R$ or $p_{\text{trans}}$, is a **local conditional distribution**, one of the network's parameters; the subscript names which table it is. Uppercase $P$ is a probability under the joint distribution, and $\theta$ collects all the parameters. The running example is movies: $R \in \lbrace 1, 2, 3, 4, 5 \rbrace$ is a rating, $G \in \lbrace \text{drama}, \text{comedy} \rbrace$ a genre.

## Review: Networks, Inference, Independence

A Bayesian network defines a joint distribution over a set of variables, which you can think of as "a database that specifies facts you know about the world" ([≈0:05](../raw/transcripts/14-bayesian-networks-learning.md)–[≈2:26](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). You choose the random variables (burglary $B$, earthquake $E$, alarm $A$), draw a directed acyclic graph connecting variables you think are related, give **every node** (not every edge) a local conditional distribution $p(x_i \mid \text{parents}(x_i))$, and multiply them together:

$$P(B = b, E = e, A = a) = p(b) \thinspace p(e) \thinspace p(a \mid b, e)$$

Each local conditional distribution is a little table, or a tensor. Summing a conditional table over all its rows does not give 1, since it holds one distribution per assignment to the parents; summing the joint table over all assignments does ([≈1:39](../raw/transcripts/14-bayesian-networks-learning.md), [≈2:26](../raw/transcripts/14-bayesian-networks-learning.md)).

Querying that database is [probabilistic inference](probabilistic-inference.md), for example $P(B \mid A = 1)$. The course has covered three algorithms ([≈3:13](../raw/transcripts/14-bayesian-networks-learning.md)–[≈4:47](../raw/transcripts/14-bayesian-networks-learning.md)). Exact inference selects the rows with $A = 1$, marginalizes out $E$ and divides by the probability of the evidence. [Rejection sampling](rejection-sampling.md) runs a probabilistic program and discards samples with $A \neq 1$. [Gibbs sampling](gibbs-sampling.md) starts from a full assignment and repeatedly resamples one variable given the rest: fix $E$ and $A$, update $B$; fix $B$ and $A$, update $E$; never update $A$, which is fixed to 1. There are many more inference algorithms, and the lecturer points to CS228 for more depth ([≈4:47](../raw/transcripts/14-bayesian-networks-learning.md)).

### Conditional independence, restated

The lecturer presents [conditional independence](conditional-independence.md) "in a slightly different way" than in lecture 13, hoping it is clearer ([≈4:47](../raw/transcripts/14-bayesian-networks-learning.md)–[≈6:18](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). $A$ and $B$ are conditionally independent given $C$ **if and only if every path from $A$ to $B$ is blocked** by nodes in $C$. A path is blocked if it contains one of three patterns:

1. A chain $X \to C \to Y$ through a conditioned node.
2. A fork $X \leftarrow C \to Y$ at a conditioned node.
3. A v-structure $X \to Z \leftarrow Y$, **unless** $Z$ or a descendant of $Z$ is conditioned on. This one runs the opposite way: it blocks when you condition on nothing, and conditioning opens it ("remember explaining away!").

On the board, with shading marking a conditioned variable, he redraws the two contrasting pairs from last time ([≈7:04](../raw/transcripts/14-bayesian-networks-learning.md)–[≈9:22](../raw/transcripts/14-bayesian-networks-learning.md)). In the v-structure $A \to C \leftarrow B$, $A$ and $B$ are independent while $C$ is unobserved (rule 3 blocks the path), and **not** independent once $C$ is shaded; this is [explaining away](explaining-away.md), where seeing the alarm makes burglary and earthquake anti-correlated. In the fork $A \leftarrow C \to B$, it is the reverse: not independent with $C$ unobserved, since the path gets through, and independent once $C$ is shaded. "If you just remember these contrasting pairs, then I think you get basically 90% of what's happening."

**A whiteboard game.** He then builds a network around $A$ and $B$ one edge at a time and polls the class on whether $A$ and $B$ are independent ([≈9:22](../raw/transcripts/14-bayesian-networks-learning.md)–[≈11:44](../raw/transcripts/14-bayesian-networks-learning.md)). The drawings are not in the source program, and the transcript does not say exactly what was drawn at each step, only the lessons stated along the way. Conditioning on a **descendant** of a v-structure's middle node also makes its parents dependent, just as conditioning on the middle node does. **Unobserved leaves** can be ignored: "you kind of pretend they don't exist," so a network whose only connections between $A$ and $B$ run through unobserved v-structures leaves them independent (in one drawing a node $D$ blocks two such paths at once). And a path that clearly connects $A$ and $B$ becomes blocked once you condition on a node along it.

**Why it matters.** In practice you condition on some variables, and independence tells you what then stops interacting. That is useful for inference, because independent parts can be handled separately and in parallel ([≈11:44](../raw/transcripts/14-bayesian-networks-learning.md), [≈12:32](../raw/transcripts/14-bayesian-networks-learning.md)). The lecturer's example: a variable $Z$ with children $X_1, X_2, \ldots$, say data points. In Gibbs sampling, once you condition on $Z$, every $X_i$ is independent of every other, because $Z$ blocks every path between them, so you can sample all the $X_i$ given $Z$ at once.

## Where Do the Probabilities Come From?

Everything so far assumed the probabilities were given, which "is not a very principled way of doing things." The natural alternative is to learn them from data ([≈13:17](../raw/transcripts/14-bayesian-networks-learning.md), [≈14:04](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). The **learning task** in the fully observed setting:

- **Input**: a set of examples, each an assignment to *all* the variables.
- **Output**: the parameters, which are the local conditional distributions, that is, all the numbers in the probability tables.

The lecture walks through a series of examples to build intuition for the algorithm, then explains the principle behind it.

## Count and Normalize

### One variable

"The world's simplest Bayesian network" is a single node $R$, the rating of a movie, with no edges ([≈14:50](../raw/transcripts/14-bayesian-networks-learning.md)–[≈17:53](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). The joint distribution is just $P(R = r) = p_R(r)$, and the parameters are five numbers, $\theta = (p_R(1), p_R(2), p_R(3), p_R(4), p_R(5))$. (A clever student might note that four suffice, because they must sum to one; the lecture does not worry about that.) The subscript $R$ names the table, which matters once there are several.

The training data is ten ratings: 1, 3, 4, 4, 4, 4, 4, 5, 5, 5. Asked how to estimate the parameters, a student answers: count the observations and normalize. "That's exactly what we're going to do" ([≈16:21](../raw/transcripts/14-bayesian-networks-learning.md)):

1. **Count** the occurrences of each rating: 1 once, 3 once, 4 five times, 5 three times.
2. **Normalize**: divide each count by the total, giving $p_R = \lbrace 1: 0.1, 3: 0.1, 4: 0.5, 5: 0.3 \rbrace$. Rating 2 never occurs, so it gets probability zero.

"This is basically the primitive that we're going to see, and everything else is variations on that theme" ([≈17:07](../raw/transcripts/14-bayesian-networks-learning.md)).

### Two variables: ignore the other variables

Add the genre: $G \to R$, with joint distribution $P(G = g, R = r) = p_G(g) \thinspace p_R(r \mid g)$ and parameters $\theta = (p_G, p_R)$ ([≈17:53](../raw/transcripts/14-bayesian-networks-learning.md)–[≈21:44](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). The data is five (genre, rating) pairs: (drama, 4), (drama, 4), (drama, 5), (comedy, 1), (comedy, 5). Now you keep two sets of counts: how often each genre occurs, and how often each genre occurs **with** each rating. The counts are drama 3 and comedy 2; drama with 4 twice, drama with 5 once, comedy with 1 once, comedy with 5 once.

Normalizing gives $p_G(\text{drama}) = 3/5$ and $p_G(\text{comedy}) = 2/5$. For $p_R(r \mid g)$ there are **two** distributions to normalize, one per genre: given drama, $2/3$ for rating 4 and $1/3$ for rating 5; given comedy, $1/2$ each for ratings 1 and 5.

The general lesson: **to estimate a local conditional distribution, just ignore the other variables.** Estimating $p_G$ does not care about $R$. Each local distribution looks only at the small part of each assignment it involves ([≈20:58](../raw/transcripts/14-bayesian-networks-learning.md), [≈21:44](../raw/transcripts/14-bayesian-networks-learning.md)).

### V-structure: condition on all the parents together

Add a third variable $A \in \lbrace 0, 1 \rbrace$, whether the movie won an award: $G \to R \leftarrow A$, with $P(G = g, A = a, R = r) = p_G(g) \thinspace p_A(a) \thinspace p_R(r \mid g, a)$ ([≈21:44](../raw/transcripts/14-bayesian-networks-learning.md)–[≈26:23](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). V-structures were tricky for inference, but "for learning it's actually going to be pretty mundane." The pattern is the same for every local distribution: count how often its local assignment occurs. For $p_R(r \mid g, a)$ that means counting how often $G$, $A$ and $R$ occur together, with the parents' values **bundled** into one key.

The counts therefore have levels: which local distribution, then the value of the parents, then the value of the node itself, mapping to a count. With five examples ((drama, 0, 3), (drama, 1, 5), (drama, 0, 1), (comedy, 0, 5), (comedy, 1, 4)), the traced estimates are $p_G = \lbrace \text{drama}: 0.6, \text{comedy}: 0.4 \rbrace$, $p_A = \lbrace 0: 0.6, 1: 0.4 \rbrace$, and four distributions for the rating, one per parent combination: (drama, 0) gives 3 and 1 half each, and (drama, 1), (comedy, 0) and (comedy, 1) each put probability 1 on their single observed rating (5, 5 and 4).

The number of distributions you normalize equals the number of parent-value combinations. And remember to condition on **all the parents simultaneously**: "the parents work as a unit even though they look like individual nodes" ([≈25:37](../raw/transcripts/14-bayesian-networks-learning.md), [≈26:23](../raw/transcripts/14-bayesian-networks-learning.md)).

### Inverted v-structure

Now one genre and two users' ratings: $R_1 \leftarrow G \to R_2$, with $P(G = g, R_1 = r_1, R_2 = r_2) = p_G(g) \thinspace p_{R1}(r_1 \mid g) \thinspace p_{R2}(r_2 \mid g)$, one local conditional distribution per node ([≈26:23](../raw/transcripts/14-bayesian-networks-learning.md)–[≈29:37](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). The data is five (genre, user 1, user 2) triples: (drama, 4, 5), (drama, 4, 4), (drama, 5, 3), (comedy, 1, 2), (comedy, 5, 4). The counts for $G$ and $R_1$ ignore $R_2$, and vice versa. There are five distributions to normalize: $p_G$, and $p_{R1}$ and $p_{R2}$ for each of the two genres. The results: $p_G$ is again 0.6 and 0.4; user 1 given drama puts $2/3$ on 4 and $1/3$ on 5; user 2 given drama puts $1/3$ each on 5, 4 and 3; each user given comedy splits half and half between its two observed ratings.

"Hopefully you've seen enough examples that I should be able to give you any Bayesian network and you should be able to estimate" it ([≈29:37](../raw/transcripts/14-bayesian-networks-learning.md)).

## Parameter Sharing

Imagine a thousand users instead of two ([≈29:37](../raw/transcripts/14-bayesian-networks-learning.md)–[≈37:24](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). With $R_1$ through $R_{1000}$ each getting its own local distribution, a user who has rated only one movie gives you almost nothing to estimate their table from. The idea of **[parameter sharing](parameter-sharing.md)** is to estimate *one* distribution over rating given genre instead of a thousand.

Take the same network, $R_1 \leftarrow G \to R_2$, but power both ratings with one table:

$$P(G = g, R_1 = r_1, R_2 = r_2) = p_G(g) \thinspace p_R(r_1 \mid g) \thinspace p_R(r_2 \mid g)$$

The parameters are now just $\theta = (p_G, p_R)$, where before they were $(p_G, p_{R1}, p_{R2})$. This is where the subscripts become important: they index tables, not nodes ([≈31:10](../raw/transcripts/14-bayesian-networks-learning.md)).

**The picture to keep in mind.** So far the probabilities have been implicit, as if they lived in the edges. For learning, it matters where they come from. "The real way to think about this is that you have these local conditional tables that are floating out," and you "hook these tables up to nodes to power them, to give them probabilistic life." Here one table powers both rating nodes, where before two tables powered one node each ([≈31:57](../raw/transcripts/14-bayesian-networks-learning.md)). The slide's figure draws exactly this: a $p_G$ table with a red dashed arrow to $G$, and a single $p_R(r \mid g)$ table with two red dashed arrows, one to $R_1$ and one to $R_2$.

**Counting.** The counts belong to the **parameters**, not to the variables ([≈32:44](../raw/transcripts/14-bayesian-networks-learning.md), [≈33:32](../raw/transcripts/14-bayesian-networks-learning.md)). There are two tables, so two sets of counts, and both users' ratings are added into the same genre–rating counts. On the same five examples, drama now has six ratings (4 three times, 5 twice, 3 once) and comedy four (1, 2, 4 and 5 once each), so

$$p_R(\cdot \mid \text{drama}) = \lbrace 3: 1/6, 4: 3/6, 5: 2/6 \rbrace, \qquad p_R(\cdot \mid \text{comedy}) = \lbrace 1: 1/4, 2: 1/4, 4: 1/4, 5: 1/4 \rbrace$$

with $p_G$ unchanged at $3/5$ and $2/5$. Three distributions are normalized instead of five ([≈35:06](../raw/transcripts/14-bayesian-networks-learning.md)). It is "almost like" having twice as many examples for each table.

A student asks how this differs from merging the two rating nodes into one variable ([≈33:32](../raw/transcripts/14-bayesian-networks-learning.md), [≈34:19](../raw/transcripts/14-bayesian-networks-learning.md)). They are still two users, and the data still records genre, user 1's rating and user 2's rating; with one node there would be nowhere to write down both. Until now variables and distributions were one to one, and this is the first time they are split.

**Reading versus writing** ([≈35:06](../raw/transcripts/14-bayesian-networks-learning.md), [≈35:53](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). Probabilistic inference only **reads** the local conditional distributions, so it does not care whether $p(r_1 \mid g)$ and $p(r_2 \mid g)$ are the same table; they are just numbers. Learning **writes** to them, so it matters. The lecturer's analogy is passing an object by reference rather than by value in programming: with a reference, a mutation in one place shows up in the other.

**When to share** ([≈34:19](../raw/transcripts/14-bayesian-networks-learning.md), [≈36:38](../raw/transcripts/14-bayesian-networks-learning.md), [≈37:24](../raw/transcripts/14-bayesian-networks-learning.md)). Fewer parameters need fewer examples to learn: a thousand bespoke per-user distributions need a lot of data from each user. More parameters give greater flexibility to capture the nuances of users who really are different. Which to choose is a modeling decision. It depends on whether the users are similar, and on how much data you have: with a lot you can afford many parameters, and with little "you need to be very stingy with your parameters."

## Hidden Markov Models

The last example before the general case, and another one with parameter sharing, is the [hidden Markov model](hidden-markov-models.md), which the lecturer recalls from the first Bayesian networks lecture, where it was a probabilistic program ([≈37:24](../raw/transcripts/14-bayesian-networks-learning.md)–[≈43:39](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md); see [lecture 12](12-bayesian-networks-i.md)). There are two sequences of variables: hidden states $H_t \in \lbrace 0, 1 \rbrace$, the position of an object at time $t$, and observations (also called evidence or emissions) $E_t \in \lbrace 0, 1 \rbrace$, a sensor reading at time $t$. It is "hidden Markov" because the hidden states form a Markov chain. HMMs used to be used in speech recognition and computational biology, and can be used for object tracking. The figure draws $H_1 \to H_2 \to H_3$ along the top, each $H_t$ with an arrow down to its $E_t$.

As always, the joint distribution is each node given its parents:

$$P(H = h, E = e) = p_{\text{start}}(h_1) \thinspace p_{\text{emit}}(e_1 \mid h_1) \thinspace p_{\text{trans}}(h_2 \mid h_1) \thinspace p_{\text{emit}}(e_2 \mid h_2) \thinspace p_{\text{trans}}(h_3 \mid h_2) \thinspace p_{\text{emit}}(e_3 \mid h_3)$$

(The slide as taught prints the first emission factor as $p_E(e_1 \mid h_1)$; the course corrected it to $p_{\text{emit}}$ four days later.) There are only three types of parameters, $\theta = (p_{\text{start}}, p_{\text{trans}}, p_{\text{emit}})$. $p_{\text{start}}$ gives the distribution over $H_1$; $p_{\text{trans}}$ gives each hidden state given the previous one; $p_{\text{emit}}$ gives the sensor reading given the hidden state at the same time. Every time step uses the same emission table, and every step after the first the same transition table ([≈39:42](../raw/transcripts/14-bayesian-networks-learning.md), [≈40:28](../raw/transcripts/14-bayesian-networks-learning.md)). This is where the difference between parameters and variables becomes stark: the chain could be 100 steps long and there would still be only three local conditional distributions ([≈41:15](../raw/transcripts/14-bayesian-networks-learning.md)).

Learning assumes the fully observed case, where you do see the hidden states. The data is two sequences, $(h_1, e_1, h_2, e_2, h_3, e_3) = (0, 0, 1, 1, 0, 0)$ and $(0, 1, 0, 1, 0, 1)$. You keep three sets of counts, one per parameter, and walk each sequence: $H_1$ counts toward start, each $(H_t, E_t)$ pair toward emit, each $(H_{t-1}, H_t)$ pair toward trans ([≈41:15](../raw/transcripts/14-bayesian-networks-learning.md)–[≈42:48](../raw/transcripts/14-bayesian-networks-learning.md)). The traced estimates: $p_{\text{start}}(0) = 1$, since both sequences start at 0; from state 0 the transitions go to 0 with probability $2/3$ and to 1 with $1/3$, and from state 1 always back to 0; in state 0 the sensor reads 0 with probability 0.4 and 1 with 0.6, and in state 1 it always reads 1. The counts are sparse, so the numbers are not very interesting. The point is that **normalization no longer looks at the network's structure at all**; it just normalizes each local conditional distribution ([≈42:48](../raw/transcripts/14-bayesian-networks-learning.md)).

## The General Algorithm

The examples went from one variable to two, to three, to three with parameter sharing, to HMMs. The general story is the same recipe, with code that makes it general ([≈43:39](../raw/transcripts/14-bayesian-networks-learning.md)–[≈46:08](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)).

Let $D$ be the set of **types** of local conditional distributions; for the HMM, $D = \lbrace \text{start}, \text{trans}, \text{emit} \rbrace$. Think of $D$ as a set of labels. Its elements are not in general variables, because parameters and variables are two separate things. The parameters are one distribution per element of $D$: $\theta = \lbrace p_d : d \in D \rbrace$, here $\lbrace p_{\text{start}}, p_{\text{trans}}, p_{\text{emit}} \rbrace$. Each variable $X_i$ is generated from the distribution $p_{d_i}$, where $d_i \in D$ tells you "which one to go fetch":

$$P(X_1 = x_1, \ldots, X_n = x_n) = \prod_i p_{d_i}(x_i \mid x_{\text{parents}(X_i)})$$

Parameter sharing is simply two variables having the same type: $d_3 = d_7$, say ([≈45:17](../raw/transcripts/14-bayesian-networks-learning.md)).

In code, a `network_structure` dictionary maps each variable name to its parameter name and its list of parent variables: $H_1$ uses start with no parents, $H_2$ and $H_3$ use trans with parent $H_1$ and $H_2$, and each $E_t$ uses emit with parent $H_t$ ([≈46:08](../raw/transcripts/14-bayesian-networks-learning.md), [≈46:57](../raw/transcripts/14-bayesian-networks-learning.md)). A single function, `supervised_learning(network_structure, training_data)`, then does the rest ([≈47:42](../raw/transcripts/14-bayesian-networks-learning.md)–[≈51:40](../raw/transcripts/14-bayesian-networks-learning.md)):

- **Count.** For each example, and each variable in it, look up the variable's parameter name and parents, read off the parents' values from the same example as a tuple, and increment `counts[parameter_name][parents_value][value]`. For $E_1 = 0$ with $H_1 = 0$, that is `counts["emit"][(0,)][0]`. Where the hand-written examples kept `counts_g`, `counts_gr` and so on, this is one nested dictionary keyed first by parameter name.
- **Normalize.** For every parameter name and every parents value, normalize the distribution over the node's value.

On the HMM data it reproduces exactly the hand-computed numbers above. It is about 15 lines of code, and it estimates the parameters of any Bayesian network: "it's count and normalize, but you just have to do some bookkeeping." It assumes the network structure is known and fixed. There are methods that learn the structure too, but the class does not cover them ([≈51:40](../raw/transcripts/14-bayesian-networks-learning.md)).

A student asks why it is called *supervised* learning ([≈51:40](../raw/transcripts/14-bayesian-networks-learning.md), [≈52:27](../raw/transcripts/14-bayesian-networks-learning.md)). Because all the variables are observed. The lecturer says it would more accurately be called the **fully observable** setting, in contrast with the unsupervised, or partially supervised, case coming later. (Four days after the lecture the course renamed the function `fully_observable_learning`; see the slide file's "Changes after the lecture".)

## Why Count and Normalize Works: Maximum Likelihood

Count and normalize seems believable, "but it's more than that": it is exactly **[maximum likelihood](maximum-likelihood.md) estimation** ([≈52:27](../raw/transcripts/14-bayesian-networks-learning.md)–[≈55:37](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). The principle, used throughout statistics and machine learning, is to find the parameters that maximize the likelihood of the data:

$$\max_\theta \prod_x P(X = x; \theta) = \max_\theta \sum_x \log P(X = x; \theta)$$

where $x$ ranges over the training examples. Given any setting of the parameters, you can evaluate the likelihood by multiplying the probabilities of the data points; maximum likelihood picks the setting that drives it as high as possible. Taking the log is allowed because log is monotonic, and the sum of log probabilities is the negative cross entropy ([≈54:02](../raw/transcripts/14-bayesian-networks-learning.md), [≈54:48](../raw/transcripts/14-bayesian-networks-learning.md)). Training a large language model by minimizing cross entropy, or perplexity, "is exactly the same as maximum likelihood" ([≈53:14](../raw/transcripts/14-bayesian-networks-learning.md)).

Normally optimization means computing gradients and worrying about step sizes and convergence. Here, in the fully observed case, there is a **closed-form solution**, and it is count and normalize: no iterative optimization at all ([≈54:48](../raw/transcripts/14-bayesian-networks-learning.md)). The lecture justifies this with two examples rather than a proof.

**One variable** ([≈55:37](../raw/transcripts/14-bayesian-networks-learning.md)–[≈57:09](../raw/transcripts/14-bayesian-networks-learning.md)). With ratings 1, 5, 5, the objective is

$$\max_\theta \thinspace p_R(1) \thinspace p_R(5) \thinspace p_R(5) \quad \text{subject to} \quad p_R(1) + p_R(2) + p_R(3) + p_R(4) + p_R(5) = 1$$

Without going through the math formally: introduce a Lagrange multiplier for the sum-to-one constraint, set the gradient to zero and solve. Each probability comes out as the number of times that rating occurred over the total, here $p_R(1) = 1/3$ and $p_R(5) = 2/3$. Intuitively, you want to push up the probabilities of ratings you saw, more for the one seen twice, and you do not want to waste probability mass on ratings you never saw, like 4.

**Two variables** ([≈57:09](../raw/transcripts/14-bayesian-networks-learning.md)–[≈59:26](../raw/transcripts/14-bayesian-networks-learning.md)). With examples (drama, 4), (drama, 5), (comedy, 5), the likelihood is six factors, two per example:

$$p_G(\text{drama}) \thinspace p_R(4 \mid \text{drama}) \thinspace p_G(\text{drama}) \thinspace p_R(5 \mid \text{drama}) \thinspace p_G(\text{comedy}) \thinspace p_R(5 \mid \text{comedy})$$

Rearrange them into groups that actually have something to do with each other:

$$[p_G(\text{drama}) \thinspace p_G(\text{drama}) \thinspace p_G(\text{comedy})] \thinspace [p_R(4 \mid \text{drama}) \thinspace p_R(5 \mid \text{drama})] \thinspace [p_R(5 \mid \text{comedy})]$$

Each group involves different parameters, which can be set independently of the others, so this is three separate optimization problems, each solved by the one-variable case: count and normalize. "You can now sleep at night in comfort" that the intuitive counting is principled.

## Laplace Smoothing

Back to one variable, with only two ratings, 1 and 4 ([≈59:26](../raw/transcripts/14-bayesian-networks-learning.md)–[≈1:04:50](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). Maximum likelihood gives $p_R(1) = p_R(4) = 0.5$ and therefore $p_R(2) = 0$. Do we really believe a rating of 2 is impossible? After two data points that seems "a little bit close-minded"; it is probably just too little data. With few samples, maximum likelihood is **overfitting** to the data.

The fix, **[Laplace smoothing](laplace-smoothing.md)**, is "a very simple idea": add a quantity $\lambda$ to all the counts. Pretend you saw every rating $\lambda$ times before seeing the real data. These **pseudocounts** are added to the real counts, and then you normalize as usual. On the board, with $\lambda = 1$, the counts become 2, 1, 1, 2, 1 for ratings 1 to 5, so the estimates are $2/7$, $1/7$, $1/7$, $2/7$, $1/7$: still more weight on the ratings you saw, but nothing at zero ([≈1:01:46](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:02:32](../raw/transcripts/14-bayesian-networks-learning.md)).

The traced runs show the three properties the lecture names ([≈1:02:32](../raw/transcripts/14-bayesian-networks-learning.md)–[≈1:04:04](../raw/transcripts/14-bayesian-networks-learning.md)):

| pseudocount $\lambda$ | data | $p_R(1)$ | $p_R(2)$ | $p_R(3)$ | $p_R(4)$ | $p_R(5)$ |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | ratings 1, 4 | 0.2857 | 0.1429 | 0.1429 | 0.2857 | 0.1429 |
| 0 | ratings 1, 4 | 0.5 | 0 | 0 | 0.5 | 0 |
| 20 | ratings 1, 4 | 0.2059 | 0.1961 | 0.1961 | 0.2059 | 0.1961 |
| 20 | rating 4, 1,000 times | 0.0182 | 0.0182 | 0.0182 | 0.9273 | 0.0182 |

As $\lambda \to 0$ you get back the maximum likelihood estimate. As $\lambda \to \infty$ you approach the uniform distribution; at 20 it is "pretty much uniform," with slightly more weight on the two ratings seen. And whatever $\lambda$ is, more data washes it out: with a thousand 4s, $p_R(4)$ heads toward 1 and the rest toward 0.

In practice, the lecturer says, a smoothing of 1 or even 0.1 is about right; "I don't think you would use 20." You can think of the pseudocounts as almost like an initialization. They prevent zeros, which is to say overfitting, in the probability estimates ([≈1:04:04](../raw/transcripts/14-bayesian-networks-learning.md)).

## Expectation Maximization

"This is one of my favorite algorithms, because it sort of is a bit magical" ([≈1:04:50](../raw/transcripts/14-bayesian-networks-learning.md)–[≈1:18:52](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). So far the training data has consisted of full assignments. What if some variables are **unobserved**?

Take $R_1 \leftarrow G \to R_2$ again. Fully observed, with examples (drama, 1, 1) and (comedy, 2, 2), count and normalize gives $p_G$ split evenly, and drama always rates 1 and comedy always rates 2 ([≈1:04:50](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:05:40](../raw/transcripts/14-bayesian-networks-learning.md)). Now remove the genre: the data is just the rating pairs $(1, 1)$ and $(2, 2)$. You cannot count $G$, and every table depends on $G$, so "I can't even estimate anything."

**Back to the principle** ([≈1:06:28](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:07:15](../raw/transcripts/14-bayesian-networks-learning.md)). This is why principles help: ask what maximum likelihood would do. It says to maximize the likelihood of the **observed** data, whatever that is. Because the network defines the full joint distribution, the unseen variable can be marginalized out:

$$\max_\theta \sum_{(r_1, r_2)} \log P(R_1 = r_1, R_2 = r_2; \theta) = \max_\theta \sum_{(r_1, r_2)} \log \sum_g P(G = g, R_1 = r_1, R_2 = r_2; \theta)$$

where the outer sums run over the training examples. The players are the observed variables $R_1, R_2$, the unobserved variable $G$, and the parameters $\theta$, and we know neither $G$ nor $\theta$.

**A chicken-and-egg problem** ([≈1:08:00](../raw/transcripts/14-bayesian-networks-learning.md)–[≈1:09:30](../raw/transcripts/14-bayesian-networks-learning.md)). If you knew $\theta$, you could compute the distribution over the unobserved variables, $P(G = g \mid R_1 = r_1, R_2 = r_2; \theta)$. If you knew the unobserved variables, you would be back in the fully observed case and could count and normalize. So bootstrap: initialize $\theta$ randomly, then iterate two steps. The algorithm is **EM** (the slide cites Dempster et al., 1977). That it maximizes the likelihood, the lecturer asks the class to take on trust rather than showing it formally.

- **E-step**: "guess" the unobserved variables, meaning compute the distribution over every value the hidden variable could take, $P(G = g \mid R_1 = r_1, R_2 = r_2; \theta)$. This turns each example into a set of **weighted full assignments**, which look like fully observed data.
- **M-step**: count and normalize on those weighted full assignments.

**The worked example** ([≈1:09:30](../raw/transcripts/14-bayesian-networks-learning.md)–[≈1:16:35](../raw/transcripts/14-bayesian-networks-learning.md)). The `expectation_maximization` function is written for this one model, not in general, and it uses parameter sharing: one table $p_R(r \mid g)$ for both ratings. The initial parameters are $p_G = 0.5$ for each genre, $p_R(\cdot \mid \text{drama}) = \lbrace 1: 0.6, 2: 0.4 \rbrace$ and $p_R(\cdot \mid \text{comedy}) = \lbrace 1: 0.4, 2: 0.6 \rbrace$.

In the E-step, on the board, the lecturer replicates each example once per possible genre and gives each copy a weight: the probability of that genre given the observed ratings ([≈1:11:01](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:11:46](../raw/transcripts/14-bayesian-networks-learning.md)). (He first labels the genres 0 and 1, then relabels them comedy and drama.) Each weight is the joint divided by the probability of the evidence:

$$q(g) = P(G = g \mid R_1 = r_1, R_2 = r_2; \theta) = \frac{p_G(g) \thinspace p_R(r_1 \mid g) \thinspace p_R(r_2 \mid g)}{\sum_{g'} p_G(g') \thinspace p_R(r_1 \mid g') \thinspace p_R(r_2 \mid g')}$$

For the example $(1, 1)$, the unnormalized values are $0.5 \times 0.6 \times 0.6 = 0.18$ for drama and $0.5 \times 0.4 \times 0.4 = 0.08$ for comedy, which normalize to about 0.69 and 0.31; the lecturer reads them out as "0.69" and "0.3" ([≈1:13:21](../raw/transcripts/14-bayesian-networks-learning.md)). The example $(2, 2)$ gets the mirror image, with more weight on comedy. The weighted data set is four rows: $(1, 1)$ as drama with weight 0.69, $(1, 1)$ as comedy with 0.31, $(2, 2)$ as drama with 0.31, and $(2, 2)$ as comedy with 0.69 ([≈1:14:10](../raw/transcripts/14-bayesian-networks-learning.md)).

The M-step "should be familiar now": count and normalize, except that each row adds its **weight** instead of 1 ([≈1:14:58](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:15:46](../raw/transcripts/14-bayesian-networks-learning.md)). $p_R(1 \mid \text{drama})$ goes from 0.6 to 0.69. Iterating sharpens it further (traced values, rounded):

| iteration | weight on drama for $(1, 1)$ | $p_R(1 \mid \text{drama})$ after the M-step | $p_R(2 \mid \text{comedy})$ after the M-step |
| --- | --- | --- | --- |
| 0 | 0.6923 | 0.6923 | 0.6923 |
| 1 | 0.8351 | 0.8351 | 0.8351 |
| 2 | 0.9624 | 0.9624 | 0.9624 |
| 3 | 0.9985 | 0.9985 | 0.9985 |
| 4 | 1.0000 | 1.0000 | 1.0000 |

$p_G$ stays at 0.5 throughout. By iteration 4 the probabilities are "pretty close to one and zero" ([≈1:16:35](../raw/transcripts/14-bayesian-networks-learning.md)).

**What happened.** The model never sees the hidden variable, so it sets it in whatever way drives up the probability of the data. Seeing $(1, 1)$ and $(2, 2)$, it concludes that dramas always give 1 and comedies always give 2, which is the same as the fully observed data from before ([≈1:16:35](../raw/transcripts/14-bayesian-networks-learning.md)). The slide's gloss: the model learns hidden variables to explain the data, which is **clustering**, and EM amplifies initial preferences.

**A second example** ([≈1:17:21](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)). Eight examples: four of $(1, 1)$, two of $(2, 2)$, one $(1, 2)$ and one $(2, 1)$. After 10 iterations EM gives $p_G(\text{drama}) = 0.5692$ and $p_G(\text{comedy}) = 0.4308$, $p_R(1 \mid \text{drama}) = 0.9124$ and $p_R(2 \mid \text{comedy}) = 0.7548$, which the lecturer reads as "slightly more probability of drama," 0.9 and 0.75. These are not as sharp as 1 and 0, because the assignments are more heterogeneous.

**Notes on EM** ([≈1:18:07](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:18:52](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)):

- Each iteration is guaranteed to increase the likelihood, and EM converges to a **local** maximum, not necessarily the global one: it can get stuck in a local optimum. (The captions have the lecturer saying EM "can't get stuck," which the sentence before it and the slide contradict; see the transcript at 1:18:07.)
- You must initialize with a **non-uniform** distribution to break symmetries. "That's why I put 0.4 and 0.6 instead of 0.5 and 0.5." Starting exactly symmetric, the two genres would stay indistinguishable.
- The hidden variable can only be recovered **up to a permutation of the labels**: swapping drama and comedy gives the same likelihood, because they are just labels. That suits clustering, where the labels do not necessarily have a semantic meaning.

## Summary and What Comes Next

The summary ([≈1:18:52](../raw/transcripts/14-bayesian-networks-learning.md), [≈1:19:37](../raw/transcripts/14-bayesian-networks-learning.md), [slides](../raw/slides/14-bayesian-networks-learning.md)): the learning task is to estimate a Bayesian network's parameters from training data. The parameters are the local conditional distributions; with parameter sharing there can be fewer of them than there are variables. If you are worried about overfitting, use Laplace smoothing, which adds pseudocounts. Expectation maximization handles partially observed data: guess each unseen variable, weight each guess by its probability given the observed variables, count and normalize the weighted, now fully labeled data as in the supervised setting, and iterate.

That ends Bayesian networks. Next time ([lecture 15](15-logic-i.md)) is "something quite different": **logic**, which enables higher-order reasoning. Bayesian modeling is one kind of probabilistic reasoning; logic adds quantifiers, as in "every student has a TA", where the word *every* "requires a bit more sophistication" ([≈1:19:37](../raw/transcripts/14-bayesian-networks-learning.md)).

---

- **[Slides](../raw/slides/14-bayesian-networks-learning.md)**: the full content of `bayes_learning.py` as taught (pinned to commit 6ba9ac8, "add Bayes learning", which landed five minutes before the lecture), every traced count and estimate including all five EM iterations, prose descriptions of the six new figures, and the changes the course made four days after the lecture. Rendered lecture: <https://stanford-cs221.github.io/autumn2025-lectures/?trace=bayes_learning> (this may now show a later revision, not the version taught). The slide links three Autumn 2023 CS221 lectures on the same topics, on supervised learning, smoothing and the EM algorithm.
- **[Transcript](../raw/transcripts/14-bayesian-networks-learning.md)**: edited and timestamped. Verbatim captions are in `original/`.
- Topic pages: [maximum likelihood](maximum-likelihood.md), [parameter sharing](parameter-sharing.md), [hidden Markov models](hidden-markov-models.md), [Laplace smoothing](laplace-smoothing.md), [expectation maximization](expectation-maximization.md); also [Bayesian networks](bayesian-networks.md), [conditional independence](conditional-independence.md), [explaining away](explaining-away.md), [probabilistic inference](probabilistic-inference.md), [rejection sampling](rejection-sampling.md) and [Gibbs sampling](gibbs-sampling.md).
- Previous lecture: [Lecture 13 — Bayesian Networks and Gibbs Sampling](13-gibbs-sampling.md).
