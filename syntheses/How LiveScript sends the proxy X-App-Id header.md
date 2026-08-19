---
type: synthesis
status: superseded
updated: 2026-08-19
date: 2026-08-17
aliases: [x-app-id, app auth header, livescript proxy auth, GC-5 client side]
tags: [airtable, proxy, auth, sdk, livescript, opentelemetry]
---

# How LiveScript sends the proxy X-App-Id header

> [!danger] Superseded at the root — 2026-08-19, identification moves to URL path
> [[2026-08-19 Identify proxy apps by URL path, not header]]: the proxy will
> identify the calling app by the **URL path** it's configured to hit (e.g.
> `proxy.livemode.com/livescript`), not by a header. Decided specifically
> *because* of the problem this page documents — a header can't be relied on
> to survive SDK transport, but a path can, since it's part of the same
> `endpointUrl` config every app already sets.
>
> That retires the reason this page exists: the entire options comparison
> below (`pnpm patch` / `customHeaders`-only / REST migration / SDK upgrade /
> `node-fetch` interceptor) was about getting a header onto SDK traffic. None
> of it is needed if identification doesn't depend on a header at all. Kept
> below as a record of what was investigated and why it was hard — not as a
> live decision to make. **Not yet implemented** — the proxy still enforces
> `X-App-Id` as described below until the path-based routing ships.

Question raised 2026-08-17: the [[Airtable Proxy]] now enforces app
identification — every proxied request must carry `X-App-Id` (LiveScript's id is
`livescript`), and without it the proxy returns **401**. *Can [[LiveScript]]
actually attach that header, given the Airtable SDK it uses?*

