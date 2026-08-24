---
type: source
status: active
updated: 2026-08-24
date: 2026-04-02
source: "raw/Clippings/Harness engineering for coding agent users.md"
url: "https://martinfowler.com/articles/harness-engineering.html"
aliases: [harness engineering, feedforward feedback, guias e sensores]
tags: [agents, harness, martin-fowler, prior-art]
---

# Birgitta Böckeler — "Harness engineering for coding agent users"

Publicado em **2026-04-02** (revisão completa; uma versão anterior, "memo", saiu
em 2026-02-17) no site da Martin Fowler, por Birgitta Böckeler (Thoughtworks).
GenAI (Claude/Claude Code) usado para pesquisa e polimento do texto, segundo o
próprio artigo.

## O que descreve

Um modelo mental para "harness" — tudo em um agente de IA além do próprio
modelo (`agent = model + harness`). Böckeler recorta esse conceito amplo
especificamente para agentes de código, distinguindo:

- **Guias (feedforward)** — antecipam o comportamento do agente e tentam
  guiá-lo *antes* de agir (AGENTS.md, Skills, docs de referência, how-tos).
- **Sensores (feedback)** — observam *depois* que o agente age e permitem
  autocorreção (revisão por outro agente, análise estática, logs, browser).

Cruzado com um segundo eixo:

- **Computacional** — determinístico, rápido, rodando em CPU (testes,
  linters, type checkers).
- **Inferencial** — julgamento semântico via LLM, mais lento e caro, não
  determinístico (revisão de código por IA, "LLM as judge").

E três categorias do que o harness está de fato regulando: **maintainability**
(a mais fácil, já tem bastante tooling pronta), **architecture fitness**
(fitness functions), e **behaviour** (a mais difícil — *"the elephant in the
room"*: como garantir que o sistema se comporta funcionalmente como esperado;
hoje a resposta mais comum é spec + suíte de testes gerada por IA + revisão
manual, e a autora considera isso ainda insuficiente).

Também nomeia **harnessability** — nem todo código é igualmente fácil de
"arnesar" (tipagem forte, limites de módulo claros e frameworks opinativos
ajudam) — e **harness templates**: bundles de guias/sensores por topologia de
serviço (dashboard de dados, serviço CRUD, processador de eventos), que a
autora especula que podem se tornar critério de escolha de stack no futuro.

## Por que interessa ao [[Agent Flow]]

- **Vocabulário compatível, não idêntico, com o [[Agent Harness Template]] de
  Luís.** O template de Luís (Trigger → Input → Harness [Soul.md, Skills,
  Tools, MCP] → Output) e o par guias/sensores de Böckeler descrevem a mesma
  preocupação — como envolver um modelo para que ele acerte com mais
  frequência — mas em eixos diferentes: Luís descreve a *composição* do
  harness (do que ele é feito); Böckeler descreve o *ciclo de controle*
  (quando e como cada peça atua). Não são a mesma taxonomia — vale ler os
  dois como complementares, não fundir os termos.
- **"Behaviour harness" é o problema mais parecido com o que A12/A13 tentam
  resolver.** A autora chama isso de elo mais fraco do modelo — nenhum
  sensor confiável garante que o agente fez a coisa certa, só que não quebrou
  nada mensurável. Contraponto direto à leitura de A12 Data Gov / A13
  Deduplication como "harness" (gate determinístico) em [[Agent Flow]]: um
  gate de enforcement cobre bem manutenibilidade e (parcialmente) arquitetura,
  mas comportamento funcional — o que A7→A8→A9 está de fato tentando fazer
  bem feito — é exatamente onde a autora diz que "ainda temos muito o que
  fazer".
- **Reforça, com outra fonte, a tensão já registrada com
  [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] e
  [[How we built our multi-agent research system]]**: nenhuma das três trata
  orquestração pesada como resolvida. Böckeler nem chega a discutir
  multi-agente diretamente — o artigo é sobre harness em torno de *um* agente
  de código — o que por si só é um dado: a conversa mais madura sobre
  "harness" no momento é reforçar um agente único, não orquestrar vários.

## Não lido em profundidade

Referências do artigo original (memo de fevereiro, follow-up sobre sensores,
citações de OpenAI/Stripe) não foram seguidas — fora do escopo desta
ingestão.
