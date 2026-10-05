---
type: source
status: stable
updated: 2026-10-05
date: 2026-10-05
aliases: [cognee, cognee intro, cognee docs]
source: "raw/Clippings/cognee introduction.md"
url: "https://docs.cognee.ai/getting-started/introduction"
tags: [agents, memory, knowledge-graph, prior-art, rag]
---

# Cognee — Introduction

Página de introdução da documentação do Cognee, clipada em 2026-10-05
(`created:` do clip; a página não tem data de publicação). Fonte rasa — é a
porta de entrada da doc, não descreve arquitetura, custo nem limites. O
aprofundamento (arquitetura, permissões, feedback, `improve`) foi lido direto
da doc ao vivo em 2026-10-05 e está em
[[Cognee como memória dos agentes e do time]], não aqui.

## O que diz

- Cognee é uma **camada de memória** para agentes: não é um LLM, e vai além
  de RAG simples — além de embeddings por chunk, monta um **knowledge graph**
  de entidades e relações, então o recall pode seguir conexões, não só
  similaridade de texto.
- Usa o LLM que você já tem (Claude, GPT, local) para construir e consultar a
  memória; prepara o contexto de cada chamada do agente.
- Quatro operações na v1.0:
  - `.remember` — ingere texto/arquivo/URL, chunka, extrai entidades, monta o
    grafo numa chamada. Memória permanente ou de sessão.
  - `.recall` — pergunta em linguagem natural; escolhe a estratégia de busca
    sozinho ou você especifica.
  - `.improve` — passes de enriquecimento sobre o grafo; com `session_ids`,
    leva memória de sessão para o grafo permanente e aplica peso por feedback.
  - `.forget` — remove item, dataset ou tudo de um usuário.
- Operações de baixo nível continuam: `add`, `cognify`, `search`, `memify`.
- Dois tempos de memória: **sessão** (curto prazo, rápida) e **grafo
  permanente**; o grafo é construído no momento do `remember` permanente.
- Interfaces: Python, CLI, REST API, MCP server, integração direta com
  Claude Code.
- Licença **Apache 2.0** — uso comercial livre.

## Perguntas abertas (desta página)

Respondidas em parte pela leitura da doc completa — ver
[[Cognee como memória dos agentes e do time]].

- Qual o custo de LLM do pipeline de ingestão (`cognify`) em volume real?
- Qualidade da extração de entidades em pt-BR / domínio Livemode?
- Como encaixa um gate humano antes de algo virar memória permanente?

## Relacionado

- [[Cognee como memória dos agentes e do time]]
- [[2026-09-10 Memória de fatos do agente (agent_facts)]]
- [[Agent Flow]]
