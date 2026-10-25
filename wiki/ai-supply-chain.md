---
title: AI Supply Chain — Compute, Data and Distribution, Seen as Technology and as Organizations
---

# AI Supply Chain

The **AI supply chain** is the chain of resources, companies and products that runs from the inputs of model development (compute and data) through the foundation models themselves to the applications built on them, the people who use them, and the people they affect. [Lecture 18](18-ai-and-society.md) introduced it as the **ecosystem view**: an AI system sits in the middle, with people and the environment both upstream and downstream of it ([societal impact](societal-impact.md)). [Lecture 19](19-economics-of-ai.md), a guest lecture by Rishi Bommasani, describes today's supply chain region by region, in order to reason about AI's economic effects.

## Why the technology alone is not enough

Lecture 19's starting point is a **dual lens**: to understand AI's economic impact you have to think about AI as a class of technology and, in parallel, about the organizations that build and use it ([≈6:18](../raw/transcripts/19-economics-of-ai.md)–[≈7:52](../raw/transcripts/19-economics-of-ai.md)). Slide 7 gives three reasons ([≈7:52](../raw/transcripts/19-economics-of-ai.md)–[≈9:23](../raw/transcripts/19-economics-of-ai.md); [slides](../raw/slides/19-economics-of-ai.md)):

1. AI's overall economic impact depends heavily on organizations outside AI. Most of the economy is not the tech sector, and AI's cumulative effect comes from whether and how all those other organizations adopt it.
2. The technology does not say who wins and who loses. Its trajectory may say that AI matters, but not which companies benefit most or are worst off.
3. Even model developers make many choices beyond the technology.

The example: on the Artificial Analysis Intelligence Index (slide 8), Google's, Anthropic's and OpenAI's top models score 73, 70 and 70. On capability alone one might conclude "Google = Anthropic = OpenAI" and treat the firms as substitutes. But they also decide **when to release** models, **how to price** them, **which products to build** (and how to integrate vertically into downstream markets), and **who to partner with**. So in reality "Google != Anthropic != OpenAI" ([slides 9–10](../raw/slides/19-economics-of-ai.md); [≈9:23](../raw/transcripts/19-economics-of-ai.md)–[≈11:41](../raw/transcripts/19-economics-of-ai.md)).

The supply chain can be described in both ways at once ([≈12:29](../raw/transcripts/19-economics-of-ai.md)–[≈13:16](../raw/transcripts/19-economics-of-ai.md)). Technologically, there are datasets, GPUs, models, coding tools and APIs. Organizationally, there are news companies, cloud companies, AI companies and coding-tool companies. The useful picture is companies with relations to each other, mediated by specific technical assets.

## The layers

Slide 12 of lecture 19 draws the foundation-model supply chain as a staircase from upstream to downstream ([slide 12](../raw/slides/19-economics-of-ai.md)):

| Layer | What | Who |
| --- | --- | --- |
| Data layer | Gathers data at scale | Company collecting or processing data |
| Compute layer | Provides processing power for training | Company providing computational resources |
| Foundation model developer | Designs the model and trains it using data and compute (data cleaning and annotation, pre-training, fine-tuning, adding safeguards, internal testing) | Foundation model company |
| Host layer | Makes models available downstream by download or API | In-house, or a cloud service provider |
| Application layer | Builds and releases an application on the model or a fine-tuned version | Application developer (the model company or a third party) |
| Application user | Uses the application | End users: the public, organisations, companies |
| Affected persons | Communities affected by the model's or application's use | Service users, people represented in outputs, people whose jobs change; also environmental costs |

The slide groups them into development, pre-deployment and post-deployment. Lecture 19 picks out three regions where the technology and the organizations can be understood together: compute, data and distribution ([≈13:16](../raw/transcripts/19-economics-of-ai.md)).

## Compute

The compute supply chain is much more than "some NVIDIA GPUs that sit in some data centers" ([≈13:16](../raw/transcripts/19-economics-of-ai.md)–[≈14:49](../raw/transcripts/19-economics-of-ai.md)). Slide 15's semiconductor value chain runs from design (co-designers, chip designers, EDA and IP vendors) through manufacturing (wafer-fab equipment, foundries, packaging) to chips (GPUs and ASICs, inference-focused chips, edge chips). Many companies play several roles, and there are many companies. Three are critical, each with an outsized role at its level of the stack and each the most valuable company in its part of the world ([slide 16](../raw/slides/19-economics-of-ai.md); [≈14:49](../raw/transcripts/19-economics-of-ai.md)–[≈16:20](../raw/transcripts/19-economics-of-ai.md)):

- **ASML** (Netherlands) has a global monopoly on extreme-UV lithography, the optical technology needed to fabricate advanced chips; \$400B valuation, the most valuable European company.
- **TSMC** (Taiwan), the top manufacturer of advanced chips, and so a critical dependency of Nvidia; \$1.5T, the most valuable Asian company.
- **Nvidia**, with a dominant share of the chips market; \$4.4T, the most valuable company.

