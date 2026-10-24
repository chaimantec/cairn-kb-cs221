---
title: Lecture 14 — Bayesian Networks and Learning (Slides)
lecture: 14
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_commit: 6ba9ac8 (2025-11-05, "add Bayes learning")
source_files:
  - https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/6ba9ac8/bayes_learning.py
rendered:
  - https://stanford-cs221.github.io/autumn2025-lectures/?trace=bayes_learning
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. Lecture 14 was delivered as
  one program, `bayes_learning.py`, which imports `ProbTable` from lecture 12's
  `bayes.py` and `normalize_dict` from `util.py`. There are no slide numbers;
  sections correspond to function calls in the source. The file is transcribed here
  AS OF COMMIT 6ba9ac8 ("add Bayes learning"), which landed at 1:25pm PST on Wed Nov 5
  2025, five minutes before the lecture, and whose README diff adds "Wed Nov 5:
  learning (lecture 14)". The next commit to the file (0cd7d5b, "update Bayes",
  6:32pm PST on Sun Nov 9, four days after the lecture) renamed functions and fixed a
  typo, listed in the "Changes after the lecture" section at the end; the file was
  later rewritten in 2026 for a later offering, so the repo's `main` branch and the
  live rendered UI no longer show what was taught; the pinned URL above does. All demo
  outputs were read on 2026-10-24 from the execution trace committed at the same
  commit (`var/traces/bayes_learning.json`), whose embedded source is byte-identical
  to the pinned file. Seven figures are shown: `alarm-bayes.png`, byte-identical to
  lecture 12's and described there, and six new images added in the same commit,
  described in the "Figures" section at the end of this file.
---

# Lecture 14 Slides — Bayesian Networks and Learning

Lecture 14 closes the Bayesian networks module by asking where the local conditional probabilities come from. It reviews the four-step definition of a Bayesian network, the three inference algorithms (exact inference, rejection sampling, Gibbs sampling) and the rule for reading conditional independence off the graph. Then it learns the parameters from data. In the **fully observed** setting every training example assigns every variable, and the learning algorithm is **count and normalize**, worked through on a single movie-rating variable, a genre → rating pair, a v-structure, an inverted v-structure, **parameter sharing** between two users' ratings, and a hidden Markov model; a general `supervised_learning` function does it for any network. Count and normalize is then shown to be the closed-form solution of **maximum likelihood** estimation. **Laplace smoothing** adds a pseudocount $\lambda$ to every count so that values unseen in training do not get probability zero. Finally, the **partially observed** setting, where the genre is never observed, is handled by **expectation maximization (EM)**, which alternates guessing a distribution over the hidden variable (E-step) with count and normalize on the resulting weighted data (M-step).

| Part | Function(s) | Content |
| ---- | ----------- | ------- |
| 1. Title | `main` | Lecture title |
| 2. Review | `review_bayesian_networks` | Four-step definition; inference algorithms; conditional independence; whiteboard game; "where do all these probabilities come from?" |
| 3. Fully observed setting | `introduce_fully_observed_setting` | The learning task |
| 4. One variable | `one_variable` | Movie rating $R$: count + normalize |
| 5. Two variables | `two_variables` | Genre $G \to$ rating $R$ |
| 6. V-structure | `v_structure` | $G \to R \leftarrow A$ (award) |
| 7. Inverted v-structure | `inverted_v_structure` | $R_1 \leftarrow G \to R_2$ with separate $p_{R1}$, $p_{R2}$ |
| 8. Parameter sharing | `parameter_sharing` | The same network with a single shared $p_R$ |
| 9. Hidden Markov model | `hidden_markov_model` | $p_{\text{start}}$, $p_{\text{trans}}$, $p_{\text{emit}}$ |
| 10. General Bayesian networks | `general_bayesian_networks`, `supervised_learning` | Parameter types $D$; count + normalize for any network |
| 11. Maximum likelihood | `maximum_likelihood`, `maximum_likelihood_one_variable`, `maximum_likelihood_two_variables` | Why count + normalize is the maximum likelihood estimate |
| 12. Laplace smoothing | `introduce_laplace_smoothing` | Pseudocounts $\lambda$; $\lambda \to 0$, $\lambda \to \infty$, more data |
| 13. Expectation maximization | `introduce_expectation_maximization`, `expectation_maximization` | Partially observed data; E-step and M-step; two runs |
| 14. Summary | `main` | Four-line summary; next time |

Notation follows the lecture's. Uppercase letters ($R$, $G$, $A$, $H_t$, $E_t$) are random variables and lowercase letters their values. A subscripted lowercase $p$ such as $p_R$ or $p_{\text{trans}}$ is a **local conditional distribution**, one of the network's parameters; uppercase $P$ is a probability under the joint distribution. $\theta$ collects all the parameters. "drama" and "comedy" are the two genres; in the figure tables they are abbreviated d and c. `normalize_dict` (from `util.py`) divides each value of a dictionary by the sum of its values.

---

## Section: Title (`main`)

**Lecture 14: learning parameters of Bayesian networks**

Order of the lecture: `review_bayesian_networks` → `introduce_fully_observed_setting` (which runs `one_variable`, `two_variables`, `v_structure`, `inverted_v_structure`, `parameter_sharing`, `hidden_markov_model`, `general_bayesian_networks`, then `maximum_likelihood`) → `introduce_laplace_smoothing` → `introduce_expectation_maximization` → summary.

---

## Section: Review (`review_bayesian_networks`)

**Joint distribution**: like a database that specifies how the world works.

1\. Define a set of random variables: $X = (X_1, \ldots, X_n)$. Example: B(urglary), E(arthquake), A(larm).

