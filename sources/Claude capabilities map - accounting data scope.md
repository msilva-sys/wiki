---
title: Claude capabilities map — accounting / large-data scope
date: 2026-08-14
type: reference
tags: [claude, capabilities, accounting, context-window, plugins, mcp]
related: "[[Meeting prep - accounting data in Claude - 2026-08-17]]"
---

# Claude capabilities usable in this scope

Companion to [[Meeting prep - accounting data in Claude - 2026-08-17]]. Organised by
**layer**, cheapest-to-adopt first. Each layer answers a different question.

> [!important] The headline finding
> Your org's plugin catalogue already contains a **`finance` plugin** whose skills are a
> near-exact match for this problem — `reconciliation`, `close-management`,
> `journal-entry`, `variance-analysis`, `financial-statements`, `audit-support`,
> `sox-testing` — bundled with **BigQuery / Snowflake / Databricks** MCP servers.
> It is available and **not currently enabled**. Tier 2 of the scoping ladder is
> largely pre-built. Install it and try it before Monday.

---

## Layer 1 — Get the data out of the conversation

*Question answered: "how do I stop filling the context window?"*

| Capability | What it gives you | Where | Limits worth knowing |
|---|---|---|---|
| **Code execution / analysis sandbox** | Claude writes and runs Python (pandas, openpyxl, matplotlib) against the file. Data is processed **through code, never loaded into context**. This is the core fix. | All plans, incl. Free. **Must be toggled on in settings** | 30MB per file in/out of the sandbox; no network or DB access from inside it; fresh environment each session |
| **File uploads** | Attach rather than paste | Chat: 500MB/file, 20 files/chat. Project files: 30MB/file | **XLSX upload requires code execution enabled** — a very common silent blocker |
| **Auto-compaction** | Long conversations get summarised automatically instead of erroring; the summarisation doesn't count against usage limits | Paid plans with code execution on | Lossy for numbers — never let a reconciliation cross a compaction boundary |
| **Projects + RAG** | Project knowledge switches to retrieval near the limit, holding ~10x more content | Paid plans | **Documents yes, ledgers no.** Retrieval sums the "relevant chunks" and gets a confidently wrong total |

## Layer 2 — Choose the right surface

*Question answered: "where should this person actually be working?"*

- **claude.ai chat + code execution** — best default for ad-hoc "analyse this export".
- **Projects** — durable instructions + reference docs (chart of accounts, close calendar,
  accounting policy). Not a home for big tables.
- **Claude for Excel** (add-in; Pro/Max/Team/Enterprise) — works on the *open workbook*,
  answers with **cell-level citations**, preserves formula relationships, traces
  `#REF!`/`#DIV/0!`, auto-compacts long sessions. Gaps: **no macros/VBA, no data tables**,
  and explicitly not recommended for audit-critical calcs without verification. Also:
  its chat history is local-only and **not in Enterprise audit logs / Compliance API**.
- **Cowork** (paid plans) — agentic, multi-step, direct local file access, long-running
  tasks that survive closing the laptop. The right answer for "consolidate these 12
  monthly exports into one report". Uses more of the usage allowance than chat.
- **Claude Code / Agent SDK / Managed Agents** — only if this becomes a real pipeline.

## Layer 3 — Procedural knowledge (Skills)

*Question answered: "how do I stop re-explaining the procedure every month?"*

**Skills** are folders of instructions + scripts Claude loads *on demand*. Progressive
disclosure means metadata costs ~100 tokens until the skill is actually needed, and full
instructions are <5k tokens — so they add capability **without** eating the window.
They work across claude.ai, Cowork, Claude Code, the API, and the Office add-ins.

The distinction to teach: **Projects = what you need to know. Skills = how to do things.**
MCP = connectivity. Subagents = delegation.

### Already in your catalogue (both currently disabled)

**`finance` plugin** — skills: `reconciliation`, `close-management`, `journal-entry`,
`journal-entry-prep`, `financial-statements`, `variance-analysis`, `audit-support`,
`sox-testing`. MCP servers bundled: BigQuery, Databricks, Snowflake, Gmail, Google
Calendar, Slack. Suggested prompts include *"Reconcile GL to subledger balances"* and
*"Analyze what's driving budget variances"* — i.e. exactly the tasks in question.

**`data` plugin** — `explore-data`, `sql-queries`, `write-query`, `validate-data`,
`statistical-analysis`, `build-dashboard`, `create-viz`, `data-context-extractor`.
The `data-context-extractor` skill is quietly the interesting one: it documents a
warehouse schema once so future queries carry full context without re-exploration.

Also present if ever relevant: `small-business` (QuickBooks-centred close/tax workflows),
`daloopa`, `bigdata-com`, `carta-*` (capital markets, not your case).

### Custom skills

