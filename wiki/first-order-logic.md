---
title: First-Order Logic — Terms, Predicates, Quantifiers, Models and Translation from English
---

# First-Order Logic

**First-order logic** extends [propositional logic](propositional-logic.md) with **objects** and **quantifiers**, and is where, in the lecturer's words, "you really get to see logic shine" ([lecture 16](16-logic-ii.md), [≈0:05](../raw/transcripts/16-logic-ii.md)). It is defined by the same three ingredients as every logic in CS221: syntax, semantics and [inference rules](inference-rules.md). The general notions from [lecture 15](15-logic-i.md), [entailment, contradiction and contingency](entailment.md), Ask and Tell, and [satisfiability](satisfiability.md), carry over unchanged; what changes is what a formula can say and what a model is.

Notation, as in the lecture: constants, variables and functions are lowercase ($\text{alice}$, $x$, $\text{father}$); predicates and formulas are capitalized, as in $\text{Student}(x)$ and $\text{Knows}(x, \text{arithmetic})$. $f$ is a formula, $w$ a model, $\mathcal{I}(f, w)$ the interpretation function, and $o_1$, $o_2$, $o_3$ objects of a model's domain.

## Why Propositional Logic Is Not Enough

In propositional logic every fact is a monolithic symbol ([≈13:18](../raw/transcripts/16-logic-ii.md)–[≈16:23](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). *Alice and Bob both know arithmetic* is fine, as AliceKnowsArithmetic ∧ BobKnowsArithmetic. But *all students know arithmetic* needs one implication per student, AliceIsStudent → AliceKnowsArithmetic and so on, which becomes a very large formula with a thousand students. *Every even integer greater than 2 is the sum of two primes* would require a propositional symbol for every integer. Propositional logic is "very clunky and not expressive enough," and two things are missing:

- **Objects and predicates**: "Alice knows arithmetic" has internal structure (Alice, knows, arithmetic) that a single symbol hides.
- **Quantifiers and variables**: "all" ranges over objects that often cannot be enumerated. Without them, logic is like programming without for loops ([≈15:38](../raw/transcripts/16-logic-ii.md), [≈16:23](../raw/transcripts/16-logic-ii.md)).

## Syntax: Terms and Formulas

The key distinction is that **terms denote objects** and **formulas denote truth values** ([≈17:10](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)).

**Terms** ([≈17:10](../raw/transcripts/16-logic-ii.md)–[≈18:43](../raw/transcripts/16-logic-ii.md)):

- **Constant symbols**, such as $\text{alice}$ and $\text{arithmetic}$, denote objects, concrete or abstract. They are not propositional symbols: "Alice is not true or false."
- **Variables**, such as $x$ and $y$.
- **Functions** applied to terms, such as $\text{father}(\text{alice})$ or $\text{add}(x, y)$. A function takes terms and returns a term, so terms nest: $\text{father}(\text{father}(\text{alice}))$, $\text{add}(\text{add}(x, y), z)$.

**Formulas** ([≈18:43](../raw/transcripts/16-logic-ii.md)–[≈21:03](../raw/transcripts/16-logic-ii.md)):

- **Atomic formulas** are predicates applied to terms. A predicate takes some number of arguments and returns true or false: $\text{Snowing}$ has arity 0 (a propositional symbol), $\text{Student}$ arity 1 (unary), $\text{Knows}$ arity 2 (binary). So $\text{Knows}(x, \text{arithmetic})$ is an atomic formula.
- **Connectives** ($\neg$, $\wedge$, $\vee$, $\rightarrow$, $\leftrightarrow$) combine formulas exactly as in propositional logic.
- **Quantifiers** $\forall x$ ("for all $x$") and $\exists x$ ("there exists $x$") apply to a formula in which the variable $x$ appears.

$$\forall x . \thinspace \text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic})$$

**What is not a formula** ([≈21:03](../raw/transcripts/16-logic-ii.md)–[≈22:36](../raw/transcripts/16-logic-ii.md), [≈24:07](../raw/transcripts/16-logic-ii.md), [≈24:53](../raw/transcripts/16-logic-ii.md)). $\text{father}(x)$ is a term, not a formula. $\text{Knows}(\text{Student}, \text{arithmetic})$ is not a formula, since Student is a predicate, not a term; this is a common mistake when translating "students know arithmetic." Nor is $\text{Knows}(\text{Student}(x), \text{arithmetic})$, since $\text{Student}(x)$ is a Boolean and every argument must denote an object. And $\text{Foo}(\text{Knows}(\text{alice}, \text{arithmetic}))$ is not a formula for the same reason: nothing can take a Boolean as an argument. Every argument is a term, and the outermost symbol of a formula returns a Boolean. The course writes terms in lowercase and formulas in uppercase to keep them apart, though this is only a convention.

