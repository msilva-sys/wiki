---
type: meeting
status: active
updated: 2026-08-26
date: 2026-08-25
attendees: [Carolina Bezerra, Matheus Silva, Luís Fernandez, Pedro Arantes, JV Abreu, Camila Sande]
source: "raw/bossabox.txt"
transcription_confidence: low
tags: [bossabox, vendor, agent-flow, discovery, prioritization, process]
aliases: [bossabox discovery, bossabox pitch]
---

# Bossabox AI transformation discovery

> [!warning] Date is unverified — from file mtime, may be the download date
> The raw file (`raw/bossabox.txt`, a saved tl;dv page) carries no meeting
> date in its content or filename. `2026-08-25` is the file's filesystem
> creation time, which dates when it was **saved to disk**, not necessarily
> the call itself. One internal clue narrows it: the "Carolina" speaker,
> scheduling the follow-up, second-guesses whether a holiday falls on
> "day 1" or "day 7" of the next month — almost certainly **7 de setembro**
> (Independência), which places this call in **late August 2026**,
> consistent with the mtime.

> [!danger] Corrected 2026-08-25 by msilva — speaker attribution on the Livemode side is unreliable
> **msilva and Luís Fernandez were both physically on this call.** The
> transcript labels every Livemode-side turn as one continuous "Speaker A"
> — all three of them (Carolina Bezerra, msilva, Luís) were picked up on
> **Carolina's microphone**, so tl;dv's diarization folded them into a
> single voice. Everything below previously attributed to "Carolina
> Bezerra" by name is really **the Livemode side, unattributable among the
> three of them** — it could be Carolina, msilva, or Luís. Kept as direct
> quotes (the wording is solid — see the transcription note below on that),
> but the speaker name is now a **placeholder, not a fact**. Where content
> makes one attribution implausible (e.g. a line referring to *"o Luiz"* in
> the third person is not Luís himself) that's noted inline; otherwise
> treat "Livemode side" as genuinely three-way ambiguous, not a soft guess
> at Carolina specifically. *This also means some of what's recorded here
> as "an outside, unbiased read of Livemode's pains" may actually be
> msilva's or Luís's own words reflected back — not a fresh outside
> validation. Worth msilva confirming directly if he remembers who said
> what, especially the prioritization framework and the Farol/monitoring
> opinions below.*

Bossabox reps Pedro Arantes, JV Abreu and Camila Sande pitch an
AI-transformation assessment and engagement. Filed under `raw/` because it
bears directly on [[Agent Flow]] — the Livemode side describes, to an
outside party, close to the same pains [[Agent Flow]] already exists to
address.

> [!note] Transcription quality
> Full tl;dv HTML export (word-by-word spans with timestamps) — wording and
> the Bossabox-side speaker tags (Pedro Arantes, JV Abreu, Camila Sande) are
> high confidence. Overall confidence downgraded to **low** because of the
> mic-conflation issue above: roughly half the transcript's speaker
> attribution is unreliable. Separately, Pedro twice addresses someone as
> **"Davi"** / **"Divi"** who never speaks on the recording — possibly a
> mis-transcription of "JV", possibly a fourth, silent participant. Not
> identified.

## Decisions

- **Follow-up meeting set for next Tuesday, 15:00** (afternoon
  specifically — Livemode side: *"não é toda manhã que o Luiz está com a
  gente, aí por isso que eu preferia fazer à tarde"*. The third-person
  reference to Luís means this specific line wasn't him — either Carolina
  or msilva. Independently reconfirms Luís's part-time-afternoons pattern
  regardless of which of the two said it).
- **The assessment runs at the level of Livemode's technical/engineering
  area as a whole**, not per-squad — Livemode side: they don't have
  distinct squads, just ~3 large projects with people assigned. Framed
  explicitly as an **MVP for the rest of the company**: prove the model
  here, then apply it elsewhere.
- **Path forward: assessment before any transformation work.** Livemode
  side picks this over relying on the team's own self-assessment — *"eu
  preferia fazer um assessment do que ter uma visão talvez contaminada do
  que a gente acha da gente mesmo."*

## Commitments

- **Bossabox (JV)** — prepare and bring a tailored assessment/journey
  proposal for Livemode by next Tuesday, informed by this
  conversation and the (non-)squad structure discussed.
- **Bossabox** — send assessment reference material (a PDF pitch deck)
  ahead of the meeting if ready early; the Livemode side offered to
  have their own LLMs help review it in advance.
- msilva — confirm, if he remembers, which of himself/Carolina/Luís
  actually said the substantive opinions below (prioritization
  framework, Farol, monitoring priority) — currently unattributable.
  *(deixado em aberto por escolha, 2026-08-26 — msilva optou por não
  perseguir isso.)*

## Open questions

- **Who said what on the Livemode side?** The central open question from
  this ingest — see the correction banner above. Everything tagged
  "Livemode side" below could be Carolina, msilva, or Luís.
