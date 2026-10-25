---
title: Societal Impact of AI — Why Technologists Should Care, Dual Use, Benefits/Misuse/Accidents, the Ecosystem View
---

# Societal Impact of AI

[Lecture 1](01-course-overview.md) raises the question early: beyond aligning an agent with its developer, "What impact do we want the agent to have on society?" ([slides](../raw/slides/01-course-overview.md)). It involves privacy, copyright, jobs, inequality and geopolitics, a deeply **sociotechnical** problem with fundamental trade-offs between different people's values ([≈10:10](../raw/transcripts/01-course-overview.md)), and the course promises a deep dive into the societal impact of AI ([≈14:51](../raw/transcripts/01-course-overview.md)). [Lecture 18](18-ai-and-society.md) is the course's answer to how to think about it. It notes that high-level principles such as the Belmont Report and the ACM Code of Ethics "all seem pretty unobjectionable", and that the real question is "how do we actually operationalize them in practice?" ([≈5:35](../raw/transcripts/18-ai-and-society.md)). It offers the frameworks below for orienting oneself. The lecture's deep dives each have their own page: [inequality in AI](ai-inequality.md), [alignment](alignment.md), [copyright and fair use](copyright-and-fair-use.md), and [openness and transparency](openness-and-transparency.md).

## Why Technologists Should Care

Lecture 18 gives three reasons ([≈0:52](../raw/transcripts/18-ai-and-society.md)–[≈3:11](../raw/transcripts/18-ai-and-society.md)). Technology reshapes society, and AI is the fastest-growing technology in history: ChatGPT has 800 million weekly active users. Technologists hold real power: they understand the technology best, they choose which problems to work on, and they make design choices that shape access, such as which languages to support, whether to release weights and which requests to refuse, choices "no one else is going to make." And leaving the consequences to someone else is the attitude of Tom Lehrer's Wernher von Braun: "Once the rockets are up, who cares where they come down? That's not my department."

## Dual Use

A **dual use technology** can be used both to benefit and to harm people, and its developers "don't get to fully control its use" ([≈6:23](../raw/transcripts/18-ai-and-society.md)). Ammonia (agriculture or chemical weapons), rockets (space or missiles), nuclear power (energy or weapons), hacking agents (penetration testing or cyberattacks) and encryption (privacy or concealing crime) are lecture 18's historical examples ([slides](../raw/slides/18-ai-and-society.md)). AI is dual use: the agents that can attack servers can also secure them ([≈13:21](../raw/transcripts/18-ai-and-society.md)). That does not make the outcome a coin toss: "You can do things to steer AI so it tilts towards the benefits" ([≈8:42](../raw/transcripts/18-ai-and-society.md)).

## Intent × Impact: Benefits, Misuse, Accidents

Lecture 18 classifies effects by the developer's or user's **intent** and the **impact** on society ([≈8:42](../raw/transcripts/18-ai-and-society.md)–[≈16:27](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

- **Beneficial applications** (good intent, positive impact): drug development (AlphaFold), healthcare (questions over health records, explaining medical jargon), education (designed for pedagogy, not task completion), robotics, weather and climate forecasting. Response: do more of this.
- **Misuse** (bad intent, negative impact): spam, fraud, AI-assisted cyberattacks, disinformation and deepfakes. Response: implement safeguards to try to prevent it, though misuse cannot be prevented altogether.
- **Accidents** (good intent, negative impact): inequality, sycophancy, overreliance, cultural homogenization, job displacement, each "unintended consequences" that neither developer nor user wants. These are probably the most common case, and where the most can be done, through preventative testing and care before deployment.
- Bad intent with positive impact gets only a question mark: you would have to be "a pretty bad supervillain."

Sycophancy shows why accidents are subtle. Providers tune models to be pleasant for users, and reinforcing users' false beliefs is the side effect, especially troubling for users with mental health issues ([≈14:55](../raw/transcripts/18-ai-and-society.md)).

## The Ecosystem View

Judging an AI system by its own behavior, whether it answers correctly and what queries it handles, is "really insufficient." Lecture 18 asks for an **ecosystem view**, with people and the environment both upstream and downstream of the system ([≈16:27](../raw/transcripts/18-ai-and-society.md)–[≈19:34](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

- **Upstream**, models are made from data, which comes from people ("all that data is really the product of people's labor"), and from compute, which comes from extracting energy and materials from the environment. The concerns here are privacy, copyright, labor practices (people are paid to create training data and may be treated poorly) and environmental impact (emissions, water, resource extraction).
- **Downstream**, people use AI for benefit and harm. The concerns are inequality, harm (toxic content or harmful actions), overreliance and jobs.

The view recurs through the lecture. It explains why data disclosure matters to the transparency index. It is the second principle for assessing open models' risk, since a model that helps design a bioweapon still leaves manufacture and deployment as gates ([≈1:06:03](../raw/transcripts/18-ai-and-society.md)). And it is in the summary: "think about the whole ecosystem, not just the model", rather than treating data as something that "shows up at the front door" ([≈1:09:05](../raw/transcripts/18-ai-and-society.md)). Lecture 19, a guest lecture on AI supply chains, was announced as expanding on it ([≈1:11:26](../raw/transcripts/18-ai-and-society.md)). [Lecture 19](19-economics-of-ai.md) opens from the same inside-the-box and outside-the-box picture ([≈0:05](../raw/transcripts/19-economics-of-ai.md)). It describes the compute, data and distribution supply chains region by region, as organizations as well as technology, and then asks how AI will affect economic growth; see [AI supply chain](ai-supply-chain.md) and [economic growth](economic-growth.md).

## Measurement and Auditing

Across its topics lecture 18 keeps returning to measurement: monitor multiple metrics across subpopulations, since no single metric is perfect, and use third-party **auditing** and public reporting, which improved face recognition after Gender Shades and transparency after the FMTI ([≈1:09:05](../raw/transcripts/18-ai-and-society.md)–[≈1:11:26](../raw/transcripts/18-ai-and-society.md)). "On a technical level, it's just evaluation… But really, I think from a social perspective, it's auditing."

## Lectures

- [Lecture 1](01-course-overview.md): AI's societal goals as a sociotechnical problem; alignment.
- [Lecture 17](17-language-models.md): AI safety concerns as models improve (hacking, bioweapons); safety tuning in [post-training](post-training.md).
- [Lecture 18](18-ai-and-society.md): why technologists should care, principles, dual use, benefits/misuse/accidents, the ecosystem view, auditing.
- [Lecture 19](19-economics-of-ai.md): the ecosystem view as supply chains, and AI's economic impact: jobs, who wins and loses, growth.
- [Lecture 20](20-fireside-chat-conclusion.md): why this year's redesign gave the societal impact of AI a homework and lectures; how to get end users to care about AI ethics (transparency, labels, advocacy; human labor and environmental strain); whether AI is a bubble.
