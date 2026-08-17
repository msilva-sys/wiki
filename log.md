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

## [2026-08-17] lint | First health check
- 13 pages, 4 raw sources. Reported before changing anything.
- **Critical: live credentials found in [[Proxy Environments]]** — Airtable PAT,
  Firebase service-account private key, LogRocket / Bugsnag / integration keys.
  Present since 2026-08-11; committed to git by the setup on 2026-08-17.
- Also found: 3 uningested sources; malformed frontmatter in [[Zed Cheatsheet]];
  off-schema `type`/`status` on the prep note; `log.md` containing zero
  wikilinks; 3 broken links. No stale pages, no contradictions.
- Corroboration, not a defect: the env file independently confirms LiveScript =
  roteiros, the edit-lock *trava*, and the base-URL-swap migration mechanism.
  It also revealed an unexploited PRD/ADR corpus.

## [2026-08-17] refactor | Redact credentials from the wiki
- [[Proxy Environments]] rewritten: every non-browser-exposed credential replaced
  with `<REDACTED>`. All variable names, comments, base/table/field IDs kept —
  that was the actual knowledge. `NEXT_PUBLIC_*` values retained deliberately.
- Added a hard rule to `CLAUDE.md`: never write a credential into the wiki; stop
  and tell msilva if one is found.
- **Git history was NOT rebuilt** — the operation was blocked by the permission
  layer. The secrets remain recoverable from commits made on 2026-08-17.
  **Rotation of the Airtable PAT and Firebase service-account key is still
  required and is msilva's to perform.**

## [2026-08-17] ingest | Three 2026-08-14 meetings
- Sources: the 1:1, Recap da Semana, and Papo de Projetos. `.docx` originals were
  read by extracting `word/document.xml`; msilva later supplied Recap as `.txt`
  and it was re-ingested in full from that.
- New: `meetings/2026-08-14 1-1 Matheus - Gabrielle.md`,
  `meetings/2026-08-14 Recap da Semana.md`,
  `meetings/2026-08-14 Papo de Projetos.md`, `projects/Pulse.md`,
  `decisions/2026-08-14 Migrate project management from Jira to Linear.md`,
  `decisions/2026-08-14 No mandatory PR review while the proxy is pre-production.md`.
- Updated: [[Airtable Proxy]] (app auth is **active**, not deferred phase 3 —
  Luís reordered the issues; World Cup caused **data loss**; msilva's own framing
  of the project), [[LiveScript]] (data loss; first confirmed over-fetch found;
  roteiros identity now confirmed three ways), [[Agent Flow]] (token cost as a
  design constraint; sharing as the underlying problem; PRD→delivery scope),
  `CLAUDE.md` (Jira → Linear), `index.md`.
- **Date correction**: `raw/2026-06-14-Papo de Projetos…` is misnamed — its
  content header reads `ago. 14, 2026`. Filed as 2026-08-14 per the
  file-contents-over-filename rule. Raw filename left untouched.
- **Correction to an earlier page**: the review-flow decision was initially
  recorded as confirmed in two meetings. Wrong — the Recap describes a *separate*
  change on the Farol project, reviewed by an AI in a fresh session rather than
  by Luís. Corrected on the decision page.
- Resolved: **Orca is real** — cited as the example of a project essential to
  operations. Distinct from an unrelated orchestration tool of the same name.
- Excluded per msilva's standing instruction: contract, working hours, commute,
  previous employer, team socializing, org structure, hiring, role transitions.
- Open: deadlines for both projects (meeting set for end of week beginning
  2026-08-17); does the design doc's phase numbering still mean anything; where
  the first agent lives; locating the PRD/ADR corpus and the documentation Hub.
