---
type: project
status: active
updated: 2026-10-22
aliases: [inventario de processos, mapeamento de processos, inventario de api keys]
tags: [governance, api-keys, finance, cfo]
---

# Inventário de Processos e Ferramentas

Iniciativa liderada por [[Daniel Robillotta]], puxada pelo CFO [[Zoca]] —
mapear todos os processos, projetos e ferramentas/APIs em uso nas áreas da
Livemode, pra reduzir duplicação (times usando as mesmas bases em paralelo,
isolados) e ganhar controle sobre chaves de API esquecidas ou ativas sem
necessidade.

Levantado em
[[2026-10-07 Daniel - Matheus (Inventário de Processos e Governança de Auth)]]
como contraparte paralela ao [[LiveAuth]] — os dois esforços seguem
separados por ora, com plano de se juntar depois.

## O que já existe

- Visualização em grafo (quadrados = projetos, bolinhas = processos), com
  servidor MCP pra um agente consultar os dados.
- Time de Transfer de Patrimônio já usa e atualiza os processos mapeados.
- Overlap a esclarecer com a [[Vitrine de IA]] (projeto da Carolina
  Bezerra) — também um catálogo, mas de soluções/projetos prontos, não de
  processos.

## Motivação concreta (incidentes citados)

- Bot do N8N deixado ligado, acionado sem querer num grupo do Slack.
- Chave da API da OpenAI consumindo crédito sem necessidade — descoberta só
  quando o TI questionou o gasto.

## Dado do n8n, 2026-10-22

[[Mapeamento dos fluxos do n8n]] dá número ao problema de chave solta:
1.256 workflows e 200+ credenciais, todas em projeto pessoal, nenhuma
gerenciada centralmente; só de Airtable, ~30 chaves, várias com nome de
pessoa. Também acha 76 fluxos com nome de teste/cópia, 20 deles ativos — o
mesmo tipo de "bot deixado ligado" citado acima.

## Open questions

- Como isso se conecta ao [[LiveAuth]] — ainda não desenhado, "vai tocando
  constantemente" até decidir.

## Relacionado

- [[Daniel Robillotta]]
- [[LiveAuth]]
- [[Vitrine de IA]]
- [[Mapeamento dos fluxos do n8n]]
