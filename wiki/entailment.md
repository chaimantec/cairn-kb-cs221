---
title: Entailment, Contradiction and Contingency — and the Ask and Tell Operations
---

# Entailment, Contradiction and Contingency

When you add a formula $f$ to a knowledge base, the set of possible worlds can only shrink. **Entailment**, **contradiction** and **contingency** are the three ways it can shrink: not at all, all the way to nothing, or somewhere in between. They are the core semantic relations of logic, and the answers a knowledge base gives to the **Ask** and **Tell** operations are just these three relations under other names. They are introduced for [propositional logic](propositional-logic.md) in [lecture 15](15-logic-i.md), but "these notions are not specific to propositional logic. These are general ideas in logic" ([≈31:10](../raw/transcripts/15-logic-i.md)–[≈38:05](../raw/transcripts/15-logic-i.md), [≈1:12:42](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)).

Notation, as in the lecture: KB is a knowledge base (a set of formulas); $f$ is a formula; $\mathcal{M}(\text{KB})$ is the set of models (possible worlds) that satisfy every formula in KB, and $\mathcal{M}(f)$ the set of models of $f$. $\text{KB} \cup \lbrace f \rbrace$ is the knowledge base with $f$ added, and $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace) = \mathcal{M}(\text{KB}) \cap \mathcal{M}(f)$. The examples use two propositional symbols, $\text{Rain}$ and $\text{Wet}$, and write each model as a (Rain, Wet) pair of 0s and 1s.

## How Much Does the Set of Models Shrink?

Adding a formula makes the knowledge base bigger, $\text{KB} \subseteq \text{KB} \cup \lbrace f \rbrace$, and its set of models smaller, $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace) \subseteq \mathcal{M}(\text{KB})$, because the new formula is one more constraint on the possible worlds ([≈29:39](../raw/transcripts/15-logic-i.md), [≈30:25](../raw/transcripts/15-logic-i.md)). "We know it can't grow. So how much does it shrink?" ([≈31:10](../raw/transcripts/15-logic-i.md)). Compare $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace)$ with $\mathcal{M}(\text{KB})$:

| Relation | Definition | Intuition | Lecture's example |
| --- | --- | --- | --- |
| Entailment, $\text{KB} \models f$ | $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace) = \mathcal{M}(\text{KB})$ | $f$ adds no information | KB = Rain, Rain → Wet; $f$ = Rain |
| Contradiction | $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace) = \emptyset$ | $f$ is incompatible with KB | KB = Rain, Wet; $f$ = not Wet |
| Contingency | neither of the above | $f$ shrinks the set, but not to nothing | KB = Rain; $f$ = Wet |

**Entailment** ([≈31:10](../raw/transcripts/15-logic-i.md)–[≈33:29](../raw/transcripts/15-logic-i.md)). KB entails $f$ if and only if adding $f$ changes nothing. Pictorially, $\mathcal{M}(\text{KB})$ lies entirely inside $\mathcal{M}(f)$, which may be much larger; "no matter what state of the world we are in that's consistent with the knowledge base, $f$ is also true." In the example, both before and after adding Rain the only model is $(1, 1)$. The symbol $\models$, with two horizontal lines, is read "entails."

**Contradiction** ([≈33:29](../raw/transcripts/15-logic-i.md)–[≈35:00](../raw/transcripts/15-logic-i.md)). KB contradicts $f$ if and only if adding $f$ leaves no models at all: no possible world makes both true, and the two sets of models are disjoint. This is the most the set can shrink. In the example, the KB's only model is $(1, 1)$, which has Wet true, so adding $\neg \text{Wet}$ leaves the empty set.

**Contingency** ([≈35:00](../raw/transcripts/15-logic-i.md)–[≈36:33](../raw/transcripts/15-logic-i.md)). $f$ is contingent with respect to KB if and only if

$$\emptyset \neq \mathcal{M}(\text{KB} \cup \lbrace f \rbrace) \neq \mathcal{M}(\text{KB})$$

The two sets partly overlap: you have "learned that we are here instead of somewhere over here." This is "the more interesting case, at least when you're adding information." In the example, knowing only Rain leaves $(1, 0)$ and $(1, 1)$; adding Wet leaves $(1, 1)$.

## Entailment and Contradiction Are Linked

