---
type: project
status: active
updated: 2026-09-01
aliases: [prxy, the proxy, airtable proxy, proxim]
tags: [airtable, go, observability, opentelemetry, cloud-run]
---

# Airtable Proxy — Project Notes (reconciled with the repo)

> Related: [[Proxy Environments]] · [[AIRTABLEGC-34]] · [[LiveScript]] ·
> [[Airtable Rate Limits]] · [[Agent Flow]]

> [!tip] Current state, 2026-08-21
> Path-based app identification is implemented and hardened in the repo. The
> next production step is the Go/Pulumi private-infrastructure slice recorded in
> [[2026-08-21 Deploy Airtable Proxy privately behind VPN]]. App-key
> authentication remains a future layer; the first deployment trusts the VPN
> and uses the app-id path for PAT selection and telemetry. Base access is
> controlled by PAT scoping in Airtable itself, not by proxy logic — see
> [[2026-08-24 Control base access via PAT scoping, not proxy logic]].
> msilva merges his own PRs
> ([[2026-08-14 No mandatory PR review while the proxy is pre-production]]).
> First confirmed anti-pattern found in the wild: an events-panel query returning
> an entire table with no need.
>
> **Tracking has already moved to Linear** — [Proxy do Airtable](https://linear.app/projetos-livemode/project/proxy-do-airtable-0d3e6dd699eb)
> (team `Projetos-livemode`), discovered 2026-08-18 to already hold 60 issues across
> 4 milestones (F1/F2/F3/Backlog deferido), a 1:1 mirror of `AIRTABLEGC` in both title
> and (mostly) status. See [[2026-08-14 Migrate project management from Jira to Linear]]
> for the full finding — this predates and supersedes the task as "still to do."
>
> **2026-08-19: F3 promoted to its own project**, per Luís
> ([[2026-08-19 1-1 Matheus - Luís]]) — [Proxy em produção validado c/
> LiveScript](https://linear.app/projetos-livemode/project/proxy-em-producao-validado-c-livescript-eb1691b502c3),
> sibling to `Proxy do Airtable` inside the same **Airtable GC** initiative. All
> 32 F3 issues (`PRO-74–105`) moved there. `Proxy do Airtable` keeps F1, F2, and
> Backlog deferido, left untouched at msilva's call — no rush, per [[Linear
> Project Structure]].
>
> **Reshaped same day, at msilva's request**: one-milestone-per-epic was wrong
> (milestones are checkpoints, epics are issue-level parents) — down to **3
> checkpoints**: *Proxy funcionalmente completo* (`PRO-78` Auth + multi-app,
> `PRO-74` Observabilidade completa, plus the finished `PRO-98` MVP work),
> *Proxy em produção* (`PRO-84` Deploy em produção, merged with `PRO-90` IaC
> (Pulumi) — see the finding below), *Validado com o LiveScript* (`PRO-95`).
> The four still-open epics (`78`, `84`, `74`, `95`) became real `parentId`
> parents of their own issues; the finished `PRO-98` MVP cluster was left flat
> on msilva's call, since parenting already-Done work has no payoff. **Known
> trade-off, flagged before acting**: this reintroduces the subtask-rollup
> blind spot [[AI status reporting on Linear]] documents — contained only as
> long as the milestones (which the readout does count) stay the real
> progress signal, not the parent issues' own rollup.
>
> **Finding from this pass**: `PRO-84` (Deploy em produção) and `PRO-90` (IaC
> Pulumi) weren't two phases — `PRO-91` "Codificar Cloud Run + Secret Manager
> em Pulumi" is the same target infra as `PRO-85`/`PRO-86` done as code
> instead of by hand. `PRO-90` is now a child of `PRO-84` rather than a
> sibling epic.
>
> **Resolved 2026-08-19, msilva**: Pulumi is the settled deploy method
> (stated as already decided, not re-confirmed with Luís in this session).
> `PRO-85` and `PRO-86` marked `duplicateOf` `PRO-91` and auto-cancelled by
> Linear — manual Cloud Run deploy and Secret Manager provisioning are
> superseded by `PRO-91` doing both as code. Left alone, deliberately:
> `PRO-89` (wiring the real notification channel) and `PRO-92` (codifying the
> alert policy in Pulumi) are related, not duplicate — different work, one
> is the rule, one is the channel it fires into. `PRO-93` (dashboards as
> code) also kept as-is; it re-declares the already-Done `PRO-102`/`103`
> dashboards, which is legitimate IaC scope, not duplicate effort to cancel.
>
> **Backlog review pass, 2026-08-19**: went through all 13 remaining Backlog
> issues in this project one by one with msilva. Fixes made: `PRO-84`/`PRO-90`
> bumped `Backlog` → `In Progress` (children had already started, status
> hadn't caught up); `PRO-91` bumped to `Todo` (next actionable step, only
> gated on the in-progress Pulumi-language spike `PRO-94`); `PRO-117`
> ("Definir canal de notificação") moved out of `Proxy do Airtable`'s
> "Backlog deferido" milestone into this project as a sub-issue of `PRO-89`,
> and unarchived — it had stopped being genuinely deferred once `PRO-89`
> needed the decision made. Everything else confirmed as accurate, no
> changes. New decision recorded from this pass: [[2026-08-18 Proxy first,
> defer LiveScript-side SDK changes]] — msilva confirmed the scope boundary
> first stated for `PRO-76` generalizes to most remaining LiveScript-side
> work.

