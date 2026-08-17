---
title: Meeting prep - accounting data in Claude (context window problem)
date: 2026-08-14
meeting: 2026-08-17
type: meeting-prep
tags: [claude, context-window, accounting, data-engineering, meeting-prep]
status: draft
---

# Meeting prep — "a year of accounting data into Claude"

> [!info] Assumption to confirm in the first two minutes
> Reading "accountability" as **accounting / finance (contabilidade)**. The tells are
> "all the data from the year" + "a spreadsheet". If they actually mean *accountability*
> in the ownership/follow-through sense, most of this note still applies — the technical
> half is about any large tabular dataset. Confirm before you launch into the technical part.

**Goal:** walk out with (a) a workflow they can use Tuesday morning, and (b) enough
understanding of the real data problem to decide whether LiveMode Tech should build
something.

---

## 1. The one idea to internalize before Monday

Their instinct is *"Claude needs to see all my data."* That instinct is the bug.

The context window is **working memory, not storage**. The fix is almost never a bigger
window — it is moving the data *behind a tool* instead of *inside the conversation*:

| Wrong shape | Right shape |
|---|---|
| Paste / attach 80k rows, ask for the annual variance | Attach the file, let Claude write Python that computes the variance, read back a 40-row result |
| One mega-conversation holding the whole year | Data lives in a file / DB; the conversation holds only the question and the answer |
| "Claude forgot the numbers from earlier" | Numbers were never supposed to live in the transcript |

Say it in one line in the meeting: **"We're going to stop feeding Claude the data and
start letting Claude query it."**

Second, quieter point — even when the data *does* fit, it shouldn't. Arithmetic over
thousands of rows read as prose is unreliable in a way that matters enormously in
accounting. Deterministic computation (code) is not just a context trick, it is the
only defensible way to produce a number someone will sign.

### Token math to have ready

Order of magnitude — say it as an estimate, not a measurement:

- One transaction row (date, account, cost centre, description, currency, value, doc ref)
  ≈ **25–60 tokens** as CSV, depending on how wide and how text-heavy it is. Numbers and
  dates tokenize inefficiently, so tables cost more per character than prose.
- A 1M-token window therefore holds very roughly **15k–40k rows** — *and nothing else*:
  no instructions, no tool definitions, no answer.
- A real year of ledger detail is plausibly 100k–1M+ rows → **millions to tens of
  millions of tokens**. That's not a "tune the prompt" gap, it's 5–50x over the limit.
- If you want the exact number for *their* file: `df.to_csv()` its length and divide
  characters by ~3. Better still, compute it live in the meeting — it lands harder.

That reframing usually ends the "can't you use the big model?" conversation in 30 seconds.

---

## 2. What to study — ranked by payoff for this specific meeting

### A. Context window mechanics (30 min) — *non-negotiable*

Know the numbers cold, because they will ask:

- **Opus 5 / Sonnet 5: 1M tokens** on all paid plans. Opus 4.6/4.7/4.8 and Sonnet 4.6:
  **500K**. Earlier models: **200K**.
- On the API, 1M is now the **default** for the latest models — no beta header, standard
  pricing.
- **Everything** counts toward the window: system prompt, project instructions, tool
  *definitions* (not just results), attached files, tool results, extended thinking
  tokens, and Claude's own output. This is why "I only pasted one spreadsheet" still blows up.
- **Auto-compaction**: on paid plans with code execution enabled, Claude summarises
  earlier turns as the conversation approaches the limit, and that summarisation does
  **not** count against usage limits. Useful to know — but for numeric work, compaction
  is a *hazard*: summarised numbers are lossy. Never let a reconciliation ride through
  a compaction boundary.
- Overflow behaviour: over-limit input = hard error; on 4.5+ models a request that would
  overflow mid-generation stops with `model_context_window_exceeded`.

### B. Code execution / the analysis sandbox (45 min) — *the actual fix*

This is the single highest-value thing to be able to demo.

