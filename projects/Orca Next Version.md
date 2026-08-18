---
type: project
status: active
updated: 2026-08-18
aliases: [orca roadmap, orca v4, next version of orca, orca-produto, orca novas versões]
tags: [orca, cde, roadmap, linear, pm]
---

# Orca Next Version

> Related: [[Orca (CDE)]] · [[Airtable Proxy]] · [[Agent Flow]]

> [!note] Not msilva's project
> Tracked here for context, the way [[Pulse]] and [[Farol]] are. msilva gained
> read access to the planning repo (`orca-produto`, local path
> `C:\Users\msilva\projects\orca-produto`) on 2026-08-18; he does not appear as
> an owner or stakeholder in any of its documents.

## What it is

The roadmap/backlog planning workspace for the **next version of
[[Orca (CDE)]]** — Livemode's production computer-vision/audio system. The repo
(`orca-produto`) is **not the product's code** — that lives at
`livemode-org/livemode-comprovante-de-entregas` on GitHub. It's a
Claude Code/Cursor-driven workspace that turns stakeholder discovery into a
Linear backlog: `project/DISCOVERY.md` → `project/PRD.md` / `project/NFR.md` →
`project/ROADMAP.md` → issues in Linear.

Linear project: **"ORCA - Novas Versões"**
(linear.app/projetos-livemode/project/orca-nova-versao-de54f2b1eca2).

## Timeline

- **2026-07-17** — meeting "ORCA | Próximos Passos": Yuri Muanes (ops, Brazil)
  and Lucas Gomes (ops, Portugal) debrief World Cup usage pain with Pedro Alves
  (engineering) and Gabrielle Ferreira (product/PM).
- **2026-08-01** — `DISCOVERY.md` groups the pain points into strategic
  opportunities (A–F) and proposes epics E1–E9.
- **2026-08-07** — roadmap of **28 items across 7 milestones (E1–E7)** approved
  by stakeholders; Linear structure created same day. Detailed delivery
  schedule still pending (Pedro Alves to share).
- **2026-08-17** — per [[2026-08-17 Weekly - Projetos e Tarefas]]: **nothing
  deployed yet**. "Pedrinho" (likely Pedro Alves) still on documentation and
  the wordmap. Deliveries planned 2026-08-17, -21, -24, -28; project targeted
  to close **2026-08-28**. See [[Orca (CDE)]] for why this is not a
  contradiction with Orca being in production.

## Milestones (execution order, not the source PDF's numbering)

| # | Focus |
|---|---|
| E1 | Infra/DevEx/Investigation — CI+staging, `orca.livemode.space` domain migration, Claude Code environment, **MAM-as-source-of-truth spike** |
| E2 | Export, data, integrations — stable brand/analysis IDs, configurable CSV export, "live TV" delivery type, cross-competition brand report |
| E3 | Analysis lifecycle — versioned reanalysis with one "current" version, visible analysis status, readable failure reasons |
| E4 | Atelier & delivery registration — duplicate-material warning, Drive import inside Atelier, auto 15s logo-placement blocks |
| E5 | Video ingestion & Drive — unify virtual-cut behavior across upload paths, clearer Drive import errors, spike on the operation's new "Central" material-distribution system |
| E6 | Events & Airtable integration — auto-unlink Airtable on delete, one Live Script across multiple events, competition Tier on event screen |
| E7 | Player & results review — table filters/sort/legend, playback speed control, frame-capture timing fixes |

Explicitly **out of scope this round**: an external client/auditor login
portal ("Orca as a client platform" — contingent on the MAM spike), audio-based
brand recognition ("namings"), and syncing brand/format taxonomy to a new
source of truth (blocked on a separate taxonomy discussion with Carolina
Bezerra).

## Why this matters to msilva's work

- **Resolves the "Orca or LiveScript?" targeting question** in [[Agent Flow]]
  and the flagged contradiction in
  [[Comparing the first-agent candidates]] — see [[Orca (CDE)]].
- [[Airtable Proxy]] records Orca as a **planned proxy consumer**; this repo is
  now the place to check whether that migration is actually sequenced (still
  unconfirmed as of 2026-08-18).
- E1's MAM spike and E6's product-positioning question are unresolved
  cross-team decisions, not yet execution-ready — worth knowing if Orca's
  timeline is ever load-bearing for an Agent Flow decision.

## Open questions

- Is Orca's migration behind the Airtable Proxy on this roadmap at all, or a
  separate, unscheduled effort? Not mentioned in `ROADMAP.md`.
- Confirm "Pedrinho" (2026-08-17 weekly) = Pedro Alves.
- Did the E1 MAM/IVC legal spike start?
