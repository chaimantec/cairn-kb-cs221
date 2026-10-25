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
| 13 | 2026-10-24 (a later run the same day) |
| 14 | 2026-10-24 (a third run the same day) |
| 15 | 2026-10-25 |
| 16 | 2026-10-25 (a later run the same day) |
| 17 | 2026-10-25 (a third run that day) |
| 18 | 2026-10-25 (a fourth run that day) |
| 19 | 2026-10-26 |

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

## Run 2026-10-24 — lecture 13 (Bayesian Networks and Gibbs Sampling)

Covered lecture 13 end to end: transcript, slide file, edited transcript, wiki page, three topic pages, cross-links, index, `kb.json`, `sources.md`. Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5 (both confirmed from the session logs).

### Transcripts

- [x] 13 Bayesian Networks and Gibbs Sampling — video Dk7Kqqehzjk (raw transcript fetched 2026-10-24: 98 markers, ~10.4k words; slug `13-gibbs-sampling`, after the lecture's own title)
- [x] 13 — edited transcript (adjudicated 2026-10-24: 98 markers identical, paragraph word ratios 0.81–1.08, number inventory clean apart from garbled-decimal restorations and digits for spoken numbers; 3 [Ed: unclear] markers; 1 inaudible student question + 2 floor responses; parent fixed one garbled sentence at [49:43], a wrong header timestamp, and trimmed the slips list to genuine slips)

### Crawl

- [x] Slide file for 13 (gibbs_sampling.py pinned at commit ed66b66, "add Gibbs sampling", 2025-11-03 1:27pm PST, three minutes before the lecture; trace's embedded source byte-identical; demo outputs from var/traces/gibbs_sampling.json at the same commit; no new figures — alarm-bayes.png and medical-bayes.png byte-identical to lecture 12's; post-lecture commit ce25241's three changes recorded at the end of the file)

### Wiki

- [x] wiki/13-gibbs-sampling.md
- [x] Topic pages gibbs-sampling, markov-blanket, conditional-independence; cross-links from bayesian-networks, probabilistic-inference, rejection-sampling, explaining-away, 12-bayesian-networks-i
- [x] INDEX.md table of contents (update for lecture 13)

### Publish

- [x] kb.json and sources.md for lecture 13
- [x] verify_kb.py clean (all hard checks), review read: open TODO boxes confirmed open, no images; its math step again reported "skipped", so check_math.mjs was run directly: chat 0 errors KB-wide; --github found 3 issues in this run's slide file (two `\,`, one formula closed before `)`), all fixed, leaving the 96 pre-existing lecture 1–10 errors
- [x] Commit and push lecture 13 (8067f2a)
- [x] BUILD_LOG.md — lecture 13's record; move this section there; TODO.md keeps only open work

### Findings

- **Pinning.** Commit ed66b66 "add Gibbs sampling" landed 2025-11-03 21:27 UTC (1:27pm PST; daylight time had ended the day before), three minutes before the lecture, adding `gibbs_sampling.py`, its trace, `normalize_dict` in `util.py`, and two `@inspect` annotations in `bayes.py`; its README diff adds "Mon Nov 3: gibbs sampling (lecture 13)". The trace's embedded source is byte-identical to the pinned file. No images were added; the two shown are lecture 12's, with identical blob hashes at both commits.
- **The course corrected the file the evening after the lecture** (ce25241, 2025-11-04 7:21pm PST, before lecture 14's commit 6ba9ac8): the Markov-blanket sentence was rewritten to "local conditional probabilities that involve the variable we're sampling... requires knowing all involved variables" (the co-parents point a student raised at 45:07); `markov_prob`'s table indices were swapped from `[child, parent]` to `[parent, child]`, which changes no number because both telephone tables are symmetric; and "C and H are independent given A", false for the medical network and not discussed in class, was deleted. The pinned version is the one taught; the slide file transcribes it and lists the three changes; the wiki flags the false line.
- **Trace vs. class.** Every number read out matches the trace: 0.44 (300-sample rejection, 0.4483 = 13/29), 0.65 (telephone rejection, 32/49), 0.02/0.32/0.34 and "5%" (0.0588) for the hand-worked B update, 0.08/0.32 → 0.2/0.8 for the A update, 0.68 (100-iteration Gibbs), "6" and "33" for the alarm Gibbs runs (0.65, 0.335). Accepted counts (29, 49) are derived from the traced fractions.
- **A number the lecture does not give.** The telephone network's exact $P(A = 1 \mid C = 1)$ is 0.68; the lecturer says only that it can be computed. The wiki pages give it, labelled as computed for the KB from the lecture's own tables, because a learner comparing 0.65 and 0.68 will ask. The alarm's Gibbs failure (0.65, 0.335 vs. 0.513) is not diagnosed in the lecture, and the wiki says so rather than supplying a diagnosis.
- **Source typos kept with [sic]:** "(D = 1)" for the telephone evidence $C = 1$; example 3's first line printing $p(b \mid a)$ for $p(b)$; example 3's conditional printed without division by $P(C = c)$; the general procedure's steps numbered "3." twice; "We're interesting in" in a code comment.
- **Transcript adjudication.** 98 markers identical; word ratios 0.81–1.08, no outliers; the per-paragraph number inventory differs only by decimal restorations fixed by the slide file ("08"→0.8, "02"→0.02, "34"→0.34, "68"→0.68, "33"→0.33 and so on), digits for spoken numbers, and bracketed editorial notes. The agent's slips list had called rounded values ("0.51", "one out of 10,000") and a correct reading of example 4's factors slips; the parent moved the rounded values to their own line, dropped the non-slip, and noted that "0.999" for 0.9999 may be the captions' loss rather than the lecturer's. Restored terms not in either slide file are spoken-only: round-robin, combinatorial, co-parents, primitive, "marrying the parents", pseudocode.

## Run 2026-10-24 — lecture 14 (Bayesian Networks and Learning)

Covered lecture 14 end to end: transcript, slide file, figures, edited transcript, wiki page, five topic pages, cross-links, index, `kb.json`, `sources.md`. Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5 (both confirmed from the session logs).

### Transcripts

- [x] 14 Bayesian Networks and Learning — video 4d9V6Sxa6gU (raw transcript fetched 2026-10-24: 104 markers, ~10.9k words; slug `14-bayesian-networks-learning`)
- [x] 14 — edited transcript (Sonnet copy-edit, adjudicated 2026-10-24: 104 markers identical, paragraph word ratios 0.76–1.09, number inventory clean apart from decimal restorations and digits for spoken numbers; 9 [Ed: unclear] markers; 3 student turns; parent removed LaTeX from the body, corrected the HMM provenance note, reclassified two non-slips, marked "can't get stuck" inline)

### Crawl

- [x] Slide file for 14 (bayes_learning.py pinned at commit 6ba9ac8, "add Bayes learning", 2025-11-05 1:25pm PST, five minutes before the lecture; trace's embedded source byte-identical; demo outputs from var/traces/bayes_learning.json at the same commit; 6 new figures read in the parent (claude-opus-5-5), alarm-bayes.png byte-identical to lecture 12's; post-lecture commit 0cd7d5b's changes recorded at the end of the file)

### Wiki

- [x] wiki/14-bayesian-networks-learning.md
- [x] Topic pages maximum-likelihood, parameter-sharing, hidden-markov-models, laplace-smoothing, expectation-maximization; cross-links from bayesian-networks, conditional-independence, 01-course-overview, 03-learning-ii, 12-bayesian-networks-i, 13-gibbs-sampling, rejection-sampling
- [x] INDEX.md table of contents (update for lecture 14)

### Publish

- [x] kb.json and sources.md for lecture 14
- [x] verify_kb.py clean (all hard checks), review read: open TODO boxes confirmed open, no images; its math step again reported "skipped", so check_math.mjs was run directly: chat 0 errors KB-wide; --github 0 issues in this run's files, leaving exactly the 96 errors and 67 warnings pre-existing in lectures 1–10
- [x] Commit and push lecture 14 (6f72ff9)
- [x] BUILD_LOG.md — lecture 14's record; move this section there; TODO.md keeps only open work

### Findings

- **Pinning.** Commit 6ba9ac8 "add Bayes learning" landed 2025-11-05 21:25 UTC (1:25pm PST), five minutes before the lecture, adding `bayes_learning.py`, its trace, and six images (`pgm-r`, `pgm-g-r`, `pgm-g-a-r`, `pgm-g-r1-r2`, `parameter-sharing`, `pgm-hmm`); its README diff adds "Wed Nov 5: learning (lecture 14)". The trace's embedded source is byte-identical to the pinned file. `alarm-bayes.png` has blob 7d72c88a at both 6ba9ac8 and f22938e.
- **The course revised the file four days later** (0cd7d5b, 2025-11-09 6:32pm PST, the day before lecture 15): `supervised_learning` → `fully_observable_learning` and `introduce_fully_observed_setting` → `introduce_fully_observable_setting` (the lecturer had said at 51:40 that "fully observable" would be the more accurate name), the HMM joint's printed `p_E(e_1 | h_1)` → `p_emit`, two lines made bullets, comments and `@clear` trace annotations. No number changed. The slide file transcribes the pinned version and lists the changes.
- **Trace vs. class.** Every number read out matches the trace: 3/5 and 2/5, 2/3 and 1/3, the board's 2/7 and 1/7 for λ = 1, EM's "0.69" and "0.3" (0.6923, 0.3077), "iteration four... pretty close to one and zero" (0.9999977), and the second EM run's "0.9" and "0.75" (0.9124 for rating 1 given drama, 0.7548 for rating 2 given comedy). The second EM run and all Laplace runs are `@stepover`, so only their final values are in the trace.
- **Figures read in the parent.** Six new images, five plain node-and-arrow diagrams (131×127 to 701×445 px) and `parameter-sharing.png` (1033×491), read directly. The parameter-sharing figure's two tables (c 2/5, d 3/5; d 3 1/6, d 4 3/6, d 5 2/6, c 1–5 except 3 at 1/4) match the traced `parameter_sharing` output exactly.
- **A source detail the wiki surfaces.** `expectation_maximization` shares one `p_r_given_g` between R1 and R2 (the lecturer says so at 1:10:16), whereas the fully observed example just before it learns separate R1 and R2 tables. Both are recorded as printed.
- **Not in the source.** The whiteboard game on conditional independence (9:22–11:44) is known only from the transcript, which does not record what was drawn at each step; the wiki states the lessons said aloud and says the drawings are not recoverable. The board's EM weighted data set and Laplace counts are reconstructed from the trace, which they match.
- **Source typos kept with [sic]:** "When do you parameter sharing?", "heterogenous", "Not that we can only recover" (for "Note that"), and the HMM's `p_E`.
- **Transcript adjudication.** 104 markers identical; word ratios 0.76–1.09, no outliers; number differences are decimal restorations ("69 and.3" → 0.69 and 0.3, "04 and point 6" → 0.4 and 0.6), digits for spoken numbers, "22" → "2, 2", and "1,00" → "1,000". The parent removed LaTeX the agent had written into the transcript body (this KB's edited transcripts keep notation as speech), corrected the header's claim that the HMM program was a lecture 1 demo (it was lecture 12's), reclassified two "slips" that were correct readings (the 0.6/0.4 → 0.69/0.3 update is the p_R row; "comedy is 0.75" is p(2 | comedy)), and marked two readings inline: "the smoothing lambda goes to zero" for the caption's "the the ghost is zero" (1:02:32), and "EM can't get stuck" (1:18:07), where the sense requires "can". Nine [Ed: unclear] markers in all. Restored names all appear in the slide files (Lagrange, Laplace, Markov, Gibbs, Bayesian); spoken-only: CS228 ("228"), cross entropy, perplexity, bespoke, bootstrap.
- **Topic pages.** Five new (maximum likelihood, parameter sharing, hidden Markov models, Laplace smoothing, expectation maximization). Maximum likelihood and hidden Markov models link lectures 3 and 12 respectively; EM links lecture 1's slide listing it as a learning method.

## Run 2026-10-25 — lecture 15 (Logic I)

Covered lecture 15 end to end: transcript, slide file, figures, edited transcript, wiki page, four topic pages, cross-links, index, `kb.json`, `sources.md`. Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5.

### Transcripts

- [x] 15 Logic I — video Q7V13XriJEc (raw transcript fetched 2026-10-25: 95 markers, ~10.5k words; slug `15-logic-i`)
- [x] 15 — edited transcript (Sonnet copy-edit, adjudicated 2026-10-25: 95 markers identical, paragraph word ratios 0.81–1.02, number inventory clean apart from "04" → 0.4 and digits for spoken numbers; no `$` in the body; 5 [Ed: unclear] markers; parent removed an unneeded unclear marker at [0:05], marked a student question at [38:05], reclassified two non-slips and recorded two slips kept as spoken)

### Crawl

- [x] Slide file for 15 (propositional_logic.py pinned at commit 0116e139, "add logic 1", 2025-11-10 1:30pm PST as the lecture began; trace's embedded source byte-identical; demo outputs from var/traces/propositional_logic.json at the same commit; 16 new figures read in the parent (claude-opus-5-5); perceive-reason-act-learn.png byte-identical to lecture 12's; post-lecture commits af5f29c1 and 79ab1572 recorded at the end of the file)

### Wiki

- [x] wiki/15-logic-i.md
- [x] Topic pages propositional-logic, entailment, satisfiability, inference-rules; cross-links from 14-bayesian-networks-learning, probabilistic-inference, bayesian-networks, 01-course-overview
- [x] INDEX.md table of contents (update for lecture 15)

### Publish

- [x] kb.json and sources.md for lecture 15
- [x] verify_kb.py clean (all hard checks), review read: open TODO boxes confirmed open, no images; its math step reported "skipped", so check_math.mjs was run directly: chat 0 errors KB-wide; --github 0 issues in this run's files, leaving exactly the 96 errors and 67 warnings pre-existing in lectures 1–10
- [x] Commit and push lecture 15 (e0e111b)
- [x] BUILD_LOG.md — lecture 15's record; move this section there; TODO.md keeps only open work

### Findings

- **Pinning.** Commit 0116e139 "add logic 1" landed 2025-11-10 21:30:51 UTC (1:30pm PST), as the lecture began, adding `propositional_logic.py`, its trace, sixteen images and `z3-solver` to `pyproject.toml`. Its README diff adds "Mon Nov 10: learning (lecture 15)" over the `propositional_logic` link (the word "learning" is carried over from lecture 14's heading) and a "Wed Nov 12: learning (lecture 16)" entry for `first_order_logic`, "under construction". The trace's embedded source is byte-identical to the pinned file. `perceive-reason-act-learn.png` has blob 3c8f4ce4 at both f22938e and 0116e139.
- **Post-lecture changes.** af5f29c1 "fix typos" (10:51pm PST the same evening) reworded the interpretation function's two cases, added an `@inspect g h`, reordered the second Ask example's KB to `[Wet, Not(Rain)]`, changed "While there are no changes to KB" to "Until there are no more changes to KB", and glossed the two set definitions. 79ab1572 (2025-11-13, after lecture 16) fixed "Think a logic as a language" and "are are". No traced value changed. The lecturer read the original loop wording aloud at 1:05:43; the wiki states the algorithm correctly and says what was printed.
- **Trace vs. class.** The one number read out, $P(\text{Rain}) = 0.4$ at 54:59 (caption "04"), is 0.1 + 0.3 from the traced joint table. All model sets, Ask answers and Tell responses in the wiki are the traced values. The trace's interpretation of $(\neg A \wedge B) \leftrightarrow C$ short-circuits and never evaluates $B$, while the figure shows $\mathcal{I}(B, w) = 1$; both are recorded.
- **Figures read in the parent.** Sixteen images, read directly: eleven diagrams (syntax/semantics overview, $\mathcal{I}(f, w)$, the evaluation tree, $\mathcal{M}(f)$, entailment/contradiction/contingency set pictures, the 0-to-1 line, the satisfiability decision tree, the modus ponens rule and worked example), two soundness grids, and three stock photographs of water glasses. The tree's values and both soundness grids match the traced model sets exactly. The "half" water glass is filled to roughly four-fifths; the slide file describes what is shown and notes the file name. The satisfiability figure asks about KB ∪ {¬f} first while the printed text describes the KB ∪ {f} call first; both are recorded.
- **Not in the lecture.** $P(\text{Rain} \mid \text{Wet} = 1) = 0.75$ is computed for the wiki page from the lecture's table and says so, as lecture 13's 0.68 was. One sentence on why modus ponens cannot derive Rain → Slippery was drafted for the inference-rules topic page and removed before commit as outside material.
- **Transcript adjudication.** 95 markers identical; word ratios 0.81–1.02, no outliers; number differences are "04" → 0.4 and digits for spoken numbers ("2 to the 100" for the caption's "two to 100"). No LaTeX in the body: the prompt said so explicitly, per the lecture 14 lesson, and the agent complied. The parent removed an [Ed: unclear] the agent had put on "AI society section" (the course's lecture list has lecture 18 "AI & Society"); marked a student question at [38:05] the agent had left unmarked as the lecturer's own (the lecturer later calls it "the previous question" and "your earlier question"); reclassified two "slips" that were correct readings ([10:53] is the slide's four example formulas read aloud; [37:18] states the entailment/contradiction relationship correctly); and recorded two genuine slips kept as spoken ([58:47] "there's no contradiction" for an unsatisfiable KB ∪ {¬f}; [1:08:53] "the syntax" for the set of entailed formulas). Five [Ed: unclear] markers remain. Restored names: John McCarthy appears in lecture 1's slide file; spoken-only terms include SMT solver, NP-complete, monotonic, proof by contradiction and existence proof.
- **Topic pages.** Four new: propositional logic, entailment (with contradiction, contingency and Ask/Tell), satisfiability (model checking, Z3, SAT solvers), inference rules (modus ponens, forward inference, soundness and completeness). Lecture 16 (first-order logic) will extend them. Cross-links added from lectures 1 and 14, probabilistic inference and Bayesian networks.

## Run 2026-10-25 — lecture 16 (Logic II)

Covered lecture 16 end to end: transcript, slide file, figures, edited transcript, wiki page, three topic pages, cross-links, index, `kb.json`, `sources.md`. Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5.

### Transcripts

- [x] 16 Logic II — video x9Mbqu06OVo (raw transcript fetched 2026-10-25: 98 markers, ~9.9k words; slug `16-logic-ii`)
- [x] 16 — edited transcript (Sonnet copy-edit, adjudicated 2026-10-25: 98 markers identical; the parent moved back one sentence carried across the [49:41]/[50:29] boundary (ratio 0.70 before, 0.85 after); ratios then 0.72–1.02, up to 1.15 only in the four paragraphs carrying [Ed: unclear] notes; number inventory clean apart from o1/o2/o3 and cs221 restorations; no `$` in the body; 5 [Ed: unclear] markers; parent removed a guessed `*[Student]*` speaker tag at [1:39] and reorganized the slips list)

### Crawl

- [x] Slide file for 16 (first_order_logic.py pinned at commit c88136d, "add first-order logic", 2025-11-12 1:29pm PST; trace's embedded source byte-identical; two new figures read in the parent)

### Wiki

- [x] wiki/16-logic-ii.md
- [x] Topic pages first-order-logic, propositionalization, unification; lecture 16 sections and cross-links in inference-rules, propositional-logic, entailment, satisfiability, 15-logic-i
- [x] INDEX.md table of contents (update for lecture 16)

### Publish

- [x] kb.json and sources.md for lecture 16
- [x] verify_kb.py clean (all hard checks, after staging the new files), review read: open TODO boxes confirmed open, no images; its math step reported "skipped", so check_math.mjs was run directly: chat 0 errors KB-wide; --github found six errors in this run's pages (four `)$)` closings, two formulas inside italics), all fixed, leaving exactly the 96 errors and 67 warnings pre-existing in lectures 1–10; check_quotes.py: 1 unmatched in this run's text, a notation gloss as on lecture 15's page
- [x] Commit and push lecture 16 (2b9e032)
- [x] BUILD_LOG.md — lecture 16's record; move this section there; TODO.md keeps only open work

### Findings

- **Pinning.** Commit c88136d "add first-order logic" landed 2025-11-12 21:29:06 UTC (1:29pm PST), a minute before the lecture, adding `first_order_logic.py`, its trace and two images (`fol-example.png`, `unique-names-domain-closure.png`). Its README diff only removes "under construction" from the `first_order_logic` entry, which lecture 15's commit had already added under "Wed Nov 12: learning (lecture 16)" (the word "learning" carried over again). The trace's embedded source is byte-identical to the pinned file; `hidden_line_numbers` is empty. `syntax-semantics-rules.png` has blob 447d69f4 at both 0116e139 and c88136d.
- **Slips on screen, fixed the next day (79ab157, 2025-11-13 6:03pm PST).** The definite-clause example's code built `Knows(x, y)` where its comment said `Takes(x, y)`; "there is some course that every student has taken" lacked Course(x) (the lecturer pointed this out at [1:09:59]); the unique-names line read "each constant maps to each object corresponds to at most one constant" (the lecturer corrected it aloud at [45:49]); the model's code had Knows(o2, o3) false while `fol-example.png` draws that arrow (the lecturer noticed at [41:57]). The same commit made missing predicate entries default to false, removed the second `motivating_example()` call, and fixed two lecture 15 typos. Re-reading the 79ab157 trace: every Ask, unify and substitute value is unchanged, and the ∀ example stays false (now at o3 rather than o2). All recorded in the slide file's "Changes after the lecture".
- **Trace vs. class.** Every value in the wiki is the traced one: the three Ask answers (I don't know, I don't know, No, repeated by the second run), Knows(alice, arithmetic) = true, ∀x Knows(x, arithmetic) = false (o1 true, o2 false, o3 never evaluated because `and` short-circuits), the two substitutions, the three unifications (the third None), θ = {x: alice, y: cs221, z: logic} and Knows(alice, logic). The interpretation code's ∃, negation, connective, unary-predicate and function-application branches are never run by the trace; the slide file says so.
- **Figures read in the parent.** Two images, read directly (claude-opus-5-5). `fol-example.png` adds a constant robert for o2 that is not in the code and omits the father function; the lecturer says he added Robert "just for fun". `unique-names-domain-closure.png` uses john and bob while the section's KB uses alice and bob.
- **Not in the lecture.** The lecturer leaves the motivating example's final "No" to the class ("ponder this, maybe offline"); the wiki derives it from the KB (Alice is a person and Phoenix a hot place, so Snowing would make her unhappy, contradicting Happy(alice)) and says the lecture does not spell it out. The complexity example's count is garbled in the captions ("10 time different"); the wiki gives $10^2 = 100$ as what the stated formula yields, not as what was said. The parent removed "Goldbach" from two of its own table rows in the slide file, since the source program never names the conjecture; the name is spoken-only (captions "code box" and "gold blocks conjecture").
- **Transcript adjudication.** The ratio check caught the one real defect: the agent moved "Takes x y and Covers y z, and implies another atomic formula, Knows x z" from [50:29] back into [49:41], completing the sentence across the marker. Marker and number checks passed it. The agent had also tagged "Yes or no or I don't know?" at [1:39] as a student's, from the captions' `>>` marks alone; the tag was removed and the [Ed: unclear] note kept. Its slips list mixed the lecturer's reactions to on-screen errors, caption omissions and one real slip; the parent split them, keeping one slip ([48:54] "propositional logic in general" for first-order logic). With the no-LaTeX and "So the question is" instructions in the prompt it wrote no `$` and marked all five restated student questions.
- **Topic pages.** Three new: first-order logic, propositionalization, substitution and unification. inference-rules gained a "First-Order Modus Ponens" section; propositional-logic a section on what it cannot say compactly; entailment and satisfiability a lecture 16 note each; lecture 15's page links forward. satisfiability now says lecture 16 does not return to what an SMT solver is, as lecture 15 had deferred it.

## Run 2026-10-25 — lecture 17 (Language Models)

Lecture 17 is the course's one lecture delivered as a slide deck rather than an executable Python program: `language_models.pdf` (119 pages, Keynote, by Ken Liu), added to the lecture repo at caa93be on 2025-11-19, two days after the Mon Nov 17 lecture. Parent claude-opus-5-5; deck reading and transcript copy-edit delegated to claude-sonnet-5-5.

### Transcripts

- [x] 17 Language Models — video 3orP3u2-jcg (raw transcript fetched 2026-10-25: 104 markers, ~15k words; slug `17-language-models`)
- [x] 17 — edited transcript (Sonnet copy-edit, adjudicated 2026-10-25: 104 markers identical; ratios 0.76–1.04, no outliers; no `$` in the body; number inventory differs only by false starts removed ("33 uh 36", "65 uh sorry, 650,000", "8 uh 880"), H100 restorations and slide numbers inside [Ed: …] notes; 20 [Ed: unclear] markers; no student questions in the captions; parent removed an outside-knowledge gloss of 桌子 and a guess about which "two more slides" were meant)

### Crawl

- [x] Slide file for 17 (language_models.pdf pinned at caa93be; 119 pages, no printed numbers, slide N = PDF page N; three Sonnet readers; headings checked as the plain sequence 1–119, since `--verify` reads slide 42's "0" as a page number)
- [x] Figure audit of the deck's chart-, table- and screenshot-heavy pages (two Sonnet passes, 600 dpi crops, 18 pages: 13 exact, 5 with chart-description errors on slides 67, 69, 84, 86, 96, all fixed; the parent confirmed the two reversals, 67 and 84, on its own crops)

### Wiki

- [x] wiki/17-language-models.md (draft; revisit after the transcript adjudication)
- [x] Topic pages and cross-links for lecture 17 (new: language-models, scaling-laws, pre-training, post-training, tokenization; lecture 17 links added to tensors, policy-gradients, reinforcement-learning, first-order-logic, hidden-markov-models, bayesian-networks, maximum-likelihood, and lectures 1, 3, 4, 5, 12, 16)
- [x] INDEX.md table of contents (update for lecture 17; coverage sentence, raw/slides note on the one PDF deck, footer)

### Publish

- [x] kb.json and sources.md for lecture 17 (materials.primary now "mixed", with slideDecks and a methodNote; one new caveat)
- [x] verify_kb.py clean (all hard checks), review read: the nine open TODO boxes confirmed open; its math step reported "did not return JSON", so check_math.mjs was run directly: chat 0 errors KB-wide; --github found six errors and one warning in this run's files (two `"$6ND$` openings after a quote mark, one `\,`, `GCD$(p, q)`, currency `$` pairing on two slide-file lines, a `$$` block inside a list item, then math inside link text), all fixed, leaving exactly the 96 errors and 67 warnings pre-existing in lectures 1–10. kb-checkers: check_transcript PASS (0 of 104 paragraphs outside 0.72–1.10); check_links 0 broken; check_quotes found 22 quotations in this run's pages that followed the raw captions rather than the edited transcript, all corrected (the one remaining unmatched quote, in policy-gradients.md, predates this run). AGENTS.md gained one general rule (a lecture may be a PDF deck)
- [x] Commit and push lecture 17 (1523e96)
- [x] BUILD_LOG.md — lecture 17's record; move this section there; TODO.md keeps only open work

### Findings

- **The one slide-deck lecture.** The lecture repo has no program for lecture 17. Commit caa93be ("add language models lecture", Ken Liu, Wed 2025-11-19 00:14 PST) adds `language_models.pdf` (31,473,410 bytes, Keynote, PDF created 08:11 UTC that day) and a README entry "Mon Nov 17: introduction to language models (lecture 17)" whose link reads `first_order_logic` → `…/langauge_models.pdf`; 12be09d a minute later fixed the label but not the misspelt filename, which 404s on the rendered site. The deck was deleted in the 2026 rewrite (a270be9). It is the only version ever committed, two days after the lecture; the transcript follows it slide by slide, so it is taken as what was shown. Slide 1 names Ken Liu; the lecturer refers to Percy in the third person.
- **Numbering.** The deck prints no slide numbers. `slide_number_map.py` reads the "0" of "P(string) = 0" on page 42 as a printed number and reports a phantom mapping, so `--verify` fails on a correct file; headings were checked as the plain sequence 1–119.
- **Readers and audit.** Three claude-sonnet-5-5 agents read pages 1–40, 41–80 and 81–119 in parallel (about 2–3 minutes each), appending in 10-page chunks. Two claude-sonnet-5-5 audit passes over 18 pages (every chart and every table of numbers in the deck, plus the photo and screenshot pages most at risk of invention) found 13 exact and 5 with chart-description errors: slide 67 (the black envelope does not reach $10^1$; the orange fit spans the axis; the parameters panel ends at about 2.3), slide 69 (nine dots, not about ten; the Gopher line a little short of $10^{24}$), slide 84 (12.3% is the share the FastText filter removes, not the band entering it), slide 86 (x-axis not evenly spaced) and slide 96 (one underline). The parent confirmed the two reversals (67, 84) on its own ~1k-token crops. No invented content; every table cell and every number on slides 6, 26, 81, 100 and 101 was exact. The deck's own illustrative numbers are inconsistent (word IDs [1 3 2 5 6 4] do not match slide 22's vocabulary, panicked is 7 on slide 22 and 9 later, slide 26's probabilities sum past 1); the slide file and wiki record this.
- **Transcript adjudication.** 104 markers identical; ratios 0.76–1.04; number inventory differs only by removed false starts ("33 uh 36", "65 uh sorry, 650,000", "8 uh 880"), H100 restorations ("800s", "age 100", "H00") and slide numbers inside [Ed: …] notes; no `$`; 20 [Ed: unclear] markers; no student questions (the captions' `>>` marks were not tagged). The parent removed the agent's claim that 桌子 means both table and desk (outside knowledge; the lecturer glosses it as desk, and the slide reader's "table" gloss was also removed from the slide file) and its guess that "two more slides" meant 118–119 (it was said on slide 115). Spoken vs. printed: "7 billion" for the 70B model (slide 100), "four bytes per word" for 144 TB (which is 36T tokens × 4 bytes), "about three trillion" for 3.71T, "6,000-something km" for 6,371 km.
- **Not discussed in class.** Slide 107 (caching and batching: "Okay, I'll skip that"), slide 116 (agents) and the list on slide 118. While describing preference labels the lecturer said he probably had no slide for them; slide 90's InstructGPT figure shows the ranking step.
- **Topic pages.** Five new: language-models, scaling-laws, pre-training, post-training, tokenization. Cross-links from tensors, policy-gradients, reinforcement-learning, first-order-logic, hidden-markov-models, bayesian-networks, maximum-likelihood and lectures 1, 3, 4, 5, 12, 16. Connections the lecture does not make itself (n-gram counting as lecture 14's count and normalize; Laplace smoothing for the zero counts) are marked as the KB's.

## Run 2026-10-25 — lecture 18 (AI & Society)

Lecture 18 is an executable program again, `society.py`, pinned at 1b5bd22 ("add society", 2025-11-19 1:26pm PST, four minutes before the lecture). Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5.

### Transcripts

- [x] 18 AI & Society — video 071zJXhvNfM (raw transcript fetched 2026-10-25: 93 markers, ~10.6k words; slug `18-ai-and-society`)
- [x] 18 — edited transcript (Sonnet copy-edit, adjudicated 2026-10-25: 93 markers identical; ratios 0.75–0.98 excluding [Ed:] notes, at most 1.10 with them; no `$` in the body; number inventory differs only by model-name restorations and one doubled "0.87 87"; no student questions; parent restored Llama 3.1 70B and *M. Butterfly* from the memorization figure, added inline caption notes to the two edits that reverse the captions, removed an inserted "to" from "450 460,000 tokens", and corrected the header's spoken-only list against the figures)

### Crawl

- [x] Slide file for 18 (society.py pinned at 1b5bd22; trace's embedded source byte-identical; 18 images read in the parent; FMTI tables checked on full-resolution crops)

### Wiki

- [x] wiki/18-ai-and-society.md
- [x] Topic pages and cross-links for lecture 18 (new: societal-impact, ai-inequality, alignment, copyright-and-fair-use, openness-and-transparency; lecture 18 links added to lectures 1, 16, 17, post-training, language-models, reinforcement-learning)
- [x] INDEX.md table of contents (update for lecture 18; coverage sentence, footer)

### Publish

- [x] kb.json and sources.md for lecture 18
- [x] verify_kb.py clean (all hard checks, after staging), review read: the open TODO boxes are this run's commit and move plus the six standing items, all still open; its math step again "did not return JSON", so check_math.mjs --github was run directly: chat 0 errors, GitHub exactly the 96 errors and 67 warnings pre-existing in lectures 1–10, every file this run touched OK. kb-checkers: check_transcript PASS (timestamps identical, 0 of 93 outside 0.72–1.10); check_links 0 broken, 0 bad anchors; check_quotes found 6 spliced or paraphrased quotations in this run's pages, all fixed; the one remaining is the Lehrer lyric quoted with " / " between the program's lines
- [x] Commit and push lecture 18 (8728e00)
- [x] BUILD_LOG.md — lecture 18's record; move this section there; TODO.md keeps only open work

### Findings

- **Pinning.** Commit 1b5bd22 "add society" (Percy Liang) landed 2025-11-19 21:26:49 UTC (1:26pm PST), four minutes before the Wed Nov 19 lecture, adding `society.py`, its trace (283 steps), 16 images in `images/`, three FMTI charts and the ChatGPT logo in `var/files/`, and the arXiv metadata for the linked papers. Its README diff adds "Wed Nov 19: AI & Society (lecture 18)" with the `society` trace. A merge (0e44c15) followed a minute later with lecture 17's deck commits. No later commit touched `society.py` before the 2026 rewrite (a270be9). The trace's embedded source is byte-identical; `hidden_line_numbers` is empty. Two images in the same commit (`levels-of-abstraction.png`, `microsoft-responsible-ai.png`) are referenced by no program at that commit and were not described. The next README commit, "update Rishi" (f700637, 2025-12-01), adds lecture 19 ("Economics of AI", a Google Slides link).
- **A program that computes nothing.** `society.py` is only `text`, `image` and `link` calls, so the slide file is a transcription of the screen plus 18 image descriptions; the trace was used for the rendered titles and authors of the linked papers.
- **Figures read in the parent.** All 18 images were read directly by claude-opus-5-5 from downscaled copies (about 1k tokens each), with no second vision pass. The FMTI subdomain heat map (3348 × 1424) was re-read as two full-resolution crops and all 182 cells reconcile with both printed average margins (27 averages, each within rounding); its column averages differ from the total-score chart's figures because those count indicators. The memorization figure's colour-bar label and axis ends were checked on a 2× crop. The CoastRunners GIF was read as frames 0, 14, 28 and 42 of 56 (score 13,500 at 0:26 and 15,500 at 0:28, laps "–/3"). The Gender Shades "largest gap" column is lighter male minus darker female (33.7 computed vs. 33.8 printed for Face++).
- **Printed vs. figure vs. spoken.** The program's bullet says "Llama 3 70B"; its figure's row and the lecturer (twice, captioned "3.17B") say Llama 3.1 70B. The lecturer says copyright "kind of emerged in" a place the captions give as "the US" right after "the 1700s in England"; the program says England (Statute of Anne), kept as an [Ed: unclear] note. Mistral 7B's spoken "55 out of 100" matches the FMTI total score. The program's typos ("camed", "explicity", "Sychopancy") are marked [sic] in the slide file. Historical and legal figures (Tuskegee "1974", "Lasts for 75 years", "December 2022" for ChatGPT, spoken) are reported as stated, with no outside correction; the parent removed one outside-knowledge gloss of ChatGPT's release date from its own wiki draft.
- **Transcript adjudication.** The Sonnet edit was clean on every script check. The parent's changes: two edits that reverse the captions ("That's my department" → "not my department"; "complete in their coverage" → "incomplete") were kept, following the program, but each now carries an inline note quoting the captions; "llamoth 3.17B" and "llama 3.17b" → Llama 3.1 70B and "and butterfly" → *M. Butterfly*, from the memorization figure the agent had not seen; "450 to 460,000" back to "450, 460,000" with an [Ed: unclear] note (the agent had inserted "to"); and the header's spoken-only list, which named a dozen names printed in the figures (Face++, Claude 3, Mistral 7B, GPT-5, DeepSeek and others). With the memory's prompt items the agent wrote no `$`, moved nothing across a marker (it caught and undid one "window" move itself), and correctly marked no student questions (there are none; "Any ideas?" at [28:51] is rhetorical). "Rishi" (captions "Richishi") is spoken-only; no surname is given.
- **Topic pages.** Five new: societal-impact (why care, dual use, intent × impact, ecosystem view, auditing), ai-inequality, alignment, copyright-and-fair-use, openness-and-transparency. Cross-links from lectures 1 (alignment, societal goals), 16 (forward reference), 17 (slide 118's open problems), post-training, language-models and reinforcement-learning. The Turing-test TODO stays open: lecture 18 does not mention it.

## Run 2026-10-26 — lecture 19 (Economics of AI; catalog title "AI Supply Chains")

Lecture 19 is a guest lecture by Rishi Bommasani given as a Google Slides deck, "CS 221 - Economics of AI" (70 pages, exported as PDF 2026-10-26). The lecture repo has no program or PDF for it: its only commit, f700637 ("update Rishi", 2025-12-01 17:48 PST, after the lecture), adds a README entry "Mon Dec 1: Economics of AI (lecture 19)" linking the Google Slides document. Parent claude-opus-5-5; deck reading and transcript copy-edit delegated to claude-sonnet-5-5.

### Transcripts

- [x] 19 Economics of AI — video lPx5PF1ttkc (raw transcript fetched 2026-10-26: 97 markers, ~14k words; slug `19-economics-of-ai`)
- [x] 19 — edited transcript (Sonnet copy-edit, adjudicated 2026-10-26: 97 markers identical; 0 of 97 paragraphs outside 0.72–1.10; no `$` in the body; number inventory differs only by "100 years" spelled out and slide numbers and markers inside [Ed:] notes; 3 student questions, speakers marked; parent restored Daron Acemoglu, Arvind and Sayash, Dario and David Autor with inline caption notes, marked the inserted [AI] at [1:39], reverted "returns to scale" to the captioned "returns of scale", and removed a guess about where Percy made an earlier remark)

### Crawl

- [x] Slide file for 19 (Google Slides PDF export of 2026-10-26; 70 pages, no printed numbers, slide N = PDF page N; three Sonnet readers, pages 1–24, 25–47, 48–70; `--verify` OK on the 1:1 fallback map)
- [x] Figure audit of the deck's chart-, table- and diagram-heavy pages (two Sonnet passes, 600 dpi crops, 20 pages: 11 exact, 9 corrected; one substantive error on slide 22, whose robots.txt panel reversal the parent confirmed on its own render)

### Wiki

- [x] wiki/19-economics-of-ai.md
- [x] Topic pages and cross-links for lecture 19 (new: ai-supply-chain, general-purpose-technologies, economic-growth; lecture 19 links added to lectures 17 and 18, societal-impact, openness-and-transparency, ai-inequality, pre-training, post-training, copyright-and-fair-use; "the one slide-deck lecture" claims about lecture 17 corrected in INDEX, kb.json, sources.md, and lecture 17's wiki page and slide file)
- [x] INDEX.md table of contents (update for lecture 19; coverage sentence, lecture 17's entry, raw/slides note, footer, four topic entries)

### Publish

- [x] kb.json and sources.md for lecture 19 (slideDecks now 2; one new caveat; AGENTS.md gained one general rule: a deck may be a Google Slides link)
- [x] verify_kb.py clean (all hard checks), review read: the open TODO boxes are this run's commit and move plus lecture 20 and the three standing items, all still open; its math step again "did not return JSON", so check_math.mjs --github was run directly: chat 0 errors, GitHub exactly the 96 errors and 67 warnings pre-existing in lectures 1–10; the only new findings were four notation warnings (plain "9×"/"900×"), fixed. kb-checkers: check_transcript PASS (timestamps identical, 0 of 97 outside 0.72–1.10); check_links found 5 broken pseudo-links on slide 31 ("(link, underlined teal)"), replaced with the URLs from the PDF's link annotations, then 0 broken; check_quotes 131 quotations in this run's pages, 1 unmatched ("Nvidia GPUs in data centers", fixed to the transcript's wording)
- [x] Commit and push lecture 19 (542117c)
- [x] BUILD_LOG.md — lecture 19's record; move this section there; TODO.md keeps only open work

### Findings

- **No program, no file.** The lecture repo's only record of lecture 19 is f700637 ("update Rishi", Percy Liang, Mon 2025-12-01 17:48 PST, after the 1:30pm lecture): a README entry "Mon Dec 1: Economics of AI (lecture 19)" linking a Google Slides document. The course schedule calls the lecture "AI Supply Chains [New]" and links nothing. The deck exported cleanly through `/export/pdf` (70 pages, 4.6 MB, PDF title "CS 221 - Economics of AI", no printed slide numbers; `slide_number_map.py` found none and fell back to 1:1, which `--verify` accepts). A Google Slides document is live, so the export (2026-10-26) may differ from what was shown. The one sign of a change: at [1:03:16] the lecturer says he mislabelled the slide that "should say if AI is a new source of labor", while the export's slide 68 is already titled "If AI injects labor, then?". Slug `19-economics-of-ai`, after the deck and README rather than the catalog title.
- **Readers and audit.** Three claude-sonnet-5-5 readers (pages 1–24, 25–47, 48–70) took 1–2 minutes each, appending in 8-page chunks. Two claude-sonnet-5-5 audits over 20 pages at 600 dpi found 11 exact and 9 with corrections. The one substantive error was slide 22's robots.txt panel: "Full restrictions" had been read as reaching 5–8% by mid-2024 when it reaches roughly 25–30%, and "No Robots.txt" as 25–30% falling to 8–10% when it is about 15% falling to nearly nothing. The parent confirmed it on a 900-px render of the page (about 1k tokens). The other eight were small: Microsoft's start value on slide 2, slide 12's text sides and bracket boundaries, the Applied Materials mark on slide 15, line crossings on slide 18, slide 27's caption ("open, open source"), slide 42's colours, slide 54's line end points and dot count, and slide 3's peak date. No invented content. Every printed value on slides 8, 37, 52 and 55 and all 28 cells of slide 27's table were exact.
- **Transcript adjudication.** The Sonnet edit was clean on every script check: 97 markers, ratios in band, no `$`, 3 student questions, and speakers marked (Percy Liang's introduction, then Rishi Bommasani). The parent's changes were these. It restored the spellings of four spoken-only names the agent had left as captioned with [Ed: unclear] (Daron Acemoglu, Arvind and Sayash, Dario, David Autor), each with an inline note quoting the captions. It bracketed an inserted "[AI]" at [1:39]. It reverted "constant returns to scale" to the captioned "returns of scale". And it removed the agent's guess that Percy's micro/macro remark was made "earlier in the course". "Eliezer" (captions "lesser") is restored from slide 42's book cover; "Martin" Trajtenberg stays as captioned with a note (slide 48 prints "M. Trajtenberg"); "Barts" (the lawsuit) stays unclear.
- **Spoken vs. printed.** "0.5 and 0.9" vs. slide 39's "0.55% and 0.90%"; "about hundred billion dollars" vs. slide 64's \$97B; the \$98 a month described as what people would accept to stop, where slide 64 says WTP; "10 gigawatts of energy" vs. the quotation's "compute"; "roughly 10 months in a year", as captioned; "the largest copyright settlement in history" vs. the article's "one of the largest". The wiki reports both sides.
- **Stale claims fixed.** Lecture 17 had been described as "the course's one slide-deck lecture" in INDEX.md, kb.json (primaryNote and a caveat), sources.md, lecture 17's wiki page and its slide file's front matter. All are now worded so that lecture 19's deck fits.
- **Topic pages.** Three new: ai-supply-chain (dual lens, layers, compute, data with its six acquisition methods and prices, distribution), general-purpose-technologies (Bresnahan and Trajtenberg's three characteristics, the evidence for AI, the J-curve, the Solow paradox), economic-growth (the Richter scale and forecasts, GDP's 2% trend, GDP-B, the production function, three views of AI). Cross-links from lectures 17 and 18, societal-impact, openness-and-transparency, ai-inequality (a new section on inequality between workers), pre-training, post-training and copyright-and-fair-use. The Turing-test TODO stays open: lecture 19 does not mention it.
