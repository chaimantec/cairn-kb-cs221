# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

## Lectures not yet built

- [ ] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k

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
- [x] Commit and push lecture 19
- [ ] BUILD_LOG.md — lecture 19's record; move this section there; TODO.md keeps only open work

## Wiki

- [ ] Re-link the Turing test mention in wiki/01-course-overview.md when a lecture that covers it is built (unlinked 2026-08-27 because no page existed; the Bayesian networks mentions were re-linked 2026-10-24) — blocked until then

## Maintenance

- [ ] Fix pre-existing GitHub math rendering in lectures 1–10 pages: `check_math.mjs --github` on 2026-10-22 reported 96 GitHub-only errors and 67 warnings, none from lecture 11 (mostly `\\` in `cases` blocks → `\cr`, `\,` → `\thinspace`, `^*` → `^{\ast}`); chat rendering is clean
- [ ] Sibling KBs: `cs224n/SEE_ALSO.md` and `cs336/SEE_ALSO.md` describe this KB as "lectures 1–4 of 20" with nothing on search, MDPs or games, stale since lecture 5. Update both to the current coverage — blocked on the user's go-ahead, since they are separate public repos

### Findings

- **No program, no file.** The lecture repo's only record of lecture 19 is f700637 ("update Rishi", Percy Liang, Mon 2025-12-01 17:48 PST, after the 1:30pm lecture): a README entry "Mon Dec 1: Economics of AI (lecture 19)" linking a Google Slides document. The course schedule calls the lecture "AI Supply Chains [New]" and links nothing. The deck exported cleanly through `/export/pdf` (70 pages, 4.6 MB, PDF title "CS 221 - Economics of AI", no printed slide numbers; `slide_number_map.py` found none and fell back to 1:1, which `--verify` accepts). A Google Slides document is live, so the export (2026-10-26) may differ from what was shown. The one sign of a change: at [1:03:16] the lecturer says he mislabelled the slide that "should say if AI is a new source of labor", while the export's slide 68 is already titled "If AI injects labor, then?". Slug `19-economics-of-ai`, after the deck and README rather than the catalog title.
- **Readers and audit.** Three claude-sonnet-5-5 readers (pages 1–24, 25–47, 48–70) took 1–2 minutes each, appending in 8-page chunks. Two claude-sonnet-5-5 audits over 20 pages at 600 dpi found 11 exact and 9 with corrections. The one substantive error was slide 22's robots.txt panel: "Full restrictions" had been read as reaching 5–8% by mid-2024 when it reaches roughly 25–30%, and "No Robots.txt" as 25–30% falling to 8–10% when it is about 15% falling to nearly nothing. The parent confirmed it on a 900-px render of the page (about 1k tokens). The other eight were small: Microsoft's start value on slide 2, slide 12's text sides and bracket boundaries, the Applied Materials mark on slide 15, line crossings on slide 18, slide 27's caption ("open, open source"), slide 42's colours, slide 54's line end points and dot count, and slide 3's peak date. No invented content. Every printed value on slides 8, 37, 52 and 55 and all 28 cells of slide 27's table were exact.
- **Transcript adjudication.** The Sonnet edit was clean on every script check: 97 markers, ratios in band, no `$`, 3 student questions, and speakers marked (Percy Liang's introduction, then Rishi Bommasani). The parent's changes were these. It restored the spellings of four spoken-only names the agent had left as captioned with [Ed: unclear] (Daron Acemoglu, Arvind and Sayash, Dario, David Autor), each with an inline note quoting the captions. It bracketed an inserted "[AI]" at [1:39]. It reverted "constant returns to scale" to the captioned "returns of scale". And it removed the agent's guess that Percy's micro/macro remark was made "earlier in the course". "Eliezer" (captions "lesser") is restored from slide 42's book cover; "Martin" Trajtenberg stays as captioned with a note (slide 48 prints "M. Trajtenberg"); "Barts" (the lawsuit) stays unclear.
- **Spoken vs. printed.** "0.5 and 0.9" vs. slide 39's "0.55% and 0.90%"; "about hundred billion dollars" vs. slide 64's \$97B; the \$98 a month described as what people would accept to stop, where slide 64 says WTP; "10 gigawatts of energy" vs. the quotation's "compute"; "roughly 10 months in a year", as captioned; "the largest copyright settlement in history" vs. the article's "one of the largest". The wiki reports both sides.
- **Stale claims fixed.** Lecture 17 had been described as "the course's one slide-deck lecture" in INDEX.md, kb.json (primaryNote and a caveat), sources.md, lecture 17's wiki page and its slide file's front matter. All are now worded so that lecture 19's deck fits.
- **Topic pages.** Three new: ai-supply-chain (dual lens, layers, compute, data with its six acquisition methods and prices, distribution), general-purpose-technologies (Bresnahan and Trajtenberg's three characteristics, the evidence for AI, the J-curve, the Solow paradox), economic-growth (the Richter scale and forecasts, GDP's 2% trend, GDP-B, the production function, three views of AI). Cross-links from lectures 17 and 18, societal-impact, openness-and-transparency, ai-inequality (a new section on inequality between workers), pre-training, post-training and copyright-and-fair-use. The Turing-test TODO stays open: lecture 19 does not mention it.
