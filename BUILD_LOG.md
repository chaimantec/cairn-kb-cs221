# Build log — CS221

Finished runs, moved verbatim from `TODO.md` once committed and pushed. Never read by the chat; a later build reads it for precedent. Open work lives in `TODO.md`; general lessons go in `AGENTS.md`.

## Index

| Lecture | Runs |
| ------- | ---- |
| 1–4 | 2026-07-26 (pre-log); source links re-pinned 2026-10-22 |
| 5 | 2026-08-27 (pre-log), with the figure vision pass for lectures 1–5; source links re-pinned 2026-10-22 |
| 6–7 | 2026-08-28 (pre-log); source links re-pinned 2026-10-22 |
| 8–9 | 2026-08-29 (pre-log); source links re-pinned 2026-10-22 |
| 10 | 2026-09-07 (pre-log); source links re-pinned 2026-10-22 |
| 11 | 2026-10-22 |
| 12 | 2026-10-24 |

## Runs 2026-07-26 to 2026-09-07 — lectures 1–10 (pre-log)

`TODO.md` was kept by category rather than by run until 2026-10-22, so these lines are moved verbatim under their original headings; the git history (commits bf2df20 to 9aed755) dates each one. Lecture 10's transcript adjudication and figure findings are also recorded in `kb.json`'s provenance caveats.

### Transcripts

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
- [x] 10 Games I — video SMOD_GiRzb8 (edited transcript adjudicated 2026-09-07: 95 markers verified, number inventory clean, 0 ratio outliers; adjudication reverted one over-correction at [1:37] — "environment is random and unknown" kept as captioned — and kept the word "left" in the coin-policy sentence at [46:52]; 1 student question rendered)

### Crawl

