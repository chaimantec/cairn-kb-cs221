---
title: Lecture 16 — Logic II (Slides)
lecture: 16
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_commit: c88136d (2025-11-12, "add first-order logic")
source_files:
  - https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/c88136d/first_order_logic.py
rendered:
  - https://stanford-cs221.github.io/autumn2025-lectures/?trace=first_order_logic
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. Lecture 16 was delivered as
  one program, `first_order_logic.py`, which builds first-order formulas with the Z3
  library's Python API (`DeclareSort`, `Const`, `Function`, `ForAll`, `Exists`, plus
  the connectives from lecture 15) and imports `I`, `get_models` and `to_formula`
  from lecture 15's `propositional_logic.py`. There are no slide numbers; sections
  correspond to function calls in the source. The file is transcribed here AS OF
  COMMIT c88136d ("add first-order logic"), which landed at 1:29pm PST on Wed Nov 12
  2025, a minute before the lecture, and whose README diff removes "under
  construction" from the `first_order_logic` entry under the heading "Wed Nov 12:
  learning (lecture 16)" [sic]. The next commit to the file (79ab157, "fix up
  first-order logic", 2025-11-13 6:03pm PST, the day after) corrected several
  slips that were on screen during the lecture; they are listed in the "Changes
  after the lecture" section at the end. The file was later rewritten in 2026 for a
  later offering, so the repo's `main` branch and the live rendered UI no longer
  show what was taught; the pinned URL above does. All demo outputs were read on
  2026-10-25 from the execution trace committed at the same commit
  (`var/traces/first_order_logic.json`), whose embedded source is byte-identical to
  the pinned file. Three images are shown: `syntax-semantics-rules.png`, unchanged
  since lecture 15 and described there (shown twice), and two new images added in
  the same commit, described in the "Figures" section at the end of this file.
---

# Lecture 16 Slides — Logic II

Lecture 16 is the second logic lecture and introduces **first-order logic**. It opens with a worked example in which a knowledge base about Alice, Phoenix and the weather is built one fact at a time and asked "Is it snowing?", reviews propositional logic from lecture 15, and shows where propositional logic becomes clunky: it has no **objects** and no **quantifiers**. First-order logic adds **terms** (constants, variables and functions, which denote objects), **predicates** applied to terms (which give atomic formulas), and the **quantifiers** $\forall$ and $\exists$. Its semantics introduces a layer of indirection: a **model** is a **domain** of objects together with an **interpretation** that maps constants, functions and predicates onto them, and the interpretation function $\mathcal{I}(f, w)$ is computed recursively, looping over the domain for a quantifier. Under the **unique names** and **domain closure** assumptions a first-order KB can be **propositionalized**. For inference, the lecture restricts to **definite clauses** and generalizes **modus ponens** with **substitution** and **unification**, then states its complexity, that it is sound but not complete, and that the **resolution** rule is needed for completeness. It closes by translating natural-language sentences into first-order logic.

| Part | Function(s) | Content |
| ---- | ----------- | ------- |
| 1. Title | `main` | Lecture title and order |
| 2. Motivating example | `motivating_example`, `ask` | A KB built fact by fact; "Is it snowing?" three times |
| 3. Review of logic | `review_logic` | Syntax, semantics and inference rules of propositional logic, recapped |
| 4. Limitations of propositional logic | `limitations_of_propositional_logic` | Alice and Bob know arithmetic; all students; even integers as sums of two primes |
| 5. First-order logic | `introduce_first_order_logic` | Overview |
| 6. Syntax | `first_order_logic_syntax` | Terms (constants, variables, functions); formulas (atomic, connectives, quantifiers); non-formulas |
| 7. Semantics | `first_order_logic_semantics`, `interpret_formula`, `interpret_term` | Attempt 1 and its two problems; attempt 2: domain plus interpretation |
| 8. Propositionalization | `propositionalization` | Unique names and domain closure; reducing to propositional logic |
| 9. Inference rules | `first_order_logic_inference_rules` | Definite clauses; modus ponens with exact match, then with substitution and unification; complexity; soundness and completeness |
| 10. Substitution | `introduce_substitution`, `substitute` | Search and replace on terms |
| 11. Unification | `introduce_unification`, `unify`, `is_variable` | Finding a substitution that makes two formulas equal |
| 12. Natural language to first-order logic | `natural_language_to_first_order_logic` | ∀ with →, ∃ with ∧; nested quantifiers; even integers as sums of two primes; reruns the motivating example |
| 13. Summary | `main` | Seven-point summary |

