---
title: Pre-training — Next-Token Prediction at Scale, Web Data and Its Filtering, and In-Context Learning
---

# Pre-training

At scale, training a [language model](language-models.md) separates naturally into two stages, **pre-training** and [post-training](post-training.md); in a homework there is no such split, just a dataset and a training run ([lecture 17](17-language-models.md), [≈45:30](../raw/transcripts/17-language-models.md)–[≈46:16](../raw/transcripts/17-language-models.md)). Pre-training means training "a giant model on a giant amount of text with a simple objective (e.g. next token prediction)" ([slide 81](../raw/slides/17-language-models.md)). Its purpose is to make the model generally knowledgeable about many things at once, which is the multi-task learning that next-token prediction provides. In this phase you do not care what the model will do, only that it models whatever it sees very well; done well, the result is "somewhat of an intelligent model," able to complete text across many tasks.

## The Data

Most pre-training text is web pages. The open model OLMo 2 (from AI2) publishes its pre-training mix ([slide 81](../raw/slides/17-language-models.md)):

| Source | Type | Tokens | Words | Bytes | Docs |
| --- | --- | --- | --- | --- | --- |
| DCLM-Baseline | Web pages | 3.71T | 3.32T | 21.32T | 2.95B |
| StarCoder (filtered version from OLMoE Mix) | Code | 83.0B | 70.0B | 459B | 78.7M |
| peS2o (from Dolma 1.7) | Academic papers | 58.6B | 51.1B | 413B | 38.8M |
| arXiv | STEM papers | 20.8B | 19.3B | 77.2B | 3.95M |
| OpenWebMath | Math web pages | 12.2B | 11.1B | 47.2B | 2.89M |
| Algebraic Stack | Math proofs code | 11.8B | 10.8B | 44.0B | 2.83M |
| Wikipedia & Wikibooks (from Dolma 1.7) | Encyclopedic | 3.7B | 3.16B | 16.2B | 6.17M |
| Total | | 3.90T | 3.48T | 22.38T | 3.08B |

Web pages (DCLM-Baseline) are 3.71T of the 3.90T tokens. Samples of such documents are very diverse ([≈47:48](../raw/transcripts/17-language-models.md)–[≈49:21](../raw/transcripts/17-language-models.md), [slide 83](../raw/slides/17-language-models.md)): templated Stack Overflow boilerplate, the navigation menu of a news site, an Oracle installation guide, the front matter of a digitized 1863 reader, and a forum question about repairing a disk after a Time Machine backup, together with the commands its author tried, which is high quality by comparison. "You'll be surprised how much stuff is on the internet"; CS336 publishes more random samples.

**Filtering.** Even that is already the high-quality part. DCLM-Baseline is about 1.4% of the DCLM pool, which was extracted from Common Crawl, a project that crawls the web by following links and saving every page it sees ([≈50:07](../raw/transcripts/17-language-models.md)–[≈50:54](../raw/transcripts/17-language-models.md), [slide 84](../raw/slides/17-language-models.md)). The DCLM paper's pipeline, as percentages of the original documents:

1. **Heuristic cleaning** keeps 19.9%. The English filter removes 50.8%, and the URL, word-removal-ratio, repetition, page-length and other filters remove the rest.
2. **Deduplication** with a Bloom filter removes 6.2%, leaving 13.7%.
3. **Model-based filtering** with a FastText classifier removes 12.3%, leaving 1.4%: DCLM-Baseline, "considered *clean* text."

The lecturer summarizes it as filtering URLs, keeping only English, deduplicating, and applying another language model to judge whether text is high quality, and finds it "hard to imagine… how much bad data is in this stuff." There is a lot of data curation behind pre-training.

Seen as a supply chain ([lecture 19](19-economics-of-ai.md); [AI supply chain](ai-supply-chain.md)), pre-training data comes mostly from public datasets such as SQuAD and the Pile and from crawling the web, which is "currently free" ([slides 21 and 23](../raw/slides/19-economics-of-ai.md)). Over the last few years websites have restricted crawlers more, and unevenly between companies: OpenAI's crawlers are blocked by name more often than Anthropic's ([≈24:48](../raw/transcripts/19-economics-of-ai.md)–[≈25:34](../raw/transcripts/19-economics-of-ai.md)). Lecture 19 also lists the open question of whether we will run out of data ([≈28:38](../raw/transcripts/19-economics-of-ai.md)).

## Capabilities Emerge from Pre-training

The GPT-3 paper, *Language Models are Few-Shot Learners* (OpenAI, 2020), is the key result ([≈50:54](../raw/transcripts/17-language-models.md)–[≈53:59](../raw/transcripts/17-language-models.md), [slides 85–87](../raw/slides/17-language-models.md)). Take a good dataset, filter it, train a very large model on a lot of data, and do nothing to specialize it for any task. Then evaluate it on benchmarks such as multiple-choice questions and summarization. As the parameter count grows from 0.1B to 175B, the average accuracy over 42 benchmarks keeps going up, with no task-specific training ([slide 86](../raw/slides/17-language-models.md); the numbers are on the [scaling laws](scaling-laws.md) page).

**In-context learning.** A large enough pre-trained model can also do a task from examples given in its input ([slide 87](../raw/slides/17-language-models.md)). Write a prompt that describes the task and shows a few examples, with no gradient updates:

```text
Translate English to French:
sea otter => loutre de mer
peppermint => menthe poivrée
plush girafe => girafe peluche
cheese =>
```

To a model that only predicts missing tokens this is a perfectly valid input sequence, and it completes it: having seen English and French text in training, it pattern-matches the task the prompt sets up ([≈53:14](../raw/transcripts/17-language-models.md)). This is **in-context learning**, or **few-shot prompting**: "simply showing examples of the task as part of the input string makes completions better." Why it works at all is, the lecturer says, very surprising, and translation is only one of many such tasks.

## Earlier Pre-trained Models

Before next-token prediction became the standard, BERT (Google, 2018) pre-trained with **masked language modeling**, filling in masked tokens from both sides, and found that pre-training on a lot of text made models very useful; that objective is used less today ([≈18:32](../raw/transcripts/17-language-models.md), [slide 38](../raw/slides/17-language-models.md)). [Lecture 1](01-course-overview.md)'s history lists the pre-trained language models ELMo, BERT and T5, then the scaling era of GPT-2, GPT-3 and Llama 3 ([≈31:44](../raw/transcripts/01-course-overview.md)).

## What Pre-training Does Not Give You

A pre-trained base model is still "autocomplete on steroids": it follows the pattern of its input and does not know that the input is a question. Asked to explain the moon landing to a six-year-old, GPT-3 lists more questions like it, because lists of questions are more common on the web than questions followed by answers ([≈54:48](../raw/transcripts/17-language-models.md)–[≈55:36](../raw/transcripts/17-language-models.md), [slide 88](../raw/slides/17-language-models.md)). Making it useful is the job of [post-training](post-training.md).

## Lectures

- [Lecture 1](01-course-overview.md): pre-trained language models in the history of AI.
- [Lecture 17](17-language-models.md): pre-training data and filtering, GPT-3 and in-context learning.
- [Lecture 19](19-economics-of-ai.md): where the data comes from and what it costs; web-crawling restrictions; whether we will run out of data.