Functions return terms, never functions ([≈24:07](../raw/transcripts/16-logic-ii.md)). First-order logic is far more restrictive than a programming language like Python, and "With a more restrictive language, it's easier to do stuff with, but it's less expressive."

## Semantics: Domain and Interpretation

A model represents a possible world. In propositional logic it assigns truth values to atomic formulas; in first-order logic that does not work ([≈27:11](../raw/transcripts/16-logic-ii.md)–[≈29:29](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)):

1. With functions there are infinitely many atomic formulas ($\text{Knows}(\text{father}(\text{alice}), \ldots)$, $\text{Knows}(\text{father}(\text{father}(\text{alice})), \ldots)$, …), and a model would have to assign each one a truth value.
2. Two different terms can denote the same object (Bob might be Alice's father), yet a truth-value assignment would treat $\text{Knows}(\text{father}(\text{alice}), \text{arithmetic})$ and $\text{Knows}(\text{bob}, \text{arithmetic})$ independently.

The fix is **a layer of indirection** ([≈29:29](../raw/transcripts/16-logic-ii.md)–[≈32:30](../raw/transcripts/16-logic-ii.md)). A model $w$ is a **domain**, a set of objects such as $\lbrace o_1, o_2, o_3 \rbrace$, together with an **interpretation** of the symbols:

- each **constant** maps to an object (alice to $o_1$, bob to $o_2$, arithmetic to $o_3$);
- each **function** maps objects to objects (father takes $o_1$ to $o_2$);
- each **predicate** maps tuples of objects to truth values (Knows is true of $(o_1, o_3)$; Student is true of $o_1$).

Constants live "in syntax land"; the interpretation maps them into the domain, and functions and predicates operate on the domain's objects. Several constants may name the same object (the lecture's figure adds a constant robert for $o_2$, "just for fun") ([≈34:04](../raw/transcripts/16-logic-ii.md), [≈34:50](../raw/transcripts/16-logic-ii.md)). Drawn as a graph, objects are nodes, constants are labels on them, unary predicates mark subsets of nodes and binary predicates are edges; knowledge graphs "are basically these types of things" ([≈34:50](../raw/transcripts/16-logic-ii.md), [≈35:37](../raw/transcripts/16-logic-ii.md)).

**The interpretation function** $\mathcal{I}(f, w)$ returns whether formula $f$ is true in model $w$, recursively ([≈35:37](../raw/transcripts/16-logic-ii.md)–[≈44:16](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). It carries a substitution from variables to objects as it goes.

- **Atomic formula** $P(t_1, \ldots, t_n)$: interpret each term to get an object, then look up the predicate's truth value on that tuple. A term is interpreted by looking up a variable in the substitution or a constant in the interpretation, or, for a function application, by interpreting its arguments and applying the function's mapping.
- **Connectives**: interpret the parts and combine the truth values, as in propositional logic.
- **$\forall x . \thinspace f$**: for each object in the domain, bind $x$ to it and interpret $f$; true if $f$ holds for every object.
- **$\exists x . \thinspace f$**: the same loop, true if $f$ holds for some object.

In the lecture's model, $\mathcal{I}(\text{Knows}(\text{alice}, \text{arithmetic}), w)$ is true, because alice is $o_1$, arithmetic is $o_3$ and Knows holds of the pair $(o_1, o_3)$. Meanwhile $\mathcal{I}(\forall x . \thinspace \text{Knows}(x, \text{arithmetic}), w)$ is false, because $o_2$ does not know arithmetic in that model ([≈38:01](../raw/transcripts/16-logic-ii.md)–[≈42:44](../raw/transcripts/16-logic-ii.md)). [Lecture 16](16-logic-ii.md) walks through both evaluations.

Connectives and predicates are handled differently because their arguments differ ([≈43:31](../raw/transcripts/16-logic-ii.md), [≈44:16](../raw/transcripts/16-logic-ii.md)): a connective's arguments are formulas and evaluate to truth values, while a predicate's arguments are terms and evaluate to objects, which the predicate's interpretation then turns into a truth value.

## Inference

Ask and Tell reduce to entailment, contradiction and contingency, and those reduce to [satisfiability](satisfiability.md), exactly as in propositional logic; the lecture's opening example answers "Is it snowing?" this way with the Z3 solver ([≈0:05](../raw/transcripts/16-logic-ii.md)–[≈2:24](../raw/transcripts/16-logic-ii.md), [≈45:03](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). Lecture 16 gives two routes to that inference:

- **[Propositionalization](propositionalization.md)**: under the unique-names and domain-closure assumptions, rewrite the knowledge base in propositional logic and use model checking. In that regime first-order logic is syntactic sugar.
- **Modus ponens with [substitution and unification](unification.md)**: on definite clauses, match a rule's variables against concrete facts and derive new atomic formulas. It is sound but not complete; completeness needs the **resolution** rule, which this offering of the course skips ([≈1:05:21](../raw/transcripts/16-logic-ii.md), [≈1:06:08](../raw/transcripts/16-logic-ii.md)). See [inference rules](inference-rules.md#first-order-modus-ponens).

## Translating English into First-Order Logic

First-order logic is "a very natural way of encoding the meaning of natural language sentences," and the assignment asks for such translations ([≈1:06:56](../raw/transcripts/16-logic-ii.md)–[≈1:11:35](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)).

**For all goes with implies; exists goes with and.** "99% of the time this is basically the rule you should follow":

- *All students know arithmetic*: $\forall x . \thinspace \text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic})$.
- *Some student knows arithmetic*: $\exists x . \thinspace \text{Student}(x) \wedge \text{Knows}(x, \text{arithmetic})$.