Notation follows the lecture's. Constants, variables and function names are lowercase (`alice`, `bob`, `arithmetic`, `x`, `y`, `z`, `father`, `add`); predicates are capitalized (`Student`, `Knows`, `Takes`, `Covers`). $f$ is a formula, $w$ a model, $\mathcal{I}$ the interpretation function (written ℐ in the printed text), $o_1$, $o_2$, $o_3$ objects of the domain (written `"o1"`, `"o2"`, `"o3"` in the code), and $\theta$ a substitution. $\text{KB} \models f$ (printed ⊧) is entailment and $\text{KB} \vdash f$ (printed ⊢) is derivation, as in lecture 15. In the code, every object belongs to one Z3 sort, `Object` ("We just define one sort (type) for simplicity"), and Z3 writes the equality of two terms as `==`.

---

## Section: Title (`main`)

**Lecture 16: First-order logic**

Order of the lecture: `motivating_example` → `review_logic` → `limitations_of_propositional_logic` → `introduce_first_order_logic` (which runs `first_order_logic_syntax`, `first_order_logic_semantics`, `propositionalization`, `first_order_logic_inference_rules` and `natural_language_to_first_order_logic`) → summary.

The symbols used across the lecture are declared at the top of the file: the constants `alice`, `bob`, `arithmetic`, `phoenix`, `cs221`, `logic` and `two`; the variables `x`, `y` and `z`; the functions `father` (one argument) and `add` (two arguments); the predicates `Person`, `Student`, `Course`, `Concept`, `Even`, `Prime`, `Hot`, `City`, `Place` and `Happy` (one argument) and `From`, `Knows`, `Takes`, `Covers` and `GreaterThan` (two arguments); and the propositional symbols `Snowing` and `Cold`.

---

## Section: Motivating example (`motivating_example`, `ask`)

The program builds a knowledge base one sentence at a time, printing each English sentence in italics with its first-order formula as a comment beneath:

| Sentence | Formula |
| -------- | ------- |
| *Alice is a student.* | Student(alice) |
| *Alice is from Phoenix.* | From(alice, phoenix) |
| *Phoenix is a hot city.* | Hot(phoenix) ∧ City(phoenix) |
| *Students are people.* | ∀x. Student(x) → Person(x) |
| *Cities are places.* | ∀x. City(x) → Place(x) |
| *If it is snowing, it is cold.* | Snowing → Cold |

*Is it snowing?* — `ask(kb, Snowing)` traced: **"I don't know"**.

*If a person is from a hot place and it is snowing, then they are unhappy.* — ∀x, y. Person(x) ∧ From(x, y) ∧ Hot(y) ∧ Place(y) ∧ Snowing → ¬Happy(x)

*Is it snowing?* — traced: **"I don't know"**.

*Alice is happy.* — Happy(alice)

*Is it snowing?* — traced: **"No"**.

This is an example of performing inference in first-order logic.

What can first-order logic represent?

How can we do inference in first-order logic?

`ask` reduces the question to two satisfiability checks with Z3's solver, as in lecture 15:

```python
def ask(kb: list[Formula], f: Formula) -> str:
    """Ask a yes/no question of the KB.  Reduce this to a satisfiability problem and call Z3's solver."""
    solver = Solver()
    solver.add(kb + [Not(f)])
    if solver.check() == unsat:
        return "Yes"
    
    solver = Solver()
    solver.add(kb + [f])
    if solver.check() == unsat:
        return "No"
    
    return "I don't know"
```

The same function runs again at the end of the lecture (see section 12) and gives the same three answers.

---

## Section: Review of logic (`review_logic`)

Logic is a language for representing knowledge and reasoning about it.

To define a logic, we need to define its syntax, semantics, and inference rules.

Figure: `syntax-semantics-rules.png` (from lecture 15 — formulas on the "Syntax" side, a nested pair of model sets on the "Semantics" side, and an "Inference rules" arrow deriving a new formula; described in raw/slides/15-logic-i.md).

**Syntax**: what are valid formulas?

Propositional logic:

- Propositional symbols (e.g., Rain Wet):
- Recursively combined using logical connectives (e.g., ∧, ∨, ¬, →, ↔)

```python
Rain = Bool("Rain")  # Rain
Wet = Bool("Wet")  # Wet
symbols = [Rain, Wet]
formula = And(Rain, Not(Wet))  # Rain ∧ ¬Wet
```

These are just symbols.

**Semantics**: what do these formulas mean?

Propositional logic:

- Models: assignments of truth values to propositional symbols — `w = {Rain: True, Wet: False}`
- Interpretation function: maps a formula and a model to a truth value — `I(formula, w)` traced: **true**
- Models: maps a formula f to the set of models M(f) that satisfy it — `get_models(formula, [Rain, Wet])` traced: one model, {Rain: true, Wet: false}
- Knowledge base: set of formulas (what we know) — `kb = [Rain, Implies(Rain, Wet)]  # Rain ∧ (Rain → Wet)`, with `f = Wet`

Relationship between knowledge base KB and formula f:

- Old models: M(KB) — traced: one model, {Rain: true, Wet: true}
- New models: M(KB ∪ {f}) — traced: the same one model, {Rain: true, Wet: true}
- Entailment: M(KB) = M(KB ∪ {f})
- Contradiction: M(KB ∪ {f}) = ∅
- Contingency: ∅ ≠ M(KB ∪ {f}) ≠ M(KB)

(The two model sets are equal, so the code sets `result = "Entailment"`; that value is not inspected in the trace.)

Used to implement:

- Ask[f]: check if f is entailed by the KB
- Tell[f]: add f to the KB if it is contingent on the KB

Satisfiability: KB is satisfiable iff M(KB) ≠ ∅

Model checking: determine if a KB is satisfiable

Reduce entailment/contradiction/contingency to satisfiability

**Inference rules**: how to derive new valid formulas from existing ones?

Set of rules (e.g., modus ponens) operate on the syntax

Modus ponens: p, p → q ⊢ q

Syntax: KB derives/proves f (KB ⊢ f) if exists set of rules that produce f

Semantics: KB entails f (KB ⊧ f) if M(KB) = M(KB ∪ {f})

Relationship between syntax and semantics:

- Soundness: if KB ⊢ f, then KB ⊧ f
- Completeness: if KB ⊧ f, then KB ⊢ f

Ideally want soundness (nothing but the truth) and completeness (the whole truth).

---

## Section: Limitations of propositional logic (`limitations_of_propositional_logic`)

Let's try to represent some facts in propositional logic.

*Alice and Bob both know arithmetic.*

```python
AliceKnowsArithmetic = Bool("AliceKnowsArithmetic")
BobKnowsArithmetic = Bool("BobKnowsArithmetic")
formula = And(AliceKnowsArithmetic, BobKnowsArithmetic)
```

*All students know arithmetic.*

```python
AliceIsStudent = Bool("AliceIsStudent")
BobIsStudent = Bool("BobIsStudent")
formula = And(Implies(AliceIsStudent, AliceKnowsArithmetic), Implies(BobIsStudent, BobKnowsArithmetic))
```

*Every even integer greater than 2 is the sum of two prime numbers.*

???

In these cases, propositional logic is very clunky or not expressive enough.

What's missing?

- Objects and predicates: propositions (e.g., AliceKnowsArithmetic) have more internal structure (alice, Knows, arithmetic)
- Quantifiers and variables: *all* is a quantifier that applies to all people, don't/can't enumerate them all

---

## Section: First-order logic (`introduce_first_order_logic`)

Figure: `syntax-semantics-rules.png` again (described in raw/slides/15-logic-i.md).

The function then runs the five sections below in order: syntax, semantics, propositionalization, inference rules, and natural language to first-order logic.

---

## Section: Syntax (`first_order_logic_syntax`)

**Syntax**: what are valid formulas?

In propositional logic, formulas denote truth values.

In first-order logic, formulas denote truth values and terms denote objects.

**Terms** (denote objects)

Constants:

```python
alice = Const("Alice", Object)  # alice
arithmetic = Const("arithmetic", Object)  # arithmetic
```

Variables:

```python
x = Const("x", Object)  # x
y = Const("y", Object)  # y
```

Functions:

```python
father = Function("father", Object, Object)  # father
add = Function("add", Object, Object, Object)  # add
term = father(alice)  # father(alice)
term = add(x, y)  # add(x, y)
```

**Formulas** (denote truth values)

Atomic formulas (predicate applied to terms):

```python
Snowing = Bool("Snowing")  # 0-arity predicate (from propositional logic)
Student = Function("Student", Object, BoolSort())  # 1-arity predicate (unary)
Knows = Function("Knows", Object, Object, BoolSort())  # 2-arity predicate (binary)
formula = Knows(x, arithmetic)  # Knows(x, arithmetic)
```

Connectives applied to formulas (just like propositional logic):

```python
formula = Implies(Student(x), Knows(x, arithmetic))  # Student(x) → Knows(x, arithmetic)
```

Quantifiers applied to formulas:

```python
# ∀x. Student(x) → Knows(x, arithmetic)
formula = ForAll([x], Implies(Student(x), Knows(x, arithmetic)))
# ∃x. Student(x) ∧ Knows(x, arithmetic)
formula = Exists([x], And(Student(x), Knows(x, arithmetic)))
```

Non-formulas (in first-order logic):

- father(x) — comment in the source: "father(x) is a term, not a formula"
- Knows(Student, arithmetic) — "Student is a predicate, not a term"
- Foo(Knows(alice, arithmetic)) — "Can't apply function (Foo, etc.) to formula"

Convention:

- Use lowercase for terms (e.g., alice, x, father(alice))
- Use uppercase for formulas (e.g., Knows(x, arithmetic))

Summary:

- Terms (denote objects): arithmetic, x, y, father(x)
- Formulas (denote truth values): ∀x. Student(x) → Knows(x, arithmetic)

(In Z3 a variable is declared exactly like a constant, with `Const`; the code tells them apart only by name. See `is_variable` in section 11.)

