---
title: Copyright and Fair Use — Copyright Law, Licenses, the Four Fair-Use Factors, Terms of Service, Memorization vs. Extraction
---

# Copyright and Fair Use

Copyright is the part of AI and society that concerns where training data comes from. In [lecture 18](18-ai-and-society.md)'s ecosystem view it is an *upstream* concern: creators of the data a model is trained on "might not be appropriately compensated", so "an incentive system is kind of broken" ([≈18:45](../raw/transcripts/18-ai-and-society.md)). Most lawsuits around generative AI are about copyright, and Anthropic agreed to pay authors \$1.5 billion to settle one ([≈35:47](../raw/transcripts/18-ai-and-society.md)). [Lecture 1](01-course-overview.md) already lists copyright among the societal questions AI raises, and [lecture 17](17-language-models.md)'s list of open problems begins with "Copyright / fair use: is it ok to train on the world's data?" ([slide 118](../raw/slides/17-language-models.md)).

All of the legal content below is as lecture 18 presents it; the lecture describes itself as "a bit of going through basic IP law" ([≈36:33](../raw/transcripts/18-ai-and-society.md)).

## Copyright Law

Copyright is one kind of **intellectual property**, alongside patents, trademarks and trade secrets. The purpose of intellectual property law is to *incentivize* the creation of intellectual goods by protecting creators ([≈36:33](../raw/transcripts/18-ai-and-society.md)). Copyright goes back to 1709 in England (the Statute of Anne). In the United States the current law is the Copyright Act of 1976, under which copyright applies to "original works of authorship fixed in any tangible medium of expression" ([≈37:20](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). The points lecture 18 draws out:

- **Only original works.** A collection such as a telephone directory is not copyrightable unless there is creativity in its selection or arrangement.
- **Expression, not ideas.** The quicksort algorithm cannot be copyrighted; code implementing it can.
- **Fixed, not published.** Since 1976 a work need only be fixed, no longer published (as in 1909), and no registration is needed for protection, unlike patents. The threshold is "extremely low": a website is copyrighted, and so, technically, is your CS221 homework ([≈38:05](../raw/transcripts/18-ai-and-society.md)–[≈38:53](../raw/transcripts/18-ai-and-society.md)).
- **Registration to sue.** A creator must register, for \$65, before suing for infringement.
- **Expiry.** In the lecture's figures, copyright lasts 75 years, after which the work enters the **public domain**, like Shakespeare, Beethoven and most of Project Gutenberg ([≈39:40](../raw/transcripts/18-ai-and-society.md)).

So "most things on the internet are actually copyrighted", and there are two ways to use a copyrighted work: a license, or fair use.

## Licenses

A **license**, from contract law, is granted by a licensor to a licensee and amounts to "a promise not to sue" ([≈40:25](../raw/transcripts/18-ai-and-society.md)). The **Creative Commons** license, created by Lessig and Eldred in 2001 to bridge the public domain and existing copyright, lets a creator give everyone permission to use and distribute a work. Wikipedia, Khan Academy and the Free Music Archive use it ([≈41:13](../raw/transcripts/18-ai-and-society.md)). Model developers also pay for licenses: Google pays Reddit, and OpenAI pays Stack Exchange and Shutterstock ([≈42:00](../raw/transcripts/18-ai-and-society.md)). But licensed, Creative Commons and public-domain data are a small part of the internet. "The vast majority of data on the internet is none of these, and that comprises most of the training data for language models."

## Fair Use

The rest has to rely on **fair use**, section 107 of the Copyright Act, which weighs four factors ([≈42:00](../raw/transcripts/18-ai-and-society.md)–[≈43:34](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

1. **Purpose and character of the use**: educational over commercial, transformative over reproductive.
2. **Nature of the work**: factual over fictional, non-creative over creative.
3. **Amount used**: a snippet over the whole work.
4. **Effect on the market** for the original: competing with it, or entering its market, weighs against fair use.

Writing a summary of a movie is fair use. So is reimplementing an algorithm from its idea rather than copying its code. So is Google Books indexing books and showing snippets, though that took a long lawsuit to establish (Authors Guild v. Google, 2002–2013) and now sets much of the precedent for data use ([≈44:21](../raw/transcripts/18-ai-and-society.md)). Copyright is **not about verbatim memorization**: plots and characters (Harry Potter's, say) can be protected even when no text is copied, while parody is likely fair use. It is "about semantics and economics as opposed to a purely technical notion of string overlap" ([≈45:10](../raw/transcripts/18-ai-and-society.md)).

## Applied to Foundation Models

Lecture 18 weighs both sides ([≈45:10](../raw/transcripts/18-ai-and-society.md)–[≈47:29](../raw/transcripts/18-ai-and-society.md)):

- **Against**: copying the data, the first step of training, is already potentially a violation even if nothing is done with it.
- **For**: training is **transformative**, since the model can do things the book cannot; and an ML system wants the **idea**, a stop sign in general, not the expression, the artistic choices of one stop-sign image. The lecturer notes this second argument is clearest for classifiers.
- **The market**: foundation models "can and do affect the market" for writers and artists. But a model trained on none of their work could compete with them just as well, so this is a real problem without being a copyright one.

**Terms of service** can restrict use further even when copyright allows it: YouTube carries a lot of Creative Commons content, but its terms of service prohibit downloading videos ([≈47:29](../raw/transcripts/18-ai-and-society.md)–[≈48:14](../raw/transcripts/18-ai-and-society.md)).

[Lecture 19](19-economics-of-ai.md) reads the Anthropic settlement as a **price**: \$1.5 billion for about 500,000 works implies about \$3,000 per work, if all the works are claimed. Data laws such as copyright and piracy law thereby imply prices for data ([slide 24](../raw/slides/19-economics-of-ai.md); [≈27:52](../raw/transcripts/19-economics-of-ai.md)–[≈28:38](../raw/transcripts/19-economics-of-ai.md)). Compliance with copyright and other data laws is one reason that lecture gives for understanding the data supply chain, not just the final dataset ([AI supply chain](ai-supply-chain.md)).

## Memorization versus Extraction

The transformation argument assumes the model does not simply contain its training data. Sometimes it does ([≈48:14](../raw/transcripts/18-ai-and-society.md)–[≈52:52](../raw/transcripts/18-ai-and-society.md)).

**Memorization** asks whether a text is *in* the weights. It is measured with the model's own probabilities, `p(book[i] | book[1:i-1])`, the [language model's](language-models.md) probability of each token of the book given the tokens before it. If those probabilities are very high, the model "knows the book." Across five models and three books, the figure lecture 18 shows finds little memorization of *M. Butterfly* or *Beloved* by any model, but Llama 3.1 70B has memorized *Harry Potter and the Sorcerer's Stone* almost end to end ([slides](../raw/slides/18-ai-and-society.md)).

**Extraction** asks whether a user can actually get the text out, "without too much work". That matters more, because copyright protects creators, and text no one can retrieve does limited harm. Prompted with "Mr. and Mrs. D" and run with a sliding window, Llama 3.1 70B generates the rest of *Harry Potter* almost verbatim. Since the model can be downloaded free, that is close to putting the book out for anyone to download. **Extraction, especially if it is easy, is a stronger case for infringement** than memorization.

It is the exception rather than the rule: nothing can be extracted about *M. Butterfly*. "You can't say universally that models memorize works, nor can you say they don't. It really depends", on the model and on the work ([≈52:52](../raw/transcripts/18-ai-and-society.md)).

## Why Data Transparency Matters Here

Deciding whether a model infringes needs at least a disclosure of what data it was trained on. That is one reason lecture 18 treats transparency as a prerequisite, and why it notes that open-weight models need disclose nothing about their data ([≈57:31](../raw/transcripts/18-ai-and-society.md), [≈1:01:25](../raw/transcripts/18-ai-and-society.md); see [openness and transparency](openness-and-transparency.md)).

## Lectures

- [Lecture 1](01-course-overview.md): copyright among the societal questions AI raises.
- [Lecture 17](17-language-models.md): copyright and fair use first on the list of open problems ([slide 118](../raw/slides/17-language-models.md)); pre-training data from the web ([pre-training](pre-training.md)).
- [Lecture 18](18-ai-and-society.md): copyright law, licenses, the four fair-use factors, foundation models, terms of service, memorization versus extraction.
- [Lecture 19](19-economics-of-ai.md): the settlement as a price for data; compliance as a reason to study the data supply chain.
- [Lecture 20](20-fireside-chat-conclusion.md): measuring how much memorized content can be extracted from language models as work only academia can do, since labs will not do it: "they're already getting sued"; lawsuits as one reason companies are not transparent about data.
