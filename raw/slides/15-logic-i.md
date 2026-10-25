---
title: Lecture 15 — Logic I (Slides)
lecture: 15
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_commit: 0116e139 (2025-11-10, "add logic 1")
source_files:
  - https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/0116e139/propositional_logic.py
rendered:
  - https://stanford-cs221.github.io/autumn2025-lectures/?trace=propositional_logic
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. Lecture 15 was delivered as
  one program, `propositional_logic.py`, which builds formulas with the Z3 library's
  Python API (`Bool`, `Not`, `And`, `Or`, `Implies`, `==` for the biconditional) and
  imports `ProbTable` from lecture 12's `bayes.py`. There are no slide numbers;
  sections correspond to function calls in the source. The file is transcribed here
  AS OF COMMIT 0116e139 ("add logic 1"), which landed at 1:30pm PST on Mon Nov 10
  2025, as the lecture began, and whose README diff adds the heading "Mon Nov 10:
  learning (lecture 15)" [sic] over a link to the `propositional_logic` trace. The
  next commit to the file (af5f29c1, "fix typos", 10:51pm PST the same evening)
  reworded several lines and reordered one example's knowledge base, and a later one
  (79ab1572, three days after) fixed two more typos; both are listed in the "Changes
  after the lecture" section at the end. The file was later rewritten in 2026 for a
  later offering, so the repo's `main` branch and the live rendered UI no longer show
  what was taught; the pinned URL above does. All demo outputs were read on
  2026-10-25 from the execution trace committed at the same commit
  (`var/traces/propositional_logic.json`), whose embedded source is byte-identical
  to the pinned file. Seventeen images are shown: `perceive-reason-act-learn.png`,
  unchanged since lecture 1 and described there, and sixteen new images added in
  the same commit, described in the "Figures" section at the end of this file
  (`syntax-semantics-rules.png` is shown twice).
---

# Lecture 15 Slides — Logic I

Lecture 15 opens the logic unit, the last technical topic of the course. It motivates logic as a **language** for representing knowledge and reasoning with it, defined by three ingredients: **syntax** (which formulas are valid), **semantics** (what they mean) and **inference rules** (how to derive new formulas from old ones). It then works through **propositional logic**. The syntax is propositional symbols closed under $\neg$, $\wedge$, $\vee$, $\rightarrow$ and $\leftrightarrow$. The semantics is built from **models** (assignments of truth values to the symbols), the **interpretation function** $\mathcal{I}(f, w)$, the set of models $\mathcal{M}(f)$ of a formula, and a **knowledge base** (KB), a set of formulas whose models are the intersection of theirs. Adding a formula $f$ to a KB relates them in one of three ways — **entailment**, **contradiction** or **contingency** — which give the answers to the operations **Ask** and **Tell**. The lecture connects this to probabilistic inference in Bayesian networks, reduces all three relations to **satisfiability** (checked with the Z3 solver), and then turns to inference rules: **modus ponens**, **forward inference**, and the **soundness** and **completeness** of a set of rules.

| Part | Function(s) | Content |
| ---- | ----------- | ------- |
| 1. Title | `main` | Lecture title |
| 2. Motivation | `motivation` | Reasoning; the A + B = 10 problem; historical note; logic as a language |
| 3. Why not natural language? | `why_not_natural_language` | Dime/nickel/penny; penny/nothing/world peace |
| 4. Types of languages | `types_of_languages` | Natural, programming and logical languages |
| 5. Ingredients of a logic | `ingredients_of_a_logic` | Syntax, semantics, inference rules |
| 6. Syntax versus semantics | `syntax_versus_semantics` | `2 + 3` vs `3 + 2`; `3 / 2` in Python 2.7 vs 3; motivation summary |
| 7. Propositional logic | `introduce_propositional_logic` | Overview |
| 8. Syntax | `propositional_logic_syntax` | Propositional symbols; connectives; examples and non-examples |
| 9. Semantics | `propositional_logic_semantics` | Roadmap of the semantic concepts |
| 10. Model | `define_model` | Assignments of truth values; $2^3 = 8$ models over $A$, $B$, $C$ |
| 11. Interpretation function | `define_interpretation_function`, `I` | $\mathcal{I}(f, w)$, computed recursively |
| 12. Models of a formula | `define_models`, `get_models` | $\mathcal{M}(f)$; Rain ∨ Wet and Rain ∧ Wet |
| 13. Knowledge base | `define_knowledge_base`, `intersect`, `to_formula` | $\mathcal{M}(\text{KB})$ as an intersection, and as the models of a conjunction |
| 14. Entailment, contradiction, contingency | `entailment_contradiction_contingency` | How far adding $f$ shrinks $\mathcal{M}(\text{KB})$ |
| 15. Ask and Tell | `ask_tell` | Yes / No / I don't know; I already knew that / I don't buy that / I learned something new |
| 16. Connection to Bayesian networks | `connection_to_bayesian_networks` | Terminology; $P(f \mid \text{KB})$ |
| 17. Satisfiability | `satisfiability` | Reducing the three relations to two satisfiability checks; model checking with Z3 |
| 18. Inference rules | `propositional_logic_inference_rules` | Back to manipulating symbols |
| 19. Forward inference | `forward_inference` | Modus ponens; general inference rules; forward inference; derivation |
| 20. Soundness and completeness | `soundness_completeness` | $\lbrace f : \text{KB} \vdash f \rbrace$ vs $\lbrace f : \text{KB} \models f \rbrace$; water glasses; is a rule sound? |
| 21. Summary | `main` | Four-line summary; next time |

