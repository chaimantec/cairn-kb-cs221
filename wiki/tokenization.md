---
title: Tokenization — Vocabularies, Subword Units and Byte-Pair Encoding
---

# Tokenization

A model that works on text first has to turn the text into numbers. **Tokenization** splits a string into a sequence of **tokens** and maps each token to an integer index using a **vocabulary**, a dictionary from strings to indices. The indices then become vectors, as one-hot vectors in [lecture 3](03-learning-ii.md) or as embeddings in a [language model](language-models.md). The vocabulary fixes what the model can recognize at all: anything outside it is unknown ([lecture 17](17-language-models.md), [≈1:04:47](../raw/transcripts/17-language-models.md)–[≈1:05:34](../raw/transcripts/17-language-models.md)).

## In Lecture 3: Splitting on Spaces

Lecture 3's pipeline for text classification starts with the simplest tokenizer: split on spaces, so "the cat in the hat" becomes ["the", "cat", "in", "the", "hat"] and then the indices [0, 1, 2, 0, 3], the same word always getting the same index. A `Vocabulary` class keeps the mapping in both directions and assigns a new index to each unseen token ([≈1:06:43](../raw/transcripts/03-learning-ii.md)). The lecture notes that modern language models use byte-pair encoding instead, which handles punctuation, compound words and out-of-vocabulary tokens more gracefully ([≈1:08:16](../raw/transcripts/03-learning-ii.md)).

## In Lecture 17: Subword Units

Lecture 17's tensor view starts the same way, with a toy vocabulary of ten words (`'The': 0`, `'and': 1`, …) ([slide 22](../raw/slides/17-language-models.md)). A real vocabulary faces a dilemma ([≈1:05:34](../raw/transcripts/17-language-models.md)–[≈1:06:20](../raw/transcripts/17-language-models.md), [slide 98](../raw/slides/17-language-models.md)). You cannot list every possible word, and an input may be rare or misspelled. But you also do not want tokens as fine-grained as single characters, because whole words occur so often. The answer is to **build the vocabulary from subword units**. Slide 98's tokenizer view shows a 126-character sentence split into 30 tokens: common words such as "build" and "vocabulary" are single tokens, "subword" splits into "sub" and "word" and "enumerating" into "enumer" and "ating", and the deliberately odd "rArE or mispeled" breaks into smaller pieces seen in training.

**Byte-pair encoding (BPE)** is the key technique (Sennrich, Haddow and Birch, *Neural Machine Translation of Rare Words with Subword Units*, cited on slide 98):

1. Start with one token per character, plus some common words and special tokens.
2. Repeatedly find the pair of adjacent tokens that occurs most often in the training text and merge it into a new token. If *in* and *put* often occur together, they become one token. Each merge adds one entry to the vocabulary.
3. Stop when the vocabulary reaches the desired size.

## Why Tokenization Matters

The vocabulary size is the $V$ of a language model: the number of classes in next-token prediction, and one dimension of its embedding matrix and output layer. A large $V$ covers more languages and tasks, which is one reason a plain MLP would scale badly, since its parameter count grows with $V$, while a Transformer depends on $V$ only through the embedding and the output layer ([slide 77](../raw/slides/17-language-models.md)). The Hugging Face model on slide 9, Qwen3-VL-235B-A22B-Thinking, has an embedding matrix of shape [151 936, 4 096].

Tokenization "both solves a lot of problems and creates a lot of problems": many problems of today's language models are due to tokenization, yet "we cannot train them without it at this point, at least" ([≈1:07:06](../raw/transcripts/17-language-models.md)). Slide 99 reproduces Andrej Karpathy's list of such problems, each answered "Tokenization": why LLMs cannot spell words or reverse a string, are worse at non-English languages such as Japanese, are bad at simple arithmetic, and break on the string "SolidGoldMagikarp". It also points to his video lecture on LLM tokenization, which the lecturer recommends since one slide "can't do justice" to the subject.

## Lectures

- [Lecture 3](03-learning-ii.md): splitting on spaces, the `Vocabulary` class, one-hot encoding; BPE mentioned.
- [Lecture 17](17-language-models.md): subword units, byte-pair encoding, and the problems tokenization causes.
