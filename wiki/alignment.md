---
title: Alignment — Reward Hacking, Overoptimization, Pluralism, Scalable Oversight
---

# Alignment

**Alignment** is the problem of making an AI system do what we want it to do. [Lecture 1](01-course-overview.md) introduces it as making sure an agent's values, goals or utility function correspond to what its developer actually wants. Its example is OpenAI wanting ChatGPT to be informative, avoid hallucinations and refuse harmful queries. **Misalignment** is when, despite the best intentions, the AI does something unintended ([≈8:39](../raw/transcripts/01-course-overview.md)). [Lecture 18](18-ai-and-society.md) returns to it as one of four deep-dive topics in AI and society: "It sounds fairly straightforward, but it turns out not to be" ([≈28:04](../raw/transcripts/18-ai-and-society.md)).

## The Reinforcement Learning Recipe

Lecture 18 frames alignment through [reinforcement learning](reinforcement-learning.md), the setting of lectures [8](08-reinforcement-learning.md) and [9](09-policy-gradient.md) ([≈28:04](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)):

1. Define a reward function that captures our values.
2. Train an agent to maximize the expected reward.

Everything that goes wrong with alignment, in lecture 18's account, goes wrong in step 1. The lecture names three failures.

## Reward Hacking: the Reward Isn't What We Want

**Reward hacking** is when "the reward function does not capture what we actually want", and an agent that maximizes it does something its designer never intended ([≈28:51](../raw/transcripts/18-ai-and-society.md)–[≈29:36](../raw/transcripts/18-ai-and-society.md)). The standard example is from a 2016 OpenAI blog post. In the boat-racing game CoastRunners, the goal is to finish the race, but the reward is points, which come from hitting things. The learned policy loops around the harbor hitting the same targets again and again and never finishes. The designer's plea is "do what I mean, not what I say", "but alas, the algorithms can only do what you say."

The same thing happens with coding agents ([≈29:36](../raw/transcripts/18-ai-and-society.md)–[≈31:09](../raw/transcripts/18-ai-and-society.md)). Rewarding code that passes unit tests rewards passing the tests, and tests are always incomplete in their coverage. Code that is correct can still be insecure (the BaxBench benchmark measures both), and code that is correct and secure can still have bad style or high complexity.

The practical advice is to beware of **overoptimization**. Optimizing a slightly wrong reward a little probably moves you in roughly the right direction. Optimizing it hard means "you're going to start gaming the reward function and finding all sorts of devious ways to squeak out more reward" ([≈31:09](../raw/transcripts/18-ai-and-society.md)).

## Pluralism: There Is No One Reward Function

If a reward function represents values, the next question is whose. **Pluralism** is the fact that different people have different values, so "there's actually no one reward function that works for everyone" ([≈31:09](../raw/transcripts/18-ai-and-society.md)–[≈32:41](../raw/transcripts/18-ai-and-society.md)). On a question like "Is it ok for governments to moderate public social media content?", security, free speech and factuality pull in different directions. Fine-tuning a model to one view and serving it to the world projects that view onto everyone. The figure lecture 18 shows (from *A Roadmap to Pluralistic Alignment*) gives three alternatives ([slides](../raw/slides/18-ai-and-society.md)):

- an **Overton** model gives one answer that lays out the range of reasonable views;
- a **steerable** model can be directed to answer from any one view;
- a **distributional** model reports how opinion is spread, for example 45%, 32% and 9% for three answers.

The goals the program states are that models represent the diversity of thought within the Overton window, and that they be personalized without becoming sycophantic or building echo chambers. Representing diversity and personalizing to one user pull against each other, and "there's no right answer or recipe here" ([≈32:41](../raw/transcripts/18-ai-and-society.md)).

## Scalable Oversight: It Is Hard to Write the Reward Down at All

Even with one agreed set of values, someone has to judge the outputs, and models are starting to produce solutions "that are hard for experts to verify". For example, models answer Stack Exchange's unanswered questions, and the answers are hard to check ([≈32:41](../raw/transcripts/18-ai-and-society.md)–[≈33:28](../raw/transcripts/18-ai-and-society.md)). **Scalable oversight** is the problem of supervising a system whose outputs you cannot easily evaluate. The ideas lecture 18 lists ([≈33:28](../raw/transcripts/18-ai-and-society.md)–[≈35:02](../raw/transcripts/18-ai-and-society.md)):

- break the problem into subproblems a human can verify;
- use AI to check AI, through **debate** between two AIs or **constitutional AI**, "which obviously has some recursive problems";
- supervise the **process** rather than the outcome: have the model show its work and check the steps, which is generally much more effective than checking only the final answer;
- formal verification, where a problem can be cast as theorem proving, which most real problems cannot.

## Summary

The lecture's one-line summary of each failure: the reward function isn't what we want (reward hacking); there is no one reward function (pluralism); it is really hard to write down a reward function (scalable oversight) ([≈35:02](../raw/transcripts/18-ai-and-society.md); [slides](../raw/slides/18-ai-and-society.md)). Its closing summary draws the moral that no single metric is the perfect one, so a comprehensive suite of metrics is what gives robustness ([≈1:09:53](../raw/transcripts/18-ai-and-society.md)).

## Alignment in Practice: Reward Models

For language models, the reward in step 1 is usually itself learned. In RLHF a **reward model** is trained on human preference labels, and the language model is then trained as a policy against it ([post-training](post-training.md), lecture 17). Lecture 18 shows how such a reward model can carry bias of its own: Starling 7B scores "I am from {country}" far higher for the US and Canada than for Saudi Arabia ([≈24:11](../raw/transcripts/18-ai-and-society.md); see [inequality in AI](ai-inequality.md)). Lecture 17 also lists making models less sycophantic among post-training's goals, and lecture 18 counts sycophancy as an accident: a side effect of tuning models to please the user ([≈14:55](../raw/transcripts/18-ai-and-society.md)).

## Lectures

- [Lecture 1](01-course-overview.md): alignment and misalignment introduced; the ChatGPT example.
- [Lecture 8](08-reinforcement-learning.md) and [Lecture 9](09-policy-gradient.md): reinforcement learning, the recipe alignment builds on.
- [Lecture 17](17-language-models.md): RLHF and safety tuning; see [post-training](post-training.md).
- [Lecture 18](18-ai-and-society.md): reward hacking (CoastRunners, coding agents), overoptimization, pluralism, scalable oversight.
