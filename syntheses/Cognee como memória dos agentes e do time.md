---
type: synthesis
status: open
updated: 2026-10-05
aliases: [cognee, estudo cognee, memória do time, memória organizacional]
tags: [agents, memory, knowledge-graph, agent-flow, harness, human-in-the-loop]
---

# Cognee como memória dos agentes e do time

Aberta em 2026-10-05, a partir de [[Cognee - Introduction]]
(raw/Clippings/cognee introduction.md). msilva salvou o Cognee como
**candidato para resolver memória dos agentes e contexto do time /
organização** — e quer estudá-lo antes de concluir qualquer coisa. Esta
página é o caderno desse estudo.

Rastreado no Linear como [PRO-870](https://linear.app/projetos-livemode/issue/PRO-870/avaliar-uma-ferramenta-pronta-de-memoria-compartilhada-para-os-agentes)
(Spike, Infra do Fluxo Agêntico, prazo 2026-10-09, relacionada à PRO-517) —
roteiro passos 1–5; o passo 6 ficou fora do escopo.

Salvo indicação, o conteúdo técnico abaixo vem da **doc ao vivo**
(`docs.cognee.ai`, lida em 2026-10-05 via `llms.txt`/páginas `.md`), não do
código — *o que a doc diz*, não verificado em execução.

## Enquadramento do msilva (2026-10-05)

- **RAG no `agent_facts` foi descartado por complexidade, não por
  princípio.** "Se uma solução já provê isso, não tem pq não usarmos."
  Corrige a leitura que eu fiz na hora do ingest (de que o Cognee "não
  reabria" a decisão) — ver nota em
  [[2026-09-10 Memória de fatos do agente (agent_facts)]].
- **Escopo é time/organização, não pessoal.** Este wiki já é um knowledge
  graph mantido por LLM, mas é do msilva; o Cognee seria a camada
  equivalente compartilhada pelo time e pelos agentes. Liga com a lacuna
  "memória do sistema agêntico" sem dono em [[Agent Flow]] (o A6 Curador
  deixou de ser agente, mas "memória organizada como infraestrutura"
  continuou o problema crítico) e com as preocupações de 2026-09-25 sobre
  contexto preso ao dashboard e agência individual do time.

## Como o Cognee trata "human in the loop"

**Resposta curta: não trata, no sentido do `agent_facts`.** Não existe fila
de aprovação nem estado `pending` na doc (busquei `approv`, `human in the
loop`, `review` no export completo — só aparecem em status de pipeline).
O gate que existe é **automático e feito por LLM**:

- `improve(session_ids=...)` → estágio *distill sessions*: um **curator**
  propõe lições duráveis, um **writer/rejecter** confere contra lições e
  entidades já existentes; só guidance com confiança acima do limiar e
  **não marcada como prejudicial** por feedback é elegível. Aceitas viram
  documentos `session_learnings` no grafo.
- **Feedback humano** existe, mas como *sinal de ranking*, não como gate:
  `session.add_feedback(qa_id, score 1–5, text)` → no próximo `improve`,
  ajusta `feedback_weight` dos nós/arestas usados na resposta. Desligado
  por padrão (`DEFAULT_FEEDBACK_INFLUENCE=0.0`). `AUTO_FEEDBACK` infere a
  nota da próxima mensagem do usuário — o oposto de HITL.
- `remember(..., self_improvement=True)` dispara `improve` sozinho em
  background.

**Mas os primitivos permitem montar o gate do lado de fora.** Hipóteses de
desenho, não testadas:

1. **Sessão como staging.** Memória de sessão não vira permanente até alguém
   chamar `improve(session_ids=...)`. Se só o host (após aprovação humana)
   chama `improve`, a sessão faz o papel de `pending`. Limite: `improve`
   leva a sessão inteira (Q&A + traces + lições), não fato a fato.
2. **Dataset de propostas vs. dataset aprovado** (mais próximo do
   `agent_facts`). Permissões são por dataset (`read`/`write`/`delete`/
   `share`, via user/role/tenant). Agentes têm `write` só em `proposals`,
   `read` só em `approved`; humano revisa e faz `remember` no `approved` +
   `forget` na proposta. A tela de revisão continua sendo nossa (ex.: a da
   SOUL), o Cognee só guarda.
3. **Aposentar sem apagar** = `close_node()` (marca `valid_to`), equivalente
   ao `retired`. **Duas ressalvas da própria doc**: só persiste no backend
   default (Ladybug) — nos outros, retorna `False` e não grava nada; e a
   busca **não filtra** nós fechados ainda, quem chama tem que filtrar com
   `is_valid()`.

## Time / organização

- Modo multi-usuário (`ENABLE_BACKEND_ACCESS_CONTROL`, default desde 0.5.0
  quando o storage suporta): **tenant** (organização), **role** (grupo
  dentro do tenant), **user**; permissão sempre **por dataset**. Em
  multi-usuário cada dataset vai para um banco físico próprio.
- Isso desenha naturalmente "memória global" (dataset do tenant, como
  `agent_key IS NULL`) vs. "memória por agente/pessoa" (dataset próprio).
- Três stores: relacional (documentos, chunks, proveniência), vetorial
  (embeddings), grafo (entidades/relações). Há handlers para **pgvector** —
  potencialmente encaixa no Postgres que o fluxo agêntico já usa
  (unverified: não conferi qual Postgres/Supabase o `livemode-fluxo-agentico`
  usa em produção, nem se o grafo roda bem fora do Ladybug).
- Proveniência: cada aresta pode ser rastreada até o chunk de origem
  (`provenance_edge_evidence`) — importante para "de onde o agente tirou
  isso".
- Plugin do Claude Code: captura **prompts, traces de tool e respostas** na
  sessão e sincroniza no grafo ao fim. Para memória de time isso é útil e
  arriscado ao mesmo tempo — tudo que alguém digita entra, sem gate.

## Roteiro de estudo proposto

Ordem pensada para responder primeiro o que pode descartar a ferramenta.

1. **Modelo mental** (1h) — `core-concepts/overview`, `architecture`,
   `data-flows`, `building-blocks/datapoints`. Objetivo: saber o que é nó,
   aresta, dataset, node set.
2. **Quickstart local** (meio dia) — `getting-started/quickstart`, rodar
   `remember`/`recall` com um punhado de documentos reais (ex.: 3–4 páginas
   deste wiki ou um design doc), **em pt-BR**. Medir: qualidade das
   entidades extraídas, tokens gastos no `cognify`, tempo.
3. **O gate humano** — protótipo da hipótese 2 acima (dataset `proposals` →
   `approved`) com dois usuários. É a pergunta que decide se substitui o
   `agent_facts` ou só fica atrás dele. Ler `guides/permission-snippets`,
   `permissions-system/*`, `main-operations/forget`, `guides/fact-validity`.
4. **Encaixe no stack** — `setup-configuration/relational-databases`,
   `vector-stores`, `graph-stores`; testar com Postgres + pgvector. Ler
   `integrations` para LangGraph/Langfuse (o fluxo agêntico usa os dois).
5. **Retrieval** — `main-operations/recall`, `guides/graph-completion`,
   `hybrid-retrieval-recall`: comparar com o "injeta tudo no prompt" de
   hoje do `agent_facts`.
6. **Só depois**: `improve`, distillation, feedback, personalização — são a
   parte mais "mágica" e a que menos casa com propõe→aprova.

Critérios para fechar a synthesis: (a) dá para montar o gate humano sem
brigar com a ferramenta? (b) custo de ingestão aceitável? (c) roda no nosso
Postgres ou exige banco novo? (d) qualidade em pt-BR.

## Perguntas abertas

- Hipótese 1 ou 2 para o gate — ou o Cognee fica só como camada de
  *retrieval* atrás da tabela `agent_facts`, que continua dona do
  propõe→aprova?
- `close_node` só no Ladybug: Ladybug é viável em produção (Cloud Run)?
- Cognee Cloud (gerenciado) vs. self-hosted — dados internos saindo da
  Livemode é aceitável?
- Ainda vale a objeção do Luís de 2026-08-20 (não desenhar memória
  compartilhada antes de assentar entidades, spike PRO-517)? Uma ferramenta
  pronta muda o custo dessa objeção, não necessariamente o mérito.

## Relacionado

- [[Cognee - Introduction]]
- [[2026-09-10 Memória de fatos do agente (agent_facts)]]
- [[Como deve funcionar o molde de agente]]
- [[Agent Flow]]
- [[Inside the multiplayer AI setups at Mintlify, LangChain, and Buffer]]
