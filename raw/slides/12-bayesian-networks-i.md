---
title: Lecture 12 — Bayesian Networks I (Slides)
lecture: 12
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_commit: f22938e (2025-10-29, "add bayes")
source_files:
  - https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/f22938e/bayes.py
rendered:
  - https://stanford-cs221.github.io/autumn2025-lectures/?trace=bayes
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. Lecture 12 was delivered as
  one program, `bayes.py`. There are no slide numbers; sections correspond to
  function calls in the source. The file is transcribed here AS OF COMMIT f22938e
  ("add bayes"), which landed at 1:26pm PDT on Wed Oct 29 2025, four minutes before
  the lecture, and whose README diff adds "Wed Oct 29: bayes i (lecture 12)". The
  next commit (ed66b66, 2025-11-03, "add Gibbs sampling") only added two `@inspect`
  trace annotations to `hidden_markov_model`, with no change to the lecture content,
  and the file was rewritten in 2026 for a later offering, so the repo's `main` branch and the live rendered UI no longer show what
  was taught; the pinned URL above does. All demo outputs were read on 2026-10-24 from
  the execution trace committed at the same commit (`var/traces/bayes.json`), whose
  embedded source is byte-identical to the pinned file. The rendered UI may differ
  from what was shown live, and the lecturer re-ran some demos in class; where the
  transcript reports different numbers, the wiki page says so. Five figures are
  shown: three new ones are described in the Figures section at the end of this
  file, and two (`perceive-reason-act-learn.png`, `game-tree.png`) are reused from
  lectures 1 and 10, where they are already described.
---

# Lecture 12 Slides — Bayesian Networks I

Lecture 12 opens the course's third module, on **Bayesian networks**. It starts by placing them on the course map: machine learning maps percepts to actions directly, state-based models (search, MDPs, games) reason about the world but represent it as one opaque state, and Bayesian networks represent the state of the world as a set of **random variables**. A probability review (joint distribution, marginalization, conditioning, all written as `einsum` over probability tables) sets up **probabilistic inference**: answering a question $P(\text{query} \mid \text{evidence})$ about a joint distribution, which the lecture likens to running a SQL query on a database. Two worked networks, the **alarm** (burglary, earthquake, alarm) and **medical diagnosis** (cold, allergies, cough, itchy eyes), each built in four steps, show **explaining away**. The general definition follows, then the observation that an autoregressive **language model** is a Bayesian network. The last part writes networks as **probabilistic programs** and does approximate inference by **rejection sampling**, including a hidden Markov model for object tracking, and closes with why Bayesian networks need a shift in mindset from classifiers.

| Part | Function(s) | Content |
| ---- | ----------- | ------- |
| 1. Motivation | `model_based_motivation` | Where the course has been; model-free vs model-based |
| 2. Probability review | `review_probability` | Random variables, joint distribution, marginalization, conditioning, probability tables as tensors |
| 3. Probabilistic inference | `introduce_probabilistic_inference` | Evidence and query; the SQL analogy |
| 4. Alarm | `introduce_alarm` (with class `ProbTable`) | Four steps to a joint distribution; inference by select–sum–divide; explaining away |
| 5. Medical diagnosis | `introduce_medical_diagnosis` | A four-variable network; a subtler explaining away |
| 6. General definition | `introduce_bayesian_networks` | Bayesian network and probabilistic inference in general |
| 7. Language models | `language_models` | Autoregressive LMs as Bayesian networks; inference as jailbreaking |
| 8. Rejection sampling | `introduce_rejection_sampling`, `alarm`, `medical_diagnosis`, `hidden_markov_model`, `rejection_sampling` | Probabilistic programs; approximate inference by sampling |
| 9. Discussion | `discussion` | Bayesian networks vs classifiers; advantages |
| 10. Summary | `main` | Six-line summary; next time |

Notation in this file follows the lecture's: uppercase letters ($B$, $E$, $A$) are random variables and lowercase letters ($b$, $e$, $a$) their values; lowercase $p$ is a **local conditional probability**, defined directly; uppercase $P$ is a probability derived from the joint distribution by the laws of probability. All variables are binary, $\lbrace 0, 1 \rbrace$, unless stated. Conditional bars are written $\mid$.

---

## Section: Title (`main`)

**Lecture 12: Bayesian networks.**

