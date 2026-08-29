# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

## Transcripts
- [x] 01 Course Overview and AI Foundations — video yaLEGZuIIgE
- [x] 02 Learning I — video ypZJaTqrNdk
- [x] 03 Learning II — video Mbe5ICIUw5Q
- [x] 04 Learning III — video 89NND-Ca0yY
- [x] 05 Search I — video fPESauMaJYA
- [x] 06 Search II — video 4Iu4KPVbnAY
- [x] 07 Markov Decision Processes — video 2ZtF1j3n6XE
- [x] 08 Reinforcement Learning — video 34Hk2v2kwg4 (edited transcript adjudicated 2026-08-29: 101 markers verified, number inventory clean, 6 boundary splits restored)

## Crawl
- [x] Fetch course site index: https://stanford-cs221.github.io/autumn2025/
- [x] Download linked PDFs and slides
- [x] Write sources.md
- [x] Figure descriptions for slide files 01-05 (vision pass — 2026-08-27: the parent model (deepseek-v4-pro) still cannot perceive images, but subagents run deepseek-v4-flash-vision-exp, which can; 26 figures + 1 video described visually, with a second vision pass auditing the chart- and text-dense figures)
- [x] Figure descriptions for slide file 06 (vision pass, 2026-08-28: 6 static images; numeric figures cross-checked against source-derived values — 8-puzzle Manhattan sum and A* counterexample modified costs both reconcile exactly)
- [x] Slide file for 07 (mdp.py executable, executed 2026-08-28 with edtrace; demo outputs read from the trace; 2 graphviz figures vision-passed against source — no discrepancies; 2 distance plots described from exact trace data)
- [x] Slide file for 08 (reinforcement_learning.py — demo outputs read 2026-08-29 from the published trace var/traces/reinforcement_learning.json; 3 new figures vision-passed — rl-framework, policy_evaluation_recurrence, value_iteration_recurrence; flaky_tram_graph reuses the lecture 7 description)

## Wiki
- [x] wiki/01-course-overview.md
- [x] wiki/02-learning-i.md
- [x] wiki/03-learning-ii.md
- [x] wiki/04-learning-iii.md
- [x] wiki/05-search-i.md
- [x] Search topic pages (search problems, dynamic programming, beam search)
- [x] wiki/06-search-ii.md
- [x] Search II topic pages (uniform cost search, A*, heuristics from relaxation)
- [x] wiki/07-markov-decision-processes.md
- [x] MDP topic pages (markov decision processes, monte carlo evaluation, policy evaluation, value iteration; cross-links added in 01, 05-topic, 06 pages)
- [ ] wiki/08-reinforcement-learning.md
- [ ] RL topic pages (reinforcement learning, model-based vs model-free, bootstrapping/Q-learning)
- [ ] INDEX.md table of contents (update for lecture 8)
- [ ] Re-link Bayesian networks / Turing test mentions in wiki/01-course-overview.md when those lectures are built (unlinked 2026-08-27 because the pages did not exist yet)
- [x] Topic pages (cross-lecture concepts)
- [x] INDEX.md table of contents (update for lectures 3-4)
- [x] INDEX.md table of contents (update for lecture 6)
- [x] INDEX.md table of contents (update for lecture 7)

## Publish
- [x] Commit and push
- [x] PATCH kbUrl onto the catalog entry
