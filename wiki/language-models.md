---
title: Language Models — Distributions over Sequences, Autoregression, n-grams and Neural Next-Token Prediction
---

# Language Models

A **language model** is a model of language, and for CS221's purposes language is "just a (structured) sequence of 'characters'" ([lecture 17](17-language-models.md), [slide 13](../raw/slides/17-language-models.md)): English, Tamil, Python or sign language. The structure comes from a **vocabulary**, the possible set of characters, and a **grammar**, the rules or conventions for how they follow each other. A language model learns that structure and learns to predict or produce sequences of the language. Its most familiar use is completing text: given *The stock market crashed and investors*, it should put high probability on *panicked* or *celebrated*, low probability on *golfing* (wrong semantics and grammar) and almost none on 桌子 (wrong vocabulary) ([≈7:00](../raw/transcripts/17-language-models.md)–[≈8:34](../raw/transcripts/17-language-models.md)).

Notation, as in lecture 17: $T$ is the sequence length, $D$ the embedding dimension, $V$ the vocabulary size, $B$ the batch size; $x_t$ is the token at position $t$ and $x_{1:t-1}$ the tokens before it.

## A Distribution over Sequences

Formally, a language model is **a distribution over sequences** ([slide 34](../raw/slides/17-language-models.md)). It assigns each whole sequence a probability, as a share of all possible sequences, not only of one prefix's completions: say 0.02 for *The stock market crashed and investors panicked*, 0.015 with *celebrated* and 0.0001 with *golfing* ([≈14:41](../raw/transcripts/17-language-models.md)–[≈15:29](../raw/transcripts/17-language-models.md)). The **chain rule** of probability unpacks the joint into one conditional per position:

$$P(x_1, \ldots, x_T) = \prod_i P(x_i \mid x_{1:i-1}).$$

So "we only need a model that, given past context, predicts 1 word (token) at a time" ([slide 35](../raw/slides/17-language-models.md)). A model built this way is **autoregressive**. [Lecture 12](12-bayesian-networks-i.md) drew the same factorization as a [Bayesian network](bayesian-networks.md) whose variables are the tokens, with every token depending on all earlier ones and the Transformer supplying each local conditional distribution. That view also allows inference backwards, from a response to the prompts likely to have produced it, which has an application in jailbreaking but is "in general it's exponentially hard" ([≈1:02:15](../raw/transcripts/12-bayesian-networks-i.md)–[≈1:03:49](../raw/transcripts/12-bayesian-networks-i.md)).

## Next-Token Prediction Is Multiclass Classification

Predicting the next token is multiclass classification over the vocabulary ([lecture 17](17-language-models.md), [≈10:53](../raw/transcripts/17-language-models.md)). [Lecture 5](05-search-i.md) said the same: "probably a hundred thousand classes, but mathematically it's exactly the same thing that we saw last week" ([≈1:12:22](../raw/transcripts/05-search-i.md)). In [tensor](tensors.md) terms ([slides 22–33](../raw/slides/17-language-models.md)):

- The text becomes a vector of token IDs, shape $(T,)$, using a vocabulary that maps strings to indices.
- An embedding turns each ID into a vector, shape $(T, D)$.
- The model outputs a probability for every word of the vocabulary, shape $(V,)$ for the next word, or $(T, V)$ when it predicts at every position at once, the first row being the distribution after the first token, the second after the first two, and so on.
- With a batch of $B$ sequences the shapes are $(B, T)$, $(B, T, D)$ and $(B, T, V)$.

**Generating text** is autoregression ([≈13:10](../raw/transcripts/17-language-models.md)–[≈13:56](../raw/transcripts/17-language-models.md)): take the distribution at the last position, choose a token, either the most likely (**greedy decoding**) or by **sampling** according to the probabilities, append it to the input and predict again. Lecture 5 casts the same loop as a [search problem](search-problems.md) (state: prompt plus the response so far; action: next token; cost: negative log probability) and spends extra test-time compute on it with $\text{best-of-}n$ ([≈1:14:41](../raw/transcripts/05-search-i.md); see the [beam search](beam-search.md) page).

**Training** a neural language model needs no separate labels: the target at each position is the next token of the input itself, so the labels are the input **shifted by one position** ([≈23:11](../raw/transcripts/17-language-models.md)–[≈23:57](../raw/transcripts/17-language-models.md), [slide 48](../raw/slides/17-language-models.md)). That gives a classification loss, and [backpropagation](backpropagation.md) and [gradient descent](gradient-descent.md) do the rest. Minimizing that cross-entropy loss "is exactly the same as maximum likelihood" ([lecture 14](14-bayesian-networks-learning.md), [≈53:14](../raw/transcripts/14-bayesian-networks-learning.md); see [maximum likelihood](maximum-likelihood.md)).

## Objectives

Lecture 17 names two objectives ([slides 36–38](../raw/slides/17-language-models.md)):

