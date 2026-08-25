---
type: meeting
status: active
updated: 2026-08-25
date: 2026-08-25
attendees: [Carolina Bezerra, Luís Fernandez]
source: "raw/Farol _ Dados - 2026_08_25 17_39 GMT-03_00 - Anotações do Gemini.md"
transcription_confidence: low
aliases: [farol dados meeting, farol architecture call]
tags: [farol, data, gcp, airtable, bigquery, governance]
---

# Farol | Dados — 2026-08-25, 17:39

> [!warning] Transcription quality — everything labelled as one speaker
> Gemini attributed **every line to "Carolina Bezerra,"** the same failure
> mode seen on the 2026-08-10 onboarding transcript. The content is
> clearly two voices in live back-and-forth — one explains the pipeline
> architecture in technical detail and argues to hand code + a DB copy to
> Marina; the other pushes back on priority and defends a data-team-vs-
> area-team boundary. **Attendees above are a best guess, not
> diarization**: the technical-explainer voice matches [[Luís Fernandez]]
> (he owns Farol's GCP structuring elsewhere in the wiki, and the ownership
> argument matches his 2026-08-17 *"a parte de dados pela gente, o resto
> por eles"* stance); the priority-skeptic voice matches
> [[Carolina Bezerra]]'s already-recorded, independent 2026-08-24 opinion
> that Farol shouldn't have been a priority — restated here almost
> verbatim, which is why this attribution is trusted more than a blind
> guess. Below, facts are marked **(voice: Luís, inferred)** /
> **(voice: Carol, inferred)** rather than asserted as diarized fact.
>
> **msilva's presence is unclear.** He never speaks. One line — *"aí o
> Mateus tá por fora completamente"* — talks about him in the third
> person, which could mean he was silently present (hence having the
> Gemini export at all) or simply referenced while absent. Not resolved.

## Decisions

- **Airtable is ruled out as the store for this data — settled on volume,
  not preference.** Farol's raw data runs ~612–750k records/year; Airtable
  Enterprise caps around 500k records per base. *(voice: Carol, inferred,
  running the numbers live on the call)*
- **Ingestion + dedup logic stays owned by the data team; a copy of the
  resulting database + the ingestion code goes to Marina (finance) for
  her own downstream use.** Concretely: Marina gets the GitHub repo, a
  copy of the DB (3–5 tables), and permission to build whatever she wants
  on top, with strong alerting so a break surfaces immediately. What she
  does with it — joining OnFly with Expresso with Uber, her own merges —
  is her problem, not the data team's. *(voice: Luís proposes it, voice:
  Carol pushes back — "eu fico zero confortável com isso, mas tô
  entendendo teu direcionamento" — then agrees)*
- **The bronze layer stays raw-ish, exposed via views, not further
  modeled.** A control table tracks which job run is "the last complete
  one" so only that snapshot is exposed — this replaces an earlier,
  unwanted attempt at merging/deduplicating records into a smaller,
  modeled dataset, walked back because it made the pipeline logic
  "complexo pra caramba."

## Action items

- [ ] Luís (or whoever owns the "programa de parceiros" API integration):
  talk to **Mariana** about how the partner-program REST API should be
  wired in. *(Possibly the same person as "Marina" below — see Open
  questions.)*
- [ ] Confirm with Marina/finance whether they're comfortable with the
  repo + DB-copy handoff model once it's actually built.

## Open questions

- **Is "Marina" (finance, will consume/maintain the Farol output) the
  same person as "Mariana" (mentioned once, re: the partner-program API)?**
  Transcription inconsistency, not confirmed either way.
- **Was msilva actually in this meeting?** See the warning above.
- **Which GCP database product?** Still not named — [[Farol]]'s existing
  open question ("BigQuery is the obvious guess... never named") stands;
  this meeting only rules out Airtable, doesn't name the replacement.
- Does **Bruno**'s team (financeiro, a separate case re: Totvs data) ever
  get its own database person, or does integration work keep defaulting
  to the data team regardless? Flagged as a repeat structural pattern by
  the priority-skeptic voice, not resolved.

## Facts stated

**(voice: Luís, inferred)** — architecture and delivery mechanics:

- **Four external data sources feed Farol**: two by REST API (Uber, and
  one more likely OnFly/Expresso judging by [[Farol]]'s existing "Uber,
  OnFly, Expresso" list), one SFTP with XML, and **Itaú** via a "reverse"
  SFTP — the bank pushes files to Livemode rather than the usual
  direction. Expresso is mid-migration to API. A separate "programa de
  parceiros" integration uses a REST API, still being worked out with
  *Mariana* (see Open questions).
- **Volume**: full-year data lands around 612k–750k records once you
  multiply out the daily row count (cited as ~17 rows/day for at least
  one source, times 365) — this is what ruled out Airtable (see
  Decisions).
- **A dedup mistake and its fix**: at some point the pipeline started
  merging/modeling records into a reduced dataset — not the intent, and
  it made the logic "complexo pra caramba." Fixed by reverting to
  **keep every snapshot** ("traz tudo") plus a **control table** marking
  which job run is the last complete one, so only that gets exposed via
  a view — not a merged/modeled table.
- **Proposes the handoff mechanism** for Marina: GitHub repo, a DB copy,
  generous permissions to build on it, heavy alerting if something
  breaks. Frames maintenance as categorically easier than building it in
  the first place ("dar manutenção é mais fácil do que construir... é
  acrescentar uma coluna").
- **States the boundary rule directly**: *"a gente não cria banco de
  dados para outras áreas."* Cross-company ("cross") data belongs to the
  central data team; data specific to one area should be owned by that
  area's own embedded data person — cites the team's actual current
  model (a data person embedded in the business team, another in
  platforms) as already working this way, better with **Raira** than
  with **Bruno** (attributed to communication, not the model itself).

**(voice: Carol, inferred)** — priority and boundary pushback:

- **Restates, independently, that Farol shouldn't have been a priority
  project** — arrived from external pressure during the World Cup,
  never went through a prioritization queue, was already large before
  she noticed. Near-verbatim repeat of the opinion already recorded from
  a *different* meeting on 2026-08-24
  ([[2026-08-24 Agent Flow discovery with Carol]]) — the repetition
  across two independent conversations is what makes this attribution
  more than a guess.
- **Questions storing raw data in GCP at all** — her first instinct was
  "why not just an Airtable table Marina can manage herself" — abandoned
  once the volume math ruled it out (see Decisions).
- **Names a recurring structural problem**: every time an integration
  like this comes up, it lands on the data team by default, becoming
  their backlog and their production risk — cites a prior, similar
  conversation with **Bruno** (financeiro) about a lack of a dedicated
  database person on his team, for an unrelated Totvs-data case.
- **Pushes on what "raw" actually means for the handoff** — argues the
  data given to Marina should look as close as possible to what she'd
  get by hand-exporting a vendor report, since anything more processed
  either falls on the data team to maintain or (worse) sits with a
  consuming team that can't actually build it reliably.

## What this changes elsewhere

- **Extends [[Farol]]'s existing "Ownership is split by layer" section**
  with the concrete mechanism (repo + DB copy + alerting handed to
  Marina) and the reason Airtable was never a real option (volume, not
  preference).
- **Second, independent instance of Carolina's Farol-low-priority
  opinion** — now recorded from two separate conversations
  ([[2026-08-24 Agent Flow discovery with Carol]] and this one),
  strengthening it from "candid remark" toward "standing view."
- New, previously unrecorded names: **Marina** (finance, Farol's
  consumer), **Bruno** (financeiro, a separate embedded-data-person gap),
  **Raira** and **Letícia** (mentioned in passing, not enough here for
  their own pages yet).
