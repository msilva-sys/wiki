---
type: index
updated: 2026-08-17
---

# Index

Catalog of every page in this wiki, one line each. Updated on every ingest.
See [[log]] for the chronological record and `CLAUDE.md` for the schema.

## Projects
- [[Airtable Proxy]] — Go reverse proxy fronting the Airtable API; v1 is observability-only, caching and rate-limiting deferred. Primary track.
- [[Agent Flow]] — proposed multi-agent architecture for the area's intake and delivery; design only. Second onboarding track, no deadline.

## Systems
- [[LiveScript]] — collaborative script editor, heaviest Airtable consumer, the reason the proxy exists. Also called *roteiros*.
- [[Proxy Environments]] — environment/config matrix for the Airtable proxy (local, dev, prod).

## Concepts
- [[Airtable Rate Limits]] — 5 req/s per base, 429 → ~30s lockout, `Retry-After`; the constraint driving the whole programme.

_(next candidates: OpenTelemetry, Reverse Proxy Patterns, Grafana LGTM)_

## People
_(empty — deliberately; see [[log]] 2026-08-17)_

## Decisions
- [[2026-08-10 Onboarding runs proxy and agent flow in parallel]] — proxy first and fast, agent flow alongside for context with no delivery pressure.

_(next candidates, extractable from [[Airtable Proxy]]: token-terminating auth, OTel/OTLP over BigQuery, Cloud Run min=1)_

## Sources
_(transcripts are filed under Meetings instead — see `CLAUDE.md`)_

## Meetings
- [[2026-08-10 Onboarding Técnico - Matheus]] — technical onboarding with Gabrielle Ferreira; origin of the proxy, the agent architecture, and how the two tracks split.
- [[Meeting prep - accounting data in Claude - 2026-08-17]] — prep notes for the 2026-08-17 accounting-data-in-Claude meeting.

## Syntheses
_(empty)_

## Reference
- [[AIRTABLEGC-34]] — ticket notes.
- [[Zed Cheatsheet]] — editor keybindings.
