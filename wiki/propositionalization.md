---
title: Propositionalization — Unique Names, Domain Closure, and First-Order Logic as Syntactic Sugar
---

# Propositionalization

**Propositionalization** turns a [first-order](first-order-logic.md) knowledge base into an equivalent [propositional](propositional-logic.md) one, so that the inference machinery of [lecture 15](15-logic-i.md), [satisfiability](satisfiability.md) checking and model checking, applies to it directly ([lecture 16](16-logic-ii.md), [≈45:03](../raw/transcripts/16-logic-ii.md)–[≈48:54](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). It cannot be done in general, "because first-order logic is more powerful," but it can when two assumptions about the model hold.

Notation, as in the lecture: constants are lowercase ($\text{alice}$, $\text{bob}$, $\text{john}$), predicates capitalized ($\text{Student}$, $\text{Knows}$); $o_1$, $o_2$, $o_3$ are objects of a model's domain, and $w_1$, $w_2$, $w_3$ are models.

## The Two Assumptions

Both concern the relationship between the objects of the domain and the constant symbols that name them ([≈45:49](../raw/transcripts/16-logic-ii.md), [≈46:35](../raw/transcripts/16-logic-ii.md)):

- **Unique names**: each object corresponds to **at most one** constant.
- **Domain closure**: each object corresponds to **at least one** constant.

(The slide as taught garbled the unique-names line; the lecturer corrected it aloud to "each object corresponds to at most one constant," and the course fixed the text the next day.)

The lecture's figure shows three models over the constants john and bob ([≈46:35](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)):

- In $w_1$, john names $o_1$ and bob names $o_2$. Both assumptions hold.
- In $w_2$, john and bob both name the single object $o_1$, so $o_1$ does not have a unique name. Unique names rules it out.
- In $w_3$, john names $o_1$ and bob names $o_3$, and $o_2$ has no name at all. Domain closure rules it out.

Together the assumptions mean there is a one-to-one relationship between objects and constants, so every object can be referred to by exactly one constant.

## The Reduction

With a one-to-one correspondence, every atomic formula over constants can become a propositional symbol: "you just remove the commas and the parentheses and the spaces" ([≈46:35](../raw/transcripts/16-logic-ii.md), [≈47:23](../raw/transcripts/16-logic-ii.md)). Quantifiers expand over the constants: a $\forall$ becomes a **conjunction** of its body instantiated with each constant, and an $\exists$ a **disjunction** ([≈48:08](../raw/transcripts/16-logic-ii.md)).

The lecture's knowledge base, with constants alice and bob ([slides](../raw/slides/16-logic-ii.md)):

$$\text{Student}(\text{alice}), \quad \text{Student}(\text{bob}), \quad \forall x . \thinspace \text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic}), \quad \exists x . \thinspace \text{Student}(x) \wedge \text{Knows}(x, \text{arithmetic})$$

becomes, with propositional symbols StudentAlice, StudentBob, KnowsAliceArithmetic and KnowsBobArithmetic,

$$\text{StudentAlice}, \quad \text{StudentBob}$$

$$(\text{StudentAlice} \rightarrow \text{KnowsAliceArithmetic}) \wedge (\text{StudentBob} \rightarrow \text{KnowsBobArithmetic})$$

$$(\text{StudentAlice} \wedge \text{KnowsAliceArithmetic}) \vee (\text{StudentBob} \wedge \text{KnowsBobArithmetic})$$

The first two are "not quite the same" as before: a predicate applied to a constant has become one monolithic propositional symbol ([≈47:23](../raw/transcripts/16-logic-ii.md)). This is the same move as the "Alice knows arithmetic" symbols that showed propositional logic's limits at the start of the lecture ([≈13:18](../raw/transcripts/16-logic-ii.md)), now done mechanically.

## First-Order Logic as Syntactic Sugar

What is left is plain propositional logic, and any technique from lecture 15 applies, such as model checking ([≈48:08](../raw/transcripts/16-logic-ii.md)). So under these assumptions first-order logic is "in some sense just syntactic sugar for propositional logic. It's the same expressivity, but it's just easier to read and write" ([≈48:08](../raw/transcripts/16-logic-ii.md), [≈48:54](../raw/transcripts/16-logic-ii.md)). The lecturer calls this "an easy case." Without the assumptions, the lecture turns to inference rules: modus ponens on definite clauses with [substitution and unification](unification.md).

## Related Pages

- [Lecture 16](16-logic-ii.md) — the lecture that introduces propositionalization
- [First-order logic](first-order-logic.md) — the syntax and semantics being reduced
- [Propositional logic](propositional-logic.md) — the target of the reduction
- [Satisfiability](satisfiability.md) — model checking, which applies once the knowledge base is propositional
- [Substitution and unification](unification.md) — the other route to first-order inference
