---
title: Lecture 13 — Bayesian Networks and Gibbs Sampling (Slides)
lecture: 13
source_format: executable-python
source_repo: https://github.com/stanford-cs221/autumn2025-lectures
source_commit: ed66b66 (2025-11-03, "add Gibbs sampling")
source_files:
  - https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/ed66b66/gibbs_sampling.py
rendered:
  - https://stanford-cs221.github.io/autumn2025-lectures/?trace=gibbs_sampling
note: >
  CS221 uses "executable lectures" — Python programs whose execution delivers the
  lecture content — rather than traditional slide PDFs. Lecture 13 was delivered as
  one program, `gibbs_sampling.py`, which imports `ProbTable` and `Bernoulli` from
  lecture 12's `bayes.py` and two helpers from `util.py`. There are no slide numbers;
  sections correspond to function calls in the source. The file is transcribed here
  AS OF COMMIT ed66b66 ("add Gibbs sampling"), which landed at 1:27pm PST on Mon Nov 3
  2025, three minutes before the lecture, and whose README diff adds "Mon Nov 3: gibbs
  sampling (lecture 13)". The next commit to the file (ce25241, "gibbs", 7:21pm PST on
  Tue Nov 4, the evening after the lecture) changed three things, listed in the
  "Changes after the lecture" section at the end; the file was later rewritten in 2026
  for a later offering, so the repo's `main` branch and the live rendered UI no longer
  show what was taught; the pinned URL above does. All demo outputs were read on
  2026-10-24 from the execution trace committed at the same commit
  (`var/traces/gibbs_sampling.json`), whose embedded source is byte-identical to the
  pinned file. The lecturer re-ran some demos in class; where the transcript reports
  different numbers, the wiki page says so. Two figures are shown, `alarm-bayes.png`
  and `medical-bayes.png`; both are byte-identical to lecture 12's pinned versions and
  are described in raw/slides/12-bayesian-networks-i.md, so no new figures appear in
  this lecture.
---

# Lecture 13 Slides — Bayesian Networks and Gibbs Sampling

Lecture 13 continues the Bayesian networks module. It reviews the four-step definition of a Bayesian network, exact inference by forming the joint distribution, probabilistic programs and **rejection sampling**, then introduces **Gibbs sampling**: start from a full assignment that already satisfies the evidence and repeatedly resample one variable at a time from its conditional distribution given all the others. A three-node **telephone** network ($A \to B \to C$) is the worked example. The **Markov blanket** (a node's parents and children) then shows that each update only needs the local conditional probabilities that touch the variable being sampled. Gibbs sampling is run on the alarm network, and the lecture contrasts when each sampler struggles: rare evidence for rejection sampling, highly correlated variables for Gibbs sampling. The last part connects graph structure to **independence** and **conditional independence**, with four two- and three-node examples, the alarm, a graphical procedure for testing conditional independence, and the medical-diagnosis network.

| Part | Function(s) | Content |
| ---- | ----------- | ------- |
| 1. Title and plan | `main` | Last time, this time |
| 2. Review | `review_bayesian_networks`, `rejection_sampling` | Four-step definition; exact inference with einops; probabilistic programs; rejection sampling |
| 3. Gibbs sampling: idea | `introduce_gibbs_sampling` | Rejection sampling's pros and cons; Gibbs sampling as MCMC |
| 4. Telephone example | `gibbs_telephone`, `gibbs_sampling` | Rejection sampling vs Gibbs sampling on $A \to B \to C$; conditional distribution from the joint; the general algorithm |
| 5. Markov blanket | `markov_blanket` | Dropping factors that do not involve the sampled variable |
| 6. Alarm | `gibbs_alarm` | Gibbs sampling for $P(B \mid A = 1)$ |
| 7. Gibbs vs rejection | `compare_gibbs_and_rejection` | Rare evidence; highly correlated variables |
| 8. Further reading | `introduce_gibbs_sampling` | MCMC, Metropolis–Hastings, mixing times |
| 9. Conditional independence | `introduce_conditional_independence` | Independence, conditional independence, four examples, alarm, general algorithm, medical diagnosis |
| 10. Summary | `main` | Three-line summary; next time |

Notation in this file follows the lecture's (and lecture 12's): uppercase letters ($A$, $B$, $C$) are random variables and lowercase letters ($a$, $b$, $c$) their values; lowercase $p$ is a **local conditional probability**, defined directly; uppercase $P$ is a probability derived from the joint distribution. All variables are binary, $\lbrace 0, 1 \rbrace$. `ProbTable` is the probability-table class from lecture 12 (described in raw/slides/12-bayesian-networks-i.md under "Helper: class `ProbTable`"): its tensor's axes are the conditioning variables followed by the generated variables, so `p_b_given_a.p[a, b]` is $p(b \mid a)$.