If their close procedure is LiveMode-specific, write it as a skill once — the
`skill-creator` skill scaffolds it. That is the durable version of "teach them a workflow".

## Layer 4 — Connect to the source instead of exporting

*Question answered: "why are we passing spreadsheets around at all?"*

- **MCP connectors** — Claude queries the system directly, so volume stops mattering:
  only the *result set* enters the context. The finance/data plugins ship with
  **BigQuery, Snowflake, Databricks** servers.
- **Currently connected in your workspace:** Slack and Atlassian. Notion and Airtable are
  available but not enabled. **No accounting/ERP connector is set up** — that's the gap.
- **Custom connectors** — an org can build an MCP server for an internal system. This is
  the Tier 3 answer if the ledger lives somewhere with an API.
- Reality check: connecting to the ERP is a **data-access and security decision**, not a
  coding one. Find the owner before promising anything.

## Layer 5 — Scale and automation

*Question answered: "how does this run without a human babysitting it?"*

- **Scheduled tasks in Cowork** — "every 3rd business day, pull the export, run the close
  checklist, post the variance summary to Slack."
- **Subagents** — each gets its own fresh context; only conclusions come back to the main
  thread. The architectural answer to "12 months of data won't fit": one agent per month,
  summaries merged. Same trick for one agent per account, per entity, per cost centre.
- **Files API** (API) — upload once, reference by `file_id` forever; 500MB/file, 500GB per
  org, and the file operations themselves are free.
- **Batch API** (API) — **50% off**, up to 100k requests per batch, most finish within an
  hour. Ideal for "classify/summarise 200k transaction descriptions" overnight.
- **Prompt caching** (API) — reuse a stable prefix (chart of accounts, policy, schema)
  across many calls; stacks with Batch discounts.
- **Memory tool / external memory** (API) — the agent writes state to files and re-reads
  it, so the window is a desk rather than a filing cabinet.

## Layer 6 — Trust, verification, and output

*Question answered: "why should an accountant believe the number?"*

- **Show the code.** The Python *is* the audit trail — reviewable, re-runnable,
  deterministic. This is the single most persuasive thing you can demo to a finance person.
- **Cell-level citations** in Claude for Excel — every figure traces back to a source cell.
- **Artifacts / dashboards** — the `data:build-dashboard` skill and HTML artifacts turn a
  monthly analysis into something they open rather than re-request.
- **Deliverable generation** — real .xlsx with working formulas, .docx, .pptx, .pdf built
  directly from the analysis, so the last mile isn't manual re-typing.
- **Tie-out as habit** — does the sum match the trial balance; do 12 months equal the year.

---

## What Claude can't do here — say these out loud

- Reach their ERP, database, or network **from the analysis sandbox**. Connectors are a
  separate, deliberate integration.
- Macros / VBA / Excel data tables (Claude for Excel).
- Be the signer. Anthropic's own guidance: not for audit-critical calculations without
  human verification.
- Guarantee conversation-level auditability in the Excel add-in (history is local, absent
  from Enterprise audit logs and the Compliance API).
- Safely ingest untrusted external workbooks without care — spreadsheets are a documented
  **prompt-injection** vector.

---

## The stack I'd actually recommend Monday

**If it's an ad-hoc analysis problem** → code execution on + the attach/aggregate/
drill-down/verify workflow. Zero build, works the same afternoon.

**If they live in workbooks** → Claude for Excel, plus persistent instructions in the
add-in settings for their formatting and currency conventions.

**If it repeats monthly** → enable the **`finance` plugin** in Cowork, start from
`close-management` and `reconciliation`, then fork whichever skill is closest into a
LiveMode-specific version. Add a scheduled task once it's stable.

**If several people have this and the data is genuinely large** → `data-context-extractor`
against a warehouse + an MCP connector to the source, so nobody exports anything again.
This is a quarter-scale project with a security conversation attached, not a Monday promise.

## Sources

- [Create and edit files with Claude (code execution)](https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude)
- [Upload files to Claude — limits](https://support.claude.com/en/articles/8241126-upload-files-to-claude)
- [Context windows — Claude Platform Docs](https://platform.claude.com/docs/en/build-with-claude/context-windows)
- [RAG for projects](https://support.claude.com/en/articles/11473015-retrieval-augmented-generation-rag-for-projects)
- [Use Claude for Excel](https://claude.com/docs/office-agents/excel)
- [Get started with Claude Cowork](https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork)
- [Skills explained: Skills vs prompts, Projects, MCP, subagents](https://claude.com/blog/skills-explained)
- [Agents for financial services](https://www.anthropic.com/news/finance-agents)
- [Files API](https://platform.claude.com/docs/en/build-with-claude/files)
- [Batch processing](https://platform.claude.com/docs/en/build-with-claude/batch-processing)
