---
type: meeting
status: active
updated: 2026-08-18
date: 2026-08-14
attendees: [Carolina Bezerra, Luis Fernandez, Arthur Tavares, João Victor Andrade, Yasmin Macedo, Michelly Magalhães, Matheus Silva]
source: "raw/2026-06-14-Papo de Projetos  - Anotações do Gemini.docx"
transcription_confidence: medium
tags: [governance, orca, hub, brain, process]
---

# Papo de Projetos (2026-08-14)

Area-wide meeting for company news, culture, tools, and ideas — explicitly *not*
task status, which belongs in [[2026-08-14 Recap da Semana]]. Run by Carolina
Bezerra, largely a walkthrough of the area's new documentation Hub.

> [!note] Re-ingested 2026-08-17
> The first pass read only Gemini's summary header (32 of 309 lines) and
> everything on this page was second-hand. Now rebuilt from the full transcript.
> **Transcription quality is better here than the other three** — Gemini labelled
> multiple speakers correctly rather than attributing everything to one person.

> [!warning] The filename date is wrong
> The raw file is named `2026-06-14-Papo de Projetos`, but its content header
> reads **`ago. 14, 2026`**. Filed as 2026-08-14 per the
> file-contents-over-filename rule in `CLAUDE.md`. Raw filename left untouched.

> [!note] Scope
> Org chart, hiring, DISC profiles, and role transitions were covered and are
> **deliberately not recorded**, per msilva's standing instruction. What follows
> is what bears on the technical work.

## Orca — much stronger than previously recorded

The first pass recorded only that Orca is "essential to operations." The full
transcript is far more specific, and it **upgrades Orca from a plausible target
to the obvious one** for the first monitoring agent in [[Agent Flow]]:

- **It is a machine-learning system** — *"um projeto bem bem técnico […] lida com
  algoritmo, com machine learning."*
- **Not reproducible by the wider company** — *"não é qualquer um que vai saber
  criar um produto desse aqui dentro ainda."*
- **In production and depended upon** — once it shipped, the team using it
  couldn't work without it.
- **Its value is quantified**: losing the automation would mean *"contratar 10
  cabeças"* — hiring ten people. Hence *"isso não pode estar fora do ar."*

A business-critical ML system that cannot go down is close to an ideal first
subject for a monitoring agent: high stakes, clear owner, and the value of an
alert is obvious to everyone.

## The team's own products

Named as the systems this area built and owns: **CRM**, **Orca**,
**[[LiveScript]]**, **Taxonomia** (in progress), and **[[Pulse]]**.

Useful correction of scale: [[LiveScript]] is one of five, not the centre of the
world — though it is the one driving the [[Airtable Proxy]].

**[[Pulse]]** got a sharper definition: the business pipeline whose end product is
delivering closing material to the finance team. That's why it classifies on the
sensitive-financial-data criterion.

## The Brain repository and the Hub — direct precedent for this wiki

Carolina walked through a centralized page covering the company, the area, its
people, its projects, and reference documents — an explicit stand-in for the
intranet Livemode doesn't have.

**Mechanically it is close to what this vault is:**

- Content lives as **Markdown in a GitHub repository called `Brain`**, and is
  rendered into the Hub. **Edits are made to the `.md` files, not the page.**
- Per-project pages carry **architecture, diagnosis, decisions, and README** —
  nearly the same taxonomy as `projects/` and `decisions/` here.
- Some content is synced from **Airtable**, which is why parts are wrong: the
  structure *"não tá 100% correta"* and several entries were visibly incorrect
  during the walkthrough.
- **Only the CRM project page is populated so far.** The rest are shells.

> [!important] Worth reconciling before building anything
> `Brain` is an existing, sanctioned, company-wide knowledge base with the same
> shape as this wiki, and [[Agent Flow]] proposes an institutional-memory agent
> that would overlap both. Three things follow: read `Brain` before designing
> that agent; consider whether proxy and agent documentation belongs there rather
> than only here; and note that msilva has write access via GitHub and was
> **asked** to correct anything wrong in his own entries.

> [!danger] The ask was repeated with a deadline of 2026-08-18
> In [[2026-08-17 Weekly - Projetos e Tarefas]] everyone was asked to open the Hub,
> **check their own entry, and report corrections** — explicitly *"entre hoje e
> amanhã"*, i.e. **by 2026-08-18**, because Gabrielle is away after that. Arthur was
> excused. Someone reported theirs was empty; the link is in the projects group.
>
> The Hub had appeared broken only because a deploy had been forgotten — it works
> again, so "it was broken" is no longer a reason not to look.
>
> This is the second time msilva has been asked. It is also the cheapest possible
> first read of `Brain`, which [[Agents read primary sources]] flags as **the
> highest-value unread thing** before designing any retrieval layer.

## Governance changes that affect the work

**A homologation flow is being built** — an application, with the support team,
to validate architecture, tooling, security, and data governance **before** a
large project is implemented. After homologation, either the originating area
maintains the system or it transfers to this team. Nothing was said about whether
the [[Airtable Proxy]] will pass through it.

**Project ownership is the area manager's call.** Luís raised Farol as a grey
zone — it carries financial data but is being built by the finance team
themselves. Carolina's answer: the manager decides whether their area is equipped
to own it; if not, it becomes a corporate project and transfers. A **governance
document** is being written to help managers make that judgement.

> [!success] Farol's grey zone was settled three days later
> Luís, in [[2026-08-17 Weekly - Projetos e Tarefas]], answering directly whether
> Farol ends up maintained by this team or by finance: *"a parte de dados pela gente,
> o restante todo por eles. A gente só entrega dados para eles no final das contas."*
> **Split by layer, not assigned wholesale** — which is a third option neither this
> meeting's framing nor the governance document anticipated.
>
> It also **contradicts this page's premise.** Farol is described above as *"being
> built by the finance team themselves"*, but on 2026-08-17 Luís is doing the GCP
> structuring and Yasmin is testing it. Either the situation changed or the original
> characterization was wrong; the transcript doesn't say which. Recorded as a
> conflict rather than resolved in favour of either.

**Projects are expected to originate anywhere**, and this team enters as
*habilitador* only when asked — *"não é a gente que corre atrás."* This confirms
the enablers front in [[Agent Flow]] from a second, independent source.

## Two facts worth knowing

- **Luís is not full-time** — afternoons, with flexibility. As Tech Lead he
  validates architecture and systems, sometimes building directly, otherwise
  supporting. Relevant to
  [[2026-08-14 No mandatory PR review while the proxy is pre-production]]: the
  deferred-review arrangement is partly a consequence of limited availability.
- **The matriz de eventos was built by Gabriel (since departed) and maintained by
  Arthur Tavares**, who is moving to another area. The `AIRTABLE_MATRIZ_*` table
  and field IDs in [[Proxy Environments]] point at this data — so Arthur is the
  person to ask about that schema, and that knowledge is in transition.

## Adjacent to Agent Flow

Two items from Carolina's own goals that overlap [[Agent Flow]]'s territory:

- **A Claude Code training programme** for all areas, which Luís is helping build.
- **A first version of a "shared AI architecture" for Livemode** — a long-term
  goal, stated without detail.

Both suggest [[Agent Flow]] is not the only initiative in this space. Worth
understanding before designing in isolation. See also the TES vendor trial noted
in [[2026-08-14 Recap da Semana]].