KB contradicts $f$ if and only if $\text{KB} \models \neg f$ ([≈36:33](../raw/transcripts/15-logic-i.md), [≈37:18](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). The models of $\neg f$ are exactly the worlds outside $\mathcal{M}(f)$. So $\mathcal{M}(\text{KB})$ fits inside $\mathcal{M}(f)$ (entailment of $f$) exactly when it misses $\mathcal{M}(\neg f)$ (contradiction of $\neg f$). Read the other way, KB entails $f$ if and only if KB contradicts $\neg f$. This is what lets all three relations be decided by a [satisfiability](satisfiability.md) checker.

## Ask and Tell

A knowledge base supports two operations, and each one's response depends only on which of the three relations holds ([≈38:51](../raw/transcripts/15-logic-i.md)–[≈47:20](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). **Ask** poses a yes/no question $f$; propositional logic supports only yes/no questions because propositions are boolean. **Tell** offers a new statement $f$.

| Relation | Ask[f] | Tell[f] |
| --- | --- | --- |
| Entailment | Yes | I already knew that |
| Contradiction | No | I don't buy that |
| Contingency | I don't know | I learned something new (and adds $f$) |

The lecture's traced examples ([≈40:26](../raw/transcripts/15-logic-i.md)–[≈45:49](../raw/transcripts/15-logic-i.md)):

- Knowing it is raining and wet, Ask "is it raining or wet?" gives **Yes**: in every world consistent with the KB, $f$ is true.
- Knowing it is not raining but wet, Ask "is it raining?" gives **No**: "it's inconceivable that $f$ is true."
- Knowing only that it is raining, Ask "is it wet?" gives **I don't know**: it depends which remaining world we are in.
- Knowing Rain and Rain → Wet, Tell "it's wet" gives **I already knew that**.
- Knowing Rain and Wet, Tell "it's not raining" gives **I don't buy that**, and the KB is unchanged.
- Knowing Rain, Tell "it's not wet" gives **I learned something new**, and the KB becomes Rain, not Wet.

To answer a query, "we're going to pretend to add it into the KB and see what changes" ([≈41:58](../raw/transcripts/15-logic-i.md)). Ask and Tell do the same computation; the only differences are the string returned and whether a contingent $f$ is actually added. They are kept separate as "more of a user interface thing" ([≈46:34](../raw/transcripts/15-logic-i.md), [≈47:20](../raw/transcripts/15-logic-i.md), [≈50:21](../raw/transcripts/15-logic-i.md), [≈51:08](../raw/transcripts/15-logic-i.md)).

The lecture implements both by enumerating every model, which is exponential. That is "just a proof of concept for understanding"; the real way is two calls to a [satisfiability](satisfiability.md) solver ([≈1:03:21](../raw/transcripts/15-logic-i.md)). Ask carries over unchanged to [first-order logic](first-order-logic.md): [lecture 16](16-logic-ii.md) opens with a first-order knowledge base whose answers to "Is it snowing?" move from "I don't know" to "No" as facts are added.

## What Tell Does Not Guarantee

**Accepted is not true.** "You can tell the knowledge base 1 plus 3 is 5, and it'll say okay, I learned something new," as long as nothing contradicts it. A knowledge base that starts out knowing nothing will accept a great deal, "and this is how fiction works, right? You just create a world" ([≈45:49](../raw/transcripts/15-logic-i.md), [≈46:34](../raw/transcripts/15-logic-i.md)).

**Contradictions do not say who is wrong.** When KB and $f$ contradict, the logic "is not opinionated about" which is at fault; it is just sets of models ([≈38:05](../raw/transcripts/15-logic-i.md)). Ask and Tell as implemented assume the knowledge base is correct and $f$ is under test ([≈41:58](../raw/transcripts/15-logic-i.md), [≈48:05](../raw/transcripts/15-logic-i.md)). If the new fact is the correct one, repairing the KB is tricky: you do not know which formulas to remove, and there may be several ways to remove enough. In practice you would surface the small set of conflicting formulas and let a person decide, much as failing tests leave you to work out whether the code, the tests or the comments are wrong ([≈48:50](../raw/transcripts/15-logic-i.md)–[≈50:21](../raw/transcripts/15-logic-i.md)).

**There is no time.** Adding formulas is monotonic: you cannot remove a fact, and the set of models only shrinks. With probabilities a belief can go up or down as evidence arrives; with logic, the set "can only shrink" ([≈48:50](../raw/transcripts/15-logic-i.md), [≈49:36](../raw/transcripts/15-logic-i.md)).

## Relation to Probabilistic Inference

[Probabilistic inference](probabilistic-inference.md) in a [Bayesian network](bayesian-networks.md) asks $P(\text{query} \mid \text{evidence})$. Here the evidence plays the role of the knowledge base and the query is what you Ask, so $P(\text{Rain} \mid \text{Wet} = 1)$ corresponds to Tell[Wet] followed by Ask[Rain] ([≈52:41](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). Give every model a probability $P(W = w)$, and the logical relations become numbers:

$$P(\text{KB}) = \sum_{w \in \mathcal{M}(\text{KB})} P(W = w), \qquad P(f \mid \text{KB}) = \frac{P(\text{KB} \cup \lbrace f \rbrace)}{P(\text{KB})}$$

$P(f \mid \text{KB})$ is 1 under entailment and 0 under contradiction, and a contingent $f$ gets a number in between where logic could only say "I don't know": "we can say, hm, 90% chance" ([≈54:59](../raw/transcripts/15-logic-i.md), [≈55:45](../raw/transcripts/15-logic-i.md)). In the lecture's joint table over Rain and Wet, $P(\text{Rain}) = 0.1 + 0.3 = 0.4$ ([≈54:59](../raw/transcripts/15-logic-i.md)). In return, logic allows arbitrary formulas as evidence and query, such as $\text{Rain} \vee \text{Snow}$, where a Bayesian network conditions only on "variable equals value" ([≈53:26](../raw/transcripts/15-logic-i.md), [≈54:12](../raw/transcripts/15-logic-i.md)).

## Related Pages

- [Lecture 15](15-logic-i.md) — the lecture that defines all three relations and Ask/Tell
- [Propositional logic](propositional-logic.md) — models, the interpretation function, and knowledge bases
- [Satisfiability](satisfiability.md) — deciding all three relations with two solver calls
- [Inference rules](inference-rules.md) — derivation ($\vdash$), the syntactic counterpart of entailment ($\models$)
- [Probabilistic inference](probabilistic-inference.md) — the probabilistic generalization of Ask
- [First-order logic](first-order-logic.md) — where the same relations and Ask/Tell apply to formulas about objects
