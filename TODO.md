# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

## Run 2026-10-24 — lecture 14 (Bayesian Networks and Learning)

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
- [ ] Commit and push lecture 14
- [ ] BUILD_LOG.md — lecture 14's record; move this section there; TODO.md keeps only open work

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

## Lectures not yet built

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