---

## Section: Semantics (`first_order_logic_semantics`, `interpret_formula`, `interpret_term`)

**Semantics**: what do these formulas mean?

Recall that a model represents a possible world.

In propositional logic, a model is an assignment of truth values to atomic formulas (propositional symbols).

```python
AliceKnowsArithmetic = Bool("AliceKnowsArithmetic")
BobKnowsArithmetic = Bool("BobKnowsArithmetic")
w = {AliceKnowsArithmetic: True, BobKnowsArithmetic: True}
```

What about in first-order logic?

**Attempt 1**

Recall that in first-order logic, atomic formulas are predicates applied to terms.

```python
term = Knows(alice, arithmetic)  # formula Knows(alice, arithmetic)
term = Student(alice)  # formula Student(alice)
```

So let's define a model as an assignment of truth values to atomic formulas.

```python
w = {Student(alice): True, Knows(alice, arithmetic): True}
```

Problem 1: what about atomic formulas with functions?

```python
father = Function("father", Object, Object)  # function father
term = father(alice)
term = father(father(alice))
term = father(father(father(alice)))
term = father(father(father(father(alice))))
```

There are an infinite number of atomic formulas!

So a single model has to assign truth values to an infinite number of atomic formulas...not good.

Problem 2: two terms (different syntax) might actual refer to the same object.

```python
term = father(alice)
term = bob
```

It could be the case that Bob is Alice's father!

But these two terms behave independently of each other in the model...not good.

```python
w = {Knows(father(alice), arithmetic): True, Knows(bob, arithmetic): False}
```

**Attempt 2**

Key idea: introduce a layer of indirection

Define **domain** to be a set of objects.

```python
domain = ["o1", "o2", "o3"]
```

Define **interpretation function** to map symbols to objects in the domain.

We start with the primitive symbols: constants, functions, and predicates.

Each symbol is mapped to something involving the objects in the domain.

```python
constants = {
    alice: "o1",
    bob: "o2",
    arithmetic: "o3",
}

functions = {
    father: {"o1": "o2"},
}

predicates = {
    Knows: {
        ("o1", "o3"): True,
        ("o2", "o3"): False,
    },
    Student: {
        "o1": True,
        "o2": False,
        "o3": False,
    },
}

interpretation = Interpretation(
    constants=constants,
    functions=functions,
    predicates=predicates,
)
```

The model is therefore the domain and the interpretation function.

```python
w = FirstOrderLogicModel(
    domain=domain,
    interpretation=interpretation,
)  # @inspect w
```

Traced: `w` has domain [o1, o2, o3]; constants alice → o1, bob → o2, arithmetic → o3; function father: o1 → o2; predicates Knows: (o1, o3) → true, (o2, o3) → false; Student: o1 → true, o2 → false, o3 → false.

Figure: `fol-example.png` — the model drawn as three circled objects in a box labelled $w$. See the Figures section; the figure and the code differ on whether $o_2$ knows arithmetic.

The two classes holding the model:

```python
@dataclass(frozen=True)
class Interpretation:
    """For defining a model in first-order logic. Maps symbols to objects in the domain."""
    constants: dict[Const, str]
    functions: dict[Function, dict[str, str]]
    predicates: dict[Function, dict[tuple[str, ...], bool]]


@dataclass(frozen=True)
class FirstOrderLogicModel:
    """A model in first-order logic. Consists of a domain and an interpretation function."""
    domain: list[str]
    interpretation: Interpretation
```

The interpretation of an arbitrary formula is defined recursively.

As in propositional logic, we define the interpretation function ℐ(f, w)...

...to map a formula f and a model w to a truth value (true/false).

