---
type: synthesis
status: active
updated: 2026-08-26
date: 2026-08-26
tags: [agents, agent-flow, a10, a14, poc, langgraph, claude-code, agent-sdk, mcp]
aliases: [PoC A10+A14, três frentes da PoC, LangGraph vs Skill vs Agent SDK]
---

# Como implementar a PoC do A10+A14 (LangGraph, Skill, Agent SDK)

Continuação de [[2026-08-24 Build A10 and A14 together, PoC first]] — aquela
decisão fechou *o quê* (A10+A14 juntos, PoC antes de produção); esta página
registra *como*, depois de uma sessão de brainstorm em chat no dia
2026-08-26. O desenho mudou bastante ao longo da conversa — fica registrado
o raciocínio, não só o resultado final.

## Escopo

- **A10 Portfolio**: completo, mas só a parte de sugestão — sem loop de
  aprovação/execução (a spec já diz *"sugere, nunca executa"*, ver
  [[Fluxo Agêntico project instruction]]).
- **A14 PM Agent**: reduzido — report em texto/print do que já está
  trackeado no Linear, sem entrega automática em canal real ainda.
- **A11 Product explicitamente fora de escopo** — verificar se "as pessoas
  estão usando" o que foi entregue é função do A11, não do A14. A própria
  Carolina Bezerra já observou isso em discovery: A11 só faz sentido
  pós-lançamento, e nada do Fluxo Agêntico foi lançado ainda (ver
  [[2026-08-24 Agent Flow discovery with Carol]]).

## O real valor da PoC (mudança de enquadramento)

A pergunta original do Luís — "framework é necessário, ou Claude sozinho
resolve" — era provocativa, no sentido de já ter uma resposta esperada em
certo nível: o Fluxo Agêntico **como um todo** é um workflow de várias
etapas e agentes (pipeline A7→A8→A9), e isso não se resolve sem algum tipo
de orquestração. Mas A10 e A14, no recorte do M1, não são esse tipo de
agente — são análise/relatório de passo único sobre dado já existente, sem
estado entre etapas, sem handoff entre agentes. Testar "framework
necessário" nesse recorte não responde a pergunta mais ampla; essa só se
resolve quando o Fluxo Agêntico chegar nos agentes de pipeline.

Por isso o valor real desta PoC não é mais "decidir se framework compensa" —
é **ter algo palpável agora, com opções de solução em mãos**, deixando a
decisão de onde investir pra produção pra depois, informada pelo que sair
daqui.

## As três frentes

Nenhuma é "a principal" — as três são protótipos leves, só de validação:

| Frente | Testa | Acesso ao Linear | Billing |
|---|---|---|---|
| **LangGraph** | Framework de orquestração completo | API key própria do Linear, cliente direto (GraphQL/REST) | API key da Anthropic — pay-as-you-go, sem colchão de assinatura |
| **Skill do Claude Code** | Uso pessoal/interativo — invocado por msilva quando quiser | Connector MCP remoto (`mcp.linear.app/mcp`, Streamable HTTP) já plugado na sessão | Consumo normal da assinatura Claude Code (uso interativo) |
| **Claude Agent SDK** | Serviço autônomo — roda sozinho, sem depender de quem chama | Mesmo endpoint remoto MCP, podendo reaproveitar a mesma API key do Linear como Bearer token | Crédito mensal separado da assinatura Pro/Max (Pro: $20/mês, Max 5x: $100/mês, Max 20x: $200/mês) — válido só pra uso individual/PoC |

### Por que três, não duas

A ideia inicial era duas frentes (LangGraph vs. "Claude direto"). Ao longo da
conversa isso se desdobrou: "Claude direto" na verdade cobre dois runtimes
bem diferentes — uma skill interativa (você invoca, MCP da sua sessão) e um
serviço via Agent SDK (roda sozinho, MCP configurado no serviço). O segundo
resolve um problema real que a skill sozinha não resolve — não dá pra supor
que quem invoca já tem o Linear configurado — então virou frente própria em
vez de descartada.

### Evitar

Uma frente **não deve chamar a outra por baixo dos panos** — ex.: LangGraph
rodando `claude -p` como subprocess, ou o inverso, LangGraph exposto como
endpoint HTTP consumido pela skill. Isso colapsaria as opções numa só
implementação com portas de entrada diferentes, e destruiria o ponto real da
PoC (ter opções de verdade). A ideia de expor a solução vencedora como
endpoint depois de escolhida é válida — só não faz parte desta PoC, ver
"Notas pra depois" abaixo.

### Reaproveitamento legítimo

- **Critérios de detecção/raciocínio compartilhado** — o mesmo texto (o que
  conta como sugestão do A10, formato do report do A14, ver
  [[Fluxo Agêntico project instruction]]) alimenta as três frentes. Escrito
  uma vez, primeiro, antes de começar a implementar (ver PRO-375/PRO-376 no
  Linear).
