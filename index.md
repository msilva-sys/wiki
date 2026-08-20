---
type: index
updated: 2026-08-20
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
- [[Airtable Proxy]] — Go reverse proxy fronting the Airtable API. Primary track. Active work: app auth + key distribution. **[[Orca (CDE)]] and other services are planned consumers**, and so are the **n8n citizen flows** (2026-08-18) — an unknown number of them, each author holding their own Airtable key, which is what makes centralized key distribution the blocker rather than a nicety. One of **three projects** in an *Airtable governance and reliability* initiative; the third, a **data hub**, is probably the wiki's missing Phase 2. **2026-08-18**: operation-detection bug fixed, `X-App-Id` confirmed end-to-end in dev (with the SDK patch, since reverted); scope clarified as proxy-first, LiveScript's own SDK migration explicitly deferred. **`PRO-78` auth epic worked through 2026-08-18**: `PRO-80`/`PRO-81` closed as already-shipped-in-code, `PRO-82` (onboarding/key-issuance) handed to Luís as a process decision rather than decided solo, `PRO-83` blocked on it. **2026-08-19**: a real, unresolved tension between Luís (proxy is msilva's 100% job) and the intranet `metas.md` (Agent Flow design is the headline August commitment) — msilva's call was to leave Linear dates alone rather than pick a side. Also **2026-08-19**: the old empty-Issues-tab mystery is solved — 47 of 60 issues were archived in one bulk operation (2026-08-06), not a permissions issue; F1/F2/F3 restored and reassigned to msilva, `Backlog deferido` left archived on purpose. A proposal to split LiveScript-side issues out into **Fluxo Agêntico** was raised and **rejected by msilva** — they stay under `Proxy do Airtable` for now. Every Done issue was checked against real merged PRs (`gh pr list`): `PRO-75`/`PRO-77`/`PRO-80` now carry their actual PR links; `PRO-60`/`PRO-98–105` deliberately left unlinked since they predate PR-based review. With the auth epic closed and `PRO-82`/`83`/`76` blocked on Luís, moved to the deploy step: `PRO-94` (Pulumi program language) is the one unblocked issue — options (Go/TypeScript/Python) brought to him as a comment, msilva's own lean toward Go, per [[2026-08-18 Bring options to Luís before deciding, communicate async and often]]. **2026-08-19, later**: app identification is being redesigned — **URL path, not `X-App-Id` header** ([[2026-08-19 Identify proxy apps by URL path, not header]]), settled with Luís specifically because a header can't survive SDK transport reliably but a path can; retires the header-injection problem on [[How LiveScript sends the proxy X-App-Id header]]. Not yet implemented. **2026-08-19, later still**: acting on Luís's actual words (not the looser wiki summary of them), F3 promoted to its own Linear project, **Proxy em produção validado c/ LiveScript**, sibling to `Proxy do Airtable` in the same initiative, with 6 new milestones matching its existing epics 1:1; `Proxy do Airtable` (F1/F2/Backlog deferido) left untouched, msilva's call.
**2026-08-20**: Luís resolved `PRO-82` — simplest path, no self-serve
tooling now, real onboarding flow worked out once real external apps show
up; matches msilva's stated lean. `PRO-83` (key/secret rotation strategy)
resolved the same day on the same policy — manual rotation, no tooling,
revisit once real DB-backed storage exists — this one msilva's own call,
not re-run past Luís, since it's applying an already-settled precedent
rather than a fresh fork. See [[2026-08-18 Bring options to Luís before
deciding, communicate async and often]].
- [[Agent Flow]] — proposed multi-agent architecture for the area's intake and delivery; design only. Second track, started 2026-08-17. **2026-08-20**: msilva's own reconsideration of whether the 14-agent diagram is the right shape at all, vs. starting simpler and evolving — reinforces the Akita counterpoint, not yet run past Luís or Carol. **Same day, Luís independently pushes the same direction**: finish the agent-inventory discovery work first, using msilva's own new **agir/informar** heuristic (act/reason vs. purely inform) to sort real agents from nested skills — reopens whether **A4 Teacher** clears that bar. **Which agent to build first is reopened** (2026-08-18): the criterion changed from lowest-risk to utility. Also 2026-08-18: it gets **its own Linear initiative, one project per agent** (Gabrielle); **Claude already authors this area's backlogs**, so A2's hard part is classification, not ticket-writing; the **token-cost evidence is retracted** — a provider switch confounded it; and **Luís contradicts Gabrielle same-day** on whether the Watcher/A5 needs the proxy at all — unresolved, blocks further A5 design until msilva runs discovery with Gabrielle and Carol separately. **2026-08-19**: msilva's own candidate reconciliation for that conflict — Watch as a **multi-tool consolidator** (proxy + LogRocket + Vercel) rather than proxy-defined — not yet run past Luís. Also new: whether A1+A2 is one agent or two; whether A3 needs a universal discovery step before executing, not just for complex work; and whether usage/UX monitoring belongs to Watch at all. **Same day, later (Part 2)**: a *possible* convergence where Gabrielle also doubts proxy-scoped Watch (attribution collapsed, unconfirmed); A6's four functions named (may be a 4-way split, not 2-way); A9 clarified against A3; A10/A11/A12/A13 relationships sketched, with A12's ownership and an A10/A13 merge both open; and a third "which agent first" framing — Carol's team's-own-pain criterion plus msilva's two stated pains (no unified cross-project backlog; no discovery/documentation minimum) — pointing at A10+A14 and A7+A8 instead. **2026-08-19, later**: Luís tells Carol dev-subagent design is project-harness scope, not part of this architecture; introduces [[Claude Agent SDK]] (headless Claude Code) as a topic to track; Luís becomes a third independent voice naming the cross-project visibility pain. **2026-08-20**: [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] registered as an external counterpoint — compounding-failure math against long orchestrated chains, and a "spec-as-source" risk flagged on A7's PRD-first output contract — not a reopened decision. **2026-08-20, later**: its two primary sources ([[Don't Build Multi-Agents]], [[How we built our multi-agent research system]]) read directly rather than at second hand — checked against the actual diagram edges (A10↔A11↔A12, A5→A3, all→A6), almost nothing in the 14-agent design clears either source's "good fit" bar for multi-agent; but the counterpoint isn't one-sided — Anthropic's own system beat single-agent by 90.2%, so the real test is fit (parallel/independent/high-value vs. shared-context/interdependent), not a blanket verdict.
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
- [[Linear Project Structure]] — **initiative = solution, project = delivery segment, milestones group deliveries.** The shape msilva must migrate the proxy backlog *into* — and **Claude writes that structure from the design docs**, which is the area's routine practice. Plus teams-as-isolation, Luís's templates (he's rewriting them), and the Slack integration. Conventions explicitly provisional and msilva is invited to change them. Free cap ~250 issues, **already hit once**; Business trial expires 2026-09-09. **2026-08-19**: corrected and executed — Luís's actual words describe promoting the milestone you're actively working on to its own sibling project, not just "structure the active project"; F3 was promoted this way (see [[Airtable Proxy]]). The rest of the restored Jira backlog can just be deleted, no rush; msilva has full autonomy on Linear org decisions; `Release` added as an unresolved concept.
- [[Claude Agent SDK]] — running Claude Code via API/CLI, no IDE. Introduced by Luís, 2026-08-19, as a topic to track; not yet tied to a specific [[Agent Flow]] agent. **2026-08-20**: [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] registered as counterpoint — harness/substrate choice mainly a cost decision, not an architecture one, for a strong model. Also 2026-08-20: [[Don't Build Multi-Agents]] notes Claude Code's own subagents (June 2025) never ran parallel to the main agent and were scoped to Q&A only, not code-writing — the same conclusion Luís reaches independently a year later.

_(next candidates: OpenTelemetry, Reverse Proxy Patterns, Grafana LGTM)_

## People

Folder opened 2026-08-18, at msilva's request, after being offered and
declined three times. Role and ownership only — no opinions, no sensitive
transcript content; see each meeting page for that.

- [[Gabrielle Ferreira]] — msilva's manager; owns [[Agent Flow]], product
  reviewer, controls the AI status readout. On leave 2026-08-24 → ~2026-09-10,
  reachable the first few days, disconnecting more once traveling.
- [[Luís Fernandez]] — Tech Lead, part-time (afternoons); primary technical
  contact during Gabrielle's leave; disagrees with her on A5's proxy scoping.
  Grants msilva full autonomy on Linear org decisions (2026-08-19).
- [[Carolina Bezerra]] — runs Papo de Projetos and the `Brain`/Hub docs;
  building the shared skills repo and Claude Code training programme. Looped
  into [[Agent Flow]] conversations 2026-08-20, weekly check-in with msilva.
- [[Yasmin Macedo]] — owns N8N/Monday flows; testing [[Farol]]'s GCP build;
  getting more of Luís's direct time (2026-08-20) tied to that ramp-up.
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
- [[2026-08-19 Identify proxy apps by URL path, not header]] — the proxy identifies calling apps by URL path (e.g. `proxy.livemode.com/livescript`), not by an `X-App-Id` header, settled with Luís because a header can't reliably survive SDK transport. Retires the header-injection problem on [[How LiveScript sends the proxy X-App-Id header]]. Not yet implemented.
- [[2026-08-18 Proxy first, defer LiveScript-side SDK changes]] — msilva's job is the proxy, not finishing LiveScript's own migration onto it. Confirmed 2026-08-19 as a general pattern, not a one-off. Still blocks `PRO-76`; `PRO-82` resolved 2026-08-20 and is no longer blocked by this rule.

_(next candidates, extractable from [[Airtable Proxy]]: token-terminating auth, OTel/OTLP over BigQuery, Cloud Run min=1)_

## Sources
- [[Fluxo Agêntico project instruction]] — **authoritative spec** for Agent Flow: AI-First philosophy, anarchic-then-integrated build strategy, per-agent detail for all 14.
- [[Fluxo Agêntico diagram]] — the 14-agent architecture diagram; A6 Curator is the hub, A13 blocks, `Bug (sistema)` is machine-fed. **2026-08-20**: now rendered as an inline Mermaid diagram (colored to match the original SVG's groupings), replacing the earlier ASCII sketch.
- [[Gabriel Packer - DAG-driven agent orchestration]] — external prior art (part 2); the model Luís shared. A working A7→A8→A9 driven by a dependency graph. Resolves what "gráfis" meant.
- [[Gabriel Packer - solo founder AI workflow (part 1)]] — the same system before dispatch was automated. Quality-gate-heavy pipeline; instructions-in-files lesson; Linear has its own agent.
- [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] — external counterpoint: compounding-failure math and receipted benchmarks against heavy multi-agent orchestration, and against spec-as-source-of-truth (PRD-first) design; his **ai-memory** tool as a structural parallel to this vault. Registered against [[Agent Flow]] 2026-08-20, not a reopened decision. **2026-08-20**: its two secondhand citations (Cognition, Anthropic) now read directly and linked in — one of them, read in full, is more nuanced than the citation implied.
- [[Don't Build Multi-Agents]] — Cognition/Walden Yan (2025-06-12), primary source behind Akita's citation. Two principles: share full agent traces, and actions carry implicit decisions that conflict in parallel. Recommends single-threaded linear agents by default. Cited data point: Claude Code's own subagents (June 2025) never ran parallel to the main agent.
- [[How we built our multi-agent research system]] — Anthropic (2025-06-13), fetched live (the raw clipping captured only the title). Reports **90.2%** improvement from its own multi-agent system — not a warning-only post. Names the exact fit test: parallel/independent/high-value work is a good fit, shared-context/interdependent work (most coding) is not. Checked against [[Fluxo Agêntico diagram]]'s edges, almost nothing in [[Agent Flow]] currently clears the "good fit" side.

_(transcripts are filed under Meetings instead — see `CLAUDE.md`)_

## Meetings
- [[2026-08-10 Onboarding Técnico - Matheus]] — origin of the proxy, the agent architecture, and how the two tracks split.
- [[2026-08-14 1-1 Matheus - Gabrielle]] — most consequential so far: Linear migration, PR flow, app auth as active work, World Cup data loss.
- [[2026-08-14 Recap da Semana]] — weekly team status; msilva's own framing of the proxy; correction on the review-flow decision.
- [[2026-08-14 Papo de Projetos]] — area/culture meeting; resolves what Orca is; documentation Hub exists.
- [[2026-08-17 Matheus - Gabriel - CazéTV revenue recognition flow]] — first enablement consultation; diagnoses the $7/day token burn (n8n, wholesale context, probable loop).
- [[2026-08-17 Weekly - Projetos e Tarefas]] — fourth weekly meeting type; the **matriz gateway** (an agent proposed by the business, A1+A2 in miniature), Luís's unattended ultracode run, the AI status readout, and **Orca not observable yet**. Also: why the proxy was ever in Jira, and a Hub action item **due 2026-08-18**.
- [[2026-08-18 1-1 Matheus - Gabrielle]] — **last 1:1 before Gabrielle's leave**, 30 min screen-share. Linear's initiative/project/milestone semantics; the Airtable initiative's three projects; **the n8n citizen flows are meant to go behind the proxy**, with per-person keys and unknown count; **Claude already authors the area's backlogs**; the tracking board's mechanism; and **Gabriel switched LLM provider**, which retracts the $7-vs-11¢ evidence. Rebuilt from the real transcript after first being written from Gemini's summary.
- [[2026-08-18 1-1 Matheus - Luís]] — second meeting of the day, ~56 min. **Contradicts Gabrielle's same-morning framing**: Luís thinks the Watcher/A5 shouldn't be scoped to the proxy (corrected 2026-08-19 from an earlier "decoupled entirely" reading — his objection is to the proxy being the defining scope, not to it playing any role). Process feedback — bring options, communicate async and often — triggered by the already-shipped SDK patch. Proxy progress (operation-detection bug fixed, `X-App-Id` confirmed end-to-end); scope clarified (proxy first, LiveScript's SDK traffic deferred); LiveScript caching clarified as intentional; no team standard yet for Linear skills, and Carol is building the real one; Claude Code verbosity fixed by pinning instructions.
- [[2026-08-19 1-1 Matheus - Gabrielle]] — third 1:1, two Gemini segments (13 min + 26 min). Attribution **fully** collapsed to msilva (worse than prior transcripts). Part 1: A5 Watcher's two candidate designs (per-project vs. multi-tool consolidator) — the latter a candidate reconciliation for the Luís/Gabrielle proxy-scoping conflict; A1+A2 one-or-two; A3-vs-A7 axis is complexity/governance, not speed. Part 2: full A6/A9–A14 walkthrough (A6's four functions, A9 vs. A3, A10↔A11↔A13 overlap, A12's ownership); a *possible* Gabrielle/Luís convergence on Watch (unconfirmed); a Carol/Luís debate on how much upfront context agentic coding actually needs; msilva's own two pains (cross-project backlog, discovery minimum) as new "which agent first" input.
- [[2026-08-19 1-1 Matheus - Luís]] — fourth 1:1 with Luís, same day as the Gabrielle 1:1 above, 15:01, ~32 min. **Settles proxy app identification**: URL path, not `X-App-Id` header ([[2026-08-19 Identify proxy apps by URL path, not header]]) — retires the header-injection problem on [[How LiveScript sends the proxy X-App-Id header]]. Linear reorg walked live: only the active project needs structure now, delete the rest of the restored backlog, msilva has full autonomy on Linear decisions. Dev-subagent design clarified as project-harness scope, not Agent Flow architecture; introduces [[Claude Agent SDK]]. Luís becomes a third independent voice confirming the cross-project visibility pain.
- [[2026-08-20 1-1 Matheus - Gabrielle]] — fifth 1:1, ~17m30s, regular check-in four days before Gabrielle's leave. `PRO-94` (Pulumi language) noted still open at meeting time (Luís sick Tue–Thu) — **resolved later the same day**, Go, see [[2026-08-18 Bring options to Luís before deciding, communicate async and often]]; msilva reconsidering the Fluxo Agêntico diagram's shape; Carol looped into Agent Flow conversations with a weekly check-in; Gabrielle's leave refined (reachable early on, disconnecting while traveling).
- [[Meeting prep - accounting data in Claude - 2026-08-17]] — prep notes for the 2026-08-17 accounting-data-in-Claude meeting.
- [[Meeting prep - first agent decision]] — **one-pager** for the deadline meeting with Gabrielle and Luís: recommendation (A1 + A2), the four candidates in a line each, six decisions needed before 2026-08-24. Meeting date not yet recorded.
- [[Meeting prep - Agent Flow discovery with Carol - 2026-08-19]] — general Agent Flow discovery agenda, not limited to one agent (**renamed 2026-08-19 from "A5 Watcher discovery" at msilva's direction** — that was too narrow). Five threads, each compared against what the [[2026-08-19 1-1 Matheus - Gabrielle]] conversation already gave us (flagged as attribution-collapsed, uncertain evidence, not settled fact): which-agent-first, Watcher/A5 scope (asked cold, per [[2026-08-18 1-1 Matheus - Luís]]), A6-vs-skills-repo overlap, her intranet tool as a possible A10, and context minimalism.

## Syntheses
- [[What should the Agent Flow research phase study]] — **status board and router** for the agent research: what's settled, what's open, what to ask before Gabrielle's leave. The A5-first row is struck through as of 2026-08-18. Same day: **Luís thinks A5/Watcher shouldn't be scoped to the proxy** (corrected 2026-08-19, narrower than the earlier "decoupled entirely" reading), contradicting the morning's framing — blocks further A5 design pending discovery with Gabrielle and Carol. **2026-08-19**: a candidate reconciliation and a possible convergence added; plus a third "which agent first" framing around the team's own pain.
- [[Comparing the first-agent candidates]] — **the decision doc.** Motivations, pros and cons for A1+A2, A5, A7 and A4 side by side, what applies whichever is chosen, and eight things to decide before 2026-08-24. Written to be handed to someone else. **2026-08-19**: a third framing (team's own immediate pain) and msilva's two stated pains noted, not yet folded into the comparison itself.
- [[Which agent should be built first]] — **now answers two criteria.** Lowest-risk (2026-08-17) → A5 Watcher on proxy telemetry; utility (2026-08-18) → the intake pair A1 + A2, also msilva's position. Both arguments kept in full, plus why A7 cannot be chat-only. **Also 2026-08-18**: Luís separately argues A5 shouldn't target the proxy at all — unreconciled. **2026-08-19**: a candidate reconciliation (Watch as multi-tool consolidator) and a possible, unconfirmed convergence toward Luís's view.
- [[How to implement A5 Watcher]] — build plan: alert rules as code, throttling as cost control, Linear as dedup store, and how to build most of it before GC-5 lands.
- [[How LiveScript sends the proxy X-App-Id header]] — **superseded 2026-08-19**: GC-5 client side, shipped via a `pnpm patch` (SDK) + centralized REST injection, **reverted 2026-08-18** to bring alternatives to Luís first; a 5-option comparison followed (patch / customHeaders-only, insufficient / REST migration / SDK upgrade, impossible / fetch interceptor). All of it retired by [[2026-08-19 Identify proxy apps by URL path, not header]] — identification moves off headers entirely. Kept as a record of what was investigated. Still corrects the "drop the PAT / X-Api-Key" onboarding model.

## Reference
- [[Claude capabilities map - accounting data scope]] — Claude capabilities by layer for large-data/accounting work, cheapest-to-adopt first.
- [[Sharing the accounting automation with the team]] — distribution options for getting one person's workflow to a team. Bears directly on [[Agent Flow]]'s sharing problem.
- [[Sharing via Projects - the accounting project]] — Projects as a sharing mechanism; what belongs in one.
- [[AIRTABLEGC-34]] — ticket notes (legacy Jira).
- [[Zed Cheatsheet]] — editor keybindings.
- [[Claude Code Working Habits]] — pinning behavior into project instructions, artifact opt-in, IDE integration, cross-model review. From Luís, 2026-08-18.