Investigated against the installed `airtable` SDK (v0.12.2) and the app's
monitoring layer. Filed as a comment on the proxy epic
[AIRTABLEGC-5](https://livemode-projetos.atlassian.net/browse/AIRTABLEGC-5?focusedCommentId=10085)
("Auth + multi-app"). Citations below are to the LiveScript repo
(`livemode-roteiros-nextjs`) and the installed SDK under `node_modules/airtable/`.

## Short answer

Not the easy way. `AIRTABLE_ENDPOINT_URL` repoints traffic at the proxy (the SDK
reads it natively — see [[Proxy Environments]]), but **the header cannot ride on
the SDK config in v0.12.2** as shipped, and the app's monitoring layer only set
the header on the REST path. Getting `X-App-Id` onto *all* traffic needed a code
change.

> [!warning] Reverted 2026-08-18 — no longer shipped
> Commits `754896b` (SDK) and `d565c26` (REST) were dropped via
> `git reset --hard c9cc711` on `feature/airtable-proxy-observability`, undoing
> both the `pnpm patch` and the REST-path header injection, at msilva's direction
> — in order to bring Luís actual alternatives instead of a fait accompli, per
> [[2026-08-18 Bring options to Luís before deciding, communicate async and often]].
> The [Resolution](#resolution--implemented-2026-08-17) section below is now a
> record of **what was tried and undone**, not current state. See
> [Options for Luís — 2026-08-18](#options-for-luís--2026-08-18) for the live
> comparison, including Luís's own suggestion.

## Findings (verified in code)

### 1. The 401 message is a red herring
On any 401, the SDK synthesizes `AirtableError('AUTHENTICATION_REQUIRED', 'You
should provide valid api key…')` **from the status code alone, ignoring the
response body** (`node_modules/airtable/lib/base.js:119-121`). So "missing
`X-App-Id`" → 401 → that misleading "provide valid api key" message. Don't chase
the API key when you see it.

### 2. `customHeaders` does NOT work in v0.12.2 for anything the app uses
The SDK has two internal transports with different header behaviour:

| Path | Merges `_customHeaders`? | Used by |
|---|---|---|
| `base.makeRequest()` | yes (`base.js:44`) | nothing in the app |
| `base.runAction()` → `run_action.js` | **no** — headers hardcoded (`run_action.js:13-27`) | **everything** |

Every operation the app calls routes through the **deprecated `runAction`**:
`.select()` (`query.js:143`), `.find/.create/.update/.destroy`
(`table.js:87,113,142`), record `.fetch/.save/.destroy` (`record.js:58-95`). The
app uses no `makeRequest`/`customHeaders` anywhere. ⇒
`new Airtable({ customHeaders: { 'X-App-Id': 'livescript' } })` would attach the
header to **no real traffic** in this version.

Upstream source confirming this (verified 2026-08-17):
- `runAction` builds a hardcoded header block and never merges `customHeaders` —
  [src/run_action.ts](https://github.com/Airtable/airtable.js/blob/master/src/run_action.ts)
  (header block ~L33-44).
- `makeRequest` is the only path that merges them —
  [src/base.ts](https://github.com/Airtable/airtable.js/blob/master/src/base.ts)
  (`_customHeaders`, mirrored at `base.js:44` locally).
- `customHeaders` is accepted as a constructor option, which is why it *looks*
  usable — [src/airtable.ts](https://github.com/Airtable/airtable.js/blob/master/src/airtable.ts).
- **No official doc states this limitation.** The README advertises
  `customHeaders` without noting it's inert on the `runAction` path; the source
  above is the only authoritative evidence.

### 3. The monitoring layer only half-covers
- `createMonitoredAirtableBase()` (`lib/services/airtable-monitoring.ts:481`) is a
  **metrics-only Proxy**: it wraps methods for `withAirtableMonitoring` then
  delegates to the SDK (`value.apply(...)`); it never touches the outbound HTTP
  request. There is **no SDK header hook** there.
- `fetchAirtableWithMonitoring()` (same file, `:598`) is a real `fetch`, so
  `init.headers['X-App-Id']` works — but it covers only the ~10 hand-built REST
  calls, not the SDK traffic.

### 4. The SDK uses its own `node-fetch`
`node_modules/airtable/lib/fetch.js:6` imports `node-fetch`, not the global
`fetch`, server-side. So patching `globalThis.fetch` would **not** intercept the
SDK path in Node.

## Resolution — implemented 2026-08-17, reverted 2026-08-18

Shipped as a **client-only patch**, the lowest-risk path at the time. Two
commits on `feature/airtable-proxy-observability` in `livemode-roteiros-nextjs`
— **reverted 2026-08-18**, see the warning callout above:

### SDK path — `754896b`
- **`pnpm patch` on `airtable@0.12.2`** (`patches/airtable@0.12.2.patch`) makes
  the deprecated `runAction` merge `base._airtable._customHeaders` after its
  hardcoded defaults — the same thing `makeRequest` already did. 3-line generic
  fix; applied natively by pnpm on install (no postinstall hook), pinned to
  0.12.2 so it fails loud on a version bump.
- **`createMonitoredAirtableBase()`** sets
  `customHeaders: { "X-App-Id": "livescript" }` on the base, so all
  `select/find/create/update/destroy` traffic now carries it.
- **`AIRTABLE_APP_ID`** lives in `lib/services/airtable-monitoring.ts` (its only
  consumer), **not** in `airtable-constants.ts`. That module runs `requireEnv()`
  at load; importing it into monitoring made every monitoring test suite demand
  Airtable env and broke `pnpm test` (→ `pnpm build`, since build runs tests).
  A code review caught this; the constant was moved out to fix it.

### REST path — `d565c26`
- **`fetchAirtableWithMonitoring()`** injects `X-App-Id` on the one chokepoint
  all REST calls funnel through (set only if the caller didn't already; merged
  into a fresh `Headers` so the original `init` is untouched).
- **`airtableApiBaseUrl()`** = `AIRTABLE_ENDPOINT_URL || api.airtable.com`,
  mirroring the SDK. Applied to the **data-plane** REST calls
  (`getEventsPaginated`, script record ops) so the flip routes both transports
  together. No-op until the env is set.
- **Metadata API** calls (`/v0/meta/...`) are pinned to
  `AIRTABLE_METADATA_BASE_URL` (Airtable direct) on purpose — see open question 2
  below.

The header is inert against `api.airtable.com`, so both commits are safe to ship
**before** `AIRTABLE_ENDPOINT_URL` flips.

## Options for Luís — 2026-08-18

Written to bring to Luís as an actual comparison, per
[[2026-08-18 Bring options to Luís before deciding, communicate async and often]].
Nothing here is decided; this replaces the "shipped it, tell him after" pattern
that triggered that decision.

### Option 1 — `pnpm patch` + `customHeaders` (what was shipped, then reverted)
The original fix: patch `run_action.js` (3 lines) to merge `_customHeaders`,
same as `base.makeRequest()` already does, then set
`customHeaders: { "X-App-Id": "livescript" }` on the monitored base.
- **Pro**: small, mechanical, pinned to the exact installed version
  (`airtable@0.12.2`) so a version bump fails the install loudly instead of
  silently dropping the fix. Covers 100% of SDK traffic in one place.
- **Con**: it's a `pnpm patch`/`patch-package`-class tool, which is what Luís
  said he doesn't trust (*"eu confio zero nisso... para mim é má prática de
  comunidade JavaScript"*). Every `pnpm install` re-applies a diff against
  vendor code that could silently stop matching upstream on any dependency
  change.

### Option 2 — `customHeaders` only, no patch (Luís's suggestion, 2026-08-18)
Just the constructor option, no patch:
```js
const rawBase = new Airtable({
  apiKey: config.apiKey,
  endpointUrl: AIRTABLE_ENDPOINT_URL,
  customHeaders: { "X-App-Id": AIRTABLE_APP_ID },
}).base(config.baseId);
```
- **Verified insufficient as-is.** In the installed `airtable@0.12.2`,
  `customHeaders` is only merged by `base.makeRequest()`
  (`node_modules/airtable/lib/base.js:44`). Every operation this app actually
  calls — `.select()`, `.find()`, `.create()`, `.update()`, `.destroy()` —
  routes through the deprecated `runAction` (`run_action.js`), which builds a
  hardcoded header object and **never reads `_customHeaders`** — confirmed by
  grep, zero references. That's ~13 service files and several dozen call sites
  (`airtable-helpers.ts`, `airtable.service.ts`, `config.service.ts`,
  `grupos.service.ts`, `inventory.service.ts`, `migration.service.ts`,
  `narrator.service.ts`, `roteiro-reset.service.ts`, `script-copy-data.ts`,
  `script-generation-data.ts`, `script-generation.service.ts`,
  `composite-order-migration.ts`).
- **The risk if shipped as-is**: no error, no warning — it just silently fails
  to attach `X-App-Id` to nearly all Airtable traffic. It would look correct in
  code review and in dev (header is inert against `api.airtable.com` either
  way) and only 401 once `AIRTABLE_ENDPOINT_URL` actually flips to the proxy.
- Does *not* need the patch **only** for the ~10 hand-built REST calls
  (`fetchAirtableWithMonitoring` already sets headers via real `fetch()`,
  unaffected by any of this).

### Option 3 — migrate the remaining SDK calls to hand-rolled REST
Replace `.select/.find/.create/.update/.destroy` call sites with the same
`fetch()`-based pattern already used for the ~10 REST calls, so the app stops
calling into `runAction` at all. No patch, no `customHeaders` gap — every
header is under direct control.
- **Pro**: the "clean, no-vendor-patch" option Luís is looking for, and reduces
  the SDK's role to the parts it isn't in the way of.
- **Con**: real migration effort — the same ~13 files / several dozen call
  sites listed above, not a 3-line fix. Higher risk of behavior drift (pagination,
  retry/backoff, error shapes) since `airtable-monitoring.ts`'s retry-with-backoff
  logic was itself built to wrap the SDK.

### Option 4 — upgrade the `airtable` SDK
Re-checked 2026-08-18 (msilva couldn't reach Airtable's GitHub on 2026-08-17):
`npm view airtable dist-tags` still reports **`latest: 0.12.2`** — same as
before, no newer release exists. **Still not a real option.**

### Option 5 — one `node-fetch` interceptor at server startup
Covers SDK + REST in one place, and is the same seam the OTel
`traceparent`/`baggage` propagation gap needs. Rejected as fragile: it's a
monkeypatch on SDK-internal `require("./fetch")` wiring that fails silently if
Next.js re-aliases the module in the server bundle.

**Where this leaves it**: Option 2 alone is not viable (it doesn't work, not
just "Luís might not like it"). The real choice is Option 1 (small patch,
tooling Luís distrusts) vs. Option 3 (no patch, real migration cost) vs.
Option 5 (fragile, not recommended). Bringing exactly this to Luís, not a
re-ship of Option 1.

## Corrections to the recorded onboarding model

The [[Airtable Proxy]] page (points 2 & 4, taken from the design doc §4/§11) says
apps add headers `X-App-Id` **+ `X-Api-Key`** and **drop the PAT**. Per GC-5 and
the SDK, both need qualifying:

- **`X-Api-Key` is deferred** — only `X-App-Id` is required right now (the
  per-app key was postponed).
- **The app cannot drop the PAT.** The SDK always sends `Authorization: Bearer
  <apiKey>` (`run_action.js:14`) and refuses to start without a non-empty apiKey
  (`airtable.js:39-41`). So the proxy must **overwrite/ignore** the incoming
  `Authorization`, and the app needs a **dummy `AIRTABLE_API_KEY`**, not an empty
  one.

> [!warning] Luís is uneasy about the `pnpm patch`, raised after the fact — 2026-08-18
> Hearing the detail of this fix for the first time in
> [[2026-08-18 1-1 Matheus - Luís]], Luís pushed back on two separate fronts:
>
> 1. **Process**: this was shipped without being brought to him as a decision with
>    alternatives. See [[2026-08-18 Bring options to Luís before deciding, communicate async and often]].
> 2. **Substance**: *"eu confio zero nisso"* about `pnpm patch`/`patch-package` as a
>    class of tool — *"para mim é má prática de comunidade JavaScript."* Not a
>    rejection, but a request to confirm there's no cleaner native option before
>    keeping it: does the `airtable` SDK support redirecting to a custom endpoint
>    without patching? msilva tried to check Airtable's GitHub the day before but it
>    was down.
>
> **Update 2026-08-18**: no longer "the patch is still what's shipped" — it was
> reverted (see the top of this page) specifically so this could be brought to
> Luís properly. His "cleaner native option" question is answered: `endpointUrl`
> is native, `customHeaders` looks native but is a no-op for this app's traffic
> without the patch — see [Options for Luís](#options-for-luís--2026-08-18).

## Open questions for the proxy (gate the endpoint flip)

Both must be answered by the proxy owner before `AIRTABLE_ENDPOINT_URL` flips:

1. **Does the proxy overwrite/ignore the incoming `Authorization`?** Required for
   the dummy-PAT approach to be safe. If it does, the data plane runs on a
   throwaway key; if not, the app must keep a real PAT on the data plane too.
2. **Does the proxy serve the Metadata API** (`/v0/meta/bases/.../tables`) or only
   the data plane (`/v0/{baseId}/...`)? Until this is confirmed, metadata calls
   are **pinned to Airtable direct** (`AIRTABLE_METADATA_BASE_URL`), which also
   means those calls **still need a real PAT** even after the flip. If the proxy
   does serve `/v0/meta`, point `AIRTABLE_METADATA_BASE_URL` at
   `airtableApiBaseUrl()` to route them too.

## Rollout

Ship the header **before** flipping `AIRTABLE_ENDPOINT_URL` to the proxy — or
every proxied call 401s. See the incomplete "uncomment one variable" note in
[[Proxy Environments]].
