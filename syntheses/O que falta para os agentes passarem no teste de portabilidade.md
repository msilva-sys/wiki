---
type: synthesis
status: active
updated: 2026-10-22
aliases: [estudo de desacoplamento, portabilidade dos agentes, varredura de portabilidade, teste de portabilidade no código]
tags: [agent-flow, arquitetura, portabilidade, molde, adr, linear, langfuse]
---

# O que falta para os agentes passarem no teste de portabilidade

Pedido de msilva em 2026-10-21: pensar o [[Agent Flow]] de forma arquitetural,
como o Luís indicou, e descobrir o que falta para o nível de desacoplamento que
ele descreveu. Ponto de partida de msilva: os agentes deveriam funcionar em
outros contextos, e há camadas externas vazando para dentro deles, com o
exemplo da lista `Criterion` em `portfolio_analyst/domain/contracts.py`.

É a primeira aplicação ao código do critério registrado em
[[Como deve funcionar o molde de agente]] (seção do teste de portabilidade,
2026-10-20). A opinião completa do Luís está em
[[O que Luís acha do projeto dos agentes]].

**Método.** Varredura read-only do repo `livemode-fluxo-agentico` (branch
`msilva/pro-925-27-e-dono-do-campo-indefinido`, commit `fb44842`) em 8 fatias
paralelas: `core/domain` + `core/use_cases`, `core/infra` (runtime),
`core/infra/linear`, `portfolio_analyst`, `project_reporter`, `intake` +
`prioritizer`, prompts/Langfuse/eval e o molde transversal. 212 achados
brutos. A verificação adversarial automática morreu por falta de crédito; os
achados que sustentam as conclusões abaixo foram conferidos direto no código
(grep/leitura). O que vem só da leitura dos agentes da varredura está marcado
`(unverified: ...)`.

## Resposta curta

O que falta não é forma, é **separação de conteúdo e um molde**. A
reorganização (PRO-1005) acertou a forma: portas, casos de uso sem import de
infra, adapters injetados, camada propõe → aprova. É por isso que o Luís disse
*"a tua reorganização não tá ruim"* e *"aqui vai funcionar"*. Mas:

1. o `core` mistura quatro coisas: o kernel genérico de agente, o domínio de
   gestão de entrega, o vocabulário do Linear e o processo do time;
2. o adapter do Linear não traduz, então o vocabulário do Linear **é** o domínio;
3. o processo da Livemode está em `Literal` e constante, não em dado;
4. não existe manifesto nem registro de agente: o core importa os agentes pelo
   nome;
5. a arquitetura não está disponível para o agente de código: o repo não tem
   `CLAUDE.md`/`AGENTS.md`, e nenhum teste falha quando uma regra de camada é
   violada.

O esqueleto do molde já existe, espalhado. O trabalho é extraí-lo e tirar o
conteúdo de dentro dele.

| Medida | Hoje |
|---|---|
| Linhas de `core/domain` + `core/use_cases` que servem a qualquer agente | ~220 de ~1.030 |
| Lugares que definem um critério do A10 | 6, em 3 sistemas (Python, front, Langfuse) |
| Imports de agente dentro do core | 12 (9 em `app.py`, 3 em `models.py`) |
| Leituras de `linear_team_id` fora do settings | 15 |
| Custo de criar o 5º agente | ~11 arquivos copiados + 5–6 edições no core/config `(unverified: contagem da varredura)` |

## A régua: três níveis de especificidade

| Nível | O que é | Onde deveria morar | Exemplo hoje |
|---|---|---|---|
| **Linear** | API, ids, GraphQL, `state_type`, priority 0–4, health `onTrack/atRisk/offTrack`, webhook | só num adapter trocável | `Health` no contrato do A14 |
| **Domínio de entrega** | Issue/Project/Initiative/Milestone, estágio, outcome, prioridade, vazão | pacote de domínio plugável, fora do kernel | `core/domain/__init__.py` inteiro |
| **Processo Livemode** | critérios, estágios em pt-BR, rota técnico/não técnico, T-shirt, Valor/Risco em label, fórmula e cortes, cadências, escada, persona | dado: manifesto, `config` do prompt no Langfuse ou tabela | `Criterion` |

