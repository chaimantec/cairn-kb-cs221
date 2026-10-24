# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

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
- [ ] Commit and push lecture 13
- [ ] BUILD_LOG.md — lecture 13's record; move this section there; TODO.md keeps only open work

### Findings (for BUILD_LOG.md)

- **Pinning.** Commit ed66b66 "add Gibbs sampling" landed 2025-11-03 21:27 UTC (1:27pm PST; daylight time had ended the day before), three minutes before the lecture, adding `gibbs_sampling.py`, its trace, `normalize_dict` in `util.py`, and two `@inspect` annotations in `bayes.py`; its README diff adds "Mon Nov 3: gibbs sampling (lecture 13)". The trace's embedded source is byte-identical to the pinned file. No images were added; the two shown are lecture 12's, with identical blob hashes at both commits.
- **The course corrected the file the evening after the lecture** (ce25241, 2025-11-04 7:21pm PST, before lecture 14's commit 6ba9ac8): the Markov-blanket sentence was rewritten to "local conditional probabilities that involve the variable we're sampling... requires knowing all involved variables" (the co-parents point a student raised at 45:07); `markov_prob`'s table indices were swapped from `[child, parent]` to `[parent, child]`, which changes no number because both telephone tables are symmetric; and "C and H are independent given A", false for the medical network and not discussed in class, was deleted. The pinned version is the one taught; the slide file transcribes it and lists the three changes; the wiki flags the false line.
- **Trace vs. class.** Every number read out matches the trace: 0.44 (300-sample rejection, 0.4483 = 13/29), 0.65 (telephone rejection, 32/49), 0.02/0.32/0.34 and "5%" (0.0588) for the hand-worked B update, 0.08/0.32 → 0.2/0.8 for the A update, 0.68 (100-iteration Gibbs), "6" and "33" for the alarm Gibbs runs (0.65, 0.335). Accepted counts (29, 49) are derived from the traced fractions.
- **A number the lecture does not give.** The telephone network's exact $P(A = 1 \mid C = 1)$ is 0.68; the lecturer says only that it can be computed. The wiki pages give it, labelled as computed for the KB from the lecture's own tables, because a learner comparing 0.65 and 0.68 will ask. The alarm's Gibbs failure (0.65, 0.335 vs. 0.513) is not diagnosed in the lecture, and the wiki says so rather than supplying a diagnosis.
- **Source typos kept with [sic]:** "(D = 1)" for the telephone evidence $C = 1$; example 3's first line printing $p(b \mid a)$ for $p(b)$; example 3's conditional printed without division by $P(C = c)$; the general procedure's steps numbered "3." twice; "We're interesting in" in a code comment.
- **Transcript adjudication.** 98 markers identical; word ratios 0.81–1.08, no outliers; the per-paragraph number inventory differs only by decimal restorations fixed by the slide file ("08"→0.8, "02"→0.02, "34"→0.34, "68"→0.68, "33"→0.33 and so on), digits for spoken numbers, and bracketed editorial notes. The agent's slips list had called rounded values ("0.51", "one out of 10,000") and a correct reading of example 4's factors slips; the parent moved the rounded values to their own line, dropped the non-slip, and noted that "0.999" for 0.9999 may be the captions' loss rather than the lecturer's. Restored terms not in either slide file are spoken-only: round-robin, combinatorial, co-parents, primitive, "marrying the parents", pseudocode.

## Lectures not yet built

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