Link: [Autumn 2023 lecture](https://stanford-cs221.github.io/autumn2023/modules/module.html#include=bayesian-networks%2Fdefinitions.js&mode=print6pp).

Order of the lecture: `model_based_motivation` → `review_probability` → `introduce_probabilistic_inference` → `introduce_alarm` → `introduce_medical_diagnosis` → `introduce_bayesian_networks` → `language_models` → `introduce_rejection_sampling` → `discussion` → summary.

---

## Section: Motivation (`model_based_motivation`)

Recall the ingredients of intelligence. Figure: `perceive-reason-act-learn.png` (reused from lecture 1 — an "Agent" box with "perceive" entering on the left, "act" leaving on the right, a "reason" loop over the top and a "learn" loop underneath).

**Where we started:** machine learning, percepts → actions (a predictor).

**Last few weeks: state-based models for reasoning.**

- Search problems: actions have deterministic outcomes.
- MDPs: actions have stochastic outcomes.
- Games: there exists an adversary (or an unknown opponent strategy).

**Model-free methods.** We just know what actions to take and how much utility it will produce. Examples: classification, regression, SARSA, Q-learning, TD learning.

**Model-based methods.** We understand how the world works. Examples: search, value iteration (MDPs), minimax (games). Figure: `game-tree.png` (reused from lecture 10 — "Key idea: game tree", the tree for game 1 with leaf utilities −50, 50, 1, 3, −5, 15).

Model-free methods are more direct and cheaper, but model-based methods are more flexible (you can change the reward function without changing the transitions).

**How should we represent the state of the world?**

---

## Section: Probability review (`review_probability`)

Represent the state of the world by a set of variables.

**Random variables** (representing attributes of the world):

- sunshine $S \in \lbrace 0, 1 \rbrace$
- rain $R \in \lbrace 0, 1 \rbrace$

Possible states of the world (**assignments** to random variables):

- $\lbrace S = 0, R = 0 \rbrace$: no sunshine and no rain
- $\lbrace S = 0, R = 1 \rbrace$: no sunshine and rain
- $\lbrace S = 1, R = 0 \rbrace$: sunshine and no rain
- $\lbrace S = 1, R = 1 \rbrace$: sunshine and rain

**Joint distribution** (probability of each assignment): $P(S = 0, R = 0) = 0.20$, $P(S = 0, R = 1) = 0.08$, $P(S = 1, R = 0) = 0.70$, $P(S = 1, R = 1) = 0.02$. In code, `P_SR = ProbTable("S R", [[0.20, 0.08], [0.70, 0.02]])`, displayed as:

| s | r | P(S=s, R=r) |
| - | - | ----------- |
| 0 | 0 | 0.2 |
| 0 | 1 | 0.08 |
| 1 | 0 | 0.7 |
| 1 | 1 | 0.02 |

**Marginalization.** Marginalizing out a variable $R$ means collapsing assignments that only differ in $R$:

- $P(S = 0) = P(S = 0, R = 0) + P(S = 0, R = 1) = 0.20 + 0.08 = 0.28$
- $P(S = 1) = P(S = 1, R = 0) + P(S = 1, R = 1) = 0.70 + 0.02 = 0.72$

Probability tables are just tensors, so we can use einops to express computations. Recall einsum: for all $s$, $P(S = s) = \sum_r P(S = s, R = r)$. In code, `P_S = ProbTable("S", einsum(P_SR.p, "s r -> s"))`, displayed as:

| s | P(S=s) |
| - | ------ |
| 0 | 0.28 |
| 1 | 0.72 |

**Conditioning.** Conditioning on $R = 1$ means selecting only assignments that satisfy the condition: $P(S = 0, R = 1) = 0.08$ and $P(S = 1, R = 1) = 0.02$. We then compute the probability of the evidence, $P(R = 1) = P(S = 0, R = 1) + P(S = 1, R = 1) = 0.08 + 0.02 = 0.1$, and divide by it:

- $P(S = 0 \mid R = 1) = P(S = 0, R = 1) / P(R = 1) = 0.08 / 0.1 = 0.8$
- $P(S = 1 \mid R = 1) = P(S = 1, R = 1) / P(R = 1) = 0.02 / 0.1 = 0.2$

We can also use einops to compute the conditional distribution, but there are a few steps:

```python
R1 = np.array([0, 1])  # Evidence [R = 1]
P_SR1 = ProbTable("S R=1", einsum(P_SR.p, R1, "s r, r -> s"))  # Filter to r = 1
P_R1 = ProbTable("R=1", einsum(P_SR1.p, "s ->"))
P_S_given_R1 = ProbTable("S | R=1", P_SR1.p / P_R1.p)
```

The evidence is encoded as an indicator vector `[0, 1]` (1 at the observed value), and the einsum multiplies it in and sums $r$ out. Traced outputs: `P_SR1` is $P(S = 0, R = 1) = 0.08$, $P(S = 1, R = 1) = 0.02$; `P_R1` is $P(R = 1) = 0.1$; `P_S_given_R1` is $P(S = 0 \mid R = 1) = 0.8$, $P(S = 1 \mid R = 1) = 0.2$ (printed as 0.7999999999999999 and 0.19999999999999998).

**Summary:**

- Joint distribution: source of truth.
- Marginalization: collapse assignments that differ only in marginalized variables.
- Conditioning: select via evidence, compute probability of evidence, then divide.
- Probability tables are tensors, so we can use einops to express computations!

---

## Section: Probabilistic inference (`introduce_probabilistic_inference`)

Random variables: $S \in \lbrace 0, 1 \rbrace$ (sunshine), $R \in \lbrace 0, 1 \rbrace$ (rain), $T \in \lbrace 0, 1 \rbrace$ (traffic), $A \in \lbrace 0, 1 \rbrace$ (autumn).

Joint distribution (source of truth): $P(S, R, T, A)$.

What is the probability of rain given that there's traffic and it's autumn?

**Probabilistic inference**: answer questions on the joint distribution. Analogy: executing SQL queries on a database.

A question includes:

- **Condition** on evidence: $[T = 1, A = 1]$ (traffic and autumn).
- Interested in **query**: $R$ (rain?).

In probability notation: $P(R \mid T = 1, A = 1)$.

Note: all variables not in evidence/query are marginalized out ($S$).

---

## Helper: class `ProbTable`

Not displayed as lecture text, but every table in the lecture is one. Its docstring: "Represents an arbitrary probability table: could be a local conditional distribution, a marginal distribution, or a conditional distribution." It is built from a description string such as `"A | B E"` (generated variables before the bar, conditioning variables after it; an item containing `=`, like `B=1`, is a fixed value rather than an axis) and either a tensor of probabilities or a function from an assignment to a probability (with an explicit `shape`). The tensor's axes are the conditioning variables followed by the generated variables. Its `asdict` method renders the tables shown in this file, one row per assignment, under a header such as `P(A=a | B=b, E=e)`. Note that the header always prints an uppercase $P$, including for local conditional distributions, which the lecture's notation writes as lowercase $p$.

---

## Section: Alarm (`introduce_alarm`)

🚨 **Problem: earthquakes, burglaries, and alarms.**

- Earthquakes and burglaries are independent events (probability $\epsilon = 0.05$).
- Either will cause an alarm to go off.
- Suppose you get an alarm.
- How does hearing that there's an earthquake change the probability of a burglary?

Joint distribution: $P(E, B, A)$. Which is larger?

- $P(B = 1 \mid A = 1)$: burglary given alarm only
- $P(B = 1 \mid A = 1, E = 1)$: burglary given alarm and earthquake

To solve this problem, let's use Bayesian networks. We need to do two things: construct a joint distribution, and perform probabilistic inference.

### Constructing the joint distribution

We could write down a huge table directly, but let's do it in a more intuitive way. There are 4 steps:

1. Define the variables ($B$, $E$, $A$).
2. Connect the variables with directed edges ($B \to A$, $E \to A$).
3. Write down local conditional probabilities for each variable (e.g., $p(e \mid b, a)$ as printed, though the network's actual local conditionals are $p(b)$, $p(e)$ and $p(a \mid b, e)$ in this example).
4. Define the joint distribution as the product of the local conditional probabilities.

**Step 1**: Define the variables ($B$, $E$, $A$).

**Step 2**: Connect the variables with directed edges suggesting direct influence ($B \to A$, $E \to A$). Figure: `alarm-bayes.png` — nodes $B$ and $E$ on top, each with an arrow into $A$ below (see Figures).

**Step 3**: Write down local conditional probabilities for each variable.

```python
epsilon = 0.05  # Probability of rare event
p_b = ProbTable("B", [1 - epsilon, epsilon])  # p(b)
p_e = ProbTable("E", [1 - epsilon, epsilon])  # p(e)
p_a_given_be = ProbTable("A | B E", lambda b, e, a: a == (b or e), shape=(2, 2, 2))  # p(a | b, e)
```

$p(b)$: $p(B = 0) = 0.95$, $p(B = 1) = 0.05$. $p(e)$: $p(E = 0) = 0.95$, $p(E = 1) = 0.05$. The alarm is deterministic, $A = B \lor E$:

| b | e | a | P(A=a \| B=b, E=e) |
| - | - | - | ------------------ |
| 0 | 0 | 0 | 1.0 |
| 0 | 0 | 1 | 0.0 |
| 0 | 1 | 0 | 0.0 |
| 0 | 1 | 1 | 1.0 |
| 1 | 0 | 0 | 0.0 |
| 1 | 0 | 1 | 1.0 |
| 1 | 1 | 0 | 0.0 |
| 1 | 1 | 1 | 1.0 |

**Step 4**: Define the joint distribution as the product of the local conditional probabilities. For all $b, e, a$:

$$P(B = b, E = e, A = a) = p(b) \thinspace p(e) \thinspace p(a \mid b, e)$$

In code, `P_BEA = ProbTable("B E A", einsum(p_b.p, p_e.p, p_a_given_be.p, "b, e, b e a -> b e a"))`, traced as:

| b | e | a | P(B=b, E=e, A=a) |
| - | - | - | ---------------- |
| 0 | 0 | 0 | 0.9025 |
| 0 | 0 | 1 | 0.0 |
| 0 | 1 | 0 | 0.0 |
| 0 | 1 | 1 | 0.0475 |
| 1 | 0 | 0 | 0.0 |
| 1 | 0 | 1 | 0.0475 |
| 1 | 1 | 0 | 0.0 |
| 1 | 1 | 1 | 0.0025 |

### Probabilistic inference

Now given the joint distribution, we can answer any query we want.

**$P(B = 1)$: burglary given no information.** `P_B = ProbTable("B", einsum(P_BEA.p, "b e a -> b"))` gives $P(B = 0) = 0.95$, $P(B = 1) = 0.05$. Note: the marginal $P(B = b)$ matches the local conditional $p(b)$ (low chance of burglary).

**$P(B = 1 \mid A = 1)$: burglary given alarm only.**

- First select based on the evidence ($A = 1$): `einsum(P_BEA.p, a1, "b e a, a -> b")` with `a1 = [0, 1]` gives $P(B = 0, A = 1) = 0.0475$, $P(B = 1, A = 1) = 0.05$.
- Compute probability of evidence: $P(A = 1) = 0.0975$.
- Divide by probability of evidence to get the conditional distribution: $P(B = 0 \mid A = 1) = 0.48717948717948717$, $P(B = 1 \mid A = 1) = 0.5128205128205129$.

Note: burglary is much more likely!

**$P(B = 1 \mid A = 1, E = 1)$: burglary given alarm and earthquake.**

- Select based on the evidence: `einsum(P_BEA.p, a1, e1, "b e a, a, e -> b")` gives $P(B = 0, A = 1, E = 1) = 0.0475$, $P(B = 1, A = 1, E = 1) = 0.0025$.
- Compute probability of evidence: $P(A = 1, E = 1) = 0.05$.
- Divide: $P(B = 0 \mid A = 1, E = 1) = 0.95$, $P(B = 1 \mid A = 1, E = 1) = 0.05$.

Note: burglary is again unlikely. Intuition: why did the alarm sound? The earthquake explains away the alarm.

**Key idea: explaining away** (figure `alarm-bayes.png` shown again):

- Suppose you have two causes ($B$, $E$) positively influencing an effect ($A$).
- Condition on the effect ($A = 1$).
- Further conditioning on one cause ($E = 1$) reduces the probability of the other cause ($B = 1$).
- In symbols: $P(B = 1 \mid A = 1, E = 1) \lt P(B = 1 \mid A = 1)$.
- Important: even if causes are independent!

**Reminder about notation:**

- Lowercase names ($e$): particular values.
- Uppercase names ($E$): random variable.
- Lowercase $p(e)$: local conditional probabilities (by definition).
- Uppercase $P(E)$: marginals/conditionals derived from the joint distribution (by laws of probability).
- Uppercase $P(E = e)$: probability (number) from a marginal/conditional distribution.

**Summary:**

- Define joint distribution: 4 steps (variables, edges, local conditional probabilities, joint distribution).
- Perform probabilistic inference: select based on evidence, compute probability of evidence, then divide.
- Explaining away: qualitative reasoning pattern that follows from the math.

---

## Section: Medical diagnosis (`introduce_medical_diagnosis`)

🩺 **Problem: medical diagnosis.** You are coughing and have itchy eyes. Do you have a cold? Let's model this problem using a Bayesian network.

**Step 1**: Define the random variables: cold $C \in \lbrace 0, 1 \rbrace$, allergies $A \in \lbrace 0, 1 \rbrace$, cough $H \in \lbrace 0, 1 \rbrace$, itchy eyes $I \in \lbrace 0, 1 \rbrace$.

**Step 2**: Connect the variables with directed edges. Figure: `medical-bayes.png` — $C \to H$, $A \to H$, $A \to I$ (see Figures).

**Step 3**: Write down local conditional probabilities for each variable.

```python
p_c = ProbTable("C", [0.9, 0.1])  # p(c)
p_a = ProbTable("A", [0.8, 0.2])  # p(a)
p_h_given_ca = ProbTable("H | C A", lambda c, a, h: 0.9 if h == (c or a) else 0.1, shape=(2, 2, 2))  # p(h | c, a)
p_i_given_a = ProbTable("I | A", lambda a, i: 0.9 if i == a else 0.1, shape=(2, 2))  # p(i | a)
```

So $p(C = 1) = 0.1$ and $p(A = 1) = 0.2$. You cough with probability 0.9 if you have a cold or allergies (or both) and 0.1 otherwise: $p(H = 1 \mid c, a) = 0.9$ when $c = 1$ or $a = 1$, and $p(H = 1 \mid C = 0, A = 0) = 0.1$. Itchy eyes follow allergies the same way: $p(I = 1 \mid A = 1) = 0.9$, $p(I = 1 \mid A = 0) = 0.1$. Traced tables:

| c | a | h | P(H=h \| C=c, A=a) |
| - | - | - | ------------------ |
| 0 | 0 | 0 | 0.9 |
| 0 | 0 | 1 | 0.1 |
| 0 | 1 | 0 | 0.1 |
| 0 | 1 | 1 | 0.9 |
| 1 | 0 | 0 | 0.1 |
| 1 | 0 | 1 | 0.9 |
| 1 | 1 | 0 | 0.1 |
| 1 | 1 | 1 | 0.9 |

| a | i | P(I=i \| A=a) |
| - | - | ------------- |
| 0 | 0 | 0.9 |
| 0 | 1 | 0.1 |
| 1 | 0 | 0.1 |
| 1 | 1 | 0.9 |

**Step 4**: Define the joint distribution as the product of the local conditional probabilities:

$$P(C = c, A = a, H = h, I = i) = p(c) \thinspace p(a) \thinspace p(h \mid c, a) \thinspace p(i \mid a)$$

In code, `P_CAHI = ProbTable("C A H I", einsum(p_c.p, p_a.p, p_h_given_ca.p, p_i_given_a.p, "c, a, c a h, a i -> c a h i"))`, traced as (floating-point tails trimmed):

| c | a | h | i | P(C=c, A=a, H=h, I=i) |
| - | - | - | - | --------------------- |
| 0 | 0 | 0 | 0 | 0.5832 |
| 0 | 0 | 0 | 1 | 0.0648 |
| 0 | 0 | 1 | 0 | 0.0648 |
| 0 | 0 | 1 | 1 | 0.0072 |
| 0 | 1 | 0 | 0 | 0.0018 |
| 0 | 1 | 0 | 1 | 0.0162 |
| 0 | 1 | 1 | 0 | 0.0162 |
| 0 | 1 | 1 | 1 | 0.1458 |
| 1 | 0 | 0 | 0 | 0.0072 |
| 1 | 0 | 0 | 1 | 0.0008 |
| 1 | 0 | 1 | 0 | 0.0648 |
| 1 | 0 | 1 | 1 | 0.0072 |
| 1 | 1 | 0 | 0 | 0.0002 |
| 1 | 1 | 0 | 1 | 0.0018 |
| 1 | 1 | 1 | 0 | 0.0018 |
| 1 | 1 | 1 | 1 | 0.0162 |

Now we can answer any question we want.

**$P(C = 1 \mid H = 1)$: cold given cough?**

- Select based on the evidence, marginalizing out non-query/evidence: `einsum(P_CAHI.p, h1, "c a h i, h -> c")` gives $P(C = 0, H = 1) = 0.234$, $P(C = 1, H = 1) = 0.09$.
- Compute probability of evidence: $P(H = 1) = 0.324$.
- Divide: $P(C = 0 \mid H = 1) = 0.7222$, $P(C = 1 \mid H = 1) = 0.2778$ (printed 0.7222222222222223 and 0.2777777777777778).

**$P(C = 1 \mid H = 1, I = 1)$: cold given cough and itchy eyes?**

- Select based on the evidence, marginalizing out non-query/evidence: `einsum(P_CAHI.p, h1, i1, "c a h i, h, i -> c")` gives $P(C = 0, H = 1, I = 1) = 0.153$, $P(C = 1, H = 1, I = 1) = 0.0234$.
- Compute probability of evidence: $P(H = 1, I = 1) = 0.1764$.
- Divide: $P(C = 0 \mid H = 1, I = 1) = 0.8673$, $P(C = 1 \mid H = 1, I = 1) = 0.1327$ (printed 0.8673469387755103 and 0.1326530612244898).

Note: the probability of cold is lower if you add itchy eyes! The code asserts `P_C_given_H1I1.p[1] < P_C_given_H1.p[1]`, i.e. $0.1327 \lt 0.2778$.

This is another, more subtle example of **explaining away** (figure `medical-bayes.png` shown again):

- Effect ($H$) has two causes ($C$, $A$).
- Itchy eyes ($I$) is not a cause of the effect ($H$)...
- But observing itchy eyes ($I = 1$) increases the probability of $A$, which is a cause!

**Summary:**

- Same recipe (joint distribution + probabilistic inference).
- Explaining away shows up in more complex ways.
- In general, observing evidence increases/decreases the probability of other nodes, propagating through the network.

---

## Section: Bayesian networks in general (`introduce_bayesian_networks`)

We have seen two examples of Bayesian networks: Alarm (figure `alarm-bayes.png`, small) and Medical diagnosis (figure `medical-bayes.png`, small).

Now let's introduce the general case.

1. Define a set of random variables $X = (X_1, \ldots, X_n)$.
2. Define any directed acyclic graph (DAG) over the variables.
3. For each node $X_i$, define a local conditional distribution $p(x_i \mid \text{parents}(x_i))$.
4. Define the joint distribution:

$$P(X_1, \ldots, X_n) = \prod_i p(x_i \mid \text{parents}(x_i))$$

**Reminders:**

- One local conditional distribution per node (not edge!).
- Local conditional distribution depends on *all* parents at once.
- Difference between $p$ and $P$.

**Probabilistic inference.** Given:

- Bayesian network $P(X_1, \ldots, X_n)$.
- Evidence $E = e$ where $E \subseteq X$. Example: $E = (X_3, X_7)$, $e = (0, 1)$.
- Query $Q \subseteq X$. Example: $Q = (X_2, X_4)$ (the source's closing parenthesis is missing).

Output: $P(Q \mid E = e)$.

---

## Section: Language models (`language_models`)

"Lots of things are secretely [sic] Bayesian networks." For example, autoregressive language models:

1. Random variables: tokens $X_1, \ldots, X_T$.
2. Edges from all previous tokens to current token ($X_1, \ldots, X_{t-1}$ to $X_t$).
3. Local conditional distribution (Transformer): $p(x_t \mid x_1, \ldots, x_{t-1})$.
4. Joint distribution:

$$P(X_1, \ldots, X_T) = \prod_t p(x_t \mid x_1, \ldots, x_{t-1})$$

Typically, you just sample forward using a language model (prompt → response): $X_1, X_2, X_3 \to X_4, X_5, X_6$.

What would doing probabilistic inference mean? $X_4, X_5, X_6 \to X_1, X_2, X_3$.

Application: jailbreaking language models. Link: [Eliciting Language Model Behaviors with Investigator Agents](https://arxiv.org/abs/2502.01236) (Xiang Lisa Li, Neil Chowdhury, Daniel D. Johnson, Tatsunori Hashimoto, Percy Liang, Sarah Schwettmann, Jacob Steinhardt; arXiv, 2025-02-03). Given a particular response (e.g., "Sure, here's how you make a bomb"), find prompts that likely generated it.

---

## Section: Rejection sampling (`introduce_rejection_sampling`)

So far we have defined Bayesian networks via writing down local conditional distributions. Now we will define them via **probabilistic programs**. (`set_random_seed(3)` makes the traced samples reproducible.)

```python
def Bernoulli(prob: float) -> int:
    """Return 1 with probability `prob` and 0 with probability `1 - prob`."""
    return np.random.choice([0, 1], p=[1 - prob, prob])

def alarm():
    B = Bernoulli(0.05)  # Burglary
    E = Bernoulli(0.05)  # Earthquake
    A = B or E           # Alarm
    return {"B": B, "E": E, "A": A}
```

Let's revisit our old alarm example. The lecture calls `alarm()` ten times; in the trace every one of the ten samples is `{"B": 0, "E": 0, "A": 0}`.

Now let's do probabilistic inference. Example: $P(B \mid A = 1)$. Writing down a distribution in a program gives us a natural way to approximately do probabilistic inference: **rejection sampling**!

**Key idea:**

- Draw a ton of samples.
- Select the samples that match the evidence.
- Record the query.

"Note: we generalize evidence (e.g., A = 1) and query (B) to" — the sentence ends there in the source; the code that follows makes both of them functions of a sample:

```python
query = lambda sample: sample["B"]
evidence = lambda sample: sample["A"] == 1

def rejection_sampling(program: Callable, query: Callable, evidence: Callable, num_samples: int) -> float:
    """Perform rejection sampling given:
    - program: defines the Bayesian network, returns a sample
    - query: sample -> value of interest
    - evidence: sample -> whether to keep the sample
    - num_samples: number of samples to draw
    """
    counts = defaultdict(int)  # Record how many of each query value we got
    for _ in range(num_samples):
        sample = program()
        if evidence(sample):
            counts[query(sample)] += 1
    # Normalize counts to obtain probabilities
    total_count = sum(counts.values())
    probs = {q: counts[q] / total_count for q in counts}
    return probs
```

Traced results for the alarm, $P(B \mid A = 1)$:

- `num_samples=10`: the trace steps through all ten draws. Nine are $\lbrace B = 0, E = 0, A = 0 \rbrace$ and are rejected; the sixth is $\lbrace B = 1, E = 0, A = 1 \rbrace$ and is kept. So `total_count` is 1 and the result is $\lbrace 1: 1.0 \rbrace$, i.e. an estimate of $P(B = 1 \mid A = 1) = 1$ from a single accepted sample.
- `num_samples=1000`: $\lbrace 1: 0.5686274509803921, 0: 0.43137254901960786 \rbrace$. These are 29/51 and 22/51, so 51 of the 1000 samples were accepted. (The exact answer from the alarm section is $0.5128$.)

Downside: if evidence is rare, this is very inefficient. But as the number of samples increases to infinity, this converges to the true probability!

**Medical diagnosis.** Example: cold given cough? $P(C \mid H = 1)$.

```python
def medical_diagnosis():
    C = Bernoulli(0.1)                      # Cold
    A = Bernoulli(0.2)                      # Allergies
    H = Bernoulli(0.9 if C or A else 0.1)   # Cough
    I = Bernoulli(0.9 if A else 0.1)        # Itchy eyes
    return {"C": C, "A": A, "H": H, "I": I}
```

One traced sample: $\lbrace C = 0, A = 0, H = 0, I = 0 \rbrace$. With query `sample["C"]`, evidence `sample["H"] == 1` and `num_samples=200`, the result is $\lbrace 0: 0.7313432835820896, 1: 0.26865671641791045 \rbrace$, which is 49/67 and 18/67, so 67 of 200 samples were accepted. (The exact answer from the medical diagnosis section is $P(C = 1 \mid H = 1) = 0.2778$.)

**Hidden Markov models for object tracking.** Figure: `hmm.png` — a chain of hidden positions $H_1 \to \cdots \to H_5$, each with an arrow down to an observed, shaded sensor reading $E_t$ (see Figures).

Example: where is the object at time 3 given the sensor reading at time 5? $P(H_3 \mid E_5 = 2)$.

```python
def hidden_markov_model():
    """
    This is a simple hidden Markov model.
    The hidden variables are the positions of the objects (H_1, ..., H_T).
    The observed variables are the sensor readings (O_1, ..., O_T).
    """
    num_steps = 5
    H = [None] * num_steps  # Positions
    E = [None] * num_steps  # Sensor readings
    for t in range(num_steps):
        H[t] = (H[t - 1] if t > 0 else 0) + Bernoulli(0.5)
        E[t] = H[t] + Bernoulli(0.5)
    return {"H": H, "E": E}
```

The object starts at position 0 and at each step moves forward by 1 with probability 0.5; the sensor reports the position plus 0 or 1, again with probability 0.5 each. The docstring calls the readings $O_1, \ldots, O_T$, but the code and the figure call them $E$. Python lists are 0-indexed, so the query is `sample["H"][2]` ($H_3$) and the evidence `sample["E"][4] == 2` ($E_5 = 2$). With `num_samples=200`, the traced result is $\lbrace 1: 0.6981132075471698, 2: 0.07547169811320754, 0: 0.22641509433962265 \rbrace$, i.e. $P(H_3 = 0 \mid E_5 = 2) \approx 0.226$, $P(H_3 = 1 \mid E_5 = 2) \approx 0.698$, $P(H_3 = 2 \mid E_5 = 2) \approx 0.075$. These are 12/53, 37/53 and 4/53, so 53 of 200 samples were accepted. The lecture gives no exact value to compare against.

**Summary:**

- Probabilistic programming: programs as representations of joint distributions.
- Rejection sampling: approximate inference by drawing samples (very flexible and very slow).

---

## Section: Discussion (`discussion`)

If you're used to thinking about classifiers, using Bayesian networks requires a shift in mindset. Figure: `medical-bayes.png`.

Example: predict cold given cough?

- Traditional machine learning: input → output.
- Bayesian networks: output, hidden → input.

**Advantages of Bayesian networks:**

- Handle **heterogenously** [sic] missing information, both at training and test time.
- Incorporate **prior knowledge** (e.g., Mendelian inheritance, laws of physics).
- Can **interpret** all the intermediate variables.
- Precursor to **causal** models (can do interventions and counterfactuals).

---

## Section: Summary (`main`)

- Probability: joint distributions, marginalization, conditioning.
- Probability tables are tensors, can use einsum to express computations.
- Bayesian networks: directed acyclic graphs of random variables.
- Probabilistic inference: ask questions $P(\text{query} \mid \text{evidence})$.
- Probabilistic programming: programs as representations of joint distributions.
- Rejection sampling: approximate inference by drawing samples.

Next time: better probabilistic inference!

---

## Figures (vision pass)

Vision pass on 2026-10-24 over the images at commit f22938e, `images/`, done by the parent model directly (each image is small, about 1k tokens). Ground-truth cross-checks: the edges in `alarm-bayes.png` match the code's $p(a \mid b, e)$ and step 2's $B \to A$, $E \to A$; the edges in `medical-bayes.png` match the factors $p(c) \thinspace p(a) \thinspace p(h \mid c, a) \thinspace p(i \mid a)$ of the traced einsum `"c, a, c a h, a i -> c a h i"`; the five time steps in `hmm.png` match `num_steps = 5`. `perceive-reason-act-learn.png` and `game-tree.png` are unchanged since lecture 10's pinned commit and are not re-described.

### `perceive-reason-act-learn.png` (reused)

The agent diagram from lecture 1: perceive, reason, act, learn. Described in raw/slides/01-course-overview.md.

### `game-tree.png` (reused)

"Key idea: game tree" with the tree for game 1 from lecture 10. Described in the Figures section of raw/slides/10-games-i.md.

### `alarm-bayes.png`

A three-node directed graph drawn as black circles with italic serif capital letters, on a white background (about 414 × 305 px). Node $B$ is at the upper left and node $E$ at the upper right; node $A$ sits below and between them. One arrow runs from $B$ down to $A$ and one from $E$ down to $A$. There is no edge between $B$ and $E$, and no labels, numbers or probabilities on the figure.

### `medical-bayes.png`

A four-node directed graph in the same style (about 565 × 439 px), in a 2 × 2 layout: $C$ top left, $A$ top right, $H$ bottom left, $I$ bottom right. Three arrows: $C \to H$ (straight down), $A \to H$ (diagonal, from upper right to lower left) and $A \to I$ (straight down). There is no edge between $C$ and $A$, none from $C$ to $I$, and none between $H$ and $I$. No labels or numbers.

### `hmm.png`

A two-row chain in the same style (about 871 × 302 px). The top row is five white circles $H_1, H_2, H_3, H_4, H_5$, left to right, joined by horizontal arrows $H_1 \to H_2 \to H_3 \to H_4 \to H_5$. The bottom row is five circles $E_1, \ldots, E_5$ filled grey, each directly under its $H_t$ with a vertical arrow $H_t \to E_t$. The grey shading marks the $E_t$ as observed variables; the white $H_t$ are hidden. No other edges and no numbers.