Transversais: apresentação dentro do domain, prompt dentro do código, tipo de
framework em porta de domínio, import entre pacotes que não deveriam se
conhecer.

## O que já passa (não mexer)

- **Forma das camadas.** Portas como `Protocol` satisfeitas por módulo, casos
  de uso sem import de infra, fakes nos testes, nos quatro agentes. É o que o
  Luís aprovou (*"Perfeito"*).
- **Revisão humana.** `core/domain/review.py` (pending → approved/rejected →
  retired), `agent_facts`/`agent_actions` com identidade fixada na montagem e as
  tools compartilhadas `remember_fact`/`list_pending_actions`/`recommend_action`.
  Luís: *"é para qualquer agente"*.
- **Trava de confirmação do intake.** Reserva antes do efeito, `UnknownResult`
  fica em andamento, só falha conhecida libera. É a peça genérica mais forte do
  repo, e está presa no intake: o `confirm` do prioritizer grava Valor/Risco
  direto, sem trava (`prioritizer/use_cases/prioritize.py:276-294`).
- **Harness.** `build_agent` (loop de tools) e `build_workflow` (StateGraph),
  checkpoint por (agente, escopo, thread), `PromptStore` e `Tracer` atrás de
  porta, cron (auth, db-ready, histórico), servidor MCP com verificador de
  token, `Settings` como fonte única.
- **ADR 0003 entregue.** Soul e skills só no Langfuse; nada de seed nem pacote
  `soul` no código.
- **Mecanismos de domínio.** A10: fingerprint → cache → julgar → resolver
  id/nome → recorrência → publicar com cooldown e `UnknownResult`. A14:
  snapshot diário imutável, escada recalculada do histórico, exceção aprovada
  por humano. Os evals do A10 e do A14 rodam com leitor falso atrás da porta
  `(unverified: leitura da varredura)`.
- **Client do Linear.** Transporte com `UnknownResult` em mutation, span por
  chamada, webhook verificado dentro do adapter, GraphQL isolado em
  `queries.py`.

## O que não passa

### 1. O core não é kernel

Só ~220 das ~1.030 linhas servem a qualquer agente: `ChatMessage`,
`agent_facts`, `agent_actions`, `agents`, `review`, `errors` e 4 das 9 portas
(`Tracer`, `PromptStore`, `FactStore`, `ActionStore`). O resto:

- `core/domain/__init__.py` se autodescreve como "Vocabulário de domínio do
  A10/A14" e carrega Issue/Project/Initiative/Milestone, `GithubPR`,
  `ProjectLabel` e a escala de priority do Linear com rótulos pt-BR.
- `core/domain/portfolio.py`: fórmula `(valor × confiança) / esforço`, cortes
  `1.0 / 0.4 / 0.15`, labels `Valor`/`Risco` com prefixo `V`/`R`, o fallback
  `"Construção"` e o parser do payload de webhook do Linear (`updatedFrom`,
  `labelIds`, `statusId`). Formato do Linear morando no domínio.
- `core/domain/stage.py`: os 9 estágios do time em pt-BR mapeados para os dois
  enums nativos do Linear.
- `core/domain/outcomes.py`: efeito, aderência e adoção por milestone, o
  exemplo exato que o Luís citou.
- `core/domain/ports.py`: 5 das 9 portas são de entrega (`PortfolioWriter`,
  `PortfolioSource`, `PriorityWriter`, `OutcomeStore`, `StageStore`), e
  `Tracer` importa `BaseCallbackHandler` do LangChain, único import de
  framework no domain.

