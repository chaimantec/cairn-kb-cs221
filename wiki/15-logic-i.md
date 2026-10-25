---
title: Lecture 15 — Logic I
---

# Lecture 15 — Logic I

Lecture 15 opens the logic unit, "the last technical topic of this course" before the AI and society section ([≈0:05](../raw/transcripts/15-logic-i.md)). After a long stretch on reasoning with search, MDPs, games and, most recently, [Bayesian networks](bayesian-networks.md), the course turns from probabilistic to **logical reasoning**. In some ways logic is more primitive, because it does not account for uncertainty, "but we'll see that that simplification allows us to do fancier things" ([≈0:52](../raw/transcripts/15-logic-i.md)). The lecture treats a logic as a **language** defined by three ingredients: syntax, semantics and inference rules. It works all three out for [propositional logic](propositional-logic.md), the simplest logic, as a vehicle for concepts that apply to every logic: models and the interpretation function, knowledge bases, [entailment, contradiction and contingency](entailment.md) and the Ask and Tell operations built on them, [satisfiability](satisfiability.md) and model checking, and [inference rules](inference-rules.md) with their soundness and completeness. A short section connects all of this back to probabilistic inference in Bayesian networks.

Notation, as in the lecture: propositional symbols are capitalized names ($P$, $Q$, $\text{Rain}$, $\text{Wet}$, $A$, $B$, $C$); $f$ and $g$ are formulas; $w$ is a model (think "world"); $\mathcal{I}(f, w)$ is the interpretation function; $\mathcal{M}(f)$ is the set of models of a formula $f$, and $\mathcal{M}(\text{KB})$ the set of models of a knowledge base KB. $\text{KB} \models f$, with two horizontal lines, reads "KB entails $f$" and is semantic; $\text{KB} \vdash f$, with one, reads "KB derives (or proves) $f$" and is syntactic ([≈33:29](../raw/transcripts/15-logic-i.md), [≈1:05:43](../raw/transcripts/15-logic-i.md)). In the lecture's code, formulas are built with the Z3 library and a model is a dictionary from symbols to true or false ([slides](../raw/slides/15-logic-i.md)).

## Why Logic?

The lecture starts with a problem: if $A + B = 10$ and $A - B = 4$, what is $A$? A student calls out 7 ([≈0:52](../raw/transcripts/15-logic-i.md)). Nobody solved it by checking all possible values of $A$ and $B$, which would be [search](search-problems.md), the other way the course has thought about reasoning. You did algebra: add the two equations to get $2A = 14$, divide both sides by 2. That is **logical or symbolic reasoning**, and it is powerful: it solved a problem with infinitely many candidate answers, in your head ([≈1:38](../raw/transcripts/15-logic-i.md), [≈2:26](../raw/transcripts/15-logic-i.md)).

**A historical note.** As [lecture 1](01-course-overview.md) said, logic was the dominant paradigm in AI before the 1990s; it was what people meant by AI when John McCarthy coined the term ([≈2:26](../raw/transcripts/15-logic-i.md)). It had two problems. It is deterministic, so it does not handle **uncertainty** (something is either true or false, "but the real world isn't so simple"), which probability addresses. And it is rule-based, so it did not leverage **data**, which machine learning addresses ([slides](../raw/slides/15-logic-i.md)). So why study it now, near the end of a course that might be expected to culminate in language models? Because logic has one strength the lecturer calls "sort of irreplaceable": **expressivity in a compact way** ([≈3:11](../raw/transcripts/15-logic-i.md)).

**Logic as a language.** The way to think about logic is as a language, with two goals: to **represent knowledge** about the world (as a textbook does) and to **reason** with that knowledge ([≈3:11](../raw/transcripts/15-logic-i.md)). Why not just use natural language, especially now that we have language models? Because natural language is slippery ([≈3:58](../raw/transcripts/15-logic-i.md), [≈4:44](../raw/transcripts/15-logic-i.md)). "A dime is better than a nickel; a nickel is better than a penny; therefore a dime is better than a penny" is a sound inference. "A penny is better than nothing; nothing is better than world peace; therefore a penny is better than world peace" has exactly the same form and is absurd. "You can't just substitute words and hope for the best"; the lecturer leaves the class to think about why it fails.

