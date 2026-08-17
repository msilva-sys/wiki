---
type: meeting
status: active
updated: 2026-08-17
date: 2026-08-14
attendees: [Gabrielle Ferreira, Maria Fernanda Lemos, Yasmin Macedo, Matheus Silva]
source: "raw/Recap da Semana - 2026_08_14 14_01 GMT-03_00 - Anotações do Gemini.txt"
transcription_confidence: low
tags: [recap, weekly, process, proxy, farol]
---

# Recap da Semana (2026-08-14)

Weekly Friday status meeting — a consolidated per-person overview of the week and
any concerns, walked through the team board. Explicitly not a detail meeting.

> [!note] Scope
> Most of this meeting is other people's project status plus hiring and team
> organization. Per msilva's standing instruction only what bears on his own work
> is recorded here, with other projects named as landmarks so they're
> recognizable later. The rest stays in the raw file.

## msilva's own description of the proxy

The clearest statement of the project in his own words, and the version the wider
team heard:

> "O proxy é um serviço que vai ficar em frente ao Airtable, então toda requisição
> pro Airtable vai passar pelo proxy e aí a gente vai ter mais controle sobre quem
> faz as requisições e como faz."

Three things this pins down:

- **Metrics being collected**: whether a request uses filters rather than
  returning the whole table, and **which application** is making it.
- **Scoped to LiveScript first, general by design** — *"primeiramente eu tô
  fazendo isso num contexto só do live script […] mas a ideia é para ser geral
  para todos os serviços aqui da empresa."*
- **Independent confirmation that LiveScript is roteiros** — msilva says it
  plainly: *"o live script, que é o de roteiros."* [[LiveScript]] previously
  recorded this on his say-so in conversation; it's now in the source record.

He also described [[Agent Flow]] to the team as automating **"desde os PRDs […]
até a entrega"** — from PRDs through delivery. That's a narrower and more concrete
scope than the architecture diagram implies, and it makes the PRD corpus flagged
in [[Proxy Environments]] directly relevant.

## Correction to an earlier page

I previously recorded the code-review restructuring as confirmed twice for
msilva's proxy. **Reading the full transcript, that's wrong**, and the detail
matters:

- What was described here is **Gabrielle's workflow on the Farol project**, not
  the proxy.
- The reviewer in that loop was **an AI in a separate session** — deliberately
  started fresh so it wouldn't carry the author's context — not Luís. It was
  dropped because review cycles took too long and returned too many changes she
  judged unnecessary: *"ele mandava um monte de coisa para alterar que
  sinceramente eu nem acho que era tão [necessário]."*
- The current flow: do the task, final review inside the task, and if nothing
  critical, close it and merge. No PR.
- **The governance documentation is being rewritten to match**, because the
  project's tooling kept insisting on Luís's approval per the old documented flow.

The direction of travel is the same as msilva's arrangement, but it's a separate
instance with a different reviewer. [[2026-08-14 No mandatory PR review while the
proxy is pre-production]] has been corrected accordingly.

## Practical items for msilva

- [ ] **Add tasks to the team board** — noted in passing that he hadn't yet.
      Worth closing alongside
      [[2026-08-14 Migrate project management from Jira to Linear]].
- [ ] **Gabriel's request was routed to msilva by Gabrielle** as a good match for
      his knowledge — the Monday meeting in
      [[2026-08-14 1-1 Matheus - Gabrielle]]. It arrives as a formal request, not
      just a chat.
- External requests now flow **Slack form → Airtable base → team board**, so
  work arrives as a tracked item. Requests go in the *projetos* channel, not IT.

## Meeting rhythm at Livemode

| Meeting | Purpose |
|---|---|
| **Recap da Semana** (Fri) | Consolidated status per person; concerns; walks the team board |
| **Papo de Projetos** | Company/area news, culture, tools, ideas — not task status. See [[2026-08-14 Papo de Projetos]] |
| **1:1** (weekly, first month) | See [[2026-08-14 1-1 Matheus - Gabrielle]] |

## Other projects named

Landmarks only — no detail captured:

- **Farol** — pulls corporate travel data (Uber, hotels, Expresso) via API into a
  database for the finance team, replacing manual spreadsheet work. Currently in
  its "bronze" phase: land raw data, no transformation. Blocked on a **GCP
  project only Luís can create**, since he holds the key.
- **Escala** — scheduling automation, formally closed and handed to the networks
  team, who built their own spreadsheet-plus-Claude solution during the World Cup
  after the general-purpose rules didn't survive Copa conditions.
- **Pulse / esteira de negócios** — see [[Pulse]].
- **Intranet**, **automatic lives creation**, **external events registration app**
  — other active threads in the area.
- **TES**, an AI-orchestration vendor, is being trialled with legal and finance
  participating. Noted only because it overlaps [[Agent Flow]]'s problem space.
