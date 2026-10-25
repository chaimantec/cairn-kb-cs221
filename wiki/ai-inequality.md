---
title: Inequality in AI — Gender Shades, Auditing, Distributionally Robust Optimization, Global Bias, Spurious Correlations
---

# Inequality in AI

**Inequality** in AI means a system that works better for some groups of people than for others. [Lecture 18](18-ai-and-society.md) treats it as an *accident*, in its taxonomy of AI's impact: neither the developer nor the user wants it, but it happens anyway "through essentially oversight or negligence" ([≈14:07](../raw/transcripts/18-ai-and-society.md)). Its everyday example is the voice assistant that does not recognize your speech as well if you have an accent. It appears again downstream in lecture 18's ecosystem view: AI helps some people more than others ([≈19:34](../raw/transcripts/18-ai-and-society.md)). The lecture's deep dive gives three forms of it, and one lesson.

## Demographic Inequality and the Power of Auditing

**Gender Shades** (Buolamwini & Gebru, 2018) evaluated the gender classifiers of Microsoft, Face++ and IBM, and broke their accuracy down by four groups: darker male, darker female, lighter male and lighter female ([≈20:20](../raw/transcripts/18-ai-and-society.md)–[≈21:06](../raw/transcripts/18-ai-and-society.md)). Darker-skinned women fared worst for all three classifiers: 79.2%, 65.5% and 65.3% accuracy respectively, against 98–100% for lighter-skinned men, gaps of 20.8 to 34.4 points ([slides](../raw/slides/18-ai-and-society.md), where the full table is transcribed). "If you were just measuring average accuracy, you would think that these systems work pretty well", but an intersectional group was getting much worse service.

After the study came out, the systems all improved. Lecture 18's takeaway is "the power of third-party **auditing**", which gives companies an incentive to fix what is measured publicly ([≈21:51](../raw/transcripts/18-ai-and-society.md)). The same theory of change drives the Foundation Model Transparency Index ([openness and transparency](openness-and-transparency.md)), and the lecture's summary ends with auditing as a powerful tool: technically just evaluation, socially a way of raising awareness that instigates the will to act ([≈1:10:41](../raw/transcripts/18-ai-and-society.md)).

The lecture gives two technical remedies ([≈21:51](../raw/transcripts/18-ai-and-society.md)–[≈22:37](../raw/transcripts/18-ai-and-society.md)):

- **Data**: collect more data for underrepresented groups. This can be costly, which is why many companies may not have it in the first place.
- **Algorithms**: upweight underrepresented groups in the training set, or use **distributionally robust optimization**. Instead of maximizing average accuracy, maximize the *worst-case* accuracy over groups, the minimum accuracy over all of them, so that the objective itself makes sure every group has good accuracy (the program cites Sagawa, Koh, Hashimoto and Liang, 2019).

The program also asks, as an aside, whether gender classification is even a well-defined task, since a classifier works from superficial features of an image while gender is a matter of self-identification ([≈23:24](../raw/transcripts/18-ai-and-society.md)).

## Global Bias and Representation

Inequality also shows up between countries ([≈23:24](../raw/transcripts/18-ai-and-society.md)–[≈24:58](../raw/transcripts/18-ai-and-society.md)). **Starling 7B** is a reward model fine-tuned from Llama 2 7B Chat; reward models like it score responses and are used to post-train language models with RL (see [post-training](post-training.md)). Asked to score the response "I am from {country}" to "Where are you from?", it gives the US and Canada high reward and places like Saudi Arabia very low reward, "for no particularly good reason". On the program's map, Western countries are blue and much of Africa and the Middle East red ([slides](../raw/slides/18-ai-and-society.md)). A language model fine-tuned against it would inherit "some pretty serious biases." This is the [alignment](alignment.md) pipeline carrying an inequality through to the model.

## Spurious Correlations

A **spurious correlation** is a pattern in the training data that doesn't generalize ([≈26:32](../raw/transcripts/18-ai-and-society.md)–[≈27:18](../raw/transcripts/18-ai-and-society.md)). A learning algorithm only looks for patterns and correlations, and nothing stops it from latching on to one that "turns out not to be the right causal variable."

Lecture 18's example is a model that predicts a collapsed lung from a chest X-ray ([≈24:58](../raw/transcripts/18-ai-and-society.md)–[≈26:32](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). Many positive X-rays show a **chest drain**, the tube that is the common treatment for a collapsed lung, and the model learned to use the drain to diagnose the condition. Its overall AUC is 0.87, but 0.94 on patients with chest drains and only 0.77 on patients without. So it is least accurate on exactly the patients who have not yet been treated and still need treatment. Minority subpopulations are often hit hardest by such correlations.

## Inequality Between Workers

[Lecture 19](19-economics-of-ai.md) looks at who gains and loses from AI in the labor market, using two studies from Erik Brynjolfsson's group ([≈4:45](../raw/transcripts/19-economics-of-ai.md)–[≈6:18](../raw/transcripts/19-economics-of-ai.md)). Payroll data from ADP show a rapid, steep drop in hiring of junior software developers after ChatGPT's release in 2022. On slide 3, headcount for ages 22–25 falls to about 0.8 of its late-2022 level by 2025, while older groups rise. In a call center where some workers adopted a generative-AI tool in 2023, the tool's effect was largest for the newest workers and close to zero for those with more than a year's tenure ([slides 3–4](../raw/slides/19-economics-of-ai.md)). Whether AI augments or automates workers may matter little for aggregate GDP, the lecturer says, but a great deal for what happens to workers ([≈1:09:26](../raw/transcripts/19-economics-of-ai.md)).

## The Lesson: Monitor Multiple Metrics

All three cases teach the same thing, and it is the lesson the program prints: "always monitor multiple metrics (different subpopulations)". An average can look excellent while a subgroup is badly served: "You look at accuracy and you get it up to 90% and you say, wow, I made so much progress" ([≈27:18](../raw/transcripts/18-ai-and-society.md)–[≈28:04](../raw/transcripts/18-ai-and-society.md)). The closing summary lists it as "Need to monitor multiple metrics and inequality" ([≈1:09:05](../raw/transcripts/18-ai-and-society.md)).

## Lectures

- [Lecture 1](01-course-overview.md): inequality among the societal questions AI raises.
- [Lecture 17](17-language-models.md): reward models in RLHF; see [post-training](post-training.md).
- [Lecture 18](18-ai-and-society.md): Gender Shades and auditing, distributionally robust optimization, global bias in a reward model, spurious correlations.
- [Lecture 19](19-economics-of-ai.md): AI's uneven effects on workers by age and tenure.
