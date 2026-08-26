---
type: decision
status: active
updated: 2026-08-26
date: 2026-08-10
decided_by: Gabrielle Ferreira, with msilva
source: "[[2026-08-10 Onboarding Técnico - Matheus]]"
tags: [onboarding, planning]
---

# Onboarding runs proxy and agent flow in parallel

**Decision.** msilva works the [[Airtable Proxy]] as the primary track, and picks
up [[Agent Flow]] alongside it for context. The proxy is expected to finish fast
— *"uma, duas semanas"*. The agent work carries **no delivery pressure**:
*"a gente tem que entregar isso aqui em duas, três semanas"* was explicitly not
the expectation.

**Escape hatch, agreed in the meeting.** If running both proves too complex,
pause one and finish the other: *"se você perceber que está ficando complexo
ficar executando um e aprofundando no outro, dá uma pausa [em] só um, termina
logo."*

## Why

The two tracks were weighed against each other:

| | [[Airtable Proxy]] | [[Agent Flow]] |
|---|---|---|
| Value | Concrete, scoped | Judged **higher** overall |
| Traction | Fast — *"bem straight forward"* | Slower, more complex to show progress |
| Readiness | Documentation prepared, structure in place, a foundation to build on | An initial drawing only |
| Dependency on Gabrielle | Low — can advance independently | High — needs much closer contact |

Three reasons parallel beat sequential:

1. **msilva prefers to absorb context through a project rather than studying
   processes first** — asked directly in the meeting, and answered that way.
2. **The two give different context.** The proxy teaches a system; the agent flow
   teaches the company and the area. Gabrielle: *"são contextos diferentes."*
3. **Avoiding a bottleneck.** The proxy is the track msilva can push without
   Gabrielle in the room — *"para também não travar ele 100% em você."*

## Consequences

- The proxy's open telemetry work is the immediate priority — see
  [[Airtable Proxy]].
- The agent track's near-term output is understanding plus one built agent, not
  a delivered system.
- A separate onboarding idea was floated but **not settled**: passing through
  three projects quickly end-to-end — e.g. add a button in [[LiveScript]], carry
  it through the full process, ship it to production. Whether the proxy counts
  as one of those three is unresolved.

## Status

Active as of 2026-08-17. Revisit if the proxy overruns the one-to-two week
estimate, since the parallel plan assumes it lands quickly.

> [!warning] Revisit trigger fired — 2026-08-26
> The proxy did overrun the 1-2 week estimate — still pre-production as of
> 2026-08-26 (building the IaC/Pulumi slice). Per msilva, in practice the two
> tracks weren't run as strictly primary/secondary either: he alternated
> between the proxy and [[Agent Flow]] week to week, closer to genuinely
> parallel than "proxy first, agent flow for context only." The original
> plan's *shape* (parallel, no delivery pressure on Agent Flow) held; the
> *primary/secondary framing* is the part that didn't match practice.
