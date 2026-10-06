---
type: source
status: stable
updated: 2026-10-06
date: 2024-09-08
aliases: [mem0, mem0 intro, mem0 blog]
source: "raw/Clippings/Mem0 - AI Memory Layer for your Agents & Apps.md"
url: "https://mem0.ai/blog/introducing-mem0"
tags: [agents, memory, prior-art, rag]
---

# Mem0 — Introduction

Post de lançamento do blog do Mem0 (Taranjeet Singh, publicado 2024-09-08),
clipado em `raw/` em 2026-09-11 e ingerido em 2026-10-06 — sinalizado como
clipping pendente no `lint` do mesmo dia. Fonte rasa — é o post de anúncio,
não a doc técnica de arquitetura.

## O que diz

- Mem0 é uma **camada de memória** para LLMs: guarda preferências do
  usuário, traços, histórico de ações e eventos, pra personalizar respostas
  entre conversas. Open source (21k+ estrelas no GitHub) + plataforma
  gerenciada.
- Problema que ataca: LLM é stateless; RAG genérico não serve bem pra
  memória que muda o tempo todo (adiciona, atualiza, contradiz).
- Pipeline em três passos: **detecção** (identifica o que vale guardar
  durante a interação) → **armazenamento/atualização** (resolve
  contradições conforme o usuário muda) → **recall** (busca semântica +
  camada de score por relevância, importância e recência).
- Claims: retrieval sub-50ms, integração em 4 linhas de código, escala de
  protótipo a produção.
- Posicionamento: não é janela de contexto crua, não é vector DB/RAG
  genérico — memória de personalização propositalmente desenhada.

**Sobre grafo**: este post de introdução não menciona camada de grafo —
descreve só busca semântica (vetorial) + scoring. Mem0 tem um backend
opcional de "graph memory" (Neo4j) fora deste post (unverified: não
conferido na doc atual, não aparece aqui). Isso importa porque
[[Cognee como memória dos agentes e do time]] cita "Mem0 sem o add-on de
grafo" como o comparativo vetorial-puro a favor do Cognee — se o add-on de
grafo do Mem0 for competente, o argumento de travessia relacional não é
exclusividade do Cognee. Fica como pergunta aberta.

## Perguntas abertas

- O add-on de graph memory do Mem0 (Neo4j) resolve a mesma travessia
  relacional que motivou escolher grafo sobre vetor na synthesis do Cognee?
- Custo e footprint de self-host vs. plataforma gerenciada do Mem0.
- Qualidade em pt-BR — não coberta neste post.

## Relacionado

- [[Cognee como memória dos agentes e do time]]
- [[Cognee - Introduction]]
