# Sources — CS221

Every slide deck, handout and reading the course website (<https://stanford-cs221.github.io/autumn2025/>) links to, with its canonical URL. The PDF binaries are deliberately **not** committed to this repo: this knowledge base is read by an agent that navigates markdown, so the URL is what is useful, and committing the decks would add well over 100MB to every clone. Cite these URLs directly.

## Linked, not copied (10)

Papers and readings the course links to but which are hosted elsewhere. They are usually all-rights-reserved and this repo is public, so they are recorded as links rather than committed here. Open them at the source.

| Document | URL | Seen |
| -------- | --- | ---- |
| `2021.acl-long.90.pdf` | <https://aclanthology.org/2021.acl-long.90.pdf> | 2026-07-26 |
| `2024.acl-long.862.pdf` | <https://aclanthology.org/2024.acl-long.862.pdf> | 2026-07-26 |
| `1702.08608.pdf` | <https://arxiv.org/pdf/1702.08608.pdf> | 2026-07-26 |
| `safexp-short.pdf` | <https://cdn.openai.com/safexp-short.pdf> | 2026-07-26 |
| `Konidaris2011a.pdf` | <https://people.cs.umass.edu/~pthomas/papers/Konidaris2011a.pdf> | 2026-07-26 |
| `externalities-and-dual-use-technologies.pdf` | <https://stanford-cs221.github.io/autumn2025-extra/hw3_route/externalities-and-dual-use-technologies.pdf> | 2026-07-26 |
| `ai-alignment-problem.pdf` | <https://stanford-cs221.github.io/spring2025-extra/modules/games/ai-alignment-problem.pdf> | 2026-07-26 |
| `math51book.pdf` | <https://web.stanford.edu/class/math51/stanford/math51book.pdf> | 2026-07-26 |
| `UCAM-CL-TR-209.pdf` | <https://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-209.pdf> | 2026-07-26 |
| `CSD-MenloPrinciplesCORE-20120803_1.pdf` | <https://www.dhs.gov/sites/default/files/publications/CSD-MenloPrinciplesCORE-20120803_1.pdf> | 2026-07-26 |

## Executable lectures

CS221 Autumn 2025 delivers lectures as executable Python programs rather than slide PDFs. The canonical sources are:

| What | URL |
| ---- | --- |
| Lecture source repo (all `.py` files, figure images, published execution traces) | <https://github.com/stanford-cs221/autumn2025-lectures> |
| Rendered lecture UI (pick a lecture by trace name) | <https://stanford-cs221.github.io/autumn2025-lectures/> |

Each `raw/slides/NN-*.md` front matter names the exact source file and trace it was compiled from.

**The repo's `main` branch is no longer the Autumn 2025 version.** From 2026-09-22 the course staff rewrote every lecture file in place for a later offering (commits a270be95 onward), so a link to `main`, and the rendered lecture UI, now shows the rewrite rather than what was taught. Every source link in this KB is therefore pinned to a commit. For lectures 1–10 that is the last commit to touch each file before the rewrite, which is the version those slide files were transcribed from (all built by 2026-09-07). Lecture 11 was built after the rewrite, so it is pinned to cbedcb10, the commit the repo labels "lecture 11", which landed on 2025-10-27 eight minutes before the lecture.

| Lecture | Source file | Pinned commit |
| ------- | ----------- | ------------- |
| 1 | `welcome.py`, `history.py` | 245d8855 |
| 1 | `tensors.py` | 17570e8f |
| 2 | `backpropagation.py` | 4f1dc062 |
| 2 | `linear_regression.py` | 17570e8f |
| 3 | `linear_classification.py` | 4f1dc062 |
| 4 | `deep_learning.py` | c86c4e70 |
| 5 | `search.py` | 6c54c89e |
| 6 | `ucs_astar.py` | 3a36a378 |
| 7 | `mdp.py` | 23c10936 |
| 8 | `reinforcement_learning.py` | 0d981424 |
| 9 | `policy_gradient.py` | ea43de33 |
| 10 | `games.py` | 9a9fc146 |
| 11 | `td_learning.py`, `simultaneous_games.py` (and their traces and images) | cbedcb10 |

A file at a pinned commit is at `https://raw.githubusercontent.com/stanford-cs221/autumn2025-lectures/<commit>/<file>`.


