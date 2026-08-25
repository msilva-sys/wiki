---
type: source
status: active
updated: 2026-08-25
date: 2026-08-25
aliases: [quadro farol, farol whiteboard]
source: "raw/foto quadro farol 2026_08_25.JPG"
tags: [farol, data, gcp, architecture, diagram]
---

# Farol pipeline whiteboard diagram

A photo of a whiteboard sketch, dated the same day as
[[2026-08-25 Farol - Dados]] and almost certainly drawn live during that
meeting — same four sources, same open questions. Unlike that transcript
(mislabeled by Gemini as a single speaker), this is a direct visual
artifact with no attribution problem, so it's trusted over the
transcript wherever the two disagree.

## What it shows

A simple bottom-up pipeline, four boxes feeding one processing step
feeding one storage cylinder:

| Source | Ingestion method |
|---|---|
| OnFly | REST (API) |
| Expresso | REST |
| Uber | SFTP |
| Itaú (written "ITAV" on the board, matches [[2026-08-25 Farol - Dados]]'s Itaú) | **"?" — left unlabeled** |

All four arrows converge into one **box labeled only "?"** — the
in-between processing step nobody could name, matching the transcript's
own line about it: *"a gente tem que passar isso aqui por essa caixinha
aqui que a gente em teoria não sabe o que que é."* That box feeds a
single **cylinder (database icon), also labeled only "?"** — the storage
layer.

## Corrects the meeting page

[[2026-08-25 Farol - Dados]]'s "Facts stated" section, built from the
garbled transcript alone, guessed *"two by REST API (Uber, and one more
likely OnFly/Expresso)"*. **Wrong per this diagram**: it's OnFly and
Expresso on REST, Uber on SFTP, Itaú's method left as an open "?" — not
guessed as "reverse SFTP" the way the transcript implied. Corrected on
that page.

## Confirms two open questions are real, not just unrecorded

Both blank boxes are **drawn as unknowns by whoever made this diagram**,
not gaps in what msilva managed to capture:

- **The processing/ingestion step between raw source and storage has no
  name yet** — a genuinely open architecture question, not documented
  anywhere else in the wiki before this.
- **The storage layer itself is unlabeled here too** — consistent with
  [[Farol]]'s existing open question ("What database? BigQuery is the
  obvious guess... never named"). The diagram doesn't resolve it either;
  it just confirms the team drew it as an explicit unknown rather than
  skipping it.

## Open questions

- What goes in the unlabeled processing box — validation, deduplication,
  format normalization, all of it?
- Is Itaú's connection method genuinely undecided, or just not drawn
  because whoever sketched this didn't know the SFTP detail from
  [[2026-08-25 Farol - Dados]] offhand?
