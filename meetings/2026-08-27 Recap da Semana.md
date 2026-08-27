---
type: meeting
status: active
updated: 2026-08-27
date: 2026-08-27
attendees: [Maria Fernanda Lemos, Yasmin Macedo, Matheus Silva, Carolina Bezerra]
source: "raw/Recap da Semana - 2026_08_27 15_06 GMT-03_00 - Anotações do Gemini.md"
transcription_confidence: low
aliases: [recap 27/08, weekly recap agosto 27]
tags: [farol, livescript, agent-flow, talisa, taxonomia, meeting]
---

# Recap da Semana — 2026-08-27

> [!warning] Transcription quality — everything labelled as one speaker, again
> Gemini attributed **every line to "Maria Fernanda Lemos,"** the same
> failure mode as [[2026-08-25 Farol - Dados]] and the 2026-08-10
> onboarding transcript. **msilva confirmed the real attendee list**:
> Mafê, Yasmin Macedo, msilva, and Carolina Bezerra — four people, not
> one. Two long blocks are attributed with above-average confidence on
> **topical match, not diarization**: Mafê's own extensive multi-project
> update (Farol, LiveScript, low-code docs, Talisa, Airtable access,
> Jorge/Valesca support, Taxonomia — all areas already established as
> hers elsewhere in this wiki), and msilva's own update (the proxy,
> Fluxo Agêntico/A14, his Linear-discipline reflection — areas only he
> owns). **Everything else — clarifying questions, coaching about
> documentation vs. communication, the "Mafê, where do you open these
> tasks?" prompt — is Yasmin or Carolina, genuinely unclear which.**

## Decisions

None — a status recap, not a decision meeting.

## Commitments

- Maria Fernanda: give Marina (finance) a deadline (a day or a few more)
  to review Farol's Expresso data and report back what's missing.
- Maria Fernanda: open the Linear task today for LiveScript's "copy
  multiple notes at once" feature, now that Luís has handed the item over.
- Maria Fernanda: finish the Taxonomia "depara" (field-mapping) work
  herself before handing dates/prioritization to the team.
- msilva (or the group): decide whether to reschedule the Papo de
  Projetos session originally meant to cover Linear — Carolina/Luís's
  calendars didn't align this week.

## Open questions

- **Is proactive LiveScript feature-monitoring/pruning the team's job?**
  Maria Fernanda raised it as genuinely unresolved — see Facts below and
  "What this changes elsewhere."
- Should Taxonomia get its own `systems/` or `projects/` page, matching
  [[Pulse]] and [[LiveScript]], now that a real status update exists for
  it? Not created here — not enough was said to justify it yet, but
  worth revisiting.
- What exactly happened with "Live Mansion" ("redes" project) — its own
  history is murky even to the speakers themselves. Not pursued further.

## Facts stated

**By Maria Fernanda Lemos** (high confidence — matches her established
role across the wiki almost exactly):

- **Farol**: met with Marina (finance) Tuesday, walked her through BigQuery
  access and which tables she can see. Expresso's data is now in the
  database and has been sent to her, along with two "skills" built to
  help her understand table structures and what she can pull from them.
  Only **Uber** remains — Luís was trying to get ahead on it, but focus
  stayed on closing Expresso first, so little progress there yet.
- **LiveScript**: met with Luís yesterday (2026-08-26) for a technical
  overview of how the product works — she'd been away from it ~40 days
  before the World Cup. He also handed her the "copy multiple notes at
  once" feature request (already fully specified) to open as a Linear task.
  Also recounted a **historical bug**: early in the year, someone (Gabi?)
  ran tests that created several Brasileirão *roteiros*; when everything
  was migrated into LiveScript's infrastructure, those test roteiros had
  to be deleted and regenerated. Luís already mapped and fixed it — it
  doesn't recur once regenerated.
- **Raises an open question about LiveScript's maintenance model**: is it
  the team's job to proactively reach out to users about new needs, or
  purely reactive? Concretely: a feature ("congelar espelho") is complex
  to maintain and, per metrics, barely used — should it be cut
  proactively, or does that require reaching out to areas first? Genuinely
  undecided in her own words — see "What this changes elsewhere."
- **Low-code documentation project**: auditing and documenting every
  low-code solution she's built to date; some (the Monday-related ones)
  are being handed to João Victor. Some old solutions' continued use is
  unconfirmed — e.g. one built for "produção executiva" months ago, no
  contact since to confirm it's still active.
- **Talisa** (new name — see people page): onboarding this week — met
  with Luís about the team's tools (GitHub, the reasoning behind each);
  taking a Google course for women in tech and a "MENS" course this week
  and tomorrow, alongside Rafa and a *jovem aprendiz* participant. Found
  the work "abstract" — Maria Fernanda plans to ground it with concrete
  examples (roteiro, SBEC, Fluxo Agêntico) and suggested the team share
  their own experience with her directly. Not currently in Papo de
  Projetos — Luís asked her not to for now, and she's busy with courses.
