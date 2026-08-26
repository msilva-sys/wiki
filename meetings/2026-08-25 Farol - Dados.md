---
type: meeting
status: active
updated: 2026-08-26
date: 2026-08-25
attendees: [Matheus Silva, Luís Fernandez, Carolina Bezerra, Arthur Tavares]
source: "raw/Farol _ Dados - 2026_08_25 17_39 GMT-03_00 - Anotações do Gemini.md"
transcription_confidence: low
aliases: [farol dados meeting, farol architecture call]
tags: [farol, data, gcp, airtable, bigquery, governance]
---

# Farol | Dados — 2026-08-25, 17:39

> [!danger] Corrected 2026-08-25 by msilva — four attendees, not two
> Confirmed directly by msilva: **he, Luís, Carolina and Arthur Tavares**
> were all in this meeting. Gemini's transcript attributes **every single
> line to "Carolina Bezerra"** — the same failure mode as the 2026-08-10
> onboarding transcript, but worse: it's not two voices collapsed into
> one label, it's **up to four**. The two attributions below (Luís,
> Carol) are kept because each is backed by independent content evidence
> (see each item), not because "it must be one of two people" — that
> framing was wrong. **Any line not explicitly matched to a stance
> already on record could just as easily belong to Arthur or to
> msilva himself**, including some of what's currently filed under
> "(voice: Luís, inferred)" — Arthur does central-de-dados work
> ([[Arthur Tavares]]) and may well be the one supplying schema-level
> detail rather than Luís. Treat every unattributed technical claim below
> as **"someone on the data side," not confirmed as any one person**.

## Decisions

- **Airtable is ruled out as the store for this data — settled on volume,
  not preference.** Farol's raw data runs ~612–750k records/year; Airtable
  Enterprise caps around 500k records per base. *(speaker among Luís,
  Carolina, Arthur uncertain — see the correction banner above)*
- **Ingestion + dedup logic stays owned by the data team; a copy of the
  resulting database + the ingestion code goes to Marina (finance) for
  her own downstream use.** Concretely: Marina gets the GitHub repo, a
  copy of the DB (3–5 tables), and permission to build whatever she wants
  on top, with strong alerting so a break surfaces immediately. What she
  does with it — joining OnFly with Expresso with Uber, her own merges —
  is her problem, not the data team's. One voice proposes it, another
  pushes back — *"eu fico zero confortável com isso, mas tô entendendo
  teu direcionamento"* — then agrees. Which two of the four is genuinely
  unclear.
- **The bronze layer stays raw-ish, exposed via views, not further
  modeled.** A control table tracks which job run is "the last complete
  one" so only that snapshot is exposed — this replaces an earlier,
  unwanted attempt at merging/deduplicating records into a smaller,
  modeled dataset, walked back because it made the pipeline logic
  "complexo pra caramba."

## Commitments

- Luís (or whoever owns the "programa de parceiros" API integration) —
  talk to **Mariana** about how the partner-program REST API should be
  wired in. *(Possibly the same person as "Marina" below — see Open
  questions.)*
- Confirm with Marina/finance whether they're comfortable with the
  repo + DB-copy handoff model once it's actually built.

## Open questions

- **Is "Marina" (finance, will consume/maintain the Farol output) the
  same person as "Mariana" (mentioned once, re: the partner-program API)?**
  Transcription inconsistency, not confirmed either way.
- **What did Arthur actually contribute?** Confirmed present (see the
  correction banner above), but nothing below is confidently his — his
  central-de-dados expertise could easily be the source of some of the
  schema/architecture detail currently filed as "someone on the data
  side." Worth asking msilva directly if he recalls.
- **What, if anything, did msilva say?** Confirmed present; the one line
  referencing him in the third person (*"aí o Mateus tá por fora
  completamente"*) reads like someone else talking about him, consistent
  with him mostly listening — but not confirmed either way.
- **Which GCP database product?** Still not named — [[Farol]]'s existing
  open question ("BigQuery is the obvious guess... never named") stands;
  this meeting only rules out Airtable, doesn't name the replacement.
- Does **Bruno**'s team (financeiro, a separate case re: Totvs data) ever
  get its own database person, or does integration work keep defaulting
  to the data team regardless? Flagged as a repeat structural pattern,
  attribution below.

## Facts stated

**Attributed with real evidence** — each backed by a match to something
already on record from a different conversation, not just a stance guess:

- **Someone states the boundary rule directly**: *"a gente não cria banco
  de dados para outras áreas."* Matches [[Luís Fernandez]]'s already-
  recorded 2026-08-17 stance almost exactly ("a parte de dados pela
  gente, o resto por eles") — attributed to him with above-average
  confidence, though Arthur (also data-side) can't be fully ruled out.
- **Someone restates, independently, that Farol shouldn't have been a
  priority project** — arrived from external pressure during the World
  Cup, never went through a prioritization queue, was already large
  before anyone noticed. Near-verbatim repeat of the opinion already
  recorded from a *different* meeting on 2026-08-24
  ([[2026-08-24 Agent Flow discovery with Carol]]) — attributed to
  [[Carolina Bezerra]] on the strength of that independent repetition,
  not a stance guess.

**Everyone else — speaker among Luís, Carolina, and Arthur genuinely
uncertain**, filed as "someone on the data side" rather than assigned to
a name:

- **Four external data sources feed Farol** — confirmed and corrected
  against [[Farol pipeline whiteboard diagram]] (a photo, no attribution
  problem, trusted over this transcript): **OnFly via REST (API),
  Expresso via REST, Uber via SFTP, Itaú's method left an open
  question** on the board itself, not confidently "reverse SFTP" the way
  the transcript alone suggested. Expresso is mid-migration to API. A
  separate "programa de parceiros" integration uses a REST API, still
  being worked out with *Mariana* (see Open questions). The diagram also
  confirms an **unlabeled processing step** and an **unlabeled storage
  layer** are both genuinely open, not just unrecorded here.
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
- **Cites the team's current embedded-data-person model** (one in the
  business team, one in platforms) as already working this way, better
  with **Raira** than with **Bruno** (attributed to communication, not
  the model itself).
- **Questions storing raw data in GCP at all** — first instinct was "why
  not just an Airtable table Marina can manage herself" — abandoned once
  the volume math ruled it out (see Decisions).
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
- **[[Arthur Tavares]] confirmed present** (corrected 2026-08-25) — a data
  meeting matching his central-de-dados work, but nothing below is
  confidently his.
