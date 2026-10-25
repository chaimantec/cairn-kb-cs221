---
title: Openness and Transparency — Centralization of Power, the Foundation Model Transparency Index, Closed to Open-Development Models, Marginal Risk
---

# Openness and Transparency

[Lecture 18](18-ai-and-society.md)'s last topic asks the class to think "not just what a model can do but essentially how a model is built": who can make decisions about a model's behavior, and who can build one at all ([≈53:38](../raw/transcripts/18-ai-and-society.md)). The risk behind the question is **centralization of power**. Frontier models are very capital-intensive, so very few big tech companies can build them, and very little is revealed about how they work ([≈53:38](../raw/transcripts/18-ai-and-society.md)–[≈54:24](../raw/transcripts/18-ai-and-society.md)). Transparency and openness are the two responses the lecture discusses. Its summary calls them "a basic foundation", like freedom in a political system: without them it is very hard to make the other improvements ([≈1:09:53](../raw/transcripts/18-ai-and-society.md)–[≈1:10:41](../raw/transcripts/18-ai-and-society.md)).

## Transparency: You Can't Improve What You Can't Measure

**Transparency is a prerequisite**: "if you can't measure it, you can't improve it." Making models safer or less harmful has to start from knowing how they are built and what they can do ([≈54:24](../raw/transcripts/18-ai-and-society.md)–[≈55:11](../raw/transcripts/18-ai-and-society.md)). Data disclosure in particular is the minimum needed to address labor practices and environmental impact and to judge [copyright](copyright-and-fair-use.md) infringement ([≈57:31](../raw/transcripts/18-ai-and-society.md)).

The **Foundation Model Transparency Index** (FMTI) measures it. The FMTI is a project of the lecturer's group, modelled on indices in economics such as the Human Development Index. It scores developers on **100 indicators** spanning upstream (data, labor, compute), model (methods, basics, access, capabilities, risks, mitigations) and downstream (distribution, usage policy, feedback, impact) properties ([≈55:11](../raw/transcripts/18-ai-and-society.md)–[≈56:43](../raw/transcripts/18-ai-and-society.md)). The May 2024 index, as lecture 18 shows it ([slides](../raw/slides/18-ai-and-society.md), every cell transcribed):

- Transparency is "fairly uneven". Total scores run from 85% (StarCoder) and 75% (Jurassic-2, Luminous) down to 41% (Titan Text Express) and 33% (Fuyu-8B), with GPT-4 at 49% and Claude 3 at 51%.
- The weakest dimensions, averaged over developers, are impact (15%), mitigations (31%) and data (34%). Closed models do especially badly upstream, "because there's not much disclosed about how these models are built" ([≈57:31](../raw/transcripts/18-ai-and-society.md)).
- **The theory of change** is that public reporting incentivizes transparency, as the Gender Shades audit did for face recognition ([inequality in AI](ai-inequality.md)). Every developer compared between October 2023 and May 2024 improved, AI21 Labs from 25 to 75 and Amazon from 12 to 41, OpenAI only from 48 to 49 ([≈58:17](../raw/transcripts/18-ai-and-society.md)–[≈59:03](../raw/transcripts/18-ai-and-society.md)).

## Openness Is a Spectrum

**Openness** is related to transparency but not the same thing ([≈59:03](../raw/transcripts/18-ai-and-society.md)–[≈59:50](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

| Level | What is released | Examples on lecture 18's slide |
| --- | --- | --- |
| **Closed** | nothing: API or product access only | GPT-5, Claude, Gemini |
| **Open-weight** | the weights, but not the data or the code | Llama, DeepSeek |
| **Open-source** | weights, code and data recipe; development still closed | GPT-J, OLMo |
| **Open-development** | all of that, plus the process, which the community can contribute to and audit | Marin |

[Lecture 17](17-language-models.md) draws the same three-way split among today's language models, closed frontier models behind an API, open-weight models and open-source models, mostly academic ([slides 111–115](../raw/slides/17-language-models.md); see [language models](language-models.md)).

Two points from lecture 18 sharpen the distinctions ([≈1:00:38](../raw/transcripts/18-ai-and-society.md)–[≈1:02:11](../raw/transcripts/18-ai-and-society.md)). First, "open model" usually means open-weight, and an open-weight model is like a **binary executable**: you can run it but you do not have its source, which "doesn't constitute open source." Second, being open-weight says nothing about transparency, since such a model need disclose nothing about its data. Mistral 7B is open-weight and scores 55 out of 100 on the index ([≈1:02:57](../raw/transcripts/18-ai-and-society.md)–[≈1:03:44](../raw/transcripts/18-ai-and-society.md)). The analogy the lecture draws is open-source software, which brought in communities, improved security and decentralized power: imagine if Microsoft Windows had been the only operating system in the 90s, with no Linux.

## Benefits and Risks of Open Models

Following *On the Societal Impact of Open Foundation Models*, lecture 18 lists three **benefits** ([≈1:02:11](../raw/transcripts/18-ai-and-society.md)–[≈1:04:29](../raw/transcripts/18-ai-and-society.md)): innovation and customizability (with weights you can fine-tune, take layers and quantize, where an API only takes a prompt and returns a response); increased transparency, though weights alone are not enough; and less centralization of power, though only partly, since training a model still takes a huge amount of capital.

The **risks** are mainly misuse. Safety tuning can be stripped off an open-weight model fairly easily, and the model then used for disinformation or hacking ([≈1:04:29](../raw/transcripts/18-ai-and-society.md)). Two principles keep the assessment honest:

- **Marginal risk.** Compare against the alternative world, which still has closed models and the internet, not against a world with no AI. Attackers used the closed Claude Code for cyberattacks, and much bioweapon information is already online or taught in advanced biology classes. "The relevant quantity is the marginal risk" ([≈1:05:17](../raw/transcripts/18-ai-and-society.md)–[≈1:06:03](../raw/transcripts/18-ai-and-society.md)).
- **The whole ecosystem.** A model can at most help design a bioweapon; manufacturing and deploying it are further gates, and gating at the physical level is sometimes easier than at the model level ([≈1:06:03](../raw/transcripts/18-ai-and-society.md)–[≈1:06:49](../raw/transcripts/18-ai-and-society.md)). This is the [societal impact](societal-impact.md) ecosystem view applied to risk.

The program's overall verdict: "need more clarity and measurement" ([slides](../raw/slides/18-ai-and-society.md)).

## Lectures

- [Lecture 17](17-language-models.md): closed, open-weight and open-source language models.
- [Lecture 18](18-ai-and-society.md): centralization of power, the Foundation Model Transparency Index, the openness spectrum, marginal risk.