**Causa raiz.** A regra do README da reorganização: "entidade/regra de domínio
mora dentro do agente que a usa. Se mais de um agente usa, vai pra `core/`".
Ela confunde *compartilhado entre os agentes de entrega* com *genérico para
qualquer agente*. Falta um terceiro nível entre `core` e `agents`: um pacote de
domínio plugável.

### 2. O adapter do Linear não traduz

O docstring de `core/infra/linear_adapter.py` promete que "é aqui, e só aqui,
que a tradução pro domínio acontece", mas as três conversões são cópia
(`domain.Issue(**issue.model_dump())`). Consequências:

- Health do Linear como tipo de domínio e como saída do LLM:
  `Health = Literal["onTrack", "atRisk", "offTrack"]` no contrato do A14. O A10
  mapeia `saudavel → onTrack` dentro das próprias regras
  (`_LINEAR_HEALTH_BY_STATUS`).
- `state_type == "started"` comparado como literal nas regras do A14;
  `ACTIVE_PROJECT_STATUS = "started"`.
- `SourceType = Literal["linear"]` no contrato do A10, e o front fixa
  `/chat/linear/` (`apps/web/src/api/portfolio-analyst.ts:129`).
- Client cru chamado sem porta: rotas `/teams`, `/projects`, `/overview` e
  `PUT /stage` do A10 e do A14, tools MCP, `core/infra/portfolio.py`, e o
  prioritizer usa o módulo `linear` como reader default.
- `Settings` exige `LINEAR_API_KEY` e `LINEAR_TEAM_ID`: sem eles o app não sobe,
  nem para agente que não toca o Linear.
- `core/infra/linear/client.py` lê a chave em tempo de import e tem um
  `__main__` de smoke test que procura o projeto "Fluxo Agêntico".

### 3. Processo da Livemode como tipo e constante: o caso `Criterion`

A cadeia de um critério do A10 hoje:

| # | Onde | O quê |
|---|---|---|
| 1 | `portfolio_analyst/domain/contracts.py:13` | `Literal` com os 5 critérios. Vira o schema de saída do LLM (`ToolStrategy`): o modelo é forçado a escolher um dos 5 |
| 2 | `portfolio_analyst/domain/rules.py:28` | `CRITERION_SEVERITY`, "chute inicial a calibrar" |
| 3 | `portfolio_analyst/domain/formatting.py:6,16` | `CRITERION_LABELS` e `CRITERION_HINTS` em pt-BR |
| 4 | `apps/web/src/lib/criteria.ts` | rótulos, hints, severidade e pesos copiados "pra manter as duas pontas" |
| 5 | Langfuse | `portfolio_analyst/skills/<critério>`, composto no system-prompt |
| 6 | `portfolio_analyst/eval/eval_experiment.py` | 6 ramos `if fixture == "<critério>"` |

Mais as docstrings das tools, que são prompt e citam critério e PRO-xxx.

`docs/reorganization/prompts-langfuse.md` já tinha decidido o contrário:
"Incluir/tirar critério é edição de prompt, sem deploy" (l. 269) e
"Descartado: lista de critérios no código" (l. 284). O código não acompanhou: o
`Literal` é essa lista, escondida num tipo.

**Nuance.** Ter critérios não é vazamento: um analista de portfólio julga por
critérios. O mecanismo (julgar por critério → severidade → recorrência →
publicar com cooldown) é molde; **a lista é instância**. Em outra empresa a
lista muda e o mecanismo fica.

Mesmo padrão em: `Route = Literal["tecnico", "nao_tecnico"]` do intake (chega
ao front e à coluna `rota`), escala `tshirt` XS–XL do prioritizer, labels
`Técnico`/`Não Técnico` e estado `triage` no writer do intake, regex
`^_alertas_`, persona "Taquinho", `PortfolioStatus`/`direction`/`Severity` em
pt-BR, `ProactiveFlag` do A14. No A14 também: a política de cobrança derivada da
letra do código de cenário do doc 2.7 (`"A"` cobra, `"B"` espera; formato
dos docs de cenário em [[Anotações sobre os cenários do A14 (2.2)]]), cortes
70% / 8 / 2 / 14 dias, escada 7 dias × 4 degraus, `WEEKLY_PUBLISH_WEEKDAY = 3`
e `is_weekly_publish_day = False` como kill switch literal
(`project_reporter/infra/cron.py:144`).

