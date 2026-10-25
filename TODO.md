# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

## Run 2026-10-25 — lecture 15 (Logic I)

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
- [ ] Commit and push lecture 15
- [ ] BUILD_LOG.md — lecture 15's record; move this section there; TODO.md keeps only open work

## Lectures not yet built

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
