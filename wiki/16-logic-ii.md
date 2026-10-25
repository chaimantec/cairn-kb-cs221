---
title: Lecture 16 — Logic II
---

# Lecture 16 — Logic II

Lecture 16 is the second of the two logic lectures, and introduces **first-order logic**, "where you really get to see logic shine and do something interesting" ([≈0:05](../raw/transcripts/16-logic-ii.md)). [Lecture 15](15-logic-i.md) used [propositional logic](propositional-logic.md) to set up the general machinery of a logic: syntax, semantics (models, the interpretation function, knowledge bases, [entailment](entailment.md), [satisfiability](satisfiability.md)) and [inference rules](inference-rules.md). Propositional logic turns out to be clunky for even simple statements about many individuals, because it has no notion of **objects** and no **quantifiers**. [First-order logic](first-order-logic.md) adds both. The lecture builds its syntax (terms that denote objects, predicates that turn terms into formulas, and the quantifiers $\forall$ and $\exists$) and its semantics (a model is a domain of objects plus an interpretation of the symbols). It then gives two ways to do inference: [propositionalization](propositionalization.md), which reduces a first-order knowledge base to propositional logic under two assumptions, and modus ponens on definite clauses, generalized with [substitution and unification](unification.md). It ends with practice translating English sentences into first-order logic, which the assignment asks for ([≈1:06:56](../raw/transcripts/16-logic-ii.md)).

Notation, as in the lecture: constants, variables and functions are lowercase ($\text{alice}$, $\text{arithmetic}$, $x$, $y$, $z$, $\text{father}$); predicates and formulas are capitalized, as in $\text{Student}(x)$ and $\text{Knows}(x, \text{arithmetic})$. $f$ is a formula and $w$ a model; $\mathcal{I}(f, w)$ is the interpretation function; $o_1$, $o_2$, $o_3$ are objects of a model's domain; $\theta$ is a substitution. $\text{KB} \models f$ reads "KB entails $f$" and $\text{KB} \vdash f$ "KB derives (or proves) $f$", as in lecture 15. The lecture's code builds formulas with the Z3 library, puts every object in a single sort called `Object` "for simplicity", and writes the equality of two terms as `==` ([slides](../raw/slides/16-logic-ii.md)).

## A Motivating Example