---

## Section: Title and plan (`main`)

**Lecture 13: Gibbs sampling.**

Last time: Bayesian networks, rejection sampling.

This time: faster probabilistic inference methods (Gibbs sampling) + conditional independence.

Order of the lecture: `review_bayesian_networks` → `introduce_gibbs_sampling` (which runs `gibbs_telephone`, `markov_blanket`, `gibbs_alarm`, `compare_gibbs_and_rejection`, then "Further reading") → `introduce_conditional_independence` → summary.

---

## Section: Review (`review_bayesian_networks`)

**Joint distribution**: like a database that specifies how the world works.

1\. Define a set of random variables: $X = (X_1, \ldots, X_n)$. Example: B(urglary), E(arthquake), A(larm).

2\. Define a directed acyclic graph (DAG) over the variables. Example: $B \to A$, $E \to A$. Figure: `alarm-bayes.png` (from lecture 12 — nodes $B$ and $E$ at the top, each with an arrow down to $A$; described in raw/slides/12-bayesian-networks-i.md).

3\. For each node, define a local conditional distribution: $p(x_i \mid \text{parents}(x_i))$. Example: $p(b)$, $p(e)$, $p(a \mid b, e)$.

```python
p_b = ProbTable("B", [0.95, 0.05])  # p(b)
p_e = ProbTable("E", [0.95, 0.05])  # p(e)
p_a_given_be = ProbTable("A | B E", lambda b, e, a: a == (b or e), shape=(2, 2, 2))  # p(a | b, e)
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

4\. Define the joint distribution: $P(X_1, \ldots, X_n) = \prod_i p(x_i \mid \text{parents}(x_i))$. Example: $P(B, E, A) = p(b) \thinspace p(e) \thinspace p(a \mid b, e)$. In code, `P_BEA = ProbTable("B E A", einsum(p_b.p, p_e.p, p_a_given_be.p, "b, e, b e a -> b e a"))`, traced as:

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

```python
P_BEA1 = ProbTable("B E A=1", P_BEA.p[:, :, 1])            # Condition on evidence: P(B, E, A = 1)
P_BA1 = ProbTable("B A=1", einsum(P_BEA1.p, "b e -> b"))   # Marginalize out other variables: P(B, A = 1)
P_A1 = ProbTable("A=1", einsum(P_BA1.p, "b ->"))           # Probability of evidence: P(A = 1)
P_B_given_A1 = ProbTable("B | A=1", P_BA1.p / P_A1.p)      # Normalize to get conditional: P(B | A = 1)
```

Traced values: $P(B, E, A = 1)$ is 0.0, 0.0475, 0.0475, 0.0025 for $(b, e) = (0, 0), (0, 1), (1, 0), (1, 1)$; $P(B = 0, A = 1) = 0.0475$ and $P(B = 1, A = 1) = 0.05$; $P(A = 1) = 0.0975$; and $P(B = 0 \mid A = 1) = 0.48717948717948717$, $P(B = 1 \mid A = 1) = 0.5128205128205129$.

Forming the joint distribution can be exponentially slow...

**Probabilistic program.** A probabilistic program returns a sample from the joint distribution ...thereby defining it.

```python
def alarm():
    B = Bernoulli(0.05)
    E = Bernoulli(0.05)
    A = B or E
    return {"B": B, "E": E, "A": A}