- **Airtable access governance**: handled two support requests this week
  — a new Olympics-broadcast hire ("Leca") needed help understanding
  which Airtable bases to access; someone from finance asked (in person,
  not via Slack) about a dedicated Airtable account for a specific
  finance sub-area. Explains the actual model: **Airtable only allows one
  owner/admin**, and access is granted **collectively per area via a
  shared general email**, not individually — so a dedicated account
  request is routine, not risky.
- **Also supporting, in parallel**: Valesca (building an executive-
  production hub on Vercel/GitHub, wants to unify it with an existing
  credentialing base Maria Fernanda built); Jorge (building a matriz
  transmission agent — the same Jorge from
  [[2026-08-25 Agent Flow discovery with Mafê]]); a TES-integration
  demo (feedback given by Camila and Bia; Arthur couldn't test it,
  citing international competitions).
- **Taxonomia**: reviewed the whole database with the team, found real
  issues — marca/produto/submarca registration needs a DB change (holding
  until a decision on whether it rides with "pulso"); a new "tier de
  jogo" field needs adding to the matriz, with a full field-mapping
  ("depara") required; a rights ("direitos") issue already resolved.
  Plans a closing meeting, then takes the depara work as her own task
  before handing dates to the team.
- **"Redes" (Live Mansion)**: a long-running, historically ownerless
  project (news curation via X/Instagram APIs, plus a "efemérides"
  database of club anniversaries) — deliverables are shipping now but
  the project's own history is murky even in her retelling (multiple
  past owners, someone who left and came back, "Ren Boy" recently joined).

**By msilva** (high confidence — matches only his own project ownership):

- Reports on the Airtable Proxy backlog (14 items, some possibly
  subtasks not being counted) and Fluxo Agêntico/LiveScript-Vercel-
  visibility items, migrated from the old Jira backlog.
- **The proxy is paused, waiting on a review from Luís** — sent him the
  requested documentation this week; a separate LiveScript branch is
  also waiting on Luís for unrelated changes.
- **Started implementing the first Fluxo Agêntico agent — the product
  one** (i.e. A14 PM Agent / the A10+A14 M1 scope already decided in
  [[2026-08-24 Build A10 and A14 together, PoC first]]). Luís asked him
  to start generating more docs for the team (architecture, decisions)
  so his work doesn't stay only in his own head — done via Claude,
  iterating on his own ideas rather than copy-pasting a generated
  summary.
- **Reflects on Linear discipline, using the proxy as his own example**:
  since nobody external demands the proxy project, dates depend entirely
  on his own sense of pacing — and generalizes this to Farol, whose
  dates in Linear are "all overdue," arguing an overdue or missing date
  is functionally the same (neither helps anyone track the project).
  Describes his own practice: connecting Linear to Claude Code so it
  proactively flags at-risk dates rather than relying on memory.
- **On LiveScript's maintenance-model question** (responding to Maria
  Fernanda): draws a line between **internal build/code-quality
  monitoring** (the team's own responsibility — noticing a better
  approach exists, or that a feature adds disproportionate complexity
  for its actual usage) and **user-facing usability prioritization**
  (primarily the users' responsibility to raise, unless feedback volume
  becomes overwhelming). Names this explicitly as **input toward one of
  the agents Agent Flow needs to build** — an agent that watches a
  product's code and usage and surfaces suggestions, rather than someone
  manually digging through PostHog-style metrics.

**Unclear speaker (Yasmin or Carolina)**:

- Asks Maria Fernanda a clarifying question early on about whether any
  Farol data is actually missing (vs. just needing Marina's review).
- Gives msilva structured feedback distinguishing **documentation** from
  **communication** — sharing what you're doing and how, so others can
  give feedback and catch overlapping work, is the actual goal; this
  shouldn't calcify into "everything needs a doc."
- Prompts Maria Fernanda directly ("Mafê, where do you open these
  tasks?") — answered consistently with her established intake channel
  (Slack → Airtable).

## Notable quotes

- Maria Fernanda, on LiveScript: *"até que ponto a gente deve ou assumir
  essas coisas que ninguém tá usando ou tipo entrar em contato com as
  áreas para entender... quais são as novas versões futuras."*
- msilva: *"o projeto da proxy não é ninguém tá demandando da gente...
  é a sua sensibilidade de olhar, ver o que que tem que ser feito."*
- Unclear speaker, to msilva: *"Eu não sinto falta de documentação. Eu
  acho que o ponto é comunicação."*

## What this changes elsewhere

- **Farol status update**: Expresso done and handed to Marina; only Uber
  remains. Updates [[Farol]] and [[Marina]].
- **A concrete, real discussion of exactly the kind of agent [[Agent
  Flow]]'s A5 Watcher / A11 Product were meant to be** — msilva's
  internal-vs-user-facing monitoring split is a genuinely new framing,
  not yet in the wiki, for where a "watches the product" agent's
  responsibility should start and stop.
- **A live precedent for Linear+AI self-monitoring** — msilva's own
  Claude-Code-to-Linear deadline-nudging practice is an ad hoc version of
  exactly what A10/A14 are meant to formalize for the whole team.
- **New person**: Talisa, onboarding this week — [[Talisa]].
- **LiveScript history**: the Brasileirão-roteiros regeneration bug,
  first recorded here.
