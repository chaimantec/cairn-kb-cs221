# How this knowledge base is organized

This repo is the knowledge base for **CS221 — Artificial Intelligence: Principles and Techniques | Autumn 2025 — Percy Liang**. It is read
by Cairn's in-extension AI chat, which fetches files over
raw.githubusercontent.com and follows relative markdown links.

## Layout

| Path                | Contents                                                      |
| ------------------- | ------------------------------------------------------------- |
| `INDEX.md`          | Entry point. Course summary + annotated table of contents.    |
| `wiki/`             | Durable pages: one per lecture, plus cross-lecture topics.    |
| `raw/slides/`       | Full text of every slide, numbered. Slide N = PDF page N.     |
| `raw/transcripts/`  | Lecture transcripts with `[MM:SS]` paragraph marks.           |
| `raw/pdfs/`         | Slides and handouts downloaded from the course website.       |
| `sources.md`        | Crawl inventory: source URL → local file, fetch date.         |
| `kb.json`           | Machine-readable coverage and provenance. Read this to know what this KB does and does not cover, and how far to trust a citation. |
| `SEE_ALSO.md`       | Sibling KBs worth reading for this course, by raw URL.        |
| `TODO.md`           | Build tracker: open work only. Unchecked boxes are outstanding work. |
| `BUILD_LOG.md`      | Finished runs, moved out of `TODO.md`: what each run did and found. Never read by the chat. |

## Conventions

- **INDEX.md is the front door.** The chat reads it first on every conversation.
  Every wiki page must appear there with a one-line description of what it holds.
  An unindexed page is effectively invisible.
- **Relative links only** (`[gradient descent](wiki/gradient-descent.md)`,
  `[slides](raw/slides/03-learning-ii.md)`). Absolute GitHub URLs break when the
  repo is renamed or forked. Slide PDFs are not committed to this repo — link
  slide *transcriptions* under `raw/slides/`, and canonical course-site URLs
  only when pointing outside the repo.
- **Cite everything.** Claims trace back to a transcript timestamp or a slide.
- **Never invent course content.** If the transcript is unclear at some point,
  say so on the page. Do not fill the gap from outside knowledge — the chat
  presents these pages as authoritative material from this course.
- **Prose over fragments.** The chat quotes these pages to learners; bullet
  fragments quote badly.
- **Math in LaTeX**: `$...$` inline, `$$...$$` displayed. Renders both in
  Cairn's chat and on github.com. Never inside a code fence — that shows the
  source instead of the formula. Define each symbol on first use, and follow the
  course's own notation rather than a tidier one, since the learner is comparing
  the page against the lecturer's board.
- **Pin every link into the lecture repo to a commit.** `stanford-cs221/autumn2025-lectures`
  is being rewritten in place for a later offering (from 2026-09-22), so its `main` branch and
  the rendered lecture UI no longer show what was taught in Autumn 2025. For a new lecture, find
  the commit the course made for it on or just before the lecture date (the commit message or
  its README diff names the lecture), read the source, traces and images at that commit, and
  check that the trace's embedded source matches the pinned file. Record the commit in the slide
  file's front matter and in the table in `sources.md`.
- **Transcripts stay verbatim.** Captions spell notation out as speech ("theta
  sub j"); reconstructing it as $\theta_j$ belongs in `wiki/`, not in
  `raw/transcripts/`.

## Rebuilding

Built and updated by the `cairn-kb` skill. To add newly released lectures,
append entries to `TODO.md` and re-run the skill — it only does unchecked work.