2\. Define a directed acyclic graph (DAG) over the variables. Figure: `alarm-bayes.png` (from lecture 12 — nodes $B$ and $E$ at the top, each with an arrow down to $A$; described in raw/slides/12-bayesian-networks-i.md).

3\. For each node, define a local conditional distribution: $p(x_i \mid \text{parents}(x_i))$. Example: $p(b)$, $p(e)$, $p(a \mid b, e)$.

```python
p_b = ProbTable("B", [0.95, 0.05]) # p(b)
p_e = ProbTable("E", [0.95, 0.05]) # p(e)
p_a_given_be = ProbTable("A | B E", lambda b, e, a: a == (b or e), shape=(2, 2, 2)) # p(a | b, e)
```

Traced tables: $p(B = 0) = 0.95$, $p(B = 1) = 0.05$; $p(E = 0) = 0.95$, $p(E = 1) = 0.05$; and $p(a \mid b, e)$ is 1 exactly when $a = (b \text{ or } e)$:

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

4\. Define the joint distribution: $P(X_1, \ldots, X_n) = \prod_i p(x_i \mid \text{parents}(x_i))$. Example: $P(B = b, E = e, A = a) = p(b) \thinspace p(e) \thinspace p(a \mid b, e)$. In code, `P_BEA = ProbTable("B E A", einsum(p_b.p, p_e.p, p_a_given_be.p, "b, e, b e a -> b e a"))`, traced as:

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

**Probabilistic inference**: answer questions (like SQL) on the joint distribution. Example: $P(B \mid A = 1)$.

1. Exact inference: select $A = 1$, marginalize out $E$, divide by $P(A = 1)$.
2. Rejection sampling: sample $(B, E, A)$ and reject unless $A = 1$.
3. Gibbs sampling: alternate sampling $B \mid E \thinspace A$ and $E \mid B \thinspace A$.

**Conditional independence**

Question: are $A$ and $B$ conditionally independent given $C$?

Yes iff every path from $A$ to $B$ is blocked (by nodes in $C$). A path is blocked if it contains one of the following patterns:

1. $X \to C \to Y$
2. $X \leftarrow C \to Y$
3. $X \to Z \leftarrow Y$ unless $C = Z$ or a descendent of $Z$ (remember explaining away!)

Contrasting pairs:

1. $A \leftarrow C \to B$: $A$ and $B$ are independent given $C$ but not marginally independent.
2. $A \to C \leftarrow B$: $A$ and $B$ are marginally independent but not independent given $C$.

Let's play a game on the whiteboard, building up a Bayesian network involving $A$ and $B$.

(The game itself was played on the whiteboard and is not in the source; see the transcript from about 6:39 and the wiki page.)

This class: where do all these probabilities come from?

---

## Section: Fully observed setting (`introduce_fully_observed_setting`)

Link: [Autumn 2023 lecture] — <https://stanford-cs221.github.io/autumn2023/modules/module.html#include=bayesian-networks%2Fsupervised-learning.js&level=0&mode=print6pp>

Learning task:

- Input: a set of examples (assignments to all variables)
- Output: local conditional distributions

Let's first walk through some examples intuitively...

(The examples follow as their own sections; the function closes with "...and then explain the underlying principle." and calls `maximum_likelihood`.)

---

## Section: One variable (`one_variable`)

Variables:

- $R \in \lbrace 1, 2, 3, 4, 5 \rbrace$: rating of a movie

Figure: `pgm-r.png` — a single circled node $R$.

$P(R = r) = p_R(r)$

Parameters: $\theta = (p_R(1), p_R(2), p_R(3), p_R(4), p_R(5))$

Training data:

```python
training_data = [{"R": r} for r in [1, 3, 4, 4, 4, 4, 4, 5, 5, 5]]
```

Intuition: $p_R(r)$ = fraction of occurrences of $r$ in training data.

1\. **Count** the occurrences of each rating:

```python
counts_r = defaultdict(float)
for x in training_data:
    counts_r[x["R"]] += 1
```

2\. **Normalize** to get probabilities:

```python
p_r = normalize_dict(counts_r)
```

Traced: `counts_r` ends as $\lbrace 1: 1, 3: 1, 4: 5, 5: 3 \rbrace$ (ten examples), and `p_r` is $\lbrace 1: 0.1, 3: 0.1, 4: 0.5, 5: 0.3 \rbrace$. Rating 2 never occurs, so it has no entry at all (an implicit probability of zero).

It's a simple algorithm: count + normalize!

---

## Section: Two variables (`two_variables`)

Variables:

- $R \in \lbrace 1, 2, 3, 4, 5 \rbrace$: rating of a movie
- $G \in \lbrace \text{drama}, \text{comedy} \rbrace$: genre of a movie

Figure: `pgm-g-r.png` — node $G$ with an arrow to node $R$.

$P(G = g, R = r) = p_G(g) \thinspace p_R(r \mid g)$

Parameters: $\theta = (p_G, p_R)$

Training data:

```python
training_data = [
    {"G": "drama", "R": 4},
    {"G": "drama", "R": 4},
    {"G": "drama", "R": 5},
    {"G": "comedy", "R": 1},
    {"G": "comedy", "R": 5},
]
```

1\. **Count** the local assignments:

```python
counts_g = defaultdict(float)
counts_gr = defaultdict(lambda: defaultdict(float))
for x in training_data:
    counts_g[x["G"]] += 1
    counts_gr[x["G"]][x["R"]] += 1
```

2\. **Normalize** to get probabilities:

