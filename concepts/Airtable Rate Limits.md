---
type: concept
status: active
updated: 2026-08-17
aliases: [429, rate limit, rate limiting, limite de uso]
tags: [airtable, rate-limits, http]
---

# Airtable Rate Limits

The constraint the entire [[Airtable Proxy]] programme exists to manage.

## The limit

- **5 requests per second, per base.**
- Exceeding it returns **HTTP 429**, followed by a lockout of roughly **30
  seconds**.
- The 429 response carries a **`Retry-After`** header, so backoff can be driven
  by the API rather than guessed.

Airtable is an API with usage limits, **not a database** — you can't write
directly to it, and it constrains callers in ways a database wouldn't. That
distinction is the root of the problem: [[LiveScript]] needs real-time
collaborative behaviour on top of a backend that can't sustain it.

## How the limit is felt in practice

Load isn't uniform — it spikes with real-world events. The
**World Cup** produced a day of concurrent usage that broke through the limit
and triggered the whole initiative
([[2026-08-10 Onboarding Técnico - Matheus]]).

Two different causes produce the same 429, and they need opposite fixes:

| Cause | Fix |
|---|---|
| Duplicate/wasteful reads — refetching what's already known | Caching, or fixing the caller |
| Sustained legitimate volume | Rate limiting, queueing, or reducing demand |

**Which one is actually happening is unknown**, and finding out is the entire
point of the observability-first approach in [[Airtable Proxy]]. The "Airtable
Usage & Anti-patterns" dashboard exists to separate these two.

## Current handling

- **Applications work around the limit by restricting users** — the *travas* in
  [[LiveScript]]: whole-row locks, single-creator constraints.
- **The proxy is intended to absorb 429s** with retry/backoff so applications
  don't each implement their own. Stated as intent, not yet built — see the
  open tension in [[Airtable Proxy]].
- **Headroom against 5 req/s is already instrumented** as a dashboard panel, and
  `airtable_429_alert` fires on any 429 in a 5-minute window.

## Related

- Attachment URLs in record payloads expire in ~2 hours, which is why record data
  can't simply be cached to reduce request volume — [[Airtable Proxy]].
