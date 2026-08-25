---
type: log
updated: 2026-08-25
---

# Log

Append-only. Newest at the bottom. Never rewrite an entry.
Prefixes: `ingest` | `callout` | `query` | `synthesis` | `lint` | `refactor` | `decision`.

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

## [2026-08-19] ingest | Luis / Matheus 1:1, 15:01
- Source: `raw/Luis _ Matheus - 2026_08_19 15_01 GMT-03_00 - Anotações do Gemini.md`.
- Discussed takeaways with msilva before writing, per the schema — confirmed
  the URL-based app identification is a **path** (`proxy.livemode.com/livescript`,
  not a subdomain) and that it's **settled**, not still pending Luís's earlier
  "mini relatório" ask.
- New: `meetings/2026-08-19 1-1 Matheus - Luís.md`,
  `decisions/2026-08-19 Identify proxy apps by URL path, not header.md`,
  `concepts/Claude Agent SDK.md`.
- Updated: `syntheses/How LiveScript sends the proxy X-App-Id header.md` —
  marked `superseded`, the whole header-injection option comparison retired by
  the new URL-path decision, kept as historical record.
  `projects/Airtable Proxy.md` — auth/identification section flags the
  supersession, not yet implemented. `concepts/Linear Project Structure.md` —
  fourth convention (only the active project needs structure now, delete the
  rest of the restored Jira backlog), msilva's autonomy on Linear decisions,
  `Release` added as an open question. `projects/Agent Flow.md` — dev-subagent
  design clarified as project-harness scope, not architecture scope; pointer
  to the new [[Claude Agent SDK]] concept; third independent confirmation
  (Luís, after Gabrielle and Carol) of the cross-project visibility pain.
  `people/Luís Fernandez.md` — Linear-org autonomy grant, visibility-pain
  confirmation, Claude Agent SDK experimentation, harness-scope statement.
- Also recorded but not promoted to pages: several open questions (what a
  Linear "Release" maps to, whether a written comparison report is still
  wanted, who originally deleted the Jira-migrated Linear issues, which
  restored issues are still valid) — left as open questions on the new
  meeting page rather than guessed at.

## [2026-08-19] callout | Four `[!msilva]` callouts on 2026-08-19 1-1 Matheus - Luís
- msilva answered four of his own open questions on
  `meetings/2026-08-19 1-1 Matheus - Luís.md` directly as `[!msilva]` callouts,
  same day the page was created. Discussed each in chat before resolving:
  1. **Mini-comparison report**: not needed — the identification decision is
     already settled.
  2. **`X-Api-Key`**: staying deferred and decoupled from the URL-path
     identification decision — not being touched now.
  3. **Linear "Release"**: resolved as *not* a distinct concept below
     Project — a release *is* a project inside the initiative, matching the
     LiveScript precedent already recorded on `concepts/Linear Project
     Structure.md` from Gabrielle's 2026-08-18 walkthrough. Milestones stay a
     manual per-project call; "ticket" = "issue."
  4. **Jira-restored issue validity**: msilva will personally triage the
     active project's own restored issues; folded into the existing action
     item rather than left as a separate question.
- All four callouts resolved as action taken and deleted from the page (they
  were to-do markers, not standing opinions).
- Updated: `meetings/2026-08-19 1-1 Matheus - Luís.md` (open questions
  resolved inline, action item extended), `concepts/Linear Project
  Structure.md` (Release open question answered, citing the resolution above).
- msilva's call, same session: the fifth open question (who deleted the
  original Jira-migrated Linear issues) doesn't matter and isn't worth
  pursuing — dropped, not answered. Updated:
  `meetings/2026-08-19 1-1 Matheus - Luís.md`.
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

## [2026-08-17] synthesis | Agent Flow research agenda
- msilva stated the project is at the **research step**. Recorded as
  `phase: research` on [[Agent Flow]].
- New: `syntheses/What should the Agent Flow research phase study.md`.
- First version argued a **sequencing problem**: A5 Watcher has no consumers and
  A6 Curator is a precondition, so the first decision was leaf-vs-hub.
- Per msilva's instruction, the `Brain` repository was **dropped as a research
  track**. The factual record of its existence stays in
  [[2026-08-14 Papo de Projetos]]; only the agenda changed.

## [2026-08-17] ingest | Fluxo Agêntico project instruction
- Source: `raw/instrucao-projeto-fluxo-agentico.md.txt` — *"Instrução do Projeto:
  Fluxo Agêntico AI-First"*, created by Gabi. **The authoritative spec for
  [[Agent Flow]]**; supersedes verbal descriptions and the diagram.
- New: `sources/Fluxo Agêntico project instruction.md` — per-agent specification
  for all 14, the authoritative connection list, and the build strategy.
- **Corrected the synthesis**: the sequencing problem it led with **does not
  exist**. The instruction mandates *"anárquica primeiro, integrada depois"* —
  agents built independently, in production individually, explicitly **without
  cross-dependency**. A consumer-less A5 is the method, not a problem. Correction
  left visible on the page.
