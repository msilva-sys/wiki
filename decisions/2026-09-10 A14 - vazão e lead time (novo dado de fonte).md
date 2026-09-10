---
type: decision
status: active
updated: 2026-09-10
date: 2026-09-10
aliases: [vazão, lead time, throughput_by_week, completedAt, startedAt]
tags: [agent-flow, agents, a14, harness, skill, metrics, linear]
---

# A14 — vazão e lead time (novo dado de fonte)

## Origem

msilva, 2026-09-10, mesma sessão de código que fechou
[[2026-09-10 A14 - retrofit dos verdicts para skill (mesmo princípio do A10)]].
Ao comparar as anotações da reunião com Luís ("métrica: issues entregues,
volume, **vazão**") contra `a14/rules.py` real, achamos que nada hoje mede
taxa — só razão estática (`completed/total`) e diff textual entre rodadas.

## Diferença em relação aos outros dois gaps já identificados

Dependência/sequência e mudança de escopo são gaps de **regra** — o dado
já existe ou é fácil de derivar do que já se lê do Linear. **Vazão é gap de
fonte** — não dá pra construir só reorganizando dado disponível.

## O gap real, auditado no código

`linear_client.py:130-138,205-213` só pede `createdAt`/`updatedAt` do
Linear; `domain.py::Issue` não tem `completed_at`/`started_at`. Linear
expõe os dois nativamente no schema (`completedAt`/`startedAt` são campos
padrão de Issue), nunca foram puxados aqui. Sem isso: não existe vazão
(quando uma issue foi concluída) nem lead time (tempo do início à entrega).
`updated_at` não serve de substituto — muda por qualquer edição, não só
conclusão.

## Decisão

**Fonte**: `linear_client.py` passa a pedir `completedAt`/`startedAt`
também; `domain.py::Issue` ganha os dois campos, opcionais (issue ativa não
tem `completed_at`; issue que nunca saiu de backlog não tem `started_at`).

**Número em Python — mesmo princípio do resto da sessão, só contagem/
duração, nunca veredito:**

- `throughput_by_week(issues, weeks=4)` — quantas issues com `completed_at`
  caem em cada semana das últimas N, por milestone/projeto. Puro count,
  mesmo estilo de `progress_by_milestone`.
- `lead_time_days(issue)` — `completed_at - started_at`; sem `started_at`,
  cai pra `created_at`, mesma ressalva de "sem baliza" que
  `compute_adherence` já usa pra `target_date` ausente. Agregação
  (média/mediana por milestone) continua em Python — é aritmética, não
  veredito.

**Skill decide**: se a vazão caindo é motivo de alerta (podia ser férias,
planejamento, ou problema real) e se o lead time está dentro do esperado —
julgamento, não cálculo. Mesmo princípio de
[[2026-09-10 A14 - retrofit dos verdicts para skill (mesmo princípio do A10)]]
e [[2026-09-10 A10 - critérios julgados por skill, não determinísticos em Python]].

**Nenhuma implementação foi autorizada nesta sessão — só o desenho.**

## Em aberto, não desenhado ainda

Dependência/sequência (leitura de `blockedBy` do Linear) e mudança de
escopo (comparar composição de um milestone entre execuções) — gaps de
regra, ficam pra uma próxima rodada de desenho.

## Relacionado

- [[2026-09-10 A14 - retrofit dos verdicts para skill (mesmo princípio do A10)]]
- [[2026-09-10 A10 - critérios julgados por skill, não determinísticos em Python]]
- [[Fronteira A10×A14 (informação e métricas)]]
- [[Notas PM vs Portfólio (pré-1-1 Luís)]]
- [[Agent Flow]]
