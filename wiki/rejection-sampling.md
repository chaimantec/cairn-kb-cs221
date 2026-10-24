---
title: Rejection Sampling — Approximate Inference with Probabilistic Programs
---

# Rejection Sampling

**Rejection sampling** estimates a conditional probability $P(\text{query} \mid \text{evidence})$ by simulation: draw a large number of samples from the joint distribution, throw away every sample that does not match the evidence, and take the histogram of the query over the samples that remain ([lecture 12](12-bayesian-networks-i.md), [≈1:06:53](../raw/transcripts/12-bayesian-networks-i.md)). It is the course's first algorithm for approximate [probabilistic inference](probabilistic-inference.md). The slide's verdict is "very flexible and very slow" ([slides](../raw/slides/12-bayesian-networks-i.md)).

## Probabilistic Programs

Rejection sampling needs only a way to draw samples, and the lecture gets one by writing a [Bayesian network](bayesian-networks.md) as a **probabilistic program**: code that returns one random sample from the joint distribution ([≈1:04:34](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:06:07](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). Probabilistic programming is a whole field exploring the interaction between programs and distributions. The alarm network becomes:

```python
def alarm():
    B = Bernoulli(0.05)  # Burglary
    E = Bernoulli(0.05)  # Earthquake
    A = B or E           # Alarm
    return {"B": B, "E": E, "A": A}
```

where `Bernoulli(prob)` returns 1 with probability `prob` and 0 otherwise. Each line samples one variable given the values of its parents, so the program is the network, written in an unusual but formal way. The lecturer likes how transparent it is: three lines of code ([≈1:06:07](../raw/transcripts/12-bayesian-networks-i.md)). Programs represent distributions, and "once you can sample you can do a lot of things" ([≈1:13:59](../raw/transcripts/12-bayesian-networks-i.md), [≈1:14:45](../raw/transcripts/12-bayesian-networks-i.md)).

## The Algorithm

Evidence and query are generalized from "variable = value" to **arbitrary functions of a sample** ([≈1:06:53](../raw/transcripts/12-bayesian-networks-i.md), [≈1:07:39](../raw/transcripts/12-bayesian-networks-i.md)). The query pulls out the value of interest, and the evidence is a test that says whether to keep the sample. For $P(B \mid A = 1)$:

```python
query = lambda sample: sample["B"]
evidence = lambda sample: sample["A"] == 1
```

Rejection sampling itself is "literally five lines of code, for some definition of five" ([≈1:07:39](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:09:14](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

```python
def rejection_sampling(program, query, evidence, num_samples):
    counts = defaultdict(int)          # how many of each query value we got
    for _ in range(num_samples):
        sample = program()
        if evidence(sample):
            counts[query(sample)] += 1
    total_count = sum(counts.values())
    return {q: counts[q] / total_count for q in counts}
```

## Results from the Lecture

All three examples were run in class, and the numbers match the trace recorded in the [slide file](../raw/slides/12-bayesian-networks-i.md), where the exact answers also are.

| Example | Query | Samples (kept) | Estimate | Exact |
| --- | --- | --- | --- | --- |
| Alarm | $P(B = 1 \mid A = 1)$ | 10 (1) | 1.0 | 0.513 |
| Alarm | $P(B = 1 \mid A = 1)$ | 1,000 (51) | 0.569 | 0.513 |
| Medical diagnosis | $P(C = 1 \mid H = 1)$ | 200 (67) | 0.269 | 0.278 |
| Hidden Markov model | $P(H_3 \mid E_5 = 2)$ | 200 (53) | 0: 0.226, 1: 0.698, 2: 0.075 | not given |

The kept counts follow from the traced fractions: 29/51, 18/67, and 12/53, 37/53 and 4/53. In class, the ten-sample run kept a single sample ("oh, I get one, I guess"), and the 1,000-sample estimate, read as 0.56, is "close, but not exactly right, because this is just a sampling algorithm" ([≈1:08:26](../raw/transcripts/12-bayesian-networks-i.md), [≈1:09:14](../raw/transcripts/12-bayesian-networks-i.md)). The medical estimate, read as 0.26, is closer with only 200 samples, because the evidence (a cough) is far more common than an alarm ([≈1:11:33](../raw/transcripts/12-bayesian-networks-i.md)).

The **hidden Markov model** shows off how succinct the approach is ([≈1:11:33](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:13:59](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). Hidden positions $H_1, \ldots, H_5$ of a moving object form a chain, and each emits a noisy sensor reading $E_t$. Starting at 0, the object advances one position with probability 0.5 at each step; the reading is the position plus 0 or 1, with probability 0.5 each. To ask where the object was at time 3 given a reading of 2 at time 5, the code tests `sample["E"][4] == 2` and records `sample["H"][2]`, since Python lists are zero-based. Exact inference here would be "fairly complicated," but rejection sampling treats the program as a black box. The answer: most likely position 1, possibly 0.

## Strengths and Weaknesses

- **Flexible.** It works for any distribution you can sample from, with any evidence test and any query ([≈1:12:20](../raw/transcripts/12-bayesian-networks-i.md), [≈1:17:06](../raw/transcripts/12-bayesian-networks-i.md)).
- **Consistent.** As the number of samples goes to infinity, the estimate converges to the true probability; "if you are very patient" it is exact in the limit ([≈1:10:00](../raw/transcripts/12-bayesian-networks-i.md)).
- **Slow when the evidence is rare.** It samples from the prior, so if the evidence has probability 0.001, almost every sample is wasted ([≈1:10:00](../raw/transcripts/12-bayesian-networks-i.md)). The alarm runs show it: 51 of 1,000 samples kept.

Estimating by sampling is a recurring move in the course; the lecturer compares it to sampling rollouts in policy evaluation ([≈1:10:00](../raw/transcripts/12-bayesian-networks-i.md); see [Monte Carlo evaluation](monte-carlo-evaluation.md)). Lecture 12 ends by promising better inference algorithms next time ([≈1:17:06](../raw/transcripts/12-bayesian-networks-i.md)).

## Related Pages

- [Lecture 12](12-bayesian-networks-i.md) — the lecture, with all three examples
- [Probabilistic inference](probabilistic-inference.md) — the exact computation this approximates
- [Bayesian networks](bayesian-networks.md) — the models being sampled
- [Monte Carlo evaluation](monte-carlo-evaluation.md) — estimation by sampling in the MDP unit