## How it's framed internally

msilva's own description to the team, worth reusing because it's compact and it
landed:

> "O proxy é um serviço que vai ficar em frente ao Airtable, então toda requisição
> pro Airtable vai passar pelo proxy e aí a gente vai ter mais controle sobre quem
> faz as requisições e como faz."

The metrics named as the point of the exercise: **whether a request filters
rather than pulling the whole table**, and **which application is calling**.
Scoped to [[LiveScript]] first, *"mas a ideia é para ser geral para todos os
serviços aqui da empresa"* — presented internally as company-wide infrastructure,
not a LiveScript fix ([[2026-08-14 Recap da Semana]]).

> [!important] A consumer class the wiki had not recorded: the **n8n citizen flows** —
> Gabrielle, 2026-08-18
> Unprompted, in [[2026-08-18 1-1 Matheus - Gabrielle]]:
>
> > *"Inclusive, conectando com a questão do proxy, a ideia é esses projetinhos eles
> > passarem pelo proxy também […] todos que use[m Air]table[,] idealmente sim. Só que o
> > problema é a gente não tem noção de quantos são e cada pessoa tem sua própria
> > chave. Teria que cada pessoa entrar e dar individualmente a chave. […] a ideia era
> > ser chave [centralizada]."*
>
> The *projetinhos* are the n8n automations colleagues build for themselves — Gabriel's
> `RR*` flows being the known example. Three facts, all new:
>
> 1. **They are intended proxy consumers**, alongside the five named systems. A
>    different population: many small flows, non-engineer authors, no central registry.
> 2. **Nobody knows how many hit Airtable.** *"não temos noção de quantos são"* — the
>    proxy's largest open unknown, and a discovery problem before it is a migration
>    problem.
> 3. **Each author holds their own Airtable PAT.** Onboarding them means either every
>    person surrendering a key, or centralized key distribution.
>
> **This validates the current active task from an unexpected direction.** App auth +
> centralizing key distribution was framed as serving the five systems; the population
> that *cannot proceed without it* is this one. Per-app keys held by individuals is
> also a live credential-sprawl problem, not a hypothetical.
>
> **Also the strongest utility argument yet for A5.** [[Which agent should be built
> first]] weighs A5's reach; an unknown number of citizen-built flows, written by
> people who by Gabrielle's own account do not know what their automations cost, is
> precisely the population that produces detectable anti-patterns and nobody watching
> them. But note the ordering: **it cannot be measured until the flows are behind the
> proxy, and they cannot get behind the proxy until key distribution exists.**
>
> **Open — is discovery in scope for msilva?** Enumerating who runs Airtable-touching
> n8n flows is a prerequisite nobody owns. Gabrielle: *"a gente vai ter que entender
> melhor [a] maneira de aplicar isso aqui para dentro."*

> [!important] The proxy is one of three projects in an **Airtable governance and
> reliability** initiative — Gabrielle, 2026-08-18
> From [[2026-08-18 1-1 Matheus - Gabrielle]], where Linear's semantics were stated
> for the first time: an **initiative is the product or solution**, a **project is a
> specific segment of value delivery** ([[Linear Project Structure]]).
>
> > *"se tiver o próprio Air Table aqui governando confiabilidade, o proxy ele tá dentro
> > dele. Então aqui quando a gente entra nessa iniciativa, a gente tem três projetos
> > que o Luís tinha criado."*
>
> 1. **this proxy**;
> 2. **expanding the proxy to other apps beyond [[LiveScript]]**;
> 3. a **Livemode data hub**.
>
> The initiative's purpose in her words: *"tudo isso é voltado para garantir, né,
> govern[ança e con]fiabilidade dos dados que hoje a gente consome do A[irtable]."*
> **Governance *and reliability*** — keep both words. Reliability is what the World Cup
> data loss was about, and it is the half a purely-governance framing loses.
>
> **This is organizational confirmation of the framing below, not a restatement of
> it.** The company-wide intent was previously only msilva's own verbal framing to
> the team and Gabrielle's rationale. It is now the shape of the initiative in the
> tool that runs the area's planning. **Luís created all three projects** — so the
> expansion beyond LiveScript was scoped before msilva arrived.
>
> **Item 3 — the data hub is probably the wiki's missing Phase 2.** Gabrielle hedges
> heavily and is relaying Luís second-hand: *"um data hub que ele tava criando da Live
> Mode que não sei se tem mais detalhes aqui […] Banco de dados intermediári[o] […] Eu
> acho que é a ideia de ir migrando do air table. Pode ser. Sei. Eu acho que é isso.
> Deve ser."*
>
> An **intermediate database**, with the idea of **migrating off Airtable**. That is
> the merged programme's Phase 2 as recorded in
> [[2026-08-10 Onboarding Técnico - Matheus]] and [[LiveScript]] — data that doesn't
> need to be in Airtable moving to a separate database. This page says below that
> Phase 2 is **absent from the roadmap**; it is not absent, it is a **sibling project**.
> *(unverified: this vault's inference from two hedged descriptions matching. **Ask
> Luís**, not Gabrielle — she said twice she only half-remembers it.)*
>
> **Practical consequence for the migration**: msilva is populating **one project**
> inside this initiative, not modelling the whole programme
> ([[2026-08-14 Migrate project management from Jira to Linear]]).

