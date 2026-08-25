---
type: project
status: active
updated: 2026-08-25
aliases: [bossabox, bossabox assessment]
tags: [bossabox, vendor, ai-transformation, agent-flow]
---

# Bossabox Engagement

Not msilva's own project, but he and Luís were both on the 2026-08-25
discovery call — Carolina Bezerra is the one driving it. Tracked because
it targets close to the same problem space as [[Agent Flow]]. See
[[2026-08-25 Bossabox AI transformation discovery]] for the meeting this
page is built from, including a real caveat: msilva, Luís and Carolina
were all picked up on Carolina's microphone, so most of the transcript's
Livemode-side attribution below is not reliably any one of them
specifically.

## What Bossabox is

An external vendor/consultancy selling an AI-driven engineering
transformation product. History, per JV Abreu (Bossabox): started as an
internal tool, **"AI Framework"** — Claude Code instructions, subagents
and skills meant to standardize their own teams' AI usage, delivery-first
then expanded to discovery/upstream/downstream. Evolved into a product,
**"Sistema Operacional" (OS)** — v1 replicated the AI Framework's logic as
an actual app (UI, backend, an agent layer on **Agno**). Now on **OS V2**:
a deliberate step back to formalize the underlying *ontology*, since every
client's process differs — the goal is a common vocabulary ("sistema de
inteligência") layered under whichever tools a team already uses (Jira,
GitHub, Linear, Notion...), not a replacement for them.

## How they engage

Two axes:

- **Scope**: **módulos** (narrow slice, faster to prove value) vs.
  **modelo operacional** (the whole thing — viable mainly for a team
  starting from zero).
- **Build mode**: **default** (their own pre-built solutions, inherited
  from the AI Framework) vs. **custom** (bespoke to the client).

Both sit under **"design build."** Before any of it, they run an
**assessment**: a **readiness** score (AI-maturity by category) plus a
**VSM/DORA**-based flow diagnostic, runnable per-squad and comparable
across squads — sometimes surfacing a bottleneck common to all of them
rather than squad-specific.

## Relationship history

- **A prior, unidentified engagement exists.** Both Pedro and the Livemode
  side refer to "the other project" where Bossabox already demoed their
  methodology to Livemode — not named in
  [[2026-08-25 Bossabox AI transformation discovery]], not otherwise on
  record in this wiki. *(unverified — worth asking directly.)*
- **Livemode already moved off Airtable once for something in this
  space** — *"esse foi o motivo da gente ter saído do Airtable."*
  Distinct from [[Airtable Proxy]]/[[LiveScript]]'s own Airtable usage;
  not otherwise identified.

## This round — 2026-08-25 discovery call

The Livemode side (Carolina, msilva, and/or Luís — see the attribution
caveat above) names Livemode's real pains directly to Bossabox — see the
full list on [[2026-08-25 Bossabox AI transformation discovery]]. The
headline one: *"Eu não tenho nada atuando no grande [...] de olhar para a
área como um todo"* — the same gap [[Agent Flow]]'s A10 Portfolio exists
to close, said the day after msilva committed to building it
([[2026-08-24 Start Agent Flow with A10 Portfolio]]).

**Next**: Bossabox brings a tailored assessment proposal next Tuesday,
15:00 (afternoon, scheduled around Luís's availability).

## Relevance to Agent Flow

- **The same core pains get named again** — cross-project visibility/
  prioritization, no area-wide view — after Gabrielle, Luís, and Carol
  converged directly with msilva ([[2026-08-24 Start Agent Flow with A10
  Portfolio]]). Whether this is genuine outside corroboration or just
  msilva/Luís restating their own case to a vendor depends on who was
  actually speaking — unresolved, see the attribution caveat.
- **Two pains not yet on Agent Flow's radar**: (1) predictability/
  consistency of AI-assisted delivery as the metric to optimize, not raw
  token or commit-size counts; (2) post-launch usage-effectiveness — did a
  shipped, prioritized feature actually get used — as the piece that
  closes the loop on prioritization. The second overlaps with A11 Product
  (usage analysis), which Carol separately argued isn't genuinely
  transversal because it needs a shipped product to analyze — this is a
  live example of exactly that: a post-launch-only concern.

## Open questions

- **Who actually said what on the Livemode side?** See the attribution
  caveat — determines whether the pains above are outside corroboration
  or msilva/Luís hearing themselves back.
- Is this engagement something [[Agent Flow]] should coordinate with,
  track as a parallel effort, or treat as fully independent?
- What was "the other project"?
- Does msilva need visibility into future Bossabox conversations, given
  the overlap with his own work?
