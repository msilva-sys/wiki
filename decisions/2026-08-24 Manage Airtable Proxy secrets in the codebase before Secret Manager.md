---
type: decision
status: stable
updated: 2026-08-24
date: 2026-08-24
decided_by: Luís Fernandez (relayed by Matheus Silva)
source: "msilva relaying a conversation with Luís, 2026-08-24 — not yet captured in a wiki-ingested transcript"
tags: [proxy, secrets, pulumi, gcp]
aliases: [secrets in codebase first, defer Secret Manager]
---

# Manage Airtable Proxy secrets in the codebase before Secret Manager

**Decision.** For the first phase of `PRO-91` (Cloud Run + Secret Manager in
Pulumi), `PROXY_APPS` stays managed in the codebase/deploy-config layer —
a Pulumi **secret config value**, passed straight through as a Cloud Run env
var — rather than moving to GCP Secret Manager. Per Luís, relayed by msilva
during the `PRO-91` implementation session (2026-08-24): *"primeiramente,
vamos fazer o gerenciamento das secrets na própria codebase. Depois nós
ajustamos esse fluxo."* Secret Manager is explicitly a later, separate step —
not dropped, deferred.

## What this means concretely

- No `gcp.secretmanager.Secret` / `SecretIamMember` resources in the Pulumi
  program (`infra/pulumi/` in the repo).
- `PROXY_APPS` is read via `pulumi config get --secret proxyApps` and wired
  directly into the Cloud Run v2 service's env vars. It's encrypted in Pulumi
  state/config the same way any Pulumi secret is, but there's no separate
  Secret Manager container to provision, grant access to, or rotate through.
- The runtime service account [[Airtable Proxy]]'s Cloud Run revision uses
  carries no elevated IAM roles yet, since there's no Secret Manager resource
  for it to read.
- Rotation is: update the Pulumi secret config value, `pulumi up` (new
  revision). This is the same "manual rotation, no tooling" policy already
  settled in `PRO-83` — see
  [[2026-08-18 Bring options to Luís before deciding, communicate async and often]]
  — not a new mechanism, just where the value physically lives.

## Relationship to the private-deployment decision

[[2026-08-21 Deploy Airtable Proxy privately behind VPN]] listed Secret
Manager secret metadata + access policy under "Pulumi ownership." This
decision narrows that: Secret Manager is still the eventual target (that
doc's shape doesn't change), it's just not built in this first `PRO-91`
pass. That page has an inline correction pointing here.

## Why

No rationale beyond Luís's stated preference was captured in this relay —
msilva reported the instruction, not the reasoning behind it. If a fuller
"why" surfaces later (e.g. in an actual 1:1), fold it in here rather than
leaving this section thin indefinitely.

## Open

- No transcript exists for the original Luís conversation msilva is drawing
  this from — same gap flagged in `log.md` on 2026-08-18 for an earlier,
  separate Secret Manager conversation with Luís that was also never
  ingested. If either surfaces later, reconcile against this page.
- What triggers the future move to Secret Manager (a specific condition, or
  just "later") is not defined.
