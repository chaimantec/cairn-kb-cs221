---
title: Conditional Independence — Reading Independence Off a Bayesian Network's Graph
---

# Conditional Independence

A [Bayesian network](bayesian-networks.md) is two things at once: a **probabilistic** object, a distribution over a set of variables, and a **combinatorial** one, a graph. Lecture 13 connects them through **independence**: some independence facts about the distribution can be read off the graph alone, and the lecture gives a procedure for doing so ([lecture 13](13-gibbs-sampling.md), [≈58:16](../raw/transcripts/13-gibbs-sampling.md)–[≈59:50](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

Notation, as in the lecture: uppercase letters are random variables and lowercase letters their values; lowercase $p$ is a local conditional probability from the network's definition, uppercase $P$ a probability derived from the joint distribution.

## Definitions

Two random variables $A$ and $B$ are **independent** if and only if

$$P(A = a, B = b) = P(A = a) \thinspace P(B = b) \text{ for all } a, b$$

They are **conditionally independent given $C = c$** if and only if the same holds with $C = c$ conditioned on in every term ([≈59:50](../raw/transcripts/13-gibbs-sampling.md), [≈1:06:48](../raw/transcripts/13-gibbs-sampling.md), [≈1:07:35](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

$$P(A = a, B = b \mid C = c) = P(A = a \mid C = c) \thinspace P(B = b \mid C = c) \text{ for all } a, b$$

## Four Small Networks

The lecture's examples, numbered as on the slide ([≈1:00:37](../raw/transcripts/13-gibbs-sampling.md)–[≈1:10:44](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). The slide writes example 3 as "A, B → C" and example 4 as "C → A, B".

| Example | Graph | Joint distribution | $A$, $B$ independent? | Given $C$? |
| --- | --- | --- | --- | --- |
| 1 | $A$ and $B$, no edge | $p(a) \thinspace p(b)$ | yes | — |
| 2 | $A \to B$ | $p(a) \thinspace p(b \mid a)$ | no | — |
| 3 | $A \to C \leftarrow B$ | $p(a) \thinspace p(b) \thinspace p(c \mid a, b)$ | **yes** | **no** |
| 4 | $A \leftarrow C \to B$ | $p(c) \thinspace p(a \mid c) \thinspace p(b \mid c)$ | **no** | **yes** |

**Example 1** is "the world's second simplest Bayesian network," and its joint pattern-matches the definition. Two variables with no connection are independent, which is "generally the right intuition," though it gets more complicated ([≈1:00:37](../raw/transcripts/13-gibbs-sampling.md), [≈1:01:23](../raw/transcripts/13-gibbs-sampling.md)). **Example 2**: $B$ depends on $A$, so they are not independent ([≈1:01:23](../raw/transcripts/13-gibbs-sampling.md)).

**Example 3** is the tricky one: $A$ and $B$ are connected through $C$, yet independent. Marginalizing $C$ out ([≈1:02:09](../raw/transcripts/13-gibbs-sampling.md)–[≈1:03:43](../raw/transcripts/13-gibbs-sampling.md)):

$$P(A = a, B = b) = \sum_c p(a) \thinspace p(b) \thinspace p(c \mid a, b) = p(a) \thinspace p(b) \sum_c p(c \mid a, b) = p(a) \thinspace p(b)$$

because a local conditional distribution summed over all values of the variable to the left of the bar is one. So "you can't just only look at whether two variables are connected or not," but this is "the only real special case that you have to think about." Intuitively, two independent causes stay independent when someone adds a variable that depends on both ([≈1:03:43](../raw/transcripts/13-gibbs-sampling.md), [≈1:04:29](../raw/transcripts/13-gibbs-sampling.md)). (The slide's first line for this example prints the factor as $p(b \mid a)$; the lecturer says $p(b)$, as does the slide's next line.)

**Example 4** looks like example 3 upside down, and the class was polled on it. "Don't be fooled": the direction matters, and they are "almost completely different Bayesian networks." Here $P(A = a, B = b) = \sum_c p(c) \thinspace p(a \mid c) \thinspace p(b \mid c)$, which does not simplify ([≈1:05:16](../raw/transcripts/13-gibbs-sampling.md), [≈1:06:02](../raw/transcripts/13-gibbs-sampling.md)). A check worth having: think of a special case. If $C$ is a coin flip and $A = C$ and $B = C$, then $A$ literally equals $B$ ([≈1:06:48](../raw/transcripts/13-gibbs-sampling.md)).

**Conditioning flips both.** In example 4, given $C$ the conditional is the joint divided by $p(c)$, which leaves exactly $p(a \mid c) \thinspace p(b \mid c)$: once you know the coin flip, $A$ and $B$ are doing independent things ([≈1:07:35](../raw/transcripts/13-gibbs-sampling.md), [≈1:08:23](../raw/transcripts/13-gibbs-sampling.md)). In example 3, given $C$ the three variables are tied together in the arbitrary function $p(c \mid a, b)$, so the conditional does not factor ([≈1:09:09](../raw/transcripts/13-gibbs-sampling.md), [≈1:09:56](../raw/transcripts/13-gibbs-sampling.md)). The lecture's summary of the pair: in example 3, $A$ and $B$ are **marginally independent but not conditionally independent**; in example 4, **marginally dependent but conditionally independent**. "This is a good primitive to have in your head" ([≈1:09:56](../raw/transcripts/13-gibbs-sampling.md), [≈1:10:44](../raw/transcripts/13-gibbs-sampling.md)).

**The alarm network is example 3.** Burglary $B$ and earthquake $E$ are independent, but not conditionally independent given that the alarm went off, $A = 1$, "which is how you get [explaining away](explaining-away.md)" ([≈1:10:44](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

## A General Procedure

To decide whether $A$ and $B$ are independent given a set of variables $C$ in a large network ([≈1:10:44](../raw/transcripts/13-gibbs-sampling.md)–[≈1:12:16](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

1. **Shade** the variables in $C$. Conditioning on something is "kind of like blocking a path."
2. **Recursively remove any non-shaded leaves.** This handles the case where $A$ and $B$ are really independent but "there's some things here that makes it look like it's not independent." You need not erase them; just think of them as not there.
3. **Connect the parents to each other** ("marrying the parents").
4. **Return whether there is a path from $A$ to $B$ that doesn't go through any shaded nodes.** If there is none, they are independent given $C$.

(The source numbers the last two steps "3." and "3.") The lecture's summary describes this as reducing conditional independence to a graph property: whether the two variables are connected "after a bunch of processing on the graph" ([≈1:15:27](../raw/transcripts/13-gibbs-sampling.md)).

## Worked Example: Medical Diagnosis

The network from lecture 12: cold $C$ and allergies $A$ both cause cough $H$, and allergies cause itchy eyes $I$ (edges $C \to H$, $A \to H$, $A \to I$). Worked in class ([≈1:12:16](../raw/transcripts/13-gibbs-sampling.md)–[≈1:13:50](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

- **$C$ and $A$ are independent.** With nothing shaded, $H$ and $I$ are unshaded leaves and are removed, leaving $C$ and $A$ with no path between them.
- **$C$ and $I$ are independent.** $H$ is an unshaded leaf and is removed, leaving $C$ on its own and the subgraph $A \to I$.
- **$C$ and $I$ are independent given $A$.** Shading $A$ "is essentially blocking off the way to go over there."
- **$C$ and $I$ are independent given $H$ and $A$.** Shading $H$ as well, the lecturer says the two are "also kind of blocked" (the transcript does not spell this case out further; the slide's line is "C and I are independent given H, A").

The slide also listed "C and H are independent given A." It was not discussed in class, and it is not true of this network: $C$ is a parent of $H$, so they are joined by an edge that no shading can block. The course deleted the line the evening after the lecture ([slides](../raw/slides/13-gibbs-sampling.md), "Changes after the lecture"). The lecturer suggested doing more of these examples in the next lecture ([≈1:13:50](../raw/transcripts/13-gibbs-sampling.md)).

## Related Pages

- [Lecture 13](13-gibbs-sampling.md) — the lecture, with the four examples, the polls, and the procedure
- [Explaining away](explaining-away.md) — what the failure of conditional independence in example 3 looks like with numbers
- [Bayesian networks](bayesian-networks.md) — the alarm and medical-diagnosis networks
- [Markov blanket](markov-blanket.md) — the other place lecture 13 uses the graph's structure