```python
def interpret_formula(f: Formula, w: FirstOrderLogicModel, subst: dict[Variable, str] = {}) -> bool:  # @inspect f w subst
    """
    Interpretation function: given a formula f and a model w, compute whether w satisfies f.
    Substitution: a dictionary mapping variables to terms.
    """
    if is_quantifier(f) and f.is_forall():
        # Universal quantifier (e.g., ∀x. Student(x))
        assert f.num_vars() == 1  # Only support one variable for now
        variable = Var(0, f.var_sort(0))  # @inspect variable # Warning: this doesn't work for nested quantifiers
        body = f.body()  # @inspect body

        # Bind the variable to each object in the domain and check if the formula is true.
        result = True
        for o in w.domain:  # @inspect o
            result = result and interpret_formula(body, w, subst | {variable: o}) # @inspect result

    elif is_quantifier(f) and f.is_exists():
        # Existential quantifier (e.g., ∃x. Student(x))
        assert f.num_vars() == 1  # Only support one variable for now
        variable = Var(f.var_name(0), f.var_sort(0))  # @inspect variable
        body = f.body()  # @inspect body

        # Bind the variable to each object in the domain and check if the formula is true.
        result = False
        for o in w.domain:  # @inspect o
            result = result or interpret_formula(body, w, subst | {variable: o}) # @inspect result

    elif f.num_args() == 0:
        # Constant symbol (e.g., alice)
        result = w.interpretation.constants[f] # @inspect result

    elif f.num_args() == 1:
        if is_not(f):
            # Negation (e.g., ¬Student(alice))
            g = f.arg(0)  # @inspect g
            result = not interpret_formula(g, w, subst) # @inspect result

        else:
            # Unary predicate (e.g., Student(alice))
            predicate, arg = f.decl(), f.arg(0)  # @inspect func arg
            predicate_value = w.interpretation.predicates[predicate]  # @inspect predicate_value
            arg_value = interpret_term(arg, w, subst)  # @inspect arg_value
            result = predicate_value[arg_value] # @inspect result

    elif f.num_args() == 2:
        if is_and(f):
            g, h = f.arg(0), f.arg(1)  # @inspect g h
            result = interpret_formula(g, w, subst) and interpret_formula(h, w, subst) # @inspect result
        elif is_or(f):
            g, h = f.arg(0), f.arg(1)  # @inspect g h
            result = interpret_formula(g, w, subst) or interpret_formula(h, w, subst) # @inspect result
        elif is_implies(f):
            g, h = f.arg(0), f.arg(1)  # @inspect g h
            result = not interpret_formula(g, w, subst) or interpret_formula(h, w, subst) # @inspect result
        elif is_eq(f):
            g, h = f.arg(0), f.arg(1)  # @inspect g h
            result = interpret_formula(g, w, subst) == interpret_formula(h, w, subst) # @inspect result
        else:
            # Binary predicate (e.g., Knows(alice, arithmetic))
            predicate, arg1, arg2 = f.decl(), f.arg(0), f.arg(1)  # @inspect predicate arg1 arg2
            predicate_value = w.interpretation.predicates[predicate]  # @inspect predicate_value
            arg1_value = interpret_term(arg1, w, subst)  # @inspect arg1_value
            arg2_value = interpret_term(arg2, w, subst)  # @inspect arg2_value
            result = predicate_value[(arg1_value, arg2_value)] # @inspect result

    return result


def interpret_term(t: Term, w: FirstOrderLogicModel, subst: dict[Variable, str]) -> bool:  # @inspect t w subst
    """Interpret a term in a model with a substitution."""
    if is_var(t):
        # Variable (e.g., x)
        result = subst[t] # @inspect result

    elif t.num_args() == 0:
        # Constant symbol (e.g., alice)
        result = w.interpretation.constants[t] # @inspect result

    else:
        # Function application (e.g., father(alice))
        func, args = t.decl(), t.args()  # @inspect func args
        func_value = w.interpretation.functions[func]  # @inspect func_value
        arg_values = [interpret_term(arg, w, subst) for arg in args]  # @inspect arg_values
        result = func_value[arg_values] # @inspect result

    return result
```

Two examples are run:

```python
result = interpret_formula(Knows(alice, arithmetic), w)  # @inspect result
result = interpret_formula(ForAll([x], Knows(x, arithmetic)), w)  # @inspect result
```

Traced, first call: `Knows(alice, arithmetic)` is a binary predicate. `interpret_term` maps `alice` to o1 and `arithmetic` to o3, and Knows at (o1, o3) is true, so `result` = **true**.

Traced, second call: the formula is a universal quantifier. Inside the quantifier, Z3 writes the bound variable as `Var(0)`, so `variable` = `Var(0)` and `body` = `Knows(Var(0), arithmetic)`. The loop binds `Var(0)` to each object in turn. With o1 the body is Knows(o1, o3) = true, so `result` stays true. With o2 it is Knows(o2, o3) = false, so `result` becomes false. At o3 the loop variable advances, but Python's `and` stops at the false `result`, so the body is never evaluated for o3. The final `result` = **false**. The trace exercises the ∀ branch, binary predicates, variables and constants; the ∃, negation, connective, unary-predicate and function-application branches are not run in this section.

Summary:

- Semantics defined by interpretation function ℐ(f, w)
- Model w = (domain, interpretation)
- Interpretation maps symbols (constants, functions, predicates) to objects in the domain
- Recursively interpret formulas and terms

How can we perform logical inference (Ask/Tell → entailment/contradiction/contingency → satisfiability)?

---

## Section: Propositionalization (`propositionalization`)

How do we perform inference in first-order logic?

```python
kb = [
    Student(alice),
    Student(bob),
    ForAll([x], Implies(Student(x), Knows(x, arithmetic))),
    Exists([x], And(Student(x), Knows(x, arithmetic))),
]
```

Let's reduce to propositional logic.

We can't do this in general (because first-order logic is more powerful).

Figure: `unique-names-domain-closure.png` — three candidate models $w_1$, $w_2$, $w_3$ for two constants, john and bob. See the Figures section.