The lecture opens by building a knowledge base one fact at a time and asking the class to reason along with it ([≈0:05](../raw/transcripts/16-logic-ii.md)–[≈2:24](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). Each English sentence is written next to its first-order formula, which the lecturer asks the class to follow intuitively before first-order logic has been defined:

- *Alice is a student*: $\text{Student}(\text{alice})$.
- *Alice is from Phoenix*: $\text{From}(\text{alice}, \text{phoenix})$.
- *Phoenix is a hot city*: $\text{Hot}(\text{phoenix}) \wedge \text{City}(\text{phoenix})$.
- *Students are people*: $\forall x . \thinspace \text{Student}(x) \rightarrow \text{Person}(x)$, "for every $x$, if $x$ is a student, that means $x$ is a person as well."
- *Cities are places*: $\forall x . \thinspace \text{City}(x) \rightarrow \text{Place}(x)$.
- *If it is snowing, it is cold*: $\text{Snowing} \rightarrow \text{Cold}$. This is just propositional logic, "but first-order logic is a superset, so we can do that, too."

**Is it snowing?** The knowledge base answers "I don't know." Then a rule is added: *if a person is from a hot place and it is snowing, then they are unhappy*,

$$\forall x, y . \thinspace \text{Person}(x) \wedge \text{From}(x, y) \wedge \text{Hot}(y) \wedge \text{Place}(y) \wedge \text{Snowing} \rightarrow \neg \text{Happy}(x)$$

Is it snowing? Still "I don't know." Finally, *Alice is happy*: $\text{Happy}(\text{alice})$. Now the answer is **"No"**, and the lecturer leaves the class to "ponder this, maybe offline" ([≈2:24](../raw/transcripts/16-logic-ii.md)). All three answers are the traced outputs of the lecture's `ask` function, which, as in lecture 15, makes two satisfiability calls with Z3: it answers "Yes" if $\text{KB} \cup \lbrace \neg f \rbrace$ is unsatisfiable, "No" if $\text{KB} \cup \lbrace f \rbrace$ is unsatisfiable, and "I don't know" otherwise ([slides](../raw/slides/16-logic-ii.md); see [entailment](entailment.md)).

The reasoning behind the "No" is not spelled out in the lecture; it follows from the knowledge base. If it were snowing, then Alice is a person (she is a student, and students are people), Phoenix is a place (it is a city, and cities are places), Alice is from Phoenix and Phoenix is hot, so the rule would make Alice unhappy, contradicting $\text{Happy}(\text{alice})$. So the knowledge base plus Snowing has no model, and the answer is no. Before Happy(alice) was added, nothing ruled out either possibility, hence "I don't know" twice.

The point of the example is how much it can express: "a lot of sentences with a lot of ands and ors and implications." The lecture's questions are what kinds of sentences first-order logic can represent and how to do inference with them. First-order logic is not powerful enough to express every sentence, "but it goes quite far" ([≈3:12](../raw/transcripts/16-logic-ii.md)). The same example is rerun at the end of the program, and the lecturer skips it there as already covered ([≈1:12:22](../raw/transcripts/16-logic-ii.md)).

## Review: What Defines a Logic

The review recaps lecture 15 ([≈3:57](../raw/transcripts/16-logic-ii.md)–[≈13:18](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). A logic is a language for representing knowledge and reasoning with it, defined by its **syntax** (which formulas are valid), its **semantics** (what they mean) and its **inference rules** (how to derive new formulas). Syntax is "just symbols," and inference rules turn symbols into more symbols; for the symbols to mean something, an **interpretation function** says what statement each formula makes about the world.

In propositional logic, a **model** (or world) is an assignment of truth values to the propositional symbols, such as Rain true and Wet false. We do not know exactly which world we live in, and formulas express constraints on the possible worlds. The interpretation function says whether a formula is satisfied by a model: Rain ∧ ¬Wet is true in that world, Wet alone would be false, and Rain alone true ([≈6:16](../raw/transcripts/16-logic-ii.md), [≈7:03](../raw/transcripts/16-logic-ii.md)). $\mathcal{M}(f)$ is the set of models of $f$; a **knowledge base** is a set of formulas, which an agent acquires over time, and its models are the intersection of its formulas' models. Adding a formula $f$ gives one of three outcomes: **entailment** if the models are unchanged ($f$ adds no information; Wet is entailed by Rain and Rain → Wet), **contradiction** if no model is left, and **contingency** otherwise ([≈8:34](../raw/transcripts/16-logic-ii.md)). These implement **Ask** and **Tell**, and all three reduce to two **satisfiability** checks done by model checking ([≈9:24](../raw/transcripts/16-logic-ii.md), [≈10:10](../raw/transcripts/16-logic-ii.md)).

On the syntax side, **modus ponens** derives $q$ from $p$ and $p \rightarrow q$; the knowledge base **derives** $f$ if repeatedly applying rules generates it. **Soundness** (whatever is derived is entailed: "nothing but the truth") and **completeness** (whatever is entailed can be derived: "the whole truth") relate derivation to entailment, pictured as staying within, and filling, a glass of water ([≈10:56](../raw/transcripts/16-logic-ii.md)–[≈13:18](../raw/transcripts/16-logic-ii.md)).

## Where Propositional Logic Falls Short

Try writing facts in propositional logic ([≈13:18](../raw/transcripts/16-logic-ii.md)–[≈16:23](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). *Alice and Bob both know arithmetic* works: define symbols AliceKnowsArithmetic and BobKnowsArithmetic and take their conjunction. *All students know arithmetic* already strains: with symbols AliceIsStudent and BobIsStudent you write

$$(\text{AliceIsStudent} \rightarrow \text{AliceKnowsArithmetic}) \wedge (\text{BobIsStudent} \rightarrow \text{BobKnowsArithmetic})$$

and with a thousand students the formula is very large. *Every even integer greater than 2 is the sum of two primes* (the lecturer names it as Goldbach's conjecture) cannot reasonably be written at all: you would have to list all integers as propositional symbols ([≈14:06](../raw/transcripts/16-logic-ii.md), [≈14:52](../raw/transcripts/16-logic-ii.md)). The goal of logic is to represent complicated things about the world compactly, as natural language does, and propositional logic is "very clunky and not expressive enough."

Two things are missing ([≈15:38](../raw/transcripts/16-logic-ii.md)):

- **Objects and predicates.** "Alice knows arithmetic" is one monolithic symbol, true or false, but it has internal structure: Alice, knows, arithmetic.
- **Quantifiers and variables.** "All" is a quantifier ranging over all people or objects, and often the objects cannot be enumerated. Programming without them is "like trying to program without for loops": you would have to write out every case ([≈15:38](../raw/transcripts/16-logic-ii.md), [≈16:23](../raw/transcripts/16-logic-ii.md)).

## First-Order Logic: Syntax

First-order logic is defined with the same three ingredients ([≈16:23](../raw/transcripts/16-logic-ii.md)). Its syntax has "a bit more machinery": in propositional logic formulas denote truth values, while in first-order logic formulas still denote truth values but **terms** denote **objects** ([≈17:10](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)).

**Terms** come in three kinds ([≈17:10](../raw/transcripts/16-logic-ii.md)–[≈18:43](../raw/transcripts/16-logic-ii.md)):

- **Constant symbols** such as $\text{alice}$ and $\text{arithmetic}$. These are not propositional symbols: a propositional symbol is a formula and is true or false, while $\text{alice}$ denotes an object, concrete or abstract. "Alice is not true or false."
- **Variables** such as $x$ and $y$.
- **Functions** applied to terms, such as $\text{father}(\text{alice})$ (father takes one object and returns an object) and $\text{add}(x, y)$ (add takes two). Functions take terms and return terms, so they build terms recursively: $\text{father}(\text{father}(\text{alice}))$, or $\text{add}(\text{add}(x, y), z)$.

**Formulas** ([≈18:43](../raw/transcripts/16-logic-ii.md)–[≈21:03](../raw/transcripts/16-logic-ii.md)):

- **Atomic formulas** are no longer just symbols but **predicates applied to terms**. A predicate takes some number of arguments and returns true or false. $\text{Snowing}$ is a 0-arity predicate, the propositional symbols of lecture 15; $\text{Student}$ is a 1-arity (unary) predicate, taking an object and returning a Boolean; $\text{Knows}$ is 2-arity (binary). So $\text{Knows}(x, \text{arithmetic})$ is an atomic formula whose arguments are terms.
- **Connectives** combine formulas exactly as in propositional logic: $\text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic})$.
- **Quantifiers** apply to formulas: put $\forall x$ or $\exists x$ in front of a formula in which the variable $x$ appears.

$$\forall x . \thinspace \text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic}) \qquad \exists x . \thinspace \text{Student}(x) \wedge \text{Knows}(x, \text{arithmetic})$$