> [!important] Orca and other services are planned consumers — msilva, 2026-08-18
> The plan is to **plug [[Orca (CDE)]] and other services into the proxy**. This
> makes the company-wide framing above concrete rather than directional, and it
> reframes the proxy: not [[LiveScript]] infrastructure, but the **shared Airtable
> data plane** for the area's five systems (CRM, Orca, [[LiveScript]], Taxonomia,
> [[Pulse]] — [[2026-08-14 Papo de Projetos]]). Orca's own migration timing is
> still unconfirmed — it isn't mentioned in [[Orca Next Version]]'s roadmap.
>
> **Why it matters beyond this page.** One [[Agent Flow]] A5 Watcher on proxy
> telemetry would eventually cover *every* service behind the proxy, including a
> business-critical ML system whose automation is valued at ~10 headcount. That
> resolves the "Orca or LiveScript?" targeting question — it is both, through one
> chokepoint. See [[Which agent should be built first]].
>
> **Open — is Orca the "second Airtable consumer" already recorded below as
> refetching and over-loading needlessly?** If so, anti-pattern detection has a
> named first customer. *(unverified: not stated; the two facts are recorded
> separately and may or may not be the same system.)*
>
> **Open — is the migration sequenced or directional?** Whether Orca is queued
> behind GC-5 or merely intended decides when A5's utility actually arrives.

## Why this project exists

Added 2026-08-17 from [[2026-08-10 Onboarding Técnico - Matheus]]. The rest of
this page describes *what* is being built; this section is the *why*, which came
from Gabrielle Ferreira in onboarding.

- **The trigger was the World Cup.** A day of heavy concurrent usage on
  [[LiveScript]] — an app requiring real-time editing and real-time feedback —
  exceeded what the Airtable API could absorb. See [[Airtable Rate Limits]].
  **Users lost work**: requests failed and edits already made were destroyed —
  *"muitas das requisições que as pessoas faziam davam erro e aí perdia o que foi
  feito"* ([[2026-08-14 1-1 Matheus - Gabrielle]]). The failure mode was data
  loss, not just slowness, which is a materially stronger justification for the
  project than "it got slow."
- **The user-visible cost is still being paid.** LiveScript restricts its users
  to stay under the limit: whole-row locking even when two people need different
  fields, and only one person creating rows at a time. These are workarounds, not
  product intent. **The proxy is what eventually lets them be removed** — worth
  keeping in view, since the repo-level work doesn't make this obvious.
- **Bad usage is cheaper to detect than to hunt.** A second Airtable consumer is
  poorly implemented — refetching and over-loading needlessly. Gabrielle's
  rationale for building detection into the proxy: *"se eu tiver que ficar
  procurando essas más práticas aqui, é muito mais trabalhoso do que eu criar um
  cara mega inteligente aqui que ele identifica e alerta."*
- **Migration is a base-URL swap.** Apps repoint at
  `https://proxy.livemode.space/{app-id}` and it passes through
  transparently — expected to be near-zero effort for app owners. This confirms
  the "no DNS cutover" correction below, from the other direction.
- **This is half of a merged initiative.** The proxy and *LiveScript
  stabilization* were joined into one programme. Phase 2 moves data that doesn't
  need to be in Airtable into a LiveScript-only database — see [[LiveScript]].
  ~~**That phase is absent from the roadmap recorded below.**~~ **Corrected
  2026-08-18**: it is not absent from the *programme* — it is very likely the
  **Livemode data hub**, a sibling project in the same initiative (see the callout at
  the top). It remains absent from *this project's* roadmap, which is now the correct
  place for it not to be. *(unverified: inference from two hedged descriptions
  matching; ask Luís.)*

> [!question] Open tension — does the proxy retry 429s, and when?
> Gabrielle described 429 retry/backoff as a proxy responsibility, so apps don't
> each implement their own: *"deu um erro de 429 […] ele próprio segura, tenta e
> retenta de novo."* But this page records v1 as **deliberately
> observability-only, with no enforcement or intervention** — and a retry is an
> intervention. Either retry is a later phase not yet written down, or the
> observability-only framing needs qualifying. **Unresolved.** Ask Gabrielle;
> the design doc's phase list is the place to check first.

> **Updated 2026-08-11** after reading the actual repo
> (`livemode-airtable-proxy-main`). The earlier version of this note was a
> research plan written *before* seeing the code, and several of its core
> assumptions were wrong. This version reflects what the repo and design doc
> actually say. Corrections are called out inline.
>
> Source of truth in the repo:
> - `airtable-proxy-design.md` — full design + roadmap
> - `doc/STATUS_proxy_airtable.md` — status as of 2026-06-30
> - `README.md` — how to run v1 locally
> - `internal/proxy/proxy.go` — the actual proxy core

---

## TL;DR — where the project actually is

- **It is not greenfield.** v1 is built (8 commits) and its acceptance test
  passes. There is a working Go proxy, two Grafana dashboards, and a 429 alert.
- **v1 adds no caching and no rate-limiting** — *on purpose*. The strategy is
  **measure first, then decide what to fix.**

  > [!warning] Corrected again 2026-08-21 — identity is path-based now
  > The old warning described the then-live `X-App-Id` rejection. Repo commits
  > `8e4297b`, `bf5e681`, and `12d1423` replaced it with hardened
  > `/{appId}/...` routing. Unknown/missing IDs still fail closed, but a valid app
  > ID is currently identification, not proof of identity. The initial private
  > deployment accepts that trade-off behind the VPN; an app key comes later.
- **My job right now is not to build caching or a rate limiter.** Those are
  deferred (roadmap phases 5–6). The live work is enriching the telemetry.