```

One traced call: `sample = {"B": 0, "E": 0, "A": 0}`.

**Rejection sampling.** With `query = lambda sample: sample["B"]` and `evidence = lambda sample: sample["A"] == 1`:

```python
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

(The function is the same as lecture 12's, redefined in this file.) Traced results:

- `num_samples=1`: the single sample is $\lbrace B = 0, E = 1, A = 1 \rbrace$, which matches the evidence, so `counts` is $\lbrace 0: 1 \rbrace$, `total_count` is 1 and the result is $\lbrace 0: 1.0 \rbrace$.
- `num_samples=300`: $\lbrace 0: 0.5517241379310345, 1: 0.4482758620689655 \rbrace$, which is 16/29 and 13/29, so 29 of the 300 samples were accepted. The trace displays the exact `P_B_given_A1` beside it for comparison (0.4872 for $B = 0$, 0.5128 for $B = 1$).

But rejection sampling can also be quite slow if the evidence is rare. How can we perform probabilistic inference faster?

---

## Section: Gibbs sampling — the idea (`introduce_gibbs_sampling`)

**Rejection sampling**

sample ~~sample~~ sample ~~sample~~ ~~sample~~ sample

(Printed with strikethrough on the second, fourth and fifth "sample": the rejected ones.)

- Pro: each sample is independent.
- Con: we have to start from scratch for every sample.
- Con: not using evidence while generating sample, leading to rejection.

**Gibbs sampling**: start with the previous sample (which always satisfies the evidence). Basic idea:

- Start with an arbitrary sample.
- Iteratively change one variable at a time conditioned on the other variables.

Gibbs sampling is a special case of Markov chain Monte Carlo (MCMC).

sample → sample → sample → sample

- Pro: Start with samples that satisfy the evidence, so don't have to reject anything.
- Con: samples are not independent, so require more samples.

The function then runs the four parts below in order: `gibbs_telephone()` ("Gibbs sampling on a telephone example"), `markov_blanket()` ("Introduce the Markov blanket to speed things up"), `gibbs_alarm()` ("Gibbs sampling on the alarm Bayesian network") and `compare_gibbs_and_rejection()` ("When does it work versus rejection sampling?"), and ends with "Further reading" (section 8 below).

---

## Section: Telephone example (`gibbs_telephone`)

**Example**: telephone. Bayesian network: $A \to B \to C$.

Intuition: A sends a bit to B, B sends a bit to C, where at each step message might be corrupted.

Here's the definition of the Bayesian network:

```python
p_a = ProbTable("A", [0.5, 0.5])                                                  # p(a)
p_b_given_a = ProbTable("B | A", lambda a, b: 0.8 if b == a else 0.2, shape=(2, 2))  # p(b | a)
p_c_given_b = ProbTable("C | B", lambda b, c: 0.8 if c == b else 0.2, shape=(2, 2))  # p(c | b)
```

Traced tables: $p(A = 0) = p(A = 1) = 0.5$; $p(b \mid a)$ is 0.8 when $b = a$ and 0.2 otherwise (rows $(a, b)$: 0 0 0.8, 0 1 0.2, 1 0 0.2, 1 1 0.8); $p(c \mid b)$ likewise (rows $(b, c)$: 0 0 0.8, 0 1 0.2, 1 0 0.2, 1 1 0.8).

Probabilistic inference goal: $P(A \mid C = 1)$.

Intuition: C=1 encourages A=1, but weakly through two steps of corruption.

### Rejection sampling on the telephone

Let's first run rejection sampling (`set_random_seed(3)`):

```python
def telephone():
    A = Bernoulli(0.5)
    B = Bernoulli(0.8 if A == 1 else 0.2)
    C = Bernoulli(0.8 if B == 1 else 0.2)
    return {"A": A, "B": B, "C": C}
```

One traced sample: $\lbrace A = 1, B = 1, C = 1 \rbrace$. With `query = lambda sample: sample["A"]`, `evidence = lambda sample: sample["C"] == 1` and `num_samples=100`, the traced result is `rejection_probs` $= \lbrace 1: 0.6530612244897959, 0: 0.3469387755102041 \rbrace$, which is 32/49 and 17/49, so 49 of the 100 samples were accepted.

### Gibbs sampling on the telephone

Let's now run Gibbs sampling (`set_random_seed(3)` again):

- Start with an initialization (full assignment).
- For each variable $X_i$, change it based on all other variables:
  - Compute the conditional distribution of $X_i$ given all other variables.
  - Sample $X_i$ from this conditional distribution.

"Initialize with an arbitrary sample that satisfies the evidence (D = 1):" [sic — the network has no $D$; the evidence is $C = 1$, and the code's initialization is `x = {"A": 1, "B": 0, "C": 1}`.]

**Conditional distribution.** Suppose the goal is to sample $B$ given $A = a$ and $C = c$.

Express conditional probability in terms of joint probability:

$$P(B = b \mid A = a, C = c) = \frac{P(A = a, B = b, C = c)}{P(A = a, C = c)}$$

...with a normalizer which is probability of all other variables we're not sampling:

$$P(A = a, C = c) = \sum_b P(A = a, B = b, C = c)$$

The joint probability is the product of the local conditional probabilities:

$$P(A = a, B = b, C = c) = p(a) \thinspace p(b \mid a) \thinspace p(c \mid b)$$

Note: we don't have to instantiate the entire joint distribution (could be exponentially large).

```python
def joint_prob(x, var, value):
    # x is an assignment (e.g., {A: 0, B: 0, C: 1})#
    # var: value is a change to the assignment (e.g., "B": 1)
    # Let y be the updated assignment (e.g., {A: 0, B: 1, C: 1})
    y = x | {var: value}
    # Compute joint probability with updated assignment: P(y)
    return p_a.p[y["A"]] * p_b_given_a.p[y["A"], y["B"]] * p_c_given_b.p[y["B"], y["C"]]
```

Note we don't instantiate the joint distribution here. But we do have to touch every variable (we'll come back to this later).

Let's say we were sampling $B \in \lbrace 0, 1 \rbrace$:

```python
p_ab0c = joint_prob(x, "B", 0)   # P(A = 1, B = 0, C = 1)
p_ab1c = joint_prob(x, "B", 1)   # P(A = 1, B = 1, C = 1)
p_ac = p_ab0c + p_ab1c           # P(A = 1, C = 1)
p_b0_given_ac = p_ab0c / p_ac    # P(B = 0 | A = 1, C = 1)
p_b1_given_ac = p_ab1c / p_ac    # P(B = 1 | A = 1, C = 1)
x["B"] = sample_dict({0: p_b0_given_ac, 1: p_b1_given_ac})
```

Traced values: $P(A = 1, B = 0, C = 1) = 0.02$ (that is, $0.5 \times 0.2 \times 0.2$), $P(A = 1, B = 1, C = 1) = 0.32$ ($0.5 \times 0.8 \times 0.8$), $P(A = 1, C = 1) = 0.34$, $P(B = 0 \mid A = 1, C = 1) = 0.058823529411764705$ and $P(B = 1 \mid A = 1, C = 1) = 0.9411764705882353$. The sampled value is $B = 1$, so `x` becomes $\lbrace A = 1, B = 1, C = 1 \rbrace$.

Note that B gets pulled to 1 because of A=1 and C=1.

### The general algorithm (`gibbs_sampling`)

Let's define Gibbs sampling in greater generality:

```python
def gibbs_sampling(init_x: dict, vars: list[str], query: Callable, joint_prob: Callable, num_iterations: int) -> dict:
    """
    Run Gibbs sampling from the initial assignment `init_x`.
    Cycle `num_iterations` over `vars` (excludes evidence variables).
    Return the estimated distribution over query.
    """
    # Initialize the assignment
    x = dict(init_x)  # Make a copy (good hygiene)

    # Track counts of query values
    counts = defaultdict(int)

    # Keep iterating over all the variables
    for _ in range(num_iterations):
        for var in vars:
            # Compute probabilities for each assignment of {var: value}
            probs = {value: joint_prob(x, var, value) for value in [0, 1]}  # value -> P(..., var=value)

            # Normalize probabilities
            probs = normalize_dict(probs)

            # Sample the variable
            x[var] = sample_dict(probs)

            # Record the query value
            counts[query(x)] += 1

    # Normalize to get the estimated distribution over query
    probs = normalize_dict(counts)

    return probs
```

Note that `counts` is incremented after every single-variable update, so a run of $T$ iterations over $k$ variables records $kT$ query values. The two helpers come from `util.py`:

```python
def sample_dict(choices: dict[Any, float]) -> Any:
    """Sample a key from a dictionary of choices based on their probabilities (values)."""
    return np.random.choice(list(choices.keys()), p=list(choices.values()))

def normalize_dict(choices: dict[Any, float]) -> dict[Any, float]:
    """Normalize a dictionary of choices based on their probabilities (values)."""
    total_prob = sum(choices.values())
    return {key: prob / total_prob for key, prob in choices.items()}
```

(`normalize_dict` was added to `util.py` in this lecture's commit.)

**One iteration** (`vars=["A", "B"]`, `num_iterations=1`, starting from $\lbrace A = 1, B = 1, C = 1 \rbrace$), as traced:

- `var = "A"`: unnormalized `probs` $= \lbrace 0: 0.08, 1: 0.32 \rbrace$, normalized $\lbrace 0: 0.2, 1: 0.8 \rbrace$; the sample keeps $A = 1$; `counts` $= \lbrace 1: 1 \rbrace$.
- `var = "B"`: unnormalized $\lbrace 0: 0.02, 1: 0.32 \rbrace$, normalized $\lbrace 0: 0.0588, 1: 0.9412 \rbrace$; the sample keeps $B = 1$; `counts` $= \lbrace 1: 2 \rbrace$.
- Result: `gibbs_probs` $= \lbrace 1: 1.0 \rbrace$.

**Run Gibbs sampling for multiple iterations** (`num_iterations=100`): traced `gibbs_probs` $= \lbrace 1: 0.68, 0: 0.32 \rbrace$.

Comparing with rejection sampling, both are in the ballpark. (The trace re-displays `rejection_probs` $= \lbrace 1: 0.6531, 0: 0.3469 \rbrace$ here.)

Running time: `O(#iterations * #variables * |domain| * #variables)`.

Summary:

- Update one variable at a time using its conditional distribution given all other variables.
- Compute the conditional distribution using the joint distribution.
- Only have to sum over domain of the variable we're sampling.

Can we be even more efficient and not touch variables we're not sampling?

---

## Section: Markov blanket (`markov_blanket`)

Joint probability: $p(a) \thinspace p(b \mid a) \thinspace p(c \mid b) \thinspace p(d \mid c) \thinspace p(e \mid d)$.

Can we avoid having to compute the full joint probability every time we sample a variable? Intuitively, only one variable changes...

Recall the telephone Bayesian network: $A \to B \to C$ (the same three tables as above, redefined; traced identically).

Suppose we're sampling $P(A \mid B = b, C = c)$. Let's look at the joint distribution:

$$P(A = 0, B = b, C = c) = p(a = 0) \thinspace p(b \mid a = 0) \thinspace p(c \mid b)$$

$$P(A = 1, B = b, C = c) = p(a = 1) \thinspace p(b \mid a = 1) \thinspace p(c \mid b)$$

$$P(B = b, C = c) = \sum_a p(a) \thinspace p(b \mid a) \thinspace p(c \mid b)$$

Notice that $p(c \mid b)$ shows up in both cases, and does not impact $P(A \mid B = b, C = c)$. So we can just ignore it!

In general, we need to only include variables that touch the variable we're sampling. This set of variables is called the **Markov blanket**. The Markov blanket of a variable is all its children and parents.

- MarkovBlanket(A) = {B}
- MarkovBlanket(B) = {A, C}
- MarkovBlanket(C) = {B}

Instead of computing the joint probability ...let us just include the variables in the Markov blanket.

```python
def markov_prob(x, var, value):
    y = x | {var: value}
    if var == "A":
        return p_a.p[y["A"]] * p_b_given_a.p[y["B"], y["A"]]  # no p(c | b)
    elif var == "B":
        return p_b_given_a.p[y["B"], y["A"]] * p_c_given_b.p[y["C"], y["B"]]  # no p(a)
    else:
        raise ValueError(f"Unknown variable: {var}")
```

The two-dimensional tables are indexed here as `[child, parent]`, the reverse of `ProbTable`'s `[parent, child]` axis order (compare `joint_prob` above). Both tables are symmetric (0.8 on the diagonal, 0.2 off it), so the traced numbers are unaffected; the indices were swapped to `[parent, child]` in the post-lecture commit (see the last section).

Now we can run Gibbs sampling with the Markov blanket: from `x = {"A": 0, "B": 0, "C": 1}`, with query `sample["A"]`, `vars=["A", "B"]`, `joint_prob=markov_prob` and `num_iterations=100`, the traced result is `probs` $= \lbrace 0: 0.37, 1: 0.63 \rbrace$.

- Running time with `joint_prob`: `O(#iterations * #variables * |domain| * #variables)`.
- Running time with `markov_prob`: `O(#iterations * #variables * |domain| * |markov_blanket|)` (the last factor printed in bold).

Summary:

- Markov blanket of a node: all its children and parents.
- Gibbs sampling requires local conditional probabilities of node and its Markov blanket.
- Much more efficient if Markov blankets are small.

---

## Section: Gibbs sampling on the alarm (`gibbs_alarm`)

Let's now run Gibbs sampling on the alarm Bayesian network. Figure: `alarm-bayes.png` (as in the review). The three tables are redefined exactly as in the review (traced identically).

```python
# Initialize with an arbitrary sample that satisfies the evidence
x = {"B": 1, "E": 1, "A": 1}

# Iterate over each variable (that's not evidence)
def compute_prob(x, var, value):
    y = x | {var: value}
    return p_b.p[y["B"]] * p_e.p[y["E"]] * p_a_given_be.p[y["B"], y["E"], y["A"]]

# We're interesting in P(B | ...)
query = lambda sample: sample["B"]

# Run Gibbs sampling
probs = gibbs_sampling(x, vars=["B", "E"], query=query, joint_prob=compute_prob, num_iterations=100)
probs = gibbs_sampling(x, vars=["B", "E"], query=query, joint_prob=compute_prob, num_iterations=200)
```

("interesting" [sic] in the source comment.) Both runs start from the same initialization, since `gibbs_sampling` copies `init_x`. Traced results:

- `num_iterations=100`: $\lbrace 0: 0.35, 1: 0.65 \rbrace$.
- `num_iterations=200`: $\lbrace 0: 0.665, 1: 0.335 \rbrace$.

Note that the estimates aren't quite accurate... (The exact answer computed in the review is $P(B = 1 \mid A = 1) = 0.5128$.)

---

## Section: Gibbs vs rejection sampling (`compare_gibbs_and_rejection`)

**What examples are hard for rejection sampling?** Answer: when evidence has low probability.

$A \to B$:

```python
p_a = ProbTable("A", [0.5, 0.5])                                         # p(a)
p_b_given_a = ProbTable("B | A", [[0.9999, 0.0001], [0.9998, 0.0002]])  # p(b | a)
```

Traced: $p(A = 0) = p(A = 1) = 0.5$; $p(B = 0 \mid A = 0) = 0.9999$, $p(B = 1 \mid A = 0) = 0.0001$, $p(B = 0 \mid A = 1) = 0.9998$, $p(B = 1 \mid A = 1) = 0.0002$.

Compute $P(A \mid B = 1)$. Reason: need to reject all samples that don't match the evidence. Gibbs sample conditions on $B = 1$, so will work fine. (No sampler is run on this example.)

**What examples are hard for Gibbs sampling?** Answer: when variables are highly correlated.

$A \to B$:

```python
p_a = ProbTable("A", [0.5, 0.5])                                          # p(a)
p_b_given_a = ProbTable("B | A", lambda a, b: a == b, shape=(2, 2))       # p(b | a)
```

Traced: $p(A = 0) = p(A = 1) = 0.5$; $p(b \mid a)$ is 1.0 when $b = a$ and 0.0 otherwise.

Probabilistic inference: $P(A)$ [no evidence]. Initialization `x = {"A": 0, "B": 0}`.

- Rejection sampling generates from scratch every time, so works perfectly (no rejection).
- Gibbs sampling gets stuck and will not explore A = 1.

```python
query = lambda sample: sample["A"]
def joint_prob(x, var, value):
    y = x | {var: value}
    return p_a.p[y["A"]] * p_b_given_a.p[y["A"], y["B"]]
gibbs_probs = gibbs_sampling(x, vars=["A", "B"], query=query, joint_prob=joint_prob, num_iterations=50)
```

Traced result: `gibbs_probs` $= \lbrace 0: 1.0 \rbrace$ — every one of the 100 recorded values is $A = 0$.

Summary:

- Rare events are hard for rejection sampling.
- Highly correlated variables are hard for Gibbs sampling.

---

## Section: Further reading (`introduce_gibbs_sampling`)

- Gibbs sampling is an example of a Markov chain Monte Carlo (MCMC) algorithm.
- A more general MCMC algorithm is Metropolis-Hastings (uses a proposal distribution).
- Theory: use mixing times to study effective running time (how correlated samples are)?
- Practice: simple and effective, but can be slow.

---

## Section: Conditional independence (`introduce_conditional_independence`)

So far: can do probabilistic inference on Bayesian networks. They have been fairly agnostic to the structure of the Bayesian network. Now: let's explore some properties of the Bayesian network. We will connect graph properties to probabilistic properties (in particularly independence).

**Independence.** Two variables $A$ and $B$ are independent iff:

$$P(A = a, B = b) = P(A = a) \thinspace P(B = b) \text{ for all } a, b$$

**Example 1 (A B)** — two nodes, no edge:

$$P(A = a, B = b) = p(a) \thinspace p(b)$$

A and B are independent.

**Example 2 (A → B):**

$$P(A = a, B = b) = p(a) \thinspace p(b \mid a)$$

A and B are not independent.

**Example 3 (A, B → C):** printed as

$$P(A = a, B = b, C = c) = p(a) \thinspace p(b \mid a) \thinspace p(c \mid b, a)$$

[sic — in this network $B$ has no parent, so the factor is $p(b)$; the next line of the source uses $p(b)$.]

$$P(A = a, B = b) = \sum_c P(A = a, B = b, C = c) = \sum_c p(a) \thinspace p(b) \thinspace p(c \mid b, a) = p(a) \thinspace p(b)$$

A and B are independent (even if they are connected)!

**Example 4 (C → A, B):**

$$P(A = a, B = b, C = c) = p(c) \thinspace p(a \mid c) \thinspace p(b \mid c)$$

$$P(A = a, B = b) = \sum_c P(A = a, B = b, C = c) = \sum_c p(c) \thinspace p(a \mid c) \thinspace p(b \mid c)$$

A and B are not independent!

**Conditional independence.** Two variables $A$ and $B$ are conditionally independent given $C = c$ iff:

$$P(A = a, B = b \mid C = c) = P(A = a \mid C = c) \thinspace P(B = b \mid C = c) \text{ for all } a, b$$

**Example 4 again (C → A, B):**

$$P(A = a, B = b \mid C = c) = \frac{p(c) \thinspace p(a \mid c) \thinspace p(b \mid c)}{p(c)} = p(a \mid c) \thinspace p(b \mid c)$$

A and B are conditionally independent given $C = c$.

**Example 3 again (A, B → C):** printed as

$$P(A = a, B = b \mid C = c) = p(a) \thinspace p(b) \thinspace p(c \mid a, b)$$

(This is as printed; the right-hand side has no division by $P(C = c)$.) A and B are not conditionally independent given $C = c$.

**Alarm example.** Variables: Burglary B, Earthquake E, Alarm A. Figure: `alarm-bayes.png`.

- B and E are independent.
- B and E are not conditionally independent given A = 1.

**General algorithm.** Question: are A and B independent given C?

1. Shade in the variables C.
2. Recursively remove any non-shaded leaves.
3. Connect parents to each other (marriage).
4. Return whether there is a path from A to B that doesn't go through any shaded nodes.

(The source numbers the last two steps "3." and "3."; they are numbered 3 and 4 here.)

**Medical diagnosis example.** Variables: Cold C, Allergies A, Cough H, Itchy eyes I. Figure: `medical-bayes.png` (from lecture 12 — $C \to H$, $A \to H$, $A \to I$; described in raw/slides/12-bayesian-networks-i.md).

- C and A are independent.
- C and I are independent.
- C and H are independent given A.
- C and I are independent given A.
- C and I are independent given H, A.

The third line, "C and H are independent given A", is false for this network ($C \to H$ is an edge, so they stay connected however the procedure is run) and was deleted in the post-lecture commit; see the last section, and the wiki page for what was said in class.

---

## Section: Summary (`main`)

Summary:

- Exact probabilistic inference: form the joint distribution and marginalize/condition (einops).
- Approximate probabilistic inference: rejection sampling, Gibbs sampling.
- Conditional independence: determine whether two variables are independent given evidence?

Next time: learning parameters of Bayesian networks.

---

## Changes after the lecture

The next commit to `gibbs_sampling.py`, ce25241 ("gibbs", 2025-11-04 7:21pm PST, the evening after the lecture, before lecture 14), changed three things. The version above is the one taught.

1. In `markov_blanket`, the sentence "In general, we need to only include variables that touch the variable we're sampling." became two: "In general, we need to only include local conditional probabilities that involve the variable we're sampling." and "Evaluating these local conditional probabilities requires knowing all involved variables."
2. In `markov_prob`, the table indices were swapped from `[child, parent]` to `[parent, child]` (`p_b_given_a.p[y["A"], y["B"]]`, `p_c_given_b.p[y["B"], y["C"]]`), matching `ProbTable`'s axis order. Because both tables are symmetric, this does not change any number.
3. In the medical-diagnosis list, "C and H are independent given A" was deleted, and "C and I are independent given H, A" was reordered to "given A, H".

## Figures

No new figures. `alarm-bayes.png` (shown twice) and `medical-bayes.png` are byte-identical at commit ed66b66 to the versions pinned for lecture 12 (git blob hashes 7d72c88a and 904786ac at both commits), and are described in the Figures section of raw/slides/12-bayesian-networks-i.md.
