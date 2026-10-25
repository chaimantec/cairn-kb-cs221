---
title: Propositional Logic — Syntax, Models, the Interpretation Function and Knowledge Bases
---

# Propositional Logic

**Propositional logic** is "basically the simplest form of logic," and in CS221 it is the vehicle for concepts that apply to every logic: models, entailment, satisfiability, soundness and completeness ([lecture 15](15-logic-i.md), [≈8:34](../raw/transcripts/15-logic-i.md)). The course treats a logic as a **language** with two goals, representing knowledge about the world and reasoning with it, and defines every logic by three ingredients ([≈3:11](../raw/transcripts/15-logic-i.md), [≈5:31](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)):

1. **Syntax**: which formulas are valid.
2. **Semantics**: what those formulas mean.
3. **Inference rules**: how to derive new formulas from existing ones.

This page covers the syntax and the basic semantics. The relations between a knowledge base and a new formula are on [entailment, contradiction and contingency](entailment.md); deciding them efficiently is [satisfiability](satisfiability.md); the third ingredient is [inference rules](inference-rules.md).

Notation, as in the lecture: $f$, $g$ and $h$ are formulas; $w$ is a model; $\mathcal{I}(f, w)$ is the interpretation function; $\mathcal{M}(f)$ is the set of models of $f$; KB is a knowledge base.

## Why a Logic, and Why Not Natural Language

Logical reasoning is what you do when you solve the lecture's opening problem (if $A + B = 10$ and $A - B = 4$, what is $A$?) by adding the equations to get $2A = 14$, rather than searching through possible values: a problem with infinitely many candidates, solved in your head ([≈0:52](../raw/transcripts/15-logic-i.md), [≈1:38](../raw/transcripts/15-logic-i.md)). Logic dominated AI before the 1990s; it fell back because it did not handle **uncertainty** (probability addresses that) or leverage **data** (machine learning addresses that). What it offers that the lecturer calls "sort of irreplaceable" is **expressivity in a compact way** ([≈2:26](../raw/transcripts/15-logic-i.md), [≈3:11](../raw/transcripts/15-logic-i.md)).

Natural language is not precise enough to reason in. "A dime is better than a nickel; a nickel is better than a penny; therefore a dime is better than a penny" is fine, but "a penny is better than nothing; nothing is better than world peace; therefore a penny is better than world peace" has the same shape and is absurd ([≈3:58](../raw/transcripts/15-logic-i.md), [≈4:44](../raw/transcripts/15-logic-i.md)). Logical languages are formal, like programming languages, and there are many of them: first-order logic and description logic are two examples on the slide ([slides](../raw/slides/15-logic-i.md)).

**Syntax is not semantics.** `2 + 3` and `3 + 2` are different syntax with the same meaning, 5; `3 / 2` is the same syntax with different meanings, 1 in Python 2.7 and 1.5 in Python 3 ([≈7:02](../raw/transcripts/15-logic-i.md)). A string's meaning is not in the string; it comes from the semantics.

## Syntax

The set of formulas is defined recursively ([≈9:20](../raw/transcripts/15-logic-i.md)–[≈11:38](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)):

- **Base case.** Every **propositional symbol** (also called an **atomic formula**) is a formula. A symbol is just a name, like a variable name: $P$, $Q$, $\text{Rain}$, $\text{Wet}$.
- **Inductive case.** If $f$ and $g$ are formulas, so are $\neg f$, $f \wedge g$, $f \vee g$, $f \rightarrow g$ and $f \leftrightarrow g$ (not, and, or, implies, and bidirectional implication or equivalence).
- Nothing else is a formula.

So $P$, $\neg P$, $\neg P \vee Q$ and $P \rightarrow (Q \vee \neg P)$ are formulas. "P ¬Q" (no connective), "P + Q" (plus is not a connective) and "P(A) ∨ Q(B)" are not, though they might be in other logics. At this stage the connectives mean nothing: "I could have easily called these like foo and bar" ([≈11:38](../raw/transcripts/15-logic-i.md)).

In the lecture's code, formulas are built with the Z3 library: `Bool("Rain")` makes a symbol, and `Not`, `And`, `Or`, `Implies` and `==` build the compound formulas ([slides](../raw/slides/15-logic-i.md)).

## Models

A **model** $w$ is an assignment of truth values to the propositional symbols. It represents a state of the world, and "anytime you see model, you can think about world." This is a model in the logical sense, not a machine learning model ([≈12:25](../raw/transcripts/15-logic-i.md), [≈13:12](../raw/transcripts/15-logic-i.md)). With $n$ symbols there are $2^n$ models: 8 for $A$, $B$, $C$, and $2^{100}$ for 100 symbols ([≈13:12](../raw/transcripts/15-logic-i.md), [≈23:21](../raw/transcripts/15-logic-i.md)). Together the models are all the ways the world could be.

## The Interpretation Function