- Available on **all tiers** including Free. Must be **enabled in settings** — and note
  **XLSX upload requires it to be on**. There is a real chance their entire problem is
  a toggle. Check this first.
- Claude writes and runs Python/JS in a sandbox: pandas, openpyxl, charting. Files are
  processed **through code, not loaded into context**.
- Practise the phrasing that forces this path: *"Don't read the file into the
  conversation. Load it with pandas, print `df.info()` and the first 5 rows, then wait
  for my next instruction."*
- Know the limits so you don't over-promise: **30MB per file** in and out of the
  sandbox. Chat attachments are documented at 500MB/file and 20 files/chat, project
  files at 30MB/file — worth verifying live, the practical ceiling for the sandbox is
  the one that bites.
- Study what *breaks*: sandbox has no access to their network or DB; each session's
  environment is fresh; a 400MB CSV needs chunked reads or a pre-filter.

### C. Pre-aggregation and grain (45 min) — *the skill they're actually missing*

Ninety percent of "context window problems" in finance are **grain problems**. Nobody
needs 800k journal lines to answer "why did marketing spend jump in May".

Learn to teach this ladder:

1. **What question are we answering?** → determines the grain.
2. **Aggregate to that grain first** (account × cost centre × month is usually enough —
   often a few thousand rows, comfortably in-window).
3. **Drill down only where the aggregate flags something.** Two-pass: summary finds the
   anomaly, detail query explains it.
4. Keep raw detail in a file/DB the whole time; never in the transcript.

Also worth an hour: **tidy data** principles (one observation per row, no merged header
cells, no totals rows embedded in data, no colour-as-meaning). Accounting spreadsheets
violate all of these, and each violation costs tokens *and* accuracy. "Your spreadsheet
is formatted for humans; we need one formatted for machines" is a useful, non-insulting
framing.

### D. Projects + RAG — and its trap for numbers (20 min)

- Project knowledge switches to **retrieval (RAG)** automatically as it approaches the
  window limit, letting a project hold roughly **10x more** content.
- **The trap, and you should be the one to raise it:** RAG retrieves *relevant passages*.
  For prose that's great. For a ledger it is dangerous — retrieving "the relevant chunks"
  of a transaction table and summing them gives a confident, wrong total. Anything
  that must **tie out** should go through code execution, not retrieval.
- Rule of thumb to offer: **RAG for documents (policies, contracts, memos), code
  execution for numbers.**

### E. Which surface should they even be using (30 min)

They may be fighting chat when they should be somewhere else entirely. Be able to
compare four options in a minute each:

- **claude.ai chat + code execution** — best default for ad-hoc analysis of a file.
- **Projects** — good for durable instructions + reference docs; not for big tables.
- **Claude for Excel** (add-in, Pro/Max/Team/Enterprise) — this may be the answer if
  they live in workbooks. Works on the *open workbook*, gives **cell-level citations**,
  preserves formula relationships, traces `#REF!`/`#DIV/0`, auto-compacts long sessions.
  Know the gaps: **no macros/VBA, no data tables**, and Anthropic explicitly does not
  recommend it for **audit-critical calculations without verification**.
- **Cowork** (paid plans) — agentic, multi-step, direct local file access, runs long
  tasks; consumes more usage than chat. Right answer for "process these 40 monthly
  files and produce a consolidated report".

### F. Context engineering principles (45 min) — *for the scoping half*

Read Anthropic's *Effective context engineering for AI agents* and be able to name four
patterns:

1. **Just-in-time retrieval** — load data when needed, not upfront.
2. **Compaction** — deliberate summarisation at safe boundaries.
3. **Sub-agents** — a fresh context per sub-task, only conclusions returned to the main thread.
4. **External memory** — the agent writes state to files and re-reads it; the filesystem
   is the memory, the window is the desk.

These four are the vocabulary you'll use to describe whatever you propose to build.

### G. Their domain (60 min) — *the highest-leverage non-technical study*

