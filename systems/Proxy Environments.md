---
type: system
status: active
updated: 2026-08-11
aliases: [envs prxy, proxy envs]
tags: [airtable, proxy, config, environments]
---

# Proxy Environments

Environment and configuration reference for the [[Airtable Proxy]].

```
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

# View IDs (per-base; opcional — se não definir, usa o default de produção)
# Production view: viwFwSXcTcsHIKqkT (Matriz CazéTV)
# Desenvolvimento view:    viwZS2NRgE64FybIB
AIRTABLE_MATRIZES_VIEW_ID=viwZS2NRgE64FybIB

# Firebase Configuration (Client-Side)
# Variáveis com prefixo NEXT_PUBLIC_ são expostas ao cliente (browser)
# Obtenha essas credenciais em: https://console.firebase.google.com/
# Project Settings → Your apps → Add app → Web
NEXT_PUBLIC_FIREBASE_API_KEY=AIzaSyBUIs8UyjEjk2IBlEPZEXqVmeQcqG4u_qw
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=livemode-roteiros-dev.firebaseapp.com
NEXT_PUBLIC_FIREBASE_PROJECT_ID=livemode-roteiros-dev
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=livemode-roteiros-dev.firebasestorage.app
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=195810529635
NEXT_PUBLIC_FIREBASE_APP_ID=1:195810529635:web:8ad4494080a5694beb4356
NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID=G-1NC9K7FF0S

# Base URL (Client-Side)
NEXT_PUBLIC_BASE_URL=http://localhost:3000

# Edit lock expiration (client-side). Time in seconds after which the edit lock is released due to inactivity. Default: 180 (3 min). Use a smaller value locally (e.g. 30 or 60) for easier development.
# NEXT_PUBLIC_EDIT_LOCK_TIMEOUT_SECONDS=180

# LogRocket (Client-Side, opcional)
# Se não definir, usa o app ID padrão mbailj/livemode-roteiros
#Deixe vazio ou comente para desativar LogRocket em desenvolvimento
NEXT_PUBLIC_LOGROCKET_APP_ID=livemode-livescript/livescript

# Desativar completamente (não grava sessão nem envia erros):
NEXT_PUBLIC_LOGROCKET_DISABLED=1

# Reduzir impacto (sem rede/console na gravação; DOM e erros continuam):
NEXT_PUBLIC_LOGROCKET_MINIMAL=0

# API key server-side/CI for uploading source maps after `pnpm build`:
LOGROCKET_API_KEY=<REDACTED-PURGED>


# Bugsnag (Client-Side: erros + performance)
# Se não definir, usa a chave padrão do projeto
NEXT_PUBLIC_BUGSNAG_API_KEY=27bfa09a57d60ca8d3f5fbdf7514f825

NEXT_PUBLIC_BUGSNAG_ENABLED_IN_DEV=1
NEXT_PUBLIC_BUGSNAG_SEND_FROM_DEV=1


# Bugsnag (Server-Side: API routes + Airtable monitoring)
# Prefer a dedicated Bugsnag project for backend/server events.
# If unset, server integration falls back to BUGSNAG_API_KEY or NEXT_PUBLIC_BUGSNAG_API_KEY.
BUGSNAG_SERVER_API_KEY=<REDACTED-PURGED>
# Optional legacy/server key alias:
# BUGSNAG_API_KEY=your_server_project_api_key_here
#
# By default, backend events are sent only from production.
# To also send backend events from development:
BUGSNAG_SERVER_SEND_FROM_DEV=1
BUGSNAG_SERVER_ENABLED_IN_DEV=1

# Firebase Admin SDK (Server-Side Only)
# Variáveis SEM prefixo NEXT_PUBLIC_ são apenas para o servidor (não expostas ao cliente)
# Obtenha essas credenciais em: https://console.firebase.google.com/
# Project Settings → Service Accounts → Generate New Private Key
# NOTA: FIREBASE_PROJECT_ID geralmente tem o mesmo valor de NEXT_PUBLIC_FIREBASE_PROJECT_ID,
# mas são usadas em contextos diferentes (servidor vs cliente)
FIREBASE_PROJECT_ID=livemode-roteiros-dev
FIREBASE_CLIENT_EMAIL=firebase-adminsdk-fbsvc@livemode-roteiros-dev.iam.gserviceaccount.com
FIREBASE_PRIVATE_KEY=<REDACTED-PURGED>



# Comma-separated emails allowed to paginate through the full change-history log
# (infinite scroll in Histórico de Alterações). UX-only gate for internal admins.
CHANGE_HISTORY_ADMIN_EMAILS=lfernandez.projetos@livemode.com,tech@livemode.com


NEXT_PUBLIC_FEATURE_GENERATE_SCRIPT_IN_APP=true
AIRTABLE_ESPELHO_COTA_FIELD_ID=fldWDr447imihH4I6

# Integrations API (PRD-032 / LIVESCRIPT-9) — static API key for /api/integrations/*
# Local-only dev value (generated with `openssl rand -hex 32`). Do NOT reuse in Vercel.
INTEGRATION_API_KEY=<REDACTED-PURGED>


#AIRTABLE_ENDPOINT_URL=http://localhost:8080

OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
```