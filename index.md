---
type: index
updated: 2026-08-17
---

# Index

Catalog of every page in this wiki, one line each. Updated on every ingest.
See [[log]] for the chronological record and `CLAUDE.md` for the schema.

## Projects
- [[Airtable Proxy]] — Go reverse proxy fronting the Airtable API. Primary track. Active work: app auth + key distribution.
- [[Agent Flow]] — proposed multi-agent architecture for the area's intake and delivery; design only. Second track, starts 2026-08-17.
- [[Pulse]] — outsourced front-end for the business pipeline. Not msilva's, but he joins the immersion week for context.

## Systems
- [[LiveScript]] — collaborative script editor, heaviest Airtable consumer, the reason the proxy exists. Also called *roteiros*.
- [[Proxy Environments]] — environment/config reference for the proxy and LiveScript. Credentials redacted 2026-08-17.

## Concepts
- [[Airtable Rate Limits]] — 5 req/s per base, 429 → ~30s lockout, `Retry-After`; the constraint driving the whole programme.

_(next candidates: OpenTelemetry, Reverse Proxy Patterns, Grafana LGTM)_

## People
_(empty — deliberately; see [[log]] 2026-08-17)_

## Decisions
- [[2026-08-10 Onboarding runs proxy and agent flow in parallel]] — proxy first and fast, agent flow alongside with no delivery pressure.
- [[2026-08-14 Migrate project management from Jira to Linear]] — Linear becomes the system of record; proxy issues to be restructured.
- [[2026-08-14 No mandatory PR review while the proxy is pre-production]] — msilva merges his own work; Luís reviews history after the fact.

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
- [[Meeting prep - accounting data in Claude - 2026-08-17]] — prep notes for the 2026-08-17 accounting-data-in-Claude meeting.

## Syntheses
- [[What should the Agent Flow research phase study]] — research agenda for the agent project; tracks, constraints, and what to ask before Gabrielle's leave.
- [[Which agent should be built first]] — A5 Watcher on proxy telemetry, and why. Includes the case against it, why A1 is the better second build, and the GC-5 timing dependency.
- [[How LiveScript sends the proxy X-App-Id header]] — GC-5 client side: **implemented** via a `pnpm patch` (SDK) + centralized REST injection; data-plane honours the endpoint, metadata pinned direct. Corrects the "drop the PAT / X-Api-Key" onboarding model.

## Reference
- [[Claude capabilities map - accounting data scope]] — Claude capabilities by layer for large-data/accounting work, cheapest-to-adopt first.
- [[Sharing the accounting automation with the team]] — distribution options for getting one person's workflow to a team. Bears directly on [[Agent Flow]]'s sharing problem.
- [[Sharing via Projects - the accounting project]] — Projects as a sharing mechanism; what belongs in one.
- [[AIRTABLEGC-34]] — ticket notes (legacy Jira).
- [[Zed Cheatsheet]] — editor keybindings.
