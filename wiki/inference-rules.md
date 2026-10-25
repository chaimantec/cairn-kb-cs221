---
title: Inference Rules — Modus Ponens, Forward Inference, Soundness and Completeness
---

# Inference Rules

**Inference rules** are the third ingredient of a logic, after syntax and semantics: rules for deriving new formulas from existing ones by manipulating symbols, the way a person does algebra, rather than by checking possible worlds. [Lecture 15](15-logic-i.md) introduces them in its last minutes, "just to give you a taste," with **modus ponens** as the example, **forward inference** as the procedure that applies rules repeatedly, and **soundness** and **completeness** as the two properties that tie what a set of rules can derive to what is actually true ([≈1:03:21](../raw/transcripts/15-logic-i.md)–[≈1:12:42](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). The lecturer says he will "talk more about this next time."

Notation, as in the lecture: KB is a knowledge base (a set of formulas) and $f$, $g$, $f_1, \ldots, f_n$ are formulas. $\text{KB} \vdash f$, with one horizontal line, reads "KB derives (or proves) $f$" and is **syntactic**. $\text{KB} \models f$, with two, reads "KB entails $f$" and is **semantic** ([entailment](entailment.md)). $\mathcal{M}(f)$ is the set of models (possible worlds) in which $f$ is true; see [propositional logic](propositional-logic.md).

## Why Rules, If Model Checking Works?

Once syntax and semantics are defined, [model checking](satisfiability.md) can answer any query as a black box ([≈1:04:09](../raw/transcripts/15-logic-i.md)). But think back to the problem that opens the lecture, $A + B = 10$ and $A - B = 4$. Nobody solved it by model checking; "we just manipulated symbols." That is normally what a human does when reasoning logically, in math or in natural language: "you hardly do any model checking, which would be like enumerating cases. You often derive things" ([≈1:04:09](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)).

## Modus Ponens

It is raining; if it is raining, it is wet; therefore it is wet ([≈1:04:57](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). Written as a rule, with the **premises** above the line and the **conclusion** below:

$$\frac{\text{Rain}, \quad \text{Rain} \rightarrow \text{Wet}}{\text{Wet}}$$

Rain and Wet can be replaced by any propositional symbols, or any formulas, giving **modus ponens**: "if I have $p$ and I have $p \rightarrow q$, then I derive $q$," written $p, \thinspace p \rightarrow q \vdash q$.

In general an inference rule has premises $f_1, \ldots, f_n$ and a conclusion $g$. Rules **operate on the syntax, not the semantics**: they match the shapes of formulas and produce new formulas, without looking at what those formulas mean ([≈1:05:43](../raw/transcripts/15-logic-i.md)).

## Forward Inference

Given a set of rules and an initial knowledge base, **forward inference** applies the rules until nothing changes ([≈1:05:43](../raw/transcripts/15-logic-i.md), [≈1:06:30](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)):

- choose a set of formulas $f_1, \ldots, f_n$ in the KB;
- if a rule $f_1, \ldots, f_n \vdash g$ matches, add the conclusion $g$ to the KB;
- repeat until there are no more changes.

KB **derives** or **proves** $f$, written $\text{KB} \vdash f$, if $f$ eventually gets added to the KB this way. (The slide as taught printed the loop condition as "While there are no changes to KB," and the lecturer read it that way; the course corrected it that evening to "Until there are no more changes to KB.")

**Example** ([≈1:06:30](../raw/transcripts/15-logic-i.md), [≈1:07:18](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). Start from

$$\text{KB} = \lbrace \text{Rain}, \text{Rain} \rightarrow \text{Wet}, \text{Wet} \rightarrow \text{Slippery} \rbrace$$

Modus ponens on Rain and Rain → Wet adds Wet. Modus ponens on Wet and Wet → Slippery then adds Slippery. No rule can add anything more, so the procedure has converged. New facts combine with old ones to produce further facts.

Some formulas cannot be derived from this KB with modus ponens; the slide names $\neg \text{Wet}$ and $\text{Rain} \rightarrow \text{Slippery}$. Some underivable formulas are false, "so it's good that you can't add them," while others are "a little bit more advanced," beyond what the rules reach ([≈1:07:18](../raw/transcripts/15-logic-i.md), [≈1:08:05](../raw/transcripts/15-logic-i.md)).

## Soundness and Completeness

Derivation is syntactic: "I have a set of facts in the knowledge base. These are just symbols, and I have a set of rules that operate on them and can generate more formulas" ([≈1:08:05](../raw/transcripts/15-logic-i.md)). Entailment is semantic: $\mathcal{M}(f)$ is a superset of $\mathcal{M}(\text{KB})$. The question is how the two sets compare ([≈1:08:53](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)):

$$\lbrace f : \text{KB} \vdash f \rbrace \quad \text{versus} \quad \lbrace f : \text{KB} \models f \rbrace$$

the formulas you can derive versus the formulas that are true given the KB. (Introducing the second set, the lecturer calls it "the syntax"; it is the semantic one, as the slide labels it.)

**The glass of water** ([≈1:08:53](../raw/transcripts/15-logic-i.md)–[≈1:11:11](../raw/transcripts/15-logic-i.md)). Think of the truth, the set of entailed formulas, as a glass: everything inside it is true. Deriving formulas is pouring water into the glass. The slides picture an empty glass for truth, a partly filled one for soundness and an overflowing one for completeness.

- **Soundness**: $\lbrace f : \text{KB} \vdash f \rbrace \subseteq \lbrace f : \text{KB} \models f \rbrace$. Everything you derive is entailed. "You might not fill the glass, but you don't go outside the glass." Whenever a sound derivation produces a formula, that formula is true.
- **Completeness**: $\lbrace f : \text{KB} \models f \rbrace \subseteq \lbrace f : \text{KB} \vdash f \rbrace$. Everything entailed can be derived: the glass is filled completely. Completeness alone allows deriving things outside the glass, false things, in which case the rules are complete but not sound.

What you want is "the truth, the whole truth, and nothing but the truth": soundness is **nothing but the truth**, completeness is **the whole truth** ([≈1:10:25](../raw/transcripts/15-logic-i.md), [≈1:11:11](../raw/transcripts/15-logic-i.md)).

## Checking Whether a Rule Is Sound

To check a rule, look at models: intersect the models of the premises and ask whether the result is a subset of the models of the conclusion ([≈1:11:11](../raw/transcripts/15-logic-i.md), [≈1:11:56](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). Over the symbols Rain and Wet, write each model as a (Rain, Wet) pair; the slides draw each set as a $2 \times 2$ grid.

**Modus ponens is sound.**

$$\mathcal{M}(\text{Rain}) \cap \mathcal{M}(\text{Rain} \rightarrow \text{Wet}) \subseteq \mathcal{M}(\text{Wet})$$

$\mathcal{M}(\text{Rain})$ is $(1, 0)$, $(1, 1)$ and $\mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ is $(0, 0)$, $(0, 1)$, $(1, 1)$. Their intersection is the single world $(1, 1)$, which is in $\mathcal{M}(\text{Wet})$. Sound.

**The reverse rule is unsound.** Consider "from Wet and Rain → Wet, infer Rain." $\mathcal{M}(\text{Wet}) \cap \mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ is $(0, 1)$ and $(1, 1)$, and $(0, 1)$, wet but not raining, is not in $\mathcal{M}(\text{Rain})$. Unsound. This is "doing a reverse implication, and it's not valid logical reasoning," so "never do this in practice" ([≈1:11:56](../raw/transcripts/15-logic-i.md)).

## Related Pages

- [Lecture 15](15-logic-i.md) — the lecture that introduces inference rules, soundness and completeness
- [Entailment, contradiction and contingency](entailment.md) — the semantic relation that derivation is measured against
- [Satisfiability](satisfiability.md) — model checking, the alternative to deriving conclusions
- [Propositional logic](propositional-logic.md) — the syntax rules operate on, and the models used to check them
