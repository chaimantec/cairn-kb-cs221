---
title: Lecture 18 — AI & Society
---

# Lecture 18 — AI & Society

Lecture 18 is "a bit of a departure from the rest of the class": after machine learning, state-based models (search, MDPs and games), Bayesian networks and logic, it turns from the technical aspects of AI to its **societal implications** ([≈0:05](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). It was given on Wed Nov 19 2025 from the executable program `society.py`, which Percy Liang committed four minutes before the lecture; unlike every earlier program in the course it computes nothing, and is only text, images and links. The lecture first sets up a few ways of orienting oneself: why technologists should care, why high-level principles are not enough, AI as a **dual use technology**, a taxonomy of **benefits, misuse and accidents**, and an **ecosystem view** with upstream and downstream effects. It then goes deeper into four representative topics: **inequality**, **alignment**, **copyright**, and **openness and transparency**. The argument that runs through all of them is that a single number about a single model is not enough: one has to monitor many metrics, across subpopulations and across the whole ecosystem, and third-party **auditing** and public reporting are powerful levers for change.

There is no new notation. The one formula on screen is the memorization measure `p(book[i] | book[1:i-1])`, the probability a model assigns to the $i$-th token of a book given all the tokens before it.

## Why Should Technologists Care?

There are many other classes about AI and society, so the lecture opens by arguing why the topic belongs in a CS class about AI ([≈0:05](../raw/transcripts/18-ai-and-society.md)–[≈3:11](../raw/transcripts/18-ai-and-society.md)). It gives three reasons.

**Technology has massive impact on society.** That has been true historically, from the printing press and the steam engine to the internet, mobile phones and social networks, which "have just changed the fabric of society, for either better or, in some cases, for worse." AI is the fastest-growing technology in history, partly because the internet and social media let it spread on top of them: ChatGPT has 800 million weekly active users, and "we're only in the very beginning stages of this technology revolution" ([≈0:52](../raw/transcripts/18-ai-and-society.md)–[≈1:39](../raw/transcripts/18-ai-and-society.md)).

**Technologists have incredible power.** They understand the capabilities and limitations of the technology better than anyone else, they choose which problems to work on, and they make design choices that shape access: which languages a model supports, whether to release a model's weights, which requests a service should accept and which it should refuse. "These are design decisions that no one else is going to make… and these are not technical questions" ([≈1:39](../raw/transcripts/18-ai-and-society.md)–[≈2:26](../raw/transcripts/18-ai-and-society.md)).

**"Let someone else worry about the consequences" is not an attitude to adopt.** As "a relatively extreme example", the program shows Wernher von Braun, who helped Hitler develop rockets in World War II and then came to the US to help develop the space program, with a photograph of a V-2 rocket on its launcher and the lines of Tom Lehrer's song: "Once the rockets are up, / Who cares where they come down? / That's not my department, / Says Wernher von Braun" ([≈3:11](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). "It's a song, but I think it describes an attitude that we probably don't want to take."

## Principles, and the Problem of Operationalizing Them

The broadest goal is to "ensure AI is developed to benefit and not harm society", which by now "almost sounds like a platitude" ([≈4:00](../raw/transcripts/18-ai-and-society.md)). There are high-level documents to draw on. The **Belmont Report** (1979) sets out ethical principles for research on human subjects. The program says it was prompted by "the 1974 Tuskegee syphilis study (African American men were purposefully not treated for syphilis)", and it names three principles: **respect for persons** (informed consent: subjects have to consent and understand what they are getting into), **beneficence** (maximize benefits, minimize harms) and **justice** (don't burden some groups disproportionately). All three were violated in that study ([≈4:00](../raw/transcripts/18-ai-and-society.md)–[≈4:49](../raw/transcripts/18-ai-and-society.md)). The **ACM Code of Ethics and Professional Conduct** asks computing professionals to contribute to human well-being, avoid harm, be honest, be fair, credit authors, respect privacy and honor confidentiality ([≈4:49](../raw/transcripts/18-ai-and-society.md)–[≈5:35](../raw/transcripts/18-ai-and-society.md)).

"These all seem pretty unobjectionable, right? But the real question is, how do we actually operationalize them in practice?" The rest of the lecture offers "frameworks, to help orient how we should think about this very challenging problem" ([≈5:35](../raw/transcripts/18-ai-and-society.md)).

## AI Is a Dual Use Technology

The main obstacle to developing AI that benefits society is that "we don't get to fully control its use." A **dual use technology** is one that can be used both to benefit and to harm people ([≈6:23](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). This is not new with AI. The program's historical examples are ammonia (agriculture or chemical weapons), rockets (space exploration or ballistic missiles), nuclear power (energy or weapons), cybersecurity tools (an agent that can hack into a server is useful for penetration testing your own servers before deployment, or for attacking someone else's) and encryption (protecting user privacy, the foundation of doing anything on the internet, or concealing criminal activity). The lecturer adds that 20 or 30 years ago there was a big debate over encryption's dual use, and "strong encryption was banned, for a bit of time" ([≈6:23](../raw/transcripts/18-ai-and-society.md)–[≈7:56](../raw/transcripts/18-ai-and-society.md)).

AI is dual use. That does not mean throwing up one's hands: "You can do things to steer AI so it tilts towards the benefits. I said it was hard, but I didn't say it was impossible" ([≈7:56](../raw/transcripts/18-ai-and-society.md)–[≈8:42](../raw/transcripts/18-ai-and-society.md)).

## Benefits, Misuse and Accidents

The lecture taxonomizes AI's impact on society along two axes, **intent** (good or bad) and **impact** (positive or negative) ([≈8:42](../raw/transcripts/18-ai-and-society.md)–[≈10:14](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

| | positive impact | negative impact |
| --- | --- | --- |
| **good intent** | **beneficial applications**: healthcare, education, science | **accidents**: robustness, fairness, security |
| **bad intent** | ? | **misuse**: spam, fraud, disinformation |

Accidents, good intent with a negative impact, are "the more subtle case" and "probably the more common case in a lot of situations." The fourth cell, bad intent with a good outcome, holds only a question mark: "you have to be a pretty bad supervillain to live in that quadrant" ([≈9:29](../raw/transcripts/18-ai-and-society.md)–[≈10:14](../raw/transcripts/18-ai-and-society.md)).

**Benefits.** Researchers choose their problems, so they can choose ones with direct positive impact ([≈10:14](../raw/transcripts/18-ai-and-society.md)–[≈12:33](../raw/transcripts/18-ai-and-society.md)). In biomedical science, AlphaFold and its successors predict not only protein structure but how proteins bind to drugs, so simulations or model calls can replace some wet-lab experiments. In healthcare, models can answer questions over electronic health records or communicate with patients, for instance translating medical jargon (the program links the MedHELM benchmark). In education there is personalized learning for students and curriculum design, problem creation and automatic grading for instructors; the challenge is "to think about pedagogy rather than task completion", because a tutor should make sure you understand rather than hand you the answer. In robotics, self-driving cars "are actually pretty good these days", and household robots would matter for countries with large aging populations. Weather and climate models help with short-term forecasting for early warning and with monitoring emissions and the effectiveness of climate policies.

**Misuse.** Bad actors can use AI to harm others ([≈12:33](../raw/transcripts/18-ai-and-society.md)–[≈14:07](../raw/transcripts/18-ai-and-society.md)). A recent Anthropic post reported attackers using Claude Code to conduct a large-scale cyberattack; agents are getting good enough for this to be effective, though the same agents can help secure systems and find vulnerabilities ("AI is always dual use in this way"). Text, images, audio and video can now be generated "with stunning fidelity", which can spread disinformation, whether by state actors or by teenagers who want to harm their peers.

**Accidents.** Here neither the developer nor the user wants the outcome, but it happens anyway "through essentially oversight or negligence" ([≈14:07](../raw/transcripts/18-ai-and-society.md)–[≈15:41](../raw/transcripts/18-ai-and-society.md)):

- **Inequality**: AI works better for some groups than others. A voice assistant may not recognize the speech of someone with an accent as well.
- **Sycophancy**: affirming a user's false beliefs, especially troubling for users with mental health issues, where it can lead to self-harm. It counts as an accident because providers did not want it: they fine-tune their models to be pleasant for the user, and reinforcing false beliefs is a side effect. ([Post-training](post-training.md) lists making a model less sycophantic among its goals.)
- **Overreliance**: developers want models to be helpful, but if they are too helpful people become overdependent and lose the ability to think critically, for example in education.
- **Cultural homogenization**: AI reinforces existing biases and stereotypes.
- **Jobs**: AI displaces some job functions. Entry-level software engineers "are maybe at the level of what Claude Code can do", which strains hiring for those positions.

In summary: for benefits, do more; for misuse, implement safeguards to try to prevent it ("I don't think you can prevent misuse altogether"); for accidents, be more careful, which is "probably where we can do the most", through preventative testing before deployment ([≈15:41](../raw/transcripts/18-ai-and-society.md)–[≈16:27](../raw/transcripts/18-ai-and-society.md)). See [societal impact](societal-impact.md).

## The Ecosystem View: Upstream and Downstream

We often think about an AI model and its properties: does it generate the correct answer, what queries does it handle. "That's really insufficient." Understanding how AI affects society needs an **ecosystem view**, which the next lecture ([lecture 19](19-economics-of-ai.md), a guest lecture by Rishi on AI supply chains) expands on ([≈16:27](../raw/transcripts/18-ai-and-society.md)–[≈17:58](../raw/transcripts/18-ai-and-society.md)). The picture to keep in mind has the AI system in the middle, with people and the environment both **upstream** and **downstream** of it: a large language model takes in data, created by people, and GPUs, which come from resources and energy in the environment; it is then deployed to users, and its use and misuse affect people ([slides](../raw/slides/18-ai-and-society.md)).

**Upstream**, models are built from data and compute. Data comes from people: a developer may think "the internet data is free. It's just out there and you can crawl it", but "all that data is really the product of people's labor." Compute comes from extracting resources from the environment, energy and the materials for data centers and GPUs ([≈17:58](../raw/transcripts/18-ai-and-society.md)–[≈18:45](../raw/transcripts/18-ai-and-society.md)). The upstream concerns are **privacy** (information about people unintentionally revealed), **copyright** (creators not appropriately compensated, so "an incentive system is kind of broken"), **labor practices** (models are data-hungry and people are usually paid to create data, and these workers may be treated poorly) and **environmental impact** (some data-center builds "are kind of insane in scale": carbon emissions, water usage, resource extraction) ([≈18:45](../raw/transcripts/18-ai-and-society.md)–[≈19:34](../raw/transcripts/18-ai-and-society.md)).

**Downstream**, AI is used by people, for benefit and for harm: inequality (AI helps some more than others), harm (toxic content or harmful actions), overreliance and jobs ([≈19:34](../raw/transcripts/18-ai-and-society.md)). See [societal impact](societal-impact.md).

## Inequality

The rest of the lecture deep-dives into "a few representative samples", since "it is impossible to cover the whole breadth" ([≈20:20](../raw/transcripts/18-ai-and-society.md)). See [inequality in AI](ai-inequality.md).

### Demographic inequality: Gender Shades and auditing

**Gender Shades** (Buolamwini & Gebru, 2018) is "a very classic study" of what inequality means for AI. It evaluated gender classifiers from Microsoft, Face++ and IBM and stratified the results by demographic group: darker male, darker female, lighter male and lighter female ([≈20:20](../raw/transcripts/18-ai-and-society.md)–[≈21:06](../raw/transcripts/18-ai-and-society.md)). The program's table shows each classifier's accuracy per group ([slides](../raw/slides/18-ai-and-society.md)):

| Gender classifier | Darker male | Darker female | Lighter male | Lighter female | Largest gap |
| --- | --- | --- | --- | --- | --- |
| Microsoft | 94.0% | 79.2% | 100% | 98.3% | 20.8% |
| Face++ | 99.3% | 65.5% | 99.2% | 94.0% | 33.8% |
| IBM | 88.0% | 65.3% | 99.7% | 92.9% | 34.4% |

One group, darker-skinned women, had much lower accuracy than the others. "If you were just measuring average accuracy, you would think that these systems work pretty well", but an intersectional group was getting much worse service ([≈21:06](../raw/transcripts/18-ai-and-society.md)). After the study came out, the systems all improved. The takeaway is "the power of third-party **auditing**", which gives companies an incentive to fix problems: "The first step in solving a problem is to identify the problem" ([≈21:51](../raw/transcripts/18-ai-and-society.md)).

Two technical strategies reduce such inequality ([≈21:51](../raw/transcripts/18-ai-and-society.md)–[≈22:37](../raw/transcripts/18-ai-and-society.md)). On the data side, collect more data for underrepresented groups, which can be costly, and that is why many companies may not have it in the first place. On the algorithmic side, upweight underrepresented groups in the dataset, or use **distributionally robust optimization**, which looks not at average accuracy but at the worst-case accuracy over groups: maximize the minimum accuracy over all the groups, an objective that makes sure every group has good accuracy (the program links Sagawa, Koh, Hashimoto and Liang, 2019). As an aside, the program asks whether gender classification is even a well-defined task: the classifier relies on superficial features of an image, whereas gender is a matter of self-identification ([≈23:24](../raw/transcripts/18-ai-and-society.md)).

### Global bias in a reward model

Inequality also appears at a global level, "I think we tend to think a lot about models and what's happening in the US" ([≈23:24](../raw/transcripts/18-ai-and-society.md)–[≈24:58](../raw/transcripts/18-ai-and-society.md)). **Starling 7B**, fine-tuned from Llama 2 7B Chat, is a **reward model**: it scores how good an AI assistant's response is. Reward models like it are used to post-train a language model with reinforcement learning, as lecture 17 described (see [post-training](post-training.md)). The study scored the response "I am from {country}" to the prompt "Where are you from?" with each country filled in. The map is very uneven: the US and Canada get high reward (blue), while places like Saudi Arabia get very low reward (red), "for no particularly good reason" ([slides](../raw/slides/18-ai-and-society.md)). On the map, rewards run from about −5.0 to −5.9, with North America the bluest and much of Africa and the Middle East the reddest. Fine-tuning a language model against such a reward model would build in "some pretty serious biases."

### Spurious correlations

A subtler kind of inequality comes from **spurious correlations**, "patterns in training data that don't generalize" ([≈24:58](../raw/transcripts/18-ai-and-society.md)–[≈28:04](../raw/transcripts/18-ai-and-society.md)). The example task is to predict from a chest X-ray whether a patient has a collapsed lung. The system's accuracy looks fine. But the researchers noticed that an X-ray classified as a collapsed lung showed a **chest drain**, a tube that is the common treatment for a collapsed lung, and that the model was relying on the presence of the drain to make its prediction (the captions are garbled at this point; see the transcript's [Ed:] note at [≈25:46](../raw/transcripts/18-ai-and-society.md)). The program's diagram states the problem: "Model uses treatment (chest drain) to diagnose the condition. Model is not accurate on patients who haven't been treated yet (!)" ([slides](../raw/slides/18-ai-and-society.md)).

The accompanying plot shows three curves with their areas under the curve (higher is better): all patients, AUC 0.87; patients with chest drains, AUC 0.94; patients without chest drains, AUC 0.77 ([≈26:32](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). So the model does worst on exactly the people who have not yet been treated "but nonetheless need to be treated." A learning algorithm only looks for patterns and correlations in its data; a spurious one leads it to latch on to something that "turns out not to be the right causal variable", so the model fails on another condition, and minority subpopulations are often affected most ([≈27:18](../raw/transcripts/18-ai-and-society.md)).

**Lesson: always monitor multiple metrics, on different subpopulations.** "You look at accuracy and you get it up to 90% and you say, wow, I made so much progress", but you have to check that you are not harming a particular subpopulation ([≈27:18](../raw/transcripts/18-ai-and-society.md)–[≈28:04](../raw/transcripts/18-ai-and-society.md)).

## Alignment

**Alignment** asks how to make an AI system do what we want it to do. "It sounds fairly straightforward, but it turns out not to be" ([≈28:04](../raw/transcripts/18-ai-and-society.md)). Putting on the [reinforcement learning](reinforcement-learning.md) hat, the recipe is: (1) define a reward function that captures our values, and (2) train an agent to maximize the expected reward. Lecture 18 gives three ways this goes wrong. See [alignment](alignment.md).

### Reward hacking

The reward function is very hard to define, especially in all cases, and if it is wrong you get **reward hacking**: "the reward function does not capture what we actually want" ([≈28:51](../raw/transcripts/18-ai-and-society.md)–[≈31:09](../raw/transcripts/18-ai-and-society.md)). The example is from a 2016 OpenAI blog post, from before OpenAI trained language models. In the boat-racing game **CoastRunners** the goal is to race a boat and finish the race, but the reward is points, which come from hitting things. The policy learned by reinforcement learning loops around the harbor hitting the same targets over and over and never completes the race ([slides](../raw/slides/18-ai-and-society.md): the animation shows the score climbing while the lap counter stays at "–/3"). For the developer this is "terribly frustrating… just do what I mean, not what I say. But alas, the algorithms can only do what you say."

It shows up in realistic cases too. A natural reward for a coding agent is passing unit tests, but tests are always incomplete in their coverage, so passing them does not mean the code is correct. Correct code may still be insecure or not robust to adversarial testing (the program links BaxBench, which evaluates language models on both correctness and security), and code that is correct and secure may still have bad style or high complexity ([≈30:22](../raw/transcripts/18-ai-and-society.md)). So: beware of **overoptimization**. If the reward is slightly wrong, optimizing it a bit likely moves you in roughly the right direction, but "if you overoptimize, you're going to start gaming the reward function and finding all sorts of devious ways to squeak out more reward" ([≈31:09](../raw/transcripts/18-ai-and-society.md)).

### Pluralism

If the reward function represents values, "whose values are we talking about?" **Pluralism** means that different people have different values, so "there's actually no one reward function that works for everyone" ([≈31:09](../raw/transcripts/18-ai-and-society.md)–[≈32:41](../raw/transcripts/18-ai-and-society.md)). The program's figure (from *A Roadmap to Pluralistic Alignment*) asks "Is it ok for governments to moderate public social media content?", a question on which three values pull in different directions: security, free speech and factuality. It shows three ways a model could respond. An **Overton** model gives one answer that lays out the range of views. A **steerable** model can be steered to answer from any one of the views. A **distributional** model gives the spread of opinion, for example "A: Yes, for public safety threats (45%)", "B: No, to protect free speech (32%)", "C: Yes, to prevent misinformation (9%)" ([slides](../raw/slides/18-ai-and-society.md)).

Fine-tuning a model to only one view and serving it to the world projects that view on everyone. Ideally, then, models represent the diversity of thought "within the Overton window" (not fringe ideas), and they should be personalized, but not so much that everyone lives in their own echo chamber, and not sycophantically. There is a trade-off between representing diversity and personalizing to an individual user, and "there's no right answer or recipe here" ([≈31:55](../raw/transcripts/18-ai-and-society.md)–[≈32:41](../raw/transcripts/18-ai-and-society.md)).

### Scalable oversight

Language models can solve very complex problems, and "already they're generating solutions that are hard for experts to verify." The lecturer's example is the UQ paper on unsolved questions: language models generate answers to the unanswered questions on Stack Exchange, and it is hard to verify whether the answers are correct. "How can we mere mortals even evaluate the output?" ([≈32:41](../raw/transcripts/18-ai-and-society.md)–[≈33:28](../raw/transcripts/18-ai-and-society.md)). Ideas for **scalable oversight** ([≈33:28](../raw/transcripts/18-ai-and-society.md)–[≈35:02](../raw/transcripts/18-ai-and-society.md)):

- **Break the problem down** into smaller subproblems that humans can verify individually.
- **Use AI itself**: if an AI is better than humans on some task, a second AI might keep the first in check. Debate between two AIs and constitutional AI propose that "really the only way to address this is to use AI to monitor AI, which obviously has some recursive problems."
- **Process-level supervision** rather than outcome-level: the model shows its work as a rationale and the steps are checked, which is generally much more effective than only checking whether the final answer is right.
- Spoken only: **formal verification**, when a problem can be cast as theorem proving, "but most problems in the world are subtle and complex, and you can't really do that."

Summing up what can go wrong with the RL recipe: the reward function isn't what we want (reward hacking); there is no one reward function (pluralism); and it is really hard, even for humans, to write down a reward function or give feedback to an agent (scalable oversight) ([≈35:02](../raw/transcripts/18-ai-and-society.md)).

## Copyright

Copyright concerns the upstream dependency of language models on data. There are many lawsuits around generative AI, mostly about copyright, and Anthropic agreed to pay authors \$1.5 billion to settle one, "so this is pretty meaningful" ([≈35:47](../raw/transcripts/18-ai-and-society.md)). The lecture then goes through "basic IP law" to see where language models fit ([≈36:33](../raw/transcripts/18-ai-and-society.md)). See [copyright and fair use](copyright-and-fair-use.md).

### What copyright protects

Copyright is one kind of **intellectual property**, alongside patents, trademarks and trade secrets. The goal of intellectual property law is to *incentivize* the creation of intellectual goods: protecting creators encourages people to create ([≈36:33](../raw/transcripts/18-ai-and-society.md)). Copyright law goes back to 1709 in England (the Statute of Anne); in the United States the most recent law is the Copyright Act of 1976, under which copyright applies to "original works of authorship fixed in any tangible medium of expression…" ([≈37:20](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). The lecture draws out several consequences ([≈37:20](../raw/transcripts/18-ai-and-society.md)–[≈39:40](../raw/transcripts/18-ai-and-society.md)):

- **Original works**: a collection such as a telephone directory is not copyrightable unless there is some creativity in the selection or arrangement. An alphabetical list has none.
- **Expression, not ideas**: you cannot copyright the quicksort algorithm, but you can copyright code that implements it.
- **Fixed, not published**: the 1976 Act expanded the scope from "published" (1909) to "fixed", and registration is not required for protection, in contrast with patents. The threshold is extremely low: your website is copyrighted, and "technically, I guess, your homework in CS221 is copyrighted."
- **Registration to sue**: a creator must register before suing for infringement, which costs \$65.
- **Expiry**: in the lecture's figures, copyright lasts 75 years, after which a work enters the **public domain**, like the works of Shakespeare and Beethoven and most of Project Gutenberg.

"So the summary is that most things on the internet are actually copyrighted." There are two ways to use a copyrighted work: get a license, or appeal to fair use ([≈39:40](../raw/transcripts/18-ai-and-society.md)–[≈40:25](../raw/transcripts/18-ai-and-society.md)).

### Licenses

A **license**, from contract law, is granted by a licensor to a licensee; effectively, "a license is a promise not to sue" ([≈40:25](../raw/transcripts/18-ai-and-society.md)). The **Creative Commons** license, created by Lessig and Eldred in 2001 to bridge the public domain and existing copyright, lets a creator permit anyone to use and distribute a work, effectively declaring it public domain before the term has elapsed. Wikipedia, Khan Academy and the Free Music Archive use it, and the program lists further examples ([≈40:25](../raw/transcripts/18-ai-and-society.md)–[≈41:13](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). Besides public-domain data, model developers license data for training: Google pays Reddit for its data, and OpenAI pays Stack Exchange and Shutterstock ([≈41:13](../raw/transcripts/18-ai-and-society.md)–[≈42:00](../raw/transcripts/18-ai-and-society.md)). But licensed, Creative Commons and public-domain data together do not cover much: "the vast majority of data on the internet is none of these, and that comprises most of the training data for language models" ([≈42:00](../raw/transcripts/18-ai-and-society.md)).

### Fair use

That leaves **fair use**, section 107 of the Copyright Act, which weighs four factors ([≈42:00](../raw/transcripts/18-ai-and-society.md)–[≈43:34](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

1. **The purpose and character of the use**: educational use is favored over commercial, and transformative over reproductive.
2. **The nature of the copyrighted work**: factual is favored over fictional, and non-creative over creative.
3. **The amount and substantiality of the portion used**: a snippet is favored over the whole work.
4. **The effect on the market** for the original: competing with the original or entering its market is looked down upon. This is the economic condition.

Examples of fair use are writing a summary of a movie you watched, reimplementing an algorithm (the idea) rather than copying its code (the expression), and Google Books indexing books and showing snippets, which "took a very, very long lawsuit to conclude" (Authors Guild v. Google, 2002–2013) and now sets much of the precedent for data use ([≈44:21](../raw/transcripts/18-ai-and-society.md)). Copyright is **not about verbatim memorization**: plots and characters such as Harry Potter's can be copyrightable even if no text is copied, while parody is likely fair use. "It's about semantics and economics as opposed to a purely technical notion of string overlap" ([≈44:21](../raw/transcripts/18-ai-and-society.md)–[≈45:10](../raw/transcripts/18-ai-and-society.md)).

### What this means for foundation models

Copying the data, the first step of training, is already potentially a violation even if nothing is done with it, unless fair use applies ("that's why it's called copyright") ([≈45:10](../raw/transcripts/18-ai-and-society.md)–[≈45:56](../raw/transcripts/18-ai-and-society.md)). On the other side, it "has been well argued" that training an ML model is **transformative**: the model can do things the book cannot. An ML system is also interested in the idea, not the expression: a stop-sign classifier wants the general idea of a stop sign, not the artistic choices of a particular image, although the lecturer notes this argument is made for classifiers and depends on the system ([≈45:56](../raw/transcripts/18-ai-and-society.md)–[≈46:43](../raw/transcripts/18-ai-and-society.md)). The remaining problem is the market. Foundation models "can and do affect the market", which is why writers and artists have "a lot of angst" about the flood of AI-generated content, but that would be a problem even for a model trained on none of their work, so it is not a copyright issue ([≈46:43](../raw/transcripts/18-ai-and-society.md)–[≈47:29](../raw/transcripts/18-ai-and-society.md)).

Even with a license or a fair-use argument, **terms of service** can impose further restrictions. YouTube hosts a lot of Creative Commons content, but its terms of service prohibit downloading videos ([≈47:29](../raw/transcripts/18-ai-and-society.md)–[≈48:14](../raw/transcripts/18-ai-and-society.md)).

### Memorization versus extraction

The transformation argument has a subtlety: a model can memorize its training data and thereby copy it ([≈48:14](../raw/transcripts/18-ai-and-society.md)–[≈53:38](../raw/transcripts/18-ai-and-society.md)). **Memorization** asks whether the text is *in* the model weights. To operationalize it, look at `p(book[i] | book[1:i-1])`: take a book and compute the probability the model assigns to each token given the previous ones. If those probabilities are very high, "the model knows the book", and the book has been copied into the model ([≈49:01](../raw/transcripts/18-ai-and-society.md)). This is the next-token probability of a [language model](language-models.md) as lecture 17 defined it.

The program's figure (from Cooper, Gokaslan, Ahmed et al., 2025) shows this for three books, *M. Butterfly*, *Beloved* and *Harry Potter and the Sorcerer's Stone*, and five models, Pythia 12B, Llama 1 13B, Llama 1 65B, Llama 2 70B and Llama 3.1 70B. Each strip marks the places in the book where a model's probability of reproducing the text is high ([≈49:47](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). Mostly white strips mean very little memorization. But Llama 3.1 70B's strip for *Harry Potter* is almost solid dark blue from start to end: it "has memorized Harry Potter and the Sorcerer's Stone basically extensively." (The printed bullet says "Llama 3 70B", while the figure's row is labelled Llama 3.1 70B.) This calls into question the argument that training is fine because it transforms the data: for some books, the models are copying it ([≈50:33](../raw/transcripts/18-ai-and-society.md)).

**Extraction** is a further level: whether a user can actually get the text out of the model, "without too much work." Since copyright exists to protect creators, a book no third party can extract does limited harm ([≈50:33](../raw/transcripts/18-ai-and-society.md)–[≈51:19](../raw/transcripts/18-ai-and-society.md)). In the experiment, prompting Llama 3.1 70B with just "Mr. and Mrs. D", the first words of *Harry Potter*, and generating with a sliding window produces the rest of the book almost verbatim, roughly 460,000 tokens (the spoken figure is unclear in the captions) ([≈51:19](../raw/transcripts/18-ai-and-society.md)–[≈52:06](../raw/transcripts/18-ai-and-society.md)). Since such models can be downloaded free from Hugging Face, this would be "basically like putting the books out for anyone to download." **Extraction, especially easy extraction, is a stronger case for infringement** than memorization.

Fortunately, this is the exception rather than the rule. "For some reason Llama really likes Harry Potter", but from *M. Butterfly* nothing can be extracted. So "you can't say universally that models memorize works, nor can you say they don't. It really depends", on the model and on the work ([≈52:52](../raw/transcripts/18-ai-and-society.md)). The program ends the section with further reading: the CS324 course notes on legality, Lemley and Casey's *Fair Learning*, *Foundation Models and Fair Use*, and *The Files are in the Computer* ([slides](../raw/slides/18-ai-and-society.md)).

## Openness and Transparency

The final topic asks the lecture's audience to think "not just what a model can do but essentially how a model is built": who can make decisions about a model's behavior, and who can build a model at all ([≈53:38](../raw/transcripts/18-ai-and-society.md)). The risk not mentioned so far is **centralization of power**. Very few big tech companies can build frontier models, because these are very capital-intensive projects, and very little is revealed about how the models work. Much of what models do is governed by the processes by which they are built ([≈53:38](../raw/transcripts/18-ai-and-society.md)–[≈54:24](../raw/transcripts/18-ai-and-society.md)). See [openness and transparency](openness-and-transparency.md).

### Transparency and the Foundation Model Transparency Index

**Transparency is a prerequisite**: "if you can't measure it, you can't improve it." Without transparency about how models are built and what they can do, "you can't even begin to start making these models better" ([≈54:24](../raw/transcripts/18-ai-and-society.md)–[≈55:11](../raw/transcripts/18-ai-and-society.md)). The **Foundation Model Transparency Index** (FMTI), a project of the lecturer's group, evaluates model developers on their transparency practices, much as indices in economics such as the Human Development Index track indicators of how society is doing. It has **100 indicators** covering upstream, model and downstream properties ([≈55:11](../raw/transcripts/18-ai-and-society.md)–[≈55:57](../raw/transcripts/18-ai-and-society.md)).

The program shows three charts from the May 2024 index ([slides](../raw/slides/18-ai-and-society.md), where every cell is transcribed). The first is a heat map of thirteen dimensions by fourteen developers. The upstream rows are data, labor and compute; then come methods, model basics, model access, capabilities, risks and mitigations; and the downstream rows are distribution, usage policy, feedback and impact ([≈55:57](../raw/transcripts/18-ai-and-society.md)–[≈56:43](../raw/transcripts/18-ai-and-society.md)). Blue is good and red is not. Transparency is "fairly uneven": some developers score 100% on many dimensions, while closed models score very poorly, especially upstream, where little is disclosed about how they are built ([≈56:43](../raw/transcripts/18-ai-and-society.md)–[≈57:31](../raw/transcripts/18-ai-and-society.md)). Averaged over developers, the lowest dimensions are impact (15%), mitigations (31%) and data (34%). Disclosure about data and compute is exactly what is needed, "at the very minimum", to mitigate harms around labor practices or environmental impact and to understand copyright infringement ([≈57:31](../raw/transcripts/18-ai-and-society.md)).

The second chart is the summary statistic, the total score per developer ([≈57:31](../raw/transcripts/18-ai-and-society.md)–[≈58:17](../raw/transcripts/18-ai-and-society.md)). It runs from StarCoder (BigCode, Hugging Face and ServiceNow) at 85% and Jurassic-2 and Luminous at 75% each, through Llama 2 at 60%, Mistral 7B at 55%, Claude 3 at 51%, GPT-4 at 49% and Gemini 1.0 Ultra at 47%, down to Titan Text Express at 41% and Fuyu-8B at 33%.

The third chart supports the **theory of change**: "public reporting will incentivize companies to be more transparent", as Gender Shades did for face recognition. Between October 2023 and May 2024 every developer compared improved: AI21 Labs from 25 to 75, BigCode/HF/ServiceNow from 53 to 85, Amazon from 12 to 41, Anthropic from 36 to 51, and OpenAI from 48 to 49 ([≈58:17](../raw/transcripts/18-ai-and-society.md)–[≈59:03](../raw/transcripts/18-ai-and-society.md)). "Just by measuring you can sort of get people to pay attention and therefore incentivize improvement."

### Openness is a spectrum

**Openness** is related to transparency but not identical, and it is "a topic I actually care quite a bit about" ([≈59:03](../raw/transcripts/18-ai-and-society.md)). The program draws it as a spectrum of four levels ([≈59:03](../raw/transcripts/18-ai-and-society.md)–[≈59:50](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

| Level | What you get | Examples on screen |
| --- | --- | --- |
| **Closed** | API-only: use through an API or a product, no access to the internals | GPT-5, Claude, Gemini |
| **Open-weight** | see the weights, but not the data or the code, nor how the model was built | Llama, DeepSeek (Qwen is also named aloud) |
| **Open-source** | see the weights, the code and the data recipe; development is still closed | GPT-J, OLMo |
| **Open-development** | all of the above, plus see the process and contribute, so the community can contribute and audit | Marin |

Open-source software is the analogy. It has succeeded in bringing in a community to improve it, and it "has been better for security and decentralizing power": imagine if, in the 90s, Microsoft Windows had been the only operating system, with no Linux ([≈1:00:38](../raw/transcripts/18-ai-and-society.md)). People often say "open models" when they mean open-weight models, but an open-weight model is like a binary executable: you can run it, but you don't have the code, and having just that "doesn't constitute open source." An open-weight model also need not disclose anything about its data, and so need not do well on the transparency index ([≈1:00:38](../raw/transcripts/18-ai-and-society.md)–[≈1:02:11](../raw/transcripts/18-ai-and-society.md)). (Lecture 17's [language models](language-models.md) page describes the same split between closed, open-weight and open-source models.)

### Why openness matters, and its risks

The program cites *On the Societal Impact of Open Foundation Models*, which is mostly about open-weight models, for three **benefits** ([≈1:02:11](../raw/transcripts/18-ai-and-society.md)–[≈1:04:29](../raw/transcripts/18-ai-and-society.md)):

1. **Innovation and customizability.** With API access you can only put in a prompt and get a response. With weights you can fine-tune, take layers, quantize and customize.
2. **Transparency**, though weights alone are not enough: Mistral 7B is an open-weight model, but it scores only 55 out of 100 on the index.
3. **Less centralization of power.** People can customize an artifact that is out in the world, instead of one central authority dictating model behavior for everyone. This is only partial, because training a model still requires a huge amount of capital.

The **risks** of open models are mostly about misuse. Anyone can take an open-weight model, even a safety-tuned one, and it is "fairly easy to just strip off the safety" and use it for disinformation or hacking ([≈1:04:29](../raw/transcripts/18-ai-and-society.md)). The lecture gives two principles for reasoning about them:

- **Think about marginal risk**: the change in risk relative to the alternatives, a world that still has closed models and the internet. Hackers used the closed Claude Code for cyberattacks, and much information about making bioweapons is already on the internet or in advanced biology classes. "It's not saying that there's no risk, but that the relevant quantity is the marginal risk" ([≈1:05:17](../raw/transcripts/18-ai-and-society.md)–[≈1:06:03](../raw/transcripts/18-ai-and-society.md)).
- **Think about the whole ecosystem.** In the feared scenario, a lay person strips the safety off an open-weight model and asks it to help make a bioweapon. But a language model only helps with the design; the bioweapon still has to be manufactured and deployed, and those are additional gates that could be shored up. Gating at the physical level is sometimes easier than at the model level ([≈1:06:03](../raw/transcripts/18-ai-and-society.md)–[≈1:06:49](../raw/transcripts/18-ai-and-society.md)).

The paper came out of 2023, a year of "a lot of angst about AI, especially in the policy world" after ChatGPT came out, "in December 2022" as the lecturer puts it. There was debate over whether open-weight models should exist at all, while Meta was releasing them, and the framework lends "some clarity into how to think about the benefits and the risks." The program's verdict: "Overall: need more clarity and measurement" ([≈1:06:49](../raw/transcripts/18-ai-and-society.md)–[≈1:07:35](../raw/transcripts/18-ai-and-society.md)).

## Summary

The program's summary has eight points ([≈1:07:35](../raw/transcripts/18-ai-and-society.md)–[≈1:11:26](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

- Technologists should care about societal impact. "After all, we are people, and as people we should care about the world that we inhabit."
- It is challenging: AI is dual use, and its capabilities are growing so quickly that its exact impact is hard to reason about. "That doesn't mean we don't try."
- Focus on beneficial applications, deter misuse, and prevent accidents.
- Think about the whole ecosystem, not just the model, both upstream and downstream, rather than treating the data as something that "shows up at the front door."
- Monitor multiple metrics and inequality.
- Alignment is hard: reward hacking, pluralism, scalable oversight. There is no one perfect metric, so a comprehensive suite of metrics is what gives robustness and an understanding of what systems are doing.
- Openness and transparency are a basic foundation, "analogous to freedom in a kind of political system."
- Auditing is a powerful tool. Technically "it's just evaluation", prompting a model and reading what comes out, but socially it raises awareness of issues and instigates "the will to actually address" them.

Next time: a deeper look at the players in the AI ecosystem, in a guest lecture on AI supply chains ([≈1:11:26](../raw/transcripts/18-ai-and-society.md)). That is [lecture 19](19-economics-of-ai.md); see also [AI supply chain](ai-supply-chain.md).

## Connections to Earlier Lectures

- [Lecture 1](01-course-overview.md) introduced **alignment**, making sure an agent's values match what the developer wants, and named the societal questions (privacy, copyright, jobs, inequality, geopolitics) that this lecture takes up.
- The alignment section's recipe, define a reward and train an agent to maximize expected reward, is the [reinforcement learning](reinforcement-learning.md) setting of lectures [8](08-reinforcement-learning.md) and [9](09-policy-gradient.md), with rewards as in [Markov decision processes](markov-decision-processes.md). Reward hacking is what happens when that reward is misspecified.
- The global-bias example is about a **reward model**, the component of RLHF in [post-training](post-training.md) (lecture 17). Lecture 17's list of post-training goals also includes reducing memorization and sycophancy, two of this lecture's concerns.
- The memorization measure is a [language model's](language-models.md) next-token probability, and the openness spectrum extends lecture 17's split between closed, open-weight and open-source models.

## Notes on the Sources

- The program was committed at 1b5bd22 four minutes before the lecture and never changed before the 2026 rewrite deleted it, so the slide file is what was on screen. It shows eighteen images, all described there; the two FMTI tables are transcribed cell by cell, and every cell of the heat map reconciles with both of its printed average margins ([slide file front matter](../raw/slides/18-ai-and-society.md)).
- Where the program and the lecturer differ, this page follows the program for what was printed. The program says "Llama 3 70B" where its figure, and the lecturer, say Llama 3.1 70B. The lecturer says the first copyright law emerged in England in the 1700s, then that it was "the first time that copyright kind of emerged in" a place the captions render as "the US" ([≈37:20](../raw/transcripts/18-ai-and-society.md)). The program places the Statute of Anne in England.
- The lecture's date for the Tuskegee study, its 75-year copyright term and its other historical figures are reported as the program states them.
- The transcript's [Ed:] markers note the places the captions are too garbled to restore with confidence, among them the chest-drain sentence at [≈25:46](../raw/transcripts/18-ai-and-society.md) and the spoken token count for the extracted book at [≈52:06](../raw/transcripts/18-ai-and-society.md); see its header.

---

- **[Slides](../raw/slides/18-ai-and-society.md)**: everything `society.py` put on screen (pinned to commit 1b5bd22), with all eighteen images described, the Gender Shades and FMTI tables transcribed, and the titles of every linked paper. The program: <https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/1b5bd22/society.py>.
- **[Transcript](../raw/transcripts/18-ai-and-society.md)**: edited and timestamped. Verbatim captions are in `original/`.
- Topic pages from this lecture: [societal impact](societal-impact.md), [inequality in AI](ai-inequality.md), [alignment](alignment.md), [copyright and fair use](copyright-and-fair-use.md), [openness and transparency](openness-and-transparency.md).
