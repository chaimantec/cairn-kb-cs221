# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

## Maintenance

- [ ] Fix pre-existing GitHub math rendering in lectures 1–10 pages: `check_math.mjs --github` on 2026-10-22 reported 96 GitHub-only errors and 67 warnings, none from lecture 11 (mostly `\\` in `cases` blocks → `\cr`, `\,` → `\thinspace`, `^*` → `^{\ast}`); chat rendering is clean
- [ ] Sibling KBs: `cs224n/SEE_ALSO.md` and `cs336/SEE_ALSO.md` describe this KB as "lectures 1–4 of 20" with nothing on search, MDPs or games, stale since lecture 5. Update both to the current coverage — blocked on the user's go-ahead, since they are separate public repos

## Run 2026-10-26 (a later run) — lecture 20 (Fireside Chat, Conclusion)

Lecture 20 (Wed Dec 3 2025) is a fireside chat: Ken Liu, the head CA, interviews Percy Liang with questions students submitted, plus live questions. No course material exists for it: the lecture repo's last Autumn 2025 commit is f700637 (lecture 19's README entry), and the course site's schedule links nothing for Dec 3. Transcript-only. Parent claude-opus-5-5; transcript copy-edit delegated to claude-sonnet-5-5.

### Older lines this run completed

- [x] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k (from "Lectures not yet built")
- [x] Re-link the Turing test mention in wiki/01-course-overview.md when a lecture that covers it is built (unlinked 2026-08-27 because no page existed; the Bayesian networks mentions were re-linked 2026-10-24) — done 2026-10-26: lecture 1 now links the new turing-test page (from "Wiki")

### Transcripts

- [x] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k (raw transcript fetched 2026-10-26: 76 markers, ~9.8k words; slug `20-fireside-chat-conclusion`)
- [x] 20 — edited transcript (Sonnet copy-edit, adjudicated 2026-10-26: 76 markers identical; 0 of 76 paragraphs outside 0.72–1.10; no `$` in the body; number inventory differs only by course numbers restored from "2 to1"-style captions, "1999" for the captions' "2099", "256" moved into an [Ed: unclear] note, and "three"/"Ten" spelled out; 7 live student questions marked as repeated; parent moved "And that is not gameable, right?" at [25:40] back into Percy's turn, corrected the header's [55:04] "as captioned" claim and its list of names printed nowhere)

### Crawl

- [x] Materials for 20: none (no lecture-repo commit after f700637 before the 2026 rewrite; the course site lists "Lecture 20: Fireside Chat, Conclusion" with no link)

### Wiki

- [x] wiki/20-fireside-chat-conclusion.md
- [x] Topic page turing-test (lectures 1 and 20) and cross-links for lecture 20 (lecture 20 links added to lecture 1, search-problems, markov-decision-processes, reinforcement-learning, hidden-markov-models, language-models, general-purpose-technologies, economic-growth, societal-impact, openness-and-transparency, copyright-and-fair-use)
- [x] INDEX.md (lecture 20 entry, coverage sentence, footer, raw/slides note, turing-test entry)

### Publish

- [x] kb.json and sources.md for lecture 20 (coverage 20 of 20; the PARTIAL caveat replaced; one new caveat)
- [x] verify_kb.py clean (all hard checks after staging), review read: the open boxes were this run's last two plus the two standing Maintenance items, all still open, and the two older lines above, now ticked and moved here; its math step again "did not return JSON", so check_math.mjs --github was run directly: chat 0 errors, GitHub exactly the 96 errors and 67 warnings pre-existing in lectures 1–10, no warning on any line this run changed (the new pages contain no math). kb-checkers: check_transcript PASS (timestamps identical, 0 of 76 outside 0.72–1.10); check_links 0 broken, 0 bad anchors (4,856 links); check_quotes 8 of 40 quotations in the lecture page and 1 in the copyright page fixed to the edited wording, then 0 unmatched
- [ ] Commit and push lecture 20
- [ ] BUILD_LOG.md — lecture 20's record; move this section there; TODO.md keeps only open work

### Findings

- **No material, by design.** The lecture repo has no record of lecture 20. f700637 ("update Rishi", 2025-12-01) is its last Autumn 2025 commit apart from a 2026-09-22 merge of a contributor's PR (d5752037, "Probs Tensor Values Adjustement") that touches only `policy_gradient.py`, after the course; the 2026 rewrite (a270be9) follows the same day. Lecture 9's pin (ea43de33) predates that merge, so it is unaffected. The course site's schedule lists "Lecture 20: Fireside Chat, Conclusion" for Wed Dec 3, 1:30–2:50pm, with no link. The recording (3,529 s, 76 caption paragraphs) is the chat alone; its "conclusion" is Percy's closing thanks at [58:12]. The moderator works from an on-screen list of submitted questions that is not available. The lecture is therefore transcript-only, the first in this KB, and has no slide file; kb.json's `materials.method` stays `source-text`, with the exception stated in `methodNote`.
- **Speakers.** The captions' `>>` marks were not used as evidence. The moderator is Ken Liu: Percy's "Thanks, Ken, for moderating" at [58:12], with lecture 1's staff slide (Ken Liu, Head CA). The Sonnet edit attributed turns from content and marked the quick joke exchanges ([10:08], [37:15]–[38:49], [47:22]–[48:09]) as "Speaker unclear". The parent moved one turn back: at [25:40] the agent had given "And that is not gameable" to Ken on the strength of a `>>`, but the next words ("right? Because if AI cures cancer …") continue Percy's argument. The agent wrote speaker turns as separate lines inside a paragraph, unlike lecture 19's inline tags; check_transcript and verify_kb accept both.
- **Restorations.** Applied from the parent's glossary in the prompt: hidden Markov model, Markov decision processes, Turing test ("touring", "terrain"), ChatGPT, OpenAI, GPT-3/GPT-5, Cursor, the course numbers ("2 to1", "2:1", "CS21" → 221; "2 to9" → 229; "2 to 4N" → 224N; "36" → 336), Rishi ("Richish", "Richishi"), CURIS ("curious program"), "AI billboards" ("hang bills"), 1999, 2000 ("2099 2000"). Six phrases remain [Ed: unclear]. With the memory's prompt items the agent wrote no `$`, moved nothing across a marker (it restored non-filler words in three paragraphs that first came in at 0.67–0.70), and marked all seven "So the question is" repeats as student questions. Its header claimed a list of names was "printed nowhere in this KB's materials", but Gemini, PyTorch, MIT and the Terminator appear in other lectures' files. The parent replaced the claim.
- **Wiki drafted from the raw captions in parallel.** check_quotes found 8 of 40 quotations no longer matching the edited wording (filler, "can transform", "walking through the door"), all fixed. It found one more in the copyright topic page's new line. Three pre-existing mismatches in reinforcement-learning.md and search-problems.md were left alone.
- **Turing-test TODO closed.** Lecture 20 discusses the Turing test at [24:05]–[26:26] (Percy: no longer a good measure; count scientific discoveries instead), so a `turing-test` topic page now joins lecture 1's account with it, and lecture 1's mention links it. That closes the standing item unlinked since 2026-08-27.
