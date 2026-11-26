---
type: decision
status: stable
date: 2026-11-25
updated: 2026-11-26
aliases: [ADR 0057, estágio do projeto, memória do A10, PRO-1672]
tags: [agent-flow, liveagents, a10, a14, memory]
---

# 2026-11-25 Estágio do projeto e retrato do portfólio na memória do A10

O estágio declarado de projeto e de iniciativa passa a ter um dono, o **A10**, e mora na
memória dele. O retrato do portfólio e o report salvo de cada produto também saem das
tabelas próprias do A10 e viram registros da memória comum.

Fonte: **ADR 0057**, aceita em 2026-11-25
([PRO-1672](https://linear.app/projetos-livemode/issue/PRO-1672), `Done`), em
`docs/decisions/` do `livemode-fluxo-agentico`. Substitui em parte a ADR 0043 (item 1) e
a 0045, quanto ao lugar onde o retrato e o report moram, e detalha o item 12 da ADR 0053.

## Por que

- O estágio ficava numa tabela que os dois agentes gravavam: o A14 pela tela do projeto
  e o A10 pela sincronização com o Linear. A memória comum só aceita gravação do dono
  (ADR 0053, item 12), então o estágio precisava de um dono.
- O retrato do portfólio (ADR 0043) e o report salvo (ADR 0045) moravam numa tabela do
  A10, contra o item 5 da ADR 0021.

## O que ficou decidido

1. **O estágio é memória do A10.** A tela do projeto e a sincronização com o Linear
   gravam pelo A10, com o Linear primeiro. O A14 só lê, pela memória do A10. O formato
   do registro fica no código comum aos agentes, para um agente não importar o outro.
2. **O retrato do portfólio e o report salvo são registros da memória do A10**, no
   instante em que foram tirados.

## Alternativas recusadas

- Estágio com o A14: quem mais grava e lê o estágio é o A10 (sincronização com o Linear
  e visão do portfólio). O A14 só lê.
- Estágio numa tabela comum, fora da memória: é declaração de uma pessoa, com autor e
  data, e apagá-lo perde conhecimento (ADR 0053, item 11).

## Consequências

- A rota de declarar o estágio sai da API do A14 e vai para a do A10.
- As tabelas antigas ficam sem leitor e saem na PRO-1676. Uma migration de 2026-11-25
  copia estágios, último update e retrato para a memória.

## Relacionado

[[Agent Flow]], [[2026-11-25 O Slack vira canal de conversa comum aos agentes]] (a mesma
leva de mudanças leva o intake e o A10 para a memória comum). A ADR 0053, a memória
permanente em quatro operações, ainda não tem página no wiki.
