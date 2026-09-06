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
- [x] 09 Policy Gradient — video lOMNskWVeD8 (raw transcript fetched 2026-08-29)
- [x] 09 Policy Gradient — video lOMNskWVeD8 (edited transcript adjudicated 2026-08-29: 96 markers verified, number inventory clean, 2 [Ed: unclear] markers kept, 3 student questions rendered from captions)
- [x] 10 Games I — video SMOD_GiRzb8 (raw transcript fetched 2026-09-07)
- [ ] 10 Games I — video SMOD_GiRzb8 (edited transcript — delegated to subagent, adjudication pending)

## Crawl
- [x] Fetch course site index: https://stanford-cs221.github.io/autumn2025/
- [x] Download linked PDFs and slides
- [x] Write sources.md
- [x] Figure descriptions for slide files 01-05 (vision pass — 2026-08-27: the parent model (deepseek-v4-pro) still cannot perceive images, but subagents run deepseek-v4-flash-vision-exp, which can; 26 figures + 1 video described visually, with a second vision pass auditing the chart- and text-dense figures)
- [x] Figure descriptions for slide file 06 (vision pass, 2026-08-28: 6 static images; numeric figures cross-checked against source-derived values — 8-puzzle Manhattan sum and A* counterexample modified costs both reconcile exactly)
- [x] Slide file for 07 (mdp.py executable, executed 2026-08-28 with edtrace; demo outputs read from the trace; 2 graphviz figures vision-passed against source — no discrepancies; 2 distance plots described from exact trace data)
- [x] Slide file for 08 (reinforcement_learning.py — demo outputs read 2026-08-29 from the published trace var/traces/reinforcement_learning.json; 3 new figures vision-passed — rl-framework, policy_evaluation_recurrence, value_iteration_recurrence; flaky_tram_graph reuses the lecture 7 description)
- [x] Slide file for 09 (policy_gradient.py — demo outputs read 2026-08-29 from the published trace var/traces/policy_gradient.json; 2 photographs vision-passed — robot_kitchen.jpg and mobile_aloha.jpg from the repo's var/files/ cache; rl-framework reuses the lecture 8 description; live-demo vs published-trace discrepancy noted in the variance-reduction section)
- [ ] Slide file for 10 (games.py — demo outputs read 2026-09-07 from the published trace var/traces/games.json; 27 figures vision-pass pending)

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
- [x] wiki/08-reinforcement-learning.md
- [x] RL topic pages (reinforcement learning, q-learning; cross-links added in markov-decision-processes and search-problems pages)
- [x] wiki/09-policy-gradient.md
- [x] Policy gradient topic page (policy-gradients; cross-links added in reinforcement-learning and q-learning pages)
- [x] INDEX.md table of contents (update for lecture 9)
- [ ] wiki/10-games-i.md
- [ ] Games topic pages (cross-lecture concepts)
- [ ] INDEX.md table of contents (update for lecture 10)
- [ ] Re-link Bayesian networks / Turing test mentions in wiki/01-course-overview.md when those lectures are built (unlinked 2026-08-27 because the pages did not exist yet)
- [x] Topic pages (cross-lecture concepts)
- [x] INDEX.md table of contents (update for lectures 3-4)
- [x] INDEX.md table of contents (update for lecture 6)
- [x] INDEX.md table of contents (update for lecture 7)

## Publish
- [x] Commit and push
- [x] PATCH kbUrl onto the catalog entry
