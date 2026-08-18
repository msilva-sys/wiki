---
type: reference
status: active
updated: 2026-08-18
aliases: [claude code habits, working with claude code, tooling notes]
tags: [claude-code, tooling, workflow]
---

# Claude Code Working Habits

Practical notes on working with Claude Code day to day, from
[[2026-08-18 1-1 Matheus - Luís]]. Personal/workflow reference, not Livemode
domain knowledge — same shelf as [[Zed Cheatsheet]].

## Pin unwanted behavior into the project the first time you see it

msilva found Claude Code noticeably more verbose than the GPT-based tooling
(Cursor) he was used to — PR descriptions in particular came out overly long.

**Luís's fix: treat an unwanted response like messy code you happen to pass
through — correct it in place, immediately, in the project's persistent
instructions.** Demonstrated live: after Claude generated an HTML artifact he
hadn't asked for, he added an instruction to never generate an artifact directly
and always ask first. *"Se você não [...] orientá-lo a fazer dessa forma, ele vai
continuar fazendo"* — a fresh session inherits the old default until it's written
down somewhere the project reads every time.

This is the same lesson already on [[Agent Flow]] from
[[Gabriel Packer - solo founder AI workflow (part 1)]] — *"write agent
instructions to files, not memory"* — applied here to a human's own daily use of
Claude Code, not to a designed multi-agent system. A third instance of the same
principle in this vault, after A6 Curator's design and this vault's own
`CLAUDE.md`.

**Caution, from the same conversation**: instructions accumulate and can go
stale. Luís has a separate project (with Yasmin) where an instruction added
earlier — asking Claude to be more didactic for her benefit — is now actively
working against him after it stopped being needed, and he has to unwind it from
the harness. Pinning behavior is not "set once, forget" — revisit instructions
when the need that motivated them changes.

## Claude Code defaults toward "vibe coding" more than Cursor did

Luís's characterization: *"ele te direciona menos para você amarrar as coisas
[...] você tem que dar esse passo proativamente."* Read as: the tool won't push
you toward tight specs and guardrails on its own — that's a habit the user has to
bring, more than with Cursor/GPT-based tooling. His own overall experience has
been positive (*"pra mim tem sido perfeito"*) once that's accounted for.

## Use the IDE integration, not the browser alone

Recommendation to msilva, who was working with a browser tab plus Zed open
alongside it, and running into OS-level friction trying to work purely from a
terminal: **install VS Code and the Claude Code extension.** Luís's claim: it
gives materially more power than driving Claude Code from a browser tab.

## Cross-model review

msilva's practice, independent of the verbosity issue: plan/implement with one
model, review with another. Currently: PR reviews done with GPT rather than
Claude. He tried to install OpenAI's Codex CLI to get a stronger reasoning model
for planning — IT could not get the GUI variant working, so he installed it via
the terminal instead. Mentioned wanting to try a setup he'd seen elsewhere —
planning with Fable/Opus-class models, implementing with a top GPT model — but
noted OpenAI's own CLI doesn't expose that particular model. Low-confidence
transcription on the exact model name; not verified.
