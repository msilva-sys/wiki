---
type: meeting
status: stable
date: 2026-10-07
updated: 2026-10-09
attendees: [Matheus Oliveira da Silva, Luís Fernandez]
aliases: [transição Luís]
tags: [airtable-proxy, agent-flow, liveauth, onboarding, transição]
---

# Matheus / Luís (Transição) — 2026-10-07

> Fonte: Granola (notes.granola.ai/d/f41e9f8e-1a4e-4d73-b040-76e56640cb7f).
> Resumo estruturado, não transcrição verbatim. Transcrição verbatim parcial
> (raw/Matheus _ Luis ( Transição) - 2026_10_07 17_35 GMT-03_00 - Anotações do
> Gemini.md) cobre só os primeiros ~4m30 dos 30min — citada abaixo onde traz
> algo que o resumo Granola não capturou.

## Decisions

- **Divisão de projetos na saída do Luís**: Matheus fica com [[Agent Flow]] e
  [[Airtable Proxy]]. [[Yasmin Macedo]] mantém [[LiveScript]] e [[Farol]].
  Ambos (Matheus e Luís, enquanto presente) compartilham **[[Opta]]**
  (projeto simples — Matheus como guardião técnico/revisor de PR).
  **[[Live Hub]]** fica pro time tocar, sem dono único. Matheus pode
  começar a olhar MCP se sobrar tempo.
- Matheus assume o **[[Onboarding]]** (projeto existente de Luís, repo
  `livemode-onboarding-devs`) como projeto próprio.
- Reunião com a área admin (Luís + Matheus + ela) marcada pra sexta-feira
  (2026-10-09) no fim do dia — apresentar arquitetura do proxy com
  desenhos, mostrar integração com o LiveScript, brainstorm e LiveScript de
  teste.

## Commitments

- Luís vai marcar a reunião de sexta com a área admin.
- Matheus vai investigar o limite de rate do Airtable (429 é por base, por
  token ou por conta) — define decisão de design no proxy.
  [PRO-996](https://linear.app/projetos-livemode/issue/PRO-996/descobrir-como-o-airtable-aplica-o-limite-de-requisicoes-por-base-por).
- Matheus vai trazer visão própria de melhorias/riscos do proxy — Luís
  prefere receber a leitura dele pra comparar com a própria, não o
  contrário.
  [PRO-997](https://linear.app/projetos-livemode/issue/PRO-997/levantar-melhorias-e-riscos-do-proxy-do-airtable-numa-visao-propria).
- Matheus e Luís vão alinhar com a Gabi uma data (semana que vem) pra subir
  o proxy em produção com o LiveScript conectado — preferem um dia com
  eventos (não zerado), monitorar por 1-2 dias antes da saída do Luís.
- Matheus vai assumir e adaptar o [[Onboarding]] — remover travas (hoje tem
  confirmação demais com o Luís), ajustar conteúdo, pilotar pra mostrar
  valor à Carol e à Gabi.

## Open questions

- Limite de rate 429 do Airtable: por base, por personal access token ou
  pela conta toda? Impacta design do [[Airtable Proxy]].
- Airtable retorna 503 com `Retry-After` como comportamento "normal" — o
  proxy ainda não trata isso.
- Qual é o sistema de usabilidade ruim que o Luís quis apresentar ao
  Matheus "depois"? Não identificado, não confundir com o LiveAuth sem
  confirmar.

## Facts stated

- Luís: proxy rodando no Cloud Run com instâncias zeradas (cold start) —
  precisa virar mínimo 1 antes de conectar o LiveScript de verdade.
- Luís: monitoramento via Grafana Cloud já está configurado.
- Luís: backlog no Linear do proxy já mapeia 3 projetos — integração com
  [[LiveAuth]], painel admin, cenário de sobrecarga.
- Luís: teste de estresse (simular X usuários simultâneos) deve rodar numa
  aplicação separada, pra não sujar os relatórios do proxy real.
- Luís, sobre o [[Onboarding]]: Matheus achou o conteúdo bom mas muito
  travado (confirmação demais com ele).
- Luís usa Obsidian + LLM como second brain pessoal também; sugeriu a
  Matheus testar o repositório dele. Possível disseminação da prática no
  time.
- Luís: o **AirBridge** (assistente pessoal dele na GCP) provavelmente vai
  ser desligado — custa ~R$190/mês e a infraestrutura não avançou.
- Luís: fez evoluções num sistema que colocaram em produção, mas a
  usabilidade ficou muito ruim e a parte técnica também não — e hoje
  ninguém usa. Queria apresentá-lo ao Matheus depois, como exemplo do
  "tamanho do problema" (raw/Matheus _ Luis ( Transição) - 2026_10_07
  17_35 GMT-03_00 - Anotações do Gemini.md). Sistema não identificado no
  texto — não confundir com o "repo fantasma" do LiveAuth
  ([[2026-10-08 Discovery LiveAuth com Ana Beatriz Fonseca (Jurídico)]])
  sem confirmação.

## Notable quotes

(nenhuma registrada com confiança suficiente para verbatim — resumo
estruturado do Granola, não transcrição.)
