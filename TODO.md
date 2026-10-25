# KB build — CS221 (Artificial Intelligence: Principles and Techniques, Stanford)

Open work only. When a run is committed and pushed, its section moves verbatim to `BUILD_LOG.md`.

## Lectures not yet built

- [ ] 17 Language Models — video 3orP3u2-jcg
- [ ] 18 AI & Society — video 071zJXhvNfM
- [ ] 19 AI Supply Chains — video lPx5PF1ttkc
- [ ] 20 Fireside Chat, Conclusion — video 5u5I5jvWR5k

## Wiki

- [ ] Re-link the Turing test mention in wiki/01-course-overview.md when a lecture that covers it is built (unlinked 2026-08-27 because no page existed; the Bayesian networks mentions were re-linked 2026-10-24) — blocked until then

## Maintenance

- [ ] Fix pre-existing GitHub math rendering in lectures 1–10 pages: `check_math.mjs --github` on 2026-10-22 reported 96 GitHub-only errors and 67 warnings, none from lecture 11 (mostly `\\` in `cases` blocks → `\cr`, `\,` → `\thinspace`, `^*` → `^{\ast}`); chat rendering is clean
- [ ] Sibling KBs: `cs224n/SEE_ALSO.md` and `cs336/SEE_ALSO.md` describe this KB as "lectures 1–4 of 20" with nothing on search, MDPs or games, stale since lecture 5. Update both to the current coverage — blocked on the user's go-ahead, since they are separate public repos

## Run 2026-10-25 — lecture 16 (Logic II)

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
- [ ] Commit and push lecture 16
- [ ] BUILD_LOG.md — lecture 16's record; move this section there; TODO.md keeps only open work

### Findings

- **Pinning.** Commit c88136d "add first-order logic" landed 2025-11-12 21:29:06 UTC (1:29pm PST), a minute before the lecture, adding `first_order_logic.py`, its trace and two images (`fol-example.png`, `unique-names-domain-closure.png`). Its README diff only removes "under construction" from the `first_order_logic` entry, which lecture 15's commit had already added under "Wed Nov 12: learning (lecture 16)" (the word "learning" carried over again). The trace's embedded source is byte-identical to the pinned file; `hidden_line_numbers` is empty. `syntax-semantics-rules.png` has blob 447d69f4 at both 0116e139 and c88136d.
- **Slips on screen, fixed the next day (79ab157, 2025-11-13 6:03pm PST).** The definite-clause example's code built `Knows(x, y)` where its comment said `Takes(x, y)`; "there is some course that every student has taken" lacked Course(x) (the lecturer pointed this out at [1:09:59]); the unique-names line read "each constant maps to each object corresponds to at most one constant" (the lecturer corrected it aloud at [45:49]); the model's code had Knows(o2, o3) false while `fol-example.png` draws that arrow (the lecturer noticed at [41:57]). The same commit made missing predicate entries default to false, removed the second `motivating_example()` call, and fixed two lecture 15 typos. Re-reading the 79ab157 trace: every Ask, unify and substitute value is unchanged, and the ∀ example stays false (now at o3 rather than o2). All recorded in the slide file's "Changes after the lecture".
- **Trace vs. class.** Every value in the wiki is the traced one: the three Ask answers (I don't know, I don't know, No, repeated by the second run), Knows(alice, arithmetic) = true, ∀x Knows(x, arithmetic) = false (o1 true, o2 false, o3 never evaluated because `and` short-circuits), the two substitutions, the three unifications (the third None), θ = {x: alice, y: cs221, z: logic} and Knows(alice, logic). The interpretation code's ∃, negation, connective, unary-predicate and function-application branches are never run by the trace; the slide file says so.
- **Figures read in the parent.** Two images, read directly (claude-opus-5-5). `fol-example.png` adds a constant robert for o2 that is not in the code and omits the father function; the lecturer says he added Robert "just for fun". `unique-names-domain-closure.png` uses john and bob while the section's KB uses alice and bob.
- **Not in the lecture.** The lecturer leaves the motivating example's final "No" to the class ("ponder this, maybe offline"); the wiki derives it from the KB (Alice is a person and Phoenix a hot place, so Snowing would make her unhappy, contradicting Happy(alice)) and says the lecture does not spell it out. The complexity example's count is garbled in the captions ("10 time different"); the wiki gives $10^2 = 100$ as what the stated formula yields, not as what was said. The parent removed "Goldbach" from two of its own table rows in the slide file, since the source program never names the conjecture; the name is spoken-only (captions "code box" and "gold blocks conjecture").
- **Transcript adjudication.** The ratio check caught the one real defect: the agent moved "Takes x y and Covers y z, and implies another atomic formula, Knows x z" from [50:29] back into [49:41], completing the sentence across the marker. Marker and number checks passed it. The agent had also tagged "Yes or no or I don't know?" at [1:39] as a student's, from the captions' `>>` marks alone; the tag was removed and the [Ed: unclear] note kept. Its slips list mixed the lecturer's reactions to on-screen errors, caption omissions and one real slip; the parent split them, keeping one slip ([48:54] "propositional logic in general" for first-order logic). With the no-LaTeX and "So the question is" instructions in the prompt it wrote no `$` and marked all five restated student questions.
- **Topic pages.** Three new: first-order logic, propositionalization, substitution and unification. inference-rules gained a "First-Order Modus Ponens" section; propositional-logic a section on what it cannot say compactly; entailment and satisfiability a lecture 16 note each; lecture 15's page links forward. satisfiability now says lecture 16 does not return to what an SMT solver is, as lecture 15 had deferred it.
