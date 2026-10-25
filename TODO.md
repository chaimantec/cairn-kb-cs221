# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

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
- [ ] Commit and push lecture 17
- [ ] BUILD_LOG.md — lecture 17's record; move this section there; TODO.md keeps only open work

### Findings

- **The one slide-deck lecture.** The lecture repo has no program for lecture 17. Commit caa93be ("add language models lecture", Ken Liu, Wed 2025-11-19 00:14 PST) adds `language_models.pdf` (31,473,410 bytes, Keynote, PDF created 08:11 UTC that day) and a README entry "Mon Nov 17: introduction to language models (lecture 17)" whose link reads `first_order_logic` → `…/langauge_models.pdf`; 12be09d a minute later fixed the label but not the misspelt filename, which 404s on the rendered site. The deck was deleted in the 2026 rewrite (a270be9). It is the only version ever committed, two days after the lecture; the transcript follows it slide by slide, so it is taken as what was shown. Slide 1 names Ken Liu; the lecturer refers to Percy in the third person.
- **Numbering.** The deck prints no slide numbers. `slide_number_map.py` reads the "0" of "P(string) = 0" on page 42 as a printed number and reports a phantom mapping, so `--verify` fails on a correct file; headings were checked as the plain sequence 1–119.
- **Readers and audit.** Three claude-sonnet-5-5 agents read pages 1–40, 41–80 and 81–119 in parallel (about 2–3 minutes each), appending in 10-page chunks. Two claude-sonnet-5-5 audit passes over 18 pages (every chart and every table of numbers in the deck, plus the photo and screenshot pages most at risk of invention) found 13 exact and 5 with chart-description errors: slide 67 (the black envelope does not reach $10^1$; the orange fit spans the axis; the parameters panel ends at about 2.3), slide 69 (nine dots, not about ten; the Gopher line a little short of $10^{24}$), slide 84 (12.3% is the share the FastText filter removes, not the band entering it), slide 86 (x-axis not evenly spaced) and slide 96 (one underline). The parent confirmed the two reversals (67, 84) on its own ~1k-token crops. No invented content; every table cell and every number on slides 6, 26, 81, 100 and 101 was exact. The deck's own illustrative numbers are inconsistent (word IDs [1 3 2 5 6 4] do not match slide 22's vocabulary, panicked is 7 on slide 22 and 9 later, slide 26's probabilities sum past 1); the slide file and wiki record this.
- **Transcript adjudication.** 104 markers identical; ratios 0.76–1.04; number inventory differs only by removed false starts ("33 uh 36", "65 uh sorry, 650,000", "8 uh 880"), H100 restorations ("800s", "age 100", "H00") and slide numbers inside [Ed: …] notes; no `$`; 20 [Ed: unclear] markers; no student questions (the captions' `>>` marks were not tagged). The parent removed the agent's claim that 桌子 means both table and desk (outside knowledge; the lecturer glosses it as desk, and the slide reader's "table" gloss was also removed from the slide file) and its guess that "two more slides" meant 118–119 (it was said on slide 115). Spoken vs. printed: "7 billion" for the 70B model (slide 100), "four bytes per word" for 144 TB (which is 36T tokens × 4 bytes), "about three trillion" for 3.71T, "6,000-something km" for 6,371 km.
- **Not discussed in class.** Slide 107 (caching and batching: "Okay, I'll skip that"), slide 116 (agents) and the list on slide 118. While describing preference labels the lecturer said he probably had no slide for them; slide 90's InstructGPT figure shows the ranking step.
- **Topic pages.** Five new: language-models, scaling-laws, pre-training, post-training, tokenization. Cross-links from tensors, policy-gradients, reinforcement-learning, first-order-logic, hidden-markov-models, bayesian-networks, maximum-likelihood and lectures 1, 3, 4, 5, 12, 16. Connections the lecture does not make itself (n-gram counting as lecture 14's count and normalize; Laplace smoothing for the zero counts) are marked as the KB's.

## Lectures not yet built

- [ ] 18 AI & Society — video 071zJXhvNfM
- [ ] 19 AI Supply Chains — video lPx5PF1ttkc
- [ ] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k

## Wiki

- [ ] Re-link the Turing test mention in wiki/01-course-overview.md when a lecture that covers it is built (unlinked 2026-08-27 because no page existed; the Bayesian networks mentions were re-linked 2026-10-24) — blocked until then

## Maintenance

- [ ] Fix pre-existing GitHub math rendering in lectures 1–10 pages: `check_math.mjs --github` on 2026-10-22 reported 96 GitHub-only errors and 67 warnings, none from lecture 11 (mostly `\\` in `cases` blocks → `\cr`, `\,` → `\thinspace`, `^*` → `^{\ast}`); chat rendering is clean
- [ ] Sibling KBs: `cs224n/SEE_ALSO.md` and `cs336/SEE_ALSO.md` describe this KB as "lectures 1–4 of 20" with nothing on search, MDPs or games, stale since lecture 5. Update both to the current coverage — blocked on the user's go-ahead, since they are separate public repos
