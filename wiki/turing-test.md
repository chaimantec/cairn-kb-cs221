---
title: The Turing Test — the Imitation Game, Objective Measurement, and What Should Replace It
---

# The Turing Test

The Turing test opens the course's history of AI in [lecture 1](01-course-overview.md), and comes back in the course's last session, [lecture 20](20-fireside-chat-conclusion.md), where Percy Liang is asked whether today's language models pass it. The two lectures make complementary points: lecture 1 says why the test mattered, and lecture 20 says why it is no longer the right measure.

## The imitation game (lecture 1)

In 1950 Alan Turing published a paper asking "Can machines think?" ([≈17:09](../raw/transcripts/01-course-overview.md)–[≈17:55](../raw/transcripts/01-course-overview.md); [lecture 1 slides](../raw/slides/01-course-overview.md), "The Turing Test (1950)", which shows the first page of "Computing Machinery and Intelligence" in *Mind*). The real question, the lecturer says, is how you could even tell whether a machine could think. Computers were hardly a thing at the time, so the question belonged almost to philosophy.

Turing's answer was a game, the **imitation game**, more popularly known as the **Turing test** ([≈17:55](../raw/transcripts/01-course-overview.md)–[≈18:42](../raw/transcripts/01-course-overview.md)). A machine and a human each try to convince a human judge that they are the human, and the judge cannot tell which is which. The slide's diagram shows the setup: candidates A (a computer) and B (a person) on one side of a line, and interrogator C on the other, wondering which is which.

## Why it mattered: objective measurement

You can complain about the Turing test, and many people have written about how it is imperfect ([≈18:42](../raw/transcripts/01-course-overview.md)). Its significance, in lecture 1's telling, is that it **grounds a philosophical question**, what intelligence is and what it means to think, **in objective measurement**. That idea of objectivity has been a strong driving force in AI's development: the field's use of benchmarks and measurement to make progress "kind of starts with Turing" ([≈18:42](../raw/transcripts/01-course-overview.md)–[≈19:27](../raw/transcripts/01-course-overview.md)).

Turing also deliberately **left the solution open** ([≈19:27](../raw/transcripts/01-course-overview.md)). He suggested something like machine learning, while many at the time were thinking about logic-based methods, but he did not know which would work. That, the lecturer says, is the nature of research: you do not know the solution, but you can define a north star. The slide puts the open question as "machine learning? logic?"

## Why it is no longer the right measure (lecture 20)

Asked in the fireside chat whether current frontier LLMs pass the Turing test, and if so what the next goal is, Percy first questions whether the test is the right way to measure progress ([≈24:05](../raw/transcripts/20-fireside-chat-conclusion.md)). He recalls mentioning it on the first day and agrees it was historically important in shaping AI's development, but he does not think it is a good measure now ([≈24:54](../raw/transcripts/20-fireside-chat-conclusion.md)). He objects to both of its parts:

- **The imitation.** The test rewards behaving like a human.
- **Humans as the benchmark.** We want AI to be far better than humans at certain things, for example much more reliable, so putting humans as the bar seems shortsighted.

He also objects to **static** tests in general, where a system enters a room and gets a score out, because they can be gamed ([≈25:40](../raw/transcripts/20-fireside-chat-conclusion.md)). His alternative is a metric in the **online** sense: **how many new scientific discoveries AI is producing**. That cannot be gamed. If AI cures cancer, invents a new material or figures out fusion, no one can say it cheated, and "if you cheated somehow and you figured out how to cure cancer, well, good for you" ([≈25:40](../raw/transcripts/20-fireside-chat-conclusion.md)–[≈26:26](../raw/transcripts/20-fireside-chat-conclusion.md)).

The two lectures fit together. Lecture 1 credits the Turing test with starting the field's habit of objective measurement; lecture 20 keeps the demand for objectivity and drops the human yardstick, asking for a measure that is both objective and impossible to game. On measurement more broadly, lecture 20 also argues that a language model's loss on next-token prediction over very long contexts says more about its capability than public leaderboards do ([≈11:40](../raw/transcripts/20-fireside-chat-conclusion.md)–[≈12:27](../raw/transcripts/20-fireside-chat-conclusion.md)), and that fair evaluation is work academia is better placed to do than industry ([≈17:06](../raw/transcripts/20-fireside-chat-conclusion.md)).

## Lectures

- [Lecture 1](01-course-overview.md): Turing's 1950 question, the imitation game, objective measurement, and the open solution.
- [Lecture 20](20-fireside-chat-conclusion.md): why the test is no longer a good measure, and scientific discoveries as an online, ungameable alternative.
