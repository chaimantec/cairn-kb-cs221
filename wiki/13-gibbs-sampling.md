---
title: Lecture 13 — Bayesian Networks and Gibbs Sampling
---

# Lecture 13 — Bayesian Networks and Gibbs Sampling

Lecture 13 continues the [Bayesian networks](bayesian-networks.md) unit with a faster way to do approximate [probabilistic inference](probabilistic-inference.md). [Rejection sampling](rejection-sampling.md) draws every sample from scratch and throws away the ones that miss the evidence. [Gibbs sampling](gibbs-sampling.md) instead keeps one full assignment that already satisfies the evidence and changes it one variable at a time, each time sampling that variable from its conditional distribution given all the others ([≈0:05](../raw/transcripts/13-gibbs-sampling.md), [≈16:23](../raw/transcripts/13-gibbs-sampling.md)). The [Markov blanket](markov-blanket.md) makes each update local, and two small networks show the opposite ways the two samplers fail: rare evidence defeats rejection sampling, highly correlated variables defeat Gibbs sampling. The last part of the lecture connects the shape of the graph to [conditional independence](conditional-independence.md), with a procedure that answers "are these two variables independent given this evidence?" from the graph alone ([≈58:16](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

Notation, as in lecture 12: uppercase letters ($A$, $B$, $C$) are random variables and lowercase letters ($a$, $b$, $c$) their values. Lowercase $p$ is a **local conditional probability**, written down when the network is defined; uppercase $P$ is a probability derived from the joint distribution. Every variable in this lecture is binary, taking values in $\lbrace 0, 1 \rbrace$.

## Review: A Joint Distribution Is a Database

The lecturer opens by calling Bayesian networks "probably one of the most kind of intensely deep" parts of the class, and asks for questions whenever something is confusing ([≈0:05](../raw/transcripts/13-gibbs-sampling.md)). The mindset: a joint distribution over a set of variables is like a **database** that specifies how the world works. A state of the world is an assignment to the variables, and the database says how likely each assignment is ([≈0:54](../raw/transcripts/13-gibbs-sampling.md)).

A Bayesian network defines that database in four steps, reviewed on the alarm network ([≈0:54](../raw/transcripts/13-gibbs-sampling.md)–[≈5:32](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

1. Choose the random variables, in general $X_1, \ldots, X_n$; here burglary $B$, earthquake $E$ and alarm $A$.
2. Draw a directed acyclic graph over them: $B \to A$ and $E \to A$, since burglaries and earthquakes are intuitively independent and the alarm depends on both. The graph is "kind of like the bones."
3. Put "meat on those bones": a local conditional distribution for every node given its parents, $p(b)$, $p(e)$ and $p(a \mid b, e)$. Here $p(B = 1) = p(E = 1) = 0.05$, and the table for $p(a \mid b, e)$ is "a probabilistically verbose way" of saying $a = b \lor e$: probability 1 when $a$ equals $b$ or $e$, and 0 otherwise.
4. The joint distribution is the product of the local conditionals, $P(B = b, E = e, A = a) = p(b) \thinspace p(e) \thinspace p(a \mid b, e)$, computed with one `einsum` over the three tables. It gives 0.9025 to nothing happening, 0.0475 each to a burglary alone and an earthquake alone, and 0.0025 to both.

You could have written down the eight numbers directly, but going through the network is more interpretable than "writing down some arbitrary numbers" ([≈5:32](../raw/transcripts/13-gibbs-sampling.md)).

What do you do with the database? Probabilistic inference: instead of SQL, you ask it questions in the language of probability, such as the probability of a burglary given that the alarm went off, $P(B \mid A = 1)$ ([≈6:18](../raw/transcripts/13-gibbs-sampling.md)). Compared with lecture 12, the lecturer adds a step ("I think this is a bit more intuitive") ([≈6:18](../raw/transcripts/13-gibbs-sampling.md)–[≈9:23](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

1. **Condition on the evidence** by slicing the tensor: keep only the rows where $A = 1$, giving $P(B, E, A = 1)$.
2. **Marginalize out** the variable you do not care about, $E$, by collapsing rows that differ only in $E$: $P(B = 0, A = 1) = 0.0475$ and $P(B = 1, A = 1) = 0.05$.
3. Compute the **probability of the evidence** by summing those two: $P(A = 1) = 0.0975$.
4. **Divide**, because "the joint divided by the marginal gives me the conditional": $P(B = 1 \mid A = 1) \approx 0.51$, the same answer as last time.

The catch is cost. With 100 binary variables the joint distribution has $2^{100}$ assignments, "and you definitely don't want to be writing that down." So the lecture turns to **approximate inference**: "try to approximate the answer and hope for the best" ([≈9:23](../raw/transcripts/13-gibbs-sampling.md), [≈10:11](../raw/transcripts/13-gibbs-sampling.md)).

## Review: Rejection Sampling, and What Is Wrong With It

A **probabilistic program** returns a sample from the joint distribution, and therefore defines it: "if samples from it obey some distribution then that's as good as the definition." The alarm program sets each variable after its parents, $B$ and $E$ with probability 0.05 each and then $A = B \lor E$; one run gives all zeros ([≈10:11](../raw/transcripts/13-gibbs-sampling.md), [≈11:00](../raw/transcripts/13-gibbs-sampling.md)).

[Rejection sampling](rejection-sampling.md) takes a query (a function that pulls $B$ out of a sample) and evidence (a test that the sample has $A = 1$), draws samples, keeps those that pass the test and normalizes the counts of the query ([≈11:47](../raw/transcripts/13-gibbs-sampling.md), [≈12:32](../raw/transcripts/13-gibbs-sampling.md)). The single traced sample happens to contain an earthquake ("oh I got an earthquake... very lucky, or not lucky"), so it is kept. Why is the total count not the same as the number of samples? Because only samples that meet the evidence are counted ([≈13:19](../raw/transcripts/13-gibbs-sampling.md)). With 300 samples the estimate of $P(B = 1 \mid A = 1)$ is about 0.44 (traced 0.448, from 29 kept samples), "in the ballpark" of the exact 0.51 but only roughly; with a million samples it would get very close ([≈13:19](../raw/transcripts/13-gibbs-sampling.md), [≈14:04](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

The problem is rare evidence. If the probability of hitting the evidence is something like $10^{-30}$, you "would just be sitting around and rejecting, rejecting, rejecting" ([≈14:04](../raw/transcripts/13-gibbs-sampling.md), [≈14:50](../raw/transcripts/13-gibbs-sampling.md)). The root cause is that the program generates samples without looking at the evidence and just hopes to hit it. The lecturer still likes rejection sampling, since it is the fastest path to a Bayesian network plus an inference algorithm that provably works in the limit, but it is not efficient ([≈14:50](../raw/transcripts/13-gibbs-sampling.md)).

## Gibbs Sampling: The Idea

The slide sets the two methods side by side ([≈15:37](../raw/transcripts/13-gibbs-sampling.md)–[≈18:41](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). Rejection sampling draws "sample ~~sample~~ sample ~~sample~~ ~~sample~~ sample", crossing out the rejected ones. Its advantage is that **every sample is independent**, so ten samples are ten independent pieces of evidence. Its two disadvantages: every sample starts from scratch, so it learns nothing from what happened before, and it generates samples without using the evidence, which causes rejections.

**Gibbs sampling** works with the previous sample, which is constructed to always satisfy the evidence, and modifies it gradually: start with an arbitrary assignment, then iteratively change one variable at a time given the other variables ([≈16:23](../raw/transcripts/13-gibbs-sampling.md), [≈17:08](../raw/transcripts/13-gibbs-sampling.md)). It is "a much more incremental approach" than throwing everything out and hoping to hit the evidence.

Gibbs sampling is a special case of **Markov chain Monte Carlo** (MCMC): you get a sequence of samples, sample → sample → sample → sample, each dependent on the previous one, which is why it is a Markov chain ([≈17:08](../raw/transcripts/13-gibbs-sampling.md), [≈17:55](../raw/transcripts/13-gibbs-sampling.md)). The trade is the mirror image of rejection sampling's. The evidence is built in from the start, so "there's no rejection in Gibbs sampling." But the samples are **not independent**, so one sample may be very similar to the next, and ten correlated samples give a worse estimate than ten independent ones; you need more of them ([≈17:55](../raw/transcripts/13-gibbs-sampling.md)).

## The Telephone Network

The worked example is the game of telephone: person $A$ sends a one-bit message to $B$, who passes it on to $C$, and at each step the message may be corrupted, so that by the end "everyone bursts out into laughter" ([≈18:41](../raw/transcripts/13-gibbs-sampling.md), [≈19:27](../raw/transcripts/13-gibbs-sampling.md)). As a Bayesian network, $A \to B \to C$, with ([≈19:27](../raw/transcripts/13-gibbs-sampling.md), [≈20:15](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

- $p(a)$: a coin flip, 0.5 for each value;
- $p(b \mid a)$: 0.8 that $B$ copies $A$, 0.2 that it flips the bit;
- $p(c \mid b)$: the same, 0.8 to preserve and 0.2 to flip.

The question: if the person at the end receives a one, what was the original message, $P(A \mid C = 1)$? Seeing $C = 1$ should raise the probability of $A = 1$, because each step's error rate is below 50%, but only weakly, since there were two chances for a flip ([≈20:15](../raw/transcripts/13-gibbs-sampling.md), [≈21:02](../raw/transcripts/13-gibbs-sampling.md)).

**Rejection sampling first.** With 100 samples, the estimate of $P(A = 1 \mid C = 1)$ is 0.65 (traced 0.653, from 49 kept samples). The lecturer offers "sketchy math" for why it is about 0.6, from the 0.2 chance of error at each step, and says the more precise value is something you can compute ([≈21:49](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). (Not computed in the lecture: lecture 12's select–sum–divide on these tables gives exactly 0.68. $C$ equals $A$ if the bit is preserved twice or flipped twice, with probability $0.8 \times 0.8 + 0.2 \times 0.2 = 0.68$, so $P(A = 1, C = 1) = 0.5 \times 0.68 = 0.34$, $P(C = 1) = 0.5$, and the ratio is 0.68.)

## Sampling One Variable: The Conditional Distribution

Gibbs sampling, as pseudocode: start with an initialization of all the variables, then go round-robin through the variables, changing each based on all the others. To change a variable, compute its **conditional distribution given all the other variables**, and sample a new value from it ([≈22:34](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

The initialization must satisfy the evidence $C = 1$; the code uses $A = 1$, $B = 0$, $C = 1$. (The printed line says "(D = 1)", a typo; the lecturer reads it as "this should be $C = 1$.") The initialization generally does not matter too much, though for some problems it can make convergence faster or slower ([≈22:34](../raw/transcripts/13-gibbs-sampling.md), [≈23:20](../raw/transcripts/13-gibbs-sampling.md)).

To resample $B$ with $A = a$ and $C = c$ held fixed, write the conditional in terms of the joint ([≈24:06](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

$$P(B = b \mid A = a, C = c) = \frac{P(A = a, B = b, C = c)}{P(A = a, C = c)}$$

The denominator, the normalizer, is the probability of all the variables you are not sampling, and is a sum over the one variable you are:

$$P(A = a, C = c) = \sum_b P(A = a, B = b, C = c)$$

and the joint is the product of the local conditional probabilities:

$$P(A = a, B = b, C = c) = p(a) \thinspace p(b \mid a) \thinspace p(c \mid b)$$

The important point is that this does **not** instantiate the joint distribution over every assignment, which could be exponentially large. Gibbs sampling only ever looks up the probability of the current assignment and of the assignments next to it; "if I have to instantiate this for all values of A, B, and C, I might as well just brute force the whole thing." As written, though, each lookup still touches every variable, a cost the lecture comes back to ([≈24:54](../raw/transcripts/13-gibbs-sampling.md)).

In code, `joint_prob(x, var, value)` takes the current assignment `x`, substitutes `var: value` to get an updated assignment `y`, and returns `p_a.p[y["A"]] * p_b_given_a.p[y["A"], y["B"]] * p_c_given_b.p[y["B"], y["C"]]` ([≈25:40](../raw/transcripts/13-gibbs-sampling.md)–[≈27:10](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). Freezing $A = 1$ and $C = 1$ and trying both values of $B$ ([≈27:10](../raw/transcripts/13-gibbs-sampling.md), [≈27:57](../raw/transcripts/13-gibbs-sampling.md)):

| $b$ | $P(A = 1, B = b, C = 1)$ | $P(B = b \mid A = 1, C = 1)$ |
| --- | --- | --- |
| 0 | $0.5 \times 0.2 \times 0.2 = 0.02$ | 0.02 / 0.34 ≈ 0.06 |
| 1 | $0.5 \times 0.8 \times 0.8 = 0.32$ | 0.32 / 0.34 ≈ 0.94 |

The normalizer is $0.02 + 0.32 = 0.34$. The traced draw sets $B = 1$, so the assignment becomes $A = 1, B = 1, C = 1$. Intuitively, $B$ "gets pulled to one because of $A = 1$ and $C = 1$": it is more likely that the message was preserved along the way than that it flipped to 0 and back to 1. The draw is still random; the lecturer puts the chance of leaving $B$ at 0 at about 5% (the traced value is 0.059) ([≈27:57](../raw/transcripts/13-gibbs-sampling.md), [≈28:44](../raw/transcripts/13-gibbs-sampling.md)).

The thing to take away: you hold an assignment, consider one variable, evaluate the joint probability of each value it could take, and sample according to those probabilities ([≈28:44](../raw/transcripts/13-gibbs-sampling.md), [≈29:30](../raw/transcripts/13-gibbs-sampling.md)).

## The General Algorithm

The general function takes the initial assignment, the list of variables to sample, the query, and the scoring function ([≈29:30](../raw/transcripts/13-gibbs-sampling.md)–[≈32:38](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

```python
def gibbs_sampling(init_x, vars, query, joint_prob, num_iterations):
    x = dict(init_x)                   # copy the initial assignment
    counts = defaultdict(int)          # counts of query values
    for _ in range(num_iterations):
        for var in vars:
            probs = {value: joint_prob(x, var, value) for value in [0, 1]}
            probs = normalize_dict(probs)
            x[var] = sample_dict(probs)
            counts[query(x)] += 1
    return normalize_dict(counts)
```

The variables sampled are $A$ and $B$ only. Why not $C$? Because $C$ is the evidence: it is being conditioned on, so it must not change ([≈30:16](../raw/transcripts/13-gibbs-sampling.md), [≈31:03](../raw/transcripts/13-gibbs-sampling.md)). Note that the query value is counted after **every single-variable update**, not once per sweep.

Stepping through one iteration from $A = 1, B = 1, C = 1$ ([≈31:03](../raw/transcripts/13-gibbs-sampling.md)–[≈32:38](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)): for $A$, the joint probabilities of the two values are 0.08 and 0.32, normalized to 0.2 and 0.8; the draw is 1, so nothing changes, and the count records one $A = 1$. For $B$, the computation is the one just done by hand; again nothing changes, and $A = 1$ is recorded a second time.

Run for 100 iterations, the estimate of $P(A = 1 \mid C = 1)$ is 0.68, "sort of in the same ballpark" as rejection sampling's 0.65. These are random algorithms, and different ones, so they will not give exactly the same thing ([≈33:24](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

### The picture on the board

The lecturer then draws the chain ([≈34:14](../raw/transcripts/13-gibbs-sampling.md)–[≈37:20](../raw/transcripts/13-gibbs-sampling.md)). Rows are the variables $A$, $B$, $C$; columns are time. $C$ is fixed at 1 and never touched. Starting from the column $(1, 0, 1)$, each new column resamples one variable, alternating $A$, $B$, $A$, $B$, and copies the others. Each column is one joint sample. Since $C = 1$ in every column, the evidence is already taken care of, and the estimate of $P(A = 1 \mid C = 1)$ is just the fraction of ones in the $A$ row. While drawing, he changed $A$ in a column where $B$ was being sampled; a student caught it ("Yep, you're right"), which makes the rule explicit: **each column changes only one variable**.

Why only one at a time? Because it keeps the update tractable. With everything else fixed, you can consider every possible value of that one variable, "brute forcing that variable," and sample from the resulting distribution ([≈36:34](../raw/transcripts/13-gibbs-sampling.md), [≈37:20](../raw/transcripts/13-gibbs-sampling.md)).

### Running time

The running time is the number of iterations, times the number of variables (each iteration visits all of them), times the size of the domain (the number of values a variable can take, here two), times the number of variables again, because each evaluation of the joint probability goes through every variable. The slide writes it `O(#iterations * #variables * |domain| * #variables)` ([≈38:08](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

The summary for this part: update one variable at a time using its conditional distribution given all the others; compute that conditional distribution from the joint; and only sum over the domain of the variable being sampled ([≈38:08](../raw/transcripts/13-gibbs-sampling.md), [≈38:56](../raw/transcripts/13-gibbs-sampling.md)).

## The Markov Blanket

Can the last factor of the running time be avoided? Take a five-variable chain with joint probability $p(a) \thinspace p(b \mid a) \thinspace p(c \mid b) \thinspace p(d \mid c) \thinspace p(e \mid d)$. Gibbs sampling as written evaluates all five factors for every value of every variable, although only one variable changes. You cannot ignore the other variables completely, because variables interact, but the interactions are potentially **local** ([≈39:41](../raw/transcripts/13-gibbs-sampling.md), [≈40:27](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)).

Back on the telephone network, resample $A$ given $B = b$ and $C = c$ ([≈40:27](../raw/transcripts/13-gibbs-sampling.md)–[≈41:58](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

$$P(A = 0, B = b, C = c) = p(a = 0) \thinspace p(b \mid a = 0) \thinspace p(c \mid b)$$

$$P(A = 1, B = b, C = c) = p(a = 1) \thinspace p(b \mid a = 1) \thinspace p(c \mid b)$$

$$P(B = b, C = c) = \sum_a p(a) \thinspace p(b \mid a) \thinspace p(c \mid b)$$

The factor $p(c \mid b)$ does not depend on $a$. It multiplies both numerators by the same number and appears in the denominator too, so it cancels and can be dropped: "I'm basically multiplying and dividing by the same number." (The caption at [≈41:58](../raw/transcripts/13-gibbs-sampling.md) gives the illustrative values as "0.1 versus 2"; the second number is unclear.)

In general you only need to include the variables that **touch** the variable being sampled, those that interact with it directly. That set is the **Markov blanket**, and the lecture defines the Markov blanket of a node in a Bayesian network as **all its children and its parents** ([≈42:45](../raw/transcripts/13-gibbs-sampling.md), [≈43:31](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). On the board, he draws a node in a larger DAG and marks its parents and children as in the blanket, and the rest as out: those are "all the local conditional distributions that touch, depend on, this value." Writing the joint as one local conditional per node, the node appears in its own factor and in each child's factor; everything else does not depend on it ([≈43:31](../raw/transcripts/13-gibbs-sampling.md)–[≈45:07](../raw/transcripts/13-gibbs-sampling.md)).

**What about co-parents?** The question comes up in class ("so the question is, do you have to include co-parents?"): does a child's other parent have to be included? The answer is **yes**. If a child $Y$ has parents $X$ (the node being sampled) and $Z$, its local conditional distribution is $p(y \mid x, z)$, and "the parents always act as a unit": to evaluate that factor while changing $X$, you need the value of $Z$ ([≈45:07](../raw/transcripts/13-gibbs-sampling.md), [≈45:52](../raw/transcripts/13-gibbs-sampling.md)). The printed definition, "all its children and parents," does not mention them. The course rewrote the sentence the evening after the lecture, to "we need to only include local conditional probabilities that involve the variable we're sampling. Evaluating these local conditional probabilities requires knowing all involved variables" ([slides](../raw/slides/13-gibbs-sampling.md), "Changes after the lecture").

For the telephone network, the class works out the blankets aloud: MarkovBlanket($A$) = $\lbrace B \rbrace$, MarkovBlanket($B$) = $\lbrace A, C \rbrace$, MarkovBlanket($C$) = $\lbrace B \rbrace$ ([≈45:52](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). So the scoring function only needs ([≈46:38](../raw/transcripts/13-gibbs-sampling.md)):

- when sampling $A$: $p(a) \thinspace p(b \mid a)$, without $p(c \mid b)$;
- when sampling $B$: $p(b \mid a) \thinspace p(c \mid b)$, without $p(a)$.

Passing this `markov_prob` to the same `gibbs_sampling` function, from $A = 0, B = 0, C = 1$ for 100 iterations, gives an estimate of 0.63 for $P(A = 1 \mid C = 1)$ ([≈47:24](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). The lecturer notes that it "should compute exactly the same answer as Gibbs sampling with joint probability, up to floating-point precision": the conditional distributions are identical, only cheaper to compute. (The traced estimate differs from the earlier 0.68 because it is a different random run from a different starting point.)

The speedup replaces the last factor of the running time, the number of variables, with the size of the Markov blanket: `O(#iterations * #variables * |domain| * |markov_blanket|)` ([≈47:24](../raw/transcripts/13-gibbs-sampling.md), [≈48:10](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). The lecturer calls it an optimization: it "doesn't resolve any sort of fundamental issues with Gibbs sampling," but "why not do it." Gibbs sampling needs only the local conditional distributions that touch the node, which is much more efficient when Markov blankets are small. When a node is connected to many others it does not save much, "but it doesn't hurt either" ([≈48:10](../raw/transcripts/13-gibbs-sampling.md), [≈48:56](../raw/transcripts/13-gibbs-sampling.md)). See [Markov blanket](markov-blanket.md).

## Gibbs Sampling on the Alarm

Now apply it to the alarm, $P(B \mid A = 1)$, starting from an assignment that satisfies the evidence: $B = 1$, $E = 1$, $A = 1$, "everything bad happens." The variables sampled are $B$ and $E$; $A$ is evidence ([≈48:56](../raw/transcripts/13-gibbs-sampling.md), [≈49:43](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). The results:

| Iterations | Estimate of $P(B = 1 \mid A = 1)$ | Exact |
| --- | --- | --- |
| 100 | 0.65 (the caption has "6") | 0.51 |
| 200 | 0.335 (read in class as "33") | 0.51 |

Both runs start from the same initialization. "So you might wonder, this is kind of not working super well" ([≈49:43](../raw/transcripts/13-gibbs-sampling.md), [≈50:30](../raw/transcripts/13-gibbs-sampling.md)). The lesson drawn is that these sampling algorithms are approximate and "your mileage is going to vary," so before using one it is very important to know when it works and when it does not. The lecture does not analyse the alarm run itself; it moves straight to that general question.

## When Each Sampler Struggles

**Rejection sampling struggles when the evidence has low probability** ([≈51:17](../raw/transcripts/13-gibbs-sampling.md)–[≈53:38](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). The fraction of samples you keep is basically the probability of the evidence, so rare evidence leaves you very few samples, possibly none. The example is a two-node network $A \to B$ with $p(A = 0) = p(A = 1) = 0.5$ and

| $a$ | $p(B = 0 \mid a)$ | $p(B = 1 \mid a)$ |
| --- | --- | --- |
| 0 | 0.9999 | 0.0001 |
| 1 | 0.9998 | 0.0002 |

so $B = 1$ is very rare. Asked for $P(A \mid B = 1)$, you take the two rows with $B = 1$, and their ratio gives 2/3 for $A = 1$. Rejection sampling would keep about one sample in 10,000 and might need a hundred thousand samples or more for a fine estimate. Gibbs sampling has no trouble: it conditions on $B = 1$ from the start, restricts immediately to those two rows and normalizes, and "will just give you the exact answer every time" ([≈52:51](../raw/transcripts/13-gibbs-sampling.md), [≈53:38](../raw/transcripts/13-gibbs-sampling.md)).

**Gibbs sampling struggles when the variables are highly correlated** ([≈53:38](../raw/transcripts/13-gibbs-sampling.md)–[≈56:44](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). Take $A \to B$ again, with $A$ a fair coin and $B = A$ with probability one, and ask for $P(A)$ with no evidence at all. The answer should be 50/50. Rejection sampling nails it: it generates from scratch every time and, with no evidence, rejects nothing. Gibbs sampling, started at $A = 0, B = 0$, gets stuck. To resample $A$ it considers $A = 1$ with $B = 0$, which has probability zero, so it must choose $A = 0$; the same happens for $B$. The chain never leaves $(0, 0)$, and the estimate is probability one on $A = 0$, which is wrong (traced: 50 iterations, every recorded value $A = 0$).

Because Gibbs sampling changes one variable at a time, it cannot move when the other variable would have to change as well. The lecturer's analogy is a three-legged race: "you're like attached to them, and you can't really move until the other person moves." Exact equality is the extreme, but near-equality, say 0.9999, would also be "pretty bad," because the chain would very rarely switch ([≈56:44](../raw/transcripts/13-gibbs-sampling.md)).

The two examples are complementary: **rare events are hard for rejection sampling, highly correlated variables are hard for Gibbs sampling.** "And the bad news is that in most real-world problems both are true," so both will struggle, but knowing why a problem is hard helps ([≈56:44](../raw/transcripts/13-gibbs-sampling.md), [≈57:31](../raw/transcripts/13-gibbs-sampling.md)).

### Further reading

Gibbs sampling is an example of an MCMC algorithm. A more general one, **Metropolis–Hastings**, uses a proposal distribution to guide the search more smartly. There is "some really beautiful theory" on how fast Markov chains run, which has to do with **mixing times** (the slide: use mixing times to study the effective running time, that is, how correlated the samples are). In practice Gibbs sampling is fairly commonly used because it is simple and effective, but it can be slow. The only simpler method, rejection sampling, generally does not get used, because the probability of hitting the evidence is usually low ([≈57:31](../raw/transcripts/13-gibbs-sampling.md), [≈58:16](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). See [Gibbs sampling](gibbs-sampling.md).

## Conditional Independence

The inference algorithms so far have been mostly agnostic to the network's structure: rejection sampling samples everything and rejects; Gibbs sampling paid a little attention through the Markov blanket. But a Bayesian network is simultaneously a **probabilistic** object, a distribution over variables, and a **combinatorial** one, a graph, and there is a connection between the graph's properties and the distribution's, in particular **independence** ([≈58:16](../raw/transcripts/13-gibbs-sampling.md)–[≈59:50](../raw/transcripts/13-gibbs-sampling.md)). See [conditional independence](conditional-independence.md) for the same material as a topic page.

Two random variables $A$ and $B$ are **independent** if and only if

$$P(A = a, B = b) = P(A = a) \thinspace P(B = b) \text{ for all } a, b$$

The lecture works four small networks, some on the board ([≈59:50](../raw/transcripts/13-gibbs-sampling.md)–[≈1:06:48](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

**Example 1: $A$ and $B$ with no edge**, "the world's second simplest Bayesian network." The joint is $p(a) \thinspace p(b)$, which pattern-matches the definition: independent. Two nodes that are not connected are independent, and "that's generally the right intuition you should have," though it gets more complicated ([≈1:00:37](../raw/transcripts/13-gibbs-sampling.md), [≈1:01:23](../raw/transcripts/13-gibbs-sampling.md)).

**Example 2: $A \to B$.** The joint is $p(a) \thinspace p(b \mid a)$, and $B$ depends on $A$: not independent ([≈1:01:23](../raw/transcripts/13-gibbs-sampling.md)).

**Example 3: $A \to C \leftarrow B$** (the slide writes "A, B → C"). $A$ and $B$ are connected through $C$, yet they are independent. Marginalize $C$ out ([≈1:02:09](../raw/transcripts/13-gibbs-sampling.md)–[≈1:03:43](../raw/transcripts/13-gibbs-sampling.md)):

$$P(A = a, B = b) = \sum_c p(a) \thinspace p(b) \thinspace p(c \mid a, b) = p(a) \thinspace p(b) \sum_c p(c \mid a, b) = p(a) \thinspace p(b)$$

The last step is the key point: summing a local conditional distribution over all values of the variable on the left of the bar gives one. "This is the part that's a little bit tricky about Bayesian networks": you cannot just look at whether two variables are connected, but this is "the only real special case that you have to think about." It is the alarm again: burglary and earthquake are independent, and someone adding a variable that depends on both "shouldn't change anything about the underlying causes' independence" ([≈1:03:43](../raw/transcripts/13-gibbs-sampling.md), [≈1:04:29](../raw/transcripts/13-gibbs-sampling.md)). (The slide's first line for this example prints the factor as $p(b \mid a)$; the lecturer says $p(b)$, as does the slide's next line.)

**Example 4: $A \leftarrow C \to B$** (the slide writes "C → A, B"). The lecturer polls the class; the answer is **not independent**. "Don't be fooled": upside down it looks like example 3, but the direction matters, and the two are "almost completely different Bayesian networks." The joint is $p(c) \thinspace p(a \mid c) \thinspace p(b \mid c)$, and $P(A = a, B = b) = \sum_c p(c) \thinspace p(a \mid c) \thinspace p(b \mid c)$ does not simplify ([≈1:05:16](../raw/transcripts/13-gibbs-sampling.md), [≈1:06:02](../raw/transcripts/13-gibbs-sampling.md)). A useful habit: think of a special case. If $C$ is a coin flip and $A = C$ and $B = C$, then $A$ literally equals $B$, so they are clearly not independent ([≈1:06:48](../raw/transcripts/13-gibbs-sampling.md)).

**Conditional independence** is the same definition with $C$ conditioned on in every term. $A$ and $B$ are conditionally independent given $C = c$ if and only if

$$P(A = a, B = b \mid C = c) = P(A = a \mid C = c) \thinspace P(B = b \mid C = c) \text{ for all } a, b$$

([≈1:06:48](../raw/transcripts/13-gibbs-sampling.md), [≈1:07:35](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)). Revisiting the two three-node examples:

- **Example 4 given $C$: conditionally independent.** The conditional is the joint divided by $p(c)$, which leaves $p(a \mid c) \thinspace p(b \mid c)$, "literally the definition." Intuitively, a coin is flipped, then $A$ and $B$ each do something that depends only on the coin; once you know the coin, they are doing independent things ([≈1:07:35](../raw/transcripts/13-gibbs-sampling.md), [≈1:08:23](../raw/transcripts/13-gibbs-sampling.md)).
- **Example 3 given $C$: not conditionally independent.** After another poll, the answer is no; "hopefully you're anchored to the burglar alarm earthquake example." $C$, $A$ and $B$ are all combined in the arbitrary function $p(c \mid a, b)$, so the conditional does not factor ([≈1:09:09](../raw/transcripts/13-gibbs-sampling.md), [≈1:09:56](../raw/transcripts/13-gibbs-sampling.md)).

These two are the examples to remember, because they look alike and behave oppositely. In example 3, $A$ and $B$ are **marginally independent but not conditionally independent**; in example 4, they are **marginally dependent but conditionally independent** given $C$. "This is a good primitive to have in your head" ([≈1:09:56](../raw/transcripts/13-gibbs-sampling.md), [≈1:10:44](../raw/transcripts/13-gibbs-sampling.md)).

**The alarm** is example 3: $B$ and $E$ are independent, but not conditionally independent given $A = 1$, "which is how you get [explaining away](explaining-away.md)," seen last lecture ([≈1:10:44](../raw/transcripts/13-gibbs-sampling.md)).

### A general procedure

For a large network, the lecture gives a procedure for deciding whether $A$ and $B$ are independent given $C$ ([≈1:10:44](../raw/transcripts/13-gibbs-sampling.md)–[≈1:12:16](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

1. **Shade** the variables $C$. Conditioning on something is "kind of like blocking a path."
2. **Recursively remove any non-shaded leaves.** This deals with the case where $A$ and $B$ are really independent but "there's some things here that makes it look like it's not independent." You don't have to erase them from the picture; just think of them as not there.
3. **Connect the parents to each other**, which you can remember as "marrying the parents."
4. **Return whether there is a path from $A$ to $B$** that doesn't go through any shaded nodes. No such path means independent.

(The source numbers the last two steps "3." and "3.")

On the medical-diagnosis network from lecture 12 (cold $C$, allergies $A$, cough $H$, itchy eyes $I$, with edges $C \to H$, $A \to H$, $A \to I$), the class works through ([≈1:12:16](../raw/transcripts/13-gibbs-sampling.md)–[≈1:13:50](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)):

- **$C$ and $A$: independent.** Nothing is shaded, so $H$ and $I$ are unshaded leaves and are removed, leaving $C$ and $A$ with no path between them.
- **$C$ and $I$: independent.** $H$ is an unshaded leaf and is removed, leaving $C$ alone and the subgraph $A \to I$, with no path between them.
- **$C$ and $I$ given $A$: independent.** Shading $A$ blocks "the way to go over there."
- Shading $H$ as well, the two are still blocked; the slide's corresponding line is "C and I are independent given H, A."

The slide also printed "C and H are independent given A." That line was not discussed in class, and it is not true of this network: $C$ is a parent of $H$, so the two are joined by an edge that no shading can block. The course deleted it from the source the evening after the lecture ([slides](../raw/slides/13-gibbs-sampling.md), "Changes after the lecture"). The lecturer suggests doing more of these examples next time ([≈1:13:50](../raw/transcripts/13-gibbs-sampling.md)).

## Summary and What Comes Next

The summary ([≈1:13:50](../raw/transcripts/13-gibbs-sampling.md)–[≈1:15:27](../raw/transcripts/13-gibbs-sampling.md), [slides](../raw/slides/13-gibbs-sampling.md)): probabilistic inference answers queries on the database that is the Bayesian network. **Exact** inference forms the joint distribution, then marginalizes and conditions (einops); it is exact, but its complexity can be exponential in the number of nodes. **Approximate** inference by rejection sampling or Gibbs sampling has complementary strengths and weaknesses. **Conditional independence**, whether two variables are independent given evidence, reduces to a graph property: are the two variables connected after some processing of the graph?

So far the Bayesian network has been given. Next time: how to **learn** its parameters, the probability tables themselves ([≈1:15:27](../raw/transcripts/13-gibbs-sampling.md)).

---

- **[Slides](../raw/slides/13-gibbs-sampling.md)**: the full content of `gibbs_sampling.py` as taught (pinned to commit ed66b66, "add Gibbs sampling", which landed three minutes before the lecture), every traced table and sampling result, and the three changes the course made the evening after the lecture. Rendered lecture: <https://stanford-cs221.github.io/autumn2025-lectures/?trace=gibbs_sampling> (this may now show a later revision, not the version taught). The two figures, `alarm-bayes.png` and `medical-bayes.png`, are lecture 12's and are described in [lecture 12's slide file](../raw/slides/12-bayesian-networks-i.md).
- **[Transcript](../raw/transcripts/13-gibbs-sampling.md)**: edited and timestamped. Verbatim captions are in `original/`.
- Topic pages: [Gibbs sampling](gibbs-sampling.md), [Markov blanket](markov-blanket.md), [conditional independence](conditional-independence.md); also [rejection sampling](rejection-sampling.md), [probabilistic inference](probabilistic-inference.md), [Bayesian networks](bayesian-networks.md) and [explaining away](explaining-away.md).
- Previous lecture: [Lecture 12 — Bayesian Networks I](12-bayesian-networks-i.md).
