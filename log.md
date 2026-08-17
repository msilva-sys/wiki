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
