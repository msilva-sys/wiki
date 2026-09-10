---
type: decision
status: active
updated: 2026-09-10
date: 2026-09-10
aliases: [blocked_by, is_blocked, dependência A14, sequência de entrega]
tags: [agent-flow, agents, a14, harness, skill, metrics, linear]
---

# A14 — dependência e sequência (blocked_by)

## Origem

msilva, 2026-09-10, mesma sessão que fechou
[[2026-09-10 A14 - vazão e lead time (novo dado de fonte)]]. Segundo gap
identificado ao comparar o framework do Luís ("escopo, **sequência**,
**dependências**, prazo") contra `a14/rules.py`/`tools.py` reais.

## Achado antes de desenhar: tool promete o que o dado não sustenta

`list_issues` (`a14/tools.py:36`) já diz no docstring "use pra responder...
o que bloqueia o quê" — mas o retorno não tem relação de bloqueio nenhuma,
só `parent_identifier` (subtask, não `blocks`/`blocked by`). O tool promete
uma capacidade que o dado não entrega hoje.

## Decisão

**Gap de fonte, não só de regra** — mesma categoria de
[[2026-09-10 A14 - vazão e lead time (novo dado de fonte)]]. `linear_client.py`
passa a pedir relações de issue do Linear (`relations`/`inverseRelations`,
tipo `blocks`); `domain.py::Issue` ganha `blocked_by: list[str]` (ids crus
das issues que bloqueiam esta).

**Número em Python — só fato, sem threshold:**

- `is_blocked(issue, all_issues)` — `blocked_by` não vazio E pelo menos um
  bloqueador ainda ativo. Fato objetivo (bloqueado agora ou não), mesma
  categoria de `concluida_sem_merge` — fica em Python, nenhuma skill
  decide isso.

**Skill decide**: sequenciamento (em que ordem entregar, dado o grafo de
bloqueio) e gravidade de uma cadeia de bloqueio longa.

## Fora de escopo, deliberadamente

**"Tempo bloqueado" (Painel A14)** — exigiria saber *desde quando* está
bloqueado; Linear não expõe isso como campo, só o estado atual da relação.
Precisaria de tracking próprio ao longo do tempo (memória nova, não só
leitura pontual) — mesma categoria dos gaps já deferidos (`effect`/
`adoption` em `outcomes.py`: "precisa de sinal que hoje não existe"). Não
desenhado agora.

**"Mudança de escopo após o aceite" (Painel A14)** — cogitado na mesma
sessão (baseline de composição de milestone, memória nova), **deixado de
lado por decisão explícita de msilva**, não por falta de desenho. Fica
pra retomar depois.

**Nenhuma implementação foi autorizada nesta sessão — só o desenho.**

## Relacionado

- [[2026-09-10 A14 - vazão e lead time (novo dado de fonte)]]
- [[2026-09-10 A14 - retrofit dos verdicts para skill (mesmo princípio do A10)]]
- [[Fronteira A10×A14 (informação e métricas)]]
- [[Agent Flow]]