- [x] Fetch course site index: https://stanford-cs221.github.io/autumn2025/
- [x] Download linked PDFs and slides
- [x] Write sources.md
- [x] Figure descriptions for slide files 01-05 (vision pass — 2026-08-27: the parent model (deepseek-v4-pro) still cannot perceive images, but subagents run deepseek-v4-flash-vision-exp, which can; 26 figures + 1 video described visually, with a second vision pass auditing the chart- and text-dense figures)
- [x] Figure descriptions for slide file 06 (vision pass, 2026-08-28: 6 static images; numeric figures cross-checked against source-derived values — 8-puzzle Manhattan sum and A* counterexample modified costs both reconcile exactly)
- [x] Slide file for 07 (mdp.py executable, executed 2026-08-28 with edtrace; demo outputs read from the trace; 2 graphviz figures vision-passed against source — no discrepancies; 2 distance plots described from exact trace data)
- [x] Slide file for 08 (reinforcement_learning.py — demo outputs read 2026-08-29 from the published trace var/traces/reinforcement_learning.json; 3 new figures vision-passed — rl-framework, policy_evaluation_recurrence, value_iteration_recurrence; flaky_tram_graph reuses the lecture 7 description)
- [x] Slide file for 09 (policy_gradient.py — demo outputs read 2026-08-29 from the published trace var/traces/policy_gradient.json; 2 photographs vision-passed — robot_kitchen.jpg and mobile_aloha.jpg from the repo's var/files/ cache; rl-framework reuses the lecture 8 description; live-demo vs published-trace discrepancy noted in the variance-reduction section)
- [x] Slide file for 10 (games.py — demo outputs read 2026-09-07 from the published trace var/traces/games.json; 27 figures vision-passed 2026-09-07, ground-truth cross-checked: all tree values and the face-off matrix (1, 2, −5, 5) reconcile with the trace; game 2's coin-flip rule and the chess weights confirmed against the transcript; body's eval-functions recurrence notes the printed d/d−1 depth asymmetry)

### Wiki

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
- [x] wiki/10-games-i.md
- [x] Games topic pages (games, minimax, alpha-beta pruning, evaluation functions; cross-links added in policy-evaluation, value-iteration, q-learning, search-problems and heuristics pages)
- [x] INDEX.md table of contents (update for lecture 10)
- [x] Topic pages (cross-lecture concepts)
- [x] INDEX.md table of contents (update for lectures 3-4)
- [x] INDEX.md table of contents (update for lecture 6)
- [x] INDEX.md table of contents (update for lecture 7)

### Publish

- [x] Commit and push
- [x] PATCH kbUrl onto the catalog entry

## Run 2026-10-22 — lecture 11 (Games II), and source-link pinning for lectures 1–10

Covered lecture 11 end to end: transcript, slide file, figures, edited transcript, wiki page, three topic pages, cross-links, index, `kb.json`, `sources.md`. Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5.

### Transcripts

- [x] 11 Games II — video 9CKRoKFdS5Y (raw transcript fetched 2026-10-22: 95 markers, ~10.6k words)
- [x] 11 Games II — edited transcript (adjudicated 2026-10-22: 95 markers identical, all paragraph word ratios 0.75–1.03, number inventory clean apart from 4 deliberate fraction fixes — "71 12ths"→7/12ths ×2, "1112th"→1/12, "512ths"→5/12ths; ~120 number words written as digits, all checked numeric; 2 [Ed: unclear] markers; 1 audible + 3 inaudible student turns; Samuel "chess program" slip kept as spoken)

### Crawl

- [x] Slide file for 11 (td_learning.py + simultaneous_games.py pinned at commit cbedcb10, the "lecture 11" commit of 2025-10-27 — both files were rewritten in 2026, so main is NOT the version taught; demo outputs from the traces at the same commit; 24 new figures vision-passed, 2 reused from lecture 10)

### Wiki

- [x] wiki/11-games-ii.md
- [x] Games II topic pages (td-learning, simultaneous-games, nash-equilibrium; cross-links added in 10-games-i, games, minimax, evaluation-functions, q-learning, reinforcement-learning, policy-evaluation)
- [x] INDEX.md table of contents (update for lecture 11)

### Publish

- [x] kb.json and sources.md for lecture 11 (also: every slide-file source link for lectures 1–10 re-pinned from `main` to its last pre-rewrite commit — the lecture repo's main was rewritten from 2026-09-22; 15 pinned URLs checked 200; table in sources.md; pinning rule added to AGENTS.md; kb.json executableLectures redefined as programs, 15)
- [x] verify_kb.py clean (hard checks), review read; its math step reported "skipped", so check_math.mjs was run directly: lecture 11 files and every line this run added pass both the chat and --github checks; chat 0 errors KB-wide
- [x] Commit and push lecture 11 (235b866, 20f1d13)
- [x] Create BUILD_LOG.md; move finished sections out of TODO.md (TODO.md keeps only open work)
### Findings

- **The lecture repo moved under us.** `stanford-cs221/autumn2025-lectures` rewrote every lecture file on `main` from 2026-09-22 for a later offering, so a build reading `main` now gets the wrong lecture. Lecture 11's sources were found by date: commit cbedcb10 "lecture 11" landed 2025-10-27 20:22 UTC, eight minutes before the 1:30pm PDT lecture, adding `td_learning.py`, `simultaneous_games.py`, both traces and 28 images; its README diff names the two traces. Both traces' embedded source is byte-identical to the pinned files. The 13 `main` links in slide files 01–10 were re-pinned to each file's last commit before the rewrite; no commit touched those files between 2025-12-02 and 2026-09-22, so those are the versions that were transcribed. The general rule is in `AGENTS.md`.
- **Figures read in the parent.** Lecture 11's 24 new figures are small PNGs and JPEGs, about 1k tokens each and about 20k in all, against ~18k for a single PDF page render, so the parent (which has vision this run) read them directly instead of spawning a vision agent. Four images in the commit (`td-pi-agent-opp.png`, `nash-morra.png`, `nash-prisoners.png`, `nash-collaborative-morra.png`) are never displayed by the source and were not described. The dice pips in `backgammon2.jpg` are illegible at 287px and were not guessed.
- **Ground-truth checks instead of a second vision audit.** The Morra payoff matrix in three figures matches the code. The five traced `evaluate_game` values (0, −0.5, 0.5, −0.5, 0.5) and the traced optimum −0.08333 reconcile with it. The lines 5p−3 and −7p+4 cross at p = 7/12 with value −1/12. The pure-strategy trees' −3 and 2 follow from the matrix, and the backgammon feature values match the board drawn.
- **Transcript adjudication.** 95 markers identical; per-paragraph word ratios 0.75–1.03, none outside the band; the number inventory is missing only the four deliberate fraction restorations ("71 12ths"→7/12ths ×2, "1112th"→1/12, "512ths"→5/12ths). The edit added ~120 digits, and a per-paragraph check showed every one is a number word written as a digit, apart from the three fraction-fix paragraphs. The agent said it read the slide file only by grep, so the parent re-checked every restored proper noun against it: all are present except "Bayesian networks" and "state-based models", which are spoken-only. The header was corrected to say so.
- **Lecturer slips, kept as spoken and flagged in the wiki:** Samuel's checkers program called "a chess program" at 32:39; B's best response to a uniform A first given as "play two" and corrected after a student at 54:34–55:21; "max over these two" corrected to min at 58:35; "about 5 million games" against the slide's 4.9 million.
- **`kb.json` `executableLectures`** had been a running counter one below the number of lectures covered (4 at five lectures, 9 at ten), matching neither lectures nor programs. It is now the number of Python programs transcribed (15), with a note.
- **Math.** `verify_kb.py` reports its math step as skipped ("did not return JSON"), so `check_math.mjs` was run directly, with and without `--github`. Lecture 11's files and every line this run added pass both. Two traps found in this run's own text: a space before a closing `$` (the chat does not render it), and a formula ending in `)` immediately followed by `)` (GitHub shows it as raw text). KB-wide, 96 GitHub-only errors remain in lectures 1–10 pages; logged in `TODO.md`.
- **Stale sibling claims.** `cs224n/SEE_ALSO.md` and `cs336/SEE_ALSO.md` both describe this KB as covering lectures 1–4 with nothing on search, MDPs or games, which has been false since lecture 5. Those are separate repos, so this is logged in `TODO.md` pending the user's go-ahead.

## Run 2026-10-24 — lecture 12 (Bayesian Networks I)

Covered lecture 12 end to end: transcript, slide file, figures, edited transcript, wiki page, four topic pages, cross-links, index, `kb.json`, `sources.md`. Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5.

### Transcripts

- [x] 12 Bayesian Networks I — video ec2rCf4iIqU (raw transcript fetched 2026-10-24: 100 markers, ~11k words)
- [x] 12 Bayesian Networks I — edited transcript (adjudicated 2026-10-24: 100 markers identical, paragraph word ratios 0.73–1.00, number inventory clean apart from garbled-decimal restorations — "05"→0.05, "0 n5"→0.95, "28"→0.28, "027"→0.27, "08 and 2"→0.8 and 0.2 — and caption duplicates; one dropped "100" at [56:09] restored by the parent; 3 [Ed: unclear] markers; 2 inaudible student questions + 1 inaudible response; slips kept as spoken)

### Crawl

- [x] Slide file for 12 (bayes.py pinned at commit f22938e, "add bayes", 2025-10-29 13:26 PDT, four minutes before the lecture; trace's embedded source byte-identical; demo outputs from var/traces/bayes.json at the same commit; 3 new figures vision-passed, 2 reused from lectures 1 and 10)

### Wiki

- [x] wiki/12-bayesian-networks-i.md
- [x] Bayesian networks I topic pages (bayesian-networks, probabilistic-inference, explaining-away, rejection-sampling) and cross-links from 01-course-overview, 11-games-ii, einops, reinforcement-learning
- [x] Re-link the Bayesian networks mentions in wiki/01-course-overview.md (half of the older open Wiki item; its Turing test half stays open)
- [x] INDEX.md table of contents (update for lecture 12)

### Publish

- [x] kb.json and sources.md for lecture 12
- [x] BUILD_LOG.md — lecture 12's record (this section)
- [x] verify_kb.py clean (all hard checks), review read: 15 open TODO boxes confirmed open, no ranking claims, no images; its math step again reported "skipped", so check_math.mjs was run directly: chat 0 errors KB-wide; --github found 6 errors in this run's slide and wiki files, all fixed, leaving the 96 pre-existing lecture 1–10 errors
- [x] Commit and push lecture 12 (25a67a5)
- [x] Move this run's section to BUILD_LOG.md; TODO.md keeps only open work

### Findings

- **Pinning.** Commit f22938e "add bayes" landed 2025-10-29 20:26 UTC (1:26pm PDT), four minutes before the lecture, adding `bayes.py`, its trace, `alarm-bayes.png`, `medical-bayes.png`, `hmm.png` and the cached arXiv metadata for 2502.01236; its README diff adds "Wed Oct 29: bayes i (lecture 12)". The trace's embedded source is byte-identical to the pinned file. The only later pre-rewrite commit to `bayes.py`, ed66b66 (lecture 13), added two `@inspect` annotations to `hidden_markov_model` and nothing else, so the lecture-1–10 convention (last pre-rewrite commit) would have given the same content here; the lecture's own commit is pinned anyway, per the rule.
- **Trace vs. class.** The numbers read out in class match the trace: one accepted sample of ten ("oh, I get one, I guess"), 0.56 for the 1,000-sample alarm estimate (trace 0.5686 = 29/51), 0.26 for the medical estimate (trace 0.2687 = 18/67), and "most likely position 1" for the HMM (trace 37/53). The accepted-sample counts (51, 67, 53) are derived from the traced fractions; only the 10-sample run was stepped into, so only its `total_count` is in the trace.
- **Figures read in the parent.** Three new images, all plain node-and-arrow diagrams (414×305, 565×439, 871×302 px), read directly and checked against the code's factorizations: the einsum `"c, a, c a h, a i -> c a h i"` fixes the medical edges, `p(a | b, e)` the alarm's, `num_steps = 5` the HMM's length. `perceive-reason-act-learn.png` and `game-tree.png` are byte-identical to lecture 10's pinned versions and were not re-described.
- **Transcript adjudication.** 100 markers identical; word ratios 0.73–1.00 with no outliers; the number inventory differs only by garbled-decimal restorations ("05"→0.05, "0 n5"→0.95, "28"→0.28, "027"→0.27, "08 and 2"→0.8 and 0.2, the joint table at 7:05–8:38) and dropped caption duplicates ("0.05 5", "0.26 26", "0.001 001", "x4 x4 5x6"). One real loss: the edit dropped "100" at 56:09 ("like 100… really good confidence"), restored by the parent. Restored terms not in the slide file (Markov decision processes, expectimax, NumPy, NP-hard, heuristic) are spoken-only and listed so in the header. Three [Ed: unclear] markers kept: 38:57 "divide by 09" (trace P(A=1) = 0.0975), 52:13 "6 of the time" (table 0.5832), 1:10:45 "floor line network".
- **Lecturer slips, kept as spoken:** "the output is B and A" for the joint einsum (33:32); "cough" for cold in the medical rejection-sampling result (1:11:33); P(cold | cough) quoted as 0.28 and then 0.27 (exact 0.2778). Spoken examples that differ from the slide: the "prior knowledge" advantage is illustrated with the alarm system in speech and with Mendelian inheritance and laws of physics on the slide; the wiki gives both.
- **Source typos kept with [sic]:** "secretely", "heterogenously"; the source's unclosed parenthesis in the query example and the truncated sentence "we generalize evidence (e.g., A = 1) and query (B) to" are noted in the slide file.
- **Builder slip caught before commit.** The first draft of the wiki page embedded the three figures as `raw/images/` links, but this KB has no committed images (the user never opted in, and `kb.json` has no `materials.images`). They were replaced with prose before anything was published. `verify_kb.py`'s image check would also have flagged the missing files, but the lesson is to check `kb.json` for `materials.images` before writing any figure into a page.
- **Math.** GitHub-only traps hit in this run's own text: `<` in inline math (use `\lt`), and a formula closed directly before `)` or after `"`. All fixed.