```python
p_g = normalize_dict(counts_g)
p_r_given_g = {}
for g in counts_g:
    p_r_given_g[g] = normalize_dict(counts_gr[g])
```

Traced: `counts_g` = $\lbrace \text{drama}: 3, \text{comedy}: 2 \rbrace$; `counts_gr` = drama $\lbrace 4: 2, 5: 1 \rbrace$, comedy $\lbrace 1: 1, 5: 1 \rbrace$. So `p_g` = $\lbrace \text{drama}: 0.6, \text{comedy}: 0.4 \rbrace$ and `p_r_given_g` = drama $\lbrace 4: 0.6667, 5: 0.3333 \rbrace$, comedy $\lbrace 1: 0.5, 5: 0.5 \rbrace$.

Notes:

- To estimate a local conditional distribution, just ignore the other variables!
- Count + normalize!

---

## Section: V-structure (`v_structure`)

Variables:

- $G \in \lbrace \text{drama}, \text{comedy} \rbrace$: genre of a movie
- $A \in \lbrace 0, 1 \rbrace$: did the movie win an award?
- $R \in \lbrace 1, 2, 3, 4, 5 \rbrace$: rating of a movie

Figure: `pgm-g-a-r.png` — nodes $G$ and $A$ side by side at the top, each with an arrow down to $R$.

$P(G = g, A = a, R = r) = p_G(g) \thinspace p_A(a) \thinspace p_R(r \mid g, a)$

Parameters: $\theta = (p_G, p_A, p_R)$

Training data:

```python
training_data = [
    {"G": "drama", "A": 0, "R": 3},
    {"G": "drama", "A": 1, "R": 5},
    {"G": "drama", "A": 0, "R": 1},
    {"G": "comedy", "A": 0, "R": 5},
    {"G": "comedy", "A": 1, "R": 4},
]
```

1\. **Count** the local assignments:

```python
counts_g = defaultdict(float)
counts_a = defaultdict(float)
counts_gar = defaultdict(lambda: defaultdict(float))
for x in training_data:
    counts_g[x["G"]] += 1
    counts_a[x["A"]] += 1
    counts_gar[(x["G"], x["A"])][x["R"]] += 1
```

2\. **Normalize** to get probabilities:

```python
p_g = normalize_dict(counts_g)
p_a = normalize_dict(counts_a)
p_r_given_ga = {}
for ga in counts_gar:
    p_r_given_ga[ga] = normalize_dict(counts_gar[ga])
```

Traced: `p_g` = $\lbrace \text{drama}: 0.6, \text{comedy}: 0.4 \rbrace$; `p_a` = $\lbrace 0: 0.6, 1: 0.4 \rbrace$; and `p_r_given_ga`, keyed by the pair (genre, award):

| (g, a) | distribution over r |
| ------ | ------------------- |
| (drama, 0) | 3: 0.5, 1: 0.5 |
| (drama, 1) | 5: 1.0 |
| (comedy, 0) | 5: 1.0 |
| (comedy, 1) | 4: 1.0 |

There's nothing special about v-structures here. Remember to condition on all parents simultaneously.

---

## Section: Inverted v-structure (`inverted_v_structure`)

Variables:

- $G \in \lbrace \text{drama}, \text{comedy} \rbrace$: genre of a movie
- $R1 \in \lbrace 1, 2, 3, 4, 5 \rbrace$: user 1's rating of a movie
- $R2 \in \lbrace 1, 2, 3, 4, 5 \rbrace$: user 2's rating of a movie

Figure: `pgm-g-r1-r2.png` — node $G$ at the top with arrows down to $R_1$ (left) and $R_2$ (right).

$P(G = g, R_1 = r_1, R_2 = r_2) = p_G(g) \thinspace p_{R1}(r_1 \mid g) \thinspace p_{R2}(r_2 \mid g)$

Parameters: $\theta = (p_G, p_{R1}, p_{R2})$

Training data:

```python
training_data = [
    {"G": "drama", "R1": 4, "R2": 5},
    {"G": "drama", "R1": 4, "R2": 4},
    {"G": "drama", "R1": 5, "R2": 3},
    {"G": "comedy", "R1": 1, "R2": 2},
    {"G": "comedy", "R1": 5, "R2": 4},
]
```

1\. **Count** the local assignments:

```python
counts_g = defaultdict(float)
counts_gr1 = defaultdict(lambda: defaultdict(float))
counts_gr2 = defaultdict(lambda: defaultdict(float))
for x in training_data:
    counts_g[x["G"]] += 1
    counts_gr1[x["G"]][x["R1"]] += 1
    counts_gr2[x["G"]][x["R2"]] += 1
```

2\. **Normalize** to get probabilities:

```python
p_g = normalize_dict(counts_g)
p_r1_given_g = {}
for g in counts_g:
    p_r1_given_g[g] = normalize_dict(counts_gr1[g])
p_r2_given_g = {}
for g in counts_g:
    p_r2_given_g[g] = normalize_dict(counts_gr2[g])
```

Traced: `p_g` = $\lbrace \text{drama}: 0.6, \text{comedy}: 0.4 \rbrace$; `p_r1_given_g` = drama $\lbrace 4: 0.6667, 5: 0.3333 \rbrace$, comedy $\lbrace 1: 0.5, 5: 0.5 \rbrace$; `p_r2_given_g` = drama $\lbrace 5: 0.3333, 4: 0.3333, 3: 0.3333 \rbrace$, comedy $\lbrace 2: 0.5, 4: 0.5 \rbrace$.

---

## Section: Parameter sharing (`parameter_sharing`)

