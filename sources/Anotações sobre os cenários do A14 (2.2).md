---
type: source
status: stable
updated: 2026-10-22
date: 2026-10-06
source: "raw/Anotações sobre cenarios-a14-2.2-alinhar-escopo.md.md"
aliases: [cenários A14 2.2, alinhar escopo, anotações cenários A14]
tags: [agent-flow, a14, cenarios, milestone, escopo]
---

# Anotações sobre os cenários do A14 (2.2)

Perguntas do msilva sobre o doc `cenarios-a14-2.2-alinhar-escopo.md`
(compartilhado no Slack), que detalha por cenários a responsabilidade
**2.2 Alinhar escopo** do A14. Raw:
`raw/Anotações sobre cenarios-a14-2.2-alinhar-escopo.md.md`. *(unverified:
2026-10-06 vem do mtime do arquivo.)* Vem logo depois da conversa com a
Carolina em [[2026-10-05 Carolina - Matheus (feedback reports A14)]].

O doc está versionado no repo de agentes
(`docs/roadmap/cenarios-a14-2.2-alinhar-escopo.md`). O commit `3e6909c`
(2026-10-07) diz *"2.2 atualizada com as respostas do Matheus de 07/10"*:
as perguntas abaixo foram respondidas no próprio doc um dia depois.

## Perguntas e o que o doc responde hoje

| Pergunta do msilva | Resposta no doc (lido em 2026-10-22) |
|---|---|
| A unidade central do A14 é milestone? | Sim. "Escopo combinado" é o trio objetivo, trabalho e data de um **milestone**; trabalho são as issues-folha (a issue-mãe agrupa, não soma) |
| Manter log próprio, já que o Linear sobrescreve? | Sim: V1 compara **registros diários** com o da segunda anterior. No código, `c744e6f` (2026-10-20): "o agente passa a gravar o que viu em cada dia" |
| Como medir o ritmo real do time? | Pontos restantes das issues-folha divididos pelo ritmo das **últimas 3 semanas** (V9). Repactuar data zera a medição |
| O que significam as condições (V1 = 1, V9 = cabe ou justo)? | Variáveis com faixas, seção 3 do doc. V1 = issues que entraram em milestone já iniciado (0 · 1 · 2–3 · 4+, ou 25%+ em milestone pequeno). V9 = escopo restante contra tempo restante (cabe com folga até 80% · justo 80–100% · não cabe 100–150% · muito fora 150%+) |
| Limiares — "não entendi" | São os cortes que separam um registro silencioso de um alerta: silêncio com 1 issue entrando; alerta a partir de 2 issues ou 25%; 50% no cenário de escopo sem definição |

Também anotado: o agente precisa ser **propositivo, não só expositivo** —
mesmo ponto que a Carolina levantou em 2026-10-05. O doc fixa a estrutura
de toda resposta da 2.2: milestone, fato com números, por que importa, até
3 ações sugeridas e uma pergunta para quem toca o projeto.

## Relacionado

- [[Agent Flow]]
- [[2026-09-10 A14 - vazão e lead time (novo dado de fonte)]] — a fonte de
  dado (`completedAt`/`startedAt`) por trás do ritmo.
