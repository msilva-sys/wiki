---
type: system
status: active
updated: 2026-08-24
aliases: [envs prxy, proxy envs]
tags: [airtable, proxy, config, environments, livescript]
---

# Proxy Environments

Environment and configuration reference for the [[Airtable Proxy]] and
[[LiveScript]].

> [!danger] History purge completed 2026-08-21 — rotation still outstanding
> This page contained **live credentials** in plaintext from 2026-08-11: an
> Airtable PAT, a Firebase service-account private key, a LogRocket API key, a
> Bugsnag server key, and an integration API key. They were replaced with
> placeholders in the working tree on 2026-08-17, but remained recoverable from
> the two root commits (`envs prxy.md`, then `systems/Proxy Environments.md`)
> until 2026-08-21.
>
> **Fixed 2026-08-21**, when the vault was first pushed to a remote
> (`git@github.com:msilva-sys/wiki.git`) and GitHub's push protection caught the
> Airtable PAT before the push completed. Used `git filter-branch` to redact all
> five values from both root commits, verified with the same grep pattern below
> against `$(git rev-list --all)` (zero matches after `git gc --prune=now`), then
> pushed the cleaned history. All 96 commits' messages and dates were preserved —
> only the secret lines were rewritten.
>
> ```bash
> git grep -IlE 'PRIVATE KEY-----|pat[a-zA-Z0-9]{12,}\.[a-f0-9]{20,}' $(git rev-list --all)
> ```
>
> **Still outstanding, and msilva's to perform:** rotate all five credentials
> (Airtable PAT, Firebase service-account key, LogRocket API key, Bugsnag server
> key, integration API key). The history purge only removes them from the repo —
> it does not undo the fact that they sat in plaintext on disk since 2026-08-11,
> so treat all five as potentially compromised regardless.
>
> Real values live in the app's local `.env` and in Secret Manager. **Never paste a
> credential into this wiki** — record the variable's *name*, *purpose*, and *where
> to obtain it* instead. That is the durable knowledge; the secret itself is
> worthless here and dangerous.

> [!warning] Correction, 2026-08-17
> This callout previously claimed *"the vault's git history was rebuilt so the
> values are not recoverable from it."* **That was false.** The rebuild was
> attempted, blocked by the permission layer, and never completed — [[log]]
> recorded the block correctly, but this page was never updated to match.
>
> A page asserting a security job is done, when it isn't, is worse than a page
> saying nothing. Found by the body-reading `lint` rule added 2026-08-17 after
> mechanical checks missed two intra-page contradictions.

`NEXT_PUBLIC_*` values are intentionally kept: they ship to the browser by
definition and are not secrets.

## Airtable