Variables: the same three, $G$, $R1$, $R2$. Figure: `pgm-g-r1-r2.png` again.

$P(G = g, R_1 = r_1, R_2 = r_2) = p_G(g) \thinspace p_R(r_1 \mid g) \thinspace p_R(r_2 \mid g)$

Parameters: $\theta = (p_G, p_R)$

Note that this is the same example but now we have a single $p_R$ that is used for both $R1$ and $R2$.

Key idea: **parameter sharing**

Figure: `parameter-sharing.png` — the network $G \to R_1$, $G \to R_2$ drawn in the middle, flanked by two parameter tables, with red dashed arrows from each table to the node(s) it powers. The left table, $p_G(g)$, has rows c 2/5 and d 3/5, and one red arrow to $G$. The right table, $p_R(r \mid g)$, has rows d 3 1/6, d 4 3/6, d 5 2/6, c 1 1/4, c 2 1/4, c 4 1/4, c 5 1/4, and **two** red arrows, one to $R_1$ and one to $R_2$: the single shared table supplies both nodes. (c = comedy, d = drama. These are exactly the traced values below.)

Intuition: each node is powered by some local conditional distribution.

Training data: the same five examples as the inverted v-structure.

1\. **Count** the local assignments:

```python
counts_g = defaultdict(float)
counts_gr = defaultdict(lambda: defaultdict(float))
for x in training_data:
    counts_g[x["G"]] += 1
    # Note: increment the same counts_gr for both R1 and R2!
    counts_gr[x["G"]][x["R1"]] += 1
    counts_gr[x["G"]][x["R2"]] += 1
```

2\. **Normalize** to get probabilities:

```python
p_g = normalize_dict(counts_g)
p_r_given_g = {}
for g in counts_g:
    p_r_given_g[g] = normalize_dict(counts_gr[g])
```

Traced: `p_g` = $\lbrace \text{drama}: 0.6, \text{comedy}: 0.4 \rbrace$; `p_r_given_g` = drama $\lbrace 4: 0.5, 5: 0.3333, 3: 0.1667 \rbrace$ (counts 3, 2, 1 out of 6 ratings), comedy $\lbrace 1: 0.25, 2: 0.25, 5: 0.25, 4: 0.25 \rbrace$ (one each out of 4).

In probabilistic inference, we're only **reading** from the local conditional distributions ...so we don't care whether $p(r_1 \mid g)$ and $p(r_2 \mid g)$ are the same distribution or not.

But when we're learning the parameters, we are **writing** to the local conditional distributions ...so it matters if $p(r_1 \mid g)$ and $p(r_2 \mid g)$ are the same distribution or not.

When do you parameter sharing? [sic]

- Having fewer parameters requires fewer examples to learn
- Having more parameters provides greater flexibility
- Which one you choose is a modeling decision (are the two users similar?)

---

## Section: Hidden Markov model (`hidden_markov_model`)

Variables:

- $H_t \in \lbrace 0, 1 \rbrace$: position of the object at time $t$
- $E_t \in \lbrace 0, 1 \rbrace$: sensor reading at time $t$

Figure: `pgm-hmm.png` — a chain $H_1 \to H_2 \to H_3$ along the top (with a stub of an arrow continuing right from $H_3$, cut off at the image edge), and an arrow from each $H_t$ straight down to $E_t$.

$P(H = h, E = e) =$

$p_{\text{start}}(h_1) \thinspace p_E(e_1 \mid h_1) \thinspace p_{\text{trans}}(h_2 \mid h_1) \thinspace p_{\text{emit}}(e_2 \mid h_2) \thinspace p_{\text{trans}}(h_3 \mid h_2) \thinspace p_{\text{emit}}(e_3 \mid h_3)$

(The second factor is printed $p_E$ [sic]; it is $p_{\text{emit}}$, as the counting code below and the parameter list show, and the post-lecture commit corrected it.)

Parameters: $\theta = (p_{\text{start}}, p_{\text{trans}}, p_{\text{emit}})$

Training data:

```python
training_data = [
    {"H1": 0, "E1": 0, "H2": 1, "E2": 1, "H3": 0, "E3": 0},
    {"H1": 0, "E1": 1, "H2": 0, "E2": 1, "H3": 0, "E3": 1},
]
```

1\. **Count** the local assignments:

```python
counts_start = defaultdict(float)
counts_trans = defaultdict(lambda: defaultdict(float))
counts_emit = defaultdict(lambda: defaultdict(float))
for x in training_data:
    counts_start[x["H1"]] += 1
    counts_emit[x["H1"]][x["E1"]] += 1
    counts_trans[x["H1"]][x["H2"]] += 1
    counts_emit[x["H2"]][x["E2"]] += 1
    counts_trans[x["H2"]][x["H3"]] += 1
    counts_emit[x["H3"]][x["E3"]] += 1
```

2\. **Normalize** to get probabilities:

```python
p_start = normalize_dict(counts_start)
p_trans = {}
for h in counts_trans:
    p_trans[h] = normalize_dict(counts_trans[h])
p_emit = {}
for h in counts_emit:
    p_emit[h] = normalize_dict(counts_emit[h])
```

Traced: `p_start` = $\lbrace 0: 1.0 \rbrace$ (both sequences start at 0); `p_trans` = from 0 $\lbrace 1: 0.3333, 0: 0.6667 \rbrace$, from 1 $\lbrace 0: 1.0 \rbrace$; `p_emit` = in state 0 $\lbrace 0: 0.4, 1: 0.6 \rbrace$, in state 1 $\lbrace 1: 1.0 \rbrace$. The two transitions per sequence are counted into the one `counts_trans`, and the three emissions into the one `counts_emit`: parameter sharing across time.