- Resolved: **autonomy over determinism** (AI-First — *"a IA é o meio de
  execução"*, humans as strategic approvers); **`Bug (sistema)`** is A1 ingesting
  *"alertas de sistemas"*; the intelligences' dotted lines go to **A9**; **A12
  also hard-blocks**, so A13 isn't the only gate; the project currently lives in a
  **claude.ai Project** with an unconfigured recurring-task facility.
- Recorded A5's concrete cadences (health 5 min / usage 1h / report 24h) and the
  four-part agent spec template the instruction requires: inputs and outputs,
  dependencies, success criteria, **limits**.
- New open questions: **Monday** is listed as an intake channel despite the Linear
  migration; **A9's controlled stack omits Go** while the [[Airtable Proxy]] is
  Go; whether **Programado** can drive a 5-minute cadence.
- Track 2 reframed: scheduling and runtime is now the sharpest unknown, and token
  cost binds there — a 5-minute cadence is ~288 invocations/day.

## [2026-08-17] ingest | Matheus / Gabriel — CazéTV revenue recognition
- Source: `raw/Matheus _ Gabriel - Fluxo de reconhecimento da receita cztv -
  2026_08_17 15_02 GMT-03_00 - Anotações do Gemini.txt`. The consultation
  Gabrielle routed to msilva on 2026-08-14; held today.
- New: `meetings/2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow.md`.
- Gemini attributed **every** line to msilva this time — the inverse of the
  earlier transcripts. Attribution inferred from content.
- **The $7/day token case now has a mechanism**, not just a number: wholesale
  context loading (an entire 816-row Airtable table fed to an agent that needs
  slices) plus a probable **loop** repeating operations. Gabriel's working
  component is the one packaged as a **skill that fetches narrowly**; the broken
  one is unpackaged.
- **n8n is the de facto runtime** for agent flows here, on a **licence shared with
  finance** — no isolation, executions crowd each other's logs, and a stuck
  *"fechamento em andamento"* lock blocked the debugging session entirely.
- Updated: [[Agent Flow]] (token cost now has a diagnosed cause),
  [[What should the Agent Flow research phase study]] (Track 2 shortened — n8n
  already answers much of the runtime question), `index.md`.
- First recorded instance of msilva doing the **A4 Teacher** job by hand:
  diagnose, explain, agree next steps.
- Financial figures deliberately not recorded; the process is, since the
  automation can't be reasoned about without it.
- Open: the review can't proceed until Gabriel resets the stuck lock and produces
  an execution with logs.

## [2026-08-17] refactor | Cite airtable.js source for the customHeaders limitation
- Added upstream source permalinks to
  [[How LiveScript sends the proxy X-App-Id header]] finding #2: `run_action.ts`
  (hardcoded headers, no `customHeaders` merge — verified via raw GitHub),
  `base.ts` (`makeRequest` merges them), `airtable.ts` (the constructor option
  that makes it *look* usable).
- Recorded that **no official doc states the limitation** — the README advertises
  `customHeaders` without noting it's inert on the deprecated `runAction` path, so
  the source is the only authoritative evidence.
- Also confirmed out-of-band (npm dist-tags): **0.12.2 is the latest published
  version**, so "Option B — upgrade the SDK" has no target. Left the synthesis's
  Option B wording as-is for now (still says "unconfirmed a version exists");
  flagged to tighten on the next pass.

## [2026-08-17] synthesis | GC-5 implemented — X-App-Id on both transports; code review #1 fixed
- Context: a code review of `livemode-roteiros-nextjs` flagged that our
  uncommitted GC-5 change broke `pnpm test` (→ `pnpm build`): a top-level import
  of `AIRTABLE_APP_ID` from `airtable-constants` dragged in its load-time
  `requireEnv()`, so the monitoring suites threw at import. Confirmed by running
  the two suites. Fixed by moving the constant into its sole consumer
  (`airtable-monitoring.ts`). Suite green: 47 files / 370 tests.
- Implemented the REST-path half of GC-5: `X-App-Id` injected centrally in
  `fetchAirtableWithMonitoring`; `airtableApiBaseUrl()` makes **data-plane** REST
  honour `AIRTABLE_ENDPOINT_URL` like the SDK; **metadata** API pinned to
  `AIRTABLE_METADATA_BASE_URL` (direct) pending the proxy contract.
- Commits in `livemode-roteiros-nextjs` on `feature/airtable-proxy-observability`:
  `754896b` (SDK path — pnpm patch + monitored-base header + constant move) and
  `d565c26` (REST path). Both validated: `tsc --noEmit` clean, 370 tests, lint clean.
- Updated: [[How LiveScript sends the proxy X-App-Id header]] (Resolution section;
  Options A/B marked not-taken, B confirmed impossible), [[Airtable Proxy]]
  (point-2 warning → resolved), [[LiveScript]] (GC-5 wiring section → implemented),
  `index.md` (synthesis line; removed a stray `a`).
- Still open (gate the endpoint flip): does the proxy overwrite `Authorization`,
  and does it serve the Metadata API. The 5 metadata call sites stay direct and
  still need a real PAT until answered.

## [2026-08-17] lint | Third health check
- 27 pages, 8 raw sources, 16 commits.
- **Credentials still in git history** (6 commits) — third lint reporting it.
  Purge remains blocked; PAT and Firebase key rotation still outstanding.
- Found: the "observability-only" claim is now contradicted by shipped code;
  three accounting pages misfiled in `sources/` with `type: reference`, missing
  `status`/`updated`, absent from the index, one a full orphan; an empty
  `Novo(a) Documento de Texto.txt` in `raw/`; the prep note still `draft` after
  its meeting date.
- Clean: raw coverage complete, no stale pages, no other contradictions, working
  tree free of secrets, one legitimate dead-end page.

## [2026-08-17] refactor | Lint fixes — misfiled reference pages, stale claim
- **Corrected [[Airtable Proxy]]**: *"no enforcement"* removed. The proxy 401s
  requests without `X-App-Id` — that is enforcement, shipping in v1 — and
  proxy-side 429 retry is intended. Narrowed the claim to "no caching, no
  rate-limiting" and kept observability-*first* as the strategy. Correction left
  visible rather than silently rewritten. This closes a tension carried since the
  first lint.
- **Moved to `reference/`** (they are original material, not summaries of `raw/`
  documents, so `sources/` was wrong): [[Claude capabilities map - accounting data scope]],
  [[Sharing the accounting automation with the team]],
  [[Sharing via Projects - the accounting project]]. Added `status:`/`updated:`
  and aliases; all three now in `index.md`.
- **Resolved both dangling links.** The prep note's `## Related` section pointed
  at two never-written placeholders; the three companion pages above already
  covered that territory, so the links were repointed rather than new pages
  invented. **The wiki now has zero broken links.**
- **Wired the sharing research into [[Agent Flow]]** — the substantive find. The
  project names collaborative maintenance as the gap it exists to fill, and a
  worked page on distributing one person's workflow to a team had been sitting
  unlinked in the wrong folder. Combined with the Gabriel session (working
  component = packaged skill, broken one = unpackaged), **packaging is emerging
  as both the token-cost lever and the sharing mechanism.**
- Not touched: the empty file in `raw/` (immutable layer, needs msilva's word).

## [2026-08-17] ingest | Gabriel session, part 2 (15:31)
- Source: the 15:31 transcript — a **second segment of the same meeting**, after
  the stuck lock was cleared. Appended to the existing meeting page rather than
  creating a second one; both files now listed in its frontmatter.
- **Corrects a claim carried across three pages**: ~$7/day is the **bad case**,
  not the characteristic cost. One observed run cost **11 centavos** against ~670
  earlier, same flow, same day. Cost is intermittent, so the primary suspect
  shifts from context volume to a **runaway execution** — two workflows firing
  concurrently where one should chain into the next. Prime suspect: the
  `simplificado` pass-through workflow.
- **Corrects the sharing claim on [[Agent Flow]]**: work *can* be handed to a
  team today — the colleague had already shared his Claude skill, and the n8n
  instance is team-wide. The real gaps are narrower: **maintenance by someone
  other than the author**, **isolation** on a shared instance, and **cross-area**
  sharing.
- New fact: a **token-consumption dashboard exists** and is obtainable on request.
  The measurement instrument for a constraint the wiki had only recorded as unease.
- Separated two failure classes that were being conflated: **business-rule
  comprehension** (the revenue figure is wrong) versus **execution/orchestration**
  (runaway cost, concurrency, missing logs).
- Updated: [[Agent Flow]] (two corrections),
  [[What should the Agent Flow research phase study]] (cost causes reordered — a
  broken trigger chain on a 5-minute cadence compounds where an oversized prompt
  does not).
- Open: execution traceability is still the blocker; the revenue number is still
  wrong.

## [2026-08-17] ingest | Gabriel Packer — DAG-driven agent orchestration
- Source: `raw/Clippings/Gabriel Packer → visorfinance.app (@gkpacker) no X.md`,
  published 2026-07-23, clipped 2026-08-17. First web-clipped source, and the
  first with reliable dates in its own frontmatter.
- New: `sources/Gabriel Packer - DAG-driven agent orchestration.md`.
- **Almost certainly the model Luís shared** — it uses Orca, orchestrates
  information flow, and is graph-based, matching all three details msilva recorded
  in [[2026-08-14 1-1 Matheus - Gabrielle]].
- **Resolves "gráfis"**: Luís meant a **dependency graph (DAG) driving execution
  waves** — explicit `blockedBy` edges, parallelism from the graph rather than
  from agent count. Previously flagged unverified on [[Agent Flow]].
- **Resolves the Orca collision**: Livemode's Orca is a business-critical ML
  system; Packer's Orca creates git worktrees and runs agent terminals. Unrelated.
- Recorded as a **production-tested implementation of the projects branch**
  (A7→A8→A9): orchestrator that writes no code, waves off a fresh `origin/main`,
  one agent per ticket, human quality gate at merge.
- **His ticket template is effectively A7's output spec** — scope and explicit
  out-of-scope, acceptance criteria, test scenarios, affected files, `blockedBy`,
  rollout and kill switch, success metrics. *"Colocar mais agentes não corrige uma
  especificação ruim"* — which argues A7, not A9, is the highest-leverage agent.
- Third independent convergence on **skills as the packaging unit**.
- Updated: [[Agent Flow]] (graphs resolved, Orca disambiguated, prior-art section),
  [[What should the Agent Flow research phase study]] (Track 2b added; the Luís
  artifact removed from the blocking list), `index.md`.
- Caveats recorded: solo founder, single codebase, and one-agent-per-ticket where
  A9 is specified to spawn sub-agents on demand.

## [2026-08-17] ingest | Gabriel Packer — solo founder AI workflow (part 1)
- Source: `raw/Clippings/Post by @gkpacker on X.md`, published 2026-03-26.
  **Part 1** of the series whose part 2 was ingested earlier today.
- New: `sources/Gabriel Packer - solo founder AI workflow (part 1).md`;
  cross-linked from the part 2 page.
- **The delta between the two posts is the finding**: part 1 dispatches every
  ticket by hand, part 2 automates only that. Every quality gate, the spec
  discipline, and the human merge review are unchanged. **He automated the
  coordination and left the judgement alone** — a migration order, not just a
  target state.
- New material from part 1: a quality-gate-heavy pipeline (QA agent driving Chrome
  via DevTools, CI, manual test, staging, post-deploy verification); **two levels
  of parallelism** (DAG across tickets; function contracts agreed between
  implementation and test agents within one); the loop closing on **AppSignal +
  PostHog**, which is A11 Product Intelligence performed by a human; and
  role-specialized agents rather than on-demand sub-agents.
- **Outside lesson worth keeping**: a practitioner reply — *"write agent
  instructions to files, not memory […] files persist across sessions and every
  agent reads the same source of truth."* Direct design input for **A6 Curator**,
  and the premise this wiki already runs on.
- **Linear ships its own agent**, which Packer judged comparable to his first
  stage but less flexible. Relevant given
  [[2026-08-14 Migrate project management from Jira to Linear]].
- **Tooling instability recorded**: Conductor (March) → Orca (July). Argues against
  committing to a single orchestrator.
- New open question: nothing among the 14 agents covers **automated QA**, which is
  a first-class stage in Packer's flow. Gap or deliberate omission?
- Updated: [[Agent Flow]], [[What should the Agent Flow research phase study]],
  [[Gabriel Packer - DAG-driven agent orchestration]], `index.md`.

## [2026-08-17] lint | Fourth health check
- 29 pages, 20 commits. **Structurally clean** — no frontmatter gaps, no orphans,
  no broken links, no stale pages, everything indexed.
- **Credentials still in git history** (6 commits), fourth report. Rotation
  outstanding.
- Content findings: [[Agent Flow]] and its synthesis recommended **different first
  builds** (A5 vs A7) without reconciling; time-critical questions for Gabrielle
  scattered across five pages with her leave starting the week of 2026-08-24;
  **packaging** and **token cost** are themes with no `concepts/` home despite
  four and three sources respectively; `people/` still empty while operational
  who-knows-what accumulates in meeting pages.
- Noted a limitation: the mechanical checks (links, frontmatter, orphans) do not
  read page bodies, so intra-page contradictions pass them.

## [2026-08-17] query | Agent Flow
- Answered from [[Agent Flow]], both Fluxo Agêntico sources, both Packer sources,
  and [[What should the Agent Flow research phase study]]. No new synthesis page —
  the research agenda already covers the ground.
- **Two defects found by reading the page**, both missed by the lint: a duplicated
  "longer-term ambition" paragraph, and an internal contradiction where the
  Philosophy section recorded autonomy as settled while "Design approach" and
  "Open questions" still listed deterministic-vs-autonomous as the first open call.

## [2026-08-17] refactor | Fix query-surfaced defects; reconcile A5 vs A7
- Removed the duplicated paragraph on [[Agent Flow]].
- **Resolved the deterministic/autonomous contradiction**: the spec settles
  autonomy; what remains open is **where the human gates sit**. Bounded by two
  facts already recorded — A12 and A13 must be deterministic because they block,
  and Packer keeps a human gate at merge.
- **Reconciled A5 vs A7** in [[What should the Agent Flow research phase study]]:
  they answer different questions. A5 is the safest *first build* (closed scope,
  no human initiative required); A7 is where the *leverage* is (spec quality
  bounds everything downstream). Recommendation recorded: **A5 first, specified to
  A7's standard.**

## [2026-08-17] synthesis | Which agent should be built first
- Worked through in discussion with msilva. New:
  `syntheses/Which agent should be built first.md`.
- **Conclusion: A5 Watcher, targeting the [[Airtable Proxy]]'s telemetry.**
- **The load-bearing argument is narrower than the wiki previously implied**: not
  that A5 is best-specified, but that it is the **only agent needing no human
  initiative** — the one documented cause of the prior attempt's death.
- Recorded the honest case *against* A5 so it isn't rediscovered: least
  agent-shaped of the fourteen, most cost-exposed (~288 invocations/day against a
  runaway-loop failure mode), teaches least, and Gabrielle's suggestion predates
  every document now in hand.
- **A1 evaluated as the alternative** at msilva's prompting. Better second build:
  most-connected agent where A5 is least; produces nothing usable alone; maximally
  exposed to the adoption failure; and its data contract can't be designed before
  its consumers exist. Named explicitly that **choosing A1 first is an argument
  against anarchic-first**, and should be raised with Gabrielle as such.
- **Scope caveat recorded**: the proxy serves incident detection cleanly, but its
  anti-pattern dashboards are *technical* opportunity (inefficient queries), not
  the *product/process* opportunity the instruction specifies. Must be stated in
  A5's spec rather than silently redefining the agent.
- **Timing dependency identified**: the proxy carries no production traffic until
  GC-5 lands, so the two tracks become usefully sequential — GC-5 → real telemetry
  → A5 has data → A5 produces `Bug (sistema)`. Specify A5 now against telemetry
  being built now.
- Flagged for repo verification: `hasFilter`/`recordCount`/`bytes` are recorded as
  coming only from LiveScript's SDK wrapper, so some anti-pattern panels may be
  app-fed. Code wins over docs.
- Updated: [[Agent Flow]], [[What should the Agent Flow research phase study]],
  `index.md`.

## [2026-08-17] synthesis | A5 as event receiver, and the risks that introduces
- Continued the A5 discussion. Expanded
  [[Which agent should be built first]] with the watching mechanism and its risks.
- **A5 does not poll.** Grafana alert rules already evaluate on a schedule and
  fire; A5 becomes the **webhook receiver**. Matches the spec, which lists
  *"webhooks"* and *"alertas de sistemas"* among A1's channels. Cost falls from
  ~288 invocations/day to *(real incidents)* + one daily pass, and detection stays
  deterministic and free in PromQL.
- **The proxy is not modified** — its telemetry path is deliberately non-blocking
  (drops rather than stalls), and Grafana already does the alerting.

**Settled with msilva 2026-08-17:**
- **Hybrid**: event-driven incidents, periodic 24h pass for opportunities, which
  are trend-shaped and have no firing moment. The 5-minute cadence belongs to
  Grafana.
- **Grouping and throttling** in Grafana's notification policy, applied *before*
  the agent, plus a daily invocation ceiling that fails closed.
- **The daily pass doubles as a dead-man's switch** — event-driven monitoring
  otherwise fails silently, and there is a documented instance of exactly that
  (Gabriel's stuck lock with no logs on the shared n8n instance).
- **A5 gets direct access to Prometheus, Loki and the observability stack**, not
  just alert payloads — otherwise the intelligence migrates into static thresholds
  and A5 degrades into a notification formatter.
- **Explicit limit**: A5 never quotes payloads or headers into an issue; IDs and
  metrics only. Also: files but never fixes, never touches production, stays
  silent unless it can say why something matters.

**Open direction, explicitly not a decision** — msilva raised implementing A13.
Recorded because A5 without A13 generates precisely what A13 exists to block:
recurring conditions filing the same issue daily. The observation worth keeping is
that **A5's required dedup logic and A13's purpose are the same thing**, so A13 may
be the natural second build. Undecided: separate agent, or logic inside A5.

- Also recorded as accepted risk: alert fatigue carries higher stakes than the
  prior attempt (Linear is the team's live board, mid-migration), and **nothing can
  be tuned until GC-5 lands** — the tuning loop *is* the project.

## [2026-08-17] query | A5
- Answered from [[Fluxo Agêntico project instruction]],
  [[Fluxo Agêntico diagram]], [[Agent Flow]] and
  [[Which agent should be built first]]. No new page — the synthesis already
  holds it.
- Surfaced a maintenance hazard: `AGENTS.md` had been created as a **full copy** of
  `CLAUDE.md` (identical but for self-references).

## [2026-08-17] refactor | AGENTS.md becomes a pointer, not a copy
- Two copies of the schema would drift, and the schema is the one file where drift
  is expensive.
- `CLAUDE.md` kept **canonical** — it is the file this environment auto-loads, and
  the mechanism the vault has relied on throughout.
- `AGENTS.md` rewritten as a pointer to it, repeating **only** the two hard rules
  (never write a credential; never modify `raw/`) so a tool reading that file alone
  still fails safe. Those two are stable, so the drift risk is negligible and the
  safety value is not.
- `CLAUDE.md` updated: directory layout now names `AGENTS.md` and explains the
  arrangement, with an explicit *don't re-duplicate the schema* instruction so a
  future session doesn't helpfully undo this. Frontmatter exemption extended to
  cover it.

## [2026-08-17] synthesis | How to implement A5 Watcher
- New: `syntheses/How to implement A5 Watcher.md` (`status: draft` — unvalidated
  against the repo). Linked from [[Which agent should be built first]] and
  `index.md`.
- **The GC-5 dependency is narrower than recorded.** The repo ships a local
  `grafana/otel-lgtm` stack, so the proxy can be run against a sandbox base with
  deliberately bad traffic generated on purpose — unfiltered reads, N+1 loops,
  enough volume to trip 429s. Rules, notification policy, receiver, dedup, the
  skill and the daily pass can all be built and validated **before** GC-5 lands.
  **Only threshold tuning needs production traffic.**
- **Alert rules belong in the proxy repo**, provisioned as code beside the
  dashboards — they depend on metric names the proxy emits, and should version with
  the code that emits them.
- **The existing 429 rule needs re-tuning**: `increase(...[5m]) > 0` fires on any
  single 429, and 429s are bursty by design. Needs a rate plus a `for:` duration so
  it fires on sustained limiting.
- **A proxy-liveness alert is missing and matters most** — without a no-data rule,
  a dead proxy produces silence, which the event-driven design cannot distinguish
  from health.
- **Dedup via Linear as the state store** — fingerprint from the alert's grouping
  labels, search for an open issue, comment rather than create. That logic *is*
  A13's core scoped to one producer, and needs no new infrastructure.
- **New open decision surfaced**: n8n versus a Cloud Run service for the receiver.
  n8n is free and is the area's runtime; Cloud Run is isolated and already the
  proxy's target. The shared-n8n isolation problem is documented, not hypothetical.
- Recorded read-only credentials as a structural enforcement of the "never fixes"
  limit, and success criteria led by **action rate** — the proportion of filed
  issues a human acts on — as the anti-fatigue measure.
- Flagged **verify against the repo**: exact metric names and label sets, and the
  metrics-vs-logs naming convention, before writing PromQL.

## [2026-08-17] decision | Agents live in a monorepo, starting with A5 only
- msilva: **monorepo layout, single-agent contents.** Explicitly not building
  infrastructure for fourteen agents up front — that would be the
  *"foundation cheio de funções para uso futuro"* Packer warns against.
- Added a *Where the code lives* section to [[How to implement A5 Watcher]].
- Layout: `agents/a5-watcher/` rather than `src/`, so the second agent is an
  addition and not a refactor; `shared/` deliberately empty until two agents need
  the same thing; per-agent `README.md` carrying the instruction's four-field spec
  (inputs/outputs · consults/feeds · success criteria · limits); a repo-level
  `CLAUDE.md` applying the instructions-in-files lesson.
- **`contracts/bug-sistema.md` is the one artifact to write first** — the only one
  with two consumers before either exists (A5 emits, A1 will consume). Turns Phase
  2 into wiring rather than archaeology.
- **A5 deliberately spans two repos**: alert rules stay in the [[Airtable Proxy]]
  repo so a metric rename and its rule update land in the same commit. Silent
  breakage from a rename is the worst failure available to a monitoring system.
  The agent's README must point at them.
- Noted the anarchic-first tension: a monorepo is an integration-first structure
  and invites premature coupling. Mitigation recorded — agents may depend on
  `shared/`, never on each other.
- Open: is this a fourth repo the **homologation flow** has an opinion about? And
  if A6 Curator is memory-as-files, does its memory live here or does A6 point at
  this vault — which is already a working prototype of that pattern.

## [2026-08-17] refactor | Correct the prior-attempt failure diagnosis
- **msilva corrected a reading propagated across three pages.** The previous
  agent's failure had been recorded as *unwillingness to file* — people wanting to
  report rather than describe a problem. **That is too narrow.**
- The actual failure was **channel fragmentation**: the bot sat on one Slack
  channel waiting to be `@`-mentioned, while people report problems through
  whatever path is at hand — a DM, another channel, a meeting, walking over. Most
  reports never reached it.
- **This retracts a claim**: "nothing in the 14-agent design addresses adoption"
  was wrong. **A1 Receptor Universal addresses the fragmentation half by design**
  — Slack, Monday, email, forms, webhooks, system alerts.
- **The unaddressed half is now sharper**: *invocation*. If every channel still
  requires a form, a webhook or a mention, the same failure is rebuilt across six
  surfaces instead of one. New open question, on [[Agent Flow]] and Track 3 of the
  research agenda: **does A1 listen passively, or wait to be addressed?** Passive
  listening is a materially different system — classifying a firehose, with cost,
  noise and privacy consequences nobody has discussed.
- **Strengthens the A5-first case rather than weakening it.** A5's input is a
  system alert: one channel, no fragmentation, no invocation, no human in the loop.
  It is the only agent immune to *both* halves.
- Updated: [[Agent Flow]] (correction callout on the prior attempt; open question
  rewritten), [[Which agent should be built first]],
  [[What should the Agent Flow research phase study]] (Track 3 reframed as
  invocation-not-friction, with a table splitting the two halves).

## [2026-08-17] lint | Fifth health check
- 32 pages, 27 commits. **Mechanically clean** — no frontmatter gaps, no orphans,
  no broken links, one legitimate dead end.
- **Credentials still in git history** (6 commits), fifth report.
- **Intra-page contradiction found**: [[Which agent should be built first]] said A5
  *"would be watching an empty pipe"* until GC-5, then 160 lines later pointed at
  the page establishing that only *tuning* is blocked. Reader hits the strong claim
  first and concludes the work is blocked.
- **Structural drift**: [[What should the Agent Flow research phase study]] had
  become a stale mirror — Track 1 recommending A5 without the case against it,
  Track 2 calling scheduling "the real unknown" after it was settled, the A5-vs-A7
  reconciliation duplicated.
- Two themes with no home: **packaging** (six sources) and **token cost** (four).
  `concepts/` held one page.
- Noted my own check limitation: raw-coverage greps filenames, and `log.md` names
  raw files, so uningested files report as covered.

## [2026-08-17] refactor | Reconcile GC-5, demote the agenda, file packaging
- **Reconciled the GC-5 claim** on [[Which agent should be built first]]: the
  section now leads with what is *actually* blocked (threshold tuning only) and
  keeps the correction visible. Sequencing restated as **build and validate
  locally now; tune when traffic arrives** — not "wait for GC-5".
- **Demoted [[What should the Agent Flow research phase study]] to a status board
  and router.** It tracks settled-vs-open and points at the pages carrying the
  reasoning; the duplicated tracks are gone. Kept: the adoption question (the one
  live research question with no other home), the pre-2026-08-24 question list, and
  a *Corrected along the way* section preserving three retracted claims.
- **New: `concepts/Packaging as skills.md`** — the theme converged from six
  independent sources. Core claim: packaging is **both** the token-cost lever and
  the sharing mechanism. Records the Gabriel case as a controlled comparison
  (packaged component works, unpackaged one fails), and what packaging does *not*
  solve: **maintenance** and **runtime isolation**.
- **Hardened the `lint` operation in `CLAUDE.md`**: exclude `log.md` from raw
  coverage checks, and read the bodies of pages edited more than twice — mechanical
  checks cannot see intra-page contradictions, which have now caused two findings.
- Not done: `people/` remains empty (msilva's standing call); the prep note is
  still `draft` after its meeting date; the empty `Novo(a) Documento de Texto.txt`
  is untouched in `raw/`.

## [2026-08-17] decision | A5's receiver runs on Cloud Run
- Worked through **how A5 receives Grafana events**, then what should host the
  endpoint. msilva settled it: **a plain container on Cloud Run,
  `min-instances=0`** — idle costs nothing, it joins the local `otel-lgtm` compose
  stack, and a container **defers** the hosting question, which hangs off the
  still-open production telemetry backend.
- New: `decisions/2026-08-17 A5 receiver runs on Cloud Run.md`. Records all three
  rejected alternatives **with what would reopen each** — n8n (isolation),
  Cloudflare Workers (reach + the skill runtime; reopens if prod lands on Grafana
  Cloud), and co-location beside the observability stack (*deferred, not rejected* —
  the best option if the stack is self-hosted, since it deletes the public surface
  and the bearer token outright).
- Updated: [[How to implement A5 Watcher]]. Stage 2 rewritten around the decision.
  Three findings added from this session:
  - **`--no-cpu-throttling` reverses the cost basis**, and because CPU is throttled
    after the response, triage cannot run post-200 — which is what the Cloud Tasks
    queue is actually for.
  - **Resolve events were missing from the design.** Without handling
    `status: "resolved"`, A5 files bugs and never learns they cleared — the stale-
    board version of risk #5.
  - **Fingerprint from `groupLabels`**, not Grafana's per-alert `fingerprint`, so
    dedup cannot drift from the grouping the policy performed.
- Also added: *parse at the edge* — one adapter from vendor payload to an internal
  alert shape, so an eventual Cloud Monitoring/Datadog switch touches one file.
- New open decision recorded: **where the daily ceiling's counter lives**, since
  Cloud Run scales horizontally and an in-process counter survives neither.
- Flagged, not acted on: the **agents-monorepo decision (2026-08-17) is recorded
  inline** in [[How to implement A5 Watcher]] rather than as a `decisions/` page,
  which is inconsistent with this entry. msilva's call whether to extract it.
- Corrected in conversation: I claimed n8n could not join the compose stack. It can;
  the real objection is that a local n8n isn't the shared-licence instance you would
  deploy to, so the local test doesn't validate the production runtime.

## [2026-08-17] lint | Sixth health check
- 34 pages, 29 commits. Mechanically clean — no orphans, no broken links, no
  frontmatter gaps, everything indexed. The new Cloud Run decision is properly
  wired and [[How to implement A5 Watcher]] already shows that question closed.
- **Critical: [[Proxy Environments]] asserted the opposite of the truth on a
  security matter.** Its callout claimed *"the vault's git history was rebuilt so
  the values are not recoverable from it."* The rebuild was blocked and never
  happened; the PAT and Firebase key remain readable in **6 commits**. `log.md`
  recorded the block correctly, so page and log directly contradicted each other.
- Five prior lints reported "credentials in history" as a standing item and none
  caught that a page was claiming the reverse. **Mechanical checks cannot see
  this**; the body-reading rule added on 2026-08-17 found it on its first run.
- Also: the hardened raw-coverage check (excluding `log.md`) correctly surfaced the
  empty `Novo(a) Documento de Texto.txt`, which had been masked. Prep note still
  `draft`, fourth report.

## [2026-08-17] refactor | Correct the false "history rebuilt" claim
- Rewrote the [[Proxy Environments]] callout to state the true position: **working
  tree clean, history not**, with the `git grep` command to verify it and the two
  outstanding actions ranked — **rotate the keys** first, purge history optionally.
- Added a visible correction block recording what the page previously claimed and
  why it was wrong. A page asserting a security job is done, when it isn't, is
  worse than a page saying nothing.
- **Escalated to `index.md`**: a danger callout at the top of the catalog, since
  five lints of standing-item reporting failed to get it resolved and the index is
  the page most likely to be read first.
- Verified: no residual false claims anywhere in the vault; the only surviving
  instance of the old wording is inside the correction that quotes it.

## [2026-08-18] query | first agent to build — criterion changed to utility

- Query: msilva thinking about which agent to build first. **His manager's criterion
  is utility**, where the 2026-08-17 analysis had optimized for lowest risk of
  failing. Manager recorded neutrally — provenance unconfirmed, asked twice.
- Three new facts from msilva, all 2026-08-18:
  - **Orca and other services are to be plugged into the Airtable Proxy** — confirms
    the already-recorded company-wide intent, and retires the "Orca or LiveScript?"
    targeting question for A5.
  - **A7 cannot be chat-only** — it needs multiple context sources to build a PRD.
  - **His position: A1 + A2 first.** Recorded as a position, not a decision.
- Updated: syntheses/Which agent should be built first.md (new criterion-1 heading,
  new utility section ~130 lines, confirm-list rewritten),
  syntheses/What should the Agent Flow research phase study.md (criterion warning,
  A5-first row struck, Orca item resolved, two new open questions),
  projects/Agent Flow.md (open questions rewritten, Bug (sistema) producer note),
  projects/Airtable Proxy.md (Orca as planned consumer; shared data plane framing),
  index.md
- Resolved: "Orca or LiveScript?" — both, through one chokepoint.
- New open: is Orca's migration sequenced or directional? Is Orca the "second
  Airtable consumer" already recorded as over-fetching? How often does the area start
  a new project? **Who owns context retrieval across agents** — nobody among the 14.
- Candidate concepts/ page noted in index: **Agents read primary sources** — they do
  not wait for the agent meant to digest it. Two instances now (A5 → Prometheus/Loki,
  A7 → PRD corpus et al).
- **No decisions/ page written.** Nothing was decided; the criterion change itself
  needs confirming with Gabrielle before 2026-08-24.

## [2026-08-18] synthesis | context retrieval is not a fifteenth agent

- msilva asked whether the shared context-retrieval need warrants a new agent, and
  answered the two prior open questions: **Orca migration timing unknown**, **no
  project-start metric**.
- New: concepts/Agents read primary sources.md — promoted from candidate to a page.
  Three instances (A5 → Prometheus/Loki, A7 → PRD corpus et al, A1 → Linear + the
  Airtable board). The rule is what makes *anarchic-first* buildable: cross-agent
  dependency is forbidden, depending on a data source is not.
- **Answered: no fifteenth agent.** Decisive reason — a retrieval *agent* would create
  the cross-agent dependency phase 1 forbids, for A1, A5 and A7 at once. It would also
  duplicate A6 (which A13 exists to block) and be the intermediary the concept warns
  against. Build a shared `tools/` layer in the monorepo instead; precedent is Packer's
  four skills, `orca-linear` among them.
- **Corollary recorded: A6 splits** into retrieval (tools, now) and curation (an agent,
  phase 2). This reinterprets the architecture's centrepiece, so it is filed as a
  recommendation and added to the Gabrielle list, not as settled.
- Recorded as confirmed-unknown rather than open: Orca timing (**put no date on "A5
  second"**) and project-start rate (**so A7 cannot be evaluated on utility at all** —
  itself an argument for A1 + A2).
- Updated: syntheses/Which agent should be built first.md,
  syntheses/What should the Agent Flow research phase study.md,
  projects/Agent Flow.md, index.md
- New open, all on the concept page: does Gabrielle accept the A6 split? What does
  `Brain` actually contain and expose — unread, and the highest-value thing to look at
  before building any retrieval. Access/permission scope for direct reads.

## [2026-08-19] query | Linear

- Answered from [[Linear Project Structure]] and
  [[2026-08-14 Migrate project management from Jira to Linear]] — hierarchy
  (initiative/project/milestone/issue), how work enters (docs → Claude writes
  the structure), the 250-issue cap and 2026-09-09 trial expiry, the flat-
  issues/small-milestones convention driven by the status board's subtask
  blindness, and the archived-issues/assignee-drift cleanup from 2026-08-18/19.
- No new synthesis page — both pages already cover the ground comprehensively.

## [2026-08-18] synthesis | first-agent candidate comparison (decision doc)

- msilva asked for a doc covering motivations, pros and cons for building each
  candidate first.
- New: syntheses/Comparing the first-agent candidates.md — deep treatment of
  **A1+A2, A5, A7, A4**; brief on A3, A13, A14, A10-A12, A6; a side-by-side table;
  facts that apply whichever is chosen; a recommendation (**A1 + A2**) with the
  sequence behind it; and eight things to decide before 2026-08-24.
- **Division of labour recorded on both pages to prevent drift**: the new page is the
  *comparison*, written to be handed to someone else; [[Which agent should be built
  first]] stays the *reasoning record* and remains the authority where they disagree.
- Makes the choice of utility reading explicit rather than implicit — expected value
  (value x P(works) x frequency), with "what it teaches" as tiebreak. Notes that under
  "value if it works" the answer flips to A7.
- Two cells in the comparison table are marked honestly unmeasurable: A7's frequency
  of use (no project-start metric) and A5's time to first value (Orca migration
  unscheduled).
- Updated: index.md, projects/Agent Flow.md (deliverables 1 and 3 marked drafted),
  syntheses/Which agent should be built first.md (forward pointer)
- Not published outside the vault; offered.

## [2026-08-18] synthesis | one-pager for the first-agent decision meeting

- New: meetings/Meeting prep - first agent decision.md — one page for the deadline
  meeting with Gabrielle and Luís, cut down from
  [[Comparing the first-agent candidates]]: the ask, where we are, the A1 + A2
  recommendation with its two comparative reasons, four candidates at one line each,
  the proposed sequence, six decisions needed, and the two things that would flip the
  answer.
- **Meeting date is unknown** — recorded as such rather than guessed. Set for "the end
  of the week beginning 2026-08-17"; the page carries a note to fill the date in and
  rename, per the meetings/ naming convention.
- States the anarchic-first departure as a disagreement to raise, not something to
  work around.
- Updated: index.md, syntheses/Comparing the first-agent candidates.md (pointer)

## [2026-08-18] lint | index and reconcile the 2026-08-17 weekly page

- **Housekeeping failure, recorded rather than hidden.** meetings/2026-08-17 Weekly -
  Projetos e Tarefas.md (323 lines) was sitting uncommitted in the working tree from a
  parallel session and was **swept into commit eca19f4**, whose message does not
  mention it. Content is intact; nothing was lost. It was **absent from index.md and
  log.md**, i.e. invisible per the schema.
- Indexed it. Two of its facts change pages written earlier today:
  - **The matriz gateway** — the business independently proposed *"um agente para tá
    ali no meio"* (interpret, validate, write) for external events into the matriz.
    **That is A1 + A2 in miniature, with demand pull behind it.** Added as a pro to
    [[Comparing the first-agent candidates]] and as a third reason in the one-pager.
  - **Orca is not observable yet** — nothing deployed as of 2026-08-17. Strengthens
    the A5 con from "unscheduled" to "not observable".
- **Contradiction flagged, not resolved**: [[2026-08-14 Papo de Projetos]] has Orca in
  production and indispensable (~10 headcount); the weekly says nothing is deployed.
  May be different scopes; nobody has said so. Marked as needing resolution before
  Orca is used as A5's value story.
- Also added the two in-house agent precedents (AI status readout; Luís's unattended
  ultracode run on Farol) to the cross-cutting facts.
- **Broken wikilink worth creating**: the weekly says the AI status readout is "filed as
  its own page" as [[AI status reporting on Linear]] — **that page does not exist.**
  It is the only in-house evidence about a running agent, so it is worth writing.
- Updated: index.md, meetings/Meeting prep - first agent decision.md,
  syntheses/Comparing the first-agent candidates.md

## [2026-08-18] ingest | Weekly - Projetos e Tarefas (2026-08-17)

- Read `raw/Weekly - Projetos e Tarefas  - 2026_08_17 14_03 GMT-03_00 - Anotações do
  Gemini.txt` in full (271 lines, 38 min). Date from the content header `ago. 17,
  2026`, corroborated by the filename — no filesystem fallback needed.
- New: meetings/2026-08-17 Weekly - Projetos e Tarefas.md,
  concepts/AI status reporting on Linear.md
- Updated: index.md, projects/Airtable Proxy.md, projects/Agent Flow.md,
  projects/Pulse.md, decisions/2026-08-14 Migrate project management from Jira to
  Linear.md, meetings/2026-08-14 Papo de Projetos.md,
  meetings/2026-08-14 Recap da Semana.md,
  syntheses/Which agent should be built first.md,
  syntheses/What should the Agent Flow research phase study.md

> [!warning] Bookkeeping anomaly — the meeting page landed in someone else's commit
> The meeting page was written, then this session was interrupted (machine suspended).
> While it sat untracked, a **parallel session committed** `eca19f4`
> *"synthesis: one-pager for the first-agent decision meeting"*, which swept the
> 323-line meeting page into that commit. Its log entry and commit message describe
> only the one-pager, so for a period the page was **committed, unlogged and
> unindexed** — the invisible-page state the schema forbids. That session then read
> the page and integrated it into `Comparing the first-agent candidates` and `index.md`
> on its own. **History not rewritten**; recorded here instead. Practical lesson: an
> ingest interrupted mid-flight leaves untracked files that another session will
> commit under the wrong message.

- **A fourth meeting type**, unknown to the wiki: a Monday Weekly that overlaps the
  Friday Recap almost exactly. Added to the rhythm table with the overlap flagged as
  an open question.
- **New concept page — the *farol*.** An AI reads the area's Linear board and produces
  the status readout the team walks weekly. Filed because it is **the only agent-like
  system running in the area**, hence the only non-paper evidence about [[Agent Flow]].
  Four accepted insights (including a good one: slow projects have *no formally late
  tasks*, so delay metrics never fire) against two distinct failures — mechanical
  subtask blindness (**6% reported vs 20–25% actual**) and reasoning past its evidence
  (wrongly calling Orca remediation; Gabrielle: *"ele não tem o contexto"*).
- **Directly actionable**: msilva is about to restructure the proxy issues in Linear,
  and that readout is what Gabrielle and Luís see between conversations. Recorded on
  three pages: **flat issues are legible, deep subtask trees are invisible.** Plus
  Luís's `Triagem`-column convention as the complement.
- **Resolved** on [[2026-08-14 Migrate project management from Jira to Linear]]: the
  `AIRTABLEGC` board is **drained wholesale**, not abandoned. Also *why* the proxy was
  in Jira — Luís was trialling tools (*"o rebelde contra o Arthur"*) and deliberately
  left it un-migrated; msilva inherited the tasks on the losing board.
- **Resolved** on [[Pulse]]: the immersion week **did not happen**. Approval failed
  (Ribon on leave; Zoca and Cristian wanted his sign-off together), DOR was stood down
  though it offered to come, approval escalates a level, **delay ≥1 month, Copinha
  window lost**. Status moved `active` → `deferred`.
- **Resolved** on [[2026-08-14 Papo de Projetos]]: Farol's ownership grey zone —
  **split by layer**, data side this team, everything else finance. A third option the
  governance framing didn't anticipate. Recorded the contradiction it creates with
  that page's "built by the finance team themselves".
- **New candidate use case, unprompted by the architecture**: the **matriz
  external-events gateway**. Osmar's Vercel app must write to the matriz, nobody will
  grant him access, and the room proposed *"criar um agente para tá ali no meio"* to
  interpret and validate — A1 + A2 in miniature with a named requester. Carries a
  non-technical blocker: **nobody knows who approves**. Filed on [[Agent Flow]] and
  added to the Gabrielle list; **not** substituted for A1 + A2.
- **Ultra Code** recorded as prior art: the tech lead runs 4–5 hour unattended agent
  workflows and reported a clear win. Noted as the token-cost constraint asked from
  the other end — his open question (*when* to use it) is the same question.
- Two proxy consumers nobody in the room connected to the project: **Fronte wants a
  test suite isolated from Airtable** (deciding that week), and the matriz flow wants
  **attribution on writes** — the problem `X-App-Id` solves for reads.
- **Time-critical**: everyone was asked to check their own Hub entry and report
  corrections **by 2026-08-18** — today. Second time msilva has been asked, and the
  cheapest first read of `Brain`.
- Kept as landmarks only, per the standing scope instruction: Arthur's central-de-dados
  work, the thumbnail generator handover, Yasmin's audience-monitor documentation,
  João Victor's CRM onboarding contract, Orca's schedule. One generalized: **a
  production dashboard died with a departed employee's account** — single-account
  ownership is a live failure mode here.
- Deferred deliberately, offered and declined for now: creating `people/` pages. This
  transcript adds ~20 names with real ownership attached and the folder is still empty;
  a prior `lint` already flagged that who-owns-what is accumulating in meeting pages.
- New open: do both weekly status meetings persist? Who approves external event
  creation? Is the *farol* fixable — and is it a target msilva could take? Should
  msilva be in the TES trial group (he is not, and vendor feedback is due while
  Gabrielle is away)? Is [[Pulse]] the 2.0 of the *Fronte de Negócios* Luís is
  stabilizing — recorded `(unverified)`, one question settles it.

## [2026-08-18] ingest | 1:1 Matheus / Gabrielle (2026-08-18)

- Read `raw/Matheus _ Gabrielle - 2026_08_18 11_04 GMT-03_00 - Anotações do Gemini.md`
- New: `meetings/2026-08-18 1-1 Matheus - Gabrielle.md`,
  `concepts/Linear Project Structure.md`, `projects/Farol.md`,
  `decisions/2026-08-18 Product feedback in Linear, code review in Git.md`,
  `decisions/2026-08-18 Save n8n execution logs for audit.md`
- Updated: `concepts/AI status reporting on Linear.md`,
  `concepts/Packaging as skills.md`,
  `decisions/2026-08-14 Migrate project management from Jira to Linear.md`,
  `projects/Airtable Proxy.md`, `projects/Agent Flow.md`, `projects/Pulse.md`,
  `meetings/2026-08-17 Weekly - Projetos e Tarefas.md`,
  `meetings/2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow.md`,
  `syntheses/What should the Agent Flow research phase study.md`, `index.md`

**Source quality is a first for this vault.** The raw file is a `.md` containing
**only Gemini's generated blocks** — resumo / próximas etapas / detalhes — with the
transcript itself left behind a Google Docs link. So there are **no verbatim quotes
on any page from this ingest, and there cannot be.** Flagged prominently on the
meeting page; every downstream claim is Gemini's paraphrase and is weaker evidence
than the quoted transcripts. Gemini also writes **"Liner"** throughout, meaning
Linear — confirmed by content (milestones, initiatives, teams, ~250-issue cap,
Business trial), not assumed.

- **A naming error the wiki had been propagating: the status readout is not "o farol".**
  [[AI status reporting on Linear]] was filed 2026-08-17 with *farol* as its alias.
  Re-reading the raw weekly, **every** occurrence of *farol* is the **project**
  [[Farol]] — including the *no deliveries* row, which is Farol's row **in** the
  readout. Gabrielle describing Projeto Farol directly is what surfaced it. Corrected
  inline on the concept page, the weekly, and `index.md`; the alias is removed. **The
  readout still has no recorded name** — nobody named it, which is its own small
  finding about the only agent-like system running in the area.
- **New page — [[Farol]].** Referenced as a landmark across five meetings and never
  filed. Gabrielle's description is the first coherent one: consolidates several
  platforms' APIs into one database (travel data for finance), Yasmin collaborating,
  currently bronze. **V2 = bronze/prata/ouro layers plus a conversational AI layer** —
  the area's second user-facing AI system, and better-conditioned than the first
  because the data is modelled before the model sees it. Nobody has connected it to
  [[Agent Flow]], and it may be the closest thing to real demand for msilva's work.
- **New page — [[Linear Project Structure]].** The schema-level answer to a question
  three meetings had left open: **initiative = the product or solution, project = a
  specific segment of value delivery, milestones group deliveries.** The **Airtable
  governance** initiative holds three projects — the [[Airtable Proxy]], *expansion to
  other applications*, and a **Livemode Data Hub** (never previously mentioned
  anywhere).
- **The team's answer to the 6% readout failure: restructure the board, not the
  agent.** `Evolução do Front-End` was split from `Estabilização` for a truer
  completion percentage. Recorded on three pages, with the limit stated plainly — it
  fixes the *category* confusion and touches **neither** subtask blindness **nor**
  reasoning past the evidence. Two of three problems untouched; the board is treated
  as adjustable and the agent as fixed.
- **The proxy is one of three projects, not the programme.** Organizational
  confirmation of the company-wide framing that had until now been only msilva's own
  words to the team. Also gives the migration a concrete scope, and raises whether
  Phase 2 (LiveScript data out of Airtable) belongs to a sibling project.
- **Two decisions filed.** *Product feedback in Linear, code review in Git* — review
  split by **kind**, not stage, with a product validator who tests in the interface
  and can return the task via comments. This is what makes
  [[2026-08-14 No mandatory PR review while the proxy is pre-production]] defensible
  rather than reckless: the technical gate relaxed, a product gate formalized.
  *Save n8n execution logs for audit* — resolves the traceability blocker
  [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] recorded twice.
  The load-bearing detail: **saving production runs, not only failures**, because the
  expensive runs *succeeded* — they produced a wrong number at $7. Failures-only would
  have missed every one.
- **Corrected on [[2026-08-14 Migrate project management from Jira to Linear]]:** the
  trial **suspends** the 250-issue cap, it does not remove it. That page said removed.
- **Found by putting two dates side by side, not stated in any meeting:** the Linear
  Business trial expires **2026-09-09**; Gabrielle returns **2026-09-10**. The trial
  lapses the day before she is back, while msilva is being asked to move the
  `AIRTABLEGC` backlog into that workspace — the cap being what postponed the
  migration originally. No purchase recorded, and the person who can authorize it
  leaves 2026-08-24. Raised to a callout on `index.md`.
- **Infrastructure [[Agent Flow]] may not need to build**, all already in Linear: a
  **Slack delivery channel** per project (relevant to A2 and to A5's notification
  design), a **human approval gate with a return path**, and **teams as the
  access-control boundary**. Plus Luís's in-house **Markdown ticket templates** for
  bugs/spikes/epics — until now the A2 output contract was modelled only on Packer's
  external template. **Read the in-house ones**; that question is answerable without
  asking anyone.
- **Weakened, not resolved**, on [[Pulse]]: two front-end tracks now visibly coexist,
  one evolving and one being stabilized, which is the shape the "is Pulse *Fronte de
  Negócios 2.0*?" question predicts. It does not confirm the lineage. Still one
  question.
- **Narrowed on [[Packaging as skills]]:** "a shared runtime crowds others' logs out of
  view" overstated it — some of that history did not exist to be crowded. Stuck locks
  and absent per-user filtering are genuine tenancy problems; observability on a shared
  runtime turns out to be **configurable**.
- **Deliberately left ambiguous rather than guessed:** the **"Tech" vs "Humans"
  accounts.** Tech has daily/monthly caps and sees every flow in the company; Humans
  has a larger token allowance and an upgrade path. **n8n** fits the flow-visibility and
  poor-per-user-filtering details; **the Claude org account** fits the tokens and
  upgrade path. Gemini conflates them and the notes do not separate them. Recorded as
  an open question on the meeting page and on [[Agent Flow]], because it decides where
  the token-measurement instrument actually lives — the thing the entire token-cost
  constraint depends on. Same question also disconfirms or confirms the n8n reading on
  the logging decision.
- **Five questions added to the pre-2026-08-24 list**, since this was the last
  scheduled 1:1 before her leave: the account question; which initiative Agent Flow
  belongs to (it has no home, which decides whether the research is visible to the
  weekly readout at all); whether treating the readout as unfixable is considered or
  just the only available lever; whether the in-house templates work as an agent output
  contract; and whether the Business plan is being bought.
- **Still unanswered after three meetings:** the Linear team/project identifier and the
  new issue-key shape. Gabrielle has an open action to send the project link, which
  settles it.
- **`people/` still empty, deliberately.** This source names Yasmin, Luís and a product
  validator role with real ownership attached, and the standing offer to create the
  folder remains declined.

## [2026-08-18] ingest | 1:1 Matheus / Gabrielle (2026-08-18) — re-ingest against the real transcript

- msilva replaced `raw/Matheus _ Gabrielle - 2026_08_18 11_04 GMT-03_00 - Anotações do Gemini.md`
  with the **actual transcript**. The earlier ingest, two entries above, was written from
  a version of that file containing **only Gemini's summary blocks**. Re-read in full and
  the fan-out redone.
- Rewritten: `meetings/2026-08-18 1-1 Matheus - Gabrielle.md` (from scratch, with quotes)
- Updated: `concepts/AI status reporting on Linear.md`,
  `concepts/Linear Project Structure.md`,
  `decisions/2026-08-18 Product feedback in Linear, code review in Git.md` (**demoted**),
  `decisions/2026-08-18 Save n8n execution logs for audit.md`,
  `projects/Airtable Proxy.md`, `projects/Agent Flow.md`, `projects/Farol.md`,
  `meetings/2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow.md`,
  `syntheses/What should the Agent Flow research phase study.md`, `index.md`

**The rewrite was not cosmetic — the summary had omitted load-bearing material and
misrepresented one thing outright.** Recording what changed, because it is the clearest
evidence yet about what Gemini's summaries are worth.

### Retracted

- **The $7-vs-11-centavos comparison is not clean.** *"Ontem ele trocou o provider, ele
  tava usando [An]tropic[,] que trocou pro GPT[,] e o fluxo dele funcionou, ele falou que
  foi a primeira vez que funcionou."* **Gabriel changed LLM provider between the two
  runs.** The summary omitted this entirely. Gabrielle names the confound herself.
  *"Same flow, same day, therefore cost is intermittent"* — the wiki's most-propagated
  inference — loses its evidence. The loop hypothesis survives on two people's
  independent judgement, not on data. Retraction filed on the CazéTV page and echoed on
  [[Agent Flow]] and the research synthesis.
- **The product-feedback split is not a decision.** The summary said feedback *"foi
  centralizado exclusivamente na plataforma"*. The transcript: *"não tem um o o martelo
  martelo batido de qual que é o melhor fluxo."* Page **demoted to `status: draft`,
  `decided_by: nobody`**, kept because the *practice* is real and five pages link to it.
  Whether it belongs in `decisions/` at all is msilva's call — flagged, not moved.

### Answered

- **Agent Flow gets its own Linear initiative, one project per agent** — Gabrielle,
  hedged three times with *talvez* but it is her answer. Lines up with *anarchic-first*:
  a project is a segment that stands alone, which is what each agent is meant to be.
- **The status readout's mechanism.** Not a Linear feature and not a vendor tool: an
  **in-house board Gabrielle controls** (*"nosso repórter"*) that pulls in-progress
  projects from Linear. **The subtask blindness is deliberate** — *"ele traz a nível de
  [issue], ele não traz a nível de sub[issue], que é para poder também não ficar tão
  confuso"* — and **drill-down is already planned.** So it is a design trade-off with an
  owner and a plan, not a defect of unknown origin. Reclassified on that page; the
  "would msilva fix it?" suggestion is correspondingly weaker.
- **"Tech" vs "Humans" resolves as *both*** — the ambiguity the previous ingest declined
  to guess at was two different tools sharing a naming convention. **Claude plans**: tech
  is limit-based (daily/monthly, no session windows), *humanos* is seat-based Pro/Max
  with more tokens and an upgrade Carol had already requested for msilva. **n8n**: the
  tech login sees every company flow with per-execution token counts, searchable by name
  only, sorted by recency — which is also the mechanism behind Gabriel's runs being
  pushed down the list.
- **The n8n defaults, precisely.** Failed executions **are** saved by default; successful
  production ones are not. So the earlier "logging was off" was too broad, and it
  explains the symptom that baffled both of them — *"tava running, aí quando terminava
  sumia"*. A run vanishing **on completion** is that default working as designed. It also
  answers "was it turned off?": no. The flow started **succeeding**.
- **Gabrielle is the product reviewer.** Not an unnamed role. She tests in the interface,
  against **homologation** rather than production, and approves/comments/returns. Luís
  reviews Yasmin's work in Git instead, because he is checking implementation not
  behaviour — so the split is two reviewers with different questions, not a policy.

### New, and operationally the most useful

- **The n8n citizen flows are meant to go behind the proxy.** Unprompted, at 00:28:09:
  *"a ideia é esses projetinhos eles passarem pelo proxy também […] todos que use[m
  Air]table[,] idealmente sim. Só que o problema é a gente não tem noção de quantos são e
  cada pessoa tem sua própria chave."* An entire consumer class the wiki had not
  recorded — many small flows, non-engineer authors, **no registry**, and **each author
  holding their own Airtable PAT**. This validates *centralizing key distribution* as the
  active task from an unexpected direction: the five named systems could be onboarded
  without it; this population cannot. Also the strongest utility argument yet for A5 —
  with the ordering caveat that nothing can be measured until the flows are behind the
  proxy, and they cannot get there until key distribution exists. **Discovery is
  unowned.**
- **Claude already authors this area's Linear backlogs.** *"eu peço para ele jogar tudo lá
  pro [Linear] […] ele próprio já cria aqui os milestones e aí […] dentro dos milestones
  vai subir [issues]. Enfim, ele cria as tarefas sozinho."* Documentation and PRDs in;
  project description, milestones, issues and subissues out, using Luís's templates.
  **This corrects the wiki's claim that the status readout was the only agent-like system
  running here** — a *working* one was in plain sight and nobody described it, because it
  is just how Gabrielle makes tickets. Two consequences: msilva's migration action item
  has an established method (hand `airtable-proxy-design.md` to Claude), and **A2's hard
  part is not authoring** — it is classification and routing, plus A1's intake breadth.
- **The budget anxiety was partly a misread mechanism, and leadership authorized the
  spend.** No hard ~$20 ceiling: IT tops up a company-wide workspace weekly and each
  person consumes against their own key. Gabrielle to Gabriel: *"durante esse período de
  teste você vai gastar um pouco mais e […] a gente tá OK com isso, sua liderança tá OK
  com isso, até você estabilizar realmente o fluxo."* Softens the token constraint on
  [[Agent Flow]] from a gate to a goal.
- **The second restructuring fix, which the summary had reduced to one.** Besides
  splitting evolution out of the stabilization project, they **broke up a single
  milestone** holding all stabilization tasks, because **Carol** could not see where the
  work stood. Regrouped 2026-08-17. That is the sharper lesson: **milestone granularity
  is the reporting instrument** — milestones are the level the board *does* count, so
  several scoped ones make partial progress visible without any nesting. Promoted to the
  first of three conventions for msilva.
- **The data hub is probably the wiki's missing Phase 2.** *"Banco de dados
  intermediári[o] […] Eu acho que é a ideia de ir migrando do air table. Pode ser. Sei.
  Eu acho que é isso. Deve ser."* Four hedges, relayed second-hand from Luís. If right,
  [[Airtable Proxy]]'s note that Phase 2 is *absent from the roadmap* is wrong — it is a
  sibling project. Recorded as this vault's inference. **Ask Luís, not Gabrielle.**
- **msilva had already hit the Linear free cap**: *"você tomou um rate limit, né? […] não
  podia mais criar."* The trial deadline stops being abstract. He committed to migrating
  everything off Jira **today**.
- **The conventions are explicitly provisional and he is invited to change them**:
  *"não tem muito também um certo nem um errado […] pode ficar à vontade[,] cara[,] [se]
  ter algum insi[ght] […] de pegar e de mudar."*
- **Luís is rewriting his own templates because he dislikes them** — *"vai também depois
  perguntar a ele o que que ele achou que tá ficando ruim."* So "read the in-house
  templates" was half an answer; **his diagnosis of why they fail in use is the better
  input** to A2's output contract. Recorded on three pages.
- **A4 Teacher's operating pattern, from the person who owns the role**: *"tu ajudou ele
  destravar e ele vai andar sozinho […] se ele travar em algum momento, ele procura a
  gente de novo e a gente ajuda[,] ajuda pontual."* Unblock, step back, help on demand —
  tighter and cheaper than the instruction's "diagnose maturity, generate tutorials,
  follow execution in real time", with a clean success criterion: the person proceeds
  without you.
- **Also new**: initiatives cut across teams (so team isolation scopes access, not
  rollup); Linear has a native description-improving agent; a **proxy Slack channel
  already exists** and the integration was offered for it specifically; project updates
  are hand-written weekly by Gabrielle; subissue-to-PR is not 1:1; and **Linear comments
  never reach Git**, so review history genuinely lives in two unsynced places.

### Lesson about the sources themselves

Gemini's summary blocks are **not a substitute for the transcript**. Across this one file
they omitted a provider switch that invalidates a cross-page inference, omitted an entire
proxy consumer class, omitted a running agent workflow, collapsed two structural fixes
into one, and **inverted a "nothing is decided" into a decision**. Everything they *did*
report was directionally right, which is what makes them dangerous: the failure mode is
confident omission, not visible error. **Prefer transcripts; when only a summary exists,
say so as loudly as the first version of the meeting page did.**

Transcript attribution is also collapsed here — every line is credited to Gabrielle,
including msilva's own questions and his commitment to migrate. Third file with this
defect, and the second where it runs in this direction.

## [2026-08-18] lint | full health check, then six fixes

### Clean

- **0 broken wikilinks** across 40 pages. **0 orphans** — every page has at least one
  inbound link excluding `log.md`. **`raw/` fully covered** — all 12 sources have a
  `sources/` or `meetings/` page. **0 pages stale by the 30-day rule** — the vault is 8
  days old, so that rule cannot fire yet; the oldest `active` page is
  [[Zed Cheatsheet]] at 7 days. **No decision marked open in one page and settled in
  another.**
- **The credential warning on `index.md` is accurate.** Verified by walking every commit:
  exactly **6** contain the Airtable PAT / Firebase key. Unchanged, because the working
  tree is clean and history does not grow. **Rotation is still outstanding.**
- Non-issues confirmed as such: `log.md:17`'s *"raw/ … currently empty"* is historical in
  an append-only file, not a stale claim; the `raw/2026-06-14-Papo de Projetos`
  misnaming is already flagged in-wiki.

### The pattern behind most of the findings

**Four of the six were retraction debt created earlier the same day.** The 2026-08-18
re-ingest corrected pages at the point where the new evidence landed and left older
affirmations of the same claim standing elsewhere — on the same page in two cases, and
two hops away in the graph in two more. Three were introduced within the hour.

**Habit to adopt: after a retraction, grep the retracted phrasing across the vault before
committing.** A retraction that only lands where the evidence arrived is not a retraction;
it is a second, contradictory claim.

### Fixed

1. **The $7-vs-11-centavos retraction now propagates.** Three pages still asserted it
   flatly. [[Comparing the first-agent candidates]] — the decision doc for a meeting in
   ~3 days — read *"the scary number was a bad case […] traced to a probable runaway
   execution, not context volume"*; rewritten to say the cause is **genuinely unknown**,
   that both levers are therefore worth building in, and that leadership has accepted
   higher spend during stabilization. [[Packaging as skills]] and [[Agent Flow]]: the
   asserting sentences struck in place rather than deleted, with the structural point each
   page actually needed preserved — for packaging, that it addresses a fixed context
   premium and never a broken trigger chain, *whichever* dominates the bill. Also struck
   the original claim on the source page,
   [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]], since it is what
   three other pages inherited.
2. **[[Meeting prep - first agent decision]] brought current** — the highest-stakes
   finding, since the meeting is ~2026-08-21. Three inputs from the re-ingest were
   missing. Recommendation reason 1 now carries **two** A5 blockers rather than one (the
   n8n flows need key distribution *and* a discovery step nobody owns); a fourth reason
   was added — **A2's ticket-authoring is already solved in-house, making A1+A2 the
   smallest of the four builds, not the largest**; and question 6 (*"can agent output file
   into Linear?"*) was **struck as answered** and replaced with **who owns finding the
   n8n flows** — a real prerequisite with no owner. Question 2 now notes that Gabrielle's
   own-initiative/one-project-per-agent suggestion is structurally anarchic-first, which
   cuts against starting at intake.
3. **Intra-page contradiction on [[Airtable Proxy]]** — the new callout said Phase 2 *"is
   not absent, it is a sibling project"* while a line below still bold-asserted *"that
   phase is absent from the roadmap."* Struck and reconciled: absent from *this project's*
   roadmap is now correct, because it belongs to the data hub.
4. **Intra-page contradiction on [[AI status reporting on Linear]]** — *"the first is
   deterministic and fixable by summing differently"* survived the reclassification of
   subtask blindness as a deliberate aggregation level. Restated, and the restatement
   produced something better than a patch: **the asymmetry between the two failure classes
   is the finding.** One has an owner, a cause and a plan; the other — reasoning past the
   evidence — has none of those, nobody in the meeting treated it as a problem, and it is
   the real design burden for [[Agent Flow]].
   Also refined *"A1 and A2 would inherit the subtask blindness directly"*: they inherit
   the **parent/subissue ambiguity**, which is in the data, but not the **choice**, which
   was made for a human skimming a weekly readout. An intake agent has a different
   audience and should decide for itself. The transferable lesson is that the choice exists
   and is worth 6% against 20–25% when made wrong for the purpose.
5. **Cross-page inconsistency on the n8n logs.** The *"executions crowd each other's logs
   out of view"* overstatement was narrowed on [[Packaging as skills]] during the re-ingest
   but left standing on [[Agent Flow]]. Both now say the same thing: successful production
   executions **were never saved**, so for those runs the history never existed to be
   crowded out; per-user filtering (searchable by name only, sorted by recency) is the
   genuine tenancy defect.
6. **Stale count** on [[Comparing the first-agent candidates]]: *"two in-house agent
   precedents"* → **three**, the new one being Claude authoring the area's Linear backlogs
   — and the most relevant of the three to the decision that page exists to inform.

### Also strengthened while in there, not just corrected

- A5's candidate section on [[Comparing the first-agent candidates]] gained both halves of
  the n8n finding: it is **A5's strongest utility argument** (an unknown number of flows
  written by people who, on the Gabriel evidence, do not know what their automations cost
  or whether they loop — the population most likely to generate anti-patterns and the one
  with nobody watching it) **and its worst dating problem** (two unscheduled prerequisites
  instead of one). Recording both under Pros and Cons rather than picking, because the
  same fact genuinely does both.

### Not fixed, reported only

- **[[Zed Cheatsheet]] is the only dead-end page** — 0 outbound links, 1 inbound. Arguably
  correct for a keybindings reference; left alone.
- **`people/` is still empty.** Offered and declined twice. Every ingest since 2026-08-14
  has added named ownership facts that now live in meeting pages instead. Noting, not
  acting.

## [2026-08-18] ingest | orca-produto repo access — Orca resolved
- msilva gained read access to `orca-produto`
  (`C:\Users\msilva\projects\orca-produto`), a planning workspace (Claude Code/Cursor)
  for the next version of Livemode's Orca — not the product's code, which lives at
  `livemode-org/livemode-comprovante-de-entregas`.
- Read in full: `README.md`, `PROJETO.md`, `project/DISCOVERY.md`, `project/ROADMAP.md`,
  `assistant/decisions/2026-08-07-retorno-stakeholders-28-itens.md`,
  `assistant/meetings/2026-07-17-orca-proximos-passos.md`, `assistant/actions/TASKS.md`.
- **New: `systems/Orca (CDE).md`** — Orca is Livemode's name for the CDE (Comprovante
  de Entregas), a computer-vision/audio system proving commercial-insertion display in
  live sports broadcasts, in production since v3.2.0 (May–Jul 2026), business-critical
  (~10 headcount worth of automation per [[2026-08-14 Papo de Projetos]]).
- **New: `projects/Orca Next Version.md`** — the roadmap/backlog planning initiative
  itself: 7 milestones (E1–E7), ~28 issues, approved by stakeholders 2026-08-07, Linear
  project "ORCA - Novas Versões". Not msilva's project; tracked for context like
  [[Pulse]] and [[Farol]].
- **Resolves a contradiction five pages had flagged or carried unresolved**: "Orca in
  production and indispensable" (2026-08-14 Papo de Projetos) vs. "Orca: nothing is
  deployed" (2026-08-17 Weekly) are not in conflict — the first is the live v3.2.0
  system, the second is the unshipped next-version roadmap. Confirmed, not inferred.
- Also resolves the older open question on whether Orca is "a real system" or a
  transcription guess ([[2026-08-10 Onboarding Técnico - Matheus]]) — deleted there,
  noted as settled by this ingest.
- Updated: [[Agent Flow]] (3 spots), [[Airtable Proxy]],
  [[Comparing the first-agent candidates]] (contradiction warning closed out),
  [[Which agent should be built first]], [[What should the Agent Flow research phase
  study]], [[Meeting prep - first agent decision]] (4 spots),
  [[2026-08-10 Onboarding Técnico - Matheus]], [[AI status reporting on Linear]],
  [[Gabriel Packer - DAG-driven agent orchestration]], `index.md`.
- Open: is Orca's proxy migration actually sequenced anywhere, or still just intended —
  it isn't mentioned in `Orca Next Version`'s own roadmap. Confirm "Pedrinho" (2026-08-17
  weekly) = Pedro Alves (unverified nickname match). Has the E1 MAM/IVC legal spike
  started?

## [2026-08-18] lint | full health check, then two fixes
- **Links**: zero broken wikilinks. Every `[[...]]` target across all 44 pages resolves
  to a real page, including the two created earlier today ([[Orca (CDE)]],
  [[Orca Next Version]]).
- **Orphans**: none. Every content page has at least one inbound link (checked against
  the full link graph, index.md and log.md links counting as real inbound).
- **Dead-ends**: only [[Zed Cheatsheet]] (0 outbound, 1+ inbound) — already known and
  previously judged correct for a keybindings reference; left alone again.
- **`raw/` coverage**: fully covered. `README.md` is the folder's own instructions, not
  a source; `Novo(a) Documento de Texto.txt` is empty and already tracked as such; both
  CazéTV Gemini exports (15_02 and 15_31) are cited together in one meeting's
  frontmatter; everything else has a 1:1 `sources/` or `meetings/` page.
- **Stale claims (30+ days)**: none — every page's `updated:` falls within
  2026-08-11–2026-08-18, and this vault is 8 days old.
- **Intra-page contradictions** (pages edited 3+ times per `git log --name-only`):
  checked all 19 — 7 already covered earlier today during the Orca ingest, the
  remaining 12 read fresh. Two real findings, both fixed:
  1. **[[Meeting prep - accounting data in Claude - 2026-08-17]]** — frontmatter said
     `updated: 2026-08-14`, but the body has a "Repointed 2026-08-17" callout describing
     an edit made to the page that day. Bumped `updated:` to match.
  2. **[[Proxy Environments]]** — a 2026-08-17 warning said the `AIRTABLE_ENDPOINT_URL`
     switch "must be flipped only after the header change ships," phrased as still
     pending. [[How LiveScript sends the proxy X-App-Id header]] (same day) records that
     change as **shipped** (commits `754896b`, `d565c26`). Reworded to reflect the
     precondition is met.
  All other 17 checked pages: internally consistent, corrections properly flagged inline
  where they exist.
- **Decisions marked open vs. settled elsewhere**: none found. The two candidates
  checked (A5 receiver hosting, n8n-logging blocker) are both already correctly
  cross-referenced as resolved in the pages that once called them open.
- Updated: [[Meeting prep - accounting data in Claude - 2026-08-17]],
  [[Proxy Environments]].

## [2026-08-18] refactor | Jira→Linear migration: discovered already done, reconciled
- msilva asked to plan the `AIRTABLEGC` → Linear migration. Checked Linear directly
  (`list_teams`, `list_projects`, `list_issues`) instead of waiting on Gabrielle's
  promised link, and found the migration substantially already complete —
  most likely an earlier, unlogged Claude Code session run against the design docs
  (Gabrielle's own documented workflow), never recorded in this wiki.
- Team `Projetos-livemode`, initiative **Airtable GC - Governança e Confiabilidade**,
  three projects matching Luís's structure exactly: `Proxy do Airtable` (`PRO-*`),
  `Proxy expandido para outros apps`, `LiveMode Data Hub (POC)`.
- Full 1:1 comparison, all 65 `AIRTABLEGC` issues (via paginated JQL, 5-at-a-time —
  this Jira MCP tool caps page size regardless of `maxResults`) against Linear:
  **100% parity.** 60 issues in `Proxy do Airtable` (across its 4 milestones
  F1/F2/F3/Backlog deferido), the remaining 5 (Data Hub epic + its spikes) in
  `LiveMode Data Hub (POC)`. No issues created — none were missing.
- Found and fixed 3 status-drift cases where Linear hadn't caught up to Jira's actual
  state: `PRO-75`, `PRO-77` moved Backlog → Done; `PRO-79` moved Backlog → In
  Progress (matching Jira's `Em andamento`).
- **msilva flagged a second gap directly**: none of his Jira-assigned issues carried
  an assignee into Linear. Confirmed — all 65 Linear issues were unassigned or
  assigned to a service account (`Tech Projetos`). Fixed by assigning the 8 issues
  Jira had on msilva (`AIRTABLEGC-4,5,6,7,14,34,36,37` → `PRO-60,74,75,77,78,79,84,90`)
  to him in Linear.
- `AIRTABLEGC` itself left untouched — msilva's call, deferred pending a separate
  decision on bulk-transition vs. archive vs. leave-as-is.
- Updated: [[2026-08-14 Migrate project management from Jira to Linear]] (open
  question resolved, migration status corrected), [[Linear Project Structure]] (same
  open question resolved), [[Airtable Proxy]] (current-state callout points at the
  live Linear project), `index.md` (both banners).
- Open: `PRO-79`'s description still says `X-Api-Key` is in scope, but Jira's version
  of the same issue notes it was explicitly deferred (2026-08-14, with Luís) — a small
  content drift, not just a status one. Not fixed this pass; flagged for msilva.
  Also open: how/whether to wind down `AIRTABLEGC`; the Business-trial purchase
  decision ([[Linear Project Structure]]).

## [2026-08-18] ingest | 1:1 Matheus / Luís (2026-08-18, 14:59)

- Source: `raw/Matheus - Luis - 2026_08_18 14_59 GMT-03_00 - Anotações do Gemini.md`.
  Second meeting of the day, after [[2026-08-18 1-1 Matheus - Gabrielle]] (11:04).
  Gemini attributes speakers correctly and consistently (unusual for this vault),
  but turns are heavily fragmented by interruptions. First appearance of Luís's
  surname, **Fernandez**, in a transcript — kept calling him "Luís" everywhere per
  existing convention; no `people/` page created (standing rule, still declined).
- New: `meetings/2026-08-18 1-1 Matheus - Luís.md`,
  `decisions/2026-08-18 Bring options to Luís before deciding, communicate async and often.md`,
  `reference/Claude Code Working Habits.md`
- Updated: `projects/Agent Flow.md`, `syntheses/Which agent should be built first.md`,
  `syntheses/What should the Agent Flow research phase study.md`,
  `projects/Airtable Proxy.md`, `systems/LiveScript.md`,
  `syntheses/How LiveScript sends the proxy X-App-Id header.md`,
  `concepts/Packaging as skills.md`, `index.md`

**The most consequential finding: Luís contradicts Gabrielle's own-morning framing,
same day.** Gabrielle's 1:1 (11:04) reinforced Orca-and-other-services-behind-the-proxy
as A5's strongest utility case. Luís (14:59) argues the opposite — *"eu desassociaria
ele completamente do proxy [...] imagina que ele não funciona com o proxy, não existe,
ele ainda é útil"* — reasoning that scoping the Watcher to the proxy caps its value at
whatever LiveScript alone generates early on. Neither referenced the other. Recorded as
an unresolved conflict per the schema, on [[Agent Flow]],
[[Which agent should be built first]] and [[What should the Agent Flow research phase
study]] — not adjudicated. **Next step, agreed in the call**: msilva runs separate
~30-minute discovery conversations with Gabrielle and Carol, deliberately apart, about
what each actually imagines the Watcher doing, before any more A5 design. This
downgrades confidence in the existing detailed A5 build plan
([[How to implement A5 Watcher]]) — its mechanism doesn't assume the proxy specifically,
but its stated motivation does, and that motivation is exactly what Luís wants set aside.

**Second finding: a decision already shipped without being brought as a decision.**
The `pnpm patch` fix for the LiveScript SDK header
([[How LiveScript sends the proxy X-App-Id header]], shipped 2026-08-17) was msilva's
own call, made to unblock validating the proxy. Hearing the detail for the first time,
Luís both distrusts the mechanism (*"eu confio zero nisso"*, calls `pnpm patch`/
`patch-package` bad JS-community practice) and objects to the process — he wasn't given
alternatives to choose between. **New process norm agreed**: bring compared options
before acting on a technical decision; communicate async and often rather than saving
things for scheduled 1:1s. Filed as
[[2026-08-18 Bring options to Luís before deciding, communicate async and often]].
Not a reversal of the patch — an open question to revisit with alternatives.

**Scope clarified**: msilva's job is the [[Airtable Proxy]] working correctly, not
migrating LiveScript's own Airtable usage (SDK-routed calls explicitly deferred,
REST-transport validation is enough for now). Partial migration is an accepted
intermediate state, not a gap — *"a gente não quer sair de zero para 100%."* Also
resolved: a caching concern msilva raised about LiveScript's events page turned out to
be intentional design (data changes rarely), not a defect masking proxy issues; the
real-time script editor is the correct place to test against.

**Progress reported**: the operation-detection bug (previously inferred from HTTP
method alone, wrong for cases like an Airtable `select` via `POST`) is fixed; page
size and byte counts are captured; `X-App-Id` confirmed working end-to-end in the dev
environment. Updated the open checklist item on [[Airtable Proxy]].

**No Linear-skills standard exists yet.** msilva had started from an Orca-derived skill
set; at least three divergent versions exist (Gabrielle's, msilva's, and Luís's own
evolved one living in the "Fronte de Negócios" repo, not anywhere shared). **A real
team-wide skills/plugins repo is being built by Carol** (named `livemode`), and Luís is
now starting to help her with it. No standard is expected yet — explicitly fine for
now. New angle on [[Packaging as skills]]: which skills are team-generic versus
project-bound is itself unsettled, separate from whether any single skill works.

**Tooling feedback, filed as a new reference page**: Claude Code's verbosity (relative
to msilva's prior GPT/Cursor experience) is fixable by pinning explicit instructions
into the project the first time an unwanted behavior appears — demonstrated live by
Luís adding a "never generate artifacts without asking" instruction. A field
application of the "instructions in files, not memory" lesson already on
[[Agent Flow]]. Also recommended: VS Code + the Claude Code extension over
browser-only use. New: `reference/Claude Code Working Habits.md`.

**Machine/security reopened and reframed**: the real risk of using a personal machine
for work is physical (loss/theft while commuting), not informational leakage as
originally framed by Gabrielle. msilva keeps using the work laptop; not urgent to
revisit.

- Excluded per standing instruction: a brief personal-health remark at the start of
  the call (msilva feeling unwell). No org-structure, hiring, or departure content in
  this transcript.
- Open, low confidence: a "Pedro" mentioned once as the possible author of an
  alternative first-agent design mockup Carol was asked to review — first mention of
  this name in the vault; not investigated this pass.

## [2026-08-18] refactor | X-App-Id SDK patch reverted; options doc for Luís
- In `livemode-roteiros-nextjs`, at msilva's direction: `git reset --hard c9cc711`
  on `feature/airtable-proxy-observability` dropped commits `754896b`/`d565c26`
  (the `pnpm patch` on `airtable@0.12.2` + REST header injection), then
  `pnpm install` to match. Not pushed, so purely local; branch then fast-forwarded
  to `origin` (Luís's unrelated `.env.example` docs commit).
- Reason: bring Luís real alternatives instead of a shipped fait accompli, per
  [[2026-08-18 Bring options to Luís before deciding, communicate async and often]].
- Verified in code (not just re-asserted from the prior investigation): installed
  `airtable@0.12.2` still routes `select/find/create/update/destroy` through
  `runAction`, which still ignores `customHeaders`; `npm view airtable dist-tags`
  still reports `latest: 0.12.2` (no upgrade path exists).
- Evaluated Luís's own suggestion (screenshot: `customHeaders` on the SDK
  constructor, no patch) — confirmed insufficient alone, same reason. Also scoped
  a new option (migrate the remaining SDK calls to hand-rolled REST): ~13 service
  files, several dozen call sites.
- Updated: `syntheses/How LiveScript sends the proxy X-App-Id header.md` (now a
  5-option comparison for Luís, "implemented" status corrected to
  "reverted"), `systems/LiveScript.md`, `projects/Airtable Proxy.md` (both had
  stale "implemented"/"confirmed working" claims, corrected inline per schema).
- Open: which option Luís picks; delivery channel to actually show him the
  comparison not yet decided.

## [2026-08-18] refactor | Open people/ — 23 pages, role and ownership only
- msilva asked to add people to the wiki, reversing the standing decision to
  leave the folder empty (offered and declined three times: 2026-08-17,
  2026-08-18 x2). Scoped with him first: **everyone named** across the vault,
  **role and ownership only** — no opinions, no sensitive transcript content.
- New: 23 pages under `people/` — [[Gabrielle Ferreira]], [[Luís Fernandez]],
  [[Carolina Bezerra]], [[Yasmin Macedo]], [[Arthur Tavares]],
  [[João Victor Andrade]], [[Michelly Magalhães]], [[Maria Fernanda Lemos]],
  [[Gabriel (CazéTV automation)]], [[Gabriel (matriz de eventos, departed)]],
  [[Osmar]], [[Nina]], [[Diego]], [[Bianca (Bia)]], [[Kauan]],
  [[Pedro Alves]], [[Yuri Muanes]], [[Lucas Gomes]], [[Júlia]], [[Sérgio]],
  [[Ribon]], [[Zoca]], [[Cristian]].
- **Two same-first-name Gabriels disambiguated as separate pages**, cross-linked
  each way rather than merged: the CazéTV n8n colleague versus the departed
  colleague who built the matriz de eventos. Confirmed distinct from context
  (one active 2026-08-17, the other described as already gone on 2026-08-14).
- **"Gabi" resolved as an alias on [[Gabrielle Ferreira]]**, not a separate
  page — corroborated by Luís's *"eu que mandei pra Gabi"* — but the
  2026-08-10 onboarding transcript's *"a second colleague, referred to as
  Gabi"* is flagged unresolved rather than silently merged, since it reads as
  a distinct attendee.
- **Found and corrected a defect while sourcing [[Pedro Alves]]**: **[[Fluxo
  Agêntico diagram]]** asserted *"Pedro (the departed developer) co-designed
  the architecture"*, citing [[2026-08-10 Onboarding Técnico - Matheus]] —
  but that meeting page names no Pedro and no departed developer. Citation
  didn't check out. Removed rather than merged into a fabricated person page;
  correction left visible on the source page with a pointer to re-verify
  against `raw/` before restoring it.
- **msilva has no page of his own** — the vault already is the record of his
  work; noted explicitly on `index.md` rather than left silently absent.
- Deliberately excluded per every relevant meeting page's standing scope
  instruction: org chart, hiring, DISC profiles, role transitions, personal
  remarks. Pages carry only what was already recorded elsewhere in the wiki
  as project-relevant ownership.
- Two attendee-only pages ([[Michelly Magalhães]], [[Maria Fernanda Lemos]])
  carry no role — say so rather than inventing one.
- Updated: `index.md` (new People section, replacing the "empty
  deliberately" note), `sources/Fluxo Agêntico diagram.md` (correction above).

> [!warning] Bookkeeping anomaly — the index.md edit landed in someone else's commit
> Same failure class as the two prior instances this log already records. A parallel
> session committed `101137b` ("revert X-App-Id SDK patch...") while this operation was
> in progress, and its snapshot of `index.md` already carried this session's new
> **People** section — so the section is real and correct, but it is attributed to a
> commit message that never mentions people/ at all. History not rewritten; recorded
> here instead. The 23 new `people/*.md` files, this log entry, and the
> `Fluxo Agêntico diagram.md` correction were made after that commit and are committed
> separately, on top of it.
- Not touched: three files already modified on disk from the prior, separate
  SDK-patch-revert operation (`projects/Airtable Proxy.md`,
  `systems/LiveScript.md`,
  `syntheses/How LiveScript sends the proxy X-App-Id header.md`) — pending
  commit from that operation, left alone as out of scope for this one.
- Open: does msilva want [[Pedro Alves]] and the unrelated "Pedro" mockup
  mention investigated further with Carol? Does "Gabi" as a second onboarding
  attendee need resolving, or is it transcription noise?

## [2026-08-18] lint | Seventh health check, then two fixes

- **Links**: after normalizing soft line-wraps inside `[[...]]` (long link text
  wrapping across two source lines, common throughout this vault and never
  actually broken in rendering), **one genuine broken link** found:
  `sources/Fluxo Agêntico diagram.md` linked `[[people/]]` — a folder, not a
  page — introduced by this session's own edit two entries up. **Fixed**:
  de-linked to plain text.
- **Orphans**: none, including all 23 new `people/` pages — every one is
  reachable from `index.md`.
- **Dead-ends**: only [[Zed Cheatsheet]], as in every prior lint.
- **`raw/` coverage**: all 14 non-empty source files still have a `sources/`
  or `meetings/` page; `Novo(a) Documento de Texto.txt` still empty and
  already tracked as such.
- **Frontmatter**: all pages carry `type`/`status`/`updated` (or are
  correctly exempt) — the 23 new person pages included.
- **Credentials in git history**: still present, unchanged commit count.
  Seventh report. Rotation still outstanding — msilva's action, not this
  vault's.
- **Re-verified the three pages touched by the not-yet-logged-here SDK-revert
  commit** (`projects/Airtable Proxy.md`, `systems/LiveScript.md`,
  `syntheses/How LiveScript sends the proxy X-App-Id header.md`): the
  patch-shipped-then-reverted fact is stated consistently across all three,
  with no stale "implemented" claims surviving anywhere. Clean.
- **Second real finding, fixed**: [[Bianca (Bia)]] stated the "Bia" =
  "Bianca" nickname match as settled fact. The source
  ([[2026-08-17 Weekly - Projetos e Tarefas]]) only uses both spellings in
  the same paragraph without confirming they're one person — the identical
  evidence class already flagged elsewhere in this vault as unverified
  ("Pedrinho" ↔ [[Pedro Alves]]), just missed on this page when it was
  written. Added the same unverified-inference flag for consistency.
- **Not re-litigated**: `status: draft` pages
  ([[Meeting prep - accounting data in Claude - 2026-08-17]],
  [[2026-08-18 Product feedback in Linear, code review in Git]],
  [[How to implement A5 Watcher]]) — all previously judged correctly draft,
  nothing new changes that.
- **Stale-claims (30-day) rule**: still cannot fire — vault is 8 days old.
- Not checked exhaustively this pass: full re-read of every page edited 3+
  times (21 candidates) — only the ones that changed since the last full
  read (six sessions ago) were re-read fresh; the rest were already verified
  clean twice and nothing has touched them since.

## [2026-08-18] refactor | PRO-79 content drift closed out
- Closed the `PRO-79` open item flagged in the migration-reconciliation entry
  above. Compared Linear's `PRO-79` against Jira's `AIRTABLEGC-37` directly
  (`get_issue`, `getJiraIssue`): Jira recorded a scope change Linear never
  picked up. `X-App-Id` enforcement was implemented and merged
  ([PR #7](https://github.com/livemode-org/livemode-airtable-proxy/pull/7)) — 401 on missing/unknown
  `X-App-Id`, 403 on base outside the app's allow-list, PAT injection + header
  strip, denial telemetry, tests green. `X-Api-Key` was explicitly deferred
  (decision with Luís, 1:1 of 2026-08-14), not dropped.
- msilva confirmed PR #7 has landed. Updated `PRO-79`: title →
  "Validar app por X-App-Id (X-Api-Key adiado)", description rewritten to match
  Jira's real scope, PR #7 linked as an attachment, status Backlog/In Progress
  → **Done**.
- Updated: [[2026-08-14 Migrate project management from Jira to Linear]]
  (third gap recorded under Resolved).
- Open: still no other issue in Todo/In Progress for msilva in
  `Proxy do Airtable` — next pick is one of the F3 Backlog items (`PRO-90`,
  `PRO-84`, `PRO-78`, `PRO-74`) or `PRO-60` (F1 Backlog); msilva hasn't chosen
  yet.

## [2026-08-18] refactor | PRO-78 picked up; four archived children found and relinked
- msilva confirmed he's actively working `PRO-78` (Auth + multi-app) — moved
  Todo → In Progress.
- Cross-checked against Jira's `AIRTABLEGC-5` (same epic): it has 5 children —
  `AIRTABLEGC-37` (done, this session, see `PRO-79` above) and four more
  (`-38` map appId, `-39` inject PAT, `-40` onboarding flow, `-41` spike on key
  rotation) that PRO-78's one-line Linear description doesn't carry.
- msilva asked to create sub-issues for those four. They already existed —
  `PRO-80`, `PRO-81`, `PRO-82`, `PRO-83`, correctly typed — but all four were
  **archived on 2026-08-06**, invisible in Linear's normal views. Not a missing
  migration; a silent archival, likely from the earlier flat-structure pass.
- Asked msilva how to relate them to `PRO-78` given the readout blind-spot this
  same decision documents (native sub-issues are invisible to the weekly AI
  status readout). He chose **flat**: `relatedTo` link instead of `parentId`.
  Set on all four, plus assignee → msilva, state → Todo.
- No available MCP tool unarchives an issue — `save_issue` silently leaves
  `archivedAt` in place. msilva restored `PRO-80/81/82/83` manually in the
  Linear UI. Re-verified via API: `archivedAt: null` on all four.
- Updated: [[2026-08-14 Migrate project management from Jira to Linear]]
  (fourth gap recorded under Resolved, now fully closed).
- Open: `AIRTABLEGC` wind-down; Business-trial purchase decision (both carried
  from prior entries).

## [2026-08-18] refactor | PRO-78 epic worked through: three closed as already-done, one deferred to Luís
- Read the actual proxy code (`C:\Users\msilva\projects\livemode-airtable-proxy`
  — confirmed via `git remote`/`git log` as the real clone; a same-named
  `Documents\repos\` copy has no `.git` and was ignored) to check each of
  `PRO-78`'s four children against reality before touching status.
- **`PRO-80` (Mapa appId → {apiKey, allowedBase}) → Done.** msilva clarified the
  roadmap: env JSON (now) → banco de dados (depois) → Secret Manager
  (eventualmente) — not straight to Secret Manager as originally designed.
  `internal/config/config.go`'s `PROXY_APPS` env var already is that map.
  Nothing left to build at this step; description updated, closed.
- **`PRO-81` (Injetar PAT) → Done.** Read `internal/proxy/proxy.go`'s
  `Director` with msilva: confirmed PAT injection from the same registry,
  `Authorization` stripped from clients first. Already shipped in PR #7 (the
  same PR `PRO-79` cited) — linked it here too.
- **`PRO-82` (Onboarding/key-issuance flow) → In Progress, not closed.**
  msilva called this one out for Luís rather than deciding it — matches
  [[2026-08-18 Bring options to Luís before deciding, communicate async and
  often]]. Drafted and tightened 3 options, posted as a Linear comment
  (`@Luis Fernandez` — first draft used the accented spelling and wouldn't
  have resolved as a real mention; corrected).
- **`PRO-83` (Spike: rotação de chaves) → `blockedBy: PRO-82`** (native
  relation, confirmed via `get_issue` `includeRelations`). msilva's own call:
  rotation can't be designed before onboarding/key-issuance is settled.
- Searched the wiki for a Luís conversation about Secret Manager msilva
  remembered having — not found. Two "Secret Manager" mentions exist
  ([[Airtable Proxy]], [[Proxy Environments]]) but neither ties to Luís; the
  only related decision (`X-Api-Key` deferral) is sourced from Jira comment
  text, not a wiki-ingested transcript. That conversation was never captured
  here — flagged to msilva.
- Updated: [[2026-08-14 Migrate project management from Jira to Linear]].
- Open: Luís's answer on `PRO-82`'s onboarding-flow options; everything else
  carried from prior entries.

## [2026-08-18] query | is a month realistic for the proxy — surfaced a timeline conflict
- msilva asked whether one month is realistic to finish the proxy (F3
  milestone). Counted open work: ~19 issues across auth-tail (blocked on
  Luís), production deploy, IaC, and LiveScript integration, against 13 Done
  since onboarding (2026-08-10).
- To check for prior context, opened `https://livemode-intranet.vercel.app/#pessoa/matheus`
  (Claude in Chrome, msilva's real logged-in session — the in-app browser hit
  Google SSO and couldn't get past it). Read all four tabs
  (Responsabilidades/Perfil/Combinados/Metas) — genuinely new source, not
  previously in this wiki.
- Found a real conflict: Luís's 2026-08-18 1:1 says the proxy is msilva's
  actual current job, 100%. The intranet's `metas.md`/`combinados.md` instead
  make the Agent Flow design (due 2026-08-31, with Gabi) and "first agent in
  production" (day 60, 2026-10-09) the headline commitments — the proxy reads
  as secondary there. Also noted in passing: the 30-day mark (2026-09-09)
  coincides exactly with the Linear Business-trial expiration already on
  record, unrelated but same date.
- msilva's call: **leave the proxy's Linear dates as they are** — no
  milestone or issue due dates set — rather than resolve the tension now.
- Updated: [[Airtable Proxy]] (new section recording the conflict; also
  resolved an old open item — confirmed the real repo clone's path via `git
  remote`/`git log`).
- Open: the Metas-vs-Luís conflict itself, unresolved by design; everything
  else carried from prior entries. The intranet page's `combinados.md`/
  `metas.md` content is not otherwise ingested — worth a proper `ingest` pass
  if msilva wants a `people`-style page for his own onboarding tracked here.

## [2026-08-19] lint | Eighth health check, then three fixes

### Clean

- **Links**: 0 broken wikilinks across 72 pages (re-ran the soft-line-wrap
  normalization from the seventh lint, extended to also collapse blockquote
  `>` continuations — needed for a wrap inside [[Airtable Proxy]]). The three
  remaining `[[...]]`/`[[people/]]` hits are inside `log.md`, quoting past
  entries as literal text, not live links.
- **Orphans**: none. **Dead-ends**: only [[Zed Cheatsheet]], as in every prior
  lint. **Frontmatter**: all pages carry `type`/`status`/`updated` or are
  correctly exempt. **`raw/` coverage**: unchanged, still complete — no new
  source files since the seventh lint.
- **Credentials**: working tree still clean (only the intentionally-kept
  `NEXT_PUBLIC_FIREBASE_API_KEY` matches a key-shaped pattern); still present
  in git history, unchanged. Eighth report — rotation still outstanding,
  still msilva's action.
- **Decisions open-vs-settled**: no mismatches. [[2026-08-18 Product feedback
  in Linear, code review in Git]] is still correctly `draft` everywhere it's
  referenced.
- **Intra-page contradictions**: the only page edited since the seventh lint
  besides `log.md` — [[2026-08-14 Migrate project management from Jira to
  Linear]], across five refactor commits (PRO-79 content drift, PRO-78's four
  archived children restored, the PRO-78 epic worked through) — read in full.
  Internally consistent; each new "Resolved" bullet is additive, none
  contradicts an earlier one.

### Fixed

1. **Dating slip on [[Airtable Proxy]] and its `log.md` entry.** The "is a
   month realistic" query and its "Timeline tension" section were dated
   2026-08-18, but the commit that recorded them (`c027ed5`) landed
   **2026-08-19 10:44 GMT-03:00** — today. Corrected the page's section
   header, its two internal date references, and its `updated:` frontmatter
   to 2026-08-19, with a visible correction note. **`log.md`'s heading is
   left as 2026-08-18** — append-only, never rewritten — so the two now
   disagree on record; this entry is the pointer between them.
2. **`index.md` had gone five commits without an update.** `PRO-79`'s content
   drift, the `PRO-78` archived-children restoration, the `PRO-78` epic
   resolution (3 of 4 children closed, one hard-blocked on Luís), and the
   2026-08-19 timeline-tension finding were all logged and landed on their
   source pages, but never reached the catalog — the exact "invisible page"
   failure mode `CLAUDE.md` warns about, this time for a stale summary line
   rather than a missing page. Caught up the [[Airtable Proxy]] bullet with
   both findings; bumped `index.md`'s own `updated:`.
3. Nothing else needed changing — see Clean above.

### Not fixed, reported only

- **[[Meeting prep - first agent decision]]** still has no confirmed meeting
  date (estimated ~2026-08-21, which is in 2 days); the file still needs
  renaming once it's known, per the `meetings/` convention. Unchanged since
  it was first flagged.
- **`AIRTABLEGC` wind-down** and the **Linear Business-trial purchase**
  decision are both still open, carried from every recent entry.

## [2026-08-19] refactor | old empty-Issues-tab mystery finally explained: 47 of 60 issues were archived
- msilva was studying `PRO-84`/`PRO-90`/`PRO-74` (deploy, IaC, observability)
  and, in the course of checking `PRO-76` specifically, hit the same
  `save_comment` "Could not find referenced Issue" error as the fourth-gap
  issues — `PRO-76` was archived too. Swept the whole project with
  `list_issues`/`includeArchived` to check for more.
- **Found 47 of 60 issues archived**, one timestamp cluster (2026-08-06,
  17:04:12–15) — the same bulk operation as the fourth gap, just far larger
  than the 4 issues caught then. Included **Done** work (`PRO-98–105`, the
  entire MVP/dashboards/alerting) and nearly all untouched Backlog.
- **This is almost certainly the answer to the empty-Issues-tab mystery from
  earlier in this migration** — msilva reported a completely empty Issues tab
  well before the fourth-gap discovery; that investigation checked team
  membership and `isAdmin` status as a candidate cause, went nowhere, and
  **was never written down**, so it silently dropped when the thread moved on.
  Not a permissions problem — archived issues just don't show up.
- msilva restored F1/F2/F3 in the Linear UI (no unarchive tool exists via
  MCP — same limitation as the fourth gap). **Left `Backlog deferido`
  (`PRO-106–117`) archived on purpose** — deferred scope, not being worked.
- Assigned all 47 to msilva (assignee changes apply even while archived,
  confirmed by testing it against `PRO-106–117` which are still archived).
  He's now the explicit sole owner of the whole backlog going forward.
- One loose end: `PRO-67` still shows `archivedAt` set despite being in the
  restored F2 group — not yet explained, needs a manual look.
- Updated: [[2026-08-14 Migrate project management from Jira to Linear]]
  (fifth gap, closing the old mystery explicitly).
- Open: `PRO-67`'s stray archive state; whether/when `Backlog deferido` gets
  restored; everything else carried from prior entries.

## [2026-08-19] refactor | LiveScript-vs-proxy split proposed and paused; Done issues linked to real PRs
- msilva asked to split the ~20 LiveScript-side issues in `Proxy do Airtable`
  out into **Fluxo Agêntico**, so a separate agent could pick them up. Issued
  18 parallel `save_issue` project-moves; **all 18 rejected by msilva** with
  "for now these will stay under Proxy do Airtable" (later corrected: the
  project's real name is "Proxy do Airtable", not "Airtable GC"). Reverted the
  one issue that had already moved (`PRO-58`) back to its original project and
  `F1 - Visibilidade LiveScript na Vercel` milestone.
- Then: "all the done issues should have the related PR." Ran
  `gh pr list --repo livemode-org/livemode-airtable-proxy --state all` — 7 PRs
  total, #1 closed/unmerged (superseded by #2), #2–7 merged.
- Linked real PRs as attachments: `PRO-75` → PR #2, `PRO-77` → PR #4, `PRO-80`
  → PR #7 (`PRO-79`/`PRO-81` already carried PR #7 from an earlier fix).
- Checked `git show b29f85b` to confirm `PRO-60`, `PRO-98`–`PRO-105` predate
  PR-based review (direct bootstrap commit to `main`, Luís, 2026-06-26) —
  **deliberately left unlinked**, no PR invented for them.
- Updated: [[2026-08-14 Migrate project management from Jira to Linear]] (two
  new Resolved bullets).
- Open: whether the LiveScript/proxy split gets revisited; `PRO-67`'s stray
  archive state; `Backlog deferido` restoration; everything else carried
  from prior entries.

## [2026-08-19] ingest | 1:1 Matheus / Gabrielle (2026-08-19)
- Source: `raw/Gabrielle _ Matheus - 2026_08_19 11_37 GMT-03_00 - Anotações do
  Gemini.md`. Third 1:1 on record, 13 min 17 s, single-topic: [[Agent Flow]]
  design (A1/A2 intake, A5 Watcher's shape, A3-vs-A7 boundary).
- New: `meetings/2026-08-19 1-1 Matheus - Gabrielle.md`.
- Updated: `projects/Agent Flow.md` (four spots), `syntheses/How to implement
  A5 Watcher.md`, `syntheses/Which agent should be built first.md` (two
  spots), `syntheses/What should the Agent Flow research phase study.md`
  (two spots), `index.md`.
- **Source quality: attribution collapsed completely**, not partially — every
  line credited to Matheus, including what reads as Gabrielle's prompting
  questions and short backchannel reactions folded mid-paragraph. Same defect
  family as [[2026-08-10 Onboarding Técnico - Matheus]] and
  [[2026-08-14 1-1 Matheus - Gabrielle]], but total this time. Content
  presented as msilva's own thinking, said aloud; anything attributed to
  Gabrielle specifically is flagged unconfirmed on the page.
- **No decisions.** Pure working/brainstorming session — flagged as such
  rather than promoted.
- **The one substantive new design material**: A5 Watcher laid out as two
  candidate architectures for the first time — **Path A** (instrumented
  per-project, e.g. inside [[LiveScript]], recurring setup cost) vs. **Path B**
  (a consolidator of tools already in use — LogRocket, Vercel, the
  [[Airtable Proxy]]'s telemetry as one input among several). msilva leans B.
- **Candidate reconciliation for the Luís/Gabrielle proxy-scoping conflict**
  (flagged unresolved since 2026-08-18): if Watch is a multi-tool
  consolidator, the proxy is one input, not Watch's defining scope — which
  may answer Luís's objection without losing Gabrielle's utility case.
  **msilva's own synthesis from this meeting, not run past Luís** — recorded
  as a candidate everywhere it touches, never as settled. [[How to implement
  A5 Watcher]]'s existing build plan is flagged as the Path-A/proxy-only
  slice, since it predates this distinction.
- **A3-vs-A7 axis corrected**: msilva explicitly rejects "fast vs. slow
  demand" as the differentiator (a framing [[Agent Flow]]'s own agent table
  still uses, annotated in place rather than rewritten) in favor of
  **implementation complexity and how many approval layers a change needs**.
- **New open question, not previously asked**: should discovery/planning
  precede *every* execution (the way msilva personally uses Claude Code —
  plan then act, scaled to complexity) rather than being a separate step
  gated by complexity? If universal, today's A3-vs-A7 fork could collapse
  into "discover always, fork by complexity after."
- **New open question**: is A1+A2 one agent or two? msilva's own test —
  worth splitting only if A2's inputs come from more than just A1.
- **New open question**: is usage/UX-pattern monitoring part of A5 Watcher,
  or a separate "product" concern? Left open, not assigned either way.
- **Governance/approval tiers wanted, not designed**: msilva wants an
  explicit tiered structure (no review needed / his approval / Gabrielle's /
  Luís's too), loosely tied to A6 Curator as the agent that would route work
  to the right tier. Noted as a new open question rather than a design.
- `Bug (sistema)` mention **confirms, doesn't extend**, the existing reading
  that it's machine-fed with A5 Watcher as a candidate producer.
- Open, carried forward: everything above, plus everything already open on
  [[Agent Flow]] and its syntheses (the Gabrielle/Carol discovery
  conversations remain the actual next step; this meeting's material is
  input to those, not a substitute for them).

## [2026-08-19] decision | PRO-94 (Pulumi language) options brought to Luís
- With the auth epic closed out and `PRO-82`/`83`/`76` blocked on Luís,
  identified `PRO-94` (choose Go/TypeScript/Python for the Pulumi program) as
  the one unblocked next issue — no GCP project needed, part of the deploy
  step (`PRO-90`/`84`/`74`) msilva wanted to study.
- Discussed tradeoffs in chat: Go (same language/toolchain/CI as the proxy,
  first-class Pulumi SDK, mock-testing support) vs. TypeScript (denser
  example coverage for `pulumi-gcp`, but a second toolchain in an all-Go repo)
  vs. Python (no clear fit). msilva's own preference: Go.
- Per [[2026-08-18 Bring options to Luís before deciding, communicate async
  and often]], posted the same 3 options as a Linear comment on `PRO-94`
  rather than deciding unilaterally, lean toward Go stated but left as Luís's
  call. Moved `PRO-94` to In Progress.
- Updated: [[2026-08-18 Bring options to Luís before deciding, communicate
  async and often]] (new "Applied since" section, also backfilling the
  `PRO-82` instance).
- Open: Luís's reply on `PRO-82` and now `PRO-94`; everything else carried
  from prior entries.

## [2026-08-19] ingest | 1:1 Matheus / Gabrielle (2026-08-19), Part 2
- Source: `raw/Gabrielle _ Matheus - 2026_08_19 11_56 GMT-03_00 - Anotações do
  Gemini.md`, 26 min 29 s, starting 6 minutes after Part 1 ended — treated as
  the same meeting's second Gemini segment, per the precedent set for the
  Gabriel session's two-part recording. **Not a new page** — appended to
  `meetings/2026-08-19 1-1 Matheus - Gabrielle.md` as "Part 2," frontmatter
  `source` now lists both raw files.
- Updated: `projects/Agent Flow.md` (five spots), `syntheses/Which agent
  should be built first.md` (two spots), `syntheses/Comparing the
  first-agent candidates.md`, `syntheses/What should the Agent Flow research
  phase study.md` (two spots), `concepts/Linear Project Structure.md`,
  `index.md` (three spots).
- **Same attribution defect as Part 1, unchanged** — everything below is
  presented as msilva's own account.
- **Finishes the 14-agent walkthrough this pair started in Part 1**: A6
  Curator's **four** functions named for the first time (institutional
  memory, continuous learning, cross-area redundancy detection, and an
  agent-facing interface layer — *"uma intranet dos agentes"*), with msilva
  flagging that four functions may be too much for one agent — sharpens,
  doesn't resolve, the existing "A6 splits into retrieval + curation"
  corollary from 2026-08-18. A9 Developer clarified against A3 (both
  executors; A3 fast/operational, A9 behind full A7 discovery). A10
  Portfolio possibly already prototyped by **Carol's intranet tool**
  (unconfirmed) and feeding A11 Product Intelligence. A12 Data Gov resolved
  as data-usage validation, with a new question of whether this team even
  owns it versus a platform/foundation layer. A13 Deduplication may be the
  same agent as A10. A14 (garbled as "prêmio," almost certainly PM Agent) is
  a reporting layer — and Linear can already auto-post release notes to
  Slack on ship, per Luís, needing no new build for that piece.
- **A genuinely new, separate debate, from a conversation with Carol and
  Luís "yesterday": how much upfront context does agentic coding actually
  need?** Not about [[Agent Flow]]'s design directly, but a direct input to
  it, and in tension with Part 1's "should discovery be universal?" question.
  Luís preps heavily and isn't clearly faster for it; Carol has had good
  results giving **less** context over time; msilva's own practice — push to
  homologation, test himself, report back via an agent, rather than
  understand everything upfront — is a concrete example of the same lean.
  Recorded as a live tension, not resolved either way; msilva says
  explicitly he still has to study this properly with Carol and Luís.
- **Possible convergence, flagged carefully.** msilva reports agreeing with
  a view attributed to Gabrielle that scoping Watch to the proxy doesn't
  make structural sense today — the same objection Luís raised on
  2026-08-18. If that attribution holds, the "unreconciled tension" recorded
  since then may be narrowing on its own, independent of the Path A/B
  reconciliation from Part 1. **Not promoted to fact anywhere it's
  recorded** — total attribution collapse means this could equally be
  msilva restating his own view. Needs a direct check with Gabrielle.
- **A third "which agent first" framing.** Carol: start with whatever
  relieves the team's **own most immediate pain**, not utility broadly.
  msilva's own two pains, stated directly for the first time: (1) no
  unified cross-project backlog/prioritization view across paused vs. active
  projects — A10+A14 territory; (2) no good discovery/documentation minimum
  — A7+A8 territory, and the same pain as the context-tradeoff debate above.
  Neither points at A1+A2 or A5. Recorded as new input, not a new decision —
  [[Comparing the first-agent candidates]] explicitly not yet rewritten to
  reflect it.
- **A8 Orchestrator gets a new stated role**: checking whether A7's BRD
  output already carries enough context before build starts — an explicit
  context-sufficiency gate not previously recorded.
- Cut off mid-sentence at the very end — *"foi um feedback que eu e Luís
  chegamos ontem"* — content lost, flagged rather than guessed at.
- Open: everything above, plus the discovery conversations with Gabrielle
  and Carol already planned since 2026-08-18, which this meeting's material
  feeds into rather than replaces.

## [2026-08-19] refactor | `[!msilva]` callout convention, Obsidian-native

- Reverted the earlier `note <idea>` operation (chat-triggered) at msilva's
  request — he wants to add his own ideas **through Obsidian directly**, not
  via a Claude Code conversation.
- Added a `[!msilva]` callout convention to `CLAUDE.md` instead: msilva writes
  `> [!msilva] ...` directly on any page, in Obsidian, no session needed.
  Kept verbatim, never silently rewritten. Stays on the page if it only
  concerns that page; fanned out (attributed, dated, same tier as a spoken
  meeting quote) the next time that area is touched if it bears on other
  pages; promoted to `decisions/` only once msilva confirms it's settled.
- Added a `lint` check for unfanned `[!msilva]` callouts.

## [2026-08-19] refactor | `[!claude]` replies + session-start callout check

- msilva wants his `[!msilva]` callouts actually **seen and thought about**,
  not just silently fanned out (or not) whenever a page happens to get
  touched later.
- Added a `[!claude]` reply convention: every `[!msilva]` callout gets a
  dated `[!claude]` reply written directly beneath it — real reasoning, a
  question back, or what was done — not an acknowledgment.
- Added a new first-thing-every-session step to `CLAUDE.md`: search for
  `[!msilva]` callouts with no `[!claude]` reply yet, think about each one,
  reply inline, fan out if it bears on other pages, then summarize the
  thinking to msilva before starting whatever the session was actually
  asked to do.
- No callouts exist yet to process — convention just created.

## [2026-08-19] refactor | Drop [!claude] inline reply — discuss in chat, register in log.md

- msilva: an inline `[!claude]` reply under every callout would clutter the
  wiki. Reverted that part.
- New shape: unhandled `[!msilva]` callouts (ones `log.md` doesn't yet
  reference) get discussed live in chat at session start, not answered on
  the page. Once talked through, the callout is either **deleted** — outcome
  folded into the page's real prose or fanned out elsewhere — or, if it's a
  standing opinion with nothing to action, **left in place** untouched.
  Either way, one `log.md` line records it was seen — that line is also what
  stops it being re-surfaced next session.
- Updated the `lint` check and the *Session start* operation in `CLAUDE.md`
  to match.

## [2026-08-19] refactor | log.md entry carries the actual reasoning, not a marker

- msilva asked where Claude's thinking on a `[!msilva]` callout actually
  lives, once it's not written on the page. As drafted, `log.md` was only
  getting a bare "seen" flag — the real reasoning would have existed only in
  that session's chat, which a later session doesn't reliably re-read.
- Fixed: the `log.md` entry for a discussed callout now carries the actual
  gist of the discussion and its conclusion — same substance as any other
  log entry — not a bookkeeping marker. Its presence is still what stops the
  callout being re-surfaced, but now it's also the durable record.

## [2026-08-19] refactor | Name the callout procedure `notes`, add `callout` log prefix

- msilva wants an explicit command to trigger the callout search-and-discuss
  procedure, not just the automatic session-start run.
- Named the procedure `notes` in `CLAUDE.md`: same steps as before (search
  unhandled `[!msilva]` callouts, discuss in chat, resolve on the page, log
  the actual reasoning), now invokable on demand by typing `notes`, and it's
  what runs automatically at session start too — one procedure, two triggers.
- Added a `callout` log prefix for entries `notes` produces, and pointed the
  `lint` checklist item at it (should rarely fire, since session start
  already covers it — `lint` is the backstop).

## [2026-08-19] callout | First `notes` run — six callouts on the 2026-08-19 1-1

- First real run of `notes`. Found six `[!msilva]` callouts on
  [[2026-08-19 1-1 Matheus - Gabrielle]] — msilva's own post-meeting
  annotations, added directly in Obsidian, none referenced here yet.
- **A1/A2 merge lean.** Agreed as a position, not a decision: merge into one
  agent, since separating them earns nothing if their scope is this similar.
  Folded into [[Agent Flow]]'s existing open question; callout deleted.
- **A5 hybrid design, via an SDK.** New material — neither Path A nor Path B
  alone, but both: Path B's multi-tool aggregation plus Path A's in-service
  integration where a service's scope justifies it, sharing one SDK for the
  in-service half instead of bespoke setup per project. Added to the
  Path A/B reconciliation block on both [[Agent Flow]] and the meeting page
  itself, and to the open question on [[How to implement A5 Watcher]].
  Untested against Luís or the existing build plan. Callout deleted.
- **Discovery-as-transversal lean.** msilva leans toward discovery being
  universal (called early, by most flows), but this is flagged against the
  same meeting's Part 2 tension (Carol/msilva's own practice favor *less*
  upfront context) rather than presented as resolving it. Folded into
  [[Agent Flow]]'s open question; callout deleted.
- **UI/UX wants its own agent.** New scope not present anywhere in the
  14-agent design — msilva's lean is that UX-pattern monitoring is narrower
  than general observability and shouldn't live inside Watch. Folded into
  [[Agent Flow]]'s existing open question on Watch's UX scope; worth raising
  in the planned discovery conversations. Callout deleted.
- **A7's foundational role.** Read as commentary, not a new claim — folded
  directly into the "Echo of the A4 Teacher pattern" paragraph on the meeting
  page itself. Callout deleted.
- **Pain 1 as a strong starting point, because it's recurring.** msilva
  confirmed this belongs on Pain 1 (cross-project backlog/prioritization,
  A10 Portfolio + A14 PM Agent), not Pain 2 as originally placed — he'd
  already moved it in Obsidian by the time this was resolved. Folded into
  the meeting page's Pain 1 paragraph, plus a light touch on
  [[Which agent should be built first]] and
  [[Comparing the first-agent candidates]], both of which already carried
  the pain-based argument and just gained the "recurring, not one-off"
  reasoning. Callout deleted.
- All six resolved by editing rather than by leaving a reply on the page,
  per msilva's instruction that inline replies clutter the wiki — the
  discussion happened in chat, this entry is the durable record.

## [2026-08-19] lint | Ninth health check, one fix

- **Link graph** (Node script, 73 pages): 0 real broken links. The five
  hits the script flags are expected non-issues: `CLAUDE.md`'s `[[Page]]` is
  the schema's own citation-format example (deliberately not a live link,
  per that section), and `log.md`'s `[[...]]`/`[[people/]]` hits are this
  file quoting past log entries verbatim.
- **Orphans**: none. **Dead ends**: one, `reference/Zed Cheatsheet.md` —
  known and accepted since the eighth lint, no outbound links by nature of
  what the page is.
- **Frontmatter**: clean across all non-exempt pages.
- **`raw/` coverage**: every source file has a wiki page except
  `Novo(a) Documento de Texto.txt` (empty, nothing to ingest) and `README.md`
  (not a source). The `2026-06-14-Papo de Projetos...docx` filename-vs-content
  date mismatch (docx says June, the meeting page correctly says
  `date: 2026-08-14`) was already resolved by a prior ingest per the
  file-contents-over-filename precedence rule — re-confirmed, not a new fix.
- **`[!msilva]` callouts**: none outstanding — the prior commit's `notes` run
  cleared all six on the 2026-08-19 1:1 page, and no others exist in the vault.
- **Intra-page consistency**: read the pages touched by both parts of the
  2026-08-19 ingest and the callout-resolution commit in full —
  [[Agent Flow]], the [[2026-08-19 1-1 Matheus - Gabrielle]] meeting page,
  [[Which agent should be built first]], [[Comparing the first-agent
  candidates]], [[What should the Agent Flow research phase study]],
  [[How to implement A5 Watcher]], [[Linear Project Structure]]. All
  consistent: the "possible convergence" finding is flagged as unconfirmed
  identically everywhere it appears, decided-vs-discussed is kept straight,
  and no decision anywhere is recorded as settled in one place and open in
  another.
- **Fixed**: a stray editing artifact from the callout-resolution commit —
  the "tiered governance" open question on the meeting page had picked up a
  trailing `.so ` after "route work to the right tier" (`tier.so `),
  left over from folding a callout's text into the paragraph. Corrected to
  end cleanly at "tier."
- **Not fixed / no action needed**: everything else. This was the cleanest
  lint pass to date — no dating errors, no stale index, no unresolved
  callouts.

## [2026-08-19] refactor | Correct Luís's A5-proxy objection
- msilva clarified in chat: Luís's position on 2026-08-18 was mischaracterized
  across five pages as "decouple A5/Watcher from the proxy entirely." The
  actual objection, per msilva (who was in the meeting): the proxy should not
  be A5's *defining scope*, not that the proxy must play no role at all.
- Kept the verbatim quote ("desassociaria ele completamente") in place per
  schema — it is real transcript evidence — but added inline corrections
  rather than rewriting it.
- Updated: meetings/2026-08-18 1-1 Matheus - Luís.md (added correction
  callout), projects/Agent Flow.md, syntheses/What should the Agent Flow
  research phase study.md, syntheses/Which agent should be built first.md,
  index.md.
- Consequence: the Gabrielle/Luís tension is narrower than previously
  recorded, and closer to (maybe identical to) the multi-tool-consolidator
  reconciliation msilva already proposed 2026-08-19. Not re-confirmed with
  Luís; still to raise in the planned discovery conversation.

## [2026-08-19] refactor | Promote F3 from milestone to its own Linear project
- msilva asked to plan the Airtable Linear reorg "like Luís said" — went back
  to `raw/Luis _ Matheus - 2026_08_19 15_01...md` (not just the wiki's earlier
  summary of it) because msilva's own recollection ("F3 should be a project
  under the Airtable initiative") didn't match what [[Linear Project
  Structure]]'s "fourth convention" bullet had recorded.
- **Found a real compression error in the earlier ingest.** The raw transcript
  has Luís saying explicitly: *"Isso é um projeto, cara. Esse projeto tem
  milestones"* — the milestone msilva is actively working in gets promoted to
  its own project, not just "the active project needs structure." The
  earlier wiki summary lost the promotion mechanism itself.
- Pulled live Linear state (`get_project`/`list_issues`) before planning,
  rather than trusting the wiki's 2026-08-18/19 snapshot — confirmed
  `PRO-67` still shows `archivedAt` despite being "restored," and that F3 had
  32 issues (13 Done) sitting flat under one 43%-progress milestone.
- **Executed, confirmed by msilva** (new project name = the milestone's own
  name; `Proxy do Airtable` left as-is, no rename):
  - Created project **Proxy em produção validado c/ LiveScript**
    (`24c71c2a-…`), sibling to `Proxy do Airtable` inside **Airtable GC —
    Governança e Confiabilidade**.
  - Created 6 milestones inside it, one per **existing epic** already in the
    32 issues — no new categorization invented: MVP local do proxy (`PRO-98`
    + 7), Auth + multi-app (`PRO-78` + 5), Observabilidade completa no proxy
    (`PRO-74` + 3), Deploy em produção (Cloud Run) (`PRO-84` + 5), IaC
    (Pulumi) (`PRO-90` + 4, including `PRO-92`/`93` which an earlier
    in-chat draft of this plan had missed), Integração & validação com
    LiveScript (`PRO-95` + 2).
  - Moved all 32 issues (`PRO-74–105`) into the new project and their
    matching milestone.
  - Left `Proxy do Airtable` (F1, F2, Backlog deferido, the `PRO-67`
    straggler) completely untouched, per msilva's explicit call — matches
    Luís's "no rush, no cognitive cost" framing for everything outside the
    actively-worked milestone.
- Updated: [[Linear Project Structure]] (corrected the fourth convention with
  the verbatim quotes, recorded the execution), [[Airtable Proxy]] (top
  callout + timeline-tension section), `index.md`.
- Not done, deliberately: `Proxy do Airtable`'s now-empty F3 milestone
  shell wasn't deleted — "leave it as it is now" was read as covering the
  whole project, including that leftover. Worth a manual glance next time
  that project is touched.
- **Follow-up, same session**: filled in the new project's summary and
  description (pt-BR, business-first per the standing Linear convention) and
  added two resource links — the GitHub repo and the sibling `Proxy do
  Airtable` project it was split from. Linear auto-flipped the project's
  status to "In Progress" on save, presumably because its milestones already
  carry real progress; not something set explicitly.

## [2026-08-19] lint | Tenth health check
- Scoped to the 14 files changed since the ninth lint (`123b0a5`), rather than
  re-reading everything: that pass had already verified the rest of the vault
  clean, and the mechanical checks below cover the whole vault regardless.
- **Mechanical checks, whole vault**: zero broken wikilinks (the only
  `[[Page]]`/`[[people/]]`-shaped hits are literal example text in `CLAUDE.md`
  and historical narration in `log.md`, not real links); zero orphans besides
  the four schema-exempt infra pages (`AGENTS.md`, `CLAUDE.md`, `index.md`,
  `log.md`); zero dead-ends besides the two already-known/accepted ones
  (`AGENTS.md`, [[Zed Cheatsheet]]); frontmatter has `type`/`status`/`updated`
  on every non-exempt page; every `raw/` file (bar `.obsidian/`, `README.md`,
  the empty `Novo(a) Documento de Texto.txt`) is cited somewhere outside
  `log.md`; no `[!msilva]` callouts outstanding; nothing close to the 30-day
  staleness threshold (oldest `updated:` is 2026-08-11, eight days).
- **Read the 14 changed files for intra-page and cross-page contradictions**
  (the check mechanical tools can't do). Two real findings, both fixed:
  1. [[2026-08-19 Identify proxy apps by URL path, not header]]'s "What's
     still open" list still carried the mini-relatório question as unclear,
     but [[2026-08-19 1-1 Matheus - Luís]] had already answered and deleted
     that exact `[!msilva]` callout in the same day's earlier commit. Struck
     through and answered on the decision page.
  2. [[2026-08-18 1-1 Matheus - Luís]]'s open question "should the `pnpm
     patch` stay in production?" and its "SDK header patch" section both
     still read as unresolved, but the patch was already reverted
     2026-08-18 and the 2026-08-19 URL-path decision retires header-based
     identification entirely — there's no live decision left to make.
     Marked moot in both places, Luís's general discomfort with
     `pnpm patch`-class tools kept as standing feedback.
- Everything else in the 14 files checked out consistent — in particular the
  A5-proxy-scoping correction and the Path A/B reconciliation read the same
  way across [[Agent Flow]], both syntheses, and the Carol prep note.
- Not done: didn't re-verify the pages the ninth lint already cleared
  (`Agent Flow`'s 30 prior commits, `Airtable Proxy`'s 19, etc. weren't
  touched again in full — only their diffs since then).

## [2026-08-19] refactor | Meeting prep — A5 Watcher discovery with Carol
- msilva asked for a prep doc for today's discovery conversation with Carol,
  modeled on the working style of the [[2026-08-19 1-1 Matheus - Gabrielle]]
  session — the second of the two deliberately-separate Watcher/A5 discovery
  conversations agreed on 2026-08-18.
- New: `meetings/Meeting prep - A5 Watcher discovery with Carol - 2026-08-19.md`.
  Structured to protect the point of asking separately: the cold opening
  question comes first, background (A5's shape, the Gabrielle/Luís tension,
  msilva's own Path B reconciliation) is held back until after she answers.
  Also flags three Carol-specific threads: her own "team's pain" criterion,
  whether her intranet tool is already A10 Portfolio, and skills-repo overlap
  with A6 Curator.
- Updated: `index.md`.

## [2026-08-19] refactor | Broaden Carol prep doc beyond A5
- msilva corrected: today's Carol meeting should not be specific to A5. The
  prior entry this session created a Watcher-only prep doc, too narrow.
- Renamed (git mv, content rewritten): `meetings/Meeting prep - A5 Watcher
  discovery with Carol - 2026-08-19.md` -> `meetings/Meeting prep - Agent Flow
  discovery with Carol - 2026-08-19.md`.
- New structure covers five threads: which-agent-first against Carol's own
  "team's pain" criterion; the Watcher/A5 discovery question (kept, asked
  cold, since it's still the specific action item from
  [[2026-08-18 1-1 Matheus - Luís]]); A6 Curator vs. the skills repo she's
  building; her intranet tool as a possible A10 Portfolio; and getting her
  context-minimalism view first-hand rather than relayed secondhand from
  [[2026-08-19 1-1 Matheus - Gabrielle]].
- Updated: `index.md` (entry rewritten in place, notes the rename).

## [2026-08-19] refactor | Add Gabi-comparison to Carol prep doc
- msilva asked for the same doc, but with what was already gathered from the
  Gabi conversation laid alongside each thread for direct comparison during
  the Carol meeting.
- Rewrote `meetings/Meeting prep - Agent Flow discovery with Carol -
  2026-08-19.md`: added a quick-comparison table at the top, and a
  "what the Gabi conversation already gave us" subsection under each of the
  five threads (which-agent-first, Watcher/A5 scope, A6-vs-skills-repo, A10/
  intranet tool, context minimalism).
- **Explicit caveat added and repeated**: [[2026-08-19 1-1 Matheus -
  Gabrielle]]'s transcription collapsed attribution entirely, so most of the
  "Gabi side" is msilva's own thinking said out loud, not confirmed quotes
  from her. The doc tells msilva to treat Carol's answers as the more
  reliable signal, not to use the Gabi column as a tie-breaker.
- Updated: `index.md`.

## [2026-08-19] refactor | Reshape the new proxy project's milestones, add parent/sub-issues
- msilva pushed back on the milestone shape from the F3-promotion session:
  *"epic are more related to an issue, no? help me think new milestones"* —
  correct instinct, milestones are project-level checkpoints, epics are
  issue-level parents, and a 1:1 mirror between the two (what was built) is
  redundant.
- Before reshaping, found that **`PRO-84` (Deploy em produção) and `PRO-90`
  (IaC Pulumi) aren't two phases** — `PRO-91`/`92`/`93` re-declare the same
  Cloud Run/Secret Manager/dashboards work `PRO-85`/`86`/(already-done
  `102`/`103`) do by hand, just as Pulumi code. Surfaced this before asking
  msilva to choose a milestone shape, since it changes the answer.
- Presented 3 milestone-shape options and asked which issue clusters should
  get real parent/sub-issue structure (`AskUserQuestion`). msilva chose the
  **3-checkpoint shape** and **4 of 6 candidate clusters** (skipped the
  already-Done MVP cluster; the Deploy+IaC cluster was chosen contingent on
  merging the two epics first, which happened).
- **Executed**: renamed 3 of the 6 existing milestones in place (no delete
  tool exists via this MCP) — `Auth + multi-app` → **Proxy funcionalmente
  completo**, `Deploy em produção (Cloud Run)` → **Proxy em produção**,
  `Integração & validação com LiveScript` → **Validado com o LiveScript**.
  Moved every issue from the other 3 (`MVP local do proxy`,
  `Observabilidade completa no proxy`, `IaC (Pulumi)`) into these; those 3
  now sit empty and can't be deleted through the API — msilva can remove
  them by hand in the Linear UI if he wants them gone.
- Set real `parentId` on 4 clusters: `PRO-78` → `79-83`; `PRO-74` → `75-77`;
  `PRO-95` → `96-97`; `PRO-90` (itself now a child of `PRO-84`) → `92-94`
  (`91` already was); plus `PRO-85-89` directly under `PRO-84`. MVP cluster
  (`PRO-98` → `99-105`) left flat, per msilva's selection.
- Updated project summary/description to match the 3-checkpoint shape and
  the epic merge.
- **Flagged, not resolved, before acting**: real parent/sub-issue structure
  is exactly what [[Linear Project Structure]] convention 3 (flat issues)
  exists to avoid — the readout's subtask blind spot was measured on a
  parent issue with unfinished children, same shape as these four now have.
  msilva chose to proceed anyway; milestones (which the readout does count)
  remain the actual reporting signal, so the risk is contained as long as
  that stays true.
- **Not resolved**: whether `PRO-85`/`86`/`89` (manual) and `PRO-91`/`92`
  (Pulumi) are both still needed now that they sit under one parent, or
  whether one side should be closed once the deploy method is settled.
  `PRO-94` (choosing the Pulumi language) is already In Progress, which
  leans Pulumi, but nothing decided or closed.
- Updated: [[Linear Project Structure]], [[Airtable Proxy]].
- **msilva deleted the 3 leftover empty milestone shells by hand in the
  Linear UI** (no delete tool exists via this MCP) — confirmed live, the
  project now holds exactly the 3 checkpoint milestones.

## [2026-08-19] decision | Deploy via Pulumi settled; PRO-85/86 marked duplicate
- Continued the Deploy-vs-IaC discussion from the reshape above. msilva:
  named it exactly right first — merging `PRO-85`/`86` into `PRO-91` isn't
  Linear housekeeping, it's deciding the deploy *method*, the same shape of
  call [[2026-08-18 Bring options to Luís before deciding, communicate async and often]]
  exists for. Presented three options (merge now / raise with Luís first /
  link without merging) rather than assuming.
- **msilva: "pulumi choice is already settled"** — decision predates this
  session and wasn't newly made here; not independently re-confirmed with
  Luís in this conversation.
- **Executed**: `PRO-85` (manual Cloud Run deploy) and `PRO-86` (manual
  Secret Manager provisioning) marked `duplicateOf` `PRO-91` — Linear
  auto-cancelled both (`status: Duplicate`) on save.
- **Left alone, deliberately, not swept into the same merge**:
  - `PRO-89` (wiring the real notification channel) and `PRO-92` (codifying
    the alert policy in Pulumi) — related, not duplicate: one's the rule,
    one's the channel it fires into.
  - `PRO-93` (dashboards as code) — re-declares the already-Done
    `PRO-102`/`103` dashboards, which is legitimate IaC scope (formalizing
    what already shipped), not wasted duplicate effort to cancel.
- Updated: [[Airtable Proxy]] (finding section resolved, superseded framing
  removed).

## [2026-08-19] refactor | Backlog review pass, Proxy em produção validado c/ LiveScript
- Went through all 13 remaining `Backlog` issues in the new project one by
  one with msilva, per his request ("lets review and validate all the
  backlog items"). Scope confirmed explicitly beforehand: this project only,
  not `Proxy do Airtable`'s F1/F2/Backlog deferido.
- Order: `PRO-74`, `76`, `84`, `87`, `88`, `89`, `90`, `91`, `92`, `93`,
  `95`, `96`, `97`. All titles/descriptions/hierarchy confirmed accurate;
  msilva confirmed `PRO-96`'s "Cenário A" framing is a settled design
  decision, not still open.
- **Status fixes made**:
  - `PRO-84`, `PRO-90` (epics): `Backlog` → `In Progress` — their child
    `PRO-94` had already started and status hadn't caught up. Same gap
    previously fixed for `PRO-78` during the auth-epic pass.
  - `PRO-91`: `Backlog` → `Todo` — next actionable step in the deploy
    milestone, gated only on the in-progress `PRO-94` spike.
- **Structural fix**: `PRO-89` ("alertas de produção com canal real") and
  `PRO-117` ("definir canal de notificação", previously sitting inert in
  `Proxy do Airtable`'s "Backlog deferido" milestone) were duplicative in
  intent — deciding the channel vs. wiring it up. Merged as parent/sub-issue
  instead of two disconnected items: `PRO-117` moved into this project,
  reassigned to the "Proxy em produção" milestone, parented under `PRO-89`.
  Also discovered `PRO-117` was **archived** (since 2026-08-06, part of the
  same bulk-archive event already documented for `Proxy do Airtable`) —
  msilva unarchived it manually in the Linear UI after getting the link.
- **PRO-76 found blocked in practice**: propagating `app.route` needs
  LiveScript to *send* it, which is LiveScript-side code, out of scope per
  [[2026-08-18 1-1 Matheus - Luís]]. Already recorded as a comment on the
  issue itself (2026-08-19, pre-dates this session) — the comment names
  Luís directly, which msilva confirmed is fine to leave as-is (he wants to
  raise the underlying scope question with Luís directly instead).
- **New decision written**: [[2026-08-18 Proxy first, defer LiveScript-side
  SDK changes]] — msilva confirmed during this pass that the constraint
  first stated for `PRO-76` generalizes ("mostly will be deferred") rather
  than being a one-off, so it's now recorded as a real decision, not just an
  issue comment. Cites both `PRO-76` and `PRO-82` as blocked by it.
- Updated: [[Airtable Proxy]] (backlog-review-pass note added to the top
  callout), `index.md` (new decision listed).

## [2026-08-20] decision | Luís resolved PRO-82 (onboarding flow)
- Luís replied on `PRO-82`'s options comment: *"Vamos pelo caminho mais
  simples possível. Um novo app entraria no proxy com configuração direto na
  base no pior dos casos. Vamos descobrir a melhor forma conforme os
  primeiros apps de fora da nossa equipe começarem a acessar o proxy."*
- Reads as option 3 of the three posted (defer formal tooling until the DB
  migration) in pragmatic form: no self-serve script now, worst case is
  direct config, real flow worked out once real external apps show up.
  Matches msilva's stated lean.
- Second instance of [[2026-08-18 Bring options to Luís before deciding,
  communicate async and often]] reaching a reply (after `PRO-94`, still
  pending).
- Updated: [[2026-08-18 Bring options to Luís before deciding, communicate
  async and often]] (`PRO-82` entry marked resolved), [[Airtable Proxy]]
  (auth epic's tail note — `PRO-82` no longer blocked; `PRO-83` still blocked
  on it but now unblockable).

## [2026-08-20] decision | PRO-83 (key/secret rotation) follows PRO-82's policy
- Discussed with msilva: `PRO-83` bundles two secrets — the Airtable PAT the
  proxy holds, and the per-app keys it issues (the `PRO-82` side). Since
  `PRO-82` settled on manual env-edit + redeploy with no self-serve layer,
  any rotation strategy today is bounded to the same manual mechanism —
  generate, edit env, redeploy, revoke/replace old value.
- **msilva's call: yes, same policy** — manual rotation for both secrets, no
  tooling built now, revisit once real DB-backed storage exists. Not run past
  Luís, on the reasoning that this is applying an already-settled precedent
  (not a fresh architectural fork), which the [[2026-08-18 Bring options to
  Luís before deciding, communicate async and often]] norm already scopes to
  "real alternatives," not every micro-choice.
- Noted in passing: `PRO-83` was overdue (`dueDate` 2026-07-31); msilva to
  handle the Linear-side status himself.
- Updated: [[Airtable Proxy]] (auth epic's tail note), `index.md`.

## [2026-08-20] ingest | Fabio Akita — Harness, Loop and Graph Engineering are bullshit
- Read `raw/Clippings/Hot take Harness, Loop Engineering, Graph Engineering
  são Bullshit.md` in full (blog post, 2026-08-18, pt-BR).
- Discussed takeaways with msilva before writing, per the schema: agreed to
  register the piece as a counterpoint against [[Agent Flow]] (not reopen any
  decision), and separately discussed the structural parallel between
  Akita's **ai-memory** tool and this vault's own CLAUDE.md-governed
  wiki-over-spec approach. Conclusion on that discussion: worth recording as
  an open question — should this vault ever also ingest raw coding-tool
  session transcripts, the way ai-memory does? — left explicitly undecided,
  not acted on. Any change to what this vault ingests is a `CLAUDE.md` schema
  proposal, not a page-level decision.
- New: [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] —
  summary, key claims (harness choice matters for cost more than quality;
  compounding-failure math against multi-agent orchestration, corroborated by
  Cognition/Anthropic/a Berkeley study; spec-as-source-of-truth critique
  paralleling UML/MDA; his own Agile-Vibe-Coding practice; the ai-memory
  parallel), open questions.
- Updated: [[Agent Flow]] (counterpoint callout on orchestration depth and
  A7's PRD-first output contract, cross-referenced against
  [[Gabriel Packer - DAG-driven agent orchestration]]'s narrower practice),
  [[Claude Agent SDK]] (harness choice as a cost decision, not an
  architecture one), `index.md` (Sources, Agent Flow and Claude Agent SDK
  entries).
- Not folded into any decision — registered as a counterpoint to weigh,
  per msilva's explicit direction.

## [2026-08-20] refactor | render the Fluxo Agêntico diagram as Mermaid
- msilva asked for the 14-agent architecture diagram to be easy to access in
  the wiki. It previously existed only as an SVG inside
  `raw/fluxo_agentico_ajustado (2).html` and a rough ASCII sketch on
  [[Fluxo Agêntico diagram]].
- Re-read the raw SVG's actual coordinates and edges directly (not just the
  page's prior summary) to render it faithfully: A5→A3 feedback loop,
  A5→A10/A11/A12 context feed, A10↔A11↔A12 collaboration, A10/A11/A12→A9,
  A13→A1 context-back-to-receptor, and A6 Curator's near-universal (faint,
  low-opacity in the original) connections to every other agent.
- Updated: [[Fluxo Agêntico diagram]] (ASCII sketch replaced with a
  Mermaid flowchart, color-classed to match the original SVG's node
  groupings), `index.md`.
- **Follow-up, same day**: msilva reported the render overflowing. Shrunk it —
  shortened node labels to `id + name` (role text stays in the table, not
  duplicated), added a Mermaid `init` directive for tighter spacing/font, and
  pruned A6 Curator's edges from all nine agents down to three representative
  ones. That pruning matters for more than size: the original SVG draws those
  nine lines at ~0.1 opacity — decoration meaning "everything feeds the
  memory hub," not nine equally load-bearing connections — so drawing all
  nine at full weight had overstated them, independent of the overflow issue.

## [2026-08-20] refactor | bring the notebook in as a git-ignored, schema-exempt folder

msilva had started a separate, deliberately ungoverned Obsidian vault at
`Documents\notes` for loose Livemode-work-related thoughts (this session
named it **notebook**), kept outside this repo so it would never be indexed
or maintained by me. He asked to move it inside `work/` for the convenience
of one Obsidian window, without giving up that separation.

- Moved: `Documents\notes` → `work\notebook` (contents untouched —
  `.obsidian/` config and `Fluxo agêntico.md`).
- `.gitignore`: added `notebook/` — its contents are never tracked.
- `CLAUDE.md`: added `notebook/` as a fifth root exemption alongside
  `CLAUDE.md`/`AGENTS.md`/`index.md`/`log.md` — no frontmatter, no indexing,
  no linking from wiki pages, read only on explicit request, never edited or
  reorganized by me. Treated like `raw/` in spirit (hands off) but not a
  source to ingest.
- `index.md`: not updated — the folder is explicitly exempt from indexing,
  same as the four other root files.

## [2026-08-20] ingest | primary sources behind Akita's anti-orchestration citations

Akita's post ([[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]])
cited Cognition's "Don't Build Multi-Agents" and Anthropic's "How we built
our multi-agent research system" secondhand, for their caveats only. msilva
asked for both ingested directly.

- Read `raw/Clippings/Don't Build Multi-Agents.md` in full (Walden Yan,
  Cognition, 2025-06-12). New: [[Don't Build Multi-Agents]] — two context-
  engineering principles (share full agent traces; actions carry implicit
  decisions that conflict in parallel), the Flappy Bird worked example, and
  the Claude Code subagent data point (June 2025: never parallel to the main
  agent, scoped to Q&A only).
- `raw/Clippings/How we built our multi-agent research system.md` turned out
  to contain only frontmatter and a title — the clipping never captured the
  body. Fetched the live page instead
  (https://www.anthropic.com/engineering/multi-agent-research-system) via
  WebFetch, 2026-08-20. New: [[How we built our multi-agent research system]] —
  flagged inline that its content is sourced from a live fetch, not the raw
  clipping.
- **Correction to the wiki's own framing, not just new content**: reading
  Anthropic's post directly shows Akita's citation was selective — that
  system beat single-agent Opus 4 by **90.2%** on their eval; the post is a
  build report with caveats, not a warning-only piece. The real claim in
  both primary sources is a **fit test** (parallel/independent/high-value
  work vs. shared-context/interdependent work, most coding), not a blanket
  verdict against multi-agent.
- **Sharpened the counterpoint against [[Agent Flow]]**, checked against
  [[Fluxo Agêntico diagram]]'s actual edges rather than the verbal
  description: A10↔A11↔A12 are bidirectional, A5→A3 is a direct feedback
  loop, every agent feeds A6 Curator (a shared-context hub), and A7→A8→A9
  runs on one shared upfront PRD — none of this is the independent-parallel
  shape either source calls a good fit. The one candidate exception (A3/A9's
  on-demand sub-agents, if genuinely independent) is an open question, not
  settled by the current spec.
- Updated: [[Agent Flow]] (new callout sharpening and partially complicating
  the existing Akita counterpoint), [[Claude Agent SDK]] (Claude Code
  subagent data point, corroborating Luís's harness-scope framing a year
  early), [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]]
  (added the two missing URLs plus the Berkeley study link, flagged the
  selective-citation correction), `index.md`.

## [2026-08-20] lint | Eleventh health check
- Scoped to the 12 files changed since the tenth lint (`d8a0850`), plus the
  usual whole-vault mechanical sweep. Reported before changing anything.
- **Mechanical checks, whole vault**: zero orphans (besides the four
  schema-exempt infra pages), one known/accepted dead-end
  ([[Zed Cheatsheet]]), frontmatter complete everywhere, nothing near the
  30-day staleness threshold, zero outstanding `[!msilva]` callouts. Zero new
  broken wikilinks beyond one real finding below — the rest of the regex hits
  are the same known false positives as the tenth lint (line-wrapped links
  resolving fine, and literal `[[Page]]`/`[[people/]]` example text in
  `CLAUDE.md`/`log.md`).
- **Read the 12 changed files for contradictions.** Three real findings, not
  yet fixed:
  1. **Stale claim, not yet corrected**: [[2026-08-18 Proxy first, defer
     LiveScript-side SDK changes]] (still `updated: 2026-08-19`) lists
     `PRO-82` under "What this blocks," but `PRO-82` was resolved the next day
     — recorded correctly on [[Airtable Proxy]] and in this log. `index.md`'s
     Decisions-section summary of this same page repeats the stale "Blocks
     `PRO-76` and `PRO-82`" line.
  2. **Wikilink to a non-page**: the same decision page links
     `[[Proxy em produção validado c/ LiveScript]]` — a Linear project name,
     referenced everywhere else in the vault as plain text or a Linear URL,
     not a wiki entity with its own page. The one `[[...]]` instance is an
     outlier, not an intentional to-do marker for a page that should exist.
  3. **Citation byte-mismatch**: [[Don't Build Multi-Agents]]'s `source:`
     field and the wiki page's own filename use a straight apostrophe, but
     the real raw file (`raw/Clippings/Don't Build Multi-Agents.md`) uses a
     curly right single quote (U+2019) — confirmed by inspecting the raw
     filename's bytes. The citation doesn't exactly match the file it points
     at.
- **Raw-layer anomaly, not a wiki defect**: `raw/Clippings/How we built our
  multi-agent research system.md` — the file [[How we built our multi-agent
  research system]] cites as its `source:` — no longer exists in `raw/`.
  It was there and empty (frontmatter + title only) during the 2026-08-20
  ingest; something removed it since. `raw/` is off-limits to touch or
  investigate further on my own initiative — flagged for msilva, not acted
  on. The page's own content is unaffected since it was written from a live
  WebFetch, already flagged inline as such.
- Not done (at the time of the report): none of the three wiki-side findings
  fixed yet, per the report-before-changing rule. Also not re-verified:
  [[Agent Flow]] and [[Airtable Proxy]]'s prior history (30+ and 25+ commits
  respectively) — only the diffs since the tenth lint were read in full,
  consistent with the ninth/tenth lints' own scoping choice.

## [2026-08-20] refactor | Lint fixes — stale PRO-82 claim, stray wikilink, citation byte-mismatch
- msilva asked for the three findings from the eleventh lint fixed.
- **[[2026-08-18 Proxy first, defer LiveScript-side SDK changes]]**: struck
  through the `PRO-82` line under "What this blocks" and added a visible
  **Resolved 2026-08-20** correction pointing at Luís's actual decision and
  [[Airtable Proxy]] — `PRO-76` remains genuinely open, `PRO-82` no longer
  blocked by this scope rule. The stray `[[Proxy em produção validado c/
  LiveScript]]` wikilink (a Linear project name, not a wiki entity) changed to
  plain quoted text, matching how it's written everywhere else in the vault.
  `updated:` bumped to 2026-08-20.
- **`index.md`**: the Decisions-section line for the same page rewritten to
  drop the stale "Blocks `PRO-76` and `PRO-82`" and state the current split —
  `PRO-76` still blocks, `PRO-82` resolved. `updated:` bumped to 2026-08-20.
- **[[Don't Build Multi-Agents]]**: `source:` frontmatter field corrected to
  use the real raw filename's curly right single quote (U+2019) instead of a
  straight apostrophe — confirmed byte-for-byte against
  `raw/Clippings/Don't Build Multi-Agents.md`. The wiki page's own filename
  and title are unaffected — wiki naming is Title Case/readable by
  convention, independent of the raw file's exact characters; only the
  citation pointer needed to match exactly.
- Not touched: the missing-raw-file anomaly on [[How we built our
  multi-agent research system]] — still flagged for msilva, not a wiki-side
  fix.

## [2026-08-20] ingest | 1:1 Matheus - Gabrielle (2026-08-20)
- Source: `raw/1_1 Matheus _ Gabrielle - 2026_08_20 15_17 GMT-03_00 -
  Anotações do Gemini.md`. Machine transcription, attribution fully collapsed
  onto "Gabrielle Ferreira" — worse than prior 1:1s, but content reads as a
  genuine back-and-forth check-in rather than one continuous voice; turns
  inferred from phrasing.
- Discussed with msilva before writing: confirmed "Ias" (transcription
  artifact) is Yasmin Macedo, getting more of Luís's time tied to Farol
  GCP/deploy ramp-up; asked to keep the personal/emotional content (feeling
  timid, adjusting to in-person work, comparisons to peers) lighter than the
  established candor precedent for prior 1:1s — written accordingly, kept to
  neutral, professionally-relevant framing.
- New: `meetings/2026-08-20 1-1 Matheus - Gabrielle.md`.
- Updated: `projects/Agent Flow.md` (msilva's own reconsideration of the
  14-agent diagram's shape, reinforcing the existing Akita counterpoint),
  `people/Gabrielle Ferreira.md` (leave detail refined), `people/Carolina
  Bezerra.md` (looped into Agent Flow conversations, weekly check-in),
  `people/Yasmin Macedo.md` (getting more of Luís's time), `index.md`.
- No new decisions — `PRO-94` (Pulumi language) confirmed still open, gated
  on Luís, who was sick Tue–Thu this week.

## [2026-08-20] decision | Pulumi IaC language: Go, aligned with Luís
- `PRO-94` resolved same day as the above 1:1 note (which had it still open,
  gated on Luís): msilva aligned with Luís on **Go**, matching msilva's
  stated lean (toolchain/CI consistency with the proxy).
- Updated: `decisions/2026-08-18 Bring options to Luís before deciding,
  communicate async and often.md` (Applied since — PRO-94 bullet resolved),
  `projects/Airtable Proxy.md` (open question resolved).

## [2026-08-20] ingest | 1:1 Matheus - Luís (2026-08-20)
- Source: `raw/Matheus _ Luis - 2026_08_20 16_55 GMT-03_00 - Anotações do
  Gemini.md`. Machine transcription, attribution fully collapsed onto
  "Matheus Oliveira da Silva" for every turn — same failure mode as the
  same-day Gabrielle transcript. Turns inferred from phrasing; attribution
  confirmed directly with msilva before writing (Luís = coaching/discovery
  feedback voice, msilva = the agir/informar analysis and memory-structure
  questions).
- New: `meetings/2026-08-20 1-1 Matheus - Luís.md`.
- Updated: `projects/Agent Flow.md` (new **agir vs. informar** heuristic for
  agent-vs-skill classification; A4 Teacher's status as a standalone agent
  reopened; Luís's same-day, independent reinforcement of msilva's own
  14-agent-diagram doubts), `people/Luís Fernandez.md` (agents-as-colleagues
  framing, discovery-first push), `index.md`.
- No decisions made this call — coaching and framework-building only. Two
  open threads for msilva to chase directly: which two of the 14 agents
  Luís considers redundant (not named in the transcript), and how much of
  this Gabrielle already independently agrees with.
- Also found and fixed while cross-checking: the 16:55 meeting page above had
  never been added to `index.md` (an un-indexed page from that session) —
  added below.

## [2026-08-20] ingest | Fluxo Agêntico diagram walkthrough with Luís
- Source: `raw/Luis _ Matheus - 2026_08_20 18_22 GMT-03_00 - Anotações do
  Gemini.md`. A second, later Luís call the same day (18:22, vs. the 16:55
  coaching call above) — the actual per-agent walkthrough of the 14-agent
  diagram, msilva running the same exercise he'd already run with Gabrielle
  on 2026-08-19. Machine transcription, attribution fully collapsed onto
  msilva except the opening line; turns inferred from phrasing and confirmed
  directly with msilva before writing (seven specific points checked one by
  one: Luís pushed to eliminate A6 as standalone; the later "organize memory
  well" line is about memory-as-infrastructure, not about Curator being
  necessary; A3 and A9 are literally the same agent, not merely similar; the
  A8+A9≈Claude Code read; the actor→input→output working-method shift;
  Luís's LangGraph-vs-Claude experiment task; and the third-voice read on
  Gabrielle's Portfolio-first pain).
- New: `meetings/2026-08-20 Fluxo Agêntico diagram walkthrough with Luís.md`.
- Updated: `projects/Agent Flow.md` (A6 Curator resolved as not-a-standalone-
  agent while the underlying memory-organization need stays critical and
  unowned; A3/A9 merge reverses the 2026-08-19 split; A8+A9 collapse toward
  "Claude Code itself"; only A13 fits "transversal intelligence," narrowing
  the A10/A13-merge question toward no; new actor→input→output working
  method, deferring inter-agent graph design; Discovery-vs-PRD-generation
  split added as open; LangGraph-vs-Claude validation experiment and the
  memory-source/per-agent-tooling inventories added as next steps; A4
  Teacher's standalone status gets a second, input/output-based argument for
  survival), `people/Luís Fernandez.md` (the verdict pass, the working
  method, the validation-experiment task), `syntheses/Which agent should be
  built first.md` and `syntheses/Comparing the first-agent candidates.md`
  (Gabrielle's own stated pain as a third independent voice for A10+A14 over
  A1+A2 — not yet folded into either page's formal recommendation), `index.md`.
- No new decisions on which agent to build first — msilva's own position
  (A1+A2) stands, now against three independently-arrived-at voices (Carol,
  msilva's own Pain #1, Gabrielle) favoring A10 Portfolio + A14 PM Agent.

## [2026-08-21] refactor | purge leaked secrets from git history, first push to remote
- msilva asked to push the vault to `git@github.com:msilva-sys/wiki.git`
  (previously local-only). No remote had been configured yet.
- The only SSH key on this machine authenticated as the `tech-livemode` GitHub
  account; the target repo belongs to the separate personal `msilva-sys`
  account. Generated a dedicated keypair, added a `github-personal` host alias
  in `~/.ssh/config`, msilva added the public key to `msilva-sys`'s GitHub
  account, remote re-pointed at the alias.
- First push attempt was rejected by **GitHub push protection**: an Airtable
  PAT was live in two commits. This vault's own `systems/Proxy Environments.md`
  had already flagged this exact gap on 2026-08-17 (redaction landed in the
  working tree; a history purge was attempted, blocked by a permission layer,
  and never completed) — the push attempt is what finally forced it.
- Scoped the exposure: real secrets existed only in the two root commits
  (`Snapshot: vault before LLM-wiki setup`, `LLM-wiki setup — schema, index,
  log, folder structure`) — five values total: Airtable PAT, Firebase
  service-account private key, LogRocket API key, Bugsnag server key,
  integration API key. Every commit after the 2026-08-17 redaction was clean.
  (An initial grep sweep over-counted ~70 "hits" — a false positive from a
  later commit's danger-callout quoting the grep pattern as documentation.)
- Used `git filter-branch --tree-filter` (built into git; no external tool
  install) to redact the five values at those two commits only, preserving all
  96 commits' messages and dates. Verified zero matches for the actual secret
  values across `git rev-list --all` after deleting the `refs/original/*`
  safety refs filter-branch leaves behind and running `git gc --prune=now`.
  Pushed the cleaned history; push succeeded.
- Updated: `systems/Proxy Environments.md` (danger callout rewritten from
  "not resolved" to "purge completed 2026-08-21," corrected the credential
  count from three to five, kept the rotation task open).
- **Open, msilva's to perform: rotate all five credentials.** They sat in
  plaintext on disk since 2026-08-11; the git purge does not undo that
  exposure window regardless of whether the push protection would have caught
  it. Treat all five as potentially compromised.

## [2026-08-21] synthesis | Fluxo Agêntico convergences published to Linear
- msilva asked to get the key points from
  [[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]] into Linear so
  the team can access them, without going through the wiki.
- Found the existing Linear structure first rather than assuming one: team
  **PRO** (Projetos-livemode), project **Fluxo Agêntico** (under initiative
  *Fluxo agêntico*), which already carries two Documents (the architecture
  diagram, the project instruction). Matched that pattern rather than
  creating an issue.
- Drafted the content as an HTML artifact first for review — msilva's
  request mid-task — then two rounds of correction before publishing:
  1. Standing global convention (never name a colleague in Linear-visible
     text) does **not** apply here — msilva explicitly said Luís can be
     named, and each verdict is attributed to him directly.
  2. msilva flagged the framing as reading too much like a settled
     spec/PRD. Retitled the doc from "Decisões de arquitetura" to
     **"Convergências da revisão dos 14 agentes"**, renamed the section
     from "Decisões" to "O que convergimos," and added an explicit line
     that the document is not a spec or PRD and does not lock the
     architecture.
- Published: Linear document
  [Convergências da revisão dos 14 agentes (2026-08-20)](https://linear.app/projetos-livemode/document/convergencias-da-revisao-dos-14-agentes-2026-08-20-26cc437061a9)
  under project Fluxo Agêntico. Seven points carried over: A1+A2 merge,
  A6 not standalone (memory-as-infra still critical), A3=A9, A8+A9≈Claude
  Code, only A13 is transversal, actor→input→output working method,
  default-through-classifier. Plus next steps (LangGraph vs. Claude
  experiment, memory-source inventory, per-agent tooling) and two open
  questions.
- Nothing changed on the wiki page itself — this operation only pushed
  existing wiki content outward to Linear.

## [2026-08-21] query | Harness framing for A10-A13, missed on first extraction
- msilva asked what Luís said about the transversal-intelligences agents
  being "skills or steps on a CI/CD" — no literal CI/CD anywhere in the
  vault or the raw transcript, but re-reading
  `raw/Luis _ Matheus - 2026_08_20 18_22 GMT-03_00 - Anotações do Gemini.md`
  in full (160 lines, not fully covered by the first extraction) surfaced a
  closer match at 00:34:34: Luís calls A13 (dedup) and A12 (data gov) a
  **"harness"** — a rule-enforcement/guardrail layer, closer to an automated
  gate than to something that reasons.
- Updated: [[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]] (new
  Facts-stated bullet and quote); [[Agent Flow]] (harness framing added under
  the existing "only A13 is transversal" callout, as the mechanism behind
  that split, not a new decision).

## [2026-08-21] synthesis | Linear doc restructured as a per-agent walkthrough
- msilva asked for the Linear document (from the 2026-08-21 synthesis entry
  above) to be restructured: instead of a flat list of seven convergence
  points, walk through **every one of the 14 agents**, matching how the
  actual 2026-08-20 conversation with Luís went — agent by agent, same as
  he'd already done with Gabrielle.
- Rewrote the Linear document in full (same id/URL, title unchanged —
  "Convergências da revisão dos 14 agentes" still fits a per-agent record):
  one section per agent A1–A14, each stating what it is and what the
  conversation actually covered for it — including points that don't
  resolve to a clean verdict (A3's non-code scope, A5's auto-trigger
  question, A7-vs-PRD-generation split) rather than only the seven that
  landed as firm calls.
- Folded in the "harness" detail from the entry above under A12/A13,
  per msilva's confirmation to add it to the Linear doc too.
- Kept the not-a-spec/PRD framing and the "O que convergimos"-style caveat
  from the previous version; nothing on the wiki page itself changed by
  this operation beyond what the entry above already recorded.

## [2026-08-21] synthesis | Same treatment for the Gabrielle diagram walkthrough
- msilva asked for the same per-agent Linear treatment for
  [[2026-08-19 1-1 Matheus - Gabrielle]] (the earlier full 14-agent
  walkthrough, done with Gabrielle before the Luís one). Reviewed the draft
  with him in chat first, same as the Luís version.
- Flagged two real differences before drafting, both confirmed with msilva:
  1. This meeting is thinking-out-loud, not a decision meeting — the wiki
     page itself says so explicitly. No verdicts to report the way the Luís
     doc reports verdicts.
  2. Attribution in this transcript is **fully** collapsed (unlike the Luís
     one, which was partial and confirmed with msilva afterward) — the wiki
     page already flags any specific claim about what Gabrielle thinks as
     unconfirmed. msilva chose to name her the same way as Luís anyway; the
     doc carries an explicit source note saying attributions to her follow
     msilva's own read, not a confirmed quote.
  3. A4 Teacher was not actually discussed in this meeting — msilva starts
     to mention it and self-corrects mid-sentence to talk about A3 instead.
     Recorded as "not discussed" rather than inventing content for it.
- Published: Linear document
  [Passagem pelos 14 agentes com Gabrielle (2026-08-19)](https://linear.app/projetos-livemode/document/passagem-pelos-14-agentes-com-gabrielle-2026-08-19-177b8945bb1a),
  project Fluxo Agêntico. Per msilva's request, kept only the per-agent
  sections (A1–A14) — dropped the standalone "which agent first" and "open
  questions" roll-up sections that the review draft had, though open
  questions specific to one agent's own discussion stayed inline under that
  agent.
- Nothing changed on the wiki page itself — same as the Luís operation, this
  only pushed existing wiki content outward to Linear.

## [2026-08-21] ingest | Agent Harness Template (Luís, Slack message)
- msilva pasted a Slack message from Luís proposing a generic agent spec:
  Trigger (Human|Machine) + Trigger Channel → Input → Harness (Soul.md,
  Skills, Tools, MCP) → Output. No `raw/` file — pasted directly into the
  session, not a transcript or document.
- Discussed with msilva first (per the ingest procedure) before writing:
  flagged that it refines the actor→input→output method from
  [[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]], and that it
  gives "harness" — used twice before in different senses (project-harness
  scope for dev subagents on [[2026-08-19 1-1 Matheus - Luís]]; A12/A13 as a
  rule-enforcement harness on the 2026-08-20 walkthrough) — one consistent
  meaning for the first time.
- New: [[Agent Harness Template]] (status `draft` — a proposed template, not
  yet validated against the 14-agent list). Includes msilva's own candidate
  synthesis (flagged as unconfirmed) that A12/A13 being called "harness"
  rather than "agent" may mean they run on Skills+Tools with no real
  Soul.md — consistent with the agir/informar heuristic.
- Updated: [[Agent Flow]] (linked under the actor→input→output working
  method); `index.md`.

## [2026-08-21] refactor | add README.md, amend schema
- Now that the vault is pushed to a real GitHub remote, added a `README.md`
  landing page (points to `CLAUDE.md` and `index.md`, notes raw/ lives outside
  the repo).
- Amended `CLAUDE.md`'s root-layout list and frontmatter-exemption clause to
  include `README.md` as infrastructure, rather than letting root drift from
  what the schema documents.

## [2026-08-21] callout | Two `[!msilva]` callouts on Agent Harness Template
- Ran `notes` at session start. Found two unresolved `[!msilva]` callouts on
  [[Agent Harness Template]], added directly in Obsidian after the ingest that
  created the page, not yet referenced here. Discussed both with msilva:
  1. **"Some agents does not need to have this harness (maybe theyre part of
     another agent harness)"** — confirmed: some of the 14 agents may not be
     top-level entries at all, but invoked as a Tool/Skill *inside* a parent
     agent's harness, with no standalone Trigger/Input/Output. This sharpens
     (doesn't just restate) the existing "maybe just a skill" flag on A4
     Teacher and the transversal group. Folded into page prose; opened a
     narrower open question — which agents nest, and into whose harness —
     since msilva's answer resolves the general case but not the specific
     mapping.
  2. **"Is Trigger Channel meant to replace or sit alongside the four entry
     channels? / No"** — confirmed as "sits alongside": Trigger Channel is a
     separate axis from [[Agent Flow]]'s four entry channels, both coexist.
     Folded into page prose as settled, no follow-up question needed.
- Both callouts deleted from the page after folding.

## [2026-08-21] decision | Private Airtable Proxy infrastructure behind VPN
- Discussed the IaC shape with msilva before writing. Settled production access
  as private at `https://proxy.livemode.com`, reached through the corporate VPN,
  GCP VPC, private DNS, a regional internal HTTPS load balancer/serverless NEG,
  and Cloud Run with `internal` ingress.
- Settled the first-phase trust model: app-id in the URL path selects the PAT,
  base allow-list, and telemetry identity but does not authenticate the caller;
  network reachability is the initial gate. A separate app key is explicitly a
  future layer, while the VPN boundary remains.
- New: [[2026-08-21 Deploy Airtable Proxy privately behind VPN]] — includes the
  request path, split-horizon DNS/TLS boundary, Pulumi vs. central-infra
  ownership, secrets, egress, future app-key phase, and pre-code open inputs.
- Reconciled [[2026-08-19 Identify proxy apps by URL path, not header]] and
  [[Airtable Proxy]] with repo ground truth: path routing is no longer pending;
  it shipped in `8e4297b` and was hardened in `bf5e681`/`12d1423`.
- Updated: `index.md` (project timeline + both decision entries).

## [2026-08-24] refactor | correct proxy domain TLD to .space
- msilva, in chat: "todos os nossos domínios terminam em *.livemode.space,
  exemplo: proxy.livemode.space." Several pages had recorded the proxy's
  domain as `proxy.livemode.com` — wrong TLD, not a typo caught earlier.
  [[Orca (CDE)]] and [[Orca Next Version]] already had it right
  (`cde.livemode.space` / `orca.livemode.space`), as did the 2026-08-10
  onboarding meeting note's illustrative `airtable.livemode.space` — so this
  was an inconsistency between pages, not a vault-wide error.
- Corrected `proxy.livemode.com` -> `proxy.livemode.space` (and the bare
  `livemode.com` DNS-zone reference) in: `index.md`,
  [[2026-08-19 Identify proxy apps by URL path, not header]],
  [[2026-08-21 Deploy Airtable Proxy privately behind VPN]] (incl. its alias
  and the still-open DNS-zone-ownership line), [[Airtable Proxy]] (5
  occurrences), and the superseded synthesis
  [[How LiveScript sends the proxy X-App-Id header]].
- Left `log.md`'s own prior entries (2026-08-19, 2026-08-21) unchanged — they
  quote what was written at the time, and this log is append-only.
  `systems/Proxy Environments.md`'s `livemode.com` hits are email addresses
  (`CHANGE_HISTORY_ADMIN_EMAILS`), unrelated to the service domain, not
  touched.
- Updated: `index.md`.

## [2026-08-24] ingest | Fluxo Agêntico diagram v0 (earlier PNG render)
- Read `sources/fluxo agêntico diagrama 0.png` in full (resized earlier this
  session, 2050x2450 -> 643x768, at msilva's request, purely for on-screen
  display — no content change).
- Compared node-by-node against the already-ingested
  [[Fluxo Agêntico diagram]] (from `raw/fluxo_agentico_ajustado (2).html`).
  Found no structural or content differences — same 14 agents, roles, three
  branches, four entry channels, colors, and both named dashed context-edges.
- Discussed with msilva before writing: confirmed this PNG is an **earlier**
  version of the already-ingested HTML source, not an independent or later
  one, and confirmed the two extra side-annotation labels visible in this
  render (`Desenvolvimento` next to A8, `Sub-agentes` next to A9) are real
  and should be folded into the existing page rather than duplicated.
- New: `sources/Fluxo Agêntico diagram v0.md` — provenance record, flags that
  the underlying PNG is filed at `sources/` rather than `raw/` (not moved,
  per the rule against touching the raw layer on my own initiative).
- Updated: `sources/Fluxo Agêntico diagram.md` (table gained the two missing
  side-labels, callout documenting why), `index.md`.

## [2026-08-24] refactor | drop the v0 source page, fold into the main diagram page
- msilva: "não há necessidade desse v0" — the separate provenance page from
  the entry above was unwarranted overhead for a render with no structural
  differences from [[Fluxo Agêntico diagram]].
- Deleted: `sources/Fluxo Agêntico diagram v0.md`.
- The actual finding survives — the "Desenvolvimento"/"Sub-agentes" labels
  stay in [[Fluxo Agêntico diagram]]'s table — only the separate-page
  treatment is retracted. Reworded that page's callout and `index.md`'s
  entry to cite `sources/fluxo agêntico diagrama 0.png` directly instead of
  a wikilink to the now-deleted page.
- Updated: `sources/Fluxo Agêntico diagram.md`, `index.md`.

## [2026-08-24] refactor | Schema: wiki content converts to pt-BR
- msilva: "gostaria de modificar a wiki para ptBR, mantendo só os jargões e
  expressões tech em inglês" — added an **Idioma** section to `CLAUDE.md`.
- New content (page prose, `index.md` catalog lines, `log.md` entries) is
  pt-BR from now on. English stays only for technical jargon/proper nouns
  (system names, frontmatter keys/values, filenames, wikilink targets, code
  blocks) and for verbatim quotes originally spoken in English.
- `CLAUDE.md` / `AGENTS.md` / `README.md` stay in English — agent-operation
  docs, not vault content.
- Migration is gradual, not a mass rewrite: existing English pages convert
  to pt-BR prose only when next touched for another reason. `log.md`'s past
  entries are never rewritten (append-only); only new ones follow pt-BR.
- Updated: `CLAUDE.md`.

## [2026-08-24] lint | Décimo segundo health check

- Escopo: os 20 arquivos de conteúdo alterados desde o último lint (commit
  `65c6a69`), lidos por dois agentes em paralelo, mais a varredura mecânica
  de sempre no vault inteiro.
- **Mecânico, vault inteiro**: 0 órfãs, 1 dead-end conhecido/aceito
  ([[Zed Cheatsheet]]), 0 páginas `status: active` estagnadas (a mais antiga
  tem 13 dias), 0 callouts `[!msilva]` pendentes, 0 wikilinks quebrados
  novos — os hits de regex restantes são os mesmos falsos-positivos já
  documentados no 9º/11º lints (`log.md` citando literalmente suas próprias
  entradas passadas; `[[Page]]`/`[[people/]]` como exemplos do próprio
  schema).
- **Achado confirmado, sem mudança**: `raw/Clippings/How we built our
  multi-agent research system.md` continua ausente — já flagado no 11º
  lint, já documentado inline em [[How we built our multi-agent research
  system]].
- **Duas fontes em `raw/Clippings/` sem página em `sources/`**: `Harness
  engineering for coding agent users.md` (Böckeler/Martin Fowler) e `How to
  Build a Custom Agent Harness.md` (Runkle/LangChain), ambas capturadas
  2026-08-21, mesmo tema (harness).
- **Contradições reais, não corrigidas ainda** (quatro, relatadas a
  msilva antes de qualquer mudança):
  1. [[Proxy Environments]] ainda descrevia o patch de header `X-App-Id`
     como implantado e necessário — foi revertido 2026-08-18 e toda a
     abordagem por header foi substituída por roteamento via path
     ([[2026-08-19 Identify proxy apps by URL path, not header]]).
  2. [[How LiveScript sends the proxy X-App-Id header]] tinha um banner
     dizendo "Not yet implemented", mas a identificação por path já estava
     implementada e endurecida (commits de 2026-08-20/21).
  3. [[2026-08-18 Bring options to Luís before deciding, communicate async and often]]
     dizia que a decisão do SDK patch estava "aberta para revisão" — na
     verdade foi extinta no dia seguinte pela decisão de path, não apenas
     reaberta.
  4. [[Agent Flow]] tinha contradição interna e cross-page sobre o status da
     fusão A1+A2: "Open questions" tratava como posição não resolvida,
     enquanto outro trecho da mesma página já tratava como fundida, e
     [[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]] já
     registrava isso como decisão reconfirmada por Luís. [[Luís Fernandez]]
     também omitia essa fusão na lista das outras da mesma reunião.
- Não corrigido por decisão consciente: pergunta em aberto de
  [[2026-08-20 1-1 Matheus - Luís]] respondida 90 min depois na reunião
  seguinte — tratado como aceitável por serem notas de reunião (fotografia
  no tempo), não uma contradição a corrigir.

## [2026-08-24] refactor | Correção dos quatro achados do 12º lint

- msilva pediu para aplicar os quatro achados de contradição do lint acima.
- **[[Proxy Environments]]**: reescrito o callout "Uncommenting alone is not
  enough" para registrar a linha do tempo completa — header shipado
  2026-08-17, revertido 2026-08-18, todo o approach substituído por path em
  2026-08-19 — em vez de descrever o header como solução atual.
- **[[How LiveScript sends the proxy X-App-Id header]]**: banner corrigido
  de "Not yet implemented" para "Implemented 2026-08-20/21", citando os
  commits que endureceram o roteamento por path.
- **[[2026-08-18 Bring options to Luís before deciding, communicate async and often]]**:
  frase sobre a decisão do SDK patch estar "aberta para revisão" reescrita
  para deixar claro que ela foi extinta no dia seguinte, não apenas
  reaberta.
- **[[Agent Flow]]**: "Is A1 + A2 one agent or two?" riscado e marcado como
  fundido/reconfirmado 2026-08-20, citando a fala de Luís
  ("Para mim eles são a mesma coisa"). **[[Luís Fernandez]]**: adicionada a
  fusão A1+A2 à lista de vereditos da mesma reunião, que antes só citava
  A6/A3-A9/A8-A9/transversais.
- Updated: `systems/Proxy Environments.md`,
  `syntheses/How LiveScript sends the proxy X-App-Id header.md`,
  `decisions/2026-08-18 Bring options to Luís before deciding, communicate async and often.md`,
  `projects/Agent Flow.md`, `people/Luís Fernandez.md`.

## [2026-08-24] ingest | Duas fontes externas sobre harness (Böckeler, LangChain)

- Lidas em full as duas clippings flagadas pelo lint acima, mesmo lote
  (`raw/Clippings/`, capturadas 2026-08-21), ambas sobre harness de agentes.
- New: [[Harness engineering for coding agent users]] — modelo mental de
  Böckeler/Martin Fowler (guias/sensores × computacional/inferencial; três
  categorias de harness, sendo "behaviour" a mais difícil). Complementa,
  não repete, o [[Agent Harness Template]] de Luís — eixo de ciclo de
  controle, não de composição.
- New: [[How to Build a Custom Agent Harness]] — guia prático de
  `create_agent`/middleware da LangChain (Runkle). `HumanInTheLoopMiddleware`
  e `SubAgentMiddleware` mapeiam diretamente em duas perguntas em aberto do
  [[Agent Flow]]: onde ficam os gates humanos, e como A3/A9 criam
  sub-agentes sob demanda.
- Updated: [[Agent Harness Template]] (nova seção citando as duas fontes),
  `index.md`.

## [2026-08-24] decision | manage Airtable Proxy secrets in the codebase before Secret Manager
- During the `PRO-91` implementation session (Pulumi program for Cloud Run +
  Secret Manager), msilva relayed a call from Luís: `PROXY_APPS` stays
  managed as a Pulumi secret config value for now, wired straight into the
  Cloud Run env var, rather than moving to GCP Secret Manager. Secret
  Manager remains the eventual target, just deferred, not dropped.
- No transcript exists for the source conversation — same gap already
  flagged 2026-08-18 for an earlier, separate Luís/Secret-Manager
  conversation that was also never ingested. Flagged again here rather than
  silently treated as equivalent to a wiki-sourced decision.
- New: [[2026-08-24 Manage Airtable Proxy secrets in the codebase before Secret Manager]].
- Updated: [[2026-08-21 Deploy Airtable Proxy privately behind VPN]] (inline
  correction under "Pulumi ownership" and "Secrets" — Secret Manager
  resources deferred; "still open" list item on secret-delivery procedure
  marked settled for this phase); [[Airtable Proxy]] (PRO-91 coded —
  `infra/pulumi/`: API enablement, Artifact Registry repo, runtime service
  account, Cloud Run v2 service; NEG/LB/DNS left for a later `PRO-90`
  sibling); `index.md`.

## [2026-08-24] refactor | traduzir o prep de discovery com a Carol para pt-BR
- msilva pediu a tradução do meeting prep da Carol para pt-BR, seguindo a
  convenção "Idioma" decidida em 2026-08-24 no `CLAUDE.md`. Página tocada
  pela primeira vez desde a decisão, então migra nesta mesma passada em vez
  de esperar por outro motivo.
- Atualizado: [[Meeting prep - Agent Flow discovery with Carol - 2026-08-24]]
  — prosa inteira traduzida para pt-BR; frontmatter, wikilinks, nomes de
  agente (A1–A14, Watcher, Curator) e a citação verbatim em português
  (*"a ideia é não ser só o nosso time... a empresa toda"*) mantidos como
  estão, conforme a regra de idioma. Sem mudança de conteúdo ou de estrutura
  — só tradução. `updated:` avançado para 2026-08-24.
- Updated: `index.md` (linha reescrita em pt-BR, nota de tradução
  adicionada).

## [2026-08-24] refactor | explicitar convergências com Luís e Gabi no prep da Carol
- msilva pediu para explicitar, no mesmo prep, pontos já conversados com o
  Luís e a Gabi e onde eles convergem — a doc original (2026-08-19) só
  comparava contra a conversa com a Gabi, e quatro conversas com o Luís
  desde então ([[2026-08-18 1-1 Matheus - Luís]],
  [[2026-08-19 1-1 Matheus - Luís]], [[2026-08-20 1-1 Matheus - Luís]],
  [[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]) tinham
  material direto para os mesmos cinco temas, nunca cruzado com esta doc.
- Sem registro de que a conversa com a Carol já tenha efetivamente
  acontecido — sinalizado explicitamente na própria doc em vez de assumido
  silenciosamente, já que o material acrescentado é cronologicamente
  posterior à data no título da página (2026-08-19).
- Atualizado: [[Meeting prep - Agent Flow discovery with Carol - 2026-08-24]]
  — novo callout e seção "Convergências já mapeadas antes desta conversa";
  coluna extra na tabela de comparação rápida; bloco "Da conversa com o
  Luís" acrescentado em cada uma das quatro seções de tema que tinham
  material correspondente (qual agente primeiro, A6 vs. repositório de
  skills, A10/intranet, minimalismo de contexto — este último já incluía o
  Luís na conversa original, só não estava explícito); checklist final
  ajustada. Principais convergências registradas: (1) dor de visibilidade
  cross-project agora em três vozes independentes — Carol, Luís, e
  Gabrielle relatada pelo Luís — apontando para A10 Portfolio + A14 PM
  Agent; (2) A6 Curator não existe como agente autônomo por ora (veredito
  do Luís), o que muda a pergunta sobre onde termina o repositório de
  skills; (3) instrução direta do Luís para entender o apetite de ritmo da
  Carol antes de desenhar estrutura de memória; (4) A10 confirmado distinto
  de A13 Deduplication.
- Updated: `index.md`.

## [2026-08-24] refactor | reescrever o prep da Carol bem mais enxuto
- msilva: a passada anterior ficou text-heavy demais — meeting preps
  precisam ser mais concisas. Feedback de processo, não específico desta
  doc; guardado em memória para as próximas.
- Reescrito [[Meeting prep - Agent Flow discovery with Carol - 2026-08-24]]
  do zero: os blocos de prosa "Da conversa com a Gabi" / "Da conversa com o
  Luís" repetidos em cada seção de tema foram cortados; todo o conteúdo
  informativo (o que cada um já disse, convergências) agora vive só na
  tabela de comparação, uma linha por tema, três colunas (Gabi/Luís/Carol).
  Perguntas viraram bullets de uma linha em vez de parágrafos. Nenhum fato
  ou convergência removido do conteúdo anterior — só a verbosidade.
- Updated: `index.md` (linha também encurtada).

## [2026-08-24] decision | despriorizar A5 Watcher como candidato a primeiro agente
- msilva: *"o Watcher é só mais um agente. Nós não precisamos focar nele."*
  Perguntei o escopo (só o prep da Carol, também a análise de "qual agente
  primeiro", ou também o design do A5 em si) — resposta: o mais amplo,
  despriorizar o design do A5 em si.
- New: [[2026-08-24 Deprioritize A5 Watcher as first-agent candidate]] —
  registra a decisão; nada do raciocínio técnico é invalidado, só deixa de
  ser prioridade/foco ativo.
- Updated: [[Which agent should be built first]] e
  [[Comparing the first-agent candidates]] (callout no topo apontando para
  a decisão; conteúdo existente sobre A5 preservado como registro
  histórico, não recomendação viva); [[How to implement A5 Watcher]]
  (`status: draft` → `deferred`, callout no topo); [[Agent Flow]] (callout
  no resumo; item aberto "qual agente primeiro" marcado como resolvido
  para A5 especificamente, resto continua aberto);
  [[Meeting prep - Agent Flow discovery with Carol - 2026-08-24]] (linha do
  Watcher/A5 na tabela marcada "se surgir", não item de abertura).
- Updated: `index.md` (entradas de todas as páginas acima).

## [2026-08-24] refactor | reformular o prep da Carol como artefato de reunião
- msilva pediu um formato diferente para o prep: o diagrama dos 14 agentes,
  a descrição de cada um segundo a instrução do projeto, e o que o Luís e a
  Gabi já acharam de cada um — para servir como substrato durante a
  conversa em si, não só como lista de temas a perguntar.
- Reescrito [[Meeting prep - Agent Flow discovery with Carol - 2026-08-24]]
  do zero: diagrama Mermaid copiado de [[Fluxo Agêntico diagram]]; tabela
  de 14 linhas (uma por agente) com descrição resumida de
  [[Fluxo Agêntico project instruction]], coluna "Luís acha" e coluna "Gabi
  acha", cada uma citando a meeting de origem
  ([[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]],
  [[2026-08-19 1-1 Matheus - Gabrielle]], [[2026-08-18 1-1 Matheus -
  Luís]], [[2026-08-19 1-1 Matheus - Luís]]). A tabela por-tema da versão
  anterior foi retirada — a maior parte das perguntas (A6 vs. skills repo,
  A10/intranet, qual agente primeiro) já fica embutida no contexto de cada
  linha da tabela de agentes; sobrou só uma lista curta de "perguntas pra
  levar" no fim. A5/Watcher permanece como uma linha normal, com nota de
  que foi despriorizado em 2026-08-24.
- Updated: `index.md`.

## [2026-08-24] refactor | usar o diagrama original no prep da Carol
- msilva: o diagrama deve ser o original (`sources/fluxo agêntico diagrama
  0.png`), não a recriação em Mermaid da passada anterior.
- Updated: [[Meeting prep - Agent Flow discovery with Carol - 2026-08-24]]
  — bloco Mermaid trocado por embed direto do PNG original
  (`![[fluxo agêntico diagrama 0.png]]`), já usado como fonte por
  [[Fluxo Agêntico diagram]].
- Updated: `index.md`.

## [2026-08-24] refactor | cortar ressalva e perguntas do prep da Carol
- msilva pediu para remover a ressalva de leitura (aviso sobre a
  transcrição da Gabi e sobre a conversa ainda não ter acontecido) e a
  seção "Perguntas pra levar" do prep.
- Updated: [[Meeting prep - Agent Flow discovery with Carol - 2026-08-24]]
  — doc agora é só a introdução de uma linha, o diagrama, e a tabela dos
  14 agentes; fecha com a instrução de fold pós-conversa.
- Updated: `index.md`.

## [2026-08-24] refactor | renomear o prep da Carol pra data real da reunião
- msilva vai se reunir com a Carol hoje (2026-08-24), não em 2026-08-19 —
  a data original era só de quando o prep foi rascunhado, não da reunião.
- Renomeado (git mv): `meetings/Meeting prep - Agent Flow discovery with
  Carol - 2026-08-19.md` -> `meetings/Meeting prep - Agent Flow discovery
  with Carol - 2026-08-24.md`. Título (H1) atualizado junto; `date:
  2026-08-24` adicionado ao frontmatter (antes só tinha `updated:`).
- Updated: todos os wikilinks pro nome antigo em `index.md` e neste log
  (`decisions/2026-08-24 Deprioritize A5 Watcher as first-agent
  candidate.md` também corrigido).

## [2026-08-24] refactor | revisar o schema — eixo entidade/proposição, audiência explícita
- Conversa longa com msilva sobre a estrutura de pastas parecer inflada e
  difusa, especialmente a fronteira `syntheses/` vs `decisions/` vs
  `concepts/` vs `reference/`.
- **Modelo assentado**: duas dimensões, não uma lista plana de pastas.
  **Entidade** (`projects/`, `systems/`, `people/` — âncora estável pra
  algo nomeável, nunca "decidida", só é) vs. **proposição** (`decisions/`,
  `concepts/`, `syntheses/` — uma afirmação, filtrada por teste em ordem:
  alguém se comprometeu com isso? não, mas seria verdade sem dono nem
  momento? não, então é raciocínio ainda em teste). Fora disso, **evidência**
  (`sources/`, `meetings/`) e **fora-do-domínio** (`reference/`, scratch
  pessoal/ferramental).
- `syntheses/` redefinida como bancada de trabalho, não corredor
  obrigatório — a maioria das decisões/conceitos nunca passa por lá.
- **Correção em cima da própria correção**: a primeira versão da regra
  dizia "dobrar toda síntese resolvida pra dentro da decisão". Testada
  contra as 4 sínteses reais do vault (`Comparing the first-agent
  candidates`, `Which agent should be built first`,
  `How LiveScript sends the proxy X-App-Id header`, `How to implement A5
  Watcher`) — são investigações densas de 300-600 linhas, não páginas-
  ponteiro, e já estão corretamente linkadas às decisões que as
  resolveram. Fundir teria enterrado a decisão sob o arquivo. Regra final:
  esconde por tamanho — pequeno raciocínio de apoio dobra pra dentro e
  apaga a síntese; investigação grande/densa fica arquivada como página
  própria, `status: superseded`/`deferred`, ligada de volta à decisão.
  Nenhuma das 4 foi alterada.
- **Nova seção "Audience"** no `CLAUDE.md`: a wiki é pro uso do próprio
  msilva, não pra um colega pegar do zero; geração de artefato
  compartilhável só sob pedido explícito dele.
- **Aplicado como teste real**: as três páginas em cadeia sobre
  compartilhar a automação de contabilidade — [[Claude capabilities map -
  accounting data scope]], [[Sharing the accounting automation with the
  team]], [[Sharing via Projects - the accounting project]] — são
  raciocínio de domínio ainda aberto (`status: active`), não scratch
  pessoal; movidas de `reference/` pra `syntheses/`, frontmatter `type:`
  corrigido de `reference` pra `synthesis`.
- Updated: `CLAUDE.md` (seções "Audience" e "Directory layout"),
  `index.md` (as três linhas movidas de `## Reference` pra
  `## Syntheses`, com nota da mudança).

## [2026-08-24] refactor | Lint fixes — fan-out incompleto da decisão do A5
- Rodado `lint` completo: reescrita a checagem de wikilinks (bugs próprios de
  encoding e de continuação de blockquote geravam falsos positivos) —
  resultado real: **zero links quebrados, zero páginas órfãs**, um dead-end
  legítimo ([[Zed Cheatsheet]]), zero páginas `status: active` desatualizadas
  (vault com 2 semanas), zero callouts `[!msilva]` pendentes, cobertura
  `raw/` completa.
- **Achado**: [[2026-08-24 Deprioritize A5 Watcher as first-agent candidate]]
  atualizou duas sínteses com banner, mas deixou de fora duas páginas que
  também tratavam A5 como candidato ativo:
  - [[What should the Agent Flow research phase study]] — o router da fase
    de pesquisa (21 edições). Adicionado banner `[!danger]` no topo, linha
    da tabela *Settled* atualizada de "reopened" para "deprioritized
    entirely", e a pergunta "is A5 still her recommendation" riscada como
    moot.
  - [[Meeting prep - first agent decision]] — banner `[!danger]` adicionado.
    **Não resolvido**: permanece incerto se a reunião que esta página
    preparava (data nunca preenchida) de fato aconteceu; se não, a
    recomendação inteira está datada, não só o ponto do A5. Marcado
    explicitamente na própria página e no `index.md` para confirmação do
    msilva, não assumido de um jeito ou de outro.
- Updated: `index.md` (as duas linhas correspondentes).

## [2026-08-24] callout | Reunião do prep do primeiro agente confirmada: nunca aconteceu
- Perguntado diretamente, msilva não lembra da reunião que
  [[Meeting prep - first agent decision]] preparava — não foi esquecida sem
  registro, simplesmente nunca foi marcada além deste prep.
- **Resolvido**: `status` mudado de `active` pra `superseded`. Banner
  reescrito confirmando o fato em vez de deixar como pergunta em aberto.
  Página mantida (não apagada) como registro do plano tal como estava em
  2026-08-18 — não como raciocínio vivo. Aponta pra
  [[What should the Agent Flow research phase study]] como estado atual da
  questão.
- Updated: `index.md`.

## [2026-08-24] refactor | Documentar `AIRTABLE_ENDPOINT_URL` no `.env.example` do LiveScript
- A pedido de msilva, adicionado exemplo comentado
  `AIRTABLE_ENDPOINT_URL=http://localhost:8080/livescript` ao
  `.env.example` do repo `livemode-roteiros-nextjs`, para testar localmente
  apontando pro [[Airtable Proxy]] em vez da Airtable direto.
- Confirmado lendo `node_modules/airtable/lib/airtable.js`: a var é lida
  nativamente pelo SDK `airtable` — nenhuma mudança de código foi
  necessária, só doc. Mas cobertura é parcial: as chamadas REST hand-rolled
  em `config.service.ts`, `airtable-helpers.ts`, `airtable.service.ts` e
  `script-base.service.ts` hardcodam `https://api.airtable.com` e não leem
  essa var — continuam indo direto. Consistente com
  [[2026-08-18 Proxy first, defer LiveScript-side SDK changes]].
- Updated: [[Proxy Environments]] — exemplo corrigido pra incluir o path
  segment `/livescript` (decisão
  [[2026-08-19 Identify proxy apps by URL path, not header]] não estava
  refletida no bloco de código), e novo callout documentando a cobertura
  parcial do SDK vs. REST hand-rolled.

## [2026-08-24] ingest | 1:1 Matheus - Luís (14:43)
- Lido raw/Luis _ Matheus - 2026_08_24 14_43 GMT-03_00 - Anotações do Gemini.md.
- Novo: [[2026-08-24 1-1 Matheus - Luís]].
- Confirmações: integração local LiveScript+proxy funcionando end-to-end sob
  o esquema de identificação por URL path; rastreamento por Handler ainda
  depende de mudança do lado do LiveScript, não feito. Achado o dashboard
  padrão "antipatterns" do Grafana (nunca aberto antes) já disparando 2× full
  table scan e 2× select sem projeção nos testes locais do msilva.
- Novo item aberto: risco de isolamento por conta GCP (plano atual não isola
  projetos entre usuários; só Enterprise resolveria, segundo Luís).
- Reforçado, não substituído: [[2026-08-14 No mandatory PR review while the
  proxy is pre-production]] — instância concreta via review por demo ao vivo.
- Atualizado: [[2026-08-21 Deploy Airtable Proxy privately behind VPN]] —
  pergunta do backend de estado do Pulumi (Cloud vs. GCS) detalhada, ainda
  aberta; novo callout sobre o risco de tier da conta GCP.
- Atualizado: [[Airtable Proxy]] — confirmação de integração local, item de
  dashboard gerencial e commit da branch de observabilidade adicionados a
  "Things to actually do".
- Atualizado: [[Agent Flow]] — msilva confirma início do PoC LangGraph vs.
  Claude Code (tarefa de 2026-08-20); instrução de Luís pra compilar extração
  vs. opinião própria separadamente após a reunião com a Carol.
- Atualizado: [[Luís Fernandez]] — preferência por review via demo ao vivo,
  pareamento no deploy GCP, e o alerta sobre o tier de conta GCP.
- Atualizado: `index.md`.

## [2026-08-24] lint | health check pós-ingest do 1:1 das 14:43
- **Links**: zero wikilinks quebrados (recontagem mecânica confirma o
  resultado do lint anterior — os únicos "alvos" que não batem com uma
  página real são quebras de linha suaves dentro de `[[...]]` já
  documentadas, mais o exemplo genérico `[[Page]]` do `CLAUDE.md` e citações
  históricas dentro deste próprio log).
- **Órfãos**: nenhum — toda página de conteúdo tem pelo menos um link de
  entrada; só `AGENTS.md`/`CLAUDE.md`/`README.md`/`index.md` não têm, o que é
  esperado e isento pelo schema.
- **Dead-end** (zero link de saída): `reference/Zed Cheatsheet.md` — esperado
  para um cheatsheet pessoal, não é um problema real, só registrado porque o
  checklist do lint pede.
- **`raw/` sem página correspondente**: nenhum de fato — todo arquivo
  substancial em `raw/` (incluindo os dois `Clippings` do Gabriel Packer e os
  quatro de harness/multi-agent) já tem página em `sources/` ou `meetings/`.
  `raw/Novo(a) Documento de Texto.txt` é um placeholder de 0 bytes, sem
  conteúdo pra ingerir.
- **Achado, não é erro do wiki**: `raw/2026-06-14-Papo de Projetos  -
  Anotações do Gemini.docx` tem data errada no nome do arquivo — o cabeçalho
  interno do documento diz "ago. 14, 2026", batendo com
  [[2026-08-14 Papo de Projetos]] (que já usa a data certa). Nome do arquivo
  só é renomeado a pedido explícito do msilva, por regra.
- **Páginas `status: active` desatualizadas (30+ dias)**: nenhuma — o vault
  todo tem duas semanas, a página mais antiga sem toque é de 2026-08-11.
- **Callouts `[!msilva]` pendentes**: nenhum — os dois encontrados
  (`concepts/Agent Harness Template.md`, `decisions/2026-08-19 Identify proxy
  apps by URL path, not header.md`) já são citações de callouts já resolvidos
  e removidos, não blocos `> [!msilva]` ainda vivos.
- **Defeito real encontrado e corrigido**: `decisions/2026-08-14 No mandatory
  PR review while the proxy is pre-production.md` tinha `updated: 2026-08-17`
  mesmo depois de eu ter editado essa página no ingest de hoje (adicionei a
  nota do review por demo ao vivo) — `updated:` corrigido pra 2026-08-24.
- Nenhuma contradição intra-página nova encontrada nas páginas mais editadas
  (`index.md`, `projects/Airtable Proxy.md`, `projects/Agent Flow.md`); as
  tensões já registradas (ex.: prazo do proxy vs. metas.md, retry de 429)
  continuam corretamente marcadas como não resolvidas em ambos os lugares.

## [2026-08-24] ingest | Agent Flow discovery com a Carol (reunião das 15:00)
- Lido `raw/Carol - Matheus.txt` — extração de leitor de tela de um corpo de
  e-mail, sem timestamps, tags só `Speaker A`/`Speaker B`. Confiança de
  transcrição marcada como `low`. Speaker A = Carolina Bezerra, Speaker B =
  msilva (inferido pelo conteúdo: B pergunta sobre "meu 1:1" e "a Gabi"; A
  se declara fora da autoria do desenho e delega pra Gabi).
- Novo: [[2026-08-24 Agent Flow discovery with Carol]].
- **Decisão nova**: [[2026-08-24 Start Agent Flow with A10 Portfolio]] —
  msilva decide começar por A10 Portfolio, convergência direta de quatro
  vozes (Gabrielle, Luís, Carol, a própria dor do msilva). Supersede
  [[Which agent should be built first]] e [[Comparing the first-agent
  candidates]] (ambas `status: superseded`, mantidas como registro de
  raciocínio) e atualiza a tabela Settled de [[What should the Agent Flow
  research phase study]].
- Pergunta já aberta **respondida**: A12 Data Gov não é trabalho deste time,
  segundo a Carol — atualizado em
  [[What should the Agent Flow research phase study]].
- Novas perguntas abertas: se A3=A9 ainda vale (trecho de baixa confiança
  reabre a dúvida) e qual dos dois — Luís ou Carol — está certo sobre quais
  agentes são de fato "transversais" (Luís: só A13; Carol: A10 sim, A11
  não).
- Reforço, não mudança: [[2026-08-24 Deprioritize A5 Watcher as first-agent
  candidate]] ganha confirmação em primeira mão da Carol sobre agentes de
  monitoramento serem menos prioritários.
- Atualizado: [[Agent Flow]], [[Carolina Bezerra]] (vira o 1:1 do msilva
  durante a licença da Gabrielle; framework de priorização em 3 variáveis;
  opiniões sobre Farol/monitoramento/Teacher/Executor), [[Gabrielle
  Ferreira]] (cobertura da licença), [[Maria Fernanda Lemos]] e [[João
  Victor Andrade]] (lacunas reais de rastreamento pro A10), [[Farol]]
  (opinião da Carol sobre priorização, registrada sem mudar o status do
  projeto).
- Atualizado: `index.md`.

## [2026-08-24] lint | health check pós-ingest da reunião com a Carol
- **Links**: zero quebrados — recontagem mecânica incluindo as páginas
  novas (`decisions/2026-08-24 Start Agent Flow with A10 Portfolio.md`,
  `meetings/2026-08-24 Agent Flow discovery with Carol.md`) confirma o
  mesmo resultado do lint anterior; os dois "alvos" novos que apareceram
  (`Gabrielle`, `João`) são quebras de linha suaves dentro do próprio
  registro deste log, não links reais.
- **Órfãos e dead-end**: sem mudança — nenhuma página nova ficou sem link
  de entrada; `reference/Zed Cheatsheet.md` continua o único dead-end real,
  já registrado.
- **`updated:` de todo arquivo tocado hoje**: confirmado `2026-08-24` em
  todos — sem repetição do defeito do lint anterior.
- **Achado e corrigido**: `meetings/Meeting prep - Agent Flow discovery
  with Carol - 2026-08-24.md` (o artefato de preparo) não apontava pra
  reunião real depois que ela aconteceu — adicionado um callout curto
  apontando pra [[2026-08-24 Agent Flow discovery with Carol]], mantendo o
  resto do artefato como estava (registro do que já se sabia antes da
  conversa). Linha correspondente em `index.md` também atualizada.
- **`[!msilva]` pendentes**: nenhum novo.
- Nenhuma contradição nova entre as três syntheses de "qual agente primeiro"
  e a decisão nova — os banners de `superseded` em
  [[Which agent should be built first]] e [[Comparing the first-agent
  candidates]] apontam consistentemente pra
  [[2026-08-24 Start Agent Flow with A10 Portfolio]], e a tabela Settled de
  [[What should the Agent Flow research phase study]] reflete o mesmo
  desfecho.

## [2026-08-24] refactor | PRO-371 criada — gap de duplicata em chamadas filtradas
- Durante um brainstorm em chat (não uma sessão de ingest) sobre um novo
  dashboard de "insights" no Grafana em cima da telemetria já coletada pelo
  proxy, surgiu um gap real: `hasFilter`/`hasFieldProjection` no proxy
  (`internal/proxy/telemetry.go`, `proxy.go emit()`) são só booleanos — o
  conteúdo real do filtro/campos nunca é logado. Isso é suficiente pra
  detectar duplicata/candidato-a-cache no caso sem filtro (full-table scan,
  não tem parâmetro pra variar), mas não no subconjunto **com filtro**: duas
  chamadas filtradas diferentes são indistinguíveis nos dados de hoje.
- msilva pediu pra mapear isso no backlog do Linear em vez de resolver agora.
  Delegado ao agente `pm-linear`: criou **`PRO-371`** ("Detectar duplicatas
  entre chamadas filtradas ao Airtable"), projeto "Proxy em produção
  validado c/ LiveScript", parent `PRO-74` (mesma milestone das irmãs
  `PRO-75`/`76`/`77`), label `Task`, sem estimate (nenhuma irmã tem).
  Descrição registra a direção discutida (hash do filtro/fields, não o
  valor cru) como algo a explorar, não solução fechada.
- Updated: [[Airtable Proxy]] (nova linha em "Things to actually do").
- Open: o próprio dashboard de insights ainda não foi construído — essa
  issue só destrava a fatia filtrada dele; o caso sem filtro (#5 original,
  full-scan) já é construível com o dado atual.

## [2026-08-24] decision | Base access via PAT scoping, not proxy logic
- msilva decided in chat (no transcript) that [[Airtable Proxy]] will not
  implement its own base-level authorization: no per-app base allow-list
  enforced in the proxy's code. Which bases an app can reach is controlled
  by how that app's Airtable PAT is scoped in Airtable itself.
- New: [[2026-08-24 Control base access via PAT scoping, not proxy logic]].
- Updated: [[Airtable Proxy]] (trust-model wording, resolves the "one base
  or all bases in v1" open question), [[2026-08-21 Deploy Airtable Proxy
  privately behind VPN]] (inline correction — the "base allow-list"
  verification step and "per-app base allow-lists" mitigation described
  there are not being built).
- Open: no rationale captured beyond the decision itself — flagged
  unverified on the new decision page in case msilva wants one recorded.

## [2026-08-24] decision | Luís confirma o motivo por trás da decisão de base/PAT
- Luís mandou uma mensagem no Slack (relayed by msilva) confirmando/explicando
  a decisão anterior: a configuração da PAT no Airtable já resolve o escopo de
  bases, ele não vê utilidade em duplicar isso no proxy, nem lembra se foi ele
  quem colocou. Ponto novo: nao deveria haver aplicações compartilhando a
  mesma PAT.
- Updated: [[2026-08-24 Control base access via PAT scoping, not proxy logic]]
  (citação verbatim de Luís na seção Why, dois pontos separados: redundância
  do allow-list e não-compartilhamento de PAT entre apps). Nota adicionada:
  o campo `"bases": []` já existe no `PROXY_APPS` do repo
  (`livemode-airtable-proxy/.env.example`) e fica como follow-up de limpeza,
  não removido nesta passada.
- Open: nenhum novo — a decisão em si já estava registrada; isso só preenche
  o "Why" que tinha ficado marcado como unverified.

## [2026-08-24] refactor | Meeting prep — Agent Flow discovery com Mafé e João Victor (2026-08-25)
- msilva avisou que a reunião de amanhã com [[Maria Fernanda Lemos]] e
  [[João Victor Andrade]] existe pra coletar como as demandas chegam até
  eles — os dois atuam como primeiro contato.
- New: `meetings/Meeting prep - Agent Flow discovery with Mafé and João Victor - 2026-08-25.md`
  — liga a reunião aos dois action items de
  [[2026-08-24 Agent Flow discovery with Carol]] (mapear fontes de dado do
  A10 Portfolio) e ao mapeamento de canal de intake do A1 Receptor Universal;
  perguntas separadas por objetivo (A10 vs. A1), formato tabela.
- Updated: `index.md`.
- Open: nenhum ainda — perguntas ficam pra reunião real; page fica como
  `meeting-prep` até a reunião acontecer e virar `meetings/2026-08-25 ...`.

## [2026-08-24] refactor | Mafé é a instância humana do front de Enablement/A4
- msilva colou no chat o cargo real de [[Maria Fernanda Lemos]], extraído do
  Hub interno (`Brain`, `pessoas/maria-fernanda-mafe`): Analista Pleno,
  Produto UX & Adoção de IA, camada **Habilitadores de IA** — apoio reativo
  às áreas, escalado ao letramento de cada uma, sem construir por elas; PM
  em projetos de produto à parte; segunda posição da cadeira vaga (Caio
  recusou).
- Achado central: essa descrição bate quase ponto a ponto com a
  especificação de **A4 Teacher** e também cobre parte da função de
  "corporatização" de **A6 Curator** ([[Fluxo Agêntico project instruction]]).
  Muda a leitura do gap que a Carol tinha apontado (2026-08-24, reunião
  anterior) — a hipótese agora é que o não-rastreado é especificamente o
  registro de interações de apoio, não o trabalho de PM dela.
- Updated: [[Maria Fernanda Lemos]] (reescrita com o cargo real),
  [[Agent Flow]] (nota nos "três fronts" e no open question de A4 Teacher),
  [[Meeting prep - Agent Flow discovery with Mafé and João Victor - 2026-08-25]]
  (novo propósito da reunião, nova tabela de perguntas específicas pra A4),
  `index.md`.
- Open: confirmar tudo isso na reunião de 2026-08-25 — nada aqui foi
  verificado com ela diretamente ainda.

## [2026-08-24] decision | M1 = A10 Portfolio + A14 PM Agent juntos, PoC primeiro
- Discussão em chat sobre o que seria a M1 do projeto Fluxo Agêntico
  (Linear, PRO), depois de M0 - Discovery. msilva decide construir A10
  Portfolio e A14 PM Agent juntos, não em sequência, com uma PoC antes de
  qualquer build de produção — estendendo a comparação LangGraph vs. Claude
  Code que o Luís já tinha tarefado num recorte só de A10.
- Resolve a lacuna deixada em aberto por
  [[2026-08-24 Start Agent Flow with A10 Portfolio]] (A10+A14 é um build ou
  dois). Resposta: um build, duas specs (actor→input→output cada, per
  método do Luís); A14 tem o escopo reduzido nessa fase porque sua spec
  completa pressupõe um pipeline (A1/A2/A7/A8) que ainda não existe.
- New: [[2026-08-24 Build A10 and A14 together, PoC first]].
- Updated: [[Agent Flow]] (novo callout de M1), [[2026-08-24 Start Agent
  Flow with A10 Portfolio]] (aponta pra resolução), `index.md`.
- Linear: milestone **M1 - PoC A10 + A14** criada no projeto Fluxo Agêntico
  (PRO), com issues pra especificar cada agente e rodar a PoC.

## [2026-08-25] refactor | Correção de grafia: apelido é "Mafê", não "Mafé"
- msilva corrigiu: o apelido de [[Maria Fernanda Lemos]] se escreve **Mafê**.
  Entradas anteriores deste log com "Mafé" ficam como estão (append-only),
  mas todo o resto do vault foi corrigido.
- Renamed: `meetings/Meeting prep - Agent Flow discovery with Mafé and João
  Victor - 2026-08-25.md` → `meetings/Meeting prep - Agent Flow discovery
  with Mafê and João Victor - 2026-08-25.md`.
- Updated: [[Maria Fernanda Lemos]] (alias e corpo), [[Agent Flow]], `index.md`.

## [2026-08-25] ingest | Bossabox AI transformation discovery (vendor)
- Lido `raw/bossabox.txt` — export HTML do tl;dv (3MB, uma linha só, cada
  palavra num `<span>` com timestamp). Extraído o conteúdo de
  `transcript-container` com um script perl ad-hoc (61 falas), não com as
  ferramentas usuais de leitura de arquivo.
- **msilva não estava na call.** Attendees: Carolina Bezerra (Livemode) +
  três reps da **Bossabox**, vendor de transformação com IA (Pedro
  Arantes, JV Abreu, Camila Sande) — nome novo neste vault.
- Data não confirmada: nem cabeçalho nem nome do arquivo trazem data; usado
  o mtime do arquivo (2026-08-25) como aproximação, com uma pista interna
  (Carol hesitando se o feriado é dia 1 ou dia 7 do mês seguinte — quase
  certamente 7 de setembro) que aponta pra final de agosto de 2026,
  consistente com o mtime. Marcado como unverified na própria página.
- Novo: [[2026-08-25 Bossabox AI transformation discovery]],
  [[Bossabox Engagement]], [[Pedro Arantes]], [[JV Abreu]], [[Camila Sande]].
- **Achado principal**: Carol, sem saber que isso alimentaria este vault,
  nomeia pra um vendor externo quase as mesmas dores que
  [[Agent Flow]] já existe pra resolver — priorização em duas camadas,
  ferramentas fragmentadas, "nada atuando no grande" (o buraco exato do
  A10 Portfolio), um dia depois de msilva ter decidido começar por ali
  ([[2026-08-24 Start Agent Flow with A10 Portfolio]]). Terceiro canal
  independente confirmando a mesma dor, sem nenhum interesse no resultado
  do Agent Flow.
- Duas dores novas, ainda não desenhadas em nenhum dos 14 agentes:
  previsibilidade de entrega assistida por IA como métrica (não tokens/
  tamanho de commit), e medição de efetividade pós-lançamento (sobrepõe
  A11 Product — e é um exemplo concreto do próprio argumento da Carol de
  2026-08-24 de que análise de uso é pós-produto, não fase de construção).
- Atualizado: [[Agent Flow]], [[Carolina Bezerra]], [[Luís Fernandez]]
  (confirmação independente do padrão de disponibilidade só à tarde),
  `index.md`.
- `updated:` de `people/Carolina Bezerra.md` e `people/Luís Fernandez.md`
  corrigido de 2026-08-24 pra 2026-08-25 — datas ficaram desalinhadas com
  o estado real do vault (`index.md` e `projects/Agent Flow.md` já
  estavam em 2026-08-25 por edições concorrentes de outra sessão).

## [2026-08-25] refactor | Correção: msilva e Luís estavam na call da Bossabox
- msilva corrigiu: ele e o Luís estavam presentes fisicamente na
  [[2026-08-25 Bossabox AI transformation discovery]] — não só a
  Carolina. Os três foram captados pelo microfone da Carolina, então o
  "Speaker A" único do export do tl;dv na verdade mistura os três; não dá
  pra saber quem falou o quê.
- `attendees` da página corrigido pra incluir Matheus Silva e Luís
  Fernandez; `transcription_confidence` rebaixado de `high` pra `low`
  (a transcrição da palavra em si continua confiável, a atribuição de
  quem fala não).
- Rebaixado em cascata: a leitura de "canal terceiro, independente,
  confirmando a dor do A10 Portfolio" não se sustenta se quem falou foi o
  próprio msilva ou o Luís — pode ser eco da própria posição deles, não
  validação de fora. Deixado como pergunta aberta, não decidido num
  sentido ou outro.
- Atualizado: [[2026-08-25 Bossabox AI transformation discovery]]
  (banner de correção, attendees, todas as atribuições "Carolina"
  trocadas por "lado da Livemode, não distinguível"), [[Bossabox
  Engagement]], [[Agent Flow]], [[Carolina Bezerra]], [[Luís Fernandez]],
  `index.md`.
- Item de ação novo: msilva confirmar, se lembrar, quem disse o quê —
  principalmente o framework de priorização e as opiniões sobre Farol/
  monitoramento, que ficam sem atribuição confiável por ora.