But we can do it if we assume two things about the model:

- Unique names: each constant maps to each object corresponds to **at most one** constant (not w_2).
- Domain closure: each object corresponds to **at least one** constant (not w_3).

(The unique-names line is printed as quoted; the commit after the lecture changed it to "each object corresponds to **at most one** constant".)

In this case, we can **propositionalize** the knowledge base.

```python
StudentAlice = Bool("StudentAlice")
StudentBob = Bool("StudentBob")
KnowsAliceArithmetic = Bool("KnowsAliceArithmetic")
KnowsBobArithmetic = Bool("KnowsBobArithmetic")
kb = [
    StudentAlice,
    StudentBob,
    And(Implies(StudentAlice, KnowsAliceArithmetic), Implies(StudentBob, KnowsBobArithmetic)),
    Or(And(StudentAlice, KnowsAliceArithmetic), And(StudentBob, KnowsBobArithmetic)),
]
```

The ∀ formula becomes a conjunction over the two constants and the ∃ formula a disjunction.

This is just propositional logic, and we can use any techniques from last time (e.g., model checking).

In this regime, first-order logic is syntactic sugar for propositional logic.

In other words, it's the same expressivity, but easier to read/write.

---

## Section: Inference rules (`first_order_logic_inference_rules`)

Recall that inference rules match formulas in the KB and produce new formulas.

Let us now define the modus ponens inference rule.

This rule assumes a special type of formulas.

Definition: a **definite clause** is a formula of the following form:

∀ x_1 ... x_n. (a_1 ∧ ... ∧ a_k) → b

...where x_1, ..., x_n are variables, a_1, ..., a_k, b are atomic formulas.

Example:

```python
# ∀ x, y, z. (Takes(x, y) ∧ Covers(y, z)) → Knows(x, z)
formula = ForAll([x, y, z], Implies(And(Knows(x, y), Covers(y, z)), Knows(x, z)))
```

(The comment reads Takes(x, y) but the code builds Knows(x, y); the commit after the lecture changed the code to Takes(x, y), matching the comment and the KB below.)

Non-example (disjunction):

```python
formula = Or(Student(alice), Student(bob))
formula = Exists([x], And(Student(x), Knows(x, arithmetic)))
```

Attempt 1: modus ponens with exact match

Premises:

- a_1, ..., a_k
- ∀ x_1 ... x_n. (a_1 ∧ ... ∧ a_k) → b

Conclusion:

- b

```python
kb = [
    # Takes(alice, cs221)
    Takes(alice, cs221),
    # Covers(cs221, logic)
    Covers(cs221, logic),
    # ∀ x, y, z. (Takes(x, y) ∧ Covers(y, z)) → Knows(x, z)
    ForAll([x, y, z], Implies(And(Takes(x, y), Covers(y, z)), Knows(x, z))),
]
```

However, Takes(alice, cs221) ≠ Takes(x, y)

So we can't apply the rule.

Solution: substitution and unification

(Sections 10 and 11 run here.)

**Attempt 2**: modus ponens with substitution and unification

Premises:

- a_1', ..., a_k'
- ∀ x_1 ... x_n. (a_1 ∧ ... ∧ a_k) → b
- θ = unify(a_1' ∧ ... ∧ a_k', a_1 ∧ ... ∧ a_k)

Conclusion:

- substitute(b, θ) = b'

The same three-formula KB is built again, and then:

```python
theta = unify(And(Takes(alice, cs221), Covers(cs221, logic)), And(Takes(x, y), Covers(y, z)), {})  # @inspect theta
if theta is not None:
    result = substitute(Knows(x, z), theta) # @inspect result @stepover
```

Traced: `unify` descends into the two conjunctions. Takes(alice, cs221) against Takes(x, y) binds x → alice and then y → cs221. Covers(cs221, logic) against Covers(y, z) unifies cs221 with y (y is a variable, so `subst[y]` is set again to cs221, leaving it unchanged) and binds z → logic. `theta` = **{x: alice, y: cs221, z: logic}**, and `substitute(Knows(x, z), theta)` = **Knows(alice, logic)**.

Complexity:

- Each application of Modus ponens produces an aotmic formula (e.g., Knows(alice, logic))
- If no functions, then complexity is num-constant-symbols^(maximum-predicate-arity)
- If functions, then complexity then possibly infinite:

```python
Knows(alice, logic)
Knows(father(alice), logic)
Knows(father(father(alice)), logic)
Knows(father(father(father(alice))), logic)
```

("aotmic" is printed as quoted.)

Properties of Modus ponens:

- Sound: if KB proves f (KB ⊢ f), then KB entails f (KB ⊧ f)
- Not complete: there are formulas where KB entails f (KB ⊧ f) but not KB proves f (KB ⊢ f)
- Need the resolution rule to be complete

Summary:

- Modus ponens: works on definite clauses
- Intuition: no disjunction (or) allowed
- Substitution: search and replace on terms in a formula
- Unification: find a substitution that makes two terms equal

---

## Section: Substitution (`introduce_substitution`, `substitute`)

Substitution: search and replace on terms in a formula

```python
formula = Knows(x, y)
subst = {x: alice, y: cs221}
result = substitute(formula, subst) # @inspect result

formula = And(Student(x), Knows(x, y))  # Student(x) ∧ Knows(x, y)
subst = {x: alice, y: z}
result = substitute(formula, subst) # @inspect result
```

```python
def substitute(f: Formula, subst: dict[Variable, Term]) -> Formula:  # @inspect f subst
    """Substitute variables subst in a formula."""
    if f in subst:
        result = subst[f] # @inspect result
    else:
        new_args = [substitute(f.arg(i), subst) for i in range(f.num_args())]  # @inspect new_args
        result = f.decl()(*new_args) # @inspect result
    return result
```

Traced, first call: x → alice and y → cs221, so `new_args` = [alice, cs221] and `result` = **Knows(alice, cs221)**.

Traced, second call: the recursion goes through `And`, then `Student(x)` (giving Student(alice)) and `Knows(x, y)` (x → alice, y → z, giving Knows(alice, z)). `result` = **And(Student(alice), Knows(alice, z))**, that is, Student(alice) ∧ Knows(alice, z). A variable can be replaced by another variable.

---

## Section: Unification (`introduce_unification`, `unify`, `is_variable`)

Unification: find a substitution that makes two terms equal

```python
result = unify(Knows(x, y), Knows(alice, bob), {}) # @inspect result
result = unify(Knows(alice, y), Knows(x, z), {}) # @inspect result @stepover
result = unify(Knows(alice, y), Knows(bob, z), {}) # Should fail! @inspect result @stepover
```

```python
def unify(f1: Formula, f2: Formula, subst: dict[Variable, Term]) -> dict[Variable, Term]:  # @inspect f1 f2 subst
    if f1 == f2:
        return subst
    elif is_variable(f1):  # @stepover
        subst[f1] = substitute(f2, subst)  # @inspect subst @stepover
        return subst
    elif is_variable(f2):  # @stepover
        subst[f2] = substitute(f1, subst)  # @inspect subst @stepover
        return subst
    else:
        if f1.decl() != f2.decl():
            return None
        for i in range(f1.num_args()):
            # If any child fails, then fail the whole thing
            if unify(f1.arg(i), f2.arg(i), subst) is None:
                return None
        return subst


def is_variable(f: Formula) -> bool:
    # In Takes(alice, x), we actually can't tell the difference between a
    # constant (alice) and a variable (x).  So we have to hardcode them.
    return f.decl().name() in ["x", "y", "z"]
```

Traced:

1. `unify(Knows(x, y), Knows(alice, bob), {})`: same predicate; x is a variable, so x → alice; y is a variable, so y → bob. `result` = **{x: alice, y: bob}**.
2. `unify(Knows(alice, y), Knows(x, z), {})`: alice against x binds x → alice; y against z binds y → z. `result` = **{x: alice, y: z}**.
3. `unify(Knows(alice, y), Knows(bob, z), {})`: alice and bob are different constants, neither a variable, so `unify` returns `None`. `result` = **None** ("Should fail!").

---

## Section: Natural language to first-order logic (`natural_language_to_first_order_logic`)

First-order logic can be used to encode natural language sentences.

*Alice and Bob both know arithmetic.*

Knows(alice, arithmetic) ∧ Knows(bob, arithmetic)

*All students know arithmetic.*

∀x. Student(x) → Knows(x, arithmetic)

*Some student knows arithmetic.*

∃x. Student(x) ∧ Knows(x, arithmetic)

Universal quantification (∀) is usually paired with an implication (→).

Existential quantification (∃) is usually paired with a conjunction (∧).

Probably wrong:

∀x. Student(x) ∧ Knows(x, arithmetic)

*Every object is both a student and knows arithmetic.*

∃x. Student(x) → Knows(x, arithmetic)

*Some object is either not a student or knows arithmetic.*

*There is some course that every student has taken.*

∃x. ∀y. Student(y) → Takes(y, x)

(As taught, the formula does not say that x is a course; the commit after the lecture changed it to ∃x. Course(x) ∧ ∀y. Student(y) → Takes(y, x).)

*Every even integer greater than 2 is the sum of two prime numbers.*

∀x. Even(x) ∧ GreaterThan(x, two) → ∃y, z. Prime(y) ∧ Prime(z) ∧ add(y, z) == x

*If a student takes a course and the course covers a concept, then the student knows the concept.*

∀x, y, z. Student(x) ∧ Takes(x, y) ∧ Course(y) ∧ Covers(y, z) ∧ Concept(z) → Knows(x, z)