Studying a supply chain often means looking for **monopolies or their approximations** ([≈16:20](../raw/transcripts/19-economics-of-ai.md)). They matter for three reasons ([slide 17](../raw/slides/19-economics-of-ai.md); [≈17:06](../raw/transcripts/19-economics-of-ai.md)–[≈18:38](../raw/transcripts/19-economics-of-ai.md)). One is **resilience**: a single player fully responsible for a layer is a bottleneck and an essential dependency for the next layer. Another is **who accrues the value** of AI's growth, much of which goes to these essential companies. The third is **geopolitics**: TSMC's location in Taiwan makes it central to the conversation about US–China competition, and Nvidia is at the center of export controls on which chips may be sold to China.

Downstream of chips are the **clouds**, used for both training and inference. Amazon, Microsoft and Google have long been the major clouds, and many frontier labs have partnerships with or dependencies on them for training; inference is a more heterogeneous market with many newer companies ([slide 18](../raw/slides/19-economics-of-ai.md); [≈18:38](../raw/transcripts/19-economics-of-ai.md)–[≈19:25](../raw/transcripts/19-economics-of-ai.md)). And compute needs data centers, which need land, electricity and water; that part of the supply chain is becoming a bottleneck ([slide 19](../raw/slides/19-economics-of-ai.md); [≈20:11](../raw/transcripts/19-economics-of-ai.md)). Asked whether natural resources will limit AI's growth, the lecturer guesses that in the US the rate-limiting factor is the ability to transmit power rather than to produce it ([≈1:10:57](../raw/transcripts/19-economics-of-ai.md)).

## Data

Data is less capital-intensive to produce than compute, so its supply chain is far more heterogeneous and less concentrated ([≈20:56](../raw/transcripts/19-economics-of-ai.md)). Lecture 19 sorts how an AI company acquires data into six methods, each with its own pricing ([slides 21 and 23](../raw/slides/19-economics-of-ai.md); [≈21:42](../raw/transcripts/19-economics-of-ai.md)–[≈27:52](../raw/transcripts/19-economics-of-ai.md)):

| Where the data is | Method | Example | What the firm pays |
| --- | --- | --- | --- |
| Within the firm | Synthetic data | RLAIF | Compute |
| Within the firm | Usage data | ChatGPT interactions, Gmail | Services, under its terms of service |
| Public | Public datasets | SQuAD, The Pile | Free |
| Public | Web crawling | | Currently free |
| Third parties | New data | Scale, Mercor | Labor (worker hours) |
| Third parties | Old data | New York Times, Reddit | A negotiated large-scale transaction |

The pricing differences create asymmetries in how firms acquire data and build models. The law can also reveal a price: Anthropic's settlement of a copyright lawsuit, \$1.5 billion for about 500,000 works, implies about \$3,000 per work ([slide 24](../raw/slides/19-economics-of-ai.md); [≈27:52](../raw/transcripts/19-economics-of-ai.md)). Web crawling, the main source of [pre-training](pre-training.md) data, is changing as AI develops. Websites have restricted crawlers more over the last few years, and asymmetrically: OpenAI's crawlers are blocked by name more often than Anthropic's (Longpre et al. 2023; [slide 22](../raw/slides/19-economics-of-ai.md); [≈24:48](../raw/transcripts/19-economics-of-ai.md)–[≈25:34](../raw/transcripts/19-economics-of-ai.md)).

The structure of the data supply chain matters for three questions ([slide 25](../raw/slides/19-economics-of-ai.md); [≈28:38](../raw/transcripts/19-economics-of-ai.md)–[≈30:55](../raw/transcripts/19-economics-of-ai.md)). **Will we run out of data?** That depends on the current training mixture, how fast each source produces data, and how abundant each is. **Compliance**: copyright and piracy, laws such as those against even possessing child sexual abuse material, and privacy law such as the GDPR. **Competition**: whether data exclusive to one developer gives it an advantage in model quality. Lecture 18 covers the copyright questions themselves ([copyright and fair use](copyright-and-fair-use.md)).

## Distribution

How a developer distributes its model is a choice along a spectrum, from fully closed (no outside access at all) through hosted access, API access and API fine-tuning, to open weights, and finally to releasing weights, data and code with or without use restrictions ([slide 27](../raw/slides/19-economics-of-ai.md); [≈30:55](../raw/transcripts/19-economics-of-ai.md)–[≈33:14](../raw/transcripts/19-economics-of-ai.md)). Lecture 19 places the boundary of "open" at whether the weights are distributed; [openness and transparency](openness-and-transparency.md) covers lecture 18's account of the same spectrum. The choice shapes the downstream supply chain in four ways ([slide 28](../raw/slides/19-economics-of-ai.md); [≈33:14](../raw/transcripts/19-economics-of-ai.md)–[≈36:16](../raw/transcripts/19-economics-of-ai.md)):

- **Vertical control.** Closed release lets a firm integrate into downstream markets, at the cost of ceding uses it does not invest in.
- **Pricing.** Closed API prices tend to be higher than open models' prices, because open weights let many companies host inference and compete.
- **Data flows.** Running a model locally can give better privacy and security, or at least a clearer view of the risks, which regulated sectors need.
- **Adaptability.** With the weights you can fine-tune and distill, enabling applications that are hard to build on closed models.

## Lectures

- [Lecture 18](18-ai-and-society.md): the ecosystem view, upstream and downstream of a model; the openness spectrum; copyright.
- [Lecture 19](19-economics-of-ai.md): the dual lens, and the compute, data and distribution supply chains.
- [Lecture 17](17-language-models.md): where the data goes, in [pre-training](pre-training.md) and [post-training](post-training.md).
