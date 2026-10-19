---
type: synthesis
status: active
updated: 2026-10-13
aliases: [CrewAI vs LangGraph, comparação CrewAI]
tags: [agent-flow, langgraph, crewai, memória]
---

# CrewAI vs LangGraph para o Fluxo Agêntico

Luís pediu a msilva uma comparação entre LangGraph e CrewAI (2026-10-13) —
ainda como Tech Lead da área, presente até o fim de outubro/2026 (ver
[[Luís Fernandez]]). Registrado em chat, não em reunião formal.

**Status: em aberto.** msilva pediu pra deixar registrado e volta nisso
depois — o que segue é o estado da análise até aqui, não uma decisão.

## A visão inicial do msilva

- **LangGraph**: mais controle; liberdade pra desenhar workflow ou agent;
  liberdade pra escolher a própria solução de memória (Cognee, Mem0, outra).
- **CrewAI**: menos trabalho.

## O que a sessão levantou, verificado

**CrewAI** (doc oficial, lida 2026-10-13):
- Dois produtos: framework open-source (Agent/Task/Crew/Flow, Python ≥3.10
  <3.14) + plataforma Enterprise hospedada (SSO/RBAC, deploy em VPC ou
  self-host, onboarding de 45 dias).
- Pricing: Basic grátis (editor visual + copiloto, GitHub, 50 execuções de
  workflow/mês), Enterprise sob consulta. Clientes citados: DocuSign,
  Experian, PepsiCo, IBM, AB InBev.
- Memória: classe `Memory` única e opinativa — escopo hierárquico tipo
  filesystem (`/agent/x`, `/customer/y`), ranking por composite score
  (semântica 0.5 + recência 0.3 + importância 0.2), backend padrão LanceDB
  + embedding OpenAI `text-embedding-3-large`. Um LLM roda em toda
  gravação/recall pra inferir escopo/categoria/importância e consolidar
  duplicatas (limiar de similaridade 0.85). Não existe mais "memória de
  sessão" como categoria separada — vira só peso de recência
  (`recency_half_life_days`, padrão 30) dentro do mesmo ranking.

**O que o repo `livemode-fluxo-agentico` já faz hoje** (verificado no
código, não na doc):
- Já é **LangGraph** (confirmado nos 4 agentes: `intake`, `prioritizer`,
  `project_reporter`, `portfolio_analyst`) + **LangChain**
  (`ChatOpenAI`, `with_structured_output`) + **Langfuse** (tracing +
  prompt versionado por ambiente via `load_task_prompt`).
- Memória hoje é **determinística, não vetorial**: contexto vem de fetch
  direto no Linear (`linear_client`) + checkpoint de conversa no Postgres
  (`PostgresSaver`, já citado em [[Agent Flow]] 2026-09-02). Nenhum dos 4
  agentes usa busca semântica pra montar contexto — cada um decide em
  Python o que entra no prompt (ex.: `_context_block` do `prioritizer`,
  com limite de caracteres explícito por documento/issue).
- Padrão por agente já maduro e repetido nos 4: `contracts.py` (tipos),
  `ports.py`, `rules.py`, `seed_prompt.py`/`seed_skills.py`, evals
  (`eval_experiment.py`/`eval_datasets.py` publicando no Langfuse).

## Pontos que a visão inicial do msilva ainda não cobre

- **"Menos trabalho" do CrewAI tem custo embutido**: toda gravação/recall
  de memória roda uma chamada de LLM + embedding, mesmo quando um lookup
  determinístico — o que os 4 agentes já fazem — resolveria de graça e sem
  custo extra.
- **Lock-in**: trocar pelo CrewAI não é incremental — reescreveria os 4
  agentes, perderia a integração já feita com Langfuse e o padrão
  `contracts.py`/`ports.py`/evals já testado e em produção.
- **Governança Enterprise do CrewAI (SSO/RBAC) não resolve um problema que
  o time tem** — sistema interno, [[LiveAuth]] já cobre autenticação entre
  apps.
- **Onde "menos trabalho" valeria de fato**: só se a topologia precisar de
  um "comitê de personas" colaborando no mesmo turno — padrão que o `Crew`
  do CrewAI facilita nativamente e que hoje não existe nos 4 agentes (cada
  um é um fluxo de um agente só por vez, não um comitê). Hipótese levantada
  em chat pro `portfolio_analyst`/A14 `project_reporter` como candidatos
  mais naturais, dado o histórico de saída "simplória" do A14
  (`feedback-agentic-output-informative`, memória, não página da wiki) —
  não confirmado, não desenhado.

## Relacionado

- [[Agent Flow]]
- [[Vocabulário do Fluxo Agêntico]]
- [[Cognee como memória dos agentes e do time]] — mesma pergunta de
  memória, outro candidato (grafo de conhecimento em vez de framework de
  orquestração)
- [[Desenho do agente LangGraph para A10+A14]]
- [[Luís Fernandez]]
