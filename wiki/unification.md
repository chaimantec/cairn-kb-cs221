---
title: Substitution and Unification — Matching Variables for First-Order Modus Ponens
---

# Substitution and Unification

**Substitution** and **unification** are the two operations that let [modus ponens](inference-rules.md) work in [first-order logic](first-order-logic.md). A first-order rule is written with variables, a knowledge base holds facts about constants, and an inference rule only checks whether formulas are syntactically equal. Substitution replaces variables with terms; unification finds the substitution that makes two formulas equal. Together they are "generalized equality" ([lecture 16](16-logic-ii.md), [≈55:16](../raw/transcripts/16-logic-ii.md)–[≈1:03:03](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)).

Notation, as in the lecture: $x$, $y$, $z$ are variables; $\text{alice}$, $\text{bob}$, $\text{cs221}$, $\text{logic}$ are constants; $\text{Knows}$, $\text{Takes}$, $\text{Covers}$, $\text{Student}$ are predicates. A substitution is written as a dictionary from variables to terms, $\lbrace x : \text{alice}, y : \text{cs221} \rbrace$, and is called $\theta$ when it comes from unification. $a_1, \ldots, a_k$ and $b$ are atomic formulas.

## Why They Are Needed

Take the knowledge base

$$\text{Takes}(\text{alice}, \text{cs221}), \qquad \text{Covers}(\text{cs221}, \text{logic}), \qquad \forall x, y, z . \thinspace (\text{Takes}(x, y) \wedge \text{Covers}(y, z)) \rightarrow \text{Knows}(x, z)$$

Intuitively it implies $\text{Knows}(\text{alice}, \text{logic})$. But modus ponens with exact matching cannot derive it: the rule's premise is $\text{Takes}(x, y)$, and $\text{Takes}(\text{alice}, \text{cs221})$ is not the same string ([≈52:08](../raw/transcripts/16-logic-ii.md)–[≈54:30](../raw/transcripts/16-logic-ii.md)). Rules are syntactic; "it's kind of dumb, but that's the rules, the rules are just the rules." In propositional logic this never came up, since the premise Rain matches the fact Rain exactly.

What $\forall$ means supplies the answer: the rule holds for any $x$, $y$ and $z$, so in particular for $x = \text{alice}$, $y = \text{cs221}$. The rule is "really like a rule template," and what is needed is a way to fill in the template ([≈54:30](../raw/transcripts/16-logic-ii.md), [≈55:16](../raw/transcripts/16-logic-ii.md)).

## Substitution

A **substitution** maps variables to terms, and applying it to a formula does "surgery" on the formula: search and replace on its terms ([≈55:16](../raw/transcripts/16-logic-ii.md)–[≈58:24](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). It is recursive: if the formula is itself a variable in the substitution, return its replacement; otherwise substitute into each argument and rebuild the formula with the same predicate, function or connective.

- $\text{Knows}(x, y)$ under $\lbrace x : \text{alice}, y : \text{cs221} \rbrace$: recurse on $x$, which becomes alice, and on $y$, which becomes cs221, then reapply Knows: $\text{Knows}(\text{alice}, \text{cs221})$.
- $\text{Student}(x) \wedge \text{Knows}(x, y)$ under $\lbrace x : \text{alice}, y : z \rbrace$: $\text{Student}(\text{alice}) \wedge \text{Knows}(\text{alice}, z)$. A variable can be replaced by another variable, not only by a constant.

## Unification

**Unification** takes two formulas and finds a substitution that makes them equal, or reports that there is none ([≈58:24](../raw/transcripts/16-logic-ii.md)–[≈1:00:43](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). Only variables can be on the left of a substitution: "I can replace $x$ with anything I want," but not alice with bob.

The lecture's `unify(f1, f2, subst)` starts from an empty substitution and builds it up:

1. If the two formulas are equal, succeed with the substitution so far.
2. If either is a variable, bind it to the other (after applying the substitution so far) and succeed.
3. Otherwise the outermost symbols must be the same (Knows cannot be replaced with Takes); unify the arguments pair by pair, and fail if any pair fails.

The traced examples ([≈59:10](../raw/transcripts/16-logic-ii.md)–[≈1:00:43](../raw/transcripts/16-logic-ii.md)):

- $\text{unify}(\text{Knows}(x, y), \text{Knows}(\text{alice}, \text{bob}))$ gives $\lbrace x : \text{alice}, y : \text{bob} \rbrace$. Applying it to both formulas makes them identical.
- $\text{unify}(\text{Knows}(\text{alice}, y), \text{Knows}(x, z))$ gives $\lbrace x : \text{alice}, y : z \rbrace$, and both formulas become $\text{Knows}(\text{alice}, z)$.
- $\text{unify}(\text{Knows}(\text{alice}, y), \text{Knows}(\text{bob}, z))$ fails, because alice and bob are different constants and cannot be unified.

In Z3, which the lecture's code uses, a variable is declared exactly like a constant, so the code cannot tell them apart from a formula like $\text{Takes}(\text{alice}, x)$ and hardcodes $x$, $y$ and $z$ as the variables ([slides](../raw/slides/16-logic-ii.md)).

## Modus Ponens with Unification

With these two tools modus ponens generalizes to first-order **definite clauses**, formulas of the form $\forall x_1 \ldots x_n . \thinspace (a_1 \wedge \cdots \wedge a_k) \rightarrow b$ ([≈1:00:43](../raw/transcripts/16-logic-ii.md)–[≈1:03:03](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). Given facts $a_1', \ldots, a_k'$ (primed to distinguish them from the rule's $a_1, \ldots, a_k$) and the rule, compute

$$\theta = \text{unify}(a_1' \wedge \cdots \wedge a_k', \thinspace a_1 \wedge \cdots \wedge a_k)$$

and if unification succeeds, conclude $b' = \text{substitute}(b, \theta)$, not $b$ itself. In the example, unifying $\text{Takes}(\text{alice}, \text{cs221}) \wedge \text{Covers}(\text{cs221}, \text{logic})$ with $\text{Takes}(x, y) \wedge \text{Covers}(y, z)$ gives $\theta = \lbrace x : \text{alice}, y : \text{cs221}, z : \text{logic} \rbrace$, and $\text{substitute}(\text{Knows}(x, z), \theta) = \text{Knows}(\text{alice}, \text{logic})$. Substitution and unification "bridge the gap between these general variables and these concrete instances." The rule's complexity, soundness and incompleteness are on the [inference rules](inference-rules.md#first-order-modus-ponens) page.

## Related Pages

- [Lecture 16](16-logic-ii.md) — the lecture that introduces substitution and unification
- [Inference rules](inference-rules.md) — modus ponens in propositional and first-order logic, soundness and completeness
- [First-order logic](first-order-logic.md) — variables, terms, predicates and quantifiers
- [Propositionalization](propositionalization.md) — the other route to first-order inference, which needs no unification
