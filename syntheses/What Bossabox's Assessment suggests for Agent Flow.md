---
type: synthesis
status: active
updated: 2026-08-26
date: 2026-08-26
aliases: [bossabox vs agent flow, assessment cross-read]
tags: [agents, bossabox, dora, vsm, team-topologies, research]
---

# What Bossabox's Assessment suggests for Agent Flow

msilva asked directly: how do [[Bossabox Assessment - institutional
deck]] and [[LiveMode e Bossabox Services - email thread]] enrich
[[Agent Flow]]'s planning? Cross-read against the existing wiki, not
just the deck read in isolation.

## 1. Independently confirms an open gap: automated QA

[[Agent Flow]] already flags: *"nothing among the 14 covers automated
QA — possibly a gap, possibly deliberate."* Bossabox's Método gives QA
**two explicit named stages with gates** (QA Dev, QA Stage), inside a
pipeline built from +200 iniciativas. An unrelated production
methodology treating QA as first-class, not incidental, is evidence
toward "gap," not "deliberate omission" — worth resolving rather than
leaving open.

## 2. A second decomposition axis worth checking the 14 agents against

Agent Flow is organized **by agent** (actor → input → output, anarchic,
no fixed sequence). Bossabox's Método is organized **by pipeline stage**
(Backlog → Protot. → Design Critique → Epic Spec → DoD Approval → Tech
Refining → Ready to Dev → In Dev → QA(Dev) → QA(Stage) → Ready to Prod →
Prod Validate), with agents distributed across stages and gates at
specific points.

These are genuinely different lenses, and Agent Flow has only used one.
Worth a pass mapping the 12 Método stages against the 14 agents to see
if anything is silently assumed rather than covered — **Design
Critique**, **DoD Approval**, and **Tech Refining** in particular don't
map cleanly onto any single named agent today.

## 3. Independent, convergent validation of a settled design call

[[Agents read primary sources]] settled that Agent Flow's agents read
Linear/repos/Loki/`Brain` **directly**, rather than through an
intermediary digest agent — the rule that makes anarchic-first possible
at all. Bossabox's Assessment engine does exactly this on its own
production system: reads **Jira, GitHub, Linear, Confluence, and
interviews** directly, no digestion layer named. An unrelated vendor
converging on the same architecture, for the same reason (no
intermediary to build first), is real evidence the rule is right — not
just a Livemode-specific workaround.

## 4. A ready-made metric vocabulary for A10 Portfolio

Two open gaps this fills at once:

- [[Agent Flow]]: *"How often does the area start a new project?
  msilva has no metric (2026-08-18)."*
- The [[2026-08-25 Agent Flow discovery with Mafê]] / [[2026-08-25 1-1
  Matheus - João Victor]] data-source discovery work is currently
  inventing what to measure from scratch.

Bossabox's example dashboard is a proven, standard instrument set: **DORA**
(deploy frequency, lead time, change-failure rate, recovery time) for
delivery rhythm, and **VSM** (dwell time per pipeline stage) for where
time actually goes. A10 doesn't need to invent a metric vocabulary —
it can adopt these and skip straight to "which of our tools can produce
them."

## 5. A more concrete version of A4 Teacher's vague maturity ladder

The [[Meeting prep - Agent Flow discovery with Mafê and João Victor -
2026-08-25]] diagnostic questions reference an **"L0–L3" literacy
framing** for A4 Teacher that was never actually specified. Bossabox's
**AI Readiness** scorecard (0–4, across adoção de IA, dados,
infraestrutura, entrega, qualidade/testes, observabilidade,
contexto/specs) is a working example of exactly that kind of ladder —
worth comparing directly next time A4's diagnostic questions get
revisited, rather than defining L0–L3 from nothing.

## 6. Names a risk pattern already scattered across the wiki, never unified

Bossabox's **Team Topologies** instrument explicitly surfaces bus-factor
risk (their case study: *"a Plataforma depende de uma única pessoa para
a infra"*). Livemode already has at least two recorded instances of the
same shape, never named this way:

- [[Luís Fernandez]] — sole owner of GCP/Cloud Run/Pulumi structuring;
  also the one who flagged the GCP account-tier isolation risk himself.
- [[Arthur Tavares]] — sole knowledge-holder of the matriz de eventos
  schema, moving to another area with **no successor recorded**.

Neither is currently anyone's job to track. Worth considering whether
this becomes something A6 Curator (or a revived monitoring function)
owns, rather than staying implicit.

## 7. The build-vs-buy question this raises, unasked until now

Bossabox's **free entry-tier Assessment** would produce, at no cost,
much of what A10 Portfolio's current data-source discovery
(Mafê, João Victor, and whatever comes next) is doing by hand: a flow
diagnostic, an architecture risk map, a team-topology read. That's a
real strategic question nobody has raised yet — does it make sense to
let Bossabox's free tier run and use it to accelerate/validate Agent
Flow's own research phase, instead of msilva building the same
diagnostic from scratch?

Two things temper this rather than settle it:

- **Carolina's already-recorded skepticism** about spending team energy
  on externally-driven projects ([[Farol]]'s low-priority opinion,
  repeated independently twice) could apply here too — this is Bossabox
  proposing more work, not less, even in the free tier (a meeting,
  review, follow-through).
- **The corroboration problem is unresolved**: [[2026-08-25 Bossabox AI
  transformation discovery]] already flagged that the pains named to
  Bossabox might be msilva/Luís echoing their own case, not outside
  validation. Using Bossabox's assessment to *validate* Agent Flow's
  direction only works if that circularity is actually broken —
  worth deciding explicitly rather than assuming either way.

## Open questions

- Does the 12-stage-vs-14-agent mapping (point 2) actually surface a
  real gap, or does it wash out once someone does it carefully?
- Should A10 Portfolio adopt DORA/VSM as its metric vocabulary outright,
  or is Livemode's shape different enough to need its own?
- Is bus-factor tracking (point 6) in scope for any of the 14 agents as
  currently specified, or a genuine fifteenth concern?
- Build-vs-buy (point 7) — worth raising with Carol/Gabrielle explicitly,
  or too early before the free-tier assessment itself even happens?
