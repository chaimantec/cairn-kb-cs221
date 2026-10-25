---
title: Satisfiability and Model Checking — Reducing Entailment, Contradiction and Contingency to One Operation
---

# Satisfiability

A knowledge base is **satisfiable** if at least one possible world is consistent with it. **Model checking** is the task of deciding whether it is. In [lecture 15](15-logic-i.md) satisfiability is the single operation that the three semantic relations ([entailment, contradiction and contingency](entailment.md)), and therefore the Ask and Tell operations, reduce to. That matters because the alternative, enumerating every model, is exponential, while satisfiability is a problem decades of solver research have made fast in practice ([≈56:31](../raw/transcripts/15-logic-i.md)–[≈1:03:21](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)).

Notation, as in the lecture: KB is a knowledge base (a set of formulas), $f$ a formula, and $\mathcal{M}(\text{KB})$ the set of models (possible worlds) satisfying every formula in KB; $\text{KB} \models f$ reads "KB entails $f$." See [propositional logic](propositional-logic.md) for these terms.

## The Problem with Enumeration

The lecture's first implementations of Ask and Tell compute $\mathcal{M}(\text{KB})$ and $\mathcal{M}(\text{KB} \cup \lbrace f \rbrace)$ by listing every model and testing each one ([slides](../raw/slides/15-logic-i.md)). With $n$ propositional symbols there are $2^n$ models, so "in general this is going to be exponentially large" ([≈57:17](../raw/transcripts/15-logic-i.md)). The fix is to stop asking for whole sets of models and ask a yes/no question that specialized tools answer well.

## Definition

$$\text{KB is satisfiable} \iff \mathcal{M}(\text{KB}) \neq \emptyset$$

There is "at least one world which is consistent with the knowledge base" ([≈57:17](../raw/transcripts/15-logic-i.md), [≈58:02](../raw/transcripts/15-logic-i.md)). In these terms, KB contradicts $f$ exactly when $\text{KB} \cup \lbrace f \rbrace$ is unsatisfiable.

## Two Calls Decide All Three Relations

A first instinct is to check $\text{KB} \cup \lbrace f \rbrace$. If it is unsatisfiable, the answer is contradiction. But if it is satisfiable, it could be entailment or contingency, so a second test is needed ([≈58:02](../raw/transcripts/15-logic-i.md), [≈58:47](../raw/transcripts/15-logic-i.md)). The second test uses the link between the relations: KB entails $f$ if and only if KB contradicts $\neg f$ ([entailment](entailment.md#entailment-and-contradiction-are-linked)). The lecture's decision tree ([≈59:33](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)):

1. Is $\text{KB} \cup \lbrace \neg f \rbrace$ satisfiable? If **no**, return **entailment**.
2. Otherwise, is $\text{KB} \cup \lbrace f \rbrace$ satisfiable? If **no**, return **contradiction**; if **yes**, return **contingency**.

Two calls are necessary: "satisfiability returns yes or no, which is only one bit of information, but we have three outcomes here" ([≈1:00:18](../raw/transcripts/15-logic-i.md)).

The first step is "basically like" a **proof by contradiction**: to show that $f$ follows from KB, add $\neg f$ and show that the result is contradictory ([≈58:47](../raw/transcripts/15-logic-i.md), [≈59:33](../raw/transcripts/15-logic-i.md)). (The edited transcript records the lecturer glossing "not satisfiable" at this point as "there's no contradiction"; what an unsatisfiable $\text{KB} \cup \lbrace \neg f \rbrace$ means is that KB contradicts $\neg f$.)

Mapped onto the knowledge-base operations, step 1 failing means Ask answers yes and Tell says "I already knew that"; step 2 failing means no and "I don't buy that"; both satisfiable means "I don't know" and "I learned something new" ([entailment](entailment.md#ask-and-tell)).

## Model Checking with Z3

**Model checking** takes a KB as input and outputs whether it is satisfiable ([≈1:00:18](../raw/transcripts/15-logic-i.md), [slides](../raw/slides/15-logic-i.md)). The lecture's code has used the **Z3** package throughout to build formulas; Z3 is "an SMT solver" (the lecturer defers what that means to the next lecture, and [lecture 16](16-logic-ii.md) does not come back to it) with tools for logical inference, including checking satisfiability ([≈1:01:04](../raw/transcripts/15-logic-i.md)):

```python
kb = [Rain, Wet]
solver = Solver()
for f in kb:
    solver.add(f)
result = solver.check()
if result == sat:
    w = solver.model()
```

In the trace, `result` is `sat` and `w` is `[Rain = True, Wet = True]` ([slides](../raw/slides/15-logic-i.md)). When a satisfiability checker answers yes, it also returns a model, "a sort of existence proof": here Rain is true and Wet is true, which justifies why the knowledge base is satisfiable ([≈1:01:50](../raw/transcripts/15-logic-i.md)).

Lecture 16 uses the same two satisfiability calls with Z3 on a first-order knowledge base, to answer "Is it snowing?" about Alice, Phoenix and the weather; see [lecture 16](16-logic-ii.md#a-motivating-example) ([slides](../raw/slides/16-logic-ii.md)).

## SAT Solvers

Satisfiability checking is NP-complete in the worst case. That "doesn't stop people from building heuristics and various tools to actually solve SAT instances with thousands or hundreds of thousands of variables" ([≈1:01:50](../raw/transcripts/15-logic-i.md), [≈1:02:35](../raw/transcripts/15-logic-i.md)). The slide names two families under the hood: the **DPLL** algorithm (exhaustive search) and **conflict-driven clause learning (CDCL)** ([slides](../raw/slides/15-logic-i.md)); their workings are outside the scope of the class. So reducing entailment, contradiction and contingency to two satisfiability calls and handing them to a fast model checker is "the real way we actually handle these queries" ([≈1:03:21](../raw/transcripts/15-logic-i.md)).

## Model Checking versus Inference Rules

With syntax and semantics defined, model checking as a black box already answers every query ([≈1:04:09](../raw/transcripts/15-logic-i.md)). But it works by finding models, possible worlds, and that is not how people reason: nobody solves $A + B = 10$, $A - B = 4$ by enumerating cases. The alternative, manipulating formulas directly, is the subject of [inference rules](inference-rules.md).

## Related Pages

- [Lecture 15](15-logic-i.md) — the lecture that introduces satisfiability and model checking
- [Entailment, contradiction and contingency](entailment.md) — the relations being decided, and Ask and Tell
- [Propositional logic](propositional-logic.md) — models and knowledge bases
- [Inference rules](inference-rules.md) — deriving conclusions syntactically instead of checking models
- [First-order logic](first-order-logic.md) and [propositionalization](propositionalization.md) — satisfiability for a first-order knowledge base