Languages come in kinds ([≈4:44](../raw/transcripts/15-logic-i.md), [≈5:31](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). **Natural languages** such as English and German are informal. **Programming languages** such as Python and C++ are formal. **Logical languages** are also formal: the slide renders the English sentence "Two divides even numbers" in first-order logic, `∀x. Even(x) → Divides(x, 2)`, and in description logic, `Even ⊑ ∀ Divides.2`. Description logic will not be covered; it is there to show that there is not one logic but "a whole set of logics, each with its own language," useful for different purposes. First-order logic is next lecture.

## The Three Ingredients of a Logic

Every logic is defined by three things ([≈5:31](../raw/transcripts/15-logic-i.md), [≈6:16](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)):

1. **Syntax** defines the set of valid formulas: the statements you can make, like sentences.
2. **Semantics** defines what those formulas mean.
3. **Inference rules** derive new formulas from existing ones, in a way that "has something to do with what these sentences mean."

The lecture's picture (`syntax-semantics-rules.png`, described in the [slides](../raw/slides/15-logic-i.md)) puts a stack of formulas on the syntax side, an inference-rule arrow producing a new formula from them, and on the semantics side nested sets of models that the formulas point to.

**Syntax versus semantics** is the distinction everything rests on ([≈6:16](../raw/transcripts/15-logic-i.md), [≈7:02](../raw/transcripts/15-logic-i.md)). Syntax asks which expressions are valid; semantics asks what they mean. `2 + 3` and `3 + 2` are different syntax with the same semantics, the number 5. And `3 / 2` is the same syntax with different semantics: 1 under Python 2.7, 1.5 under Python 3, "a cause of probably many bugs." The point is that the meaning of a string is not contained in the string; it depends on the semantics, which give "the sentence some life" ([≈7:48](../raw/transcripts/15-logic-i.md); the caption garbles the spoken expression at this point).

## Propositional Logic: Syntax

Propositional logic is "basically the simplest form of logic," used here to develop concepts such as entailment and soundness, which have "a very, very precise meaning in logic" and matter more than propositional logic itself ([≈8:34](../raw/transcripts/15-logic-i.md)).

Its syntax is defined recursively ([≈9:20](../raw/transcripts/15-logic-i.md)–[≈11:38](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). The base case is the **propositional symbols**, also called **atomic formulas**: any identifier, like a variable name, such as $P$, $Q$, $\text{Rain}$, $\text{Wet}$. "These are just names, and these are just symbols"; what they mean is deliberately left for later. The inductive case uses the **logical connectives**: if $f$ and $g$ are formulas, then so are

$$\neg f, \qquad f \wedge g, \qquad f \vee g, \qquad f \rightarrow g, \qquad f \leftrightarrow g$$

(negation, and, or, implication, and bidirectional implication or equivalence). Nothing else is a formula. So $P$, $\neg P$, $\neg P \vee Q$ and $P \rightarrow (Q \vee \neg P)$ are formulas, while "P ¬Q" (no connective between the symbols), "P + Q" (plus is not a connective) and "P(A) ∨ Q(B)" (no such parentheses in propositional logic) are not, though they might be formulas in other logics.

It is hard not to read meaning into $\wedge$ and $\vee$, but "I could have easily called these like foo and bar"; so far they are only symbols ([≈11:38](../raw/transcripts/15-logic-i.md), [≈12:25](../raw/transcripts/15-logic-i.md)).

## Semantics: Models and the Interpretation Function

Semantics needs "a lot of terms. Each term isn't complicated, but you just have to remember them" ([≈12:25](../raw/transcripts/15-logic-i.md)).

**Model.** A **model** $w$ in propositional logic is an assignment of truth values to the propositional symbols, and it represents a state of the world; "anytime you see model, you can think about world." This is a model in the logical sense, not a machine learning model, "unfortunately a clash of terminology" ([≈12:25](../raw/transcripts/15-logic-i.md), [≈13:12](../raw/transcripts/15-logic-i.md)). With three symbols $A$, $B$, $C$ there are $2^3 = 8$ models, from all false to all true, and together they are all the ways the world could be ([≈14:00](../raw/transcripts/15-logic-i.md)).

**Interpretation function.** The bridge from syntax to semantics is the **interpretation function** $\mathcal{I}(f, w)$, which takes a formula $f$ and a model $w$ and returns true (1) if $w$ satisfies $f$ and false (0) if it does not ([≈14:00](../raw/transcripts/15-logic-i.md), [≈14:48](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). Picture the space of all models, with the formula sitting outside it in syntax world; $\mathcal{I}$ checks the formula against one point of that space.

It is defined recursively, following the recursive syntax ([≈15:34](../raw/transcripts/15-logic-i.md)–[≈19:28](../raw/transcripts/15-logic-i.md)). For a propositional symbol, look up its value in $w$. For $\neg g$, negate $\mathcal{I}(g, w)$. For $g \wedge h$, $g \vee h$, $g \rightarrow h$ and $g \leftrightarrow h$, interpret $g$ and $h$ and combine the two results with the corresponding operation; the code computes $g \rightarrow h$ as "not $\mathcal{I}(g, w)$, or $\mathcal{I}(h, w)$", and $g \leftrightarrow h$ as equality of the two values.

**Worked example** ([≈14:48](../raw/transcripts/15-logic-i.md)–[≈17:06](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). Take $f = (\neg A \wedge B) \leftrightarrow C$ and the model $w$ in which $A$ is true, $B$ is true and $C$ is false. Evaluate it as a tree, from the leaves up:

- $\mathcal{I}(A, w) = 1$, so $\mathcal{I}(\neg A, w) = 0$.
- $\mathcal{I}(B, w) = 1$, so $\mathcal{I}(\neg A \wedge B, w) = 0$ ("and"-ing 0 and 1 gives 0).
- $\mathcal{I}(C, w) = 0$.
- The biconditional asks whether its two sides are equal; 0 equals 0, so $\mathcal{I}((\neg A \wedge B) \leftrightarrow C, w) = 1$.

The lecture's figure draws this full tree. In the traced code, Python's `and` stops as soon as $\neg A$ comes back false, so $B$ is never actually evaluated; the answer is the same ([slides](../raw/slides/15-logic-i.md)).

"For any formula, however complicated, I can put it in the interpretation function, against any world, and I can get a boolean" ([≈18:41](../raw/transcripts/15-logic-i.md)). The idea is not specific to these connectives: "you can define any language you want with different symbols, and as long as you define an interpretation function, you have a logic to work with" ([≈19:28](../raw/transcripts/15-logic-i.md), [≈20:16](../raw/transcripts/15-logic-i.md)).

## Models of a Formula

The interpretation function is where semantics starts, but the object used from then on is the **set of models of a formula** ([≈20:16](../raw/transcripts/15-logic-i.md), [≈24:09](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)):

$$\mathcal{M}(f) = \lbrace w : \mathcal{I}(f, w) = 1 \rbrace$$

the worlds in which $f$ is true. Think of $f$ as a sentence: $\text{Rain} \vee \text{Wet}$ is "it is raining or wet," and its meaning is the set of all possible worlds we could be in for that sentence to be true.

The lecture's `get_models` computes it by brute force: enumerate every model, keep the ones the interpretation function accepts ([≈21:02](../raw/transcripts/15-logic-i.md)–[≈23:21](../raw/transcripts/15-logic-i.md)). With the symbols $\text{Rain}$ and $\text{Wet}$ there are four models. Writing each as a (Rain, Wet) pair of 0s and 1s, the traced results are:

- $\mathcal{M}(\text{Rain} \vee \text{Wet})$ = $(0, 1)$, $(1, 0)$, $(1, 1)$: wet but not raining, raining but not wet, or both.
- $\mathcal{M}(\text{Rain} \wedge \text{Wet})$ = $(1, 1)$ only.

The lesson is **compactness**: a small formula represents a potentially very large set of models. With 100 propositional symbols there are $2^{100}$ models, and "a very simple propositional formula" might represent trillions of them ([≈23:21](../raw/transcripts/15-logic-i.md)).

## Knowledge Bases

A **knowledge base** (KB) is a set of formulas: "our list of things that we know to be true about the world," a set of facts you add to over time ([≈24:54](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). The lecturer calls it "in some sense, syntactic sugar." Its semantics, $\mathcal{M}(\text{KB})$, is the set of models that satisfy **every** formula in it: all the possible worlds we could be in, given what we know ([≈25:40](../raw/transcripts/15-logic-i.md)).

**Example** ([≈25:40](../raw/transcripts/15-logic-i.md)–[≈28:50](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). Let $\text{KB} = \lbrace \text{Rain}, \text{Rain} \rightarrow \text{Wet} \rbrace$: we know it is raining, and that whenever it rains it is wet. One way to get its models is to intersect the models of its formulas:

- $\mathcal{M}(\text{Rain})$ = $(1, 0)$, $(1, 1)$: raining, wet or not.
- $\mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ = $(0, 0)$, $(0, 1)$, $(1, 1)$. The only world missing is "raining and not wet." The implication does not say whether it is raining, only that if it is, it must be wet.
- The intersection is $(1, 1)$: raining and wet, which is what common sense says.

Equivalently, a KB is semantically the **conjunction** of its formulas: put an "and" between them, $\text{Rain} \wedge (\text{Rain} \rightarrow \text{Wet})$, and its models are again just $(1, 1)$. The lecture goes back and forth between the two views, a set of facts or one big formula.

**Knowledge shrinks the models.** Adding a formula makes the KB bigger, $\text{KB} \subseteq \text{KB} \cup \lbrace f \rbrace$, but its set of models smaller, $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace) \subseteq \mathcal{M}(\text{KB})$, because each new fact intersects one more restriction onto the worlds still possible ([≈28:50](../raw/transcripts/15-logic-i.md)–[≈30:25](../raw/transcripts/15-logic-i.md)). The larger $\mathcal{M}(\text{KB})$ is, the more uncertain we are; think of the KB as a set of constraints on the possible worlds. "As we gain more knowledge, we narrow down the possible worlds we might be in."

## Entailment, Contradiction and Contingency

Since adding $f$ can only shrink $\mathcal{M}(\text{KB})$, the key question is **how much** it shrinks, and there are exactly three cases ([≈31:10](../raw/transcripts/15-logic-i.md)–[≈38:05](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). The [entailment](entailment.md) topic page goes through them in more depth.

**Entailment: no shrinking.** KB entails $f$, written $\text{KB} \models f$, if and only if

$$\mathcal{M}(\text{KB} \cup \lbrace f \rbrace) = \mathcal{M}(\text{KB})$$

so $f$ adds no information. In the figure, $\mathcal{M}(\text{KB})$ sits entirely inside a larger $\mathcal{M}(f)$: in every world consistent with the knowledge base, $f$ is true ([≈31:57](../raw/transcripts/15-logic-i.md), [≈33:29](../raw/transcripts/15-logic-i.md)). Example: $\lbrace \text{Rain}, \text{Rain} \rightarrow \text{Wet} \rbrace$ entails $\text{Rain}$; the models before and after are both $(1, 1)$ ([≈32:44](../raw/transcripts/15-logic-i.md)).

**Contradiction: shrinking to nothing.** KB contradicts $f$ if and only if

$$\mathcal{M}(\text{KB} \cup \lbrace f \rbrace) = \emptyset$$

so $f$ is not compatible with the KB; there is no world in which both are true, and the figure shows the two sets of models disjoint ([≈33:29](../raw/transcripts/15-logic-i.md), [≈34:15](../raw/transcripts/15-logic-i.md)). Example: $\lbrace \text{Rain}, \text{Wet} \rbrace$ contradicts $\neg \text{Wet}$; the old models are $(1, 1)$, the new set is empty ([≈34:15](../raw/transcripts/15-logic-i.md), [≈35:00](../raw/transcripts/15-logic-i.md)).

**Contingency: everything in between.** $f$ is contingent with respect to KB if and only if

$$\emptyset \neq \mathcal{M}(\text{KB} \cup \lbrace f \rbrace) \neq \mathcal{M}(\text{KB})$$

so $f$ shrinks the set of models but not completely; the figure shows the two sets partly overlapping. This is "the more interesting case, at least when you're adding information" ([≈35:00](../raw/transcripts/15-logic-i.md), [≈35:46](../raw/transcripts/15-logic-i.md)). Example: with $\text{KB} = \lbrace \text{Rain} \rbrace$ and $f = \text{Wet}$, the models go from $(1, 0)$, $(1, 1)$ to $(1, 1)$: not empty, and not the same ([≈35:46](../raw/transcripts/15-logic-i.md), [≈36:33](../raw/transcripts/15-logic-i.md)).

**Entailment and contradiction are two sides of one coin.** KB contradicts $f$ if and only if $\text{KB} \models \neg f$ ([≈36:33](../raw/transcripts/15-logic-i.md), [≈37:18](../raw/transcripts/15-logic-i.md)). The models of $\neg f$ are everything outside $\mathcal{M}(f)$, so if $\mathcal{M}(\text{KB})$ lies inside $\mathcal{M}(f)$ (entailment), it misses $\mathcal{M}(\neg f)$ entirely: KB contradicts $\neg f$ whenever KB entails $f$. The satisfiability reduction below depends on this.

**Who is at fault in a contradiction?** A student asked whether the knowledge base or $f$ is wrong when they contradict. The logic "is not opinionated about that": there is no notion of right or wrong, only sets of models, and it could be either ([≈38:05](../raw/transcripts/15-logic-i.md)).

## Ask and Tell

All this machinery supports two operations on a knowledge base ([≈38:51](../raw/transcripts/15-logic-i.md)–[≈47:20](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). **Ask** (written Ask[f] on the slide) poses a yes/no question $f$, and propositional logic only supports yes/no questions because propositions are boolean. **Tell** (Tell[f]) gives the KB a new statement $f$. Each response depends only on which of the three relations holds between KB and $f$.

**Ask** answers yes, no, or I don't know. To answer a query, "we're going to pretend to add it into the KB and see what changes" ([≈41:58](../raw/transcripts/15-logic-i.md)). The traced examples:

| Knowledge base | Question | Relation | Answer |
| --- | --- | --- | --- |
| Rain, Wet | Is it raining or wet? ($\text{Rain} \vee \text{Wet}$) | entailment | Yes |
| not Rain, Wet | Is it raining? ($\text{Rain}$) | contradiction | No |
| Rain | Is it wet? ($\text{Wet}$) | contingency | I don't know |

"Yes" means absolutely yes: in all possible worlds consistent with the KB, $f$ is true ([≈41:11](../raw/transcripts/15-logic-i.md)). "No" means it is inconceivable that $f$ is true: none of the KB's worlds is consistent with it ([≈41:58](../raw/transcripts/15-logic-i.md)). "I don't know" means it depends which of the remaining worlds we are in; "another way of wording it is it depends" ([≈42:46](../raw/transcripts/15-logic-i.md)). Ask assumes the knowledge base is correct and treats $f$ as the thing being tested, which answers the earlier question about fault ([≈41:58](../raw/transcripts/15-logic-i.md)).

**Tell** answers "I already knew that," "I don't buy that," or "I learned something new," and only in the last case is $f$ added to the KB ([≈43:31](../raw/transcripts/15-logic-i.md)–[≈45:49](../raw/transcripts/15-logic-i.md)):

| Knowledge base | Told | Relation | Response | KB afterwards |
| --- | --- | --- | --- | --- |
| Rain, Rain → Wet | it's wet ($\text{Wet}$) | entailment | I already knew that | unchanged |
| Rain, Wet | it's not raining ($\neg \text{Rain}$) | contradiction | I don't buy that | unchanged |
| Rain | it's not wet ($\neg \text{Wet}$) | contingency | I learned something new | Rain, not Wet |

Being accepted by Tell does not make a fact true. "You can tell the knowledge base 1 plus 3 is 5, and it'll say okay, I learned something new," provided nothing contradicts it; a knowledge base that starts out knowing nothing will accept a great deal, "and this is how fiction works, right? You just create a world" ([≈45:49](../raw/transcripts/15-logic-i.md), [≈46:34](../raw/transcripts/15-logic-i.md)).

In summary ([≈46:34](../raw/transcripts/15-logic-i.md), [≈47:20](../raw/transcripts/15-logic-i.md)): both operations compare $\mathcal{M}(\text{KB})$ with $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace)$. Entailment means Ask says yes and Tell says I already knew that; contradiction, no and I don't buy that; contingency, I don't know and I learned something new.

**Questions from the class** ([≈48:05](../raw/transcripts/15-logic-i.md)–[≈51:08](../raw/transcripts/15-logic-i.md)):

- Should a knowledge base be thought of as a database? "Absolutely": it stores facts.
- What if you Tell the KB something that contradicts it and the new fact is the correct one? This Tell assumes the KB is always correct. Otherwise "you have some work to do," and it is tricky, because you do not know which parts of the KB to remove and there may be several ways to remove enough to end the contradiction.
- Can a knowledge base become outdated? There is no notion of time in propositional logic, or in logic in general. Adding formulas is **monotonic**: you cannot remove anything, and the set of models only shrinks. With probabilities, by contrast, adding evidence can move a belief up or down. In practice you would detect contradictions with some external mechanism, surface the small set of conflicting formulas, and let the user decide what to remove. The lecturer compares this to failing tests in coding: the code might be wrong, or the tests outdated, or the comments; in general it is unknowable from inside, and you need external information to decide.
- Why are Ask and Tell separate if they do the same computation? It is "more of a user interface thing": sometimes you want to ask and sometimes to tell. The only difference in the code is the line that adds a contingent fact.

## Connection to Bayesian Networks

"You thought we were done with them," but Bayesian networks are closely connected to propositional logic ([≈51:08](../raw/transcripts/15-logic-i.md)–[≈56:31](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). A [Bayesian network](bayesian-networks.md) defines a joint distribution; the lecture uses a joint table over $\text{Rain}$ and $\text{Wet}$:

| Rain | Wet | $P(\text{Rain}, \text{Wet})$ |
| --- | --- | --- |
| 0 | 0 | 0.5 |
| 0 | 1 | 0.1 |
| 1 | 0 | 0.1 |
| 1 | 1 | 0.3 |

The translation ([≈51:54](../raw/transcripts/15-logic-i.md), [≈52:41](../raw/transcripts/15-logic-i.md)): **random variables** correspond to **propositional symbols**, and **assignments** (rows of the table) correspond to **models**, so a joint distribution is a probability distribution over models. In [probabilistic inference](probabilistic-inference.md), $P(\text{query} \mid \text{evidence})$, the **evidence** plays the role of the knowledge base and the **query** is what you Ask about. So $P(\text{Rain} \mid \text{Wet} = 1)$ corresponds to Tell[Wet] followed by Ask[Rain].

There are two differences ([≈53:26](../raw/transcripts/15-logic-i.md)–[≈56:31](../raw/transcripts/15-logic-i.md)). First, in Bayesian networks the query and evidence are simple assignments, "variable equals value"; in logic they are arbitrary formulas, such as $(\text{Rain} \wedge \text{Wet}) \vee (\text{Rain} \wedge \neg \text{Snow})$. If you know only that it is raining or snowing, logic can condition on $\text{Rain} \vee \text{Snow}$ directly; a Bayesian network would need a separate variable for it, so "you can do it, but it's not native." Second, Bayesian networks have probabilities and logic does not. The bridge is to give each model a probability and sum:

$$P(\text{KB}) = \sum_{w \in \mathcal{M}(\text{KB})} P(W = w), \qquad P(f \mid \text{KB}) = \frac{P(\text{KB} \cup \lbrace f \rbrace)}{P(\text{KB})}$$

where $P(\text{KB} \cup \lbrace f \rbrace)$ is the same sum over $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace)$. For instance, $P(\text{Rain}) = 0.1 + 0.3 = 0.4$, the sum over the two models in which it is raining ([≈54:59](../raw/transcripts/15-logic-i.md)). The lecture does not compute the conditional; from the same table, $P(\text{Rain} \mid \text{Wet} = 1) = 0.3 / (0.1 + 0.3) = 0.75$ (computed for this page from the lecture's table), where logic, told only Wet, would answer "I don't know."

$P(f \mid \text{KB})$ is a number between 0 and 1 that generalizes yes (1), no (0) and I don't know (anything in between): instead of "I don't know" you can say "90% chance" ([≈55:45](../raw/transcripts/15-logic-i.md)). So probability generalizes logic's zeros and ones, while logic's formulas allow more expressive queries. There is a whole line of work on probabilistic logical languages that the course does not cover ([≈56:31](../raw/transcripts/15-logic-i.md)).

## Satisfiability and Model Checking

So far, Ask and Tell enumerate every model, which is exponential in the number of symbols. To do better, reduce everything to one operation ([≈56:31](../raw/transcripts/15-logic-i.md)–[≈1:03:21](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)); the [satisfiability](satisfiability.md) topic page has the details.

A KB is **satisfiable** if and only if $\mathcal{M}(\text{KB}) \neq \emptyset$: at least one world is consistent with it ([≈57:17](../raw/transcripts/15-logic-i.md), [≈58:02](../raw/transcripts/15-logic-i.md)).

**Two calls decide all three relations** ([≈58:02](../raw/transcripts/15-logic-i.md)–[≈1:00:18](../raw/transcripts/15-logic-i.md)). Checking $\text{KB} \cup \lbrace f \rbrace$ alone is not enough: unsatisfiable means contradiction, but satisfiable could be entailment or contingency. So use the coin from above, $\text{KB} \models f$ if and only if KB contradicts $\neg f$:

1. Is $\text{KB} \cup \lbrace \neg f \rbrace$ satisfiable? If **no**, KB entails $f$.
2. Otherwise, is $\text{KB} \cup \lbrace f \rbrace$ satisfiable? If **no**, KB contradicts $f$; if **yes**, $f$ is contingent.

The first step is "basically like" a **proof by contradiction**: to show $f$ follows from KB, add $\neg f$ and show the result is contradictory. Two calls are necessary because each satisfiability check returns one bit and there are three outcomes ([≈1:00:18](../raw/transcripts/15-logic-i.md)). (In the transcript the lecturer glosses "not satisfiable" here as "there's no contradiction"; an unsatisfiable $\text{KB} \cup \lbrace \neg f \rbrace$ means KB *does* contradict $\neg f$.)

**Model checking** is the task of determining whether a KB is satisfiable; people "have been working on model checking for ages" ([≈1:00:18](../raw/transcripts/15-logic-i.md)). The lecture's code has been using **Z3**, "an SMT solver" (what that means is left for next time), throughout ([≈1:01:04](../raw/transcripts/15-logic-i.md)). Add the formulas Rain and Wet to a Z3 solver and call `check()`: it returns `sat`, and with it a model, `[Rain = True, Wet = True]`, "a sort of existence proof" that justifies the answer ([≈1:01:50](../raw/transcripts/15-logic-i.md)).

SAT solving is NP-complete in the worst case, but decades of research on heuristics mean modern solvers handle instances with thousands or hundreds of thousands of variables ([≈1:01:50](../raw/transcripts/15-logic-i.md), [≈1:02:35](../raw/transcripts/15-logic-i.md)); the slide names the DPLL algorithm and conflict-driven clause learning (CDCL). That is "the real way we actually handle these queries"; the enumeration in the lecture's Ask and Tell is "just a proof of concept for understanding" ([≈1:03:21](../raw/transcripts/15-logic-i.md)).

## Inference Rules: Modus Ponens and Forward Inference

The last five minutes give "a taste" of the third ingredient, with more next time ([≈1:03:21](../raw/transcripts/15-logic-i.md); [inference rules](inference-rules.md) topic page). With syntax and semantics defined, model checking as a black box already answers every query ([≈1:04:09](../raw/transcripts/15-logic-i.md)). But nobody solved $A + B = 10$ by model checking. Humans doing logical reasoning, in math or in natural language, rarely enumerate cases; they **derive** things by manipulating symbols.

**Modus ponens** ([≈1:04:57](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). It is raining; if it is raining, it is wet; therefore it is wet. Written as a rule, with the premises above the line and the conclusion below:

$$\frac{\text{Rain}, \quad \text{Rain} \rightarrow \text{Wet}}{\text{Wet}}$$

Rain and Wet can be replaced by any formulas, giving modus ponens in general, $p, \thinspace p \rightarrow q \vdash q$: if I have $p$ and $p \rightarrow q$, I derive $q$.

In general an **inference rule** takes premises $f_1, \ldots, f_n$ to a conclusion $g$, and rules **operate on the syntax, not the semantics** ([≈1:05:43](../raw/transcripts/15-logic-i.md)).

**Forward inference** applies the rules repeatedly ([≈1:05:43](../raw/transcripts/15-logic-i.md), [≈1:06:30](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). Given a set of rules and an initial KB, choose formulas $f_1, \ldots, f_n$ in the KB; if some rule $f_1, \ldots, f_n \vdash g$ matches, add $g$ to the KB; repeat until there are no more changes. (The slide as taught printed the loop condition as "While there are no changes to KB," and the lecturer read it that way; the course corrected it that evening to "Until there are no more changes to KB.") KB **derives** or **proves** $f$, written $\text{KB} \vdash f$, if $f$ eventually gets added.

**Example** ([≈1:06:30](../raw/transcripts/15-logic-i.md), [≈1:07:18](../raw/transcripts/15-logic-i.md)). Start from $\text{KB} = \lbrace \text{Rain}, \text{Rain} \rightarrow \text{Wet}, \text{Wet} \rightarrow \text{Slippery} \rbrace$. Modus ponens on Rain and Rain → Wet adds Wet. Modus ponens on the new Wet and Wet → Slippery adds Slippery. Nothing more can be added, so the process has converged. New facts combine with old facts to produce further facts. Some formulas cannot be derived: the slide names $\neg \text{Wet}$ and $\text{Rain} \rightarrow \text{Slippery}$. Some underivable formulas are false, "so it's good that you can't add them," and some are "a little bit more advanced" than these rules can reach ([≈1:07:18](../raw/transcripts/15-logic-i.md), [≈1:08:05](../raw/transcripts/15-logic-i.md)).

## Soundness and Completeness

Derivation ($\vdash$) is syntactic; entailment ($\models$) is semantic. How do the formulas you can derive, $\lbrace f : \text{KB} \vdash f \rbrace$, compare with the formulas that are entailed, $\lbrace f : \text{KB} \models f \rbrace$ ([≈1:08:05](../raw/transcripts/15-logic-i.md), [≈1:08:53](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md))? (Introducing the second set, the lecturer calls it "the syntax"; it is the semantic one.)

**The glass of water** ([≈1:08:53](../raw/transcripts/15-logic-i.md)–[≈1:11:11](../raw/transcripts/15-logic-i.md)). Think of the truth, the set of entailed formulas, as a glass: everything inside the glass is true. Deriving formulas with modus ponens is like pouring water into it. The slides show an empty glass beside "Truth," a partly filled glass beside "Soundness," and an overflowing one beside "Completeness."

- A set of rules is **sound** if everything it derives is entailed:
  $\lbrace f : \text{KB} \vdash f \rbrace \subseteq \lbrace f : \text{KB} \models f \rbrace$.
  "You might not fill the glass, but you don't go outside the glass." Any formula a sound derivation produces is true.
- A set of rules is **complete** if everything entailed can be derived:
  $\lbrace f : \text{KB} \models f \rbrace \subseteq \lbrace f : \text{KB} \vdash f \rbrace$.
  The glass is filled completely, though a complete but unsound procedure may also spill over and derive false things.

Ideally you want "the truth, the whole truth, and nothing but the truth": soundness is nothing but the truth, completeness the whole truth ([≈1:10:25](../raw/transcripts/15-logic-i.md), [≈1:11:11](../raw/transcripts/15-logic-i.md)).

**Checking a rule for soundness** means looking at models: intersect the models of the premises and check that the result lies inside the models of the conclusion ([≈1:11:11](../raw/transcripts/15-logic-i.md), [≈1:11:56](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). The slides draw each set as a $2 \times 2$ grid over (Rain, Wet).

- **Modus ponens is sound.** $\mathcal{M}(\text{Rain}) \cap \mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ is the single world $(1, 1)$, which is inside $\mathcal{M}(\text{Wet})$.
- **The reverse rule is unsound.** The rule "from Wet and Rain → Wet, infer Rain" fails: $\mathcal{M}(\text{Wet}) \cap \mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ is $(0, 1)$ and $(1, 1)$, and $(0, 1)$, wet but not raining, is not in $\mathcal{M}(\text{Rain})$. It amounts to "doing a reverse implication, and it's not valid logical reasoning... never do this in practice."

## Summary and What Comes Next

The summary ([≈1:11:56](../raw/transcripts/15-logic-i.md), [≈1:12:42](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)): a logic has syntax, semantics and inference rules. On the semantic side the lecture introduced models, the interpretation function (which induces the set of models of a formula), knowledge bases, and entailment, contradiction, contingency and satisfiability. Soundness and completeness connect syntax to semantics. None of these notions is specific to propositional logic; "these are general ideas in logic." Next time the course graduates to first-order logic, "where we'll actually do some really interesting things."

---

- **[Slides](../raw/slides/15-logic-i.md)**: the full content of `propositional_logic.py` as taught (pinned to commit 0116e139, "add logic 1", which landed as the lecture began), every traced model set and Ask/Tell answer, prose descriptions of the sixteen new figures, and the wording the course changed that evening. Rendered lecture: <https://stanford-cs221.github.io/autumn2025-lectures/?trace=propositional_logic> (this may now show a later revision, not the version taught). The slide links the Autumn 2023 CS221 logic overview.
- **[Transcript](../raw/transcripts/15-logic-i.md)**: edited and timestamped. Verbatim captions are in `original/`.
