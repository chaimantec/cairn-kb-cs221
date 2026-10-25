---
title: Scaling Laws — Loss vs. Compute, Data and Parameters; the 6ND Rule; Kaplan and Chinchilla
---

# Scaling Laws

"Language models scale" is the third of [lecture 17](17-language-models.md)'s reasons why modeling language is a good idea ([slide 63](../raw/slides/17-language-models.md)): with lots of data and a big model you can (1) fit all the tasks at once without specialization, and (2) the loss keeps going down. As the lecturer puts it, "every time you double the compute, double the model size, you just expect the loss to drop," with no sign of stopping for practical purposes today, though some people would say we are starting to hit something; the captions are unclear on what ([≈31:36](../raw/transcripts/17-language-models.md)–[≈32:23](../raw/transcripts/17-language-models.md)). **Scaling laws** are the empirical curves that make this quantitative.

Notation: $L$ is the test loss (cross entropy), $C$ the training compute, $D$ the dataset size in tokens and $N$ the number of parameters. On this page $D$ is the token count, as in the scaling-law papers, not the embedding dimension of [language models](language-models.md).

## One Model Instead of Many

Chat, coding and translation used to be separate language models: Google's neural machine translation system, and OpenAI's `davinci-codex` for code. Today a single model, GPT-5.1, does chat, code, tool use, translation and summarization ([≈32:23](../raw/transcripts/17-language-models.md)–[≈33:54](../raw/transcripts/17-language-models.md), [slides 64–65](../raw/slides/17-language-models.md)). The translation system had 380 million parameters, considered big at the time. The newer model's size is not official, but it is possibly about 100 times larger, and it outperforms Llama 3 405B, which is about 1,000 times larger than the translation model.

## Kaplan et al. (OpenAI, 2020)

*Scaling Laws for Neural Language Models* found that "the loss scales as a power-law with model size, dataset size, and the amount of compute used for training, with some trends spanning more than seven orders of magnitude," while architectural details such as width or depth matter little within a wide range ([slide 66](../raw/slides/17-language-models.md), from the abstract). Its Figure 1 ([slide 67](../raw/slides/17-language-models.md)) plots test loss against each factor on log axes, and each is a straight line, a power law:

$$L = \left(C_{\min} / 2.3 \cdot 10^{8}\right)^{-0.050}, \qquad L = \left(D / 5.4 \cdot 10^{13}\right)^{-0.095}, \qquad L = \left(N / 8.8 \cdot 10^{13}\right)^{-0.076},$$

with compute in PF-days and parameters counted without the embeddings. In the compute panel, each faint curve is one training run, and the lower envelope of all the runs follows the fitted line. Over the plotted ranges the loss falls from about 6.4 to about 2.8 with compute, from about 4.1 to 2.75 with data, and from about 5.7 to 2.3 with parameters. The figure's caption: "For optimal performance all three factors must be scaled up in tandem."

## Chinchilla (DeepMind, 2022)

*Training Compute-Optimal Large Language Models* fixed a compute budget, trained models of different sizes on as many tokens as that budget allows, and plotted the loss against model size ([≈33:54](../raw/transcripts/17-language-models.md)–[≈34:40](../raw/transcripts/17-language-models.md), [slides 68–69](../raw/slides/17-language-models.md)). Each budget, from $6 \times 10^{18}$ to $3 \times 10^{21}$ FLOPs, gives a U-shaped "IsoFLOP" curve with a clear valley: for each budget there is an optimal model size, smaller budgets have higher valleys at smaller models, and the largest budget reaches a loss of about 2.2 at a few billion parameters. Drawing a line through the valleys, "they just don't stop. They just keep going down": it predicts the compute-optimal model size and token count, and the loss, for larger budgets. For the compute budget of DeepMind's earlier Gopher model the projection is 63B parameters trained on 1.4T tokens (the slide gives the two numbers; their ratio is about 22 tokens per parameter).

## The 6ND Rule and the Cost of Scale

Lecture 17 also uses a rule of thumb for training compute ([slide 6](../raw/slides/17-language-models.md)), $C \approx 6ND$. For Llama 3, with $N = 405 \times 10^9$ parameters and $D = 16 \times 10^{12}$ tokens,

$$6 \times (405 \times 10^9) \times (16 \times 10^{12}) \approx 3.9 \times 10^{25} \text{ FLOPs},$$

close to the $3.8 \times 10^{25}$ that Meta reported. At $4.32 \times 10^{19}$ FLOPs per H100-day that is about 880,000 H100-days, 650,000 years on a MacBook M2, or about \$42 million at \$2 per H100-hour, for one pre-training run with no failures ([≈2:23](../raw/transcripts/17-language-models.md)–[≈3:09](../raw/transcripts/17-language-models.md)). Qwen3's 36 trillion training tokens are about 27 trillion words, which would take about a million years to type ([slide 5](../raw/slides/17-language-models.md)). This is the industrialization lecture 17 opens with, and that [lecture 1](01-course-overview.md) also describes (GPT-4 at about \$100M to train, xAI's Colossus with 200K H100s, Stargate's \$500B, [≈34:08](../raw/transcripts/01-course-overview.md)).

## Capabilities Scale Too

Scale improves more than the loss. The GPT-3 paper evaluated models from 0.1B to 175B parameters on 42 benchmarks with no task-specific training, and the average accuracy rises with size in every setting ([slide 86](../raw/slides/17-language-models.md); see [pre-training](pre-training.md)): at 175B parameters it is about 57 with a few examples in the prompt (few-shot), 51 with one (one-shot) and 43 with none (zero-shot), against roughly 23–25 at 0.1B. Few-shot performance grows fastest, which the paper reads as larger models being better at in-context learning.

## Scaling at Test Time

Compute can also be spent at inference. [Lecture 5](05-search-i.md) presented *Large Language Monkeys: Scaling Inference Compute with Repeated Sampling*, in which $\text{best-of-}n$ sampling with a small model can compete with a much larger one ([≈1:13:54](../raw/transcripts/05-search-i.md)), and lecture 17 lists **test-time scaling** (reasoning, parallel sampling) among the other things that make language models work: a model that writes many things before it answers does better, as a student with a scratch pad does on an exam ([≈1:11:43](../raw/transcripts/17-language-models.md), [slide 108](../raw/slides/17-language-models.md)).

## Lectures

- [Lecture 1](01-course-overview.md): scaling laws in the history of AI; the cost of frontier models.
- [Lecture 5](05-search-i.md): scaling inference compute with repeated sampling.
- [Lecture 17](17-language-models.md): Kaplan and Chinchilla, the 6ND rule, one model for every task.
