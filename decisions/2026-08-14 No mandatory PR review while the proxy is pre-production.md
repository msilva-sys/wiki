---
type: decision
status: stale
updated: 2026-10-19
date: 2026-08-14
decided_by: Luís Fernandez, Gabrielle Ferreira
source: "[[2026-08-14 1-1 Matheus - Gabrielle]]"
tags: [process, code-review, git]
---

# No mandatory PR review while the proxy is pre-production

**Decision.** msilva opens and merges his own PRs on the [[Airtable Proxy]]
without waiting for approval. Luís reviews the commit history afterwards rather
than gating each change.

> [!note] Corrected 2026-08-17
> This page previously claimed the decision was confirmed independently at the
> team recap. It wasn't. The recap describes a **separate** de-bureaucratization
> of review on the **Farol** project, where the reviewer was an AI in a fresh
> session rather than Luís — see [[2026-08-14 Recap da Semana]]. Same direction
> of travel, different instance. The only source for *this* decision is the 1:1.

## Why

- **The proxy is not in production.** Low blast radius if something isn't done
  the ideal way the first time.
- **Approval gating was creating a bottleneck.** The same arrangement was made
  with another developer, where waiting on Luís's availability was blocking her.
- **It generates learning** for whoever is building.

The area is moving this way generally: the parallel change on Farol removed
formal PRs during active development for the same reason — cycle time — and the
**governance documentation is being rewritten** to stop mandating an approval
step nobody is performing.

## Scope and limits

- Applies to the **proxy while pre-production**. It is *not* a general statement
  about production repositories, and nothing was said about what changes when the
  proxy ships.
- Luís still reviews commit history retrospectively — this is deferred review,
  **not absent review**.
- Corrections after the fact are expected and accepted as normal.

## Consequences

- Commit messages and history carry the review burden. They're the artifact Luís
  actually reads.
- [[Agent Flow]] sits even further toward autonomy — described as msilva's own
  project from scratch, with Luís as consultant rather than validator.

## Open questions

- **O que substitui isto agora que o proxy está em produção?** Continua
  indefinido, mas deixou de ser hipotético — ver a nota de premissa vencida
  no fim da página.

> [!note] Concrete instance, 2026-08-24
> [[2026-08-24 1-1 Matheus - Luís]]: rather than reading a diff, Luís reviewed
> a piece of proxy work by watching msilva do a **live walkthrough** of the
> metrics actually being captured in Grafana — explicitly to avoid the
> overhead of standing up the environment himself. Same underlying decision
> (deferred, not absent review), a different mechanism than "read the commit
> history" described above. msilva also confirmed he still isn't opening PRs
> for this branch of work, for the same pre-production reasoning.

> [!note] Premissa vencida, 2026-10-19 (lint)
> As duas condições que sustentavam esta decisão caíram, e nada foi decidido
> em substituição.
>
> **(1) O proxy não é mais pre-production.** Está em produção no Cloud Run
> desde 2026-09-14 ([[Airtable Proxy]]). A cláusula que dá nome à página
> descreve um estado que acabou há mais de um mês.
>
> **(2) O revisor nomeado aqui está de saída.** A revisão descrita — Luís
> lendo o histórico de commits depois do merge — depende de alguém que está
> deixando a empresa, com a transição já em curso desde 2026-10-07
> ([[2026-10-07 Divisão de projetos na transição de Luís]]). msilva é o dono
> único do proxy desde então.
>
> Na prática a decisão continua valendo por inércia, não por escolha: os PRs
> seguem sendo abertos e mergeados pelo próprio autor, agora sem a revisão a
> jusante que era a contrapartida do acordo. `status` movido para `stale`
> até que exista uma decisão sobre o que vale em produção.
