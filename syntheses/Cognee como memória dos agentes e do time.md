---
type: synthesis
status: active
updated: 2026-10-07
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
- **Grafo vale a pena (2026-10-06).** Confirmado em conversa: a necessidade
  real não é só factoide isolado, é travessia relacional — "quem bloqueia o
  quê", "que decisão depende de qual projeto" — o mesmo padrão que este
  wiki já modela via wikilinks entre `projects/`/`people/`/`decisions/`. Sem
  grafo, a camada de memória vira busca vetorial — "grep melhorado" — e
  perde essa travessia. Pesa a favor do Cognee sobre uma solução puramente
  vetorial (ex.: Mem0 sem o add-on de grafo).
- **Checado 2026-10-06**: [[Mem0 - Introduction]] (post de lançamento do
  blog, não a doc técnica) descreve só busca semântica + scoring por
  relevância/importância/recência — nenhuma menção a grafo. O Mem0 tem um
  backend opcional de graph memory (Neo4j) fora deste post, não conferido
  aqui. Se esse add-on cobrir a mesma travessia relacional, o argumento
  "grafo vale a pena" perde parte da força contra o Mem0 especificamente
  (não contra busca vetorial em geral) — pergunta fica aberta, ver abaixo.

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

   **Resolvido (msilva, 2026-10-07): não precisamos do `close_node`.** A
   fila de aprovação (pending/rejected) fica inteiramente fora do Cognee —
   só o que já foi aprovado vira `.remember`, mesmo desenho da hipótese 2
   acima. E um fato que ficou desatualizado não precisa manter histórico:
   pode simplesmente **sumir** com `.forget`, em vez de ser aposentado com
   `close_node`. Sem depender de `close_node`, a trava "só persiste no
   Ladybug" deixa de valer como bloqueio — qualquer backend de grafo (Neo4j,
   Kuzu-remote, Memgraph) serve. Isso também simplifica o passo 4 do
   roteiro: a escolha de backend de grafo volta a ser só sobre maturidade/
   operação em produção, não mais restrita ao Ladybug.

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

## Onde rodar — comparativo (2026-10-07)

msilva cogitou começar pelo Cognee Cloud free tier. Antes de decidir, medido
o gasto real de LLM do `livemode-fluxo-agentico` via API de métricas do
Langfuse (`us.cloud.langfuse.com`, projeto do fluxo agêntico) — todo o
histórico do projeto começa em 2026-09-07, não tem dado antes disso:

| Janela | Tokens | Custo | Observações |
|---|---|---|---|
| 30 dias (= todo o histórico) | 21,78M | $3,29 | 19.496 |
| Últimos 7 dias | 9,84M | $1,24 | 12.454 |

Uso está acelerando, não estável — 7 dias já é quase metade do total de 30.
Taxa dos últimos 7 dias extrapolada pro mês: **~42M tokens / ~$5,30**, mais
realista que os $3,29 do total bruto. Por trace: produção (`LangGraph`, A10/
A14/intake/priorizador) = 18,94M tokens / $2,74 (87%); eval/CI = ~2,6M
tokens / $0,51 (15%). Custo baixo apesar do volume porque cache de prompt
carrega o peso — uma chamada amostrada teve 7.265 tokens de prompt, 5.773
vindos de `input_cache_read` (barato) contra 1.370 de `input_cache_creation`.
A SOUL inteira (system prompt por agente) fica cacheada e reusada.

| | Cognee Cloud Free | Cognee Cloud Standard | Self-host local (dev) | Self-host produção |
|---|---|---|---|---|
| Custo | $0, 1M tokens/mês | $1/1M tokens | Grátis + chave LLM própria | Grátis + chave LLM própria |
| Cabe no volume atual? | **Não** — frota já roda ~9,84M tokens/semana; 1M/mês estoura em dias se o `cognify` tocar fração relevante disso | Provável sim, mas vira ~$20-40/mês — maior linha de custo LLM do projeto (hoje tudo custa $3-5/mês com cache) | Sim — custo segue o preço do próprio modelo (gpt-5.6-luna: ~$0,20/1M input, ~$1,20/1M output), sem markup do Cognee | Mesmo caso, em escala |
| Dado sai da Livemode? | Sim | Sim | Não | Não |
| Testa backend de grafo de produção? | Não | Não | Sim, mas com Ladybug (sem necessidade real, ver abaixo) | Sim, já pode ser Neo4j/Kuzu-remote direto |
| Setup | Zero | Zero | Baixo (embarcado) | Alto (Postgres+pgvector+grafo remoto) |
| 1 workspace só | Trava modelo global/por-agente | Resolve ($5/mês por workspace extra) | N/A (dataset é seu) | N/A |

