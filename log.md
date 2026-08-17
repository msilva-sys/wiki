---
type: log
updated: 2026-08-17
---

# Log

Append-only. Newest at the bottom. Never rewrite an entry.
Prefixes: `ingest` | `query` | `synthesis` | `lint` | `refactor` | `decision`.

## [2026-08-17] refactor | LLM-wiki setup
- Initialized git in the vault; snapshot commit taken before any changes.
- Added `CLAUDE.md` (schema), `index.md`, `log.md`.
- Created `projects/ systems/ concepts/ people/ decisions/ sources/ meetings/ syntheses/`.
- Filed 5 pre-existing notes into the new structure; renamed two for readability
  (`proxy project` → `Airtable Proxy`, `envs prxy` → `Proxy Environments`).
- `raw/` designated the immutable source layer; currently empty.
- Open: no sources ingested yet; concepts/, people/, decisions/ are stubs.

## [2026-08-17] refactor | Schema: meeting-transcript ingest variant
- Added a transcript-specific `ingest` flow to `CLAUDE.md`: extract decisions /
  action items / open questions rather than summarize; attribute claims to the
  speaker; separate decided from discussed; judgment on what propagates.

## [2026-08-17] ingest | Onboarding Técnico - Matheus (2026-08-10)
- Source: `raw/2026-08-10 Onboarding Técnico - Matheus - Anotações do Gemini.txt`
  (renamed at msilva's direction to date-first; time and `(1)` suffix dropped).
- Machine transcription, low confidence — Gemini attributes all speech to
  Gabrielle Ferreira, including other speakers. Noted on the meeting page.
- New: `meetings/2026-08-10 Onboarding Técnico - Matheus.md`,
  `projects/Agent Flow.md`, `systems/LiveScript.md`,
  `concepts/Airtable Rate Limits.md`,
  `decisions/2026-08-10 Onboarding runs proxy and agent flow in parallel.md`.
- Updated: `projects/Airtable Proxy.md` — added a "Why this project exists"
  section (World Cup trigger, LiveScript *travas* as product debt, base-URL-swap
  migration, merged initiative) and flagged an unresolved tension: Gabrielle
  describes proxy-side 429 retry, but the page records v1 as
  observability-only with no intervention.
- Updated: `index.md`, `CLAUDE.md` (raw/ rename rule; transcripts satisfy the
  `sources/` lint check via `meetings/`).
- Confirmed by msilva: **LiveScript and the "roteiros" app are the same system** —
  merged into one page with both names as aliases.
- Per msilva's instruction, org-related material in the transcript (vendor
  evaluation, an inter-colleague disagreement, a team departure) was **left in
  the raw file and not filed**. No `people/` pages created this ingest;
  speaker attribution is inline on the meeting page instead.
- Open after this ingest: does proxy-side retry belong to v1? Is "Orca" a real
  system? Which agent gets built first and where does it live?

## [2026-08-17] refactor | Schema: derive dates automatically
- Added a `Dates` section to `CLAUDE.md` at msilva's request: never ask for a
  date that can be read.
- Precedence: file contents → filename → filesystem timestamps (last resort,
  must be flagged unverified). Evidence: the 2026-08-10 transcript reports
  `birth=2026-08-17`, i.e. its download date, a week after the meeting.
- Recorded PT month abbreviations and day-first `DD/MM/YYYY` parsing.
- Clarified `date:` (event) vs `updated:` (last touched) and which one leads a
  filename.
