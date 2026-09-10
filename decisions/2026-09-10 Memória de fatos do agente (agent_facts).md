---
type: decision
status: active
updated: 2026-09-10
date: 2026-09-10
aliases: [agent_facts, memória de fatos, fact memory, remember_fact, recall_facts]
tags: [agent-flow, agents, memory, database, harness]
---

# Memória de fatos do agente (agent_facts)

## Origem

msilva, 2026-09-10, sessão de código em `livemode-fluxo-agentico` (branch
`langgraph`). Pergunta de partida: como implementar memória do fluxo agêntico
em nível global e individual. Fechado por iteração — cada rodada expôs um
problema no desenho anterior antes de chegar aqui.

## Não é o mesmo que o estado operacional que já existe

`a10/memory.py`, `a14/memory.py` e `outcomes.py` já existem no repo, mas são
outra coisa: estado sempre derivado de cálculo determinístico (`rules.py`),
nunca escrito pelo LLM — cooldown de sugestão, snapshot pra calcular delta,
outcome de entrega. Nenhum agente decide o que vira memória ali.

`agent_facts` é o agente reconhecendo, durante a execução, um fato que vale a
pena carregar pro futuro — mais perto de memória semântica/de insight do que
de histórico de estado.

## Decisão

Padrão **propõe → aprova**, não escrita autônoma: o agente nunca grava um
fato ativo direto. Reaproveita o mesmo mecanismo de promoção manual que a
SOUL já usa ([[2026-09-01 Modelar SOUL em tabelas com composição plana]]) —
conteúdo do Harness que só entra em produção com um humano no meio.

Dois níveis, na mesma tabela: `agent_key IS NULL` = global (entre agentes,
sem dono único, mesmo princípio de `outcomes.py`); `agent_key = "a10"/"a14"`
= individual.

## Modelo

```sql
CREATE TABLE agent_facts (
    id serial PRIMARY KEY,
    agent_key text,              -- NULL = global; "a10"/"a14" = individual
    fact text NOT NULL,
    reasoning text NOT NULL,      -- por que o agente achou isso importante
    metadata jsonb NOT NULL DEFAULT '{}',  -- contexto livre, não usado em filtro
    proposed_by text NOT NULL,    -- preenchido pelo host, não pelo LLM
    source_ref text,              -- run/thread de origem
    status text NOT NULL DEFAULT 'pending'
        CHECK (status IN ('pending', 'approved', 'rejected', 'retired')),
    created_at timestamptz NOT NULL DEFAULT now(),
    reviewed_by text,
    reviewed_at timestamptz
);
ALTER TABLE agent_facts ENABLE ROW LEVEL SECURITY;
```

## Escrita

```
remember_fact(fact: str, reasoning: str, metadata: dict | None = None)
```

Tool disponível no A10 e A14, batch e chat. `reasoning` obrigatório no
schema — se o agente não consegue justificar, não deveria estar chamando o
tool. `agent_key`/`proposed_by`/`source_ref` vêm do fechamento de
`create_tools(...)` (mesmo padrão de closure que já existe em
`a10/tools.py`), nunca informados pelo LLM. Sempre nasce `pending`.

## Revisão

Reaproveita a mesma superfície de tela da SOUL (`frontend/src/pages/soul-page.tsx`),
não uma tela nova: lista `pending`, aprova ou rejeita; lista `approved`,
permite aposentar (`retired`) a qualquer momento — esse é o mecanismo de
revisitar um fato que ficou desatualizado, sem apagar o histórico de quem
propôs e por quê.

## Leitura

Sem tool — igual ao resto da memória de domínio, o host lê antes do
`invoke()` e injeta como texto no prompt, mesmo padrão de
`outcomes.read_outcomes_by_project`/bloco "última análise de portfólio" já
usado hoje (`a10/agent.py:174`, `:189-203`). Filtro só por `agent_key`
(global + do próprio agente) — sem filtro por entidade nomeada, sem cap.

**Simplificação deliberada**: dois desenhos mais sofisticados foram cogitados
e descartados por ora —

- filtro por `initiative_name`/`criterion` resolvido pelo host (rejeitado:
  ainda inflava a decisão e dependia de o agente nomear a entidade certa);
- RAG por similaridade semântica (rejeitado: resolve um problema de volume
  que não existe ainda — dezenas de fatos, não milhares).

Se o prompt inchar na prática,avaliar essas alternativas então, não agora.

## Onde isso mora no molde

[[Como deve funcionar o molde de agente]] reserva um campo `memory_policy`
na "hipótese de estrutura", ainda em branco. `agent_facts` é a peça concreta
que preenche esse campo — mas é um **quarto tipo de conteúdo de Harness**,
distinto dos três que o molde já reconhece (Soul, Skill candidata,
task-prompt do Langfuse): os três são sempre autoria humana offline;
`agent_facts` é o único que nasce da execução do próprio agente, com humano
só como gate de aprovação, não como autor.

## Gate pendente — só a metade global

A metade `agent_key IS NULL` toca direto no que
[[Como deve funcionar o molde de agente]] registra: Luís pediu em 2026-08-20
pra não desenhar memória compartilhada entre agentes ainda, até as entidades
assentarem — rastreado como spike **PRO-517**. Esta decisão é exatamente
esse desenho. Antes de implementar a metade global, checar com ele se a
objeção ainda vale — o próprio PRO-517 já pede esse revisit agora que
A10/A14 e o molde avançaram.

A metade individual não tem essa objeção registrada — mesmo padrão de
`a10/memory.py`/`a14/memory.py`, que já existem e nunca foram contestados.

**Nenhuma implementação foi autorizada nesta sessão — só o desenho.**

## Relacionado

- [[Como deve funcionar o molde de agente]]
- [[Agent Harness Template]]
- [[2026-09-01 Modelar SOUL em tabelas com composição plana]]
- [[Agent Flow]]
- [[2026-09-10 1-1 Matheus - Luís]] — precursor informal desta decisão,
  mesmo dia: fluxo `pendente` → aprovação humana discutido e endossado
  antes do desenho técnico ("essa ideia de passar por uma aprovação, eu
  gosto pra caramba"). Não resolve por si só o gate do PRO-517 (memória
  global) — a conversa não menciona esse spike especificamente.