---

## Corrected mental model (things I had wrong)

1. **Language is Go, not Node.** The proxy is Go's `net/http/httputil.ReverseProxy`.
   Ignore any "in-memory Node cache / Redis-vs-Node" framing — not relevant.

2. **No DNS cutover.** I assumed clients would transparently hit
   `api.airtable.com` and DNS would silently redirect to the proxy. The real
   onboarding (design §11) is **explicit per-app reconfiguration**:
   - `airtable` SDK: set `endpointUrl: <proxy-url>` (via `AIRTABLE_ENDPOINT_URL`)
   - raw REST: swap the base-URL constant
   - put the app ID in the endpoint path (`https://proxy.livemode.space/{app-id}`)
   - remove the real Airtable PAT from the app environment
   So there is no "intercept at the DNS layer" step to plan.

   > [!note] Reconciled 2026-08-21 with the shipped path implementation
   > [[LiveScript]]'s Airtable SDK still requires a non-empty `apiKey` and sends
   > it as `Authorization`, so the client may need a non-secret placeholder until
   > the future app-key layer gives that field a real proxy credential. The proxy
   > overwrites/removes incoming authorization before Airtable. No custom app-id
   > header or SDK patch is needed: the endpoint path carries identity for SDK and
   > raw REST traffic alike.

3. **Observability-first, not caching-first.** The whole premise is: instrument
   every Airtable call, *then* let the dashboards tell us whether 429s come from
   duplicate reads (→ cache) or sustained volume (→ rate limit). We don't guess
   up front.

4. **Token-terminating boundary (decided).** The proxy holds the Airtable PAT
   (Secret Manager) and injects `Authorization: Bearer {PAT}` on every request.
   Apps never receive the real PAT. Path-based app identification is shipped;
   separate app-key authentication is deliberately deferred. The first private
   deployment trusts the VPN/network, while a future app-key layer will prove
   that the caller may claim the selected app ID. See
   [[2026-08-21 Deploy Airtable Proxy privately behind VPN]].

5. **Instrumentation is decided: OpenTelemetry / OTLP.** Local dev uses a single
   `grafana/otel-lgtm` container (Loki + Prometheus + Tempo + Grafana).
   Production points the same OTLP exporter at Cloud Monitoring / Datadog / New
   Relic — proxy code unchanged. **No BigQuery** (Log Analytics is the optional
   upgrade if ad-hoc SQL is ever needed). — Answers my old "BigQuery? Datadog?"
   question.

6. **Deployment target is decided: private Cloud Run, `min=1 max>1` for HA.**
   It is reached through the VPN at `https://proxy.livemode.space`, via private
   DNS and an internal HTTPS load balancer/serverless NEG; Cloud Run ingress is
   `internal`. See [[2026-08-21 Deploy Airtable Proxy privately behind VPN]]. The proxy
   is on every app's critical path, so a single instance is unacceptable. v1
   holds no shared state, so multiple instances need no coordination. (Design §3.)

---

## Production private infrastructure plan (2026-08-21)

```text
Internal apps -> VPN -> GCP VPC -> private DNS (proxy.livemode.space)
  -> regional internal HTTPS load balancer -> serverless NEG
  -> Cloud Run (internal ingress) -> api.airtable.com
```

**Trust model now.** Network reachability through the VPN gates access. The
app-id path selects the PAT and telemetry identity; it does not authenticate
the caller. There is no base allow-list in the proxy — base access is
controlled by how each PAT is scoped in Airtable, not by proxy code, see
[[2026-08-24 Control base access via PAT scoping, not proxy logic]]. A future
app key adds proof of identity without making the service public.

**Central infrastructure supplied to the app stack:** existing GCP project,
VPC/VPN and routes, frontend and proxy-only subnets, private DNS zone, and TLS
certificate/PKI. `proxy.livemode.space` uses split-horizon DNS and resolves to the
load balancer's private IP only for connected clients.

**Go/Pulumi-owned application resources:** required APIs, Artifact Registry,
runtime service account/IAM, Secret Manager metadata/access, Cloud Run v2,
serverless NEG, regional backend service, URL map, HTTPS target proxy, private
address, forwarding rule, and (if delegated) the DNS record. Image build/push
stays in CI and deployments reference immutable images.

**Secrets.** Pulumi owns secret containers and IAM; a secure operator/pipeline
supplies secret values. The current single `PROXY_APPS` secret matches the code.
PATs and future app keys never enter the repository or clear-text config.

**Egress.** Start with Cloud Run's normal internet egress to Airtable. Add Direct
VPC egress + Cloud NAT only if policy requires a static IP or centralized egress.

**Before coding:** resolve project/region, exact network resource IDs, VPN DNS
forwarding, DNS/certificate ownership, Pulumi state backend, secret-delivery
procedure, and the production OTLP backend.