You cannot scope this well without knowing what they're actually doing at year-grain.
Learn enough to ask sharp questions about:

- **Month-end close** — the calendar, what's manual, where the bottleneck is.
- **Reconciliation** — bank, intercompany, AR/AP ageing. This is the classic "needs the
  whole year" task, and it's a *join*, not a chat.
- **Trial balance / balancete → DRE and balance sheet** — how the year rolls up.
- **Variance analysis** — budget vs actual, YoY, month-over-month by cost centre.
- **BR-specific:** SPED (ECD/ECF), NF-e, obligations calendar — and which of these is
  driving the "I need the whole year" urgency. Compliance deadlines change the
  conversation from "nice analysis" to "must tie out exactly".
- **Where the data really lives.** The spreadsheet is almost certainly an *export* from
  an ERP or accounting system. Find the upstream system — that reframes the whole
  problem from "handle a big file" to "query the source".

### H. Verification and auditability (30 min) — *what makes this credible*

For accounting, "probably right" is worthless. Be ready with:

- Deterministic computation (code) over model arithmetic, always.
- **Show the code.** The Python is the audit trail; it's reviewable and re-runnable.
- Tie-out checks as a habit: does the sum match the trial balance total? Do the twelve
  months add to the annual figure?
- Cell-level citations (Claude for Excel) so every number traces to a source cell.
- Human review before anything leaves the building.

### I. Data handling and risk (20 min) — *the thing that kills the project if ignored*

- Financial data classification: what is this person allowed to upload at all? Check
  LiveMode's policy before you encourage a pattern that violates it.
- **Prompt injection via spreadsheets** — Anthropic explicitly warns that external
  workbooks (vendor files, downloaded templates) can carry hidden instructions. Relevant
  the moment third-party files enter the flow.
- Retention: Claude for Excel inputs/outputs deleted within 30 days; its chat history is
  local (IndexedDB), not synced, and **not in Enterprise audit logs or the Compliance
  API**. If auditability of the *conversation* matters, that's a real constraint.

---

## 3. Diagnostic script — run the meeting from these

Do not open with solutions. Twenty minutes of these questions changes what you'd build.

**The data**

1. Show me the spreadsheet. How many rows, how many tabs, what's one row?
2. Where did it come from — which system exported it? Can we reach that system directly?
3. What's the grain: one row per transaction, per document, per day, per account?
4. Is it clean, or is it human-formatted — merged cells, subtotal rows, colour coding?
5. How much of the year do you *actually* need for a typical question?

**The task**

6. Walk me through the last time this worked. What did you get out of it?
7. What's the actual question you're asking Claude? (Not "analyse the year" — the real one.)
8. Is this a one-off or does it repeat monthly?
9. Does the output need to **tie out exactly**, or is directional enough?
10. Who consumes the answer — you, your manager, an auditor, a regulator?

**The current setup**

11. Which Claude are you using — chat, Projects, the Excel add-in, Cowork? Which plan?
12. **Is code execution / file creation enabled in your settings?** ← check live
13. Which model? (200K vs 500K vs 1M changes the arithmetic)
14. What exactly does the failure look like — an error, or answers that go vague/wrong?
15. Are you starting fresh conversations or one long thread?

**The constraints**

16. Any restriction on what data can go into an AI tool?
17. How much time does this cost you per month today?

> [!tip] Q14 is the diagnostic fork
> A hard error = a size problem, solved by code execution or aggregation.
> **Degrading quality** = context rot / compaction loss, solved by restructuring the
> workflow. These have different fixes and people conflate them constantly.

---

## 4. Teach-now: the 20-minute live demo

Have this ready to do *with* their real file, screen shared.

1. Confirm code execution is on. (Possibly the whole fix.)
2. Fresh conversation. Attach the file — don't paste it.
3. First prompt, verbatim-ish:
   > "Use code execution. Don't print the raw data into our conversation. Load this file
   > with pandas and show me only: shape, column names and dtypes, date range, and 5 sample rows."