- **Next-token prediction (NTP)** learns $P(x_t \mid x_1, \ldots, x_{t-1})$, sequentially. It is the most prevalent objective, and the one used for pre-training the models people use today ([≈16:59](../raw/transcripts/17-language-models.md)–[≈17:45](../raw/transcripts/17-language-models.md)).
- **Masked language modeling (MLM)** fills in a missing token from both sides, for example $P(x_t \mid x_1, \ldots, x_{t-1}, x_{t+1}, \ldots, x_T)$: for `import ___ as np`, `numpy` and `jax.numpy` are plausible and `import` is the wrong grammar. BERT popularized it in 2018; it is not used as much today ([≈17:45](../raw/transcripts/17-language-models.md)–[≈18:32](../raw/transcripts/17-language-models.md)).

## Three Ways to Implement One

Lecture 17 builds up three implementations ([≈19:18](../raw/transcripts/17-language-models.md)–[≈23:57](../raw/transcripts/17-language-models.md), [slides 39–48](../raw/slides/17-language-models.md)).

1. **Count whole sequences.** Collect a pile of documents, count each sequence, and use the empirical frequency as its probability. It fails because the exact string may never appear: $P(\text{string}) = 0$ most of the time, especially for long text.
2. **Count n-grams.** Assume each word depends only on the previous $n - 1$ words, "the Markov assumption but for $n$ instead of 1" (see [hidden Markov models](hidden-markov-models.md)), so with $n = 3$ the factor for *panicked* becomes $P(\text{panicked} \mid \text{The} \ldots \text{investors}) \approx P(\text{panicked} \mid \text{and, investors})$. Short n-grams do appear in the documents, so the counts are non-zero; in the lecture's four snippets *stock market* occurs twice, once followed by *crashed*, giving $P(\text{crashed} \mid \text{stock, market}) = 1/2$. The result is a giant lookup table of probabilities. Dividing counts like this is the count-and-normalize estimate that [lecture 14](14-bayesian-networks-learning.md) derived for Bayesian networks, and its zero-count problem is what [Laplace smoothing](laplace-smoothing.md) addresses there; lecture 17 itself mentions neither.
3. **Train a neural network** that takes the sequence and outputs next-token probabilities, as above. The architecture matters only in that it should not be the bottleneck: lecture 17 shows why a plain MLP would be one (its parameter count grows as $O(DVT^2)$, its weights cannot favour different positions depending on the input, and it cannot reuse computation) and names the Transformer's parameter sharing across positions, attention and KV cache as the fixes ([slides 76–79](../raw/slides/17-language-models.md)).

## Why Model Language

Lecture 17 gives three reasons ([slide 70](../raw/slides/17-language-models.md)):

1. **Many tasks are just language modeling**: writing an email, writing code, replying to an advisor are all sequence completion.
2. **Language modeling enables multi-task learning**: to predict the next token of a Wikipedia biography the model must memorize facts, of a math page it must learn math, of a logic puzzle it must reason (or memorize). One objective teaches all of them, the observation of GPT-2's *Language Models are Unsupervised Multitask Learners* (2019). The logic homework solved such puzzles with [first-order logic](first-order-logic.md); a language model instead just puts high probability on the right answer as the next token ([slide 61](../raw/slides/17-language-models.md)).
3. **Language models scale**: one model fits all the tasks, and the loss keeps falling as data, parameters and compute grow; see [scaling laws](scaling-laws.md).

## From Model to Product

A language model at scale goes through [pre-training](pre-training.md), next-token prediction on trillions of filtered web tokens, which yields a knowledgeable "autocomplete on steroids", and [post-training](post-training.md), which makes it follow instructions and refuse harmful requests. Its vocabulary comes from a [tokenizer](tokenization.md). Today "language model" usually means a large language model built by an organization, sold behind an API (closed frontier models), released as weights (open-weight models), or released with data and code too (open-source models, mostly academic) ([lecture 17](17-language-models.md), [slides 111–115](../raw/slides/17-language-models.md)). [Lecture 1](01-course-overview.md) places this in the history of AI, from pre-trained models (ELMo, BERT, T5) through scaling (GPT-2, GPT-3, Llama 3) to reasoning models ([≈31:44](../raw/transcripts/01-course-overview.md)–[≈34:08](../raw/transcripts/01-course-overview.md)).

## Lectures

- [Lecture 1](01-course-overview.md): language models in the history of AI; industrialization.
- [Lecture 3](03-learning-ii.md): turning text into tensors (tokenization, one-hot vectors).
- [Lecture 5](05-search-i.md): a language model as multiclass classification; test-time compute as search.
- [Lecture 12](12-bayesian-networks-i.md): language models as Bayesian networks; backward inference and jailbreaking.
- [Lecture 14](14-bayesian-networks-learning.md): cross entropy as maximum likelihood, including for language models.
- [Lecture 17](17-language-models.md): the full treatment, everything on this page.
