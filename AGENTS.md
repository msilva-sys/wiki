# Agent instructions → see `CLAUDE.md`

This vault is an **LLM-maintained wiki** about msilva's work at Livemode. The
schema that governs how you read, write, and reorganize it lives in a single
canonical file:

## → Read [`CLAUDE.md`](CLAUDE.md) before doing anything else.

It defines the three layers, the directory layout, page conventions, date
resolution, and the four operations (`ingest`, `query`, `lint`, `log`).

**This file is a pointer, not a copy.** It previously duplicated the schema, which
meant two files could drift apart — and the schema is the one file where drift is
expensive. Do not restore the duplicate. If a tool needs the rules under this
filename, keep this pointer and let it follow the link.

---

## The two rules that must never be missed

Repeated here deliberately, so that a tool reading only this file still fails
safe. Everything else is in `CLAUDE.md`.

1. **Never write a credential into the wiki.** No tokens, private keys, API keys,
   passwords, or connection strings — not even dev or sandbox ones, and not even
   when copying a file verbatim. Record the variable **name**, its purpose, and
   **where to obtain it**; replace the value with
   `<REDACTED — where to find it>`. This vault is a git repository: a secret
   written here is a secret in its history. If you find one already present,
   **stop and tell msilva before committing**.

2. **Never modify `raw/`.** It is the immutable source layer. Read it; never edit,
   move, rename, or delete anything there on your own initiative — only when
   msilva explicitly directs it.