### 4. Não existe molde; o registro é à mão e dentro do core

- `core/infra/app.py` importa 9 módulos de agentes e `core/infra/models.py`,
  3. Viola a regra "core nunca importa agente".
- ~~`AgentKey` é um `StrEnum` fechado com os 4 agentes~~ *(superado em
  2026-10-22: `ad8df96` abriu a chave, declarada pelo pacote do agente —
  PRO-1143, passo 5)*; `INSTRUCTIONS` de
  `core/infra/mcp.py` descreve 3 deles e o Linear; `_ORIGIN_BY_PREFIX` do
  tracer conhece a rota do Slack do intake.
- Único import agente → agente: `project_reporter/infra/cron.py:19` importa
  `alert_cron_failure` do portfolio_analyst.
- Boilerplate repetido em vez de extraído: montagem de `infra/agent.py`, rotas
  run/chat/history/confirm, tools MCP, esqueleto do cron e loop de publicação,
  cache por fingerprint, log de publicado, `ChatMessage` copiado 3 vezes,
  `load_dotenv` + `stdout.reconfigure` em 5 adapters, `with_structured_output`
  repetido no intake e no prioritizer.
- O `project_reporter` contorna o `build_agent` no `run`: `_ReportAnalyst`
  instancia `Langfuse()` direto porque a porta `Tracer` não devolve `trace_id`
  nem grava score.
- Modelo único global, construído no import: `ChatOpenAI("gpt-5.6-luna",
  reasoning_effort="low")` para os quatro.

### 5. Apresentação e prompt dentro do código

- `project_reporter/domain/formatting.py` (917 linhas) gera o markdown do
  status update do Linear e as mensagens de Slack em pt-BR dentro do domain,
  com a rota do front `/actions?id=`; a função de política
  (`missing_data_items`) mora lá.
- Prompt em Python, contra o ADR 0003: o `_context_block` do prioritizer
  (instruções sobre Valor/Risco/T-shirt e Linear), as mensagens de disparo nos
  casos de uso ("Analise o backlog…", "Gere o relatório…"), o cabeçalho
  `## Fatos aprovados…` no core e docstrings de tool com regra de processo e
  PRO-xxx.

### 6. A arquitetura não está disponível para o agente de código

O repo não tem `CLAUDE.md` nem `AGENTS.md`; em `.claude/skills` só há duas
skills de LiveAuth. As 4 ADRs existem em `docs/decisions/`, mas nenhuma sessão
do Claude Code as carrega por padrão, e não há teste que falhe quando uma regra
de import é violada. É literalmente o diagnóstico do Luís: *"Como é que ele vai
seguir a arquitetura [...] se você não tá disponibilizando a arquitetura para
ele?"*

## Arquitetura-alvo (proposta, não decidida)

```
apps/api/src/
├── kernel/            o core de hoje, só o que vale para QUALQUER agente
│   ├── domain/        AgentSpec, AgentKey (str), ChatMessage, review, facts, actions,
│   │                  UnknownResult; portas Tracer, PromptStore, FactStore, ActionStore,
│   │                  Conversation, ConfirmationLedger, Notifier
│   └── infra/         build_agent, build_workflow, structured_call, checkpoint, modelo por
│                      config, tracer, prompt store, repos de fatos/ações/confirmação,
│                      cron (rota agendada + publish_batch + alerta), servidor MCP, auth,
│                      db/orm, settings, repos genéricos (cache, log de publicação, event log)
├── delivery/          domínio plugável de gestão de entrega (serve Linear, Jira, ...)
│   ├── domain/        Issue/Project/Initiative/Milestone com tipos neutros (Stage, Priority,
│   │                  Health), outcomes, buckets de portfólio, fórmula como estratégia;
│   │                  portas WorkTrackerReader/Writer, OutcomeStore, StageStore
│   ├── use_cases/     sync de prioridade, reconciliação de estágio
│   └── infra/         rotas de portfólio, repos de outcome e estágio
├── adapters/linear/   único lugar com a palavra Linear: client, queries, webhook → ChangeEvent,
│                      de-para de Stage/Priority/Health, esquema de labels Valor/Risco
├── agents/<agente>/   domain (contrato e regras do agente), use_cases, infra fina, manifest
├── config/livemode/   processo do time como dado (ou no config do prompt no Langfuse)
└── app.py             composição fora do kernel: lista de manifestos → routers, MCP, cron, ORM
```

