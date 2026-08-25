---
type: meeting
status: active
updated: 2026-08-25
date: 2026-08-25
attendees: [Carolina Bezerra, Pedro Arantes, JV Abreu, Camila Sande]
source: "raw/bossabox.txt"
transcription_confidence: high
tags: [bossabox, vendor, agent-flow, discovery, prioritization, process]
aliases: [bossabox discovery, bossabox pitch]
---

# Bossabox AI transformation discovery

> [!warning] Date is unverified — from file mtime, may be the download date
> The raw file (`raw/bossabox.txt`, a saved tl;dv page) carries no meeting
> date in its content or filename. `2026-08-25` is the file's filesystem
> creation time, which dates when it was **saved to disk**, not necessarily
> the call itself. One internal clue narrows it: Carolina, scheduling the
> follow-up, second-guesses whether a holiday falls on "day 1" or "day 7"
> of the next month — almost certainly **7 de setembro** (Independência),
> which places this call in **late August 2026**, consistent with the mtime.

**msilva was not on this call.** Attendees are Carolina Bezerra (Livemode)
and three reps from **Bossabox**, a vendor pitching an AI-transformation
assessment and engagement: Pedro Arantes, JV Abreu, Camila Sande. Filed
under `raw/` because it bears directly on [[Agent Flow]] — Carolina
describes, unprompted and to an outside party, close to the same pains
[[Agent Flow]] already exists to address.

> [!note] Transcription quality
> Full tl;dv HTML export (word-by-word spans with timestamps), high
> confidence on wording and speaker attribution. One unresolved point:
> Pedro twice addresses someone as **"Davi"** / **"Divi"** who never speaks
> on the recording — possibly a mis-transcription, possibly a silent
> participant. Not identified; not assumed to be anyone already in this
> wiki.

## Decisions

- **Follow-up meeting set for next Tuesday, 15:00** (afternoon specifically
  — Carolina: *"não é toda manhã que o Luiz está com a gente, aí por isso
  que eu preferia fazer à tarde"*, an independent reconfirmation of Luís's
  part-time-afternoons pattern, from a context that has nothing to do with
  msilva).
- **The assessment runs at the level of Livemode's technical/engineering
  area as a whole**, not per-squad — Carolina: they don't have distinct
  squads, just ~3 large projects with people assigned. Framed explicitly as
  an **MVP for the rest of the company**: prove the model here, then apply
  it elsewhere.
- **Path forward: assessment before any transformation work.** Carolina
  picks this over relying on the team's own self-assessment — *"eu
  preferia fazer um assessment do que ter uma visão talvez contaminada do
  que a gente acha da gente mesmo."*

## Action items

- [ ] **Bossabox (JV)** — prepare and bring a tailored assessment/journey
      proposal for Livemode by next Tuesday, informed by this
      conversation and the (non-)squad structure discussed.
- [ ] **Bossabox** — send assessment reference material (a PDF pitch deck)
      ahead of the meeting if ready early; Carolina offered to have their
      own LLMs help review it in advance.

## Open questions

- **What was "the other project"** Bossabox already worked with Livemode
  on, where they first demoed their methodology? Referenced by both Pedro
  and Carolina as already-happened, not identified in this transcript or
  elsewhere in this wiki. *(unverified — worth asking directly.)*
- **Who is "Davi"/"Divi"?** See the transcription-quality note above.
- Is this Bossabox engagement something [[Agent Flow]] should track as a
  parallel/competing effort, coordinate with, or stay independent from?
  Not raised in this wiki yet — msilva wasn't on the call.

## Facts stated

- **Bossabox's product history**: started as an internal tool called **"AI
  Framework"** — Claude Code instructions/subagents/skills, meant to
  standardize what their own teams used AI for (delivery-focused first,
  later discovery/upstream/downstream too). Evolved into a full product,
  **"Sistema Operacional" (OS)** — v1 replicated the AI Framework's logic
  as an actual product (UI, backend, an agent layer built on **Agno**). Now
  building **OS V2**: a step back to formalize the *ontology* underneath —
  because every company's process differs, V2 aims to give a common
  vocabulary ("sistema de inteligência") layered under whatever tools a
  team already uses (Jira, GitHub, Linear, Notion...), rather than
  replacing them.