Notation follows the lecture's. Propositional symbols are capitalized names (`P`, `Q`, `Rain`, `Wet`, `A`, `B`, `C`); $f$, $g$ and $h$ are formulas; $w$ is a model; $\mathcal{I}$ is the interpretation function, written ℐ in the printed text; $\mathcal{M}(f)$ and $\mathcal{M}(\text{KB})$ are sets of models, written M(f) and M(KB) in the printed text and $\mathcal{M}$ in the figures. $\text{KB} \models f$ ("KB entails $f$", printed ⊧) is semantic; $\text{KB} \vdash f$ ("KB derives / proves $f$", printed ⊢) is syntactic. In the code, a formula is a Z3 expression and a model is a Python dict from symbols to `True`/`False`; the traced models below are written as (Rain, Wet) pairs of 0s and 1s, so $(1, 0)$ means Rain is true and Wet is false.

---

## Section: Title (`main`)

**Lecture 15: Propositional logic**

Order of the lecture: `motivation` (which runs `why_not_natural_language`, `types_of_languages`, `ingredients_of_a_logic`, `syntax_versus_semantics`) → `introduce_propositional_logic` (which runs `propositional_logic_syntax`, `propositional_logic_semantics`, `propositional_logic_inference_rules`) → summary.

---

## Section: Motivation (`motivation`)

Figure: `perceive-reason-act-learn.png` (from lecture 1 — an "Agent" box with "perceive" entering on the left, "act" leaving on the right, a "reason" loop over the top and a "learn" loop underneath; described in raw/slides/05-search-i.md and raw/slides/12-bayesian-networks-i.md).

Focus: reasoning

Last time: probabilistic reasoning (Bayesian networks)

This time: logical reasoning (propositional logic, first-order logic)

Link: [Autumn 2023 lecture] — <https://stanford-cs221.github.io/autumn2023/modules/module.html#include=logic%2Foverview.js&mode=print6pp>

Problem: If A + B = 10 and A - B = 4, what is A?

How did you solve this?

Certainly not by searching through all possible values of A and B...

Instead you use algebra.

This is an example of symbolic or logical reasoning!

Historical note:

- Logic was the dominant paradigm in AI before 1990s
- Problem 1: deterministic, didn't handle **uncertainty** (probability addresses this)
- Problem 2: rule-based, didn't leverage **data** (machine learning addresses this)
- Then why are we studying it?
- Strength: provides **expressivity** in a compact way (we'll see what this means)

The way to think about **logic** is a language.

Two goals of a logical language:

1. Represent knowledge about the world
2. Reason with that knowledge

(The function then runs the four sections below and closes with the summary printed at the end of "Syntax versus semantics".)

---

## Section: Why not natural language? (`why_not_natural_language`)

Why not use natural language?

Example 1:

- A dime is better than a nickel.
- A nickel is better than a penny.
- Therefore, a dime is better than a penny.

Example 2:

- A penny is better than nothing.
- Nothing is better than world peace.
- Therefore, a penny is better than world peace??

Natural language is slippery...

---

## Section: Types of languages (`types_of_languages`)

Natural languages (informal):

- English: Two divides even numbers.
- German: Zwei dividieren geraden zahlen.

Programming languages (formal):

- Python: `def even(x): return x % 2 == 0`
- C++: `bool even(int x) { return x % 2 == 0; }`

Logical languages (formal):

- First-order-logic: `∀x. Even(x) → Divides(x, 2)`
- Description logic: `Even ⊑ ∀ Divides.2`

---

## Section: Ingredients of a logic (`ingredients_of_a_logic`)

Figure: `syntax-semantics-rules.png` — "Syntax" (red) on the left, a stack of eight formulas (pink bars) above a dark-red downward arrow labelled "Inference rules" (green) that ends at one more formula; "Semantics" (blue) on the right, a box holding nested ellipses labelled "models". An arrow runs from the top formula to the inner (blue) ellipse, and one from the derived formula to the outer (green) ellipse. Described in full under Figures.

1. **Syntax**: defines a set of valid formulas
2. **Semantics**: defines the meaning of these formulas
3. **Inference rules**: how to derive new valid formulas from existing ones

---

## Section: Syntax versus semantics (`syntax_versus_semantics`)

Syntax: what are valid expressions in the language?

Semantics: what is the meaning of these expressions?

Different syntax, same semantics:

- Syntax: `2 + 3`, semantics: 5
- Syntax: `3 + 2`, semantics: 5

Same syntax, different semantics:

- Syntax: `3 / 2`, semantics: 1 (Python 2.7)
- Syntax: `3 / 2`, semantics: 1.5 (Python 3)

Then, back in `motivation`:

Summary:

- Logic enables expressive representation/reasoning
- Think a logic as a language [sic]
- Defined by syntax, semantics, and inference rules

Next: propositional logic

Later: first-order logic

---

## Section: Propositional logic (`introduce_propositional_logic`)

We now present propositional logic, the simplest form of logic.

We will also introduce all the general concepts (e.g., entailment, soundness).

Figure: `syntax-semantics-rules.png` (shown again; see "Ingredients of a logic").

(The function then runs `propositional_logic_syntax`, `propositional_logic_semantics` and `propositional_logic_inference_rules`.)

---

## Section: Syntax (`propositional_logic_syntax`)

Syntax: what are valid formulas?

Propositional symbols (atomic formulas):

```python
# Base case
P = Bool("P")        # P
Q = Bool("Q")        # Q
Rain = Bool("Rain")  # Rain
Wet = Bool("Wet")    # Wet
```

Logical connectives: ∧, ∨, ¬, →, ↔

Let f and g be any propositional formulas.

```python
# Inductive case
f = Rain
g = Wet
```

Then the following are also propositional formulas:

```python
h1 = Not(f)          # ¬f
h2 = And(f, g)       # f ∧ g
h3 = Or(f, g)        # f ∨ g
h4 = Implies(f, g)   # f → g
h5 = f == g          # f ↔ g
```

Nothing else is a formula.

Examples of formulas:

```python
f = P                          # P
f = Not(P)                     # ¬P
f = Or(Not(P), Q)              # ¬P ∨ Q
f = Implies(P, Or(Q, Not(P)))  # P → (Q ∨ ¬P)
```

Non-formulas (in propositional logic):

- P ¬Q
- P + Q
- P(A) ∨ Q(B)

Now we know what are the set of valid formulas.

But what do all these formulas mean?

---

## Section: Semantics (`propositional_logic_semantics`)

Formulas by themselves are just symbols (syntax).

They don't have any meaning yet (semantics).

There are a lot of concepts to introduce.

```python
define_model()                           # State of world
define_interpretation_function()         # Connects formulas (syntax) to models (semantics)
define_models()                          # Formula -> models (possible worlds)
define_knowledge_base()                  # Set of formulas
entailment_contradiction_contingency()   # Relationships between knowledge base and new formula
ask_tell()                               # Operate on a knowledge base
connection_to_bayesian_networks()        # Connection to Bayesian networks
satisfiability()                         # Reduce Ask/Tell to satisfiability
```

---

## Section: Model (`define_model`)

A model (in logic) represents a state of the world.

Definition: a **model** w in propositional logic is

...an assignment of truth values to the propositional symbols.

**Example**:

Three propositional symbols:

```python
A = Bool("A")  # A
B = Bool("B")  # B
C = Bool("C")  # C
```

2^3 = 8 possible models:

```python
models = [
    {A: False, B: False, C: False},
    {A: False, B: False, C: True},
    {A: False, B: True,  C: False},
    {A: False, B: True,  C: True},
    {A: True,  B: False, C: False},
    {A: True,  B: False, C: True},
    {A: True,  B: True,  C: False},
    {A: True,  B: True,  C: True},
]
```

---

## Section: Interpretation function (`define_interpretation_function`, `I`)

The interpretation function connects a formula (syntax) and a model (semantics).

Definition: **interpretation function** ℐ

...maps a formula f and a model w either:

- true (1): w satisfies f, or
- false (0): w does not satisfy f

Figure: `logic-fw.png` — a formula $f$ (pink bar) on the left joined by a dashed dark-red line to a single blue point $w$ inside a box on the right.

**Example**:

```python
f = And(Not(A), B) == C  # (¬A ∧ B) ↔ C
w = {A: True, B: True, C: False}
```

Figure: `logic-i-tree.png` — the evaluation tree for this example: $\mathcal{I}((\neg A \wedge B) \leftrightarrow C, w) = 1$ at the root, with children $\mathcal{I}(\neg A \wedge B, w) = 0$ and $\mathcal{I}(C, w) = 0$; under the first, $\mathcal{I}(\neg A, w) = 0$ (whose child is $\mathcal{I}(A, w) = 1$) and $\mathcal{I}(B, w) = 1$.

```python
result = I(f, w)  # @inspect result
```

The recursive interpretation function:

```python
def I(f: Formula, w: Model) -> bool:  # @inspect f w
    """Interpretation function: given a formula f and a model w, compute whether w satisfies f."""
    if f.num_args() == 0:
        result = w[f]  # @inspect result

    elif f.num_args() == 1:
        g = f.arg(0)
        if is_not(f):
            result = not I(g, w)  # @inspect result
        else:
            raise ValueError(f"Unsupported formula: {f}")

    elif f.num_args() == 2:
        g, h = f.arg(0), f.arg(1)
        if is_and(f):  # f = And(g, h)
            result = I(g, w) and I(h, w)  # @inspect result
        elif is_or(f):  # f = Or(g, h)
            result = I(g, w) or I(h, w)  # @inspect result
        elif is_implies(f):  # f = Implies(g, h)
            result = not I(g, w) or I(h, w)  # @inspect result
        elif is_eq(f):  # f = (g == h)
            result = I(g, w) == I(h, w)  # @inspect result
        else:
            raise ValueError(f"Unsupported formula: {f}")

    return result
```

Traced: $\mathcal{I}(A, w)$ = true, so $\mathcal{I}(\neg A, w)$ = false; the conjunction $\neg A \wedge B$ is then false, and because Python's `and` stops at the first false operand, the trace never evaluates $B$ (the figure shows $\mathcal{I}(B, w) = 1$ for completeness). $\mathcal{I}(C, w)$ = false, and the biconditional compares false with false, so `result` = **true**, matching the root of the figure.

---

## Section: Models of a formula (`define_models`, `get_models`)

Recall the interpretation function ℐ(f, w) maps a formula f and a model w to a truth value (true/false).

Definition: the **models** of a formula f, denoted M(f), are the models w such that ℐ(f, w) = true.

Figure: `logic-models.png` — a formula $f$ (pink bar) on the left with a dark-red arrow into a box on the right, pointing at a light-blue ellipse labelled $\mathcal{M}(f)$.

**Example**:

```python
Rain = Bool("Rain")
Wet = Bool("Wet")
f1 = Or(Rain, Wet)  # @inspect f1
models1 = get_models(f1, [Rain, Wet])  # @inspect models1
f2 = And(Rain, Wet)  # @inspect f2
models2 = get_models(f2, [Rain, Wet])  # @inspect models2 @stepover
```

```python
def get_models(f: Formula, symbols: list[PropositionalSymbol]) -> list[Model]:
    """Return all the models that satisfy the formula f."""
    models = []  # @inspect models
    for values in itertools.product([False, True], repeat=len(symbols)):
        # For every model (assignment of truth values to the symbols)...
        w = dict(zip(symbols, values))  # @inspect w
        if I(f, w):  # Does f satisfy the model w? @stepover
            models.append(w)  # @inspect models
    return models
```

Traced: `get_models` visits the four models in the order (Rain, Wet) = $(0, 0)$, $(0, 1)$, $(1, 0)$, $(1, 1)$. For $f_1$ = Rain ∨ Wet it keeps three: `models1` = $(0, 1)$, $(1, 0)$, $(1, 1)$. For $f_2$ = Rain ∧ Wet it keeps one: `models2` = $(1, 1)$.

A (small) formula **compactly** represents a (potentially large) set of models.

---

## Section: Knowledge base (`define_knowledge_base`, `intersect`, `to_formula`)

Definition: a **knowledge base** (KB) is a set of formulas.

Think of the KB as a set of facts that you add to over time.

Semantics: **models M(KB)** are the models that satisfy every formula in KB.

These are the possible worlds we could be in given our knowledge KB.

```python
Rain = Bool("Rain")
Wet = Bool("Wet")
symbols = [Rain, Wet]

kb = [Rain, Implies(Rain, Wet)]
```

Models of a KB is the intersection of the models of its formulas:

```python
models_rain = get_models(Rain, [Rain, Wet])  # M(Rain) @inspect models_rain @stepover
models_rain_implies_wet = get_models(Implies(Rain, Wet), [Rain, Wet])  # M(Rain → Wet) @inspect models_rain_implies_wet @stepover
models_kb = intersect(models_rain, models_rain_implies_wet)  # M({Rain, Rain → Wet}) @inspect models_kb @stepover
```

Traced: $\mathcal{M}(\text{Rain})$ = $(1, 0)$, $(1, 1)$; $\mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ = $(0, 0)$, $(0, 1)$, $(1, 1)$; their intersection `models_kb` = $(1, 1)$ only.

Semantically, a KB is equivalent to the conjunction of its formulas:

```python
f = to_formula(kb)  # Rain ∧ (Rain → Wet) @inspect f @stepover
f_models = get_models(f, symbols)  # M(Rain ∧ (Rain → Wet)) @inspect f_models @stepover
```

Traced: `f` = `And(Rain, Implies(Rain, Wet))`, and `f_models` = $(1, 1)$, the same single model as the intersection.

```python
def intersect(list1: list[Model], list2: list[Model]) -> list[Model]:
    """Return the intersection of two lists of models."""
    return [m for m in list1 if m in list2]


def to_formula(kb: list[Formula]) -> Formula:
    """Convert a list of formulas to a single formula."""
    f = kb[0]  # @inspect f
    for g in kb[1:]:  # @inspect g
        f = And(f, g)  # @inspect f
    return f
```

---

## Section: Entailment, contradiction, contingency (`entailment_contradiction_contingency`)

Adding more formulas to the KB: KB ⊆ (KB ∪ {f})...

...shrinks the set of models: M(KB ∪ {f}) ⊆ M(KB).

Intuition: as we gain knowledge, we narrow down the possible worlds we might be in.

```python
Rain = Bool("Rain")
Wet = Bool("Wet")
symbols = [Rain, Wet]

# Helper function to get the models of a KB
def M(kb: list[Formula]) -> list[Model]:
    return get_models(to_formula(kb), symbols)
```

How much does M(KB) shrink?

**Entailment**:

- Definition: KB entails f (KB ⊧ f) iff M(KB ∪ {f}) = M(KB).

Figure: `logic-entailment.png` — a green ellipse $\mathcal{M}(\text{KB})$ lying entirely inside a larger pink ellipse labelled $\mathcal{M}(f)$.

- Intuition: f adds no more information.

```python
def entails(kb: list[Formula], f: Formula) -> bool:  # @inspect kb f
    old_models = M(kb)  # M(KB) @inspect old_models @stepover
    new_models = M(kb + [f])  # M(KB ∪ {f}) @inspect new_models @stepover
    return new_models == old_models
assert entails([Rain, Implies(Rain, Wet)], Rain)
```

Traced: for KB = {Rain, Rain → Wet} and $f$ = Rain, `old_models` = `new_models` = $(1, 1)$.

**Contradiction**:

- Definition: KB contradicts f iff M(KB ∪ {f}) = ∅.
- Intuition: f is not compatible with KB.

Figure: `logic-contradiction.png` — a green ellipse $\mathcal{M}(\text{KB})$ on the left and a separate pink ellipse $\mathcal{M}(f)$ on the right, not touching.

```python
def contradicts(kb: list[Formula], f: Formula) -> bool:  # @inspect kb f
    old_models = M(kb)  # M(KB) @inspect old_models @stepover
    new_models = M(kb + [f])  # M(KB ∪ {f}) @inspect new_models @stepover
    return new_models == []
assert contradicts([Rain, Wet], Not(Wet))
```

Traced: for KB = {Rain, Wet} and $f$ = ¬Wet, `old_models` = $(1, 1)$ and `new_models` is empty.

**Contingency**:

- Definition: f is contingent with respect to KB iff ∅ ≠ M(KB ∪ {f}) ≠ M(KB).
- Intuition: shrinks the set of models but not completely.

Figure: `logic-contingency.png` — a green ellipse $\mathcal{M}(\text{KB})$ and a pink ellipse $\mathcal{M}(f)$ that partly overlap; the overlap is shaded olive.

```python
def contingent(kb: list[Formula], f: Formula) -> bool:  # @inspect kb f
    old_models = M(kb)  # M(KB) @inspect old_models @stepover
    new_models = M(kb + [f])  # M(KB ∪ {f}) @inspect new_models @stepover
    return new_models != [] and new_models != old_models
assert contingent([Rain], Wet)
```

Traced: for KB = {Rain} and $f$ = Wet, `old_models` = $(1, 0)$, $(1, 1)$ and `new_models` = $(1, 1)$.

Relationship between entailment and contradiction:

- KB contradicts f iff KB ⊧ ¬f

Summary:

- Compare to M(KB ∪ {f}) with M(KB).
- Entailment (no change): M(KB ∪ {f}) = M(KB).
- Contradiction (empty set): M(KB ∪ {f}) = ∅.
- Contingency (shrinks the set): ∅ ≠ M(KB ∪ {f}) ≠ M(KB).

---

## Section: Ask and Tell (`ask_tell`)

Now let's explore what you can do with a knowledge base.

```python
Rain = Bool("Rain")
Wet = Bool("Wet")
symbols = [Rain, Wet]
```

Suppose we have a knowledge base KB.

```python
kb = [Rain, Implies(Rain, Wet)]

# Helper function to get the models of a KB
def M(kb: list[Formula]) -> list[Model]:
    return get_models(to_formula(kb), symbols)
```

What can we do with it?

- Ask yes/no questions of the KB: Ask[f]
- Tell the KB new statements: Tell[f]

What are the possible responses?

Depends on the relationship between KB and f.

### Ask

Let's begin by asking yes/no questions of the KB.

There are three possible responses: yes, no, and I don't know.

```python
def ask(kb: list[Formula], f: Formula) -> str:
    old_models = M(kb)  # M(KB) @inspect old_models @stepover
    new_models = M(kb + [f])  # M(KB ∪ {f}) @inspect new_models @stepover

    if new_models == old_models:  # KB entails f
        return "Yes"

    if new_models == []:  # KB contradicts f
        return "No"

    return "I don't know"         # KB is contingent with respect to f

kb = [Rain, Wet]  # It's raining and wet
result = ask(kb, Or(Rain, Wet))  # Is it raining or wet?  @inspect result

kb = [Not(Rain), Wet]  # It's not raining
result = ask(kb, Rain)  # Is it raining?  @inspect result

kb = [Rain]
result = ask(kb, Wet)  # Is it wet?  @inspect result
```

Traced:

1. KB = {Rain, Wet}, Ask[Rain ∨ Wet]: `old_models` = `new_models` = $(1, 1)$, so `result` = **"Yes"**.
2. KB = {¬Rain, Wet}, Ask[Rain]: `old_models` = $(0, 1)$, `new_models` empty, so `result` = **"No"**.
3. KB = {Rain}, Ask[Wet]: `old_models` = $(1, 0)$, $(1, 1)$, `new_models` = $(1, 1)$, so `result` = **"I don't know"**.

### Tell

Now let's add new information to the KB.

There are three possible responses: I already knew that, I don't buy that, and I learned something new.

```python
def tell(kb: list[Formula], f: Formula) -> list[Formula]:
    old_models = M(kb)  # M(KB) @inspect old_models @stepover
    new_models = M(kb + [f])  # M(KB ∪ {f}) @inspect new_models @stepover

    if new_models == old_models:  # KB entails f
        return kb, "I already knew that"

    if new_models == []:  # KB contradicts f
        return kb, "I don't buy that"

    return kb + [f], "I learned something new"  # KB is contingent with respect to f

kb = [Rain, Implies(Rain, Wet)]  # It's raining, and if it's raining, it's wet.
new_kb, result = tell(kb, Wet)  # It's wet  @inspect new_kb result

kb = [Rain, Wet]  # It's raining and wet
new_kb, result = tell(kb, Not(Rain))  # It's not raining  @inspect new_kb result

kb = [Rain]  # It's raining
new_kb, result = tell(kb, Not(Wet))  # It's not wet  @inspect new_kb result
```

Traced:

1. KB = {Rain, Rain → Wet}, Tell[Wet]: `old_models` = `new_models` = $(1, 1)$; `new_kb` = [Rain, Implies(Rain, Wet)] (unchanged), `result` = **"I already knew that"**.
2. KB = {Rain, Wet}, Tell[¬Rain]: `new_models` empty; `new_kb` = [Rain, Wet] (unchanged), `result` = **"I don't buy that"**.
3. KB = {Rain}, Tell[¬Wet]: `old_models` = $(1, 0)$, $(1, 1)$, `new_models` = $(1, 0)$; `new_kb` = [Rain, Not(Wet)], `result` = **"I learned something new"**.

The `kb = [Rain, Implies(Rain, Wet)]` assigned near the top of the function is not used by any of the six calls, each of which sets its own KB.

Summary:

- Ask: queries a knowledge base
- Tell: adds new information to a knowledge base
- Entailment (Ask: yes, Tell: I already knew that)
- Contradiction (Ask: no, Tell: I don't buy that)
- Contingency (Ask: I don't know, Tell: I learned something new)

---

## Section: Connection to Bayesian networks (`connection_to_bayesian_networks`)

Recall that a Bayesian network defines a joint distribution over a set of variables.

```python
P = ProbTable("Rain Wet", [[0.5, 0.1], [0.1, 0.3]])  # P(Rain, Wet) @inspect P @stepover
```

Traced table:

| rain | wet | P(Rain=rain, Wet=wet) |
| ---- | --- | --------------------- |
| 0 | 0 | 0.5 |
| 0 | 1 | 0.1 |
| 1 | 0 | 0.1 |
| 1 | 1 | 0.3 |

For every assignment, we have a probability (between 0 and 1).

Translating terminology between Bayesian networks ↔ propositional logic

- Random variables ↔ propositional symbols
- Models ↔ assignments

Recall probabilistic inference: P(query | evidence)

- Evidence ↔ knowledge base KB
- Query ↔ thing we're Ask[]'ing about

P(Rain | Wet = 1) ↔ Tell[Wet]; Ask[Rain]

Key difference:

- In Bayesian networks, query and evidence are just simple assignments to variables (e.g., Rain = 1, Wet = 0).
- In propositional logic, query and evidence are arbitrary formulas (e.g., (Rain ∧ Wet) ∨ (Rain ∧ ¬Snow)).

Printed as plain text in the source; in the course's notation:

$$P(\text{KB}) = \sum_{w \in \mathcal{M}(\text{KB})} P(W = w)$$

$$P(\text{KB} \cup \lbrace f \rbrace) = \sum_{w \in \mathcal{M}(\text{KB} \cup \lbrace f \rbrace)} P(W = w)$$

$$P(f \mid \text{KB}) = \frac{P(\text{KB} \cup \lbrace f \rbrace)}{P(\text{KB})}$$

This provides a number between 0 and 1 that generalizes the yes/no/I don't know responses.

Figure: `logic-01.png` — a horizontal number line from 0 to 1, labelled "no" under 0, "don't know" under the middle, and "yes" under 1.

The program defines the table but does not compute $P(\text{Rain} \mid \text{Wet} = 1)$ or any other conditional from it.

---

## Section: Satisfiability (`satisfiability`)

So far: Ask[f] and Tell[f] implemented in terms of entailment, contradiction, and contingency.

How do we implement entailment, contradiction, and contingency efficiently?

Currently, we enumerate all the models M(KB) and M(KB ∪ {f}), which will be exponentially large.

To get faster algorithms, let's reduce to a single operation.

Definition: KB is **satisfiable** iff M(KB) ≠ ∅.

Let's reduce entailment/contradiction/contingency to satisfiability.

Call satisfiability checker on KB ∪ {f}.

If not satisfiable, then return contradiction.

But if satisfiable, could be entailment or contingency.

Recall KB entails f iff KB contradicts ¬f.

Call the satisfiability checker on KB ∪ {¬f}.

If not satisfiable, then return entailment.

Figure: `logic-satisfiability.png` — a decision tree. Root: "Is KB ∪ {¬f} **satisfiable**?" Its "no" branch (red) leads to a box "entailment"; its "yes" branch (green) leads to a second question, "Is KB ∪ {f} **satisfiable**?", whose "no" branch leads to "contradiction" and whose "yes" branch leads to "contingent". (The figure asks about KB ∪ {¬f} first; the printed text above describes the KB ∪ {f} call first.)

Note that we need two calls because one call only gives us 1 bit of information and we have 3 outcomes.

**Model checking**: task for determining if a KB is satisfiable.

- Input: a KB
- Output: whether KB is satisfiable

We can use Z3's solver to check satisfiability.

```python
Rain = Bool("Rain")
Wet = Bool("Wet")
kb = [Rain, Wet]
solver = Solver()
for f in kb:  # @inspect f
    solver.add(f)

# Perform model checking
result = solver.check()  # @inspect result @clear f

# If satisfiable, return some model in M(KB)
if result == sat:
    w = solver.model()  # @inspect w
```

Traced: `result` = `sat`, and `w` = `[Rain = True, Wet = True]`.

Under the hood: SAT solvers

- DPLL algorithm (exhaustive search)
- Conflict-driven clause learning (CDCL)

Summary:

- Entailment, contradiction, and contingency can be reduced to satisfiability
- Use model checking to compute satisfiability

---

## Section: Inference rules (`propositional_logic_inference_rules`)

We have defined the syntax and semantics of propositional logic.

We can do logical inference (Ask/Tell) via satisfiability via model checking.

But remember the problem: If A + B = 10 and A - B = 4, what is A?

We didn't do model checking, but rather manipulated symbols.

This is what you normally do as a human (e.g., for math).

(The function then runs `forward_inference` and `soundness_completeness`.)

---

## Section: Forward inference (`forward_inference`)

Example of an inference:

- It is raining (Rain).
- If it is raining, then it is wet (Rain → Wet).
- Therefore, it is wet (Wet).

Figure: `logic-modus-ponens-rain-wet.png` — the inference written as a rule: "Rain, Rain → Wet" above a horizontal bar and "Wet" below it, with a small legend to the right reading "(premises)" over "(conclusion)".

General modus ponens: p, p → q ⊢ q

General inference rule:

- Premises: set of formulas f_1, ..., f_n
- Conclusion: formula g

Rules operate on the **syntax**, not the **semantics**.

**Forward inference**

Input:

- set of inference rules (Rules)
- initial knowledge base (KB)

While there are no changes to KB: [sic — corrected that evening to "Until there are no more changes to KB:"]

- Choose a set of formulas f_1, ..., f_n ∈ KB
- If matching rule f_1, ..., f_n ⊢ g ∈ Rules, then add g to KB.

We say that KB derives/proves f (KB ⊢ f) if f eventually gets added to KB.

Example:

Figure: `logic-modus-ponens-example.png` — a boxed worked example titled "Example: Modus ponens inference". Starting point: KB = {Rain, Rain → Wet, Wet → Slippery}. "Apply modus ponens to Rain and Rain → Wet": KB = {Rain, Rain → Wet, Wet → Slippery, **Wet**} (Wet in red). "Apply modus ponens to Wet and Wet → Slippery": KB = {Rain, Rain → Wet, Wet → Slippery, **Wet**, **Slippery**} (both in red). "Converged."

Can't derive some formulas: ¬Wet, Rain → Slippery

---

## Section: Soundness and completeness (`soundness_completeness`)

Syntax: KB proves/derives f (KB ⊢ f)

Semantics: KB entails f (KB ⊧ f)

What is the relationship?

In particular, compare:

Syntax: { f: KB ⊢ f }

Semantics: { f: KB ⊧ f }

Truth: { f: KB ⊧ f }

Figure: `empty-water-glass.jpg` — a photograph of an empty clear drinking glass.

Soundness: { f: KB ⊢ f } ⊆ { f: KB ⊧ f }

Figure: `half-water-glass.jpg` — a photograph of a tall glass partly filled with water (the water line sits well above halfway, though the file is named "half").

Completeness: { f: KB ⊧ f } ⊆ { f: KB ⊢ f }

Figure: `full-water-glass.jpg` — a photograph of water being poured from a bottle into a glass that is full and overflowing, with water spilled on the surface beneath.

What we want is ideally:

- *The truth, the whole truth, and nothing but the truth*
- Soundness: nothing but the truth
- Completeness: the whole truth

Examples:

Figure: `logic-soundness1.png` — "Is (Rain, Rain → Wet / Wet) (Modus ponens) sound?" Below it, $\mathcal{M}(\text{Rain}) \cap \mathcal{M}(\text{Rain} \rightarrow \text{Wet}) \subseteq ? \thinspace \mathcal{M}(\text{Wet})$, with a $2 \times 2$ grid (rows Rain 0/1, columns Wet 0/1) under each set. $\mathcal{M}(\text{Rain})$ shades the Rain = 1 row; $\mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ shades every cell except (Rain 1, Wet 0); the cell in both, (1, 1), is a darker red in each grid. $\mathcal{M}(\text{Wet})$ shades the Wet = 1 column green. The intersection (1, 1) lies inside the green column: "**Sound!**"

Figure: `logic-soundness2.png` — "Is (Wet, Rain → Wet / Rain) sound?" Below it, $\mathcal{M}(\text{Wet}) \cap \mathcal{M}(\text{Rain} \rightarrow \text{Wet}) \subseteq ? \thinspace \mathcal{M}(\text{Rain})$. $\mathcal{M}(\text{Wet})$ shades the Wet = 1 column; $\mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ shades every cell except (Rain 1, Wet 0); the intersection, (0, 1) and (1, 1), is darker red. $\mathcal{M}(\text{Rain})$ shades the Rain = 1 row green. The intersection's cell (0, 1) lies outside the green row: "**Unsound!**"

---

## Section: Summary (`main`)

Summary:

- Syntax, semantics, inference rules
- Models, interpretation function, knowledge base
- Semantics: Entailment, contradiction, contingency, satisfiability
- Connect syntax and semantics: soundness, completeness

Next time: first-order logic (fancy stuff)

---

## Changes after the lecture

The next commit to `propositional_logic.py`, af5f29c1 ("fix typos", 2025-11-10 10:51pm PST, the evening after the lecture), changed the following. The version above is the one taught. No traced value changed.

1. The interpretation function's two cases became "true (1): means that f is true in w, or" and "false (0): means that f is false in w." (previously "w satisfies f" / "w does not satisfy f").
2. `I` gained an `@inspect g h` annotation on the line that splits a binary formula into its two arguments.
3. In `ask_tell`, the second Ask example's KB was reordered to `[Wet, Not(Rain)]` with the comment "It is wet and not raining." (previously `[Not(Rain), Wet]`, "It's not raining"); the other comments gained full stops and "It's" became "It is". The answers are unchanged.
4. In `forward_inference`, "While there are no changes to KB:" became "Until there are no more changes to KB:".
5. In `soundness_completeness`, the two set definitions gained glosses: "(formulas that can be derived from KB)" and "(formulas that are are entailed by KB)".

A later commit, 79ab1572 ("fix up first-order logic", 2025-11-13 6:03pm PST, after lecture 16), corrected "Think a logic as a language" to "Think of a logic as a language" and "that are are entailed" to "that are entailed".

## Figures

`perceive-reason-act-learn.png` is byte-identical at commit 0116e139 to the version pinned for lecture 12 (git blob 3c8f4ce4 at both commits) and is described in raw/slides/05-search-i.md and raw/slides/12-bayesian-networks-i.md. The other sixteen images were added in commit 0116e139 and were read directly for this file by the parent model (claude-opus-5-5). The numeric and logical content was checked against the trace: the evaluation tree's values against the traced `I` calls, the soundness grids against the traced model sets for Rain, Wet and Rain → Wet, and the modus ponens example against the forward-inference definition.

- **`syntax-semantics-rules.png`** (1034 × 721 px): the three ingredients of a logic. On the left, the heading "Syntax" in red over a vertical stack of eight pink bars, the first labelled "formula"; below the stack a thick dark-red arrow points down, labelled "Inference rules" in green, to a ninth pink bar. On the right, the heading "Semantics" in blue over a rounded box containing a large light-green ellipse with a smaller light-blue ellipse inside it, labelled "models" above. A black arrow runs from the top formula bar to the edge of the inner light-blue ellipse; another runs from the derived bar at the bottom to the edge of the outer light-green ellipse. Shown twice (`ingredients_of_a_logic` and `introduce_propositional_logic`).
- **`logic-fw.png`** (803 × 368 px): a pink bar labelled $f$ on the left; a rounded box on the right containing one blue dot labelled $w$; a dashed dark-red line joins the bar to the dot. It pictures $\mathcal{I}(f, w)$ as the formula being checked against a single model.
- **`logic-i-tree.png`** (1511 × 669 px): a tree of boxed equations, formulas in red. Root: $\mathcal{I}((\neg A \wedge B) \leftrightarrow C, w) = 1$. Children: $\mathcal{I}(\neg A \wedge B, w) = 0$ (left) and $\mathcal{I}(C, w) = 0$ (right). Under the left child: $\mathcal{I}(\neg A, w) = 0$ and $\mathcal{I}(B, w) = 1$. Under $\mathcal{I}(\neg A, w)$: $\mathcal{I}(A, w) = 1$. These are the values for $w$ = {A: true, B: true, C: false}, and every one matches the traced recursion (which skips $B$; see the section above).
- **`logic-models.png`** (924 × 500 px): a pink bar labelled $f$ on the left with a solid dark-red arrow into a rounded box on the right, ending at a light-blue ellipse labelled $\mathcal{M}(f)$ above it.
- **`logic-entailment.png`** (1043 × 394 px): a dark-green ellipse labelled $\mathcal{M}(\text{KB})$ wholly inside a larger pink ellipse, which is labelled $\mathcal{M}(f)$ to its right. Every model of the KB is a model of $f$.
- **`logic-contradiction.png`** (1110 × 310 px): a green ellipse labelled $\mathcal{M}(\text{KB})$ on the left and a smaller, separate pink ellipse labelled $\mathcal{M}(f)$ on the right. They share no models.
- **`logic-contingency.png`** (936 × 285 px): a green ellipse labelled $\mathcal{M}(\text{KB})$ overlapping a smaller pink ellipse labelled $\mathcal{M}(f)$; the overlap is a darker olive lens. Part of $\mathcal{M}(\text{KB})$ survives adding $f$, part does not.
- **`logic-01.png`** (791 × 122 px): a horizontal line with end ticks, "0" above the left end and "1" above the right; beneath it "no" at the left, "don't know" in the middle and "yes" at the right.
- **`logic-satisfiability.png`** (1323 × 719 px): the two-call decision tree described in the Satisfiability section. "satisfiable" is in purple, the "no" branch labels in red and the "yes" labels in green; the three outcomes ("entailment", "contradiction", "contingent") are in heavy-bordered boxes.
- **`logic-modus-ponens-rain-wet.png`** (1228 × 230 px): "Rain, Rain → Wet" over a horizontal bar over "Wet", and to the right a small "(premises)" over a bar over "(conclusion)".
- **`logic-modus-ponens-example.png`** (1776 × 863 px): the boxed example described in the Forward inference section, with an orange title "Example: Modus ponens inference" beside a small multicoloured dodecahedron icon, step labels in blue, and each newly added formula in red.
- **`logic-soundness1.png`** and **`logic-soundness2.png`** (2174 × 980 px each): the two soundness checks described in the Soundness section. In each, the premises' model sets are written in red and the conclusion's in green, with a 2 × 2 grid under each (rows "Rain" 0 and 1, columns "Wet" 0 and 1). Light red marks a cell in that premise's models, darker red a cell in both premises' models, green a cell in the conclusion's models. In the first, the only darker cell is (Rain 1, Wet 1), inside the green Wet = 1 column, and the verdict "Sound!" is in blue. In the second, the darker cells are (0, 1) and (1, 1); (0, 1) falls outside the green Rain = 1 row, and the verdict "Unsound!" is in dark red. The grids match the traced model sets: $\mathcal{M}(\text{Rain})$ = $(1, 0)$, $(1, 1)$; $\mathcal{M}(\text{Rain} \rightarrow \text{Wet})$ = $(0, 0)$, $(0, 1)$, $(1, 1)$.
- **`empty-water-glass.jpg`** (127 × 168 px), **`half-water-glass.jpg`** (160 × 237 px), **`full-water-glass.jpg`** (186 × 245 px): three small stock photographs on white or pale grey backgrounds, described in the Soundness section: an empty glass, a glass partly filled with water, and a glass being filled from a bottle until it overflows. They are printed beside "Truth", "Soundness" and "Completeness" respectively; the source gives them no caption.