The function then calls `motivating_example()` again, rebuilding the KB of section 2. Traced: the three Ask calls again return "I don't know", "I don't know" and "No". (The commit after the lecture removed this second call.)

---

## Section: Summary (`main`)

Summary:

- First-order logic: more powerful than propositional logic
- Introduces terms (denote objects), functions, predicates
- Introduces variables and quantifiers (∀, ∃) that operate over objects
- Model/interpretation function: maps symbols to objects in the domain
- Propositionalization: reduce first-order logic to propositional logic
- Modus ponens with substitution and unification: works on definite clauses
- Higher-order logics to represent more (*70% of students know machine learning*)

---

## Changes after the lecture

The next commit to `first_order_logic.py`, 79ab157 ("fix up first-order logic", 2025-11-13 6:03pm PST, the day after the lecture), changed the following. The version above is the one taught. No traced Ask answer, unification or substitution changed, and $\forall x.\ \text{Knows}(x, \text{arithmetic})$ is still false.

1. In the semantics example, Knows at (o2, o3) became **true** (it was false, while `fol-example.png` draws the arrow from $o_2$ to $o_3$), and the Student entries for o2 and o3 were deleted. A predicate lookup with no entry now returns false (`predicate_value.get(..., False)`), so the ∀ example becomes false at o3 instead of o2.
2. The semantics summary's last bullet became "Recursively interpret larger formulas and terms", and its closing question became "How can we perform logical inference with respect to these semantics?"
3. In `propositionalization`, "Suppose we have the following knowledge base:" was added before the KB; the figure moved below "But we can do it if we assume two things about the model:"; the unique-names line became "each object corresponds to **at most one** constant (not w_2)"; and "But what if we don't have these assumptions?" was added at the end.
4. In `first_order_logic_inference_rules`, "This rule assumes a special type of formulas." became "This rule operate a special type of formula called a definite clause."; the example became the printed line "Example: ∀ x, y, z. (Takes(x, y) ∧ Covers(y, z)) → Knows(x, z)", with the code fixed to Takes(x, y) and a gloss naming the variables (x, y, z) and the atomic formulas (a_1 = Takes(x, y), a_2 = Covers(y, z), b = Knows(x, z)); the premises were listed one per line; the KB comments gained the labels a_1, a_2 (and a_1', a_2' in attempt 2); "We would like conclude b: Knows(alice, logic)" was added; "So we can't apply the rule." gained "(no exact match)"; "Find the substitution to make a_1' ∧ a_2' equal to a_1 ∧ a_2" was added before the `unify` call; the complexity lines became "If we have no functions…" and "If we have functions…"; and the summary's intuition became "no disjunction (or, exists) allowed".
5. `unify` gained the docstring "Given two formulas `f1` and `f2`, find a substitution (adding to `subst`) that makes them equal."
6. In `natural_language_to_first_order_logic`, "There is some course that every student has taken" became ∃x. Course(x) ∧ ∀y. Student(y) → Takes(y, x), and the closing call to `motivating_example()` was removed.

The same commit fixed two typos in lecture 15's `propositional_logic.py`; they are recorded in raw/slides/15-logic-i.md.

## Figures

`syntax-semantics-rules.png` is byte-identical at commit c88136d to the version pinned for lecture 15 (git blob 447d69f4 at both commits) and is described in raw/slides/15-logic-i.md. The two new images were added in commit c88136d and were read directly for this file by the parent model (claude-opus-5-5). Their content was checked against the code at the same commit.

- **`fol-example.png`** (666 × 529 px): a first-order model drawn inside a box labelled $w$ in its top-left corner. Three circled objects: $o_1$ upper left, $o_2$ upper right, $o_3$ below between them. Constant names are in green beside the objects that interpret them: "alice" to the left of $o_1$; "bob" above $o_2$ and "robert" to its right (two constants naming the same object); "arithmetic" below $o_3$. Predicates are in red: "Student" above $o_1$, and two arrows labelled "Knows", one from $o_1$ to $o_3$ and one from $o_2$ to $o_3$. The figure differs from the model built in the code at this commit in three ways: the code has no constant `robert`, its function `father` (o1 → o2) is not drawn, and the code sets Knows at (o2, o3) to **false** while the figure draws that arrow. The commit after the lecture changed the code to true, matching the figure.
- **`unique-names-domain-closure.png`** (1564 × 236 px): three model boxes side by side, labelled $w_1$, $w_2$, $w_3$, each using the constants john and bob (green) and the predicate Student (red). In $w_1$, john names $o_1$ and bob names $o_2$, and both objects are marked Student. In $w_2$, john and bob both name the single object $o_1$, which is marked Student; this is the model the unique-names assumption rules out. In $w_3$, john names $o_1$ and bob names $o_3$, both marked Student, and $o_2$ between them has no name and no predicate; this is the model domain closure rules out. The figure's constants are john and bob, while the KB in the same section uses alice and bob.
