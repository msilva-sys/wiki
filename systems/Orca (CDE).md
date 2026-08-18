---
type: system
status: active
updated: 2026-08-18
aliases: [orca, cde, comprovante de entregas]
tags: [orca, cde, ml, computer-vision, audio, production]
---

# Orca (CDE)

> Related: [[Airtable Proxy]] · [[Agent Flow]] · [[LiveScript]] · [[Orca Next Version]]

> [!important] Resolves an open question flagged across five pages
> Whether "Orca" is a real, distinct system was unverified since
> [[2026-08-10 Onboarding Técnico - Matheus]], and a later contradiction
> ("in production" vs. "nothing deployed") was flagged as unresolved in
> [[Comparing the first-agent candidates]]. Confirmed 2026-08-18 by msilva gaining
> access to the `orca-produto` planning repo. See **Resolving the production
> contradiction** below.

## What it is

**Orca is Livemode's name for the CDE (Comprovante de Entregas)** — a
computer-vision/audio pipeline that proves display of commercial insertions
(brand logos, ad breaks) in live sports broadcasts. It replaced an outsourced
manual audit process ("Spot"). CDE is the formal/business name; the team calls
the product Orca day to day, and it ships versioned releases (the last major
one before this ingest was **v3.2.0**, sprints May–July 2026).

Named alongside the area's other four owned products — **CRM**, Orca,
[[LiveScript]], **Taxonomia** (in progress), and [[Pulse]] — in
[[2026-08-14 Papo de Projetos]].

**Source of this page**: the `orca-produto` planning repo (msilva gained access
2026-08-18) — `README.md`, `PROJETO.md`, `project/DISCOVERY.md`,
`project/ROADMAP.md`, `assistant/decisions/2026-08-07-retorno-stakeholders-28-itens.md`,
`assistant/meetings/2026-07-17-orca-proximos-passos.md`. That repo is **not**
Orca's product code — see [[Orca Next Version]].

## Production status

- **In production, business-critical, cannot go down.** Per
  [[2026-08-14 Papo de Projetos]]: a machine-learning system whose automation is
  valued at roughly ten headcount (*"contratar 10 cabeças"*); losing it is not
  an option (*"isso não pode estar fora do ar"*).
- Domain today: `cde.livemode.space`. A pending migration to
  `orca.livemode.space` (with HTTPS) is scoped as roadmap work — see
  [[Orca Next Version]].
- Used hard during the 2026 World Cup by ops in Brazil (Yuri Muanes) and
  Portugal (Lucas Gomes) — thousands of deliveries processed, several rough
  edges surfaced (below).

## How it works, and where it's rough

- **Video ingestion is currently YouTube-dependent.** YouTube is used as the
  audit source of truth, which is fragile: live-stream cuts/reorganization,
  IP restrictions, 403 errors and rate limits broke audits during high-volume
  events. A candidate fix is an internal recording server, called **"Mã"/MAM**
  in discussions — plugging it in directly is technically possible per
  engineering (Pedro Alves), but migrating away from YouTube has a
  **non-technical blocker**: proving signal parity to external auditors (e.g.
  **IVC**), who today treat the YouTube link as the reference. (unverified:
  legal/commercial resolution — flagged as a formal spike, not yet done)
- **Material (asset) ingestion is largely manual** — uploads via a single
  linked Google Drive folder per event/property, with duplicate-material
  false positives when a fingerprinted file changes slightly (e.g. a QR code
  swap).
- **Integrates with Airtable** for event/brand data — the naming template
  ("gabarito") for formats and brands is pulled from Airtable today.
  [[Airtable Proxy]] records Orca as a **planned proxy consumer** (see below).
- **Integrates with [[LiveScript]]** via the "Live Script" (roteiro) concept —
  a planned-vs-executed comparison per event. Today one script can only link
  to one event, which breaks for continuous multi-game broadcasts (Kings
  League, weekend grids).
- **No stable brand/analysis identifiers yet** — brands and analyses are
  keyed by free-text name, blocking consistent reporting and integrations.
- **Product-positioning is unresolved internally**: is Orca the team's daily
  analysis workspace, or just the data engine behind another interface? Named
  in [[2026-08-14 Papo de Projetos]] and [[Orca Next Version]] (opportunity F /
  milestone E6) as an open, unresolved product question, not yet decided.

## Resolving the production contradiction

Two wiki pages recorded what looked like a contradiction and explicitly
flagged it as unresolved (see the warning in
[[Comparing the first-agent candidates]] and
[[What should the Agent Flow research phase study]]):

- [[2026-08-14 Papo de Projetos]]: Orca is **in production and indispensable**.
- [[2026-08-17 Weekly - Projetos e Tarefas]]: **"nothing is deployed"** — the
  developer (Pedrinho) is still on documentation and the wordmap.

**These describe different scopes, not a contradiction.** The first statement
is about the *existing, shipped* system (v3.2.0, in production since before
the World Cup). The second is about **the next-version roadmap** —
[[Orca Next Version]] — a separate body of work (7 milestones, ~28 issues,
Linear project "ORCA - Novas Versões", approved by stakeholders 2026-08-07)
that had not shipped anything as of the 2026-08-17 weekly. "Pedrinho" almost
certainly refers to **Pedro Alves**, the engineer named throughout the
`orca-produto` planning docs as owner of the E1 infrastructure milestone.
(unverified: nickname-to-name match, not stated explicitly in either source)

## Open questions

- Is the MAM/YouTube source-of-truth spike resolved, and did legal/commercial
  clear the IVC parity question?
- Is Orca's migration behind the [[Airtable Proxy]] sequenced or still just
  intended? ([[Which agent should be built first]] needs this dated.)
- Confirm "Pedrinho" = Pedro Alves.