**Non-formulas** are a good check on the distinction ([≈21:03](../raw/transcripts/16-logic-ii.md)–[≈22:36](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). $\text{father}(x)$ is a term, not a formula: a father is a person, not true or false. $\text{Knows}(\text{Student}, \text{arithmetic})$ is not a formula, because Student is a predicate, not a term; nor would $\text{Knows}(\text{Student}(x), \text{arithmetic})$ be, because $\text{Student}(x)$ returns a Boolean and every argument must denote an object. And with some function $\text{Foo}$, $\text{Foo}(\text{Knows}(\text{alice}, \text{arithmetic}))$ is not a formula, because Knows returns a Boolean and "you can't pass a Boolean into anything." Anything that appears as an argument must be a term, and the outermost symbol of a formula must return a Boolean. The course's convention, lowercase for terms and uppercase for formulas, helps keep them apart, though technically you can name them anything ([≈22:36](../raw/transcripts/16-logic-ii.md), [≈23:21](../raw/transcripts/16-logic-ii.md)).

Two student questions ([≈24:07](../raw/transcripts/16-logic-ii.md), [≈24:53](../raw/transcripts/16-logic-ii.md)). *Can a function return another function?* No: functions return terms. First-order logic is much more restrictive than Python, where anything goes, and "With a more restrictive language, it's easier to do stuff with, but it's less expressive." *Why is Knows(Student, arithmetic) not a formula?* Only a term can go there, and Student is a predicate. It is a common mistake: since Alice knows arithmetic is $\text{Knows}(\text{alice}, \text{arithmetic})$, it is natural to write "students know arithmetic" by putting Student in the same slot, but that is incorrect (the right translation uses a quantifier; see [below](#from-natural-language-to-first-order-logic)).

As with all logics, and languages in general, the definition is recursive: atomic formulas first, then how to build bigger formulas from them. What is new in first-order logic is building terms first and feeding them into predicates ([≈24:53](../raw/transcripts/16-logic-ii.md), [≈25:39](../raw/transcripts/16-logic-ii.md)).

## First-Order Logic: Semantics

A model represents a possible world; in propositional logic it assigns truth values to the atomic formulas. How should that lift to first-order logic ([≈25:39](../raw/transcripts/16-logic-ii.md)–[≈27:11](../raw/transcripts/16-logic-ii.md))?

**Attempt 1: assign truth values to atomic formulas.** The atomic formulas are now predicates applied to terms, so a model could be $\lbrace \text{Student}(\text{alice}) : \text{true}, \thinspace \text{Knows}(\text{alice}, \text{arithmetic}) : \text{true} \rbrace$ ([≈27:11](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). This has two problems.

1. **Infinitely many atomic formulas.** With functions, $\text{father}(\text{alice})$, $\text{father}(\text{father}(\text{alice}))$, $\text{father}(\text{father}(\text{father}(\text{alice})))$ and so on are all terms, so a single model would have to assign truth values to infinitely many atomic formulas, which is "no good" ([≈27:58](../raw/transcripts/16-logic-ii.md), [≈28:43](../raw/transcripts/16-logic-ii.md)). Without functions this approach is mostly fine.
2. **Two terms can denote the same object.** $\text{father}(\text{alice})$ and $\text{bob}$ are different terms, but Bob could be Alice's father. Assigning truth values formula by formula allows $\text{Knows}(\text{father}(\text{alice}), \text{arithmetic})$ to be true while $\text{Knows}(\text{bob}, \text{arithmetic})$ is false, with no coherence between atomic formulas that concern the same object ([≈28:43](../raw/transcripts/16-logic-ii.md), [≈29:29](../raw/transcripts/16-logic-ii.md)). Asked why the two could have different truth values, the lecturer says that is part of the point: such a model "should not even potentially be allowed," yet attempt 1 lets you write it down as a state of the world, inconsistencies and all ([≈34:04](../raw/transcripts/16-logic-ii.md)).

**Attempt 2: a layer of indirection.** Instead of mapping symbols straight to truth values, define the objects explicitly ([≈29:29](../raw/transcripts/16-logic-ii.md)–[≈32:30](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). A **domain** is a set of objects, here $\lbrace o_1, o_2, o_3 \rbrace$, given arbitrary names. An **interpretation** maps the primitive symbols (constants, functions and predicates) to things involving those objects:

- **Constants** map to objects: $\text{alice} \mapsto o_1$, $\text{bob} \mapsto o_2$, $\text{arithmetic} \mapsto o_3$.
- **Functions** map objects to objects: $\text{father}$ takes $o_1$ to $o_2$ ("o2 is a father of o1"). The functions work on the objects of the domain, not on the constants: the constants are "in syntax land," and the interpretation maps them into "a sort of canonical object space" ([≈31:00](../raw/transcripts/16-logic-ii.md)).
- **Predicates** map tuples of objects to truth values: $\text{Knows}$ is true of $(o_1, o_3)$ and false of $(o_2, o_3)$, and "every other combination I guess I'll just assume is false"; $\text{Student}$ is true of $o_1$ and false of $o_2$ and $o_3$ ([≈31:45](../raw/transcripts/16-logic-ii.md)).

A **model** in first-order logic is the domain together with the interpretation ([≈32:30](../raw/transcripts/16-logic-ii.md), [≈33:18](../raw/transcripts/16-logic-ii.md)). Asked whether the mapping is hardcoded, the lecturer says to think of it as a mathematical object, written in code to make it concrete and formal; you would rarely instantiate such a model in full, more often partially and lazily ([≈33:18](../raw/transcripts/16-logic-ii.md), [≈34:04](../raw/transcripts/16-logic-ii.md)).

**A model as a graph** ([≈34:04](../raw/transcripts/16-logic-ii.md)–[≈35:37](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). The figure draws the objects as nodes and the constant symbols as labels or pointers onto them: alice points to $o_1$, arithmetic to $o_3$, bob to $o_2$, and a second constant, robert, also points to $o_2$, added "just for fun" to show that constants and objects need not be one-to-one. A unary predicate attaches to a subset of the nodes (Student on $o_1$), and a binary predicate is a set of edges (Knows from $o_1$ to $o_3$ and from $o_2$ to $o_3$). Knowledge graphs "are basically these types of things that have some roots in first-order logic." (The figure draws $o_2$ knowing arithmetic, while the code's interpretation at the time of the lecture made it false; the course changed the code to match the figure the next day.)

**The interpretation function** $\mathcal{I}(f, w)$ again maps a formula and a model to true or false, and is again defined by recursion on the formula, "more complicated because there are more moving parts" ([≈35:37](../raw/transcripts/16-logic-ii.md)–[≈37:13](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). The lecture's `interpret_formula` takes a formula, a model and a **substitution** mapping variables to objects, explained below. It has cases for $\forall$, $\exists$, negation, the binary connectives (handled exactly as in propositional logic) and predicates; a separate `interpret_term` maps a term to an object: a variable through the substitution, a constant through the interpretation, and a function application by interpreting its arguments and applying the function's mapping.

*Worked example:* $\text{Knows}(\text{alice}, \text{arithmetic})$ ([≈38:01](../raw/transcripts/16-logic-ii.md)–[≈40:23](../raw/transcripts/16-logic-ii.md)). This is a binary predicate with arguments alice and arithmetic. Look up what the predicate symbol Knows means in $w$: a mapping from pairs of objects to truth values. Interpret the first argument: alice is a constant, which the interpretation sends to $o_1$. Interpret the second: arithmetic goes to $o_3$. Then look up $(o_1, o_3)$ in Knows's mapping: true. So $\mathcal{I}(\text{Knows}(\text{alice}, \text{arithmetic}), w) = \text{true}$. The general recipe: break the formula down, and at the leaves look up the appropriate items in $w$.

*Worked example:* $\forall x . \thinspace \text{Knows}(x, \text{arithmetic})$ ([≈40:23](../raw/transcripts/16-logic-ii.md)–[≈42:44](../raw/transcripts/16-logic-ii.md)). For a universal quantifier, iterate over every object in the domain, bind the variable to it in the substitution, and interpret the body; the formula is true if the body holds for all objects. Here $o_1$ knows arithmetic, but $o_2$ does not ("actually here I've said o2 doesn't know arithmetic"), so the result is **false**. (An existential quantifier is the same loop with "or" in place of "and.")

A student asks why "and" and "or" are handled differently from other predicates ([≈43:31](../raw/transcripts/16-logic-ii.md), [≈44:16](../raw/transcripts/16-logic-ii.md)). The connectives combine formulas: interpret $g$ and $h$ and combine the truth values, as in propositional logic. A binary predicate is an atomic formula whose arguments are not formulas but terms, so evaluating them returns objects, not Booleans; the predicate's interpretation in $w$ is then looked up to turn that pair of objects into true or false.

In summary, the semantics of every logic is defined by its interpretation function. In first-order logic the model is a domain plus an interpretation of all the symbols, and formulas and terms are interpreted recursively ([≈42:44](../raw/transcripts/16-logic-ii.md)). See [first-order logic](first-order-logic.md) for the whole recursion.

## Inference by Propositionalization

Inference, as before, means Ask and Tell, which reduce to entailment, contradiction and contingency, which reduce to satisfiability ([≈45:03](../raw/transcripts/16-logic-ii.md)). One approach is to reduce the first-order knowledge base to propositional logic, where model checking is available ([≈45:03](../raw/transcripts/16-logic-ii.md)–[≈48:54](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). The example knowledge base is

$$\text{Student}(\text{alice}), \quad \text{Student}(\text{bob}), \quad \forall x . \thinspace \text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic}), \quad \exists x . \thinspace \text{Student}(x) \wedge \text{Knows}(x, \text{arithmetic})$$

This cannot be done in general, because first-order logic is more powerful. It can be done under two assumptions about how objects relate to constants:

- **Unique names**: each object corresponds to **at most one** constant. (The slide as taught garbled this line; the lecturer corrected it aloud, "this should say each object corresponds to at most one constant" ([≈45:49](../raw/transcripts/16-logic-ii.md)), and the course fixed the text the next day.)
- **Domain closure**: each object corresponds to **at least one** constant.

The figure shows three candidate models for the constants john and bob ([≈46:35](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). In $w_1$, john names $o_1$ and bob names $o_2$: allowed. In $w_2$, john and bob both name $o_1$, so $o_1$ does not have a unique name: ruled out by unique names. In $w_3$, a third object $o_2$ has no name at all: ruled out by domain closure. Together the two assumptions mean there is a one-to-one relationship between objects and constants.

Then you can **propositionalize** ([≈46:35](../raw/transcripts/16-logic-ii.md)–[≈48:54](../raw/transcripts/16-logic-ii.md)). Each atomic formula becomes a propositional symbol, by removing "the commas and the parentheses and the spaces": StudentAlice, StudentBob, KnowsAliceArithmetic, KnowsBobArithmetic. A $\forall$ becomes a conjunction over all constants, and an $\exists$ a disjunction:

$$(\text{StudentAlice} \rightarrow \text{KnowsAliceArithmetic}) \wedge (\text{StudentBob} \rightarrow \text{KnowsBobArithmetic})$$

$$(\text{StudentAlice} \wedge \text{KnowsAliceArithmetic}) \vee (\text{StudentBob} \wedge \text{KnowsBobArithmetic})$$

What remains is propositional logic, and any technique from last time applies. In this regime first-order logic is **syntactic sugar** for propositional logic: "the same expressivity, but it's just easier to read and write." See [propositionalization](propositionalization.md).

## Modus Ponens with Substitution and Unification

The general case calls for inference rules, which match formulas in the knowledge base and produce new ones ([≈48:54](../raw/transcripts/16-logic-ii.md); introducing this part, the lecturer says "let's look at propositional logic in general," meaning first-order logic). Lecture 15's modus ponens is extended to first-order logic in two attempts ([≈49:41](../raw/transcripts/16-logic-ii.md)–[≈1:03:03](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)).

**Definite clauses.** Modus ponens assumes a special type of formula. A **definite clause** has the form

$$\forall x_1 \ldots x_n . \thinspace (a_1 \wedge \cdots \wedge a_k) \rightarrow b$$

where $x_1, \ldots, x_n$ are variables and $a_1, \ldots, a_k, b$ are atomic formulas ([≈49:41](../raw/transcripts/16-logic-ii.md)). For example,

$$\forall x, y, z . \thinspace (\text{Takes}(x, y) \wedge \text{Covers}(y, z)) \rightarrow \text{Knows}(x, z)$$

says that if $x$ takes a class $y$ and $y$ covers a concept $z$, then $x$ knows $z$ ([≈50:29](../raw/transcripts/16-logic-ii.md)). (On the slide as taught, the code under this printed formula built $\text{Knows}(x, y)$ in place of $\text{Takes}(x, y)$; the course fixed it the next day.) Anything involving disjunction is not a definite clause: $\text{Student}(\text{alice}) \vee \text{Student}(\text{bob})$ is not, and neither is $\exists x . \thinspace \text{Student}(x) \wedge \text{Knows}(x, \text{arithmetic})$, because "exists is basically a compact representation of or, and for all is a compact representation of and" ([≈50:29](../raw/transcripts/16-logic-ii.md), [≈51:19](../raw/transcripts/16-logic-ii.md)).

**Attempt 1: modus ponens with exact match.** From premises $a_1, \ldots, a_k$ and $\forall x_1 \ldots x_n . \thinspace (a_1 \wedge \cdots \wedge a_k) \rightarrow b$, conclude $b$. It looks like propositional modus ponens with a $\forall$ in front ([≈51:19](../raw/transcripts/16-logic-ii.md), [≈52:08](../raw/transcripts/16-logic-ii.md)). Take the knowledge base $\text{Takes}(\text{alice}, \text{cs221})$, $\text{Covers}(\text{cs221}, \text{logic})$ and the rule above. Intuitively we should infer $\text{Knows}(\text{alice}, \text{logic})$, but the rule cannot be applied: rules are syntactic and check equality, and $\text{Takes}(\text{alice}, \text{cs221})$ is not the same string as $\text{Takes}(x, y)$ ([≈52:57](../raw/transcripts/16-logic-ii.md), [≈53:43](../raw/transcripts/16-logic-ii.md)). "It's kind of dumb, but that's the rules, the rules are just the rules. You just follow them." In propositional logic this never came up, because Rain matches Rain exactly ([≈53:43](../raw/transcripts/16-logic-ii.md), [≈54:30](../raw/transcripts/16-logic-ii.md)).

The fix comes from what $\forall$ means: the rule holds for any $x$, $y$ and $z$, so in particular for $x = \text{alice}$ and $y = \text{cs221}$. The rule is "really like a rule template," and two operations make that precise ([≈54:30](../raw/transcripts/16-logic-ii.md), [≈55:16](../raw/transcripts/16-logic-ii.md)).

**Substitution** is a mechanism to "do surgery on formulas": search and replace on the terms of a formula ([≈55:16](../raw/transcripts/16-logic-ii.md)–[≈58:24](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). Applying the substitution $\lbrace x : \text{alice}, y : \text{cs221} \rbrace$ (a dictionary from variables to terms, as the code writes it: $x$ maps to alice, $y$ to cs221) to $\text{Knows}(x, y)$ recurses on the two arguments, replaces each, and reassembles: $\text{Knows}(\text{alice}, \text{cs221})$. Applying $\lbrace x : \text{alice}, y : z \rbrace$ to $\text{Student}(x) \wedge \text{Knows}(x, y)$ gives $\text{Student}(\text{alice}) \wedge \text{Knows}(\text{alice}, z)$: what a variable is replaced with can itself be a variable.

**Unification** is "generalized equality": find a substitution that makes two formulas equal, or report that none exists ([≈58:24](../raw/transcripts/16-logic-ii.md)–[≈1:00:43](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). Only variables can be substituted: "I can replace $x$ with anything I want," but not alice with bob. The traced examples:

- $\text{Knows}(x, y)$ with $\text{Knows}(\text{alice}, \text{bob})$ gives $\lbrace x : \text{alice}, y : \text{bob} \rbrace$; applying it to both makes them identical.
- $\text{Knows}(\text{alice}, y)$ with $\text{Knows}(x, z)$ gives $\lbrace x : \text{alice}, y : z \rbrace$, and both become $\text{Knows}(\text{alice}, z)$.
- $\text{Knows}(\text{alice}, y)$ with $\text{Knows}(\text{bob}, z)$ fails, because alice and bob cannot be unified.

The algorithm recurses: if the two formulas are equal, succeed; if either is a variable, bind it to the other; otherwise the predicate or function symbols must match (Knows cannot be replaced with Takes), and each pair of arguments is unified in turn, failing if any fails ([≈59:10](../raw/transcripts/16-logic-ii.md)). See [unification](unification.md).

**Attempt 2: modus ponens with substitution and unification** ([≈1:00:43](../raw/transcripts/16-logic-ii.md)–[≈1:03:03](../raw/transcripts/16-logic-ii.md)). The premises are formulas $a_1', \ldots, a_k'$ from the knowledge base (primed to distinguish them from the rule's) and the rule $\forall x_1 \ldots x_n . \thinspace (a_1 \wedge \cdots \wedge a_k) \rightarrow b$. Unify the premises with the rule's left-hand side,

$$\theta = \text{unify}(a_1' \wedge \cdots \wedge a_k', \thinspace a_1 \wedge \cdots \wedge a_k)$$

and if that succeeds, the conclusion is not $b$ itself but $b' = \text{substitute}(b, \theta)$. In the example, unifying $\text{Takes}(\text{alice}, \text{cs221}) \wedge \text{Covers}(\text{cs221}, \text{logic})$ with $\text{Takes}(x, y) \wedge \text{Covers}(y, z)$ gives $\theta = \lbrace x : \text{alice}, y : \text{cs221}, z : \text{logic} \rbrace$, and substituting into $\text{Knows}(x, z)$ concludes $\text{Knows}(\text{alice}, \text{logic})$. Substitution and unification "bridge the gap between these general variables and these concrete instances."

**Complexity** ([≈1:03:03](../raw/transcripts/16-logic-ii.md)–[≈1:04:34](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). Each application of modus ponens produces an atomic formula, such as $\text{Knows}(\text{alice}, \text{logic})$. Without functions, the number of atomic formulas that can ever be produced is

$$(\text{number of constant symbols})^{\text{maximum predicate arity}}$$

The lecturer's example is 10 constant symbols and binary predicates; the captions garble the count he then states, which by the formula is $10^2 = 100$ ([≈1:03:49](../raw/transcripts/16-logic-ii.md)). With functions the number can be infinite: $\text{Knows}(\text{alice}, \text{logic})$, $\text{Knows}(\text{father}(\text{alice}), \text{logic})$, $\text{Knows}(\text{father}(\text{father}(\text{alice})), \text{logic})$, and so on. A rule such as "if $x$ knows logic, then the father of $x$ knows logic" generates infinitely many formulas ([≈1:04:34](../raw/transcripts/16-logic-ii.md)).

**Soundness and completeness** ([≈1:04:34](../raw/transcripts/16-logic-ii.md)–[≈1:06:08](../raw/transcripts/16-logic-ii.md)). Modus ponens is **sound**: if it derives a formula, the knowledge base entails it. It is **not complete**: some entailed formulas cannot be derived, a simple kind being anything with a disjunction in it. Completeness for first-order logic needs another rule, **resolution**, which is "a little bit more complicated"; it was covered in previous offerings of the course and is skipped this time ([≈1:05:21](../raw/transcripts/16-logic-ii.md), [≈1:06:08](../raw/transcripts/16-logic-ii.md)).

In summary ([≈1:06:08](../raw/transcripts/16-logic-ii.md)): modus ponens works on definite clauses, which have an if-then structure, such as if Rain then Wet, or if $\text{City}(x)$ then $\text{Place}(x)$. The intuition is that no disjunction is allowed. Substitution is search and replace on the terms of a formula, unification finds a substitution that makes two formulas equal, and both are needed because first-order rules must match variables with constants. See [inference rules](inference-rules.md).

## From Natural Language to First-Order Logic

The last part is translation, which part of the assignment involves ([≈1:06:56](../raw/transcripts/16-logic-ii.md)–[≈1:11:35](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)). First-order logic is "a very natural way of encoding the meaning of natural language sentences."

- *Alice and Bob both know arithmetic*: $\text{Knows}(\text{alice}, \text{arithmetic}) \wedge \text{Knows}(\text{bob}, \text{arithmetic})$.
- *All students know arithmetic*: $\forall x . \thinspace \text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic})$.
- *Some student knows arithmetic*: $\exists x . \thinspace \text{Student}(x) \wedge \text{Knows}(x, \text{arithmetic})$.

**The pairing rule** ([≈1:07:42](../raw/transcripts/16-logic-ii.md)–[≈1:09:14](../raw/transcripts/16-logic-ii.md)). Universal quantification is usually paired with an implication, and existential quantification with a conjunction; "99% of the time this is basically the rule you should follow." Mixing them up is probably wrong:

- $\forall x . \thinspace \text{Student}(x) \wedge \text{Knows}(x, \text{arithmetic})$ says every object in the domain is a student and knows arithmetic, not what was meant.
- $\exists x . \thinspace \text{Student}(x) \rightarrow \text{Knows}(x, \text{arithmetic})$ says some object is either not a student or knows arithmetic, so it is true as long as there is a single non-student.

"Just remember: for all, implication; exists, and."

**More examples** ([≈1:09:59](../raw/transcripts/16-logic-ii.md)–[≈1:11:35](../raw/transcripts/16-logic-ii.md)):

- *There is some course that every student has taken*: $\exists x . \thinspace \forall y . \thinspace \text{Student}(y) \rightarrow \text{Takes}(y, x)$. The lecturer notes that "course should be in there": technically it should be $\exists x . \thinspace \text{Course}(x) \wedge \forall y . \thinspace \text{Student}(y) \rightarrow \text{Takes}(y, x)$, which also shows the pairing rule twice, an $\exists$ with an $\wedge$ and a $\forall$ with an $\rightarrow$ (the course added $\text{Course}(x)$ to the slide the next day).
- *Every even integer greater than 2 is the sum of two prime numbers*: $\forall x . \thinspace \text{Even}(x) \wedge \text{GreaterThan}(x, \text{two}) \rightarrow \exists y, z . \thinspace \text{Prime}(y) \wedge \text{Prime}(z) \wedge \text{add}(y, z) = x$, the statement propositional logic could not write.
- *If a student takes a course and the course covers a concept, then the student knows the concept*: $\forall x, y, z . \thinspace \text{Student}(x) \wedge \text{Takes}(x, y) \wedge \text{Course}(y) \wedge \text{Covers}(y, z) \wedge \text{Concept}(z) \rightarrow \text{Knows}(x, z)$.

The last is a definite clause, so modus ponens can use it; the first two are not, because of their $\exists$ ([≈1:11:35](../raw/transcripts/16-logic-ii.md)). Translation takes practice, much like translating an informal specification into code: "it's just a different language that you have to get used to."

## Summary and Beyond First-Order Logic

The summary ([≈1:12:22](../raw/transcripts/16-logic-ii.md)–[≈1:13:56](../raw/transcripts/16-logic-ii.md), [slides](../raw/slides/16-logic-ii.md)): first-order logic is more powerful than propositional logic. It adds terms, which denote objects, functions, which derive objects from objects, and predicates, which take objects and return Booleans; variables and quantifiers operate over all objects, like "a for loop over objects," which is where its power comes from, the way a for loop lets you write one-liners that do a lot. Its semantics is an interpretation function that maps the constants, functions and predicates to the objects of a domain. With unique names and domain closure, a first-order KB can be propositionalized; with definite clauses, modus ponens with substitution and unification applies; completeness needs resolution, which the course does not cover.

**Higher-order logics** ([≈1:13:56](../raw/transcripts/16-logic-ii.md), [≈1:14:42](../raw/transcripts/16-logic-ii.md)). The course's journey in logic ends here, but there are logics that express more. Try writing *70% of students know machine learning* in first-order logic and you will find you cannot: it requires treating the students as a set and operating on that set, not just quantifying over individual elements. The lecturer ties this to the earlier question about functions that return functions.

Next time the course zooms out to think more broadly about AI: [language models](17-language-models.md) and AI in society, the last three lectures, followed by a final lecture in a fireside-chat format with some presentations ([≈1:14:42](../raw/transcripts/16-logic-ii.md), [≈1:15:29](../raw/transcripts/16-logic-ii.md)).

---

- **[Slides](../raw/slides/16-logic-ii.md)**: the full content of `first_order_logic.py` as taught (pinned to commit c88136d, "add first-order logic", which landed a minute before the lecture), every traced Ask answer, interpretation, substitution and unification, prose descriptions of the two new figures, and the slips the course corrected the next day. Rendered lecture: <https://stanford-cs221.github.io/autumn2025-lectures/?trace=first_order_logic> (this may now show a later revision, not the version taught).
- **[Transcript](../raw/transcripts/16-logic-ii.md)**: edited and timestamped. Verbatim captions are in `original/`.
