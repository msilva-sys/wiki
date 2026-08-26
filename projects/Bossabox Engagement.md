---
type: project
status: active
updated: 2026-08-26
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

**The "camada de sustentação" concept, in Camila's own words** (2026-08-25
call): *"Uma empresa tem os seus sistemas de registros [...] o que a gente
quer construir na verdade é um sistema de inteligência para todos esses
registros."* The pitch isn't to replace Jira/Linear/GitHub — it's to be
"a camada de baixo sustentando essas ferramentas," so a Linear ticket
"vire contexto certo para quando entrar para desenvolvimento," and that
context can originate even *before* the tracked tool — e.g. a Notion doc
from a strategy meeting that defined the initiative in the first place.
The layer is meant to be editable directly (adjust an agnostic skill
rather than a tool-specific one) rather than locked inside whichever tool
happens to hold it. Their own stated rationale for why this matters:
every tool biases the process somehow — *"quantas vezes mexendo no Jira
você quer mudar alguma coisa, ah, não pode porque o Jira não deixa"* —
and framed this as the same shape of problem that made Livemode leave
Airtable once already (see below). The **job to be done** they named for
this layer: give management visibility into whether AI usage is actually
effective — *"o pessoal tá usando certo? [...] o que que eu posso fazer
para eles usarem mais certo?"* — not just orchestration, usage
observability.

## How they engage

Two axes, both explained in more depth on the 2026-08-25 call
(`raw/bossabox.txt`, ~13:55–17:30) than the deck alone gives:

- **Scope: módulos vs. modelo operacional.** Not a menu choice — a
  response to something they learned building the product: *"vai ser
  muito difícil a gente conseguir transformar, principalmente numa
  operação que já existe, que já tá, que tem várias pessoas, a gente
  mudar o modelo operacional de uma vez só [...] vai demorar bastante."*
  **Modelo operacional** (the whole picture, adopted at once) is viable
  mainly for a team starting a squad from zero — nothing existing to
  reconcile. **Módulos** (the same picture sliced into addressable
  pieces) is what they call the "**caminho mais natural**" for an
  operation already running, like Livemode's — faster to prove value,
  easier to align on whether it worked. What's constant across both:
  the same three-way premise — **software + método + IA embutida em
  tudo** — is guaranteed regardless of which path is taken. Concrete
  example they gave (a different client): a Slack-based module for
  ticket generation/prioritization used by a non-technical CX team — the
  user-facing flow is a chat with a context-aware agent, but a full agent
  layer sits behind it, built for that one value stream only. That's what
  a "módulo" looks like in practice.
- **Build mode: default vs. custom** — this axis lives *inside* each
  scope choice (a módulo or the whole modelo operacional can each be
  default or custom). **Default** = solutions they've already built,
  inherited from the AI Framework, "nível de customização pequeno."
  **Custom** = they take what's already built and evolve/adapt it to the
  client's specific context — the more customized form. Their stated
  rationale isn't just commercial: different contexts genuinely need
  different bars — *"eu vou entrar num contexto mais regulatório [...]
  tem uma outra régua [...] uma outra suíte de teste [...] eu deveria ter
  mais liberdade para poder customizar."* This same reasoning is what
  they cite as the motivation for **OSV2** (the ontology layer) — any
  tool (Linear, Jira, paper, Notion) biases the flow in some way, and
  they want a tool-agnostic "sustentação" underneath.

**Where the assessment fits both axes**: it's what decides *which*
módulo to address and *how customized* it needs to be — not a separate
upfront step, but the input to both choices: *"a gente roda esse
diagnóstico, entende de fato onde que estão esses gargalos e depois
[decide o resto]."*

