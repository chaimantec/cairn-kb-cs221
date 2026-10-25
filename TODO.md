# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

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
- [ ] Commit and push lecture 18
- [ ] BUILD_LOG.md — lecture 18's record; move this section there; TODO.md keeps only open work

### Findings

- **Pinning.** Commit 1b5bd22 "add society" (Percy Liang) landed 2025-11-19 21:26:49 UTC (1:26pm PST), four minutes before the Wed Nov 19 lecture, adding `society.py`, its trace (283 steps), 16 images in `images/`, three FMTI charts and the ChatGPT logo in `var/files/`, and the arXiv metadata for the linked papers. Its README diff adds "Wed Nov 19: AI & Society (lecture 18)" with the `society` trace. A merge (0e44c15) followed a minute later with lecture 17's deck commits. No later commit touched `society.py` before the 2026 rewrite (a270be9). The trace's embedded source is byte-identical; `hidden_line_numbers` is empty. Two images in the same commit (`levels-of-abstraction.png`, `microsoft-responsible-ai.png`) are referenced by no program at that commit and were not described. The next README commit, "update Rishi" (f700637, 2025-12-01), adds lecture 19 ("Economics of AI", a Google Slides link).
- **A program that computes nothing.** `society.py` is only `text`, `image` and `link` calls, so the slide file is a transcription of the screen plus 18 image descriptions; the trace was used for the rendered titles and authors of the linked papers.
- **Figures read in the parent.** All 18 images were read directly by claude-opus-5-5 from downscaled copies (about 1k tokens each), with no second vision pass. The FMTI subdomain heat map (3348 × 1424) was re-read as two full-resolution crops and all 182 cells reconcile with both printed average margins (27 averages, each within rounding); its column averages differ from the total-score chart's figures because those count indicators. The memorization figure's colour-bar label and axis ends were checked on a 2× crop. The CoastRunners GIF was read as frames 0, 14, 28 and 42 of 56 (score 13,500 at 0:26 and 15,500 at 0:28, laps "–/3"). The Gender Shades "largest gap" column is lighter male minus darker female (33.7 computed vs. 33.8 printed for Face++).
- **Printed vs. figure vs. spoken.** The program's bullet says "Llama 3 70B"; its figure's row and the lecturer (twice, captioned "3.17B") say Llama 3.1 70B. The lecturer says copyright "kind of emerged in" a place the captions give as "the US" right after "the 1700s in England"; the program says England (Statute of Anne), kept as an [Ed: unclear] note. Mistral 7B's spoken "55 out of 100" matches the FMTI total score. The program's typos ("camed", "explicity", "Sychopancy") are marked [sic] in the slide file. Historical and legal figures (Tuskegee "1974", "Lasts for 75 years", "December 2022" for ChatGPT, spoken) are reported as stated, with no outside correction; the parent removed one outside-knowledge gloss of ChatGPT's release date from its own wiki draft.
- **Transcript adjudication.** The Sonnet edit was clean on every script check. The parent's changes: two edits that reverse the captions ("That's my department" → "not my department"; "complete in their coverage" → "incomplete") were kept, following the program, but each now carries an inline note quoting the captions; "llamoth 3.17B" and "llama 3.17b" → Llama 3.1 70B and "and butterfly" → *M. Butterfly*, from the memorization figure the agent had not seen; "450 to 460,000" back to "450, 460,000" with an [Ed: unclear] note (the agent had inserted "to"); and the header's spoken-only list, which named a dozen names printed in the figures (Face++, Claude 3, Mistral 7B, GPT-5, DeepSeek and others). With the memory's prompt items the agent wrote no `$`, moved nothing across a marker (it caught and undid one "window" move itself), and correctly marked no student questions (there are none; "Any ideas?" at [28:51] is rhetorical). "Rishi" (captions "Richishi") is spoken-only; no surname is given.
- **Topic pages.** Five new: societal-impact (why care, dual use, intent × impact, ecosystem view, auditing), ai-inequality, alignment, copyright-and-fair-use, openness-and-transparency. Cross-links from lectures 1 (alignment, societal goals), 16 (forward reference), 17 (slide 118's open problems), post-training, language-models and reinforcement-learning. The Turing-test TODO stays open: lecture 18 does not mention it.

## Lectures not yet built

- [ ] 19 AI Supply Chains — video lPx5PF1ttkc
- [ ] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k

## Wiki

- [ ] Re-link the Turing test mention in wiki/01-course-overview.md when a lecture that covers it is built (unlinked 2026-08-27 because no page existed; the Bayesian networks mentions were re-linked 2026-10-24) — blocked until then

## Maintenance

- [ ] Fix pre-existing GitHub math rendering in lectures 1–10 pages: `check_math.mjs --github` on 2026-10-22 reported 96 GitHub-only errors and 67 warnings, none from lecture 11 (mostly `\\` in `cases` blocks → `\cr`, `\,` → `\thinspace`, `^*` → `^{\ast}`); chat rendering is clean
- [ ] Sibling KBs: `cs224n/SEE_ALSO.md` and `cs336/SEE_ALSO.md` describe this KB as "lectures 1–4 of 20" with nothing on search, MDPs or games, stale since lecture 5. Update both to the current coverage — blocked on the user's go-ahead, since they are separate public repos
