---
type: source
status: stable
updated: 2026-10-22
date: 2025-01-10
source: "raw/Clippings/A Practical Perspective on Orchestrating AI Agent Systems with DAGs.md"
url: "https://medium.com/@arpitnath42/a-practical-perspective-on-orchestrating-ai-agent-systems-with-dags-c9264bf38884"
aliases: [Arpit Nath DAGs, DAG orchestration Medium]
tags: [agents, orchestration, dag, prior-art]
---

# Arpit Nath — Orchestrating AI Agent Systems with DAGs

Artigo no Medium de **Arpit Nath**, publicado em 2025-01-10, recortado em
2026-09-11.

## O argumento

- Orquestrar agentes com dependências entre tarefas pede um **DAG**:
  dependências explícitas, paralelismo onde não há dependência, retry e
  fallback por nó, visibilidade para depurar.
- Três alternativas testadas e descartadas pelo autor:
  - **árvore de dependências** — sem paralelismo; uma falha reinicia o
    fluxo;
  - **arquitetura por eventos** — dependências implícitas, difícil de
    rastrear, cada agente com tratamento de falha próprio;
  - **fila simples** — sequencial, uma falha trava tudo.
- Exemplo: moderação de conteúdo com um serviço orquestrador montando o
  DAG, um serviço de LLM (análise de texto com RAG), um de ETL (metadados
  de imagem) e um de notificação. Traz um DAG de brinquedo em Go — o
  próprio autor avisa que falta tratamento de erro, monitoramento e retry.

## Leitura para a Livemode

Pouco além do que a wiki já tem. O DAG como motor de ordem de execução é o
mesmo de [[Gabriel Packer - DAG-driven agent orchestration]], e o contraponto
está em [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]].
Os agentes do [[Agent Flow]] hoje são independentes, sem cadeia de
dependência entre eles para um DAG orquestrar.