---

## Section: General Bayesian networks (`general_bayesian_networks`, `supervised_learning`)

Variables $(X_1, \ldots, X_n)$

**Parameters**

Let $D$ be the set of types of local conditional distributions.

- Example: $D = \lbrace \text{start}, \text{trans}, \text{emit} \rbrace$ for the HMM

Parameters $\theta = \lbrace p_d : d \in D \rbrace$

- Example: $\theta = \lbrace p_{\text{start}}, p_{\text{trans}}, p_{\text{emit}} \rbrace$ for the HMM

**Joint distribution**

Each $X_i$ is generated from $p_{d_i}$.

$P(X_1 = x_1, \ldots, X_n = x_n) = \prod_i p_{d_i}(X_i \mid x_{\text{parents}(X_i)})$

(Printed with an uppercase $X_i$ as the first argument.)

Parameter sharing: two $X_i$ might have the same $d_i$.

Training data: the same two HMM sequences. Figure: `pgm-hmm.png` again.

```python
network_structure = {
    # variable name -> (parameter name, parent variable names)
    "H1": ("start", []),
    "H2": ("trans", ["H1"]),
    "H3": ("trans", ["H2"]),
    "E1": ("emit", ["H1"]),
    "E2": ("emit", ["H2"]),
    "E3": ("emit", ["H3"]),
}

# Learn the parameters
theta = supervised_learning(network_structure, training_data)
```

```python
def supervised_learning(network_structure, training_data, pseudocounts=None):
    """Perform supervised learning on a Bayesian network.
    Args:
        network_structure: variable name -> (parameter name, parent variable names)
          Example: {"H1": ("p_start", []), "H2": ("p_trans", ["H1"]), ...}
        data: a list of dictionaries of variable assignments
          Example: [{"H1": 0, "E1": 0}, {"H1": 0, "E1": 1}, ...]
        pseudocounts: a dictionary of pseudocounts to add to the counts (for Laplace smoothing)
    Returns:
        theta: a dictionary of parameters
          Example: {"p_start": ..., "p_trans": ..., "p_emit": ...}
    """
    # Initialize counts
    # counts[parameter_name][parent_values][value] = how many times we've seen this
    if pseudocounts is None:
        counts = defaultdict(lambda: defaultdict(lambda: defaultdict(float)))
    else:
        counts = deepcopy(pseudocounts)

    # Count 
    for x in training_data:  # For each assignment x...
        for var, value in x.items():
            parameter_name, parent_vars = network_structure[var]
            parents_value = tuple(x[parent_var] for parent_var in parent_vars)
            counts[parameter_name][parents_value][value] += 1

    # Normalize
    theta = defaultdict(lambda: defaultdict(lambda: defaultdict(float)))
    for parameter_name in counts:
        for parents_value in counts[parameter_name]:
            theta[parameter_name][parents_value] = normalize_dict(counts[parameter_name][parents_value])

    return theta
```

