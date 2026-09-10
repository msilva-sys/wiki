---
type: decision
status: active
updated: 2026-09-10
date: 2026-09-10
aliases: [days_overdue, pr_open_days, health via skill, retrofit A14]
tags: [agent-flow, agents, a14, harness, skill, criteria]
---

# A14 — retrofit dos verdicts para skill (mesmo princípio do A10)

## Origem

msilva, 2026-09-10, mesma sessão de código que fechou
[[2026-09-10 A10 - critérios julgados por skill, não determinísticos em Python]].
Ao desenvolver o lado A14 das anotações da reunião com Luís com a mesma
profundidade dada ao A10, msilva pediu que o A14 seguisse o mesmo princípio:
métricas determinísticas + skills que as interpretam, em vez de threshold
hardcoded em Python.

## Decisão

Mesmo princípio do A10, aplicado aos três verdicts que `a14/rules.py` hoje
decide sozinho: **Python só calcula o número, nunca decide se ele é grave o
suficiente pra virar alerta.**

## Os três retrofits

**`detect_delays` → `Alert` deixa de ser decisão de Python.** Hoje já filtra
(só devolve milestone atrasado) e `run_a14` (`a14/agent.py:67,138`) calcula
isso **antes** do `invoke()` e **sobrescreve** `report.alerts` depois — o
que o LLM produzisse ali era descartado. Vira: `progress_by_milestone`
ganha `days_overdue` por milestone (positivo = atrasado, `None` sem
`target_date`, mesma lógica de "sem baliza" que `compute_adherence` já
usa), sempre exposto, nunca filtrado. `Alert` (mesma forma de hoje) passa a
ser preenchido pelo LLM, guiado por skill.

**`detect_code_signals` → `pr_open_days` cru, sem filtro de 7 dias.**
`stale_days: int = 7` (`a14/rules.py:28`) hoje decide em Python se o sinal
existe — PR aberto há 6 dias nunca chega ao LLM. Vira: Python expõe
`pr_open_days` de todo PR aberto vinculado, sem cortar; skill decide o que
vira `CodeSignal` de `pr_aberto_ha_muito_tempo`. `concluida_sem_merge` fica
como está — fato binário sem número pra calibrar (não tem "quão aberto"),
mesmo tratamento que fatos sem threshold já receberam na decisão do A10.

**`compute_health` → LLM escolhe, guiado por skill.**
`OFFTRACK_OVERDUE_MILESTONE_THRESHOLD = 2` (`a14/rules.py:150`) é chute de
severidade, hoje calculado **depois** do relatório fechado, em `cron.py`
— nem participa da saída do LLM. Vira: `A14Report` ganha campo `health`
preenchido pelo próprio LLM (mesmo enum fixo `onTrack`/`atRisk`/`offTrack`,
o que o Linear espera no status update), a partir de `days_overdue`/
`pr_open_days` crus. `cron.py` para de chamar `compute_health()`, só lê
`report.health`.

## Consequência estrutural

O núcleo da mudança é tirar de `a14/agent.py::run_a14` as duas linhas que
computam alerts/code_signals antes do invoke e as duas que sobrescrevem
depois (`:66-68`, `:138-139`) — hoje 100% determinístico (LLM nem
participa), passa a ser 100% julgamento do LLM, com Python só alimentando
número cru via `calculate_progress` (tool já existente, só precisa carregar
`days_overdue`/`pr_open_days`).

## O que fica de fora, deliberadamente

`progress_by_milestone` (razão completed/total) e `compare_progress` (diff
textual entre rodadas) continuam puros — contagem, não veredito de
gravidade. `compute_adherence` também fica como está — alimenta
`outcomes.py`, lido pelo A10 como sinal de portfólio; precisa ser estável
entre execuções pra isso fazer sentido, diferente de um alerta mostrado
direto pra alguém no relatório.

## Skills — A14 confirma a mesma necessidade que o A10 já tinha aberto

[[2026-09-10 A10 - critérios julgados por skill, não determinísticos em Python]]
já tirava "Skills" do conceitual do molde pro A10; esta decisão confirma que
a mesma tabela `skills` serve os dois agentes, com critérios próprios de
cada um (A14: julgar health, julgar PR parado, julgar gravidade de atraso —
diferente dos critérios do A10).

**Nenhuma implementação foi autorizada nesta sessão — só o desenho.**

## Relacionado

- [[2026-09-10 A10 - critérios julgados por skill, não determinísticos em Python]]
- [[Como deve funcionar o molde de agente]] — seção "Slot Skills"
- [[Fronteira A10×A14 (informação e métricas)]]
- [[Notas PM vs Portfólio (pré-1-1 Luís)]]
- [[Agent Flow]]