- **Two engagement shapes, crossed with two build modes.** Shape:
  **módulos** (narrow, faster to prove value) vs. **modelo operacional**
  (holistic, slower, only viable for a team building from zero). Build
  mode: **default** (their own pre-built solutions, inherited from the AI
  Framework) vs. **custom** (bespoke for the client's context). All of it
  sits under one umbrella they call **"design build."**
- **The diagnostic product**: an **assessment** combining a **readiness**
  score (AI-maturity by category) and a **VSM/DORA**-based flow diagnostic.
  Can run per-squad and compare squads against each other — sometimes
  surfacing a structural bottleneck common to all of them (e.g. every squad
  struggling at refinement) rather than a squad-specific one.
- **Carolina names Livemode's real pains directly to the vendor** (an
  independent, unprompted account — she wasn't talking to msilva):
  1. **Prioritization, at two layers**: which project to work on now, and
     which task to pick up next / how many people to staff on it.
  2. **Cross-tool collaboration within one project** is hard — everyone
     uses their own tools (*"a gente acabou indo mais para o cloud
     code"*), work gets stepped on, nobody has a shared, established flow.
  3. **AI usage today is individual, not standardized.** Someone finds a
     good tool, word spreads informally, everyone imitates — not a
     top-down direction. The company's own "use whatever tool you want"
     culture is good but works against standardization.
  4. **Linear adoption is the one area-wide initiative underway**, now
     wired into Cloud Code via MCP for task management — matches this
     wiki's own record of the Linear migration, from a source that has no
     idea this wiki exists.
  5. **Nothing looks at the whole area.** *"Eu não tenho nada atuando no
     grande, né, de olhar para a área como um todo, olhar todos os
     projetos."* This is [[Agent Flow]]'s A10 Portfolio gap, restated
     independently, to a vendor, the day after msilva committed to
     building exactly that — see
     [[2026-08-24 Start Agent Flow with A10 Portfolio]].
  6. **Estimation/predictability with AI-assisted development.** JV
     (Bossabox) reframes the metric: not tokens or commit size, but
     **consistency of throughput** — a team reliably shipping the same N
     tasks per period beats one that swings between 10/20/35.
  7. **Post-launch measurement is a real gap.** Carolina: they can monitor
     a site's health automatically, but can't yet tell whether a
     prioritization call was actually *effective* — whether a shipped
     feature gets used at all. She frames this as the piece that "closes
     the loop" on prioritization.
  8. **Context loss as the company scales.** *"Cada ano que passa, a gente
     dobra de tamanho, a gente vai perdendo contexto."* Today's shared
     whole-area understanding rests on a few people who happen to have it,
     not on any system.
- **Camila Sande (Bossabox) on the value chain**: AI connected to the
  actual codebase improves refinement quality (surfaces things even a
  senior dev would miss until mid-implementation), speeds development
  while reducing regressions/rollbacks, and production data feeds back
  into the system. Framed as "digitizing what's in people's heads" so
  process survives turnover and scales with headcount.
- **Prior context, not previously in this wiki**: Livemode had already
  moved off **Airtable** for something in this space — *"esse foi o motivo
  da gente ter saído do Airtable, porque a gente de fato começou a bater em
  limitações."* Distinct from [[Airtable Proxy]] and [[LiveScript]]'s own
  Airtable use — likely a different, earlier tool-migration decision, not
  identified further here.

## Notable quotes

- Carolina, on why assessment beats self-review: *"eu preferia fazer um
  assessment do que ter uma visão talvez contaminada do que a gente acha da
  gente mesmo."*
- Carolina, on the portfolio gap: *"Eu não tenho nada atuando no grande,
  né, de olhar para a área como um todo, olhar todos os projetos."*
- Carolina, on AI adoption culture: *"alguém vai lá, constrói uma coisa e
  fala assim, putz, eu usei a ferramenta tal [...] todo mundo começa a
  aplicar, mas muito mais pela lógica [...] do que por a gente ter um
  direcional aqui dentro."*
- Carolina, on scaling context loss: *"cada ano que passa, a gente dobra de
  tamanho, a gente vai perdendo contexto."*
- JV Abreu, reframing the metric for AI-assisted delivery: *"em vez de ter
  um time que entrega 20, 10, 35 [...] tem um time que entrega 20 de
  maneira constante."*

## What this changes elsewhere

| Page | Change |
|---|---|
| [[Agent Flow]] | Independent, third-channel validation of the A10 Portfolio gap (from Carolina to a vendor, not to msilva); two new pain points not yet on its radar — predictability/estimation as a metric, post-launch usage-effectiveness measurement |
| [[Carolina Bezerra]] | Her own unprompted framing of the team's pains to an outside party |
| [[Luís Fernandez]] | Independent reconfirmation of the afternoon-only availability pattern |
| [[Bossabox Engagement]], [[Pedro Arantes]], [[JV Abreu]], [[Camila Sande]] | New pages |
