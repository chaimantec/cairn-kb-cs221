# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

## Run 2026-10-24 — lecture 12 (Bayesian Networks I)

Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5.

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
- [ ] BUILD_LOG.md — lecture 12's record
- [x] verify_kb.py clean (all hard checks), review read: 15 open TODO boxes confirmed open, no ranking claims, no images; its math step again reported "skipped", so check_math.mjs was run directly: chat 0 errors KB-wide; --github found 6 errors in this run's slide and wiki files, all fixed, leaving the 96 pre-existing lecture 1–10 errors
- [ ] Commit and push lecture 12
- [ ] Move this run's section to BUILD_LOG.md; TODO.md keeps only open work

### Findings

- **Pinning.** Commit f22938e "add bayes" landed 2025-10-29 20:26 UTC (1:26pm PDT), four minutes before the lecture, adding `bayes.py`, its trace, `alarm-bayes.png`, `medical-bayes.png`, `hmm.png` and the cached arXiv metadata for 2502.01236; its README diff adds "Wed Oct 29: bayes i (lecture 12)". The trace's embedded source is byte-identical to the pinned file. The only later pre-rewrite commit to `bayes.py`, ed66b66 (lecture 13), added two `@inspect` annotations to `hidden_markov_model` and nothing else, so the lecture-1–10 convention (last pre-rewrite commit) would have given the same content here; the lecture's own commit is pinned anyway, per the rule.
- **Trace vs. class.** The numbers read out in class match the trace: one accepted sample of ten ("oh, I get one, I guess"), 0.56 for the 1,000-sample alarm estimate (trace 0.5686 = 29/51), 0.26 for the medical estimate (trace 0.2687 = 18/67), and "most likely position 1" for the HMM (trace 37/53). The accepted-sample counts (51, 67, 53) are derived from the traced fractions; only the 10-sample run was stepped into, so only its `total_count` is in the trace.
- **Figures read in the parent.** Three new images, all plain node-and-arrow diagrams (414×305, 565×439, 871×302 px), read directly and checked against the code's factorizations: the einsum `"c, a, c a h, a i -> c a h i"` fixes the medical edges, `p(a | b, e)` the alarm's, `num_steps = 5` the HMM's length. `perceive-reason-act-learn.png` and `game-tree.png` are byte-identical to lecture 10's pinned versions and were not re-described.
- **Transcript adjudication.** 100 markers identical; word ratios 0.73–1.00 with no outliers; the number inventory differs only by garbled-decimal restorations ("05"→0.05, "0 n5"→0.95, "28"→0.28, "027"→0.27, "08 and 2"→0.8 and 0.2, the joint table at 7:05–8:38) and dropped caption duplicates ("0.05 5", "0.26 26", "0.001 001", "x4 x4 5x6"). One real loss: the edit dropped "100" at 56:09 ("like 100… really good confidence"), restored by the parent. Restored terms not in the slide file (Markov decision processes, expectimax, NumPy, NP-hard, heuristic) are spoken-only and listed so in the header. Three [Ed: unclear] markers kept: 38:57 "divide by 09" (trace P(A=1) = 0.0975), 52:13 "6 of the time" (table 0.5832), 1:10:45 "floor line network".
- **Lecturer slips, kept as spoken:** "the output is B and A" for the joint einsum (33:32); "cough" for cold in the medical rejection-sampling result (1:11:33); P(cold | cough) quoted as 0.28 and then 0.27 (exact 0.2778). Spoken examples that differ from the slide: the "prior knowledge" advantage is illustrated with the alarm system in speech and with Mendelian inheritance and laws of physics on the slide; the wiki gives both.
- **Source typos kept with [sic]:** "secretely", "heterogenously"; the source's unclosed parenthesis in the query example and the truncated sentence "we generalize evidence (e.g., A = 1) and query (B) to" are noted in the slide file.
- **Builder slip caught before commit.** The first draft of the wiki page embedded the three figures as `raw/images/` links, but this KB has no committed images (the user never opted in, and `kb.json` has no `materials.images`). They were replaced with prose before anything was published. `verify_kb.py`'s image check would also have flagged the missing files, but the lesson is to check `kb.json` for `materials.images` before writing any figure into a page.
- **Math.** GitHub-only traps hit in this run's own text: `<` in inline math (use `\lt`), and a formula closed directly before `)` or after `"`. All fixed.

## Lectures not yet built

- [ ] 13 Bayesian Networks and Gibbs Sampling — video Dk7Kqqehzjk
- [ ] 14 Bayesian Networks and Learning — video 4d9V6Sxa6gU
- [ ] 15 Logic I — video Q7V13XriJEc
- [ ] 16 Logic II — video x9Mbqu06OVo
- [ ] 17 Language Models — video 3orP3u2-jcg
- [ ] 18 AI & Society — video 071zJXhvNfM
- [ ] 19 AI Supply Chains — video lPx5PF1ttkc
- [ ] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k

## Wiki

- [ ] Re-link the Turing test mention in wiki/01-course-overview.md when a lecture that covers it is built (unlinked 2026-08-27 because no page existed; the Bayesian networks mentions were re-linked 2026-10-24) — blocked until then

## Maintenance

- [ ] Fix pre-existing GitHub math rendering in lectures 1–10 pages: `check_math.mjs --github` on 2026-10-22 reported 96 GitHub-only errors and 67 warnings, none from lecture 11 (mostly `\\` in `cases` blocks → `\cr`, `\,` → `\thinspace`, `^*` → `^{\ast}`); chat rendering is clean
- [ ] Sibling KBs: `cs224n/SEE_ALSO.md` and `cs336/SEE_ALSO.md` describe this KB as "lectures 1–4 of 20" with nothing on search, MDPs or games, stale since lecture 5. Update both to the current coverage — blocked on the user's go-ahead, since they are separate public repos