The swapped versions are probably wrong. $\forall x . \thinspace \text{Student}(x) \wedge \text{Knows}(x, \text{arithmetic})$ says every object in the domain is a student who knows arithmetic. $\exists x . \thinspace \text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic})$ says some object is either not a student or knows arithmetic, which any single non-student makes true ([≈1:08:28](../raw/transcripts/16-logic-ii.md), [≈1:09:14](../raw/transcripts/16-logic-ii.md)).

**Nested quantifiers and functions** ([≈1:09:59](../raw/transcripts/16-logic-ii.md), [≈1:10:47](../raw/transcripts/16-logic-ii.md)):

- *There is some course that every student has taken*: $\exists x . \thinspace \text{Course}(x) \wedge \forall y . \thinspace \text{Student}(y) \rightarrow \text{Takes}(y, x)$. The slide as taught omitted $\text{Course}(x)$, and the lecturer pointed out that "course should be in there."
- *Every even integer greater than 2 is the sum of two prime numbers*: $\forall x . \thinspace \text{Even}(x) \wedge \text{GreaterThan}(x, \text{two}) \rightarrow \exists y, z . \thinspace \text{Prime}(y) \wedge \text{Prime}(z) \wedge \text{add}(y, z) = x$.
- *If a student takes a course and the course covers a concept, then the student knows the concept*: $\forall x, y, z . \thinspace \text{Student}(x) \wedge \text{Takes}(x, y) \wedge \text{Course}(y) \wedge \text{Covers}(y, z) \wedge \text{Concept}(z) \rightarrow \text{Knows}(x, z)$, a definite clause.

Translation takes practice, like turning an informal specification into code ([≈1:11:35](../raw/transcripts/16-logic-ii.md)).

## Limits: Higher-Order Logics

First-order logic is not powerful enough for every sentence ([≈3:12](../raw/transcripts/16-logic-ii.md)). *70% of students know machine learning* cannot be written in it, because it needs to talk about the set of students and operate on that set, not only quantify over individual objects. Logics that can are **higher-order logics**, which the course mentions but does not cover ([≈1:13:56](../raw/transcripts/16-logic-ii.md), [≈1:14:42](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)).

## Related Pages

- [Lecture 16](16-logic-ii.md) — the lecture that introduces first-order logic
- [Propositional logic](propositional-logic.md) — the simpler logic it extends, where models, knowledge bases and the interpretation function were introduced
- [Propositionalization](propositionalization.md) — reducing a first-order knowledge base to propositional logic
- [Substitution and unification](unification.md) — the matching that first-order modus ponens needs
- [Inference rules](inference-rules.md) — modus ponens, soundness and completeness, and the first-order version
- [Entailment, contradiction and contingency](entailment.md) and [satisfiability](satisfiability.md) — the semantic relations behind Ask and Tell
