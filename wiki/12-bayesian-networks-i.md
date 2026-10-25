---
title: Lecture 12 — Bayesian Networks I
---

# Lecture 12 — Bayesian Networks I

Lecture 12 opens the course's third unit. The state-based models of the last weeks (search, MDPs, games) let an agent plan, but they treat the state of the world as one opaque thing; [Bayesian networks](bayesian-networks.md) represent it as a set of **random variables** and reason about it under uncertainty ([≈3:59](../raw/transcripts/12-bayesian-networks-i.md)). The lecture reviews probability with every operation written as an `einsum` over a probability table, defines [probabilistic inference](probabilistic-inference.md) as asking a joint distribution for $P(\text{query} \mid \text{evidence})$, and builds two networks, an alarm and a medical diagnosis, in four steps each. Both show [explaining away](explaining-away.md): observing one cause of an effect lowers the probability of the other, even when the causes are independent. It then notes that an autoregressive language model is itself a Bayesian network, rewrites networks as **probabilistic programs**, and does approximate inference by [rejection sampling](rejection-sampling.md) ([≈1:16:18](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

Notation, used throughout the lecture: uppercase letters ($B$, $E$, $A$) are random variables and lowercase letters ($b$, $e$, $a$) their values. Lowercase $p$ is a **local conditional probability**, written down by definition; uppercase $P$ is a probability **derived** from the joint distribution by the laws of probability. Every variable in this lecture is binary, taking values in $\lbrace 0, 1 \rbrace$, except the positions in the hidden Markov model at the end.

## Where Bayesian Networks Fit

The lecture places the new unit on the course map ([≈0:05](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:39](../raw/transcripts/12-bayesian-networks-i.md)). An agent has to perceive, reason, act and learn. The course started with machine learning, which maps percepts straight to actions with a trained predictor, linear or a deep neural network. Then came **state-based models for reasoning**, because an intelligent agent cannot only react; it has to think or plan: [search problems](search-problems.md), where actions have deterministic outcomes; [MDPs](markov-decision-processes.md), where outcomes are stochastic but their distribution is known; and [games](games.md), where there is an adversary, or more generally an unknown opponent strategy.

Across those units the course used both kinds of method ([≈1:39](../raw/transcripts/12-bayesian-networks-i.md)–[≈3:59](../raw/transcripts/12-bayesian-networks-i.md)):

- **Model-free** methods know just enough to pick an action and say how much utility it will bring: classification, regression, SARSA, [Q-learning](q-learning.md), [TD learning](td-learning.md).
- **Model-based** methods understand how the world works, so they can plan ("if I take this action, this will happen"): search, [value iteration](value-iteration.md), [minimax](minimax.md).