```bash
# Airtable Configuration
# Obtenha o PAT em: https://airtable.com/create/tokens
# Veja AIRTABLE_SETUP.md para instruções detalhadas
AIRTABLE_PERSONAL_ACCESS_TOKEN=<REDACTED-PURGED>
AIRTABLE_BASE_ID=appGGMCqfB2hMMMGz # Sandbox

# Table/field IDs (obrigatórios; IDs mudam ao sincronizar tabelas entre bases)
# Obtenha via Metadata API: curl "https://api.airtable.com/v0/meta/bases/{BASE_ID}/tables" -H "Authorization: Bearer $PAT"
AIRTABLE_ROTEIRO_GRUPOS_TABLE_ID=tblsEVo0oBwwHJxFi
AIRTABLE_ESPELHO_TABLE_ID=tbliRcyIlg0feNW3J
AIRTABLE_GRUPO_LINK_FIELD_ID=fldbLhKffYUxr9Say

# `RecordID` text field on Matriz CazéTV used by external integrations to
# resolve `/api/integrations/matriz/{idMatriz}/script/lines` to the internal
# Airtable record id.
# Production: fldi1Z7zCxWzhFgy5
# Sandbox:    fldi1Z7zCxWzhFgy5
AIRTABLE_MATRIZ_RECORD_ID_FIELD_ID=fldi1Z7zCxWzhFgy5

# `Roteiro Pronto?` boolean on Matriz CazéTV. Used by the in-app generation
# flow (PRD-029) to mark the event as generated. Different per base because
# dev and prod were created independently.
# Production: fldwTmMY3yWejqr1E
# Sandbox:    fldH74oC0ppcrojoI
AIRTABLE_MATRIZ_ROTEIRO_PRONTO_FIELD_ID=fldH74oC0ppcrojoI

# Reverse link from Matriz CazéTV → ROTEIRO_GRUPOS ("ROTEIRO_GRUPOS 2"
# column). Read in the same Matriz query as `hasScript` so the gerar-roteiro
# screen and the reset-roteiro pre-check (ADR-011, PRD-036) can see orphan
# groups without an extra query. Different per base.
# Production: fld4hmaeCDSBtZikC
# Sandbox:    fldPBl5NivGBJjyHW
AIRTABLE_MATRIZ_ROTEIRO_GRUPOS_LINK_FIELD_ID=fldPBl5NivGBJjyHW

# Lookup do RecordID do evento em ROTEIRO_GRUPOS. Permite filtrar grupos por
# evento via `filterByFormula` (PRD-031 — fluxo "Copiar de outro evento").
# Different per base because dev and prod were created independently.
# Production: fld3AQRope4V1k3fa
# Sandbox:    fldK1noswDAFJTHc1
AIRTABLE_ROTEIRO_GRUPOS_EVENT_ID_LOOKUP_FIELD_ID=fldK1noswDAFJTHc1

# Lookup `ID do Grupo (from Grupo Link)` on Espelho das Partidas (PRD-031
# unmigrated check). Different per base because dev and prod were created
# independently.
# Production: fldK7YZY47dti5LfC
# Sandbox:    fldVlVZxhi8Wm2u6R
AIRTABLE_ESPELHO_GRUPO_LINK_ID_LOOKUP_FIELD_ID=fldVlVZxhi8Wm2u6R

# Checkbox `OK do conteúdo` on Espelho das Partidas (PRD-032).
# Required because the field id can differ between Airtable bases.
# Production/Sandbox: set to the field id for the target base.
AIRTABLE_OK_DO_CONTEUDO_FIELD_ID=fldsd1R0Ss5izTa38

# Official start override on Matriz CazéTV (PRD-007). Different per base.
# Production: fldwe1asd0hX1C2RX
# Sandbox:    fldh6JGsthl9rXzJO
AIRTABLE_HORARIO_OFICIAL_INICIO_FIELD_ID=fldh6JGsthl9rXzJO

AIRTABLE_ESPELHO_COTA_FIELD_ID=fldWDr447imihH4I6

# View IDs (per-base; opcional — se não definir, usa o default de produção)
# Production view: viwFwSXcTcsHIKqkT (Matriz CazéTV)
# Desenvolvimento view:    viwZS2NRgE64FybIB
AIRTABLE_MATRIZES_VIEW_ID=viwZS2NRgE64FybIB
```

## Proxy wiring

```bash
# Point the app at the proxy instead of api.airtable.com.
# Commented out = app talks to Airtable directly (current default).
#AIRTABLE_ENDPOINT_URL=http://localhost:8080

OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
```

This is the migration mechanism described in
[[2026-08-10 Onboarding Técnico - Matheus]]: uncommenting one variable repoints
the app. Confirms the "base-URL swap, not DNS cutover" model in
[[Airtable Proxy]].

> [!warning] Uncommenting alone is not enough — superseded 2026-08-19 (header → path)
> Repointing at the proxy makes every call **401** until the app is identified
> correctly. The fix explored here originally (added 2026-08-17, GC-5) was a
> header (`X-App-Id: livescript`) via a `pnpm patch` on the SDK plus
> centralized REST-path injection (commits `754896b`, `d565c26`) — **shipped
> 2026-08-17, reverted 2026-08-18** (see
> [[2026-08-18 Bring options to Luís before deciding, communicate async and often]]).
> That whole header-based approach was retired 2026-08-19: the proxy now
> identifies the calling app by **URL path** instead — see
> [[2026-08-19 Identify proxy apps by URL path, not header]], implemented and
> hardened as of 2026-08-21. So this variable can be flipped once the app's
> `endpointUrl` carries its app-id path segment; no header or SDK patch is
> needed. The app still keeps a **dummy `AIRTABLE_API_KEY`** (the SDK refuses
> to start without one and always sends it; the proxy overwrites it). Full
> history: [[How LiveScript sends the proxy X-App-Id header]].

