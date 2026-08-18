---
type: log
updated: 2026-08-18
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
