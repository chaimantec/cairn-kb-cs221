---
title: Economic Growth and AI — GDP, the 2% Trend, the Production Function, and Three Views of AI's Impact
---

# Economic Growth and AI

The second half of [lecture 19](19-economics-of-ai.md), a guest lecture by Rishi Bommasani, asks how AI might affect **economic growth**, measured by GDP. It takes the macroeconomic view, which reasons about the whole economy in aggregate. Computer science usually works at the micro level, improving specific decisions such as an architecture, an algorithm or a dataset ([slide 32](../raw/slides/19-economics-of-ai.md); [≈37:51](../raw/transcripts/19-economics-of-ai.md)–[≈39:23](../raw/transcripts/19-economics-of-ai.md)). The scope is frontier AI as currently understood, the LLM class of models rather than self-driving cars or recommender systems, and aggregate outcomes rather than who gets the benefits ([slide 33](../raw/slides/19-economics-of-ai.md)).

## How big an impact?

Impact is unsigned, since very good and very bad effects both count; growth is positive ([≈40:08](../raw/transcripts/19-economics-of-ai.md)). Nate Silver's **technological Richter scale** ranks technologies on an exponential scale: technology of the month (a smartphone app), year (the VCR), decade (credit cards), century (electricity), millennium (agriculture) and epoch (the rise of humans) ([slides 35–36](../raw/slides/19-economics-of-ai.md); [≈40:55](../raw/transcripts/19-economics-of-ai.md)–[≈41:41](../raw/transcripts/19-economics-of-ai.md)). In a Forecasting Research Institute survey of AI professors, economists and superforecasters, the modal answer is that AI will be the technology of the century ([slide 37](../raw/slides/19-economics-of-ai.md); [≈41:41](../raw/transcripts/19-economics-of-ai.md)). Forecasts span the scale ([slides 39–43](../raw/slides/19-economics-of-ai.md); [≈42:28](../raw/transcripts/19-economics-of-ai.md)–[≈44:48](../raw/transcripts/19-economics-of-ai.md)):

- **Daron Acemoglu**'s "The Simple Macroeconomics of AI" puts the increases in TFP and GDP over the next 10 years at no more than 0.55% and 0.90% (slide 39). It is a modest estimate: important, but not game-changing.
- **Arvind and Sayash**'s "AI as Normal Technology" predicts somewhat more.
- **Dario** foresees "a country of geniuses in a datacenter".
- At the extremes, AI curing cancer with 10 gigawatts of compute ("Utopia") and the book *If Anyone Builds It, Everyone Dies* ("Doom").

Asked for his own view, the lecturer puts most of his probability on century or decade, leaning towards century ([≈1:08:40](../raw/transcripts/19-economics-of-ai.md)).

## GDP and its 2% trend

World GDP over the long run is flat for most of history and then rises ever more steeply ([slide 60](../raw/slides/19-economics-of-ai.md)). On a log scale, US GDP per person has grown at a strikingly steady **2% per year** for the last 150 years, despite everything that happened along the way ([slide 61](../raw/slides/19-economics-of-ai.md), Chad Jones 2023; [≈51:45](../raw/transcripts/19-economics-of-ai.md)–[≈52:32](../raw/transcripts/19-economics-of-ai.md)). The question about AI becomes: will it sustain this exponential, or break from it, and in which direction ([≈53:18](../raw/transcripts/19-economics-of-ai.md))?

Computing so far has not visibly bent the curve: "You can see the computer age everywhere but in the productivity statistics" (Solow; slide 62). If anything, growth over roughly the last 20 years has been below 2%. One reason is that **GDP is about payments**. Many Internet-era technologies, such as Google Search and Maps or Facebook, are free or subsidized and paid for by ads, so their value never appears on the ledger ([slide 63](../raw/slides/19-economics-of-ai.md); [≈53:18](../raw/transcripts/19-economics-of-ai.md)–[≈54:52](../raw/transcripts/19-economics-of-ai.md)).