- **`langchain-mcp-adapters`** — pacote oficial da LangChain
  (`pip install langchain-mcp-adapters`, `github.com/langchain-ai/langchain-mcp-adapters`)
  que converte tools de um servidor MCP em tools do LangGraph via
  `MultiServerMCPClient`. Cogitado pro LangGraph acessar o mesmo MCP do
  Linear que as outras duas frentes usam — descartado depois em favor de
  acesso direto por API key (mais simples, e a PoC não precisa mais de
  comparação rigorosamente justa). Fica registrado como opção válida se
  algum dia fizer sentido reverter essa escolha.

## Sequência

1. **Critérios compartilhados** — escritos como etapa própria, antes das
   três implementações (evita retrabalho se mudar de ideia no meio).
2. **Implementar as três frentes**, sem timebox.
3. **Testar contra o caso do Farol** (ver abaixo) — não a reunião inteira
   com a Carol.
4. **Mostrar as três saídas pro Luís e/ou Carol**, avisando que é saída de
   uma PoC — não escondido, ao contrário do que se cogitou inicialmente
   (avaliação "cega").
5. **PoC encerra** quando as três existem e o feedback foi coletado — a
   decisão de qual caminho (ou quais) vira produção é etapa separada, fora
   deste escopo.

## Caso de teste: o Farol

Inicialmente cogitado como "a reunião inteira com a Carol"
([[2026-08-24 Agent Flow discovery with Carol]]), mas essa reunião é
discovery/estratégia, não um exercício de priorização de backlog — não tem
uma lista de itens reais sendo julgados. O caso concreto e testável dentro
dela é mais estreito: Carol avaliou, em discovery, que o **Farol** não
deveria ter sido projeto prioritário — chegou por pressão de demanda
externa, nunca passou por fila de priorização, e já tinha crescido antes de
ser percebido (*"isso tá engasgado aqui na minha boca"*). Isso bate direto
com os critérios de detecção do A10 (escopo descontrolado, priorização
desalinhada).

**Critério de validação**: rodando o A10 sobre o backlog real de hoje, ele
sinaliza o Farol como fora de prioridade sozinho, sem alguém precisar
apontar isso primeiro?

n=1 — só esse caso. Foi considerado adicionar um segundo caso real, mas
decidido manter simples por ora; o objetivo agora é ter algo palpável, não
uma validação exaustiva.

## Notas pra depois (fora do escopo desta PoC)

- **Expor a solução vencedora como serviço/endpoint**, consumido por várias
  interfaces (Claude Code, dashboard, Slack etc.) — arquitetura de produção
  normal, uma vez que já se souber qual caminho (ou quais) investir.
- **Migrar o Agent SDK de crédito de assinatura pra API key da Anthropic**
  se essa frente virar produção compartilhada com o time — a doc oficial da
  Anthropic é explícita que o crédito da assinatura é *"sized for individual
  experimentation and automation"*, não pra *"shared production
  automation"* (fonte: support.claude.com/en/articles/15036540).

## Pendências operacionais (não mudam o desenho, resolver antes de codar)

- ~~De onde vem a API key da Anthropic pro LangGraph — pessoal ou da
  Livemode.~~ ~~Quem paga o uso da API key.~~ **Resolvidas 2026-08-26**: a
  frente LangGraph usa **OpenAI, não Anthropic** — chave já existente, já
  configurada no `.env` do repo. Ver
  [[Desenho do agente LangGraph para A10+A14]] pro resto do desenho técnico
  dessa frente (padrão agent/`create_agent`, toolset, Langfuse, etc.).
- ~~Onde o código de cada frente vai morar (repositório).~~ **Resolvido
  2026-08-26**: um repositório único pras três frentes — pasta local
  `C:\Users\msilva\projects\livemode-fluxo-agentico`, remoto
  `git@github.com:tech-livemode/livemode-fluxo-agentico.git`.

## O que isso muda em outros lugares

| Page | Change |
|---|---|
| [[2026-08-24 Build A10 and A14 together, PoC first]] | Ganha um pointer pra este desenho mais detalhado |
| [[Agent Flow]] | Novo callout apontando pra este design |
| Linear — PRO-375, PRO-376 | Atualizadas com critérios de detecção compartilhados e o caso de teste (Farol) |
| Linear — PRO-377 | Retitulada e reescrita pra refletir três frentes, não duas; ganhou 4 sub-issues: PRO-392 (LangGraph), PRO-393 (Skill), PRO-394 (Agent SDK), PRO-395 (validação com Luís/Carol, bloqueada pelas três) |
| [[Desenho do agente LangGraph para A10+A14]] (2026-08-26) | Nova página — desenho técnico do interior da frente LangGraph (agent vs. workflow, toolset, Langfuse, OpenAI), mapeado também em PRO-392 |
