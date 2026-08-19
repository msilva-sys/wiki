---
type: index
updated: 2026-08-19
---

# Index

Catalog of every page in this wiki, one line each. Updated on every ingest.
See [[log]] for the chronological record and `CLAUDE.md` for the schema.

> [!danger] Outstanding — rotate the Airtable PAT and Firebase service-account key
> Both sat in plaintext in this vault from 2026-08-11 and remain readable in **6
> git commits**. The working tree is clean; history is not. Rotation is the action
> that matters and only msilva can do it. Details:
> [[Proxy Environments]]. Reported by every `lint` since the first.

> [!warning] Time-boxed — Gabrielle is on leave 2026-08-24 → 2026-09-10
> [[2026-08-18 1-1 Matheus - Gabrielle]] was the last scheduled 1:1 before it.
> - **The migration itself is done** — discovered 2026-08-18, not performed this
>   session: all 65 `AIRTABLEGC` issues already had Linear counterparts (60 in
>   `Proxy do Airtable`, 5 in `LiveMode Data Hub (POC)`), title-for-title. Three
>   drifted statuses reconciled. See
>   [[2026-08-14 Migrate project management from Jira to Linear]]. `AIRTABLEGC` itself
>   is left untouched for now (msilva's call) — winding it down is a separate,
>   still-open question.
> - **The Linear Business trial still expires 2026-09-09**, one day before Gabrielle is
>   back, with the ~250-issue free cap behind it. No purchase recorded; she's the
>   person who can authorize it. Lower urgency now that the migration itself isn't
>   adding to the count, but still unresolved.
>
> The question list to close before 2026-08-24:
> [[What should the Agent Flow research phase study]].

## Projects
- [[Airtable Proxy]] — Go reverse proxy fronting the Airtable API. Primary track. Active work: app auth + key distribution. **[[Orca (CDE)]] and other services are planned consumers**, and so are the **n8n citizen flows** (2026-08-18) — an unknown number of them, each author holding their own Airtable key, which is what makes centralized key distribution the blocker rather than a nicety. One of **three projects** in an *Airtable governance and reliability* initiative; the third, a **data hub**, is probably the wiki's missing Phase 2. **2026-08-18**: operation-detection bug fixed, `X-App-Id` confirmed end-to-end in dev (with the SDK patch, since reverted); scope clarified as proxy-first, LiveScript's own SDK migration explicitly deferred. **`PRO-78` auth epic worked through 2026-08-18**: `PRO-80`/`PRO-81` closed as already-shipped-in-code, `PRO-82` (onboarding/key-issuance) handed to Luís as a process decision rather than decided solo, `PRO-83` blocked on it. **2026-08-19**: a real, unresolved tension between Luís (proxy is msilva's 100% job) and the intranet `metas.md` (Agent Flow design is the headline August commitment) — msilva's call was to leave Linear dates alone rather than pick a side.
- [[Agent Flow]] — proposed multi-agent architecture for the area's intake and delivery; design only. Second track, started 2026-08-17. **Which agent to build first is reopened** (2026-08-18): the criterion changed from lowest-risk to utility. Also 2026-08-18: it gets **its own Linear initiative, one project per agent** (Gabrielle); **Claude already authors this area's backlogs**, so A2's hard part is classification, not ticket-writing; the **token-cost evidence is retracted** — a provider switch confounded it; and **Luís contradicts Gabrielle same-day** on whether the Watcher/A5 needs the proxy at all — unresolved, blocks further A5 design until msilva runs discovery with Gabrielle and Carol separately.
- [[Orca Next Version]] — roadmap/backlog planning workspace for [[Orca (CDE)]]'s next version (repo `orca-produto`, access gained 2026-08-18). Not msilva's, tracked for context. 7 milestones, ~28 issues, approved by stakeholders 2026-08-07; nothing deployed to production yet as of the 2026-08-17 weekly.
- [[Pulse]] — outsourced front-end for the business pipeline. Not msilva's, but he joins the immersion week for context. **Now `deferred` (2026-08-18)**: approval failed, DOR stood down, delay ≥1 month, Copinha window lost.
- [[Farol]] — travel-data consolidation (Uber, OnFly, Expresso) for finance; Luís + Yasmin. Not msilva's. **V2 adds bronze/prata/ouro layers and a conversational AI layer** — the area's second user-facing AI system. Resolves the *farol* name collision with the status readout.

## Systems
- [[LiveScript]] — collaborative script editor, heaviest Airtable consumer, the reason the proxy exists. Also called *roteiros*.
- [[Orca (CDE)]] — Livemode's computer-vision/audio system proving commercial-insertion display in live sports broadcasts (the CDE, "Comprovante de Entregas"). In production since v3.2.0; business-critical, ~10 headcount worth of automation. **Resolves the "in production" vs. "nothing deployed" contradiction** flagged across five pages — the two statements were about the live system vs. its unshipped next-version roadmap ([[Orca Next Version]]).
- [[Proxy Environments]] — environment/config reference for the proxy and LiveScript. Credentials redacted 2026-08-17.

## Concepts
- [[Airtable Rate Limits]] — 5 req/s per base, 429 → ~30s lockout, `Retry-After`; the constraint driving the whole programme.
- [[Packaging as skills]] — converged from six sources: packaging is both the token-cost lever and the sharing mechanism. What it doesn't solve is maintenance. **2026-08-18**: the team-generic vs. project-specific boundary is itself unsettled — at least three divergent Linear-skill versions exist; Carol is building the real shared repo, with Luís now helping.
- [[Agents read primary sources]] — agents query the underlying data rather than wait for the agent meant to digest it. Three instances (A5, A7, A1); it is what makes *anarchic-first* buildable, and it is why context retrieval is **not** a fifteenth agent. Splits A6 into tools + curation.
- [[AI status reporting on Linear]] — the weekly status readout, Gabrielle's *"nosso repórter"*. **Mechanism found 2026-08-18**: an in-house board she controls that pulls from Linear, aggregating at issue level **deliberately**, drill-down planned. So the subtask blindness is a design trade-off, not a defect. Four real insights and two real failures in one readout. **Not called "o farol"** — that is the project [[Farol]] — and **no longer the only agent-like system running here**.
- [[Linear Project Structure]] — **initiative = solution, project = delivery segment, milestones group deliveries.** The shape msilva must migrate the proxy backlog *into* — and **Claude writes that structure from the design docs**, which is the area's routine practice. Plus teams-as-isolation, Luís's templates (he's rewriting them), and the Slack integration. Conventions explicitly provisional and msilva is invited to change them. Free cap ~250 issues, **already hit once**; Business trial expires 2026-09-09.

_(next candidates: OpenTelemetry, Reverse Proxy Patterns, Grafana LGTM)_

## People

Folder opened 2026-08-18, at msilva's request, after being offered and
declined three times. Role and ownership only — no opinions, no sensitive
transcript content; see each meeting page for that.

- [[Gabrielle Ferreira]] — msilva's manager; owns [[Agent Flow]], product
  reviewer, controls the AI status readout. On leave 2026-08-24 → ~2026-09-10.
- [[Luís Fernandez]] — Tech Lead, part-time (afternoons); primary technical
  contact during Gabrielle's leave; disagrees with her on A5's proxy scoping.
- [[Carolina Bezerra]] — runs Papo de Projetos and the `Brain`/Hub docs;
  building the shared skills repo and Claude Code training programme.
- [[Yasmin Macedo]] — owns N8N/Monday flows; testing [[Farol]]'s GCP build.
- [[Arthur Tavares]] — maintains the matriz de eventos schema; moving to
  another area, so that knowledge is in transition.
- [[João Victor Andrade]] — new hire, CRM onboarding contract.
- [[Michelly Magalhães]] · [[Maria Fernanda Lemos]] — attendees only, no
  role recorded yet.
- [[Gabriel (CazéTV automation)]] — n8n revenue-recognition flow; **distinct
  from** [[Gabriel (matriz de eventos, departed)]], who built the matriz and
  has since left.
- [[Osmar]] · [[Nina]] · [[Diego]] — *reportagem* team; the matriz
  external-events gateway use case in [[Agent Flow]] is their problem.
- [[Bianca (Bia)]] · [[Kauan]] — with Arthur in the TES vendor-trial group.
- [[Pedro Alves]] — Orca Next Version engineer, E1 infrastructure; likely
  "Pedrinho" (unverified nickname match).
- [[Yuri Muanes]] · [[Lucas Gomes]] — Orca ops, Brazil/Portugal.
- [[Júlia]] — co-created the CRM Slack channel.
- [[Sérgio]] · [[Ribon]] · [[Zoca]] · [[Cristian]] — leadership approval
  chain that stalled [[Pulse]]'s immersion week.

_(msilva himself has no page — this vault already is the record of his work.)_

## Decisions
- [[2026-08-10 Onboarding runs proxy and agent flow in parallel]] — proxy first and fast, agent flow alongside with no delivery pressure.
- [[2026-08-14 Migrate project management from Jira to Linear]] — Linear becomes the system of record. **Turned out to already be ~done** (found 2026-08-18): 65/65 issues mirrored across two Linear projects; `AIRTABLEGC`'s wind-down is now the only open piece.
- [[2026-08-14 No mandatory PR review while the proxy is pre-production]] — msilva merges his own work; Luís reviews history after the fact.
- [[2026-08-17 A5 receiver runs on Cloud Run]] — plain container, `min-instances=0`. Records why n8n, Cloudflare Workers and co-location were not chosen, and what would reopen each.
- [[2026-08-18 Product feedback in Linear, code review in Git]] — **`draft`, demoted from a decision**: the transcript has Gabrielle saying *"não tem martelo batido"*. Kept because the practice is real and observed — **she is the product reviewer**, testing in homologation and returning work via Linear comments, while Luís reviews Yasmin's work in Git. Linear comments never reach Git.
- [[2026-08-18 Save n8n execution logs for audit]] — save **successful production** runs; failed ones already were. That is why runs *"vanished on completion"* — the flow started succeeding. Unblocks the loop diagnosis, which now has two believers and no clean measurement.
- [[2026-08-18 Bring options to Luís before deciding, communicate async and often]] — msilva agreed to present alternatives before acting on a technical decision, and to communicate more often, asynchronously. Triggered by the SDK patch having shipped without this.

_(next candidates, extractable from [[Airtable Proxy]]: token-terminating auth, OTel/OTLP over BigQuery, Cloud Run min=1)_

## Sources
- [[Fluxo Agêntico project instruction]] — **authoritative spec** for Agent Flow: AI-First philosophy, anarchic-then-integrated build strategy, per-agent detail for all 14.
- [[Fluxo Agêntico diagram]] — the 14-agent architecture diagram; A6 Curator is the hub, A13 blocks, `Bug (sistema)` is machine-fed.
- [[Gabriel Packer - DAG-driven agent orchestration]] — external prior art (part 2); the model Luís shared. A working A7→A8→A9 driven by a dependency graph. Resolves what "gráfis" meant.
- [[Gabriel Packer - solo founder AI workflow (part 1)]] — the same system before dispatch was automated. Quality-gate-heavy pipeline; instructions-in-files lesson; Linear has its own agent.

_(transcripts are filed under Meetings instead — see `CLAUDE.md`)_

## Meetings
- [[2026-08-10 Onboarding Técnico - Matheus]] — origin of the proxy, the agent architecture, and how the two tracks split.
- [[2026-08-14 1-1 Matheus - Gabrielle]] — most consequential so far: Linear migration, PR flow, app auth as active work, World Cup data loss.
- [[2026-08-14 Recap da Semana]] — weekly team status; msilva's own framing of the proxy; correction on the review-flow decision.
- [[2026-08-14 Papo de Projetos]] — area/culture meeting; resolves what Orca is; documentation Hub exists.
- [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] — first enablement consultation; diagnoses the $7/day token burn (n8n, wholesale context, probable loop).
- [[2026-08-17 Weekly - Projetos e Tarefas]] — fourth weekly meeting type; the **matriz gateway** (an agent proposed by the business, A1+A2 in miniature), Luís's unattended ultracode run, the AI status readout, and **Orca not observable yet**. Also: why the proxy was ever in Jira, and a Hub action item **due 2026-08-18**.
- [[2026-08-18 1-1 Matheus - Gabrielle]] — **last 1:1 before Gabrielle's leave**, 30 min screen-share. Linear's initiative/project/milestone semantics; the Airtable initiative's three projects; **the n8n citizen flows are meant to go behind the proxy**, with per-person keys and unknown count; **Claude already authors the area's backlogs**; the tracking board's mechanism; and **Gabriel switched LLM provider**, which retracts the $7-vs-11¢ evidence. Rebuilt from the real transcript after first being written from Gemini's summary.
- [[2026-08-18 1-1 Matheus - Luís]] — second meeting of the day, ~56 min. **Contradicts Gabrielle's same-morning framing**: Luís wants the Watcher/A5 decoupled from the proxy entirely. Process feedback — bring options, communicate async and often — triggered by the already-shipped SDK patch. Proxy progress (operation-detection bug fixed, `X-App-Id` confirmed end-to-end); scope clarified (proxy first, LiveScript's SDK traffic deferred); LiveScript caching clarified as intentional; no team standard yet for Linear skills, and Carol is building the real one; Claude Code verbosity fixed by pinning instructions.
- [[Meeting prep - accounting data in Claude - 2026-08-17]] — prep notes for the 2026-08-17 accounting-data-in-Claude meeting.
- [[Meeting prep - first agent decision]] — **one-pager** for the deadline meeting with Gabrielle and Luís: recommendation (A1 + A2), the four candidates in a line each, six decisions needed before 2026-08-24. Meeting date not yet recorded.

## Syntheses
- [[What should the Agent Flow research phase study]] — **status board and router** for the agent research: what's settled, what's open, what to ask before Gabrielle's leave. The A5-first row is struck through as of 2026-08-18. Same day: **Luís wants A5/Watcher decoupled from the proxy entirely**, contradicting the morning's framing — blocks further A5 design pending discovery with Gabrielle and Carol.
- [[Comparing the first-agent candidates]] — **the decision doc.** Motivations, pros and cons for A1+A2, A5, A7 and A4 side by side, what applies whichever is chosen, and eight things to decide before 2026-08-24. Written to be handed to someone else.
- [[Which agent should be built first]] — **now answers two criteria.** Lowest-risk (2026-08-17) → A5 Watcher on proxy telemetry; utility (2026-08-18) → the intake pair A1 + A2, also msilva's position. Both arguments kept in full, plus why A7 cannot be chat-only. **Also 2026-08-18**: Luís separately argues A5 shouldn't target the proxy at all — unreconciled.
- [[How to implement A5 Watcher]] — build plan: alert rules as code, throttling as cost control, Linear as dedup store, and how to build most of it before GC-5 lands.
- [[How LiveScript sends the proxy X-App-Id header]] — GC-5 client side: shipped via a `pnpm patch` (SDK) + centralized REST injection, **reverted 2026-08-18** to bring alternatives to Luís first. Now a live 5-option comparison (patch / his customHeaders-only suggestion, verified insufficient / REST migration / SDK upgrade, still impossible / fetch interceptor). Corrects the "drop the PAT / X-Api-Key" onboarding model.

## Reference
- [[Claude capabilities map - accounting data scope]] — Claude capabilities by layer for large-data/accounting work, cheapest-to-adopt first.
- [[Sharing the accounting automation with the team]] — distribution options for getting one person's workflow to a team. Bears directly on [[Agent Flow]]'s sharing problem.
- [[Sharing via Projects - the accounting project]] — Projects as a sharing mechanism; what belongs in one.
- [[AIRTABLEGC-34]] — ticket notes (legacy Jira).
- [[Zed Cheatsheet]] — editor keybindings.
- [[Claude Code Working Habits]] — pinning behavior into project instructions, artifact opt-in, IDE integration, cross-model review. From Luís, 2026-08-18.