- **What was "the other project"** Bossabox already worked with Livemode
  on, where they first demoed their methodology? Referenced by both Pedro
  and the Livemode side as already-happened. **Direction confirmed by
  msilva 2026-08-26**: it was Bossabox who demoed to Livemode (the
  transcript read ambiguously on this point). The project itself is still
  not named anywhere in this wiki — see [[Bossabox Engagement]] for the
  related "Pulso"/[[Pulse]] confirmation from the same exchange.
- **Who is "Davi"/"Divi"?** See the transcription-quality note above.
- Is this Bossabox engagement something [[Agent Flow]] should track as a
  parallel/competing effort, coordinate with, or stay independent from?

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
- **"Design build"** is Bossabox's name for the whole engagement journey,
  not a separate product step — *"a gente tem uma jornada que a gente
  chama de design build. Todo o coração daquilo que a gente tá fazendo é
  desenhar uma solução e implementar ela para vocês."* Design (diagnose +
  plan) plus build (implement), bundled as one journey rather than a
  consulting phase handed off to a separate delivery phase. The
  **assessment lives inside it**, not as a pre-sale gate before it: *"isso
  fica dentro dessa jornada de design build [...] por isso que eu quis
  colocar isso dentro aqui do design build."*
- **Two engagement shapes, crossed with two build modes**, both under that
  design-build umbrella. Shape: **módulos** (narrow, faster to prove
  value) vs. **modelo operacional** (holistic, slower, only viable for a
  team building from zero). Build mode: **default** (their own pre-built
  solutions, inherited from the AI Framework) vs. **custom** (bespoke for
  the client's context).
- **The diagnostic product**: an **assessment** combining a **readiness**
  score (AI-maturity by category) and a **VSM/DORA**-based flow diagnostic.
  Can run per-squad and compare squads against each other — sometimes
  surfacing a structural bottleneck common to all of them (e.g. every squad
  struggling at refinement) rather than a squad-specific one.
- **The Livemode side names real pains directly to the vendor** — see the
  attribution caveat above; this could be Carolina, msilva, or Luís, in any
  combination across the points below:
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
     wiki's own record of the Linear migration.
  5. **Nothing looks at the whole area.** *"Eu não tenho nada atuando no
     grande, né, de olhar para a área como um todo, olhar todos os
     projetos."* This is [[Agent Flow]]'s A10 Portfolio gap, restated to a
     vendor the day after msilva committed to building exactly that — see
     [[2026-08-24 Start Agent Flow with A10 Portfolio]]. **If this line is
     msilva's or Luís's own words** rather than an outside-to-the-decision
     voice, it's restating the case rather than independently
     corroborating it — see the correction banner.
  6. **Estimation/predictability with AI-assisted development.** JV
     (Bossabox) reframes the metric: not tokens or commit size, but
     **consistency of throughput** — a team reliably shipping the same N
     tasks per period beats one that swings between 10/20/35.
  7. **Post-launch measurement is a real gap.** The Livemode side: they can
     monitor a site's health automatically, but can't yet tell whether a
     prioritization call was actually *effective* — whether a shipped
     feature gets used at all. Framed as the piece that "closes the loop"
     on prioritization.
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

- Livemode side, on why assessment beats self-review: *"eu preferia fazer
  um assessment do que ter uma visão talvez contaminada do que a gente
  acha da gente mesmo."*
- Livemode side, on the portfolio gap: *"Eu não tenho nada atuando no
  grande, né, de olhar para a área como um todo, olhar todos os
  projetos."*
- Livemode side, on AI adoption culture: *"alguém vai lá, constrói uma
  coisa e fala assim, putz, eu usei a ferramenta tal [...] todo mundo
  começa a aplicar, mas muito mais pela lógica [...] do que por a gente
  ter um direcional aqui dentro."*
- Livemode side, on scaling context loss: *"cada ano que passa, a gente
  dobra de tamanho, a gente vai perdendo contexto."*
- JV Abreu, reframing the metric for AI-assisted delivery: *"em vez de ter
  um time que entrega 20, 10, 35 [...] tem um time que entrega 20 de
  maneira constante."*

## What this changes elsewhere

| Page | Change |
|---|---|
| [[Agent Flow]] | Downgraded from "independent third-channel validation" to "possibly independent" — the A10 Portfolio gap restated to a vendor, but speaker may be msilva or Luís, not a fresh outside voice |
| [[Carolina Bezerra]] | Attribution of her opinions here withdrawn — could equally be msilva's or Luís's words |
| [[Luís Fernandez]] | Attendance corrected to present; availability-pattern quote is *about* him, not necessarily *by* anyone identifiable as not-him |
| [[Bossabox Engagement]], [[Pedro Arantes]], [[JV Abreu]], [[Camila Sande]] | Pages stand; Livemode attendee list corrected |
