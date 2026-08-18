---
type: index
updated: 2026-08-18
---

# Index

Catalog of every page in this wiki, one line each. Updated on every ingest.
See [[log]] for the chronological record and `CLAUDE.md` for the schema.

> [!danger] Outstanding — rotate the Airtable PAT and Firebase service-account key
> Both sat in plaintext in this vault from 2026-08-11 and remain readable in **6
> git commits**. The working tree is clean; history is not. Rotation is the action
> that matters and only msilva can do it. Details:
> [[Proxy Environments]]. Reported by every `lint` since the first.

## Projects
- [[Airtable Proxy]] — Go reverse proxy fronting the Airtable API. Primary track. Active work: app auth + key distribution. **Orca and other services are planned consumers** (2026-08-18) — it is the area's shared Airtable data plane, not LiveScript infrastructure.
- [[Agent Flow]] — proposed multi-agent architecture for the area's intake and delivery; design only. Second track, started 2026-08-17. **Which agent to build first is reopened** (2026-08-18): the criterion changed from lowest-risk to utility.
- [[Pulse]] — outsourced front-end for the business pipeline. Not msilva's, but he joins the immersion week for context.

## Systems
- [[LiveScript]] — collaborative script editor, heaviest Airtable consumer, the reason the proxy exists. Also called *roteiros*.
- [[Proxy Environments]] — environment/config reference for the proxy and LiveScript. Credentials redacted 2026-08-17.

## Concepts
- [[Airtable Rate Limits]] — 5 req/s per base, 429 → ~30s lockout, `Retry-After`; the constraint driving the whole programme.
- [[Packaging as skills]] — converged from six sources: packaging is both the token-cost lever and the sharing mechanism. What it doesn't solve is maintenance.
- [[Agents read primary sources]] — agents query the underlying data rather than wait for the agent meant to digest it. Three instances (A5, A7, A1); it is what makes *anarchic-first* buildable, and it is why context retrieval is **not** a fifteenth agent. Splits A6 into tools + curation.

_(next candidates: OpenTelemetry, Reverse Proxy Patterns, Grafana LGTM)_

## People
_(empty — deliberately; see [[log]] 2026-08-17)_

## Decisions
- [[2026-08-10 Onboarding runs proxy and agent flow in parallel]] — proxy first and fast, agent flow alongside with no delivery pressure.
- [[2026-08-14 Migrate project management from Jira to Linear]] — Linear becomes the system of record; proxy issues to be restructured.
- [[2026-08-14 No mandatory PR review while the proxy is pre-production]] — msilva merges his own work; Luís reviews history after the fact.
- [[2026-08-17 A5 receiver runs on Cloud Run]] — plain container, `min-instances=0`. Records why n8n, Cloudflare Workers and co-location were not chosen, and what would reopen each.

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
- [[Meeting prep - first agent decision]] — **one-pager** for the deadline meeting with Gabrielle and Luís: recommendation (A1 + A2), the four candidates in a line each, six decisions needed before 2026-08-24. Meeting date not yet recorded.

## Syntheses
- [[What should the Agent Flow research phase study]] — **status board and router** for the agent research: what's settled, what's open, what to ask before Gabrielle's leave. The A5-first row is struck through as of 2026-08-18.
- [[Comparing the first-agent candidates]] — **the decision doc.** Motivations, pros and cons for A1+A2, A5, A7 and A4 side by side, what applies whichever is chosen, and eight things to decide before 2026-08-24. Written to be handed to someone else.
- [[Which agent should be built first]] — **now answers two criteria.** Lowest-risk (2026-08-17) → A5 Watcher on proxy telemetry; utility (2026-08-18) → the intake pair A1 + A2, also msilva's position. Both arguments kept in full, plus why A7 cannot be chat-only.
- [[How to implement A5 Watcher]] — build plan: alert rules as code, throttling as cost control, Linear as dedup store, and how to build most of it before GC-5 lands.
- [[How LiveScript sends the proxy X-App-Id header]] — GC-5 client side: **implemented** via a `pnpm patch` (SDK) + centralized REST injection; data-plane honours the endpoint, metadata pinned direct. Corrects the "drop the PAT / X-Api-Key" onboarding model.

## Reference
- [[Claude capabilities map - accounting data scope]] — Claude capabilities by layer for large-data/accounting work, cheapest-to-adopt first.
- [[Sharing the accounting automation with the team]] — distribution options for getting one person's workflow to a team. Bears directly on [[Agent Flow]]'s sharing problem.
- [[Sharing via Projects - the accounting project]] — Projects as a sharing mechanism; what belongs in one.
- [[AIRTABLEGC-34]] — ticket notes (legacy Jira).
- [[Zed Cheatsheet]] — editor keybindings.
