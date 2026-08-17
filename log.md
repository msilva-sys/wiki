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

## [2026-08-17] ingest | Three 2026-08-14 meetings (first pass)
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

## [2026-08-17] lint | Second health check
- 19 pages, 4 sources, 7 commits.
- **Credentials still present in git history** — unchanged; the purge remains
  blocked and rotation is still outstanding.
- Found: [[2026-08-14 Papo de Projetos]] had been built from Gemini's summary
  only (32 of 309 lines read); stale `.docx` source path on
  [[2026-08-14 1-1 Matheus - Gabrielle]] after msilva re-supplied it as `.txt`;
  the "observability-only" framing now contradicted twice over; unfixed
  frontmatter items carried from the first lint.
- Clean: raw coverage complete, every page indexed, no stale pages, working tree
  free of secrets. Hubs formed — [[Airtable Proxy]] 14 inbound links,
  [[LiveScript]] 12, [[Agent Flow]] 11.

## [2026-08-17] ingest | Papo de Projetos, re-ingested in full
- Rebuilt [[2026-08-14 Papo de Projetos]] from the complete transcript. Better
  source than the others: Gemini labelled multiple speakers correctly.
- **Orca substantially upgraded**: a machine-learning system, in production,
  whose automation is valued at ~10 headcount and which cannot go down. Now the
  stronger candidate for the first monitoring agent in [[Agent Flow]].
- **`Brain` repository discovered** — a company-wide Markdown-in-GitHub knowledge
  base rendered into a Hub, with per-project architecture/decisions/README pages.
  Direct precedent for this vault and overlaps the proposed institutional-memory
  agent. msilva has write access and was asked to correct his own entries.
- Also recorded: the team's five own products (CRM, Orca, LiveScript, Taxonomia,
  Pulse); a homologation flow being built for large projects; project ownership
  resting with area managers; Luís is part-time (afternoons); the matriz de
  eventos — the source of the `AIRTABLE_MATRIZ_*` IDs in [[Proxy Environments]] —
  was built by a departed colleague and maintained by Arthur Tavares, who is
  changing area.
- Verified the re-supplied 1:1 `.txt` against the earlier `.docx` extraction:
  same content, same endpoint. **No re-ingest needed** — only the source path was
  corrected.

## [2026-08-17] refactor | Lint fixes
- [[Zed Cheatsheet]] frontmatter repaired — the delimiter and fields had
  collapsed onto one line, so nothing parsed.
- Meeting prep note given an `updated:` field; `meeting-prep`, `reference`, and
  `draft` added to the schema's enumerations rather than forcing the page to fit.
- `CLAUDE.md` now exempts itself, `index.md`, and `log.md` from frontmatter and
  orphan checks, so lint stops re-reporting them.
- Citation example de-linked so it no longer registers as a broken link.
## [2026-08-17] ingest | 1:1 re-read from the .txt source
- Read the re-supplied `.txt` directly rather than relying on the earlier
  comparison against the `.docx` extraction. **The comparison had been too
  shallow** — it matched line counts, byte counts and the final line, which
  established the transcript bodies were the same but missed detail.
- **The `.txt` is transcript-only**: it drops Gemini's summary block that the
  `.docx` carried. Every existing claim on the page is supported by the
  transcript body, so nothing was invalidated, but the `.txt` is the thinner
  source of the two.
- **Two additions the first pass missed**, both on [[Agent Flow]]: the design
  approach is genuinely undecided between a deterministic flow and simply
  exposing tools to agents, and **Luís suggested graph-based approaches**
  (transcription garbled — flagged unverified). Also recorded msilva's prior
  related work and Gabrielle's ownership framing.
- Pinned Gabrielle's leave to the week of 2026-08-24 by cross-referencing
  [[2026-08-14 Papo de Projetos]].
- Lesson recorded: verifying a file by metadata is not the same as reading it.

## [2026-08-17] ingest | Fluxo Agêntico diagram (14 agents)
- Source: `raw/fluxo_agentico_ajustado (2).html` — an SVG architecture diagram,
  the design artifact behind [[Agent Flow]]. First non-transcript source.
- New: `sources/Fluxo Agêntico diagram.md` with the full 14-agent table and flow.
- **Three corrections to [[Agent Flow]]**, which had been built from the verbal
  description only: **A6 Curator is the architectural centre**, not one of six
  transversal agents; **A5 Watcher is not transversal** and feeds back directly
  into A3 Executor; **A13 Deduplication blocks**, it doesn't merely advise.
- Noted `Bug (sistema)` as a machine-generated entry channel — the likely
  integration point between the [[Airtable Proxy]] and [[Agent Flow]].