> [!tip] PRO-91 coded 2026-08-24 — Cloud Run + Secret Manager, minus Secret Manager
> Wrote the Go Pulumi program at `infra/pulumi/` (its own module, separate
> from the proxy's `go.mod`): required API enablement, an Artifact Registry
> Docker repo, a dedicated Cloud Run runtime service account, and the Cloud
> Run v2 service itself (`internal` ingress, `min=1`/`max=3`, image supplied
> via config with a placeholder until CI publishes a real tag). `go build` /
> `go vet` / `gofmt` all pass; `pulumi preview`/`up` still need msilva's real
> GCP project and credentials.
>
> **Scope narrowed mid-implementation**: per Luís (relayed by msilva),
> `PROXY_APPS` stays managed as a Pulumi secret config value for this first
> phase rather than moving into GCP Secret Manager — no
> `gcp.secretmanager.*` resources were written. See
> [[2026-08-24 Manage Airtable Proxy secrets in the codebase before Secret Manager]],
> which also corrects [[2026-08-21 Deploy Airtable Proxy privately behind VPN]]'s
> "Pulumi ownership" list on this point.
>
> Still out of scope, left for a later `PRO-90` sibling: the serverless NEG,
> internal load balancer, private DNS record, and VPC/subnet wiring — all
> blocked on the network identifiers in the "before coding" list above, which
> remain unresolved.

> [!tip] Local integration confirmed under the path scheme, 2026-08-24
> [[2026-08-24 1-1 Matheus - Luís]]: msilva confirms LiveScript and the proxy
> run together locally with the URL-path identification scheme
> ([[2026-08-19 Identify proxy apps by URL path, not header]]), and Grafana
> shows the data flowing end-to-end — the first confirmation of this since the
> header-based approach was retired. Metrics captured match the design doc
> exactly (filter presence, operation, response bytes); **handler-level
> tracing is still not possible** without a LiveScript-side change (this is
> the "normalize `app.route`" item below, now confirmed still open, not just
> unconfirmed).
>
> Also on this call: neither of them had actually opened Grafana's
> pre-existing default **"antipatterns"** dashboard before — it was already
> firing on msilva's own recent local tests (2× full table scan, 2× select
> without field projection). Luís wants it turned into a **management-level**
> view for decision-making (built via code — Claude Code can generate it),
> distinct from the raw per-request panels. Reviewed this round via a live
> walkthrough rather than a diff read — see
> [[2026-08-14 No mandatory PR review while the proxy is pre-production]].

---

## The caching landmine (remember this)

Airtable record payloads embed attachment URLs (`v5.airtableusercontent.com`)
that **expire in ~2 hours** — which is exactly why the app currently uses
`no-store`. Therefore any future cache must apply **only to
metadata / schema / field-options, never to record data**. (Design §7.)

The clearest cache win already spotted — metadata refetched on every request —
is actually an **app-side fix** (`config.service.ts`, `narrator.service.ts`),
not a proxy feature. Good to know before proposing "just add a cache."

---

## What's already built (v1 — done)

- **`internal/proxy/proxy.go`** — `ReverseProxy` with `Director` (injects PAT) +
  `ModifyResponse` (captures status/`retry-after`/`x-airtable-request-id`).
  Telemetry pushed onto a buffered async channel (cap 1024) so the hot path adds
  zero latency. Non-blocking send: drops rather than stalls if the channel fills.
- **`internal/proxy/telemetry.go`** — parses `baseId`/`tableId`/`operation` from
  the request path.
- **`internal/otelsetup/otel.go`** — 3 OTel providers (trace/metric/log) → OTLP/gRPC.
- **`cmd/proxy/main.go`** — `/healthz`, graceful shutdown, `otelhttp` span per request.
- **Dashboards** — "Airtable Proxy — Overview" (6 panels) + "Airtable Usage &
  Anti-patterns" (16 panels: full-scan, over-fetch, N+1, cache candidates,
  rate-limit headroom vs 5 req/s, …).
- **Alert** — `airtable_429_alert`: fires when `increase(airtable_429_count_total[5m]) > 0`.
- **`scripts/acceptance.sh`** — validates the 4 design §9 done-criteria.

**Security invariants (do not break):** PAT / Authorization is *never* logged;
response headers pass through an allowlist only.

---

> [!tip] Progress, 2026-08-18 ([[2026-08-18 1-1 Matheus - Luís]])
> **`hasFilter` and operation detection fixed.** The proxy previously inferred the
> Airtable operation from the HTTP method alone — wrong, since Airtable allows a
> `select` via `POST`. Now uses a proper triage/switch instead. Page size and
> response byte count are also captured. Metrics confirmed flowing into Grafana.
> **`X-App-Id` confirmed working end-to-end** in the dev environment (proxy +
> LiveScript integrated locally) — **while the now-reverted client patch was in
> place**; see [[How LiveScript sends the proxy X-App-Id header]]. This is
> progress against the open item directly below, not yet closed —
> `hasFieldProjection`/`recordCount` status unconfirmed.
>
> **Scope clarified the same day**: msilva's job is the proxy working 100%, not
> LiveScript's own Airtable usage. LiveScript talks to Airtable via **both** the
> SDK and hand-built REST calls; only REST-transport validation is in scope right
> now, and that's a deliberate stopping point, not a gap — *"a gente não quer sair
> de zero para 100%."* Partial migration (some percentage of calls through the
> proxy) is an accepted intermediate state, not a problem.

## What's actually open next (the real work)

From `STATUS_proxy_airtable.md`, immediate items — all about *enriching
telemetry*, not enforcement:

- [x] **Extract usage signals in the proxy itself:** `hasFilter`,
      `hasFieldProjection`, `recordCount`, `bytes`. Today these come only from
      the roteiros ([[LiveScript]]) app's SDK wrapper — `proxy.go`'s `emit()` logs
      status/latency/operation but not these. Moving them into the proxy makes
      anti-pattern detection work for *any* app, not just roteiros.
      **Progress 2026-08-18**: `hasFilter`, operation, page size and bytes now
      captured in the proxy per the callout above. `hasFieldProjection`/`recordCount`
      not confirmed either way — verify in the repo before marking fully done.
      **Resolved 2026-08-26**: `hasFilter`/`hasFieldProjection` confirmed captured
      as booleans (see `PRO-371`/`PRO-397` below, filed against exactly this pair).
      `recordCount` is still not confirmed anywhere in this page — the `[x]` above
      covers `hasFilter`/`hasFieldProjection`/`bytes` only; `recordCount` remains
      genuinely open, not just unconfirmed prose.
- [ ] **Normalize `app.route`** into logs/spans (from Next.js
      `getRequestContext()`) for proxy↔app correlation, then add a "Top app
      routes by Airtable calls" panel.
- [ ] Clean the SDK-serialized body (`{id, fields}` only; drop `_table`/`_base`).
- [ ] Commit roteiros observability work on `feat/airtable-observability-local`
      (not `main`, no push).

**Deferred (do not start unprompted):** ~~multi-app API-key auth (phase 3)~~,
Pulumi IaC (phase 4), rate-limiting (phase 5), metadata cache (phase 6).

> [!important] Superseded 2026-08-17 — app auth is active work, not deferred
> As of 2026-08-14 msilva is **building app authentication and centralizing
> Airtable key distribution** ([[2026-08-14 1-1 Matheus - Gabrielle]]). That is
> "phase 3" in the list above, being done during phase 1.
>
> This is not scope creep — **Luís deliberately reordered the issues**, partly to
> keep msilva inside the proxy and out of [[LiveScript]], and he considers his
> original breakdown outdated enough that he'd design it differently today. The
> phase numbering above therefore describes the *design doc's* plan, not the
> current plan. Treat the roadmap as a record of intent, not a schedule.
>
> The reordering is also why the issues are being restructured during
> [[2026-08-14 Migrate project management from Jira to Linear]].