**GDP-B** tries to capture what GDP misses by measuring **consumer surplus**: the value a buyer gets beyond the price paid ([slide 64](../raw/slides/19-economics-of-ai.md); [≈55:38](../raw/transcripts/19-economics-of-ai.md)–[≈57:09](../raw/transcripts/19-economics-of-ai.md)). It uses choice experiments that ask what people would give up for a good, or pay for it. A 2025 survey found that about 40% of adults use generative AI, and that they value it at \$98 a month. Scaled to the US population, that is about \$97 billion of consumer surplus a year that traditional GDP accounting does not show. The lecturer would like more bespoke measurement tools for AI's effects than GDP, which he finds a very coarse instrument ([≈1:09:26](../raw/transcripts/19-economics-of-ai.md)–[≈1:10:12](../raw/transcripts/19-economics-of-ai.md)).

## The production function

Economists often model output with a production function ([slide 67](../raw/slides/19-economics-of-ai.md); [≈1:02:29](../raw/transcripts/19-economics-of-ai.md)–[≈1:03:16](../raw/transcripts/19-economics-of-ai.md)):

$$Y = A \cdot K^{\alpha} \cdot L^{1-\alpha}$$

Here $Y$ is output (GDP), $K$ is capital (things like data centers), and $L$ is labor (hours of people doing things, and perhaps hours of AI doing things). $A$ is **total factor productivity** (TFP), the efficiency with which capital and labor are converted into output. $\alpha$ is the capital share, between 0 and 1. Not all economists accept this exact form. Taking it as given, if you could increase only $K$ or only $L$, you would want to grow the two at equal rates to grow $Y$ fastest.

## Three views of what AI does

Lecture 19 does not forecast. It shows how each of three beliefs about AI translates into a prediction for GDP ([slide 65](../raw/slides/19-economics-of-ai.md); [≈57:54](../raw/transcripts/19-economics-of-ai.md)–[≈59:26](../raw/transcripts/19-economics-of-ai.md)).

**AI makes one sector super productive** ([slide 66](../raw/slides/19-economics-of-ai.md); [≈59:26](../raw/transcripts/19-economics-of-ai.md)–[≈1:01:43](../raw/transcripts/19-economics-of-ai.md)). GDP grows only **slightly**, much less than that sector's productivity, because prices in the sector fall even as consumption grows. David Autor's example is illumination. Candles gave way to electric light, the productivity of lighting soared, its price and its jobs fell, and its share of the economy shrank. Then **Baumol's cost disease**: other sectors must match the productive sector's wages, so they become more expensive ("good/cheap software, expensive healthcare"). The sectors that did not become more productive then dominate the GDP share.

**AI is an injection of cheap labor** ([slide 68](../raw/slides/19-economics-of-ai.md); [≈1:03:16](../raw/transcripts/19-economics-of-ai.md)–[≈1:05:35](../raw/transcripts/19-economics-of-ai.md)). $L$ grows. If capital $K$ keeps up, GDP can grow a lot: the curve shifts up if the injection is one-time, and its exponential rate grows if the injection is sustained. If $L$ outpaces $K$, the extra labor cannot all be put to use. Labor in developed economies has plateaued, as population growth has stagnated and the labor share has flattened, so new labor is valuable. What matters is how much labor AI supplies, and for which tasks. This is the view behind Acemoglu's estimates ([≈58:40](../raw/transcripts/19-economics-of-ai.md)), and the one the lecturer finds most plausible ([≈1:09:26](../raw/transcripts/19-economics-of-ai.md)).

**AI generates new ideas** ([slide 69](../raw/slides/19-economics-of-ai.md); [≈1:05:35](../raw/transcripts/19-economics-of-ai.md)–[≈1:07:54](../raw/transcripts/19-economics-of-ai.md)). This is the most aggressive prediction: a change in the exponential rate of growth, perhaps even growth faster than exponential. Paul Romer showed that ideas are at the heart of growth because they are **non-rival**: once invented, an idea such as linear algebra can be reused by everyone, while a computer has to be built again for each user. On this view what matters is how AI changes R&D and science, and the lecturer points to Ben Jones (2025) for specific models.

## Lectures

- [Lecture 19](19-economics-of-ai.md): everything above.
- Related topics: [general-purpose technologies](general-purpose-technologies.md), including the J-curve; [AI supply chain](ai-supply-chain.md); [societal impact](societal-impact.md).
- [Lecture 20](20-fireside-chat-conclusion.md): asked about AI's projected impact, Percy Liang places it on lecture 19's scale: "century would be my guess", a magnitude like computing's ([≈23:18](../raw/transcripts/20-fireside-chat-conclusion.md)–[≈24:05](../raw/transcripts/20-fireside-chat-conclusion.md)).