- The diagram is a **revision**: stale HTML comments preserve an older numbering
  (e.g. `A10: Project Orchestrator` now renders as A8), so agents were renumbered
  at least once. Rendered labels treated as authoritative.
- Date unresolved — no internal or filename date; filesystem mtime is the
  download. Flagged unverified per the Dates rule.
- **Schema fix**: the `sources/Source: <Title>.md` convention was unusable — a
  colon is an illegal filename character on Windows. Dropped the prefix.
- Raised: the `Brain` repository overlaps A6 Curator, i.e. the centrepiece of the
  architecture rather than a peripheral component.

## [2026-08-17] synthesis | Agent Flow research agenda
- msilva stated the project is at the **research step**. Recorded as
  `phase: research` on [[Agent Flow]].
- New: `syntheses/What should the Agent Flow research phase study.md`, derived
  from existing pages only — no external research performed yet.
- **Names a sequencing problem not previously identified**: A5 Watcher, the
  suggested first build, has no consumers (A3 doesn't exist), and almost every
  agent depends on A6 Curator, making A6 a precondition rather than a peer. The
  real first decision is leaf-that-proves-plumbing vs hub-everything-needs.
- Four research tracks: resolve A6 against `Brain` (blocking); the
  deterministic/autonomous fork; adoption (the recorded cause of the prior
  failure, unaddressed by the diagram); and what feeds `Bug (sistema)`.
- Observed that the diagram is **not paradigm-uniform** — A13 must be
  deterministic to block, while A3 and A9 create sub-agents dynamically. So the
  question is which parts are deterministic, not which paradigm wins.
- Sequencing tied to the calendar: Gabrielle on leave from the week of
  2026-08-24, Luís part-time afternoons, deadline meeting end of the week of
  2026-08-17.

## [2026-08-17] refactor | Lint fixes (continued)
- **Not done**: the two dangling links from the meeting prep note
  (`Claude - context window and large data`,
  `LiveMode - AI tooling and data access`) are left as intentional to-do markers.
  Writing those pages means reading that note in full first — creating them from
  a skim is the mistake that produced the bad Papo page.

## [2026-08-17] synthesis | LiveScript → proxy auth (X-App-Id, GC-5)
- Source: code investigation of `livemode-roteiros-nextjs` + the installed
  `airtable` SDK v0.12.2 (`node_modules/airtable/lib/*.js`), plus the pasted GC-5
  task note. No `raw/` file — findings are code-derived, so filed as a synthesis,
  not a `sources/` page.
- Also posted as a comment on the proxy epic **AIRTABLEGC-5** ("Auth + multi-app"),
  comment id 10085 — the LiveScript client-side counterpart to that proxy work.
  (Jira, not Linear, at msilva's explicit direction despite the migration.)
- New: `syntheses/How LiveScript sends the proxy X-App-Id header.md`. Core
  findings, all verified in code: (1) the SDK's 401 → "provide valid api key"
  message is synthesized from the status code, ignoring the body
  (`base.js:119-121`) — a red herring for a missing `X-App-Id`; (2) `customHeaders`
  is a no-op in v0.12.2 for every op the app uses (all go through the deprecated
  `runAction`/`run_action.js`, not `makeRequest`); (3) `createMonitoredAirtableBase`
  is metrics-only and never touches outbound HTTP, so only the REST wrapper can
  set the header app-side; (4) the SDK uses its own `node-fetch`, so a global
  `fetch` patch misses it. Two fixes left open pending research: a startup
  `node-fetch` interceptor (Option A, doubles as the OTel propagation seam) or an
  SDK upgrade (Option B).
- **Corrections to the recorded onboarding model** on [[Airtable Proxy]] (design
  §4/§11): `X-Api-Key` is deferred (only `X-App-Id` now), and "drop the PAT" is
  not literally possible — the SDK always sends `Authorization: Bearer` and won't
  start without a non-empty key, so the app needs a **dummy** `AIRTABLE_API_KEY`
  and the proxy must overwrite the header. Annotated points 2 and 4 inline rather
  than rewriting them.
- Updated: [[Airtable Proxy]] (two inline corrections), [[LiveScript]] (new
  "Wiring to the proxy (auth)" section), [[Proxy Environments]] (the "uncomment
  one variable" note flagged incomplete — 401s without the header first),
  `index.md`.
- Open (carried to the ticket): does the proxy overwrite the incoming
  `Authorization`? Does it serve the Metadata API or only the data plane? Both
  gate how narrowly the interceptor is scoped.