(The function body is shown here without its trace annotations. The docstring's argument is called `data` but the parameter is `training_data`, and its example parameter names carry a `p_` prefix that the call above does not use.)

Traced `theta` (stepped into, so the trace shows it filling in one parameter at a time):

| parameter | parents value | distribution |
| --------- | ------------- | ------------ |
| start | () | 0: 1.0 |
| emit | (0,) | 0: 0.4, 1: 0.6 |
| emit | (1,) | 1: 1.0 |
| trans | (0,) | 1: 0.3333, 0: 0.6667 |
| trans | (1,) | 0: 1.0 |

These are the same numbers the hand-written HMM code produced.

...and then explain the underlying principle.

---

## Section: Maximum likelihood (`maximum_likelihood`)

So far: count + normalize is an intuitive algorithm. It turns out this corresponds exactly to maximum likelihood estimation!

**Maximum likelihood** principle: find the parameters $\theta$ that maximize the likelihood of the data.

$$\max_\theta \prod_x P(X = x; \theta)$$

$$= \max_\theta \sum_x \log P(X = x; \theta)$$

Solution is count + normalize! **Closed form** - no iterative optimization needed!

Why is this? Let's justify this by examples.

### One variable (`maximum_likelihood_one_variable`)

Consider the one variable case. Variable: $R \in \lbrace 1, 2, 3, 4, 5 \rbrace$: rating of a movie. Figure: `pgm-r.png`. $P(R = r) = p_R(r)$. Parameters: $\theta = (p_R)$.

Training data (defined in the code, not displayed as text): `[{"R": 1}, {"R": 5}, {"R": 5}]`.

$$\max_\theta p_R(1) \thinspace p_R(5) \thinspace p_R(5)$$

subject to $p_R(1) + p_R(2) + p_R(3) + p_R(4) + p_R(5) = 1$

Sketch:

- Introduce Lagrange multiplier for the sum-to-one constraint

Set gradient of objective to 0 and solve

Result: $p_R(1) = 1/3$, $p_R(5) = 2/3$

(The last two lines are printed as plain lines rather than bullets; the post-lecture commit made them bullets.)

### Two variables (`maximum_likelihood_two_variables`)

Consider the two variable case. Variables: $G \in \lbrace \text{drama}, \text{comedy} \rbrace$: genre of a movie; $R \in \lbrace 1, 2, 3, 4, 5 \rbrace$: rating of a movie. Figure: `pgm-g-r.png`. $P(G = g, R = r) = p_G(g) \thinspace p_R(r \mid g)$. Parameters: $\theta = (p_G, p_R)$.

Training data (in the code): `(drama, 4)`, `(drama, 5)`, `(comedy, 5)`.

$$\max_\theta p_G(\text{drama}) \thinspace p_R(4 \mid \text{drama}) \thinspace p_G(\text{drama}) \thinspace p_R(5 \mid \text{drama}) \thinspace p_G(\text{comedy}) \thinspace p_R(5 \mid \text{comedy})$$

Rearrange factors into groups that are actually connected:

$$\max_\theta [p_G(\text{drama}) \thinspace p_G(\text{drama}) \thinspace p_G(\text{comedy})] \thinspace [p_R(4 \mid \text{drama}) \thinspace p_R(5 \mid \text{drama})] \thinspace [p_R(5 \mid \text{comedy})]$$

This splits up into separate optimization problems:

$$\max_{p_G} [p_G(\text{drama}) \thinspace p_G(\text{drama}) \thinspace p_G(\text{comedy})]$$

$$\max_{p_R(\cdot \mid \text{drama})} [p_R(4 \mid \text{drama}) \thinspace p_R(5 \mid \text{drama})]$$

$$\max_{p_R(\cdot \mid \text{comedy})} [p_R(5 \mid \text{comedy})]$$

Each optimization problem can be solved in the same way as the one variable case!

Summary:

- Maximum likelihood principle: find the parameters $\theta$ that maximize the likelihood of the data
- Count + normalize is the closed form solution to maximum likelihood estimation

---

## Section: Laplace smoothing (`introduce_laplace_smoothing`)

Link: [Autumn 2023 lecture] — <https://stanford-cs221.github.io/autumn2023/modules/module.html#include=bayesian-networks%2Fsmoothing.js&mode=print6pp>

```python
network_structure = {"R": ("R", [])}
training_data = [{"R": 1}, {"R": 4}]
theta = supervised_learning(network_structure, training_data)
```

Traced: `theta` = R $\lbrace 1: 0.5, 4: 0.5 \rbrace$.

Do we really believe that $p_R(2) = 0$?

Solution: **Laplace smoothing**

Simple idea: just add $\lambda$ to all the counts.

```python
def get_pseudocounts(smoothing: float):
    # Return the pseudocounts given Laplace smoothing with λ (lam)
    return {"R": {(): {1: smoothing, 2: smoothing, 3: smoothing, 4: smoothing, 5: smoothing}}}
pseudocounts = get_pseudocounts(smoothing=1)
theta = supervised_learning(network_structure, training_data, pseudocounts)
```

The traced runs, all on the two examples $R = 1$ and $R = 4$ unless stated:

| run | $p_R(1)$ | $p_R(2)$ | $p_R(3)$ | $p_R(4)$ | $p_R(5)$ |
| --- | -------- | -------- | -------- | -------- | -------- |
| no pseudocounts | 0.5 | — | — | 0.5 | — |
| $\lambda = 1$ | 0.2857 | 0.1429 | 0.1429 | 0.2857 | 0.1429 |
| $\lambda = 0$ | 0.5 | 0.0 | 0.0 | 0.5 | 0.0 |
| $\lambda = 20$ | 0.2059 | 0.1961 | 0.1961 | 0.2059 | 0.1961 |
| $\lambda = 20$, 1,000 examples all $R = 4$ | 0.0182 | 0.0182 | 0.0182 | 0.9273 | 0.0182 |

(A dash means the value has no entry in the dictionary. As fractions: $\lambda = 1$ gives $2/7$ and $1/7$; $\lambda = 20$ gives $21/102$ and $20/102$; the last row is $1020/1100$ and $20/1100$.)

The text between the runs, in order:

- After $\lambda = 1$: "As smoothing λ → 0, we get back to the original maximum likelihood estimate." (then the $\lambda = 0$ run)
- "As smoothing λ → ∞, we get a uniform distribution." (then the $\lambda = 20$ run)
- "No matter what the smoothing λ is, as we get more data, it gets washed out:" (then `training_data = [{"R": 4}] * 1000`, still with the $\lambda = 20$ pseudocounts)

Summary:

- Laplace smoothing: add pseudocounts $\lambda$ to all the counts
- Prevents zeros in the probability estimates

---

## Section: Expectation maximization (`introduce_expectation_maximization`, `expectation_maximization`)

Link: [Autumn 2023 lecture] — <https://stanford-cs221.github.io/autumn2023/modules/module.html#include=bayesian-networks%2Fem-algorithm.js&mode=print6pp>

So far: training data consists of full assignments. Now: what if some of the variables are unobserved?

**Fully-observed (supervised) setting.** Figure: `pgm-g-r1-r2.png`.

```python
training_data = [
    {"G": "drama", "R1": 1, "R2": 1},
    {"G": "comedy", "R1": 2, "R2": 2},
]
network_structure = {
    "G": ("G", []),
    "R1": ("R1", ["G"]),
    "R2": ("R2", ["G"]),
}
theta = supervised_learning(network_structure, training_data)
```

Traced: $p_G = \lbrace \text{drama}: 0.5, \text{comedy}: 0.5 \rbrace$; R1 given drama $\lbrace 1: 1.0 \rbrace$, given comedy $\lbrace 2: 1.0 \rbrace$; R2 the same. (This network gives $R1$ and $R2$ separate parameters.)

**Partially-observed setting.** Figure: `pgm-g-r1-r2.png`.

```python
training_data = [  # No G!
    {"R1": 1, "R2": 1},
    {"R1": 2, "R2": 2},
]
```

What should we do?

Maximum likelihood: maximize the likelihood of the observed data. Here, the data is whatever is observed!

$$\max_\theta \sum_x \log P(X = x; \theta)$$

$$\max_\theta \sum_{r_1, r_2} \log P(R1 = r_1, R2 = r_2; \theta)$$

$$\max_\theta \sum_{r_1, r_2} \log \sum_g P(G = g, R1 = r_1, R2 = r_2; \theta)$$

(Printed as `Σ_{r1,r2}`: the sum runs over the observed $(r_1, r_2)$ pairs in the training data.)

The players

- Observed variables: $R1$, $R2$
- Unobserved variables: $G$
- Parameters: $\theta$

Need to figure out what both $G$ and $\theta$ are!

One algorithm to solve this optimization problem is **Expectation Maximization** (EM) [Dempster+ 1977].

It's a chicken and egg problem:

- If know parameters $\theta$, can compute distribution over unobserved variables $P(G = g \mid R1 = r_1, R2 = r_2; \theta)$.
- If know unobserved variables, can compute parameters $\theta$.

Let's just initialize the parameters $\theta$ randomly, and then iterate:

- E-step: compute $P(G = g \mid R1 = r_1, R2 = r_2; \theta)$ to provide weighted full assignments.
- M-step: compute $\theta$ that maximizes the expected log likelihood of the weighted assignments.

```python
theta = expectation_maximization(training_data, num_iterations=5)
```

```python
def expectation_maximization(training_data, num_iterations: int):
    # Initialize parameters θ = (p_g, p_r_given_g) randomly
    p_g = {"drama": 0.5, "comedy": 0.5}
    p_r_given_g = {
        "comedy": {1: 0.4, 2: 0.6},  # e.g., p(r = 1 | g = comedy) = 0.4
        "drama": {1: 0.6, 2: 0.4},
    }

    # Iterate
    for iteration in range(num_iterations):
        # E-step: compute q(g) = P(G = g | R1 = r1, R2 = r2; θ)
        weighted_training_data = []  # Hallucinated training data
        for x in training_data:
            # First compute P(G = g, R1 = r1, R2 = r2; θ)
            q = {}
            for g in p_g:
                q[g] = p_g[g] * p_r_given_g[g][x["R1"]] * p_r_given_g[g][x["R2"]]
            # Normalize to get P(G = g | R1 = r1, R2 = r2; θ)
            q = normalize_dict(q)

            for g in q:
                weighted_training_data.append((x | {"G": g}, q[g]))

        # M-step: compute θ that maximizes the expected log likelihood of the weighted assignments.
        # Count...
        counts_g = defaultdict(float)
        counts_gr = defaultdict(lambda: defaultdict(float))
        for x, weight in weighted_training_data:
            counts_g[x["G"]] += weight
            counts_gr[x["G"]][x["R1"]] += weight
            counts_gr[x["G"]][x["R2"]] += weight

        # and normalize!
        p_g = normalize_dict(counts_g)
        p_r_given_g = {}
        for g in counts_gr:
            p_r_given_g[g] = normalize_dict(counts_gr[g])

    return {"p_g": p_g, "p_r_given_g": p_r_given_g}
```

(Shown without its trace annotations. The "random" initialization is fixed numbers, deliberately non-uniform. Note that this function uses **one** `p_r_given_g` for both ratings, counting $R1$ and $R2$ into the same `counts_gr` — the parameter-sharing model — whereas the fully-observed example just above learned separate $R1$ and $R2$ tables.)

**Traced run, 5 iterations, data $(1, 1)$ and $(2, 2)$.** Initial $p_G$ = 0.5/0.5; $p_R(\cdot \mid \text{drama}) = \lbrace 1: 0.6, 2: 0.4 \rbrace$, $p_R(\cdot \mid \text{comedy}) = \lbrace 1: 0.4, 2: 0.6 \rbrace$.

In each iteration the E-step computes, for each example, the unnormalized $q(g) = p_G(g) \thinspace p_R(r_1 \mid g) \thinspace p_R(r_2 \mid g)$ and normalizes it; by symmetry the example $(2, 2)$ gets the mirror image of $(1, 1)$'s weights. The M-step then counts each completed example with its weight. $p_G$ stays at 0.5/0.5 in every iteration, and the new $p_R(1 \mid \text{drama})$ equals the weight just given to drama for the example $(1, 1)$:

| iteration | unnormalized $q$ for $(1, 1)$ (drama, comedy) | normalized $q$ for $(1, 1)$ | $p_R(\cdot \mid \text{drama})$ after the M-step | $p_R(\cdot \mid \text{comedy})$ after the M-step |
| --------- | ------------------------------------------ | ------------------------ | ----------------------------------------- | ------------------------------------------ |
| 0 | 0.18, 0.08 | 0.6923, 0.3077 | 1: 0.6923, 2: 0.3077 | 1: 0.3077, 2: 0.6923 |
| 1 | 0.2396, 0.0473 | 0.8351, 0.1649 | 1: 0.8351, 2: 0.1649 | 1: 0.1649, 2: 0.8351 |
| 2 | 0.3487, 0.0136 | 0.9624, 0.0376 | 1: 0.9624, 2: 0.0376 | 1: 0.0376, 2: 0.9624 |
| 3 | 0.4632, 0.0007 | 0.9985, 0.0015 | 1: 0.9985, 2: 0.0015 | 1: 0.0015, 2: 0.9985 |
| 4 | 0.4985, 0.0000 | 1.0000, 0.0000 | 1: 1.0000, 2: 0.0000 | 1: 0.0000, 2: 1.0000 |

(Values rounded to four places. In iteration 4 the exact traced values are 0.9999976821853497 and 2.3178146503212775e-06. The first row is $0.5 \times 0.6 \times 0.6 = 0.18$ and $0.5 \times 0.4 \times 0.4 = 0.08$, so $q = 9/13$ and $4/13$.) The returned `theta` is $p_G = \lbrace \text{drama}: 0.5, \text{comedy}: 0.5 \rbrace$, drama $\lbrace 1: 1.0, 2: 0.0 \rbrace$, comedy $\lbrace 1: 0.0, 2: 1.0 \rbrace$ to the precision displayed.

Intuition: model learns hidden variables to explain the data (clustering)

EM amplifies initial preferences

**Let's try another example:**

```python
training_data = [  # No G!
    {"R1": 1, "R2": 1},
    {"R1": 1, "R2": 1},
    {"R1": 1, "R2": 1},
    {"R1": 1, "R2": 1},
    {"R1": 2, "R2": 2},
    {"R1": 2, "R2": 2},
    {"R1": 1, "R2": 2},
    {"R1": 2, "R2": 1},
]
theta = expectation_maximization(training_data, num_iterations=10)
```

Traced result (stepped over, so only the final value is in the trace): $p_G = \lbrace \text{drama}: 0.5692, \text{comedy}: 0.4308 \rbrace$; $p_R(\cdot \mid \text{drama}) = \lbrace 1: 0.9124, 2: 0.0876 \rbrace$; $p_R(\cdot \mid \text{comedy}) = \lbrace 1: 0.2452, 2: 0.7548 \rbrace$.

The probabilities are a bit smoother to account for more heterogenous [sic] assignments.

Notes

- Guaranteed to increase the likelihood each iteration and converge to a local maximum
- Not guaranteed to converge to the global maximum
- Need to initialize at a non-uniform distribution to break symmetries
- Not [sic] that we can only recover hidden variable up to permutation of labels (can switch drama and comedy)

---

## Section: Summary (`main`)

Summary:

- Learning task: given training data, estimate the parameters of the Bayesian network
- Parameters: local conditional distributions (could have parameter sharing)
- Laplace smoothing: add pseudocounts (smoothing) to prevent overfitting (zeros)
- Expectation Maximization: handle partially-observed data (impute weighted data)

Next time: logic (higher-order reasoning - e.g., *every student has a TA*)!

---

## Changes after the lecture

The next commit to `bayes_learning.py`, 0cd7d5b ("update Bayes", 2025-11-09 6:32pm PST, four days after the lecture and the day before lecture 15), changed the following. The version above is the one taught. None of the changes alters a number.

1. `introduce_fully_observed_setting` was renamed `introduce_fully_observable_setting`, and `supervised_learning` was renamed `fully_observable_learning` (with the comment "we are in the fully observed setting" at its call in `general_bayesian_networks`). Comments were added to the three calls in `main`: "Fully observable setting: all variables are observed", "Prevent overfitting", "Partially-observable setting: some variables are unobserved".
2. In the HMM joint distribution, the printed $p_E(e_1 \mid h_1)$ was corrected to $p_{\text{emit}}(e_1 \mid h_1)$.
3. In `maximum_likelihood_one_variable`, "Set gradient of objective to 0 and solve" and "Result: p_R(1) = 1/3, p_R(5) = 2/3" became bullets.
4. "The players" gained a colon.
5. In `expectation_maximization`, the E-step comment became "guess the hidden variables and weight the training data accordingly", and the definition of $q(g)$ moved to the comment on `q = {}`.
6. Trace-display annotations (`@clear`) were added in several functions, which changes what the rendered trace shows at each step but not what is computed.

## Figures

`alarm-bayes.png` is byte-identical at commit 6ba9ac8 to the version pinned for lecture 12 (git blob 7d72c88a at both commits) and is described in raw/slides/12-bayesian-networks-i.md. The other six images were added in commit 6ba9ac8 and were read directly for this file:

- **`pgm-r.png`** (131 × 127 px): a single circled node labelled $R$.
- **`pgm-g-r.png`** (384 × 152 px): two circled nodes in a row, $G$ on the left and $R$ on the right, joined by an arrow from $G$ to $R$.
- **`pgm-g-a-r.png`** (316 × 304 px): circled nodes $G$ (top left) and $A$ (top right), each with an arrow pointing down to a circled node $R$ centred below them: a v-structure.
- **`pgm-g-r1-r2.png`** (296 × 298 px): circled node $G$ at the top, with arrows down to $R_1$ (bottom left) and $R_2$ (bottom right): an inverted v-structure.
- **`parameter-sharing.png`** (1033 × 491 px): the $G \to R_1$, $G \to R_2$ network in the centre. On the left, a boxed table with header $g$, $p_G(g)$ and rows "c 2/5" and "d 3/5", with a red dashed arrow to node $G$. On the right, a boxed table with header $g$, $r$, $p_R(r \mid g)$ and rows "d 3 1/6", "d 4 3/6", "d 5 2/6", "c 1 1/4", "c 2 1/4", "c 4 1/4", "c 5 1/4", with **two** red dashed arrows, one to $R_1$ and one to $R_2$. The point of the picture is that one table feeds two nodes. The fractions match the traced `parameter_sharing` output exactly (3/6 = 0.5, 2/6 = 0.3333, 1/6 = 0.1667, 1/4 = 0.25; 3/5 = 0.6, 2/5 = 0.4).
- **`pgm-hmm.png`** (701 × 445 px): top row $H_1 \to H_2 \to H_3$, with a short line continuing right from $H_3$ that is cut off at the image edge (the chain goes on); bottom row $E_1$, $E_2$, $E_3$, each with an arrow from the $H_t$ directly above it.