**Regra de dependência.** `agents → delivery → kernel`; `adapters/linear`
implementa portas de `delivery`; só `app.py` conhece todo mundo. Kernel não
importa ninguém; agente não importa agente; domain não importa infra,
LangChain, LangGraph nem SQLAlchemy.

**Dois níveis de portabilidade.** Mesma função noutra empresa ou ferramenta:
troca `adapters/linear` e `config/livemode`. Outro domínio: fica só o
`kernel`. Os "~5% de exemplo de domínio" do Luís são `config/livemode`.

**O teste do Luís como código.** Um teste com `ast` que varre os imports e
falha se a regra de dependência for violada, incluindo qualquer import de
`adapters.linear` fora dele mesmo e de `app.py`. Sem dependência nova. É o
guardrail que ele cobrou: *"sem tu criar os guardrails corretos"*.

**Manifesto mínimo**, só com campos que os 4 agentes reais usam (esboço da
varredura, exemplo do A10):

```yaml
agent:
  name: portfolio_analyst          # hoje AgentKey
  kind: agent                      # | workflow (intake, prioritizer) | structured_call
  purpose: "analisa o portfólio por iniciativa e propõe pontos de atenção; nunca executa"
  prompts: {system: portfolio_analyst/system-prompt}
  model: {name: gpt-5.6-luna, reasoning_effort: low, recursion_limit: 25}
  io: {run: {input: PortfolioInput, output: PortfolioModelOutput}, chat: {scope: [source_type, source_ref]}}
  catalogs: {criteria: ...}        # config do prompt no Langfuse ou YAML
  reads: [WorkTrackerReader]
  writes: [{port: publish_initiative_update, gate: cooldown, cooldown_days: 7}]
  memory: {facts: true, actions: true, result_cache: [source_type, source_ref], publication_log: [unit_id, criterion]}
  triggers:
    - {kind: cron, schedule: "0 12 * * 4"}
    - {kind: http, routes: [run, chat, history], auth: user}
    - {kind: mcp, tools: [run, chat]}
  eval: agents/portfolio_analyst/eval
```

Contra a hipótese de [[Como deve funcionar o molde de agente]]: `non_goals`
vira frase em `purpose`, `soul_schema` vira `prompts`, `permissions` vira
`auth` por trigger, `human_gates` vira `writes[].gate`, `memory_policy` vira
`memory`, `capabilities` vira `tools/reads/writes`. Nenhum agente usa esses
campos como estrutura própria hoje.

## Ordem de execução

Mesma regra do PRO-1005: cada PR é só path, só renomeação ou só lógica.

1. **Arquitetura antes de código** (método do Luís). ADR 0005: três camadas e
   "processo é dado", superando a regra "usado por mais de um agente → core".
   ADR 0006: manifesto de agente e registro fora do kernel. `CLAUDE.md` na raiz
   apontando para as ADRs e para a regra de dependência. O teste de imports.
   Revisar com o Luís antes da saída dele.
2. **Desgrudar sem mudar comportamento.** Composição sai do core (`app.py`,
   `models.py` recebem a lista de agentes); `alert_cron_failure` vai para o
   kernel ou vira porta `Notifier`; `Tracer.callback()` devolve `Any`;
   settings do Linear opcionais; higiene dos achados avulsos.
