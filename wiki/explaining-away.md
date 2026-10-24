---
title: Explaining Away — How One Cause Lowers the Probability of Another
---

# Explaining Away

**Explaining away** is the reasoning pattern in which two causes of an effect compete to explain it: once you observe the effect, also learning that one cause occurred **lowers** the probability of the other ([lecture 12](12-bayesian-networks-i.md), [≈43:37](../raw/transcripts/12-bayesian-networks-i.md), [≈44:23](../raw/transcripts/12-bayesian-networks-i.md)). It holds **even when the two causes are independent**, which is what makes it feel paradoxical. The lecturer calls it "quite general and powerful" and "a defining feature of a Bayesian network," and stresses that it is not an extra assumption: it is a qualitative pattern that "follows from the math" of a [Bayesian network](bayesian-networks.md) ([≈42:50](../raw/transcripts/12-bayesian-networks-i.md), [≈46:42](../raw/transcripts/12-bayesian-networks-i.md)).

## The Pattern

Two causes $B$ and $E$ each positively influence an effect $A$ ([≈44:23](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). Condition on the effect, $A = 1$. Further conditioning on one cause, $E = 1$, reduces the probability of the other:

$$P(B = 1 \mid A = 1, E = 1) < P(B = 1 \mid A = 1)$$

Here $P(B = 1 \mid A = 1)$ is the probability of $B$ given the effect alone, and $P(B = 1 \mid A = 1, E = 1)$ is the same once you also know $E$ happened.

## The Alarm: Is It a Burglary?

Burglaries $B$ and earthquakes $E$ are independent, each with probability 0.05, and either one sets off your home alarm $A$. You are away, your phone reports the alarm, then the news reports an earthquake near your house. Does that change the probability of a burglary ([≈24:06](../raw/transcripts/12-bayesian-networks-i.md)–[≈25:44](../raw/transcripts/12-bayesian-networks-i.md))? Intuition says it should go down, but the two are independent, so "what do burglaries and earthquakes have to do with each other? Nothing, right?" The lecture calls this a paradox and resolves it by computing ([≈39:44](../raw/transcripts/12-bayesian-networks-i.md), [≈41:17](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)):

| What you know | $P(B = 1 \mid \ldots)$ |
| --- | --- |
| nothing | 0.05 |
| the alarm went off | 0.05 / 0.0975 ≈ 0.513 |
| the alarm went off, and there was an earthquake | 0.0025 / 0.05 = 0.05 |

The alarm alone makes a burglary "a little bit over 50%" likely; adding the earthquake sends it back to its prior. The intuition ([≈42:05](../raw/transcripts/12-bayesian-networks-i.md)): why did the alarm sound? It must have been an earthquake or a burglary. Once you see the earthquake, it explains the alarm, which "takes the pressure off of the burglary and puts it on the earthquake."

**What happened to independence?** $B$ and $E$ are independent **only as long as you do not observe $A$**. Observing a variable downstream of two independent variables makes the upstream variables dependent ([≈42:50](../raw/transcripts/12-bayesian-networks-i.md)). Lecture 13 states this in the language of [conditional independence](conditional-independence.md): in the shape $A \to C \leftarrow B$, $A$ and $B$ are marginally independent but not conditionally independent given $C$; in the alarm, $B$ and $E$ are independent but not conditionally independent given $A = 1$, "which is how you get explaining away" ([lecture 13](13-gibbs-sampling.md), [≈1:09:56](../raw/transcripts/13-gibbs-sampling.md), [≈1:10:44](../raw/transcripts/13-gibbs-sampling.md)).

## Medical Diagnosis: Explaining Away at One Remove

Explaining away also works through a variable that is not a cause at all ([≈53:48](../raw/transcripts/12-bayesian-networks-i.md)–[≈56:09](../raw/transcripts/12-bayesian-networks-i.md), [slides](../raw/slides/12-bayesian-networks-i.md)). Cold $C$ and allergies $A$ are independent causes of cough $H$, and allergies also cause itchy eyes $I$ (edges $C \to H$, $A \to H$, $A \to I$). With $p(C = 1) = 0.1$, $p(A = 1) = 0.2$, and each symptom following its causes with probability 0.9:

| What you know | $P(C = 1 \mid \ldots)$ |
| --- | --- |
| you cough | 0.09 / 0.324 ≈ 0.28 |
| you cough and have itchy eyes | 0.0234 / 0.1764 ≈ 0.13 |

Adding itchy eyes almost halves the probability of a cold, though itchy eyes have no edge to the cough. The chain of reasoning: itchy eyes raise the probability of allergies, and allergies, a cause of the cough, then explain the cough away from the cold ([≈56:09](../raw/transcripts/12-bayesian-networks-i.md)). When the lecturer polled the class beforehand there were "a lot of tentative hands": "that's why we have math" ([≈54:36](../raw/transcripts/12-bayesian-networks-i.md)). Hardly anyone would confidently guess how itchy eyes should change things, but with the network defined, "all these reasoning patterns pop out" ([≈56:09](../raw/transcripts/12-bayesian-networks-i.md), [≈56:54](../raw/transcripts/12-bayesian-networks-i.md)).

## Why It Matters

Explaining away is one case of a general behaviour: in a Bayesian network, observed evidence **propagates**, raising and lowering the probabilities of other nodes, and in a network with a hundred variables and seventeen observed the interactions are too tangled to follow by intuition ([≈56:54](../raw/transcripts/12-bayesian-networks-i.md), [≈57:40](../raw/transcripts/12-bayesian-networks-i.md)). The lecturer's case for Bayesian networks rests partly on this: earlier heuristic ways of reasoning about uncertainty were "inconsistent and kind of broken," and only mathematical clarity made patterns like this make sense ([≈43:37](../raw/transcripts/12-bayesian-networks-i.md)).

## Related Pages

- [Lecture 12](12-bayesian-networks-i.md) — both examples, worked step by step
- [Bayesian networks](bayesian-networks.md) — the alarm and medical-diagnosis networks and how they are defined
- [Probabilistic inference](probabilistic-inference.md) — the select–sum–divide computation behind each number above
- [Conditional independence](conditional-independence.md) — the general rule: a common effect, once observed, makes its causes dependent