Risco comum aos quatro, não só de escala: "tokens processados" do Cognee são
as chamadas internas dele (extração de entidade, montagem de grafo) —
normalmente **mais** que o texto de entrada (múltiplas passadas), não um
espelho 1:1 do que se manda pro `.remember`. E se o recall do Cognee injeta
conteúdo de grafo que muda o prefixo do prompt a cada chamada, quebra o
cache que hoje carrega 87% do custo barato — o custo real pode subir
desproporcional à conta simples de token-count.

**Leitura**: free tier serve só pro quickstart pontual (passo 2 do roteiro,
3-4 páginas de teste), não pra medir custo em escala — pra isso o número que
importa é o self-host, que reflete o preço do próprio modelo, não o markup
do Cognee. Decisão de onde rodar em produção continua em aberto.

## Achado no código (2026-10-07)

`agent_facts` não é só desenho — a metade individual (`agent_key` não nulo)
já está implementada em `livemode-fluxo-agentico` desde 2026-09-11
(`ff2ad74`, "memoria de fatos (agent_facts) -- só a metade individual"):
`core/agent_facts.py`, `core/agent_facts_api.py`, com testes. A metade
global (`agent_key IS NULL`) segue não implementada, consistente com o gate
do Luís (PRO-517) registrado em
[[2026-09-10 Memória de fatos do agente (agent_facts)]]. O propõe→aprova que
o Cognee precisaria recriar por fora já roda em produção hoje, pelo menos na
metade individual — isso é o que estaria em jogo se o Cognee substituir essa
peça.

## Execução da PoC — PRO-983, 2026-10-07

PRO-870 **continua aberta** — os critérios "pronto quando" (custo de ingestão
real, encaixe no Postgres, qualidade em pt-BR, recomendação escrita) não
foram cumpridos, só o desenho teórico do gate. Criada sub-issue
[PRO-983](https://linear.app/projetos-livemode/issue/PRO-983/rodar-poc-do-cognee-cloud-com-dados-reais-do-time)
pra rodar a PoC de fato: Cognee Cloud (free tier), com dado real — decidido
em chat usar um recorte do **time Projetos-livemode inteiro no Linear**
(não a wiki pessoal, não o Airtable legado — Linear é o sistema de registro
atual, em pt-BR, com volume real de issues/comentários).

## Perguntas abertas

- ~~Hipótese 1 ou 2 para o gate~~ — **resolvido 2026-10-07**: hipótese 2.
  Fila de aprovação (pending/rejected) fica fora do Cognee, numa tabela
  nossa (mesmo papel que `agent_facts` já cumpre); só o aprovado vira
  `.remember`. Ainda em aberto dentro disso: essa tabela própria continua
  sendo literalmente `agent_facts`, ou vira só o dataset `proposals` do
  Cognee com uma tela de revisão por cima? **Leaning (msilva, 2026-10-07,
  não fechado)**: faz sentido centralizar no Cognee (dataset `proposals`/
  `approved`) em vez de manter `agent_facts` em paralelo. Ressalva levantada
  na mesma conversa: `agent_facts` já está em produção e o Cognee ainda não
  passou por nenhum passo do roteiro (2-6) — migrar antes de validar
  qualidade/custo/pt-BR arrisca pagar a migração duas vezes se o Cognee for
  descartado depois. Ordem sugerida: validar primeiro (passos 2 e 3), só
  então centralizar.
- ~~`close_node` só no Ladybug~~ — **resolvido 2026-10-07, moot**: não
  precisamos aposentar sem apagar; fato desatualizado é removido com
  `.forget`. Sem `close_node`, qualquer backend de grafo serve — a escolha
  de produção fica livre entre Neo4j/Kuzu-remote/Memgraph por maturidade de
  operação, não mais travada no Ladybug.
- Cognee Cloud (gerenciado) vs. self-hosted — dados internos saindo da
  Livemode é aceitável?
- Ainda vale a objeção do Luís de 2026-08-20 (não desenhar memória
  compartilhada antes de assentar entidades, spike PRO-517)? Uma ferramenta
  pronta muda o custo dessa objeção, não necessariamente o mérito.
- O add-on de graph memory do Mem0 (Neo4j) resolve a mesma travessia
  relacional que motivou escolher grafo sobre vetor aqui? Não coberto pelo
  post de lançamento lido em [[Mem0 - Introduction]].

## Relacionado

- [[Cognee - Introduction]]
- [[2026-09-10 Memória de fatos do agente (agent_facts)]]
- [[Como deve funcionar o molde de agente]]
- [[Agent Flow]]
- [[Inside the multiplayer AI setups at Mintlify, LangChain, and Buffer]]
- [[Mem0 - Introduction]]
