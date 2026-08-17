---
type: synthesis
status: active
updated: 2026-08-17
date: 2026-08-17
aliases: [x-app-id, app auth header, livescript proxy auth, GC-5 client side]
tags: [airtable, proxy, auth, sdk, livescript, opentelemetry]
---

# How LiveScript sends the proxy X-App-Id header

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
the SDK config in v0.12.2**, and the app's monitoring layer can only set the
header on the REST path. Getting `X-App-Id` onto *all* traffic needs one of two
changes, **both still pending research** (Option A / Option B below).

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

## Two options to cover all traffic (pending research)

- **Option A — one `node-fetch` interceptor at server startup**
  (`instrumentation.node.ts`, the existing OTel bootstrap). Covers SDK + REST in
  one place, no call-site edits, and is the **same seam** needed for the OTel
  `traceparent`/`baggage` propagation gap. It is a monkeypatch on SDK-internal
  wiring.
  Research: (a) confirm Next.js doesn't alias `node-fetch` in the server bundle;
  (b) confirm the wrap actually intercepts the SDK's `require("./fetch")`;
  (c) scope injection to the proxy origin only.
- **Option B — upgrade the `airtable` SDK** to a version where all operations go
  through `makeRequest` (which honours `customHeaders`); then it's one line on the
  base config.
  Research: (a) find/confirm a version that moved `select/find/…` off the
  deprecated `runAction` — **unconfirmed that one exists in the 0.x line**;
  (b) assess regression across the ~56 SDK call sites.

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

## Open questions for the proxy

1. Does the proxy overwrite/ignore the incoming `Authorization`? (Required for the
   dummy-PAT approach to be safe.)
2. Does the proxy serve the **Metadata API** (`/v0/meta/bases/.../tables`) or only
   the data plane (`/v0/{baseId}/...`)? This decides whether meta calls stay
   pointed at Airtable directly and how narrowly to scope the interceptor.

## Rollout

Ship the header **before** flipping `AIRTABLE_ENDPOINT_URL` to the proxy — or
every proxied call 401s. See the incomplete "uncomment one variable" note in
[[Proxy Environments]].