3. **Separar o domínio de entrega.** Mover tipos, portas, `stage`, `outcomes` e
   `portfolio` para `delivery/`; parser de webhook e `linear_adapter` para
   `adapters/linear/`. Depois, a tradução real: `Stage`, `Priority` e `Health`
   neutros, mapeados no adapter. Toca o prompt do A14, que hoje emite
   `onTrack`.
4. **Processo vira dado, começando pelo `Criterion`.** Catálogo único
   `{key, label, hint, severity}`; `criterion: str` validado contra ele; schema
   do LLM montado do catálogo na montagem (enum dinâmico, o modelo continua
   restrito); rota que expõe o catálogo; front apaga `criteria.ts`; eval
   dirigido por metadata. Depois, a mesma técnica para rota do intake, T-shirt,
   esquema Valor/Risco, estágios, fórmula e cortes, cadência e kill switch, e
   uma `ReporterPolicy` para os cortes e a escada do A14.
5. **Molde.** `AgentSpec` + registro; `AgentKey` aberto; `INSTRUCTIONS`
   composto dos manifestos; origem de trace declarada pelo trigger. Extrair
   para o kernel a trava de confirmação (prioritizer adota), `structured_call`,
   rota agendada + `publish_batch` e os repos genéricos. `Tracer.score`, para o
   A14 voltar ao `build_agent`. Apresentação fora do domain (renderer por canal
   com templates de config) e os prompts que ainda estão em Python indo para o
   Langfuse.

**Critério de pronto.** O teste de imports passa, e um 5º agente de exemplo
nasce só com manifesto, contrato e caso de uso, sem editar o kernel.

## Achados avulsos

- **Bug.** `_ACTIVE_ISSUE_FILTER` (`core/infra/linear/queries.py:40`) exclui
  `completed`/`canceled` mas não `duplicate`; `Issue.is_active` e a query de
  esforço (l. 130) excluem. `list_issues(active_only=True)` devolve duplicadas
  como ativas, e elas entram no workload por pessoa do A10.
- **Dado pessoal.** `project_reporter/domain/formatting.py:84` tem o e-mail de
  um colega num comentário.
- **Texto divergente.** O template de cobrança diz "report de segunda"
  (`formatting.py:564`), mas a cadência é quinta.
- **Código morto.** `stage_inconsistent` e `count_issues_without_milestone` do
  A14 e `needs_ack` do intake não têm chamador fora dos testes.
- **Eval no banco do app.** O eval do A10 rejeita fatos e ações com
  `reviewed_by="eval-cleanup"` e o do A14 apaga o projeto de teste, no banco
  configurado, embora `run_a10`/`run_a14` aceitem stores injetados.
- **Efeitos no import.** `get_tracer()` em `core/infra/llm.py`, a chave do
  Linear em `client.py` e `stdout.reconfigure` em vários módulos.

## Em aberto

- **Validar com o Luís** antes de 2026-10-31. Ele propôs um projeto conjunto
  "por fora", a partir do desenho do quadro; as duas ADRs do passo 1 são o
  candidato natural.
- **Onde mora o catálogo de critérios.** `config` JSON do prompt no Langfuse
  mantém a decisão "sem deploy" de `prompts-langfuse.md`. YAML versionado no
  pacote deixa revisável em PR.
- **LangGraph nos `use_cases`** do intake e do prioritizer. Não é I/O, mas a
  porta `Conversation` não isola o harness. Ou uma ADR declara LangGraph como
  linguagem do runtime, ou o grafo desce para infra. Coerente com o Luís: loop
  vs workflow é *"zero"* para o molde.
- **Fronteira do pacote de entrega.** Nome (`delivery/`?) e se `outcomes` e
  `stage` ficam nele ou viram dado do processo.
- **Identificadores pt-BR em enums** que cruzam API e banco (`saudavel`,
  `atencao`...): códigos neutros com rótulo no catálogo, ou manter.