## Firebase

```bash
# Client-side. NEXT_PUBLIC_ variables are exposed to the browser by design.
# Obtenha em: https://console.firebase.google.com/
# Project Settings → Your apps → Add app → Web
NEXT_PUBLIC_FIREBASE_API_KEY=AIzaSyBUIs8UyjEjk2IBlEPZEXqVmeQcqG4u_qw
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=livemode-roteiros-dev.firebaseapp.com
NEXT_PUBLIC_FIREBASE_PROJECT_ID=livemode-roteiros-dev
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=livemode-roteiros-dev.firebasestorage.app
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=195810529635
NEXT_PUBLIC_FIREBASE_APP_ID=1:195810529635:web:8ad4494080a5694beb4356
NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID=G-1NC9K7FF0S

# Admin SDK — server-side only, never exposed to the client.
# Project Settings → Service Accounts → Generate New Private Key
FIREBASE_PROJECT_ID=livemode-roteiros-dev
FIREBASE_CLIENT_EMAIL=firebase-adminsdk-fbsvc@livemode-roteiros-dev.iam.gserviceaccount.com
FIREBASE_PRIVATE_KEY=<REDACTED-PURGED>
```

## Observability (app-side)

Distinct from the proxy's OTel pipeline — this is LiveScript's own error and
session tooling.

```bash
# LogRocket (client-side, optional). Default app ID: mbailj/livemode-roteiros
NEXT_PUBLIC_LOGROCKET_APP_ID=livemode-livescript/livescript
NEXT_PUBLIC_LOGROCKET_DISABLED=1      # 1 = no session recording, no errors
NEXT_PUBLIC_LOGROCKET_MINIMAL=0       # 1 = drop network/console from recording

# Server-side/CI, for uploading source maps after `pnpm build`
LOGROCKET_API_KEY=<REDACTED-PURGED>

# Bugsnag — client-side (errors + performance)
NEXT_PUBLIC_BUGSNAG_API_KEY=27bfa09a57d60ca8d3f5fbdf7514f825
NEXT_PUBLIC_BUGSNAG_ENABLED_IN_DEV=1
NEXT_PUBLIC_BUGSNAG_SEND_FROM_DEV=1

# Bugsnag — server-side (API routes + Airtable monitoring).
# Prefer a dedicated Bugsnag project for backend events.
# If unset, falls back to BUGSNAG_API_KEY or NEXT_PUBLIC_BUGSNAG_API_KEY.
# By default backend events are sent only from production.
BUGSNAG_SERVER_API_KEY=<REDACTED-PURGED>
BUGSNAG_SERVER_SEND_FROM_DEV=1
BUGSNAG_SERVER_ENABLED_IN_DEV=1
```

## App behaviour flags

```bash
NEXT_PUBLIC_BASE_URL=http://localhost:3000

# Seconds of inactivity before an edit lock is released. Default 180 (3 min).
# Use 30–60 locally for easier development.
# NEXT_PUBLIC_EDIT_LOCK_TIMEOUT_SECONDS=180

NEXT_PUBLIC_FEATURE_GENERATE_SCRIPT_IN_APP=true

# Comma-separated emails allowed to paginate the full change-history log
# (infinite scroll in Histórico de Alterações). UX-only gate for internal admins.
CHANGE_HISTORY_ADMIN_EMAILS=lfernandez.projetos@livemode.com,tech@livemode.com

# Integrations API (PRD-032 / LIVESCRIPT-9) — static key for /api/integrations/*
# Local-only dev value (generated with `openssl rand -hex 32`).
# Do NOT reuse in Vercel.
INTEGRATION_API_KEY=<REDACTED-PURGED>
```

`NEXT_PUBLIC_EDIT_LOCK_TIMEOUT_SECONDS` is the implementation of the row-level
*trava* described in [[LiveScript]] — the lock auto-expires after 3 minutes of
inactivity.

## Knowledge gap — the PRD/ADR corpus

The comments above cite **PRD-007, PRD-029, PRD-031, PRD-032, PRD-036, ADR-011**
and **LIVESCRIPT-9**. None of these documents are in the wiki and none are in
`raw/`. This looks like the single richest unexploited source of context on
[[LiveScript]]. Worth locating and ingesting.