Both sit under **"design build"** — Bossabox's name for the whole
engagement journey (design/diagnose *and* implement, bundled as one
journey rather than separate consulting-then-delivery phases), not a
distinct product step. The assessment lives *inside* this journey, not as
a pre-sale gate before it — per JV/Pedro (unattributed between the two)
on the 2026-08-25 call, *"a gente tem uma jornada que a gente chama de
design build [...] isso fica dentro dessa jornada de design build."*
Before any of it, they run an
**assessment**: a **readiness** score (AI-maturity by category) plus a
**VSM/DORA**-based flow diagnostic, runnable per-squad and comparable
across squads — sometimes surfacing a bottleneck common to all of them
rather than squad-specific.

> [!note] Método and Assessment, in concrete detail — 2026-08-26
> [[Bossabox Assessment - institutional deck]] (their sales deck,
> emailed as a follow-up — [[LiveMode e Bossabox Services - email
> thread]]): the **Método** is a fixed 12-stage pipeline (Backlog →
> Protot. → Design Critique → Epic Spec → DoD Approval → Tech Refining →
> Ready to Dev → In Dev → QA(Dev) → QA(Stage) → Ready to Prod → Prod
> Validate), upstream/downstream split at the Ready-to-Dev boundary,
> **+40 agents and 8 quality gates** embedded throughout, MCP connectors
> to whatever tools a client already uses. The **Assessment** itself
> reads directly from **Jira, GitHub, Linear, Confluence, and
> interviews** — nearly Livemode's exact toolset — and comes in a
> **free entry tier** (hypothesis-level, no cost) and a **paid, scoped
> deep tier** (validated root cause, AI Readiness score, executable
> plan). The proposal expected next ([[2026-08-25 Bossabox AI
> transformation discovery]]'s "Tuesday, 15:00") is probably the free
> tier — not confirmed.

## Relationship history

- **A prior, unidentified engagement exists — direction confirmed by
  msilva (2026-08-26).** Both Pedro and the Livemode side refer to "the
  other project," where **Bossabox demoed their own methodology to
  Livemode** (not the reverse — msilva confirmed this directly after the
  raw transcript read ambiguously on this point). The project itself is
  still not named anywhere in this wiki.
- **[[Pulse]] ("Pulso") confirmed as the same project** referenced in
  that same exchange (msilva, 2026-08-26). The Livemode side, comparing
  Bossabox's demoed methodology against their own day-to-day work, named
  Pulso as the exception rather than the norm: *"o pulso, né, que agora
  tem nome, né, aquele projeto da esteira do comercial, é assim uma das
  soluções mais robustas que a gente tem aqui dentro [...] as nossas
  soluções, o que a gente de fato trabalha aqui no dia a dia, são
  projetos que vão ser mais simples do que aquilo."* Two things this adds
  to [[Pulse]]: independent confirmation that "Pulso" is the name in
  active use (bearing on that page's open question about the
  *Fronte de Negócios 2.0* naming lineage), and that it's regarded
  internally as unusually robust/complex relative to Livemode's typical
  project.
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
15:00 (afternoon, scheduled around Luís's availability). **Confirmed
2026-08-26**: Pedro Arantes opened a dedicated email thread ([[LiveMode
e Bossabox Services - email thread]]) to centralize this, with the
Assessment deck attached — see the note above.

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

- **Who is "Edu"?** A second unidentified name on the Bossabox side,
  addressed directly at the close of the axes explanation (~17:20) —
  *"Beleza, acho que deu para entender um pouquinho aqui, Edu."* Not in
  the attendee list (Pedro Arantes, JV Abreu, Camila Sande). Same shape
  of problem as the "Davi"/"Divi" mistranscription already flagged on
  [[2026-08-25 Bossabox AI transformation discovery]] — could be a
  garbled name of someone already on the call, or a fourth participant.
  Not investigated further.
- **Who actually said what on the Livemode side?** See the attribution
  caveat — determines whether the pains above are outside corroboration
  or msilva/Luís hearing themselves back.
- Is this engagement something [[Agent Flow]] should coordinate with,
  track as a parallel effort, or treat as fully independent?
- What was "the other project" — direction confirmed (Bossabox demoed to
  Livemode), but the project itself still isn't named anywhere.
- Does msilva need visibility into future Bossabox conversations, given
  the overlap with his own work?