> [!note] Confirmed again 2026-08-17, by msilva to the whole team
> In [[2026-08-17 Weekly - Projetos e Tarefas]] he reported both services running
> locally and integrated (*"já consegui rodar tanto o [proxy] quanto o live script na
> minha máquina. Já fiz a integração entre os dois"*), the ticket's metrics being
> captured, and **proxy authentication as the current work** — *"saber de quem tá se
> comunicando com o proxy, pra gente conseguir ter o controle melhor de métricas,
> observabilidade e tal."* Third independent confirmation, and the first stated to
> the team rather than in a 1:1.

---

## Restructuring the issues in Linear — shape matters (2026-08-18)

The migration is msilva's task and the restructuring is sanctioned
([[2026-08-14 Migrate project management from Jira to Linear]]). What was missing
was any guidance on *shape*, and there now is some:

The team reviews an **AI-generated status readout** weekly, and it **ignores subtask
progress** — it reports a parent issue as undelivered until the whole thing is done.
Measured effect on a real project in the same meeting: **6% reported against 20–25%
actual**, which produced a stated near-panic from Gabrielle. See
[[AI status reporting on Linear]].

**So: flat issues, not deep subtask trees.** This is not cosmetic — the deadline
conversation for this project is happening in the week of 2026-08-17, msilva has the
shortest track record on the team, and the readout is what Gabrielle and Luís see
between conversations. Luís's `Triagem`-column convention is the complement: park
found-but-unprioritized work there, where it's visible, instead of nesting it.

**Three more shape constraints, from [[2026-08-18 1-1 Matheus - Gabrielle]]** — full
detail on [[Linear Project Structure]]:

- **Use milestones to group deliveries.** Milestones are the level *above* issues and
  the readout counts them, so they express "this is a chunk of work" without incurring
  subtask blindness. This is the structural alternative to nesting.
- **Split along "new work" vs "fixing earlier work".** The team did exactly this to
  *Fronte de Negócios*, separating `Evolução do Front-End` from `Estabilização`,
  because a project mixing the two yields a completion percentage that describes
  neither. The proxy has the same seam: v1 hardening versus new capability
  (app auth, key distribution, onboarding new consumers).
- **Use Luís's Markdown templates** for bugs, spikes and epics rather than inventing
  a format. Issue descriptions are what the product-validation gate reads
  ([[2026-08-18 Product feedback in Linear, code review in Git]]).

**Use the established method rather than doing it by hand.** Gabrielle's own workflow,
described in [[2026-08-18 1-1 Matheus - Gabrielle]]: hand the documentation and PRDs to
Claude, ask it to push everything into Linear, and **it writes the project description,
the milestones, and the issues inside them** using Luís's templates —
*"ele cria as tarefas sozinho."* The proxy already has the input this expects:
`airtable-proxy-design.md` and `doc/STATUS_proxy_airtable.md`. This is the area's
routine practice, not an experiment.

**And a clock.** The Linear Business trial expires **2026-09-09**, with the ~250-issue
free cap behind it and Gabrielle on leave 2026-08-24 → 2026-09-10. **msilva has
already hit that cap once** — Gabrielle: *"você tomou um rate limit, né? […] não podia
mais criar."* He committed in the 1:1 to moving everything off Jira **today**
(2026-08-18). Fourth time recorded.

---

## Corrected learning roadmap

**Still relevant:**
- Airtable API fundamentals + the **5 req/s per base** limit (429 → ~30s
  lockout, `Retry-After` header present).
- HTTP caching basics — but framed around the metadata-only constraint above.
- Reverse-proxy patterns — specifically Go's `httputil.ReverseProxy`
  (`Director` / `ModifyResponse` / `ErrorHandler`), not Express middleware.

**Newly important (weren't on the original list):**
- **Go** — enough to read/modify `internal/proxy/*`. This is the real gap.
- **OpenTelemetry / OTLP** — the metric/log/trace SDK the whole thing runs on.
- **Grafana + LGTM stack** (Loki / Prometheus / Tempo) — how the dashboards are
  queried (LogQL, PromQL, TraceQL).

**De-prioritized (deferred phases):**
- Token-bucket rate limiting — real, but phase 5, and harder than the original
  note assumed (multi-instance → needs Memorystore or a per-instance budget).
- Request queueing — not planned; the design explicitly avoids a queue.

---

## Questions still genuinely open (worth asking Gabrielle / team)

Most of my original "questions to ask" are already answered by the design doc.
These remain open (design §14):

- **Telemetry backend for prod** — Cloud Monitoring vs Datadog vs New Relic?
- **Retain queryable raw records for usage analysis?** (Log Analytics / BigQuery
  / SaaS query language) — cheap to decide now, hard to backfill later.
- **Caching freshness tolerance** per table/operation — still the right question,
  but scoped to *metadata only* given the attachment-URL constraint.
- ~~Pulumi language (Go / TS / Python)~~ **Resolved 2026-08-20**: Go, aligned
  with Luís — see [[2026-08-18 Bring options to Luís before deciding,
  communicate async and often]]. Matches msilva's stated lean (toolchain/CI
  consistency with the proxy).
- Notification channel (Slack / PagerDuty / email); where the app API-key map
  lives when that future layer ships; ~~one base or all bases in v1~~
  **resolved 2026-08-24**: no proxy-side base restriction at all — see
  [[2026-08-24 Control base access via PAT scoping, not proxy logic]].
- **Private-infra inputs** — GCP project/region; VPC, frontend subnet and
  proxy-only subnet IDs; VPN DNS forwarding; private-zone and certificate
  ownership; Pulumi state backend; whether egress must use a static IP/NAT.

**Added 2026-08-18, from [[2026-08-17 Weekly - Projetos e Tarefas]] — two consumers
nobody connected to this project in the room:**

- **Fronte de Negócios wants an automated test suite *isolated from Airtable*,** and
  is deciding that week whether to run it. Luís: the manual test cases are
  *"bem chatos"* and doing them by hand is *"impossible"*. Isolation from Airtable is
  precisely what a controllable chokepoint offers — but nobody suggested the proxy,
  and the likely default is mocks or a separate base. **Worth raising while the
  decision is still open**, and worth knowing that a project two weeks from
  production is an Airtable consumer.
- **The matriz external-events flow** proposes writing into the matriz through an
  automation, with a requirement to log who created and who cancelled each record —
  i.e. attribution on Airtable writes. That is the same problem `X-App-Id` solves for
  reads. See the use-case note in [[Agent Flow]] and the tables in
  [[Proxy Environments]].

Both are **read/write consumers arriving independently**, which strengthens the
shared-data-plane framing this page already carries for Orca — and note that Orca
itself, as of 2026-08-17, still has **nothing deployed to production**, so it is not
yet the second consumer in practice.

**To verify (couldn't check myself):** the repo is on a network share with no
visible `.git`, so I can't confirm the "8 commits / acceptance passed" claims in
the status doc. Worth confirming the actual git state matches the doc.
(Resolved 2026-08-18: the real clone is
`C:\Users\msilva\projects\livemode-airtable-proxy` — confirmed via `git remote`/
`git log`. A same-named copy under `Documents\repos\` has no `.git` and is stale.)

## Timeline tension: two sources disagree on where this month goes (2026-08-19)

> [!info] Date corrected 2026-08-19 (eighth lint)
> This section and its `log.md` entry were originally dated 2026-08-18. The
> commit that recorded it landed 2026-08-19 10:44 GMT-03:00, and that's when the
> conversation actually happened — a one-day dating slip, not a backdated fact.
> `log.md`'s entry is left as-is per the append-only rule; this page is corrected
> since it isn't.

msilva asked whether a month is realistic to finish what was then F3 (production
deploy + LiveScript-validated) — since promoted to its own project, [Proxy em
produção validado c/ LiveScript](https://linear.app/projetos-livemode/project/proxy-em-producao-validado-c-livescript-eb1691b502c3)
(see the callout at the top). What's actually left there: the auth epic's tail
(`PRO-82` resolved 2026-08-20 — Luís picked the simplest onboarding path, no
self-serve tooling for now; `PRO-83` (key/secret rotation strategy) resolved
the same day, msilva's own call, not re-run past Luís — same policy: manual
rotation, no tooling, revisit once real DB-backed storage exists), production
deploy (`PRO-84`, 5 issues, none started), IaC via Pulumi (`PRO-90`, 5
issues), and LiveScript integration/validation (`PRO-95`, 2 issues) — roughly
19 open issues against 13 already Done since onboarding (2026-08-10).

Two sources on record don't point the same way:
- **Luís, 2026-08-18 1:1**: *"Teu primeiro trabalho é botar o proxy funcionando
  100%."* The proxy is msilva's actual current job; LiveScript's own SDK-side
  migration is explicitly out of scope for now.
- **The intranet `metas.md`/`combinados.md`** (`https://livemode-intranet.vercel.app/#pessoa/matheus`
  — read live 2026-08-19, not yet otherwise ingested here) frame the proxy as
  backlog-qualified work, not the headline measure: the August commitment is the
  **Agent Flow design validated with Gabi by 2026-08-31**, and the 60-day goal
  (2026-10-09) is "first agent of the flow in production" — a different project.

Not resolved — msilva's call 2026-08-19 was to **leave the proxy's Linear dates
as they are** (no milestone/issue due dates set) rather than pick a side. Worth
raising explicitly with Luís or Gabrielle if the two timelines start to
genuinely conflict, per [[2026-08-18 Bring options to Luís before deciding,
communicate async and often]].

---

## Things to actually do

- [x] Turn Grafana's default "antipatterns" dashboard into a management-level
      view for decision-making, via code (Claude Code) — 2026-08-24,
      [[2026-08-24 1-1 Matheus - Luís]]. **Correction, 2026-08-26**: this page
      previously said the "Insights" dashboard was "in design, not yet
      built" — wrong. Checked the repo directly
      (`grafana/dashboards/airtable-insights.json`): it's already built, 5
      panels — anti-pattern rate as a trend (%, not raw count), per-base
      rate-limit headroom (count of peaks above 80% of the limit), an
      estimated-avoidable-calls figure scoped to the no-filter/no-projection
      case only (the filtered subset isn't reliable yet — see `PRO-371`
      below), latency p95 with-filter vs. without-filter per table, and top
      tables by no-projection bytes.
      **Also found, same pass**: an unpushed local branch
      `msilva/airtable-proxy-dashboard-telemetry-fixes` (commit `c8f1941`,
      1 ahead of `main`) already fixes real problems a dashboard review
      caught — "Total de chamadas" was double-counting the app-log and
      proxy-log streams instead of splitting by `service_name`; the N+1
      panels were misreading empty `trace_id` as the biggest offender
      (rescoped to `roteiros-nextjs`, the only side with valid trace
      correlation); several panel titles/descriptions stated estimates or
      heuristics as fact (softened, with the `hasFilter`-only-catches-
      `filterByFormula` caveat extended everywhere it applies); the 429
      alert no longer implies per-base rate-limiting is the only possible
      cause (Airtable also 429s on the monthly plan cap). Proxy-side: async
      telemetry emission now reattaches the request's span context, so log
      lines finally carry a `trace_id`/`span_id` — verified live against a
      real request's Tempo span. **Merged 2026-08-26**:
      [PR #11](https://github.com/livemode-org/livemode-airtable-proxy/pull/11)
      (`f530530`, on top of `c8f1941`) — live on `main`.
      **Tracked in Linear, 2026-08-26**: this whole item (no issue existed
      before) is now `PRO-396`, filed `Done` under the `PRO-74` epic.
- [ ] `PRO-371` — detect duplicates among **filtered** Airtable calls
      specifically (the no-filter case above is already solid). Proxy today
      only logs `hasFilter`/`hasFieldProjection` as booleans, not the actual
      filter/fields content, so two differently-filtered calls to the same
      table are indistinguishable. Proposed direction (not decided): log a
      hash of `filterByFormula`+`fields`, not the raw value.
      **New sibling issue, 2026-08-26**: `PRO-397` — filtering by Airtable
      `view` isn't detected at all (`hasFilter` only looks at
      `filterByFormula`), a distinct gap from telling two `filterByFormula`
      filters apart. Found while documenting `PRO-396`'s caveats.
- [x] ~~Commit whatever changed (msilva says env-only) to get LiveScript's
      `feat/airtable-observability-local` branch working against the proxy;
      confirm whether the REST-call path needs the same URL-sourcing fix as
      the SDK path — same call.~~ **Deferred, 2026-08-26**: msilva confirms
      this isn't happening for now — no LiveScript-side change was made. A
      fresh instance of [[2026-08-18 Proxy first, defer LiveScript-side SDK
      changes]], not a dropped commitment. **Correction, same day**: the
      earlier note here pointed at `PRO-62` — wrong issue, that one is about
      committing the observability branch work, unrelated to this
      SDK-vs-REST question. The confirm-REST-path half of this commitment
      was investigated separately (repo `livemode-roteiros-nextjs`, branch
      `feature/airtable-proxy-observability`): SDK calls read
      `AIRTABLE_ENDPOINT_URL` natively and are confirmed working locally;
      7 hand-rolled REST call sites across 4 files
      (`airtable-helpers.ts`, `airtable.service.ts`,
      `script-base.service.ts`, `config.service.ts`) are hardcoded to
      `api.airtable.com` and don't read the var — need their own fix.
      Finding filed onto `PRO-96` (already the right issue for LiveScript
      proxy routing, Cenário A), which stays in Backlog per the deferral
      above — this only documents the gap, doesn't schedule the fix.
- [ ] Confirm the private-infra inputs in
      [[2026-08-21 Deploy Airtable Proxy privately behind VPN]].
- [ ] Start the Go/Pulumi stack only after those inputs and ownership boundaries
      are known; do not recreate shared VPN/VPC/DNS infrastructure in this repo.
- [ ] Get a **dev** Airtable PAT + a test base id → run `docker compose up --build`
      and watch the dashboards populate (README "Run it").
- [ ] Learn just enough Go to read `internal/proxy/proxy.go` comfortably.
- [ ] Decide with the team on the immediate telemetry-enrichment work above.
- [ ] Ask Gabrielle the still-open §14 questions (not the ones already answered).