4. Second prompt — aggregate before analysing:
   > "Group by account and month, sum the values, and give me a pivot of the 20 largest
   > accounts by absolute total. Show the code."
5. Third prompt — the two-pass drill-down:
   > "Flag any account-month more than 2 standard deviations from that account's monthly
   > mean. For the top 3 flags, pull only those transactions."
6. Tie-out: *"Does your grand total match the trial balance?"* — make verification feel
   routine, not paranoid.
7. Hand them the takeaway rule, written down: **attach, aggregate, drill down, verify.**

The point of the demo is not the analysis. It's that they *see* the raw data never
entering the conversation and the window never filling up.

---

## 5. Scoping ladder — pick a rung, don't over-build

| Tier | Build | When it's right | Effort |
|---|---|---|---|
| **0. Config + habit** | Enable code execution, teach the four-step workflow, one page of prompts | One-off or low-frequency analysis; the file is a manageable export | ~1 hour |
| **1. Right surface** | Move them to Claude for Excel and/or Cowork; set persistent instructions | They live in workbooks, or the job is "many files → one report" | Days |
| **2. Reusable recipe** | A **Skill** encoding their close/reconciliation procedure + a Cowork task; a clean pre-aggregated extract as the standard input | The task repeats monthly with the same shape | 1–2 weeks |
| **3. Query the source** | Read-only connector/MCP to the ERP or a warehouse; Claude queries instead of receiving files | Multiple people have this problem; data volume is genuinely large; it recurs | Weeks+, needs data & security buy-in |

Bias toward **Tier 0 or 1 on Monday**, and only name Tier 2/3 as a follow-up with
conditions attached. Note that no accounting/ERP connector is currently set up on your
side — Tier 3 starts with a data-access conversation, not a coding one.

Trigger for going up a tier: **frequency × people × volume**. One person, once a year →
Tier 0. Five people, every month, on a million rows → Tier 3.

---

## 6. Traps — don't say these

- ❌ "Just use the 1M model." Buys 5x on a 50x problem, and quality degrades before the
  limit does.
- ❌ "Claude can do your reconciliation." Claude can *compute* it; a human signs it.
  Anthropic's own guidance says don't use this for audit-critical work without verification.
- ❌ Promising anything that needs ERP access before you know who owns that decision.
- ❌ Letting them believe a summarised/compacted number is a real number.
- ❌ Dismissing the spreadsheet as "badly built". It works for them. Reframe as
  human-format vs machine-format.

---

## 7. Sunday checklist

- [ ] Confirm "accountability" = accounting (message them: *"can you send the spreadsheet
      ahead of Monday, even a redacted sample?"* — a sample beforehand is worth 20 minutes
      in the meeting)
- [ ] Study block A + B + C (~2h) — the must-haves
- [ ] Skim D–I (~1.5h)
- [ ] Build a fake 200k-row ledger CSV and run the §4 demo end to end, so you've hit the
      failure modes before they do
- [ ] Check your own settings: code execution on, which model, which plan
- [ ] Find out whether LiveMode has a policy on financial data in AI tools
- [ ] Decide your default recommendation *before* the meeting, then let the answers change it

---

## Related

- [[Claude - context window and large data]]
- [[LiveMode - AI tooling and data access]]

## Sources

- [How large is the context window on paid Claude plans?](https://support.claude.com/en/articles/8606394-how-large-is-the-context-window-on-paid-claude-plans)
- [Context windows — Claude Platform Docs](https://platform.claude.com/docs/en/build-with-claude/context-windows)
- [Create and edit files with Claude (code execution)](https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude)
- [Upload files to Claude — limits](https://support.claude.com/en/articles/8241126-upload-files-to-claude)
- [Retrieval augmented generation (RAG) for projects](https://support.claude.com/en/articles/11473015-retrieval-augmented-generation-rag-for-projects)
- [Use Claude for Excel](https://claude.com/docs/office-agents/excel)
- [Get started with Claude Cowork](https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork)
- [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
