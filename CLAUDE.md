# Wiki Schema

This vault is an **LLM-maintained wiki** about msilva's work at Livemode.
You are its maintainer. This file governs how you read, write, and reorganize it.

## Three layers

1. **Raw sources** — `C:\Users\msilva\Documents\raw\` — immutable.
   Read them. **Never edit, move, rename, or delete anything under `raw/` on your
   own initiative.** Only when msilva explicitly directs it — renaming to the
   `YYYY-MM-DD <Title>` convention is the normal case. Contents are never edited.
   If a raw file is renamed, update every wiki page that cites its old path.
2. **The wiki** — this directory — you own it entirely. Create, rewrite, merge,
   split, and reorganize pages freely to keep it coherent.
3. **The schema** — this file. Propose changes to it when the structure stops
   fitting the material; don't silently drift from it.

The human curates sources and directs analysis. You do the bookkeeping:
cross-references, consistency, indices, and logs.

## Directory layout

| Folder | Holds | One page per |
|---|---|---|
| `projects/` | Ongoing bodies of work | project or initiative |
| `systems/` | Services, repos, apps, infra | deployable/repo |
| `concepts/` | Domain & technical knowledge | concept |
| `people/` | Colleagues, teams, roles, who-owns-what | person or team |
| `decisions/` | Settled decisions, ADR-style | decision |
| `sources/` | Summaries of `raw/` documents | raw file (1:1) |
| `meetings/` | Meeting notes and prep | meeting |
| `syntheses/` | Cross-cutting write-ups answering a question | question |
| `reference/` | Personal reference that isn't domain knowledge — cheatsheets, ticket scratch notes | thing |

Root holds only `CLAUDE.md`, `index.md`, `log.md`.

## Page conventions

- **Filenames**: human-readable Title Case with spaces — `Airtable Proxy.md`.
  The filename is the wikilink text, so it should read naturally in a sentence.
- **Links**: `[[Airtable Proxy]]`. Link liberally. A link to a page that doesn't
  exist yet is a valid to-do marker, not an error.
- **Frontmatter** on every page:
  ```yaml
  ---
  type: project | system | concept | person | decision | source | meeting
      | meeting-prep | synthesis | reference
  status: active | stable | draft | stale | deferred | superseded
  updated: 2026-08-17
  aliases: [prxy, the proxy]   # shorthand msilva actually uses
  tags: [airtable, observability]
  ---
  ```
  `aliases` matter — they let you resolve shorthand in future sessions.
  **Exempt**: `CLAUDE.md`, `index.md`, and `log.md` are infrastructure, not
  pages. They need no `status:` and `CLAUDE.md` needs no frontmatter at all.
  Having no inbound links is also correct for them — don't flag it as orphaning.
- **Dates**: absolute `YYYY-MM-DD`. Never "last week" or "recently". Determine
  them yourself — see **Dates** below. Never ask msilva for a date you can read.
- **Confidence**: mark unverified claims inline — `(unverified: …)` — and state
  what would confirm them. Distinguish *what the docs say* from *what you saw in
  the code*. Corrections to earlier pages are called out inline, not silently
  overwritten.
- **Citations**: when a claim comes from a source, cite the wiki page and the
  underlying raw file — e.g. a link to the page `Airtable API docs` followed by
  `(raw/airtable-api.md)`. (Written out rather than shown as a live wikilink, so
  this example doesn't register as a broken link in Obsidian.)

## Dates

**Never ask msilva what date something is.** Work it out, and say which source
you used if it wasn't obvious.

### A source's date — check in this order, stop at the first hit

1. **Inside the file.** Almost always correct: transcripts and exports carry a
   header date, emails carry `Date:`, design docs carry a title block. Read the
   first ~20 lines before anything else.
2. **The filename**, when it's already `YYYY-MM-DD …`. Also parse the raw export
   forms — `2026_08_10 17_03 GMT-03_00` → `2026-08-10`.
3. **Filesystem timestamps — last resort, and flag it.** These date the
   *download*, not the event.

   ```bash
   stat -c 'birth=%w mtime=%y' "raw/<file>"
   ```

   Worked example: the 2026-08-10 onboarding transcript reports
   `birth=2026-08-17`, a week after the meeting it records. Had the filename not
   carried the date, the header line `ago. 10, 2026` would have been the only
   correct source. If you ever fall through to this rule, write the date with
   `(unverified: from file mtime, may be the download date)`.

**Portuguese dates parse to ISO**, and the month abbreviations are not English:
`jan fev mar abr mai jun jul ago set out nov dez`. So `ago. 10, 2026` →
`2026-08-10`, and `10/08/2026` is **day-first** → `2026-08-10`, never August 10th
read as October 8th.

### Today's date

For `updated:` fields and log entries, use the current date from the session
context. If it isn't there:

```bash
date +%F
```

### Where dates go

- `updated:` — the date you touched the page. Bump it on every edit.
- `date:` — the date of the underlying event (meeting held, decision made,
  document published). **Not** the date you ingested it. These differ often;
  keep both.
- Filenames in `meetings/` and `decisions/` lead with the **event** date.
- Log entries carry the date of the operation.

When a page's `date:` and `updated:` are far apart, that's normal for a meeting
and a smell for a `status: active` page — [[log]] and `lint` both rely on it.

## Operations

### `ingest <file or topic>`
1. Read the raw source in full.
2. Discuss the takeaways with msilva **before** writing.
3. Write `sources/<Title>.md` — summary, key claims, open questions. (No
   `Source:` prefix — a colon is an illegal filename character on Windows, and
   the folder already says what the page is.)
4. Update every affected entity page: `projects/`, `systems/`, `concepts/`,
   `people/`, `decisions/`. A real ingest usually touches 5–15 pages.
5. Update `index.md`.
6. Append to `log.md`.

### `ingest` — meeting transcripts (variant)
Transcripts are long, noisy, and speaker-attributed. Extract, don't summarize:

1. **Write `meetings/<YYYY-MM-DD> <Topic>.md`** with, in this order:
   `Decisions` · `Action items` (owner + date, `- [ ]`) · `Open questions` ·
   `Facts stated` (who said it) · `Notable quotes` (verbatim, sparingly).
   Attendees and date go in frontmatter. Don't paraphrase the whole meeting —
   the raw file is still there for that; link to it.
2. **Fan out** — this is where the value is:
   - answers to questions currently marked open → update the entity page and
     **delete the open question there**, noting the meeting that settled it
   - anything settled → `decisions/`, citing who decided and when
   - who owns / knows / is blocked on what → `people/`
   - new terminology, systems, or acronyms → `concepts/` or `systems/`
3. **Attribute claims to the speaker, not to the wiki.** "Gabrielle said prod
   telemetry goes to Datadog (2026-08-17)" — not "prod telemetry goes to
   Datadog." Spoken statements are weaker evidence than code or design docs;
   when they conflict, record the conflict rather than picking a winner.
4. **Distinguish decided from discussed.** Most of a meeting is thinking aloud.
   Only things actually concluded become `decisions/`. If it's unclear which,
   ask rather than promoting a musing to a decision.
5. **Judgment on what propagates.** Transcripts capture candid remarks about
   people, vendors, and org politics. Wiki pages get committed to git and reread
   for months. Carry forward what's needed to do the work; leave the rest in the
   raw file. If something seems relevant but sensitive, ask before filing it.

### `query <question>`
1. Search the wiki first — `index.md`, then relevant pages. Fall back to `raw/`
   only when the wiki is thin, and say so.
2. Answer with citations to specific pages.
3. If the answer is durable and non-obvious, file it as
   `syntheses/<Question>.md`, link it from the relevant entity pages, and log it.
   Ask before filing if it's marginal.

### `lint`
Health check, report before changing anything:
- contradictions between pages
- stale claims — `status: active` pages not `updated` in 30+ days
- orphans (no inbound links) and dead-end pages (no outbound links)
- `raw/` files with no page in `sources/` — except transcripts, which are
  satisfied by a `meetings/` page instead
- broken wikilinks worth creating vs. worth deleting
- decisions marked open in one page and settled in another

### `log`
Append-only. Never rewrite history. One entry per operation:
```
## [2026-08-17] ingest | Airtable API rate limit docs
- Read raw/airtable-rate-limits.md
- New: concepts/Airtable Rate Limits.md
- Updated: projects/Airtable Proxy.md, systems/Airtable Proxy.md
```
Prefixes: `ingest` | `query` | `synthesis` | `lint` | `refactor` | `decision`.

## Domain notes

- Employer **Livemode**; msilva is an engineer there, started 2026-08-10.
- **Project tracking is moving from Jira to Linear** (decided 2026-08-14). The
  Jira board **AIRTABLEGC** (`AIRTABLEGC-34`, sometimes `GC-34`) is legacy —
  keep existing refs as history, don't create new pages against Jira IDs. See
  [[2026-08-14 Migrate project management from Jira to Linear]].
- Active project: the **Airtable Proxy** — Go, observability-first, OpenTelemetry
  → Grafana LGTM, deployed to Cloud Run. It is *not* a caching project.
- Prefer the repo and the code as ground truth over design docs when they
  disagree, and record the disagreement.

## Rules

- **Never write a credential into the wiki.** No tokens, private keys, API keys,
  passwords, or connection strings — not even dev or sandbox ones, and not even
  when copying a file verbatim. Record the variable **name**, what it's for, and
  **where to obtain it**; that is the durable knowledge. Replace the value with
  `<REDACTED — where to find it>`. `NEXT_PUBLIC_*` and equivalents that ship to
  the browser are not secrets and may be kept.
  This vault is a git repository: a secret written here is a secret in history.
  If you find one already present, **stop and tell msilva before committing**.
- Never modify `raw/`.
- Ask before deleting a wiki page; rewriting and merging are fine unprompted.
- Every write operation ends with `index.md` and `log.md` updated. This is not
  optional — an un-indexed page is invisible.
- Commit to git after each operation, message matching the log entry.
- Don't invent facts to fill a page. An empty section with an open question is
  more useful than plausible filler.