The **interpretation function** $\mathcal{I}(f, w)$ connects syntax to semantics: given a formula $f$ and a model $w$, it returns true (1) if $w$ satisfies $f$ and false (0) otherwise ([≈14:00](../raw/transcripts/15-logic-i.md), [≈14:48](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). It is defined recursively, mirroring the syntax:

- a propositional symbol is looked up in $w$;
- $\mathcal{I}(\neg g, w)$ is the negation of $\mathcal{I}(g, w)$;
- $\mathcal{I}(g \wedge h, w)$, $\mathcal{I}(g \vee h, w)$, $\mathcal{I}(g \rightarrow h, w)$ and $\mathcal{I}(g \leftrightarrow h, w)$ combine $\mathcal{I}(g, w)$ and $\mathcal{I}(h, w)$ with and, or, "not the first or the second," and equality respectively.

For example, with $A$ and $B$ true and $C$ false, $\mathcal{I}(\neg A, w) = 0$, so $\mathcal{I}(\neg A \wedge B, w) = 0$; $\mathcal{I}(C, w) = 0$; and since both sides of $(\neg A \wedge B) \leftrightarrow C$ are 0, the whole formula is true ([≈15:34](../raw/transcripts/15-logic-i.md)–[≈17:06](../raw/transcripts/15-logic-i.md)). Any formula, however complicated, can be evaluated against any world this way, and in general "as long as you define an interpretation function, you have a logic to work with" ([≈18:41](../raw/transcripts/15-logic-i.md), [≈19:28](../raw/transcripts/15-logic-i.md)).

## The Models of a Formula

The object used from then on is the **set of models** of a formula ([≈20:16](../raw/transcripts/15-logic-i.md), [≈24:09](../raw/transcripts/15-logic-i.md)):

$$\mathcal{M}(f) = \lbrace w : \mathcal{I}(f, w) = 1 \rbrace$$

Read $f$ as a sentence, such as "it is raining or wet"; its meaning is the set of possible worlds in which it is true. Over $\text{Rain}$ and $\text{Wet}$, with each model written as a (Rain, Wet) pair, the lecture's traced results are $\mathcal{M}(\text{Rain} \vee \text{Wet})$ = $(0, 1)$, $(1, 0)$, $(1, 1)$ and $\mathcal{M}(\text{Rain} \wedge \text{Wet})$ = $(1, 1)$ ([≈21:48](../raw/transcripts/15-logic-i.md)–[≈23:21](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)).

This is the sense in which logic is **compact**: a small formula can stand for a huge set of models, trillions of them with 100 symbols ([≈23:21](../raw/transcripts/15-logic-i.md)).

## Knowledge Bases

A **knowledge base** is a set of formulas, "our list of things that we know to be true about the world," which grows over time; the lecturer agrees it is right to think of it as a database of facts ([≈24:54](../raw/transcripts/15-logic-i.md), [≈48:05](../raw/transcripts/15-logic-i.md)). Its models are the worlds that satisfy every formula in it:

$$\mathcal{M}(\text{KB}) = \bigcap_{f \in \text{KB}} \mathcal{M}(f)$$

the possible worlds we could be in given what we know ([≈25:40](../raw/transcripts/15-logic-i.md), [≈26:27](../raw/transcripts/15-logic-i.md)). Equivalently, a KB means the same as the conjunction of its formulas ([≈28:03](../raw/transcripts/15-logic-i.md)). For $\text{KB} = \lbrace \text{Rain}, \text{Rain} \rightarrow \text{Wet} \rbrace$: $\mathcal{M}(\text{Rain})$ = $(1, 0)$, $(1, 1)$; $\mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ = $(0, 0)$, $(0, 1)$, $(1, 1)$, every world except raining-and-not-wet; and the intersection is $(1, 1)$, raining and wet ([≈26:27](../raw/transcripts/15-logic-i.md)–[≈28:03](../raw/transcripts/15-logic-i.md)).

Adding formulas makes the KB larger and its set of models smaller: $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace) \subseteq \mathcal{M}(\text{KB})$. "As we gain more knowledge, we narrow down the possible worlds we might be in" ([≈29:39](../raw/transcripts/15-logic-i.md), [≈30:25](../raw/transcripts/15-logic-i.md)). How much it shrinks is the subject of [entailment, contradiction and contingency](entailment.md). Adding formulas is **monotonic**: there is no removing a fact or notion of time, and the set of models only shrinks, whereas with probabilities new evidence can move a belief up or down ([≈48:50](../raw/transcripts/15-logic-i.md), [≈49:36](../raw/transcripts/15-logic-i.md)).

## Relation to Bayesian Networks

Propositional symbols correspond to the random variables of a [Bayesian network](bayesian-networks.md), and models to assignments, so a joint distribution is a probability distribution over models ([≈51:54](../raw/transcripts/15-logic-i.md), [≈52:41](../raw/transcripts/15-logic-i.md)). The lecture develops this connection under [entailment](entailment.md#relation-to-probabilistic-inference).

## Related Pages

- [Lecture 15](15-logic-i.md) — the lecture that introduces propositional logic
- [Entailment, contradiction and contingency](entailment.md) — what adding a formula to a knowledge base does, and the Ask and Tell operations
- [Satisfiability](satisfiability.md) — whether a knowledge base has any model, and how solvers check it
- [Inference rules](inference-rules.md) — modus ponens, derivation, soundness and completeness
- [Probabilistic inference](probabilistic-inference.md) — the probabilistic counterpart of querying a knowledge base