Model-free methods are more direct and cheaper, which is why many practical applications use them; knowing what to do is a simpler problem than understanding the world. Model-based methods are more flexible: with a model you can change the reward function on the fly, keep the transitions and recompute the optimal policy. A Q-value "compiles down" rewards and transitions together, so it cannot tell whether an action is good because a good outcome is likely or because the expected outcome is better ([≈3:13](../raw/transcripts/12-bayesian-networks-i.md), [≈3:59](../raw/transcripts/12-bayesian-networks-i.md)). (Lecture 8 made the same contrast; see [reinforcement learning](reinforcement-learning.md#model-based-vs-model-free).)

If you buy into modelling the world, how should you represent its state? That "seems like a very daunting thing," and it is what Bayesian networks are about: how to represent the world, and how to reason about it in the face of uncertainty ([≈3:59](../raw/transcripts/12-bayesian-networks-i.md)).

## Probability, Written as Tensors

Bayesian networks are built on probability, so the lecture reviews it, "in a slightly maybe unorthodox way" ([≈4:46](../raw/transcripts/12-bayesian-networks-i.md)). See [probabilistic inference](probabilistic-inference.md) for the same material as a topic page.

The state of the world is represented by **random variables**, each an attribute of the world: $S$ for whether the sun is shining and $R$ for whether it is raining, each 1 or 0. An **assignment** gives every variable a value, so two binary variables give four possible states of the world ([≈5:33](../raw/transcripts/12-bayesian-networks-i.md), [≈6:19](../raw/transcripts/12-bayesian-networks-i.md)). A **joint distribution** gives each assignment a probability ([≈6:19](../raw/transcripts/12-bayesian-networks-i.md), [≈7:05](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

| $s$ | $r$ | $P(S = s, R = r)$ | reading |
| --- | --- | --- | --- |
| 0 | 0 | 0.20 | no sun, no rain ("probably, I don't know, nighttime") |
| 0 | 1 | 0.08 | rain, no sun |
| 1 | 0 | 0.70 | sunny and dry, high "because we're in California" |
| 1 | 1 | 0.02 | sunny and rainy, rare |

In code this is a `ProbTable`, a wrapper around a NumPy array whose first axis is $S$ and second is $R$; the lecture displays it unrolled into one row per assignment, because four-dimensional tensors are hard to draw ([≈7:05](../raw/transcripts/12-bayesian-networks-i.md)–[≈8:38](../raw/transcripts/12-bayesian-networks-i.md)).

**Marginalization** ignores a variable: it collapses all assignments that differ only in that variable and adds their probabilities. Marginalizing out $R$ gives $P(S = 0) = 0.20 + 0.08 = 0.28$ and $P(S = 1) = 0.70 + 0.02 = 0.72$; pictorially, the first two rows merge and so do the last two ([≈9:26](../raw/transcripts/12-bayesian-networks-i.md), [≈10:11](../raw/transcripts/12-bayesian-networks-i.md)). And "here comes the cool part": probability tables are just tensors, so [einops](einops.md) is back, and "all the laws of probability are essentially all covered by einops" ([≈10:57](../raw/transcripts/12-bayesian-networks-i.md)). The marginal

$$P(S = s) = \sum_r P(S = s, R = r) \quad \text{for all } s$$

is the one-liner `einsum(P_SR.p, "s r -> s")`: the input's axes are labelled `s r`, the output keeps `s`, and any axis that does not appear on the right is summed over ([≈11:45](../raw/transcripts/12-bayesian-networks-i.md), [≈12:32](../raw/transcripts/12-bayesian-networks-i.md)). (`.p` pulls the underlying tensor out of the table, "a bit of a notational nuisance.")

**Conditioning** on a partial assignment, such as $R = 1$ (you look outside and see rain), selects only the assignments that satisfy it. Three steps ([≈13:17](../raw/transcripts/12-bayesian-networks-i.md)–[≈15:39](../raw/transcripts/12-bayesian-networks-i.md)):

1. **Select** the rows consistent with the evidence: $P(S = 0, R = 1) = 0.08$ and $P(S = 1, R = 1) = 0.02$.
2. **Compute the probability of the evidence** by summing what is left: $P(R = 1) = 0.1$.
3. **Divide** by it: $P(S = 0 \mid R = 1) = 0.08 / 0.1 = 0.8$ and $P(S = 1 \mid R = 1) = 0.2$.

The division is a renormalization: the selected probabilities are small because rain itself is rare, and dividing by the evidence "blows them up" so they sum to one ([≈15:39](../raw/transcripts/12-bayesian-networks-i.md)). In einsum, the selection is done by an **indicator tensor** `R1 = [0, 1]`, which is 1 exactly where the constraint holds; multiplying it in zeroes out the other rows, and leaving `r` off the output drops the axis, since $R$ is always 1 after selection ([≈16:24](../raw/transcripts/12-bayesian-networks-i.md), [≈17:11](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

```python
R1 = np.array([0, 1])                                     # evidence R = 1
P_SR1 = einsum(P_SR.p, R1, "s r, r -> s")                 # select: P(S = s, R = 1)
P_R1 = einsum(P_SR1, "s ->")                              # probability of evidence: 0.1
P_S_given_R1 = P_SR1 / P_R1                               # divide: [0.8, 0.2]
```

A summing einsum with no output axes adds up every element ([≈17:58](../raw/transcripts/12-bayesian-networks-i.md)). Two student questions followed ([≈18:44](../raw/transcripts/12-bayesian-networks-i.md)). Does the final division need an einsum? No: the probability of the evidence is a scalar, so plain division does it. Does this work for continuous variables? Everything in this class uses discrete random variables, where einsum applies. With continuous variables, marginalization becomes integration, and conditioning gets "a little bit dicey," because you can end up conditioning on an event of probability zero.

The lecturer's summary of the intuitions ([≈19:30](../raw/transcripts/12-bayesian-networks-i.md), [≈20:16](../raw/transcripts/12-bayesian-networks-i.md)): the **joint distribution is the source of truth**, a probability for every state of the world, and everything else derives from it. Marginalization collapses assignments that differ only in the marginalized variables. Conditioning means you observe something, then select by the evidence, compute its probability and divide. Probability tables are tensors, so einops expresses all of it.

## Probabilistic Inference

With four variables, sunshine $S$, rain $R$, traffic $T$ and autumn $A$, the joint distribution is $P(S, R, T, A)$, and what you do with it is ask it questions: what is the probability of rain, given that there is traffic and it is autumn ([≈21:03](../raw/transcripts/12-bayesian-networks-i.md))? **Probabilistic inference** is answering such questions from the joint distribution. The analogy to keep in mind: the joint distribution is a **SQL database**, and probabilistic inference runs **queries** against it. There is a more formal relationship, which the lecture does not go into ([≈21:49](../raw/transcripts/12-bayesian-networks-i.md)).

Every question has the same shape ([≈22:35](../raw/transcripts/12-bayesian-networks-i.md), [≈23:21](../raw/transcripts/12-bayesian-networks-i.md)). **Evidence** is a set of variables fixed to observed values, here $T = 1, A = 1$. The **query** is the variable you want, here $R$. In notation the question is

$$P(R \mid T = 1, A = 1)$$

and every variable mentioned in neither the evidence nor the query, here $S$, is marginalized out. See [probabilistic inference](probabilistic-inference.md).

Nothing so far is specific to Bayesian networks. The point of a Bayesian network is to make it **easier to define the joint distribution** in the first place ([≈23:21](../raw/transcripts/12-bayesian-networks-i.md), [≈24:06](../raw/transcripts/12-bayesian-networks-i.md)).

## The Alarm: Four Steps to a Joint Distribution

The first example comes before any formal definition ([≈24:06](../raw/transcripts/12-bayesian-networks-i.md)–[≈26:30](../raw/transcripts/12-bayesian-networks-i.md)). Earthquakes and burglaries are independent events, each with probability $\epsilon = 0.05$ ("which is actually kind of high, but let's go with that"). Your home alarm goes off if there is an earthquake or a burglary. You are away on a trip, your phone tells you the alarm has gone off but not why, and then the news reports an earthquake near your house. How does hearing that change the probability of a burglary? The class was polled: down, up, or the same. Intuitively it should go down; but the two are supposed to be independent, so why would earthquakes have anything to do with burglaries? "There's a paradox here, which we're going to resolve" by defining a Bayesian network, after which "everything will become crystal clear." Formally, the question is which is larger, $P(B = 1 \mid A = 1)$ or $P(B = 1 \mid A = 1, E = 1)$.

Solving it takes two things: construct a joint distribution, then do probabilistic inference on it ([≈27:16](../raw/transcripts/12-bayesian-networks-i.md)). You could write the joint table out directly, since three binary variables have only $2^3 = 8$ assignments, but there is a more intuitive, modular way, in four steps ([≈27:16](../raw/transcripts/12-bayesian-networks-i.md)–[≈34:18](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

1. **Define the variables**: $B$ (is there a burglary?), $E$ (an earthquake?), $A$ (is the alarm going off?). "Usually that's the easy part."
2. **Connect them with directed edges**, which suggest direct influence: $B \to A$ and $E \to A$, since the alarm goes off if $B$ or $E$. The figure (`alarm-bayes.png`) draws $B$ and $E$ side by side with no edge between them, each with an arrow down into $A$. The lecturer is careful here: it is natural to say an edge means "causes," but causality is a tricky topic, and Bayesian networks do not automatically imply causality. Thinking of edges as causes is fine as intuition, but it is not formal ([≈28:48](../raw/transcripts/12-bayesian-networks-i.md)).
3. **Write down a local conditional distribution for each variable given its parents**: $p(b)$, $p(e)$ and $p(a \mid b, e)$. Each is a table sitting on a node. $p(B = 1) = p(E = 1) = 0.05$. The alarm is deterministic, written compactly as a function rather than a list of rows: `lambda b, e, a: a == (b or e)`, which gives probability 1 when $a$ equals $b \lor e$ and 0 otherwise ([≈30:22](../raw/transcripts/12-bayesian-networks-i.md)–[≈31:55](../raw/transcripts/12-bayesian-networks-i.md)).
4. **Define the joint distribution as the product** of the local conditional distributions:

$$P(B = b, E = e, A = a) = p(b) \thinspace p(e) \thinspace p(a \mid b, e) \quad \text{for all } b, e, a$$

Whenever you see a "for all" over a product of tensors like this, it is an einsum: `einsum(p_b, p_e, p_a_given_be, "b, e, b e a -> b e a")` ([≈32:44](../raw/transcripts/12-bayesian-networks-i.md), [≈33:32](../raw/transcripts/12-bayesian-networks-i.md)). The result has four nonzero entries ([slides](../raw/slides/12-bayesian-networks-i.md)): nothing happens with probability $0.9025$ ("nothing usually happens"), burglary only and earthquake only each have $0.0475$, and both together, with the alarm sounding, $0.0025$ ([≈34:18](../raw/transcripts/12-bayesian-networks-i.md)).

### Querying the alarm network

That joint distribution is the database; now run queries on it, each one starting from the joint and marginalizing and conditioning as needed ([≈35:03](../raw/transcripts/12-bayesian-networks-i.md)).

**$P(B)$, with no evidence.** Erase $E$ and $A$ from the einsum output to marginalize them out: $P(B = 1) = 0.05$, the same as the local conditional $p(b)$ ([≈35:50](../raw/transcripts/12-bayesian-networks-i.md), [≈37:25](../raw/transcripts/12-bayesian-networks-i.md)). This is where the lecturer stresses lowercase versus uppercase. $p(b)$ is just **defined**, written down by hand; $P(B)$ is **derived** from the joint by the laws of probability. They are mathematically different objects, and "it's not maybe obvious at first sight that they should be the same," though they turn out the same, which is why people often mix the two up ([≈35:50](../raw/transcripts/12-bayesian-networks-i.md)–[≈37:25](../raw/transcripts/12-bayesian-networks-i.md)).

**$P(B \mid A = 1)$, burglary given the alarm only** ([≈37:25](../raw/transcripts/12-bayesian-networks-i.md)–[≈39:44](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). One einsum both selects and marginalizes, "which is why einsum is so powerful": multiply in the indicator `a1 = [0, 1]` and keep only `b` in the output. That gives $P(B = 0, A = 1) = 0.0475$ and $P(B = 1, A = 1) = 0.05$, small numbers, because burglaries and alarms are both rare, and not summing to one. The probability of the evidence is their sum, $P(A = 1) = 0.0975$, and dividing by it gives

$$P(B = 1 \mid A = 1) = \frac{0.05}{0.0975} \approx 0.513$$

"A little bit over 50%": you see the alarm and think it could be a burglary, an earthquake or both, so "you should be really worried at this point." A reminder on reading such tables: a conditional distribution sums to one over the variables on the left of the bar, not those on the right ([≈39:44](../raw/transcripts/12-bayesian-networks-i.md)).

**$P(B \mid A = 1, E = 1)$, burglary given the alarm and the earthquake** ([≈40:32](../raw/transcripts/12-bayesian-networks-i.md), [≈41:17](../raw/transcripts/12-bayesian-networks-i.md)). Two indicator tensors now, `a1` and `e1`, in one einsum that zeroes out the rows where the evidence is false and drops $E$ and $A$: $P(B = 0, A = 1, E = 1) = 0.0475$ and $P(B = 1, A = 1, E = 1) = 0.0025$. The evidence has probability $0.05$, and

$$P(B = 1 \mid A = 1, E = 1) = \frac{0.0025}{0.05} = 0.05$$

The probability of burglary has gone back down to its prior. "The people who had kind of followed your intuition were correct."

## Explaining Away

So what happened to independence ([≈42:05](../raw/transcripts/12-bayesian-networks-i.md), [≈42:50](../raw/transcripts/12-bayesian-networks-i.md))? Ask why the alarm sounded: it must have been an earthquake or a burglary. Once you observe the earthquake, it **explains away** the alarm: it is probably the earthquake that set it off, which "takes the pressure off of the burglary and puts it on the earthquake." The key point is that $B$ and $E$ are independent **if you do not observe $A$**. Observing a variable downstream of two independent variables makes the upstream variables no longer independent. And "this is just math, so it's just true."

The general pattern ([≈43:37](../raw/transcripts/12-bayesian-networks-i.md), [≈44:23](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)): two causes ($B$, $E$) positively influence an effect ($A$). Condition on the effect ($A = 1$). Further conditioning on one cause ($E = 1$) reduces the probability of the other:

$$P(B = 1 \mid A = 1, E = 1) < P(B = 1 \mid A = 1)$$

and, the cool part, this holds **even if the causes are independent**. The lecturer calls explaining away "quite general and powerful," and "a defining feature of a Bayesian network." Part of why he thinks Bayesian networks matter: before people nailed these things down probabilistically, there were heuristic ways of reasoning about uncertainty that "were all sort of inconsistent and kind of broken," and mathematical clarity is what made them make sense ([≈43:37](../raw/transcripts/12-bayesian-networks-i.md)). See [explaining away](explaining-away.md).

The section closes with a summary ([≈45:56](../raw/transcripts/12-bayesian-networks-i.md), [≈46:42](../raw/transcripts/12-bayesian-networks-i.md)): define a joint distribution by the four-step procedure, an intuitive alternative to "slavishly going through and writing out every probability"; do probabilistic inference by selecting the rows that match the evidence, computing the probability of the evidence and dividing; and explaining away is a qualitative reasoning pattern that follows from the math.

## Medical Diagnosis: Explaining Away Through a Third Variable

The second network has four nodes and asks: if you are coughing and have itchy eyes, do you have a cold ([≈47:30](../raw/transcripts/12-bayesian-networks-i.md))? The lecturer prefaces it with "don't trust the medical sciences; this is just an example. Don't take this as medical advice." The variables are cold $C$, allergies $A$, cough $H$ and itchy eyes $I$, each simply yes or no, though in reality there are degrees ([≈48:17](../raw/transcripts/12-bayesian-networks-i.md)). Using "my very sketchy medical prior knowledge," cold and allergies are independent, with no edge between them; you cough when you have a cold or allergies; and you have itchy eyes only when you have allergies ([≈48:17](../raw/transcripts/12-bayesian-networks-i.md), [≈49:04](../raw/transcripts/12-bayesian-networks-i.md)). So the edges are $C \to H$, $A \to H$ and $A \to I$: cold and allergies are both parents of cough, and allergies alone is the parent of itchy eyes (figure `medical-bayes.png`, [slides](../raw/slides/12-bayesian-networks-i.md)).

The local conditional distributions ([≈49:53](../raw/transcripts/12-bayesian-networks-i.md)–[≈51:28](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)): a 10% chance of a cold, $p(C = 1) = 0.1$, and a 20% chance of allergies, $p(A = 1) = 0.2$. You cough with probability 0.9 if you have a cold or allergies and with probability 0.1 otherwise, so $p(H = 1 \mid c, a) = 0.9$ if $c = 1$ or $a = 1$, and $0.1$ if neither. Unlike the alarm's ones and zeros, these are softened "to hedge our bets a little bit." Itchy eyes agree with allergies with probability 0.9: $p(I = 1 \mid A = 1) = 0.9$ and $p(I = 1 \mid A = 0) = 0.1$. Multiplying the four together,

$$P(C = c, A = a, H = h, I = i) = p(c) \thinspace p(a) \thinspace p(h \mid c, a) \thinspace p(i \mid a),$$

gives "this pretty horrendous mess," a 16-row table, as the einsum `"c, a, c a h, a i -> c a h i"`. The lecture's convention is to write each table's conditioning variables first and its own variable last, so $p(h \mid c, a)$ has axes `c a h` ([≈51:28](../raw/transcripts/12-bayesian-networks-i.md), [≈52:13](../raw/transcripts/12-bayesian-networks-i.md)). Spot-checking it, the all-zero assignment, nothing wrong at all, has probability 0.5832, "most of the time," and assignments with many things wrong are much less likely ([≈52:13](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

**Cold given cough.** Select on $H = 1$ and collapse allergies and itchy eyes in one einsum, then divide by $P(H = 1) = 0.324$: $P(C = 1 \mid H = 1) = 0.09 / 0.324 \approx 0.28$ ([≈52:58](../raw/transcripts/12-bayesian-networks-i.md), [≈53:48](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

**Cold given cough and itchy eyes.** Before computing it, the lecturer polled the class: does adding itchy eyes increase, decrease or leave unchanged the probability of a cold? "A lot of tentative hands there, which is to be expected; that's why we have math" ([≈53:48](../raw/transcripts/12-bayesian-networks-i.md), [≈54:36](../raw/transcripts/12-bayesian-networks-i.md)). Selecting on $H = 1$ and $I = 1$ and dividing by $P(H = 1, I = 1) = 0.1764$ gives

$$P(C = 1 \mid H = 1, I = 1) = \frac{0.0234}{0.1764} \approx 0.13$$

so the probability of a cold has gone **down**, from about 0.27 to 0.13, "almost half" ([≈54:36](../raw/transcripts/12-bayesian-networks-i.md), [≈55:22](../raw/transcripts/12-bayesian-networks-i.md)). (The lecturer reads the first value as both 0.28 and 0.27; it is 0.2778.)

Why? It is explaining away again, one step removed ([≈55:22](../raw/transcripts/12-bayesian-networks-i.md), [≈56:09](../raw/transcripts/12-bayesian-networks-i.md)). Cough has two causes, cold and allergies. Itchy eyes are not a cause of the cough; there is no edge from $I$ to $H$. But $I$ is connected to $A$, so observing itchy eyes raises the probability of allergies, and allergies then explain away the cough, making a cold less likely. "I think this is really neat": you define the network and the reasoning patterns pop out, where hardly anyone would have had confident intuitions about how itchy eyes should change things ([≈56:09](../raw/transcripts/12-bayesian-networks-i.md), [≈56:54](../raw/transcripts/12-bayesian-networks-i.md)).

In general, imagine a 100-variable network with 17 evidence variables, all interacting. Intuitively, evidence **propagates**: observing one node suggests values for others, which propagate in turn. It can get complicated, and in general computing these quantities can be NP-hard ([≈56:54](../raw/transcripts/12-bayesian-networks-i.md), [≈57:40](../raw/transcripts/12-bayesian-networks-i.md)).

## Bayesian Networks in General

The general case is the same four steps ([≈57:40](../raw/transcripts/12-bayesian-networks-i.md), [≈58:26](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

1. Define a set of random variables $X = (X_1, \ldots, X_n)$.
2. Define any **directed acyclic graph** (DAG) over the variables: any graph, as long as it has no cycles.
3. For each node $X_i$, define a local conditional distribution $p(x_i \mid \text{parents}(x_i))$.
4. Define the joint distribution as the product of all of them:

$$P(X_1 = x_1, \ldots, X_n = x_n) = \prod_{i=1}^{n} p(x_i \mid \text{parents}(x_i))$$

(The slide writes the left-hand side as $P(X_1, \ldots, X_n)$.) Three reminders, "because it's easy to make this mistake" ([≈58:26](../raw/transcripts/12-bayesian-networks-i.md), [≈59:12](../raw/transcripts/12-bayesian-networks-i.md)):

- There is **one local conditional distribution per node, not per edge**. Do not forget $p(c)$ just because no edge points into $C$.
- A local conditional distribution depends on **all its parents at once**. Writing $p(h \mid c) \thinspace p(h \mid a)$ instead of $p(h \mid c, a)$ is incorrect; the parents have to be tied together. "In the literature they call this: the parents are married."
- Know the difference between the local conditional distributions $p$ and the distributions $P$ derived from the joint.

**Probabilistic inference in general** ([≈59:12](../raw/transcripts/12-bayesian-networks-i.md), [≈59:58](../raw/transcripts/12-bayesian-networks-i.md)): given a Bayesian network $P(X_1, \ldots, X_n)$, evidence $E = e$ for some subset $E \subseteq X$ (for example, $X_3 = 0$ and $X_7 = 1$), and a query $Q \subseteq X$ (for example, $X_2$ and $X_4$), output the table $P(Q \mid E = e)$, a probability for every assignment to $Q$. Every variable not mentioned, here $X_1$, $X_5$ and $X_6$, is marginalized out. See [Bayesian networks](bayesian-networks.md).

## Language Models Are Bayesian Networks

Bayesian networks show up in many places where you would not think of them ([≈1:00:43](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:02:15](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). Put on "Bayesian network hats," and an **autoregressive language model** is one:

1. The random variables are the tokens $X_1, \ldots, X_T$.
2. Each token has edges from all previous tokens, $X_1, \ldots, X_{t-1} \to X_t$.
3. The local conditional distribution $p(x_t \mid x_1, \ldots, x_{t-1})$ is given by the Transformer.
4. The joint distribution is the product, $P(X_1, \ldots, X_T) = \prod_t p(x_t \mid x_1, \ldots, x_{t-1})$.

Drawn as a network (on the board), every variable is connected to all previous ones. Normally you only **sample forward**: given a prompt $X_1, X_2, X_3$, sample $X_4$, then $X_5$, then $X_6$ ([≈1:01:30](../raw/transcripts/12-bayesian-networks-i.md), [≈1:02:15](../raw/transcripts/12-bayesian-networks-i.md)). But probabilistic inference lets you reason about any subset of variables given any other, so you could go **backwards**: given a response $X_4, X_5, X_6$, which prompt generated it? That has an application in **jailbreaking** language models: given a response such as "Sure, here's how you make a bomb," find the prompts likely to have produced it ([≈1:02:15](../raw/transcripts/12-bayesian-networks-i.md), [≈1:03:03](../raw/transcripts/12-bayesian-networks-i.md)). The lecture links Li et al., [*Eliciting Language Model Behaviors with Investigator Agents*](https://arxiv.org/abs/2502.01236) (2025), of which the lecturer is a co-author.

This is uncommon, mostly because language models are designed to go forward, but once you define a joint distribution "you can mathematically go in any order you want," and other orders can give interesting insights. The downside is cost: the backward problem is extremely expensive computationally and "in general exponentially hard," and the lecturer describes the paper as having ideas on how to make it faster using variational inference ([≈1:03:03](../raw/transcripts/12-bayesian-networks-i.md), [≈1:03:49](../raw/transcripts/12-bayesian-networks-i.md)). [Lecture 17](17-language-models.md) derives the same chain-rule factorization from the language-model side, and shows the jailbreaks that safety tuning tries to prevent; see [language models](language-models.md) and [post-training](post-training.md).

## Probabilistic Programs and Rejection Sampling

The last ten minutes give "a different way to think about Bayesian networks," which makes approximate inference easy ([≈1:03:49](../raw/transcripts/12-bayesian-networks-i.md), [≈1:04:34](../raw/transcripts/12-bayesian-networks-i.md)). So far networks were defined by writing down local conditional distributions, already better than writing the whole table, which no one does for many variables. Now they are defined by **probabilistic programs**; probabilistic programming is a whole field exploring the interaction between programs and distributions. The alarm becomes a function that returns one random sample from the joint distribution ([≈1:04:34](../raw/transcripts/12-bayesian-networks-i.md), [≈1:05:21](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

```python
def alarm():
    B = Bernoulli(0.05)  # Burglary
    E = Bernoulli(0.05)  # Earthquake
    A = B or E           # Alarm
    return {"B": B, "E": E, "A": A}
```

where `Bernoulli(prob)` returns 1 with probability `prob` and 0 otherwise. "This is actually a formal definition of a Bayesian network," just an unusual one, and the lecturer likes how transparent it is: three lines of code ([≈1:06:07](../raw/transcripts/12-bayesian-networks-i.md)). Sampling it repeatedly mostly gives $\lbrace B = 0, E = 0, A = 0 \rbrace$, since the events are rare; in the traced run all ten calls did ([≈1:05:21](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

A program gives a natural way to do approximate inference: **rejection sampling** ([≈1:06:53](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:09:14](../raw/transcripts/12-bayesian-networks-i.md)). Draw a ton of samples, keep the ones that match the evidence, and record a histogram of the query over the kept samples. Evidence and query are generalized to **arbitrary functions of a sample**: the query plucks out a value (`sample["B"]`), and the evidence tests whether a sample satisfies a criterion (`sample["A"] == 1`). Rejection sampling is then "literally five lines of code, for some definition of five": loop, sample, test the evidence, increment a count for the query value, and finally normalize the counts.

For $P(B \mid A = 1)$ the results match the trace in the slide file ([≈1:08:26](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:10:00](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

- With 10 samples, only one sample passed the evidence (it had a burglary), so the estimate is $P(B = 1 \mid A = 1) = 1$: "not very interesting."
- With 1,000 samples, the estimate is $0.57$ (51 samples kept), against the exact $0.51$: "close, but not exactly right, because this is just a sampling algorithm." The lecturer reads it as 0.56.

The algorithm is inefficient when the evidence is rare, because it is sampling from the prior: if the evidence has probability 0.001, almost every sample is wasted. But it is mathematically clean, with a guarantee that as the number of samples goes to infinity the estimate converges to the true probability ([≈1:10:00](../raw/transcripts/12-bayesian-networks-i.md)). Sampling to estimate expectations is a recurring move in the course, as in policy evaluation ([≈1:10:00](../raw/transcripts/12-bayesian-networks-i.md), [≈1:10:45](../raw/transcripts/12-bayesian-networks-i.md); see [Monte Carlo evaluation](monte-carlo-evaluation.md)).

**Medical diagnosis** as a program draws $C$, $A$, then $H$ with probability 0.9 if $C$ or $A$ else 0.1, then $I$ with probability 0.9 if $A$ else 0.1. With 200 samples, rejection sampling estimates $P(C = 1 \mid H = 1) \approx 0.27$ (the lecturer reads 0.26), close to the exact 0.28. It is closer than the alarm estimate even with fewer samples, because the evidence is more probable (67 of the 200 samples were kept) ([≈1:10:45](../raw/transcripts/12-bayesian-networks-i.md), [≈1:11:33](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

**A [hidden Markov model](hidden-markov-models.md) for object tracking** shows how succinct probabilistic programs can be ([≈1:11:33](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:13:59](../raw/transcripts/12-bayesian-networks-i.md)). $H_1, H_2, \ldots$ are an object's positions at successive times, which you do not see; instead you get a noisy sensor reading $E_t$ of each. The figure (`hmm.png`) draws the hidden positions as a chain $H_1 \to H_2 \to \cdots \to H_5$, each with an arrow down to its reading $E_t$, and shades the $E_t$ to mark them as observed.

The program runs five steps. At each one the object stays put with probability 0.5 or advances one position with probability 0.5, starting from 0, and the sensor reports the true position plus 0 or 1, again with probability 0.5 each ([≈1:12:20](../raw/transcripts/12-bayesian-networks-i.md), [≈1:13:09](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). The question: where was the object at time 3, given that the sensor read 2 at time 5, $P(H_3 \mid E_5 = 2)$? In the code that is `sample["H"][2]` given `sample["E"][4] == 2`, because Python lists are zero-based. Computing this exactly would be "fairly complicated," but rejection sampling handles it as a black box. With 200 samples, the estimate puts the object most likely at position 1 (about 0.70), maybe at 0 (about 0.23), rarely at 2 (about 0.08) ([≈1:13:59](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)).

The takeaway ([≈1:13:59](../raw/transcripts/12-bayesian-networks-i.md), [≈1:14:45](../raw/transcripts/12-bayesian-networks-i.md)): probabilistic programming treats programs as representations of distributions, a different way of thinking about probability with attractive features, because once you can sample you can do a lot, such as estimating any query by rejection sampling. The slide's verdict on rejection sampling: very flexible and very slow. See [rejection sampling](rejection-sampling.md).

## Discussion: A Shift in Mindset from Classifiers

If you are used to building classifiers, Bayesian networks require a shift in mindset ([≈1:14:45](../raw/transcripts/12-bayesian-networks-i.md), [≈1:15:32](../raw/transcripts/12-bayesian-networks-i.md)). To predict a cold from a cough, traditional machine learning trains a classifier from input (cough) to output (cold). A Bayesian network often goes the other way, from **output (and hidden variables) to input**: it describes how a cold generates the things you observe, and probabilistic inference runs it backwards.

Why do it this way? Four advantages ([≈1:15:32](../raw/transcripts/12-bayesian-networks-i.md), [≈1:16:18](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

- **Missing information.** If the doctor did not record whether you had itchy eyes, a Bayesian network takes that into account automatically. The slide says it handles information missing in different ways from one example to the next, at training and at test time.
- **Prior knowledge.** If you know how something works, such as what your alarm system reacts to, you can write it down instead of learning it. (The slide's examples are Mendelian inheritance and the laws of physics.)
- **Interpretability.** Every intermediate variable can be interpreted, which is often missing from big end-to-end systems.
- **Causality.** Bayesian networks are a precursor to causal models, which can answer questions about interventions and counterfactuals. That is beyond the scope of this class.

## Summary and What Comes Next

The lecture's summary ([≈1:16:18](../raw/transcripts/12-bayesian-networks-i.md), [≈1:17:06](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)): probability is joint distributions, marginalization and conditioning, and since probability tables are tensors, einsum expresses all of it. Bayesian networks, directed acyclic graphs of random variables, are a convenient way to define joint distributions. Probabilistic inference answers questions of the form $P(\text{query} \mid \text{evidence})$. Probabilistic programs represent joint distributions as programs, and rejection sampling is a simple, generic way to do inference in any Bayesian network, but it is very slow. Next time: better probabilistic inference algorithms and alternatives, which [lecture 13](13-gibbs-sampling.md) delivers as [Gibbs sampling](gibbs-sampling.md), along with [conditional independence](conditional-independence.md).

---

- **[Slides](../raw/slides/12-bayesian-networks-i.md)**: the full content of `bayes.py` as taught (pinned to commit f22938e, "add bayes", which landed four minutes before the lecture), every traced probability table and sampling result, and descriptions of the five figures. Rendered lecture: <https://stanford-cs221.github.io/autumn2025-lectures/?trace=bayes> (this may now show a later revision, not the version taught).
- **[Transcript](../raw/transcripts/12-bayesian-networks-i.md)**: edited and timestamped. Verbatim captions are in `original/`.
- Topic pages: [Bayesian networks](bayesian-networks.md), [probabilistic inference](probabilistic-inference.md), [explaining away](explaining-away.md), [rejection sampling](rejection-sampling.md); also [einops](einops.md), for the einsum notation used throughout.
