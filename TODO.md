# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

## Lectures not yet built

- [ ] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k

## Wiki

- [ ] Re-link the Turing test mention in wiki/01-course-overview.md when a lecture that covers it is built (unlinked 2026-08-27 because no page existed; the Bayesian networks mentions were re-linked 2026-10-24) — blocked until then

## Maintenance

- [ ] Fix pre-existing GitHub math rendering in lectures 1–10 pages: `check_math.mjs --github` on 2026-10-22 reported 96 GitHub-only errors and 67 warnings, none from lecture 11 (mostly `\\` in `cases` blocks → `\cr`, `\,` → `\thinspace`, `^*` → `^{\ast}`); chat rendering is clean
- [ ] Sibling KBs: `cs224n/SEE_ALSO.md` and `cs336/SEE_ALSO.md` describe this KB as "lectures 1–4 of 20" with nothing on search, MDPs or games, stale since lecture 5. Update both to the current coverage — blocked on the user's go-ahead, since they are separate public repos

## Run 2026-10-26 (a later run) — lecture 20 (Fireside Chat, Conclusion)

Lecture 20 (Wed Dec 3 2025) is a fireside chat: Ken Liu, the head CA, interviews Percy Liang with questions students submitted, plus live questions. No course material exists for it: the lecture repo's last Autumn 2025 commit is f700637 (lecture 19's README entry), and the course site's schedule links nothing for Dec 3. Transcript-only. Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5.

### Transcripts

- [x] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k (raw transcript fetched 2026-10-26: 76 markers, ~9.8k words; slug `20-fireside-chat-conclusion`)
- [ ] 20 — edited transcript (Sonnet copy-edit, adjudicated)

### Crawl

- [x] Materials for 20: none (no lecture-repo commit after f700637 before the 2026 rewrite; the course site lists "Lecture 20: Fireside Chat, Conclusion" with no link)

### Wiki

- [ ] wiki/20-fireside-chat-conclusion.md
- [ ] Topic page turing-test (lectures 1 and 20) and cross-links for lecture 20
- [ ] INDEX.md (lecture 20 entry, coverage sentence, footer)

### Publish

- [ ] kb.json and sources.md for lecture 20
- [ ] verify_kb.py clean, review read; check_math.mjs --github; kb-checkers check_transcript, check_links, check_quotes
- [ ] Commit and push lecture 20
- [ ] BUILD_LOG.md — lecture 20's record; move this section there; TODO.md keeps only open work
