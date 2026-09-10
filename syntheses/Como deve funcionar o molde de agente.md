---
type: synthesis
status: active
updated: 2026-09-10
date: 2026-09-01
aliases: [molde de agente, agent blueprint, agent template configurável]
tags: [agent-flow, agents, harness, soul, configuration]
---

# Como deve funcionar o molde de agente

## Origem

msilva, 2026-09-01, de uma conversa com [[Luís Fernandez]]: faltam (a) editar
o comportamento dos agentes sem programar — dashboard tipo Langfuse pra
atributos da Soul — e (b) um **molde de agente** reutilizável. A Soul V1 foi
decidida no mesmo dia e ficou registrada aqui por estar ligada ao molde.

## Anatomia: molde, instância, runtime

[[Agent Harness Template]] já define a anatomia: Trigger + Trigger Channel →
Input → Harness (`Soul`, Skills, Tools, MCP) → Output. O molde transforma
essa anatomia num **contrato reutilizável e instanciável**, não um segundo
modelo concorrente:

- **molde** — quais partes existem, campos obrigatórios, variações
  permitidas, invariantes que toda instância preserva;
- **instância** — valores concretos de um agente (objetivo, Input/Output,
  Soul, capabilities);
- **runtime** — executa a instância, conecta adapters/Tools/MCP/memória/
  observabilidade.

Hipótese de trabalho, ainda não fechada com Luís.

## Hipótese de estrutura

```yaml
kind:
purpose:
non_goals:
trigger:
input_schema:
output_schema:
soul_schema:
capabilities:
permissions:
human_gates:
memory_policy:
success_criteria:
evaluation_dataset:
```

O molde não é um prompt-base — é o **contrato de produto e operação**: o que
dispara o agente, o que ele aceita, o que entrega, o que pode fazer, como a
qualidade é verificada.

## Molde único, sem família de arquétipos — resolvido 2026-09-10

Cogitado: base comum + arquétipos específicos (leitor/analista, consultor,
operador com permissão de escrita, monitor disparado por eventos,
orquestrador), pra evitar dois extremos — molde genérico demais (sem contrato
real) ou molde por agente (sem reaproveitamento). [[Harness engineering for
coding agent users]] registra ideia próxima (bundles por topologia de
serviço), sem validar que esses sejam os arquétipos certos aqui.

**Decisão** (sessão de código em `livemode-fluxo-agentico`, 2026-09-10):
molde-base único por ora. As duas instâncias reais (A10, A14) são o mesmo
arquétipo; não há evidência de um segundo. Revisitar quando um agente
estruturalmente diferente (escrita com aprovação, disparado por evento) for
construído de fato.

## Componente pode não ser agente independente

Parte dos 14 candidatos de [[Agent Flow]] pode existir como Skill ou Tool
dentro do harness de outro agente, sem Trigger/Input/Output próprios
([[Agent Harness Template]]). Primeiro campo conceitual do molde talvez seja
a natureza do componente: `standalone_agent`, `skill`, `tool` ou `guardrail`.
Ainda não discutido com Luís quais dos 14 se enquadram em qual.

## Configuração não programática e a Soul

O molde é o schema que torna um dashboard de config possível — sem ele,
"editar sem código" vira editor de prompt livre; com ele, vira configuração
estruturada, versionada, testável.

[[Desenho do agente LangGraph para A10+A14]] usa Langfuse Prompt Management
pro prompt de tarefa. A Soul (fonte comportamental, independente desse
prompt) fechou sua própria fronteira em 2026-09-01: `soul_versions` guarda
texto direto por versão, `soul_composition` compõe profile+fragments de
forma plana — sem YAML, sem campos aninhados, sem propagação automática. Ver
[[2026-09-01 Modelar SOUL em tabelas com composição plana]] pro schema. V1 já
implementado (PRO-503, Done); conteúdo real dos fragments segue em aberto.

## Vocabulário de domínio vs. molde — resolvido 2026-09-02

Pergunta de origem (msilva, comparando com a camada de ontologia da
[[Bossabox Engagement]]): o molde deveria carregar vocabulário de domínio
compartilhado entre sistemas de registro (Linear, GitHub, futuros)? Resolvido
em duas partes:

**1. Molde e vocabulário de domínio são eixos diferentes.** Molde é sobre
*como o agente opera*; vocabulário de domínio é sobre *o que as coisas
significam*, e mora num módulo compartilhado (padrão de `linear_client.py`),
não dentro do contrato de cada instância.

**2. "Vocabulário de domínio" misturava duas peças sob um nome só:**

- **Semântica mecânica de campo** (`Issue`, `state_type`, `Milestone`) —
  resolvida pelo schema da própria Tool via tool-calling, sem fragment. Regra
  hoje escondida em código (`a10/tools.py`:
  `CLOSED_STATE_TYPES = {"completed", "canceled", "duplicate"}`) deveria
  idealmente virar campo computado no tipo (`Issue.is_closed`) — não decidido
  se vale migrar já ou só quando um segundo sistema de tickets aparecer.
- **Metodologia de estruturação de demanda** (como decompor projeto →
  milestone → issue) — não é campo de tipo, precisa de fragment/skill,
  deliberadamente independente de qual adapter escreve o resultado depois.
  Candidato a fragment compartilhado entre A7/A14/A2 (não confirmado com
  Luís); base de conteúdo candidata: o "ticket template" de [[Gabriel Packer
  - DAG-driven agent orchestration]] (escopo, critério de aceite,
  `blockedBy`, rollout/kill switch). Critério fragment-vs-skill: sempre
  relevante e pequeno → fragment; às vezes relevante ou grande → skill (mesmo
  narrow fetching já validado no A10).

**Nome a evitar**: "vocabulário de domínio" sozinho, ambíguo entre as duas
peças acima — nomear como "metodologia de estruturação de demanda" ou
equivalente.

Mesmo padrão vale do lado da **escrita**, generalização de msilva a partir do
mesmo gap: qualquer agente que grava num sistema externo (A10, A14, não só
A14) deveria ter um adapter por plataforma-alvo traduzindo do modelo de
domínio interno pro formato nativo dela. Não é capacidade nova a construir
agora — é o mesmo gap de hoje (só resolvido pro Linear), generalizar quando
um segundo sistema de registro real aparecer.

**Auditoria de código relacionada** (2026-09-01, `linear_client.py`/
`repo_tools.py`/`github_client.py`): GitHub não precisa de vocabulário de
domínio próprio (dado bruto = "sinal", correto como está); achado real foi
**duplicação** de modelo de PR (`GithubPR` dentro de `Issue` vs. dict solto
de `get_pr_status`) — corrigido: `GithubPR` ganhou os campos que faltavam,
`get_pr_status` passa a devolver `list[GithubPR]`. `Issue`/`Project`/
`Milestone` já genéricos, sem trabalho pendente.

**Memória do sistema agêntico segue sem dono** — Luís pediu em 2026-08-20 pra
não desenhar isso ainda, assentar as entidades primeiro. Rastreado como spike
[PRO-517](https://linear.app/projetos-livemode/issue/PRO-517/revisitar-se-ja-da-pra-desenhar-a-memoria-compartilhada-dos-agentes):
revisitar com ele se a objeção ainda vale, agora que A10/A14 e o molde
avançaram.

**Desenho feito mesmo assim, 2026-09-10** — ver
[[2026-09-10 Memória de fatos do agente (agent_facts)]]: padrão
propõe→aprova (`agent_facts`, individual + global), sem implementação
autorizada. A metade global é exatamente o que este spike pede pra revisitar
com Luís antes de codar; a metade individual não tem objeção registrada.

## Slot MCP: refinado numa sessão de código, 2026-09-10

Discussão em `livemode-fluxo-agentico` (branch `langgraph`), puxada por
msilva a partir da própria anatomia do Harness, pra entender melhor o slot
MCP antes de mexer em código. Nenhuma implementação foi feita.

**MCP é um adapter, mas com uma propriedade que os adapters manuais
(`linear_client.py`, `github_client.py`) não têm: plugável em runtime, do
lado do host.** Adicionar/trocar integração vira mudança de config (entrada
nova numa lista de servidores — nome, transporte, comando/URL, auth), não
código novo no agente. Mesmo mecanismo do `.mcp.json` do Claude Code — a
versão Python equivalente é o `MultiServerMCPClient` da lib
`langchain-mcp-adapters` (cogitada e descartada uma vez, ver `HANDOFF.md` do
repo), que aceita o mesmo formato de config.

**A plugabilidade de transporte sozinha não basta.** Só funciona de verdade
se o agente raciocina sobre "quais tools existem agora" (via tool-calling
schema) em vez de integração fixa hardcoded no prompt/lógica — bate com a
resolução acima sobre semântica de campo via schema de tool.

**Duas variantes de transporte com implicação de deploy diferente**: stdio
(subprocesso) exige processo persistente — funciona local ou serviço
contínuo, não em função serverless (ex.: Vercel); remoto/HTTP funciona em
qualquer lugar. Relevante pro `livemode-fluxo-agentico`, que roda em função
serverless da Vercel.

**Correção importante, motivada por uma pergunta de segurança de msilva**: em
um runtime **multi-tenant** — como o dashboard do A10/A14 hospedado na
Vercel, acessível por qualquer `@livemode.com` logado via `auth_gate.py` —
"o host decide quais MCPs existem" não basta. Um MCP com credencial de uma
pessoa (ex.: Slack pessoal) configurado no nível do host vazaria
acesso/identidade pra todo mundo que usa o dashboard. Versão correta: **MCP
plugável é por identidade, não por deployment**, sempre que o runtime é
compartilhado — cada `email` (já disponível via `auth_gate.get_current_user`,
usado hoje por `require_admin`) teria seu próprio conjunto de MCPs, wireado
por request, não um `mcp_servers.json` único carregado no boot. Numa sessão
pessoal de Claude Code isso é de graça (single-user); num serviço hospedado
compartilhado, é requisito explícito de desenho.

**Por que nada disso foi codado**: mesma regra de amostra do molde inteiro —
só existem 2 integrações reais hoje (Linear, GitHub), resolvidas com adapter
direto, nenhum host pediu uma terceira em runtime ainda. Um spike isolado
(testando Slack via MCP) foi cogitado mas parado antes de codar: sem token de
Slack disponível, sem `langchain-mcp-adapters` instalado, questão de
identidade acima ainda não resolvida na hora. Rastreado no mesmo espaço de
PRO-517.

## Slot Agent-as-tool: agente chamando agente — desenhado, 2026-09-10

Mesma sessão de código, puxado de uma pergunta concreta sobre `outcomes.py`
(A10 lê o outcome que A14 escreve, store compartilhado — deveria A10 chamar
A14 direto em vez disso?). Generalizado por msilva: agentes deveriam poder
se comunicar quando necessário pra alcançar o objetivo, não só trocar fatos
por um store passivo. Ver [[2026-09-10 Agentes expostos como tool uns para
os outros]] pro desenho completo.

**Decisão**: por padrão, todo agente pode chamar qualquer outro através do
contrato público dele (`Input → Output`), via um primitivo genérico
`ask_agent(agent_key, question)` — não um tool bespoke por par, não gateado
por critério/contexto (cogitado, descartado: msilva prefere disponível por
padrão). Não quebra "cada agente define a própria interface" — quebraria só
se um agente importasse o pacote interno do outro; chamar pelo contrato
público é o mesmo acesso que qualquer consumidor externo já tem.

**Guardrails tratados como requisito, não simplificação opcional** —
justamente por ser padrão, não exceção pontual: pilha de chamada (lista de
`agent_key` já visitados, propagada via config) pra recusar ciclo antes de
gastar token (o `recursion_limit` de hoje só protege o loop *dentro* de um
agente, uma chamada cross-agent é um `invoke()` novo); profundidade máxima
de cadeia independente de ciclo; rastreamento cross-agent como span/trace
ligado no Langfuse.

**Escopo maior que `agent_facts`** — sistêmico desde o início (sustenta o
roteamento A2→A3/A4/A7), não uma decisão local de A10/A14. Nenhuma
implementação autorizada; alinhar com Luís antes de codar pesa mais aqui
que no `agent_facts`.

## Slot Skills (sentido 1: conteúdo sob demanda) — fechado, 2026-09-10

Mesma sessão de código do slot MCP acima. Cobre só o primeiro dos dois
sentidos de "Skill" já registrados em [[Agent Harness Template]] — conteúdo
carregado sob demanda dentro do Harness, paralelo a Tools. O segundo sentido
(componente que não vira agente de primeiro nível, aninhado no harness de
outro) segue em aberto, não tocado nesta sessão.

**O que é**: mesmo tipo de conteúdo que um Fragment de SOUL — texto,
metodologia, procedimento, versionado em `soul_versions`. A diferença é a
**política de carregamento**: Fragment é sempre composto no profile; Skill só
entra no contexto quando o gatilho bate.

**Diferença de Tool**: Tool é I/O — o modelo chama, recebe dado ou executa
ação. Skill não é chamada, é instrução injetada que muda *como* o modelo
interpreta o dado que as Tools já trouxeram. Exemplo concreto (hipotético,
não construído): uma Tool `list_issues()` devolve dado bruto; uma Skill
"como julgar escopo descontrolado" mudaria como o A10 interpreta esse dado
pro critério `escopo_descontrolado`, sem buscar dado novo nenhum.

**Não é plugável como MCP.** MCP resolve acesso a sistema externo por
identidade; Skill é conhecimento que o próprio time autora e versiona, sem
credencial de terceiro envolvida. Isolar por usuário/host não tem evidência
de necessidade — mesma regra já aplicada à decisão de arquétipos acima.

**Mecanismo de carregamento**: dispatch determinístico, não classificador de
relevância (ao contrário da descoberta de Skill do Claude Code, por matching
de descrição). Pro A10, chaveado pelo critério em avaliação — só carrega
skill quando o critério é julgado por LLM (`priorizacao_desalinhada`,
`escopo_descontrolado`); os determinísticos (`iniciativa_estagnada`,
`gargalo_de_capacidade`, resolvidos em `rules.py`) nunca tocam skill
nenhuma. Pro A14, chaveado por tipo de tarefa (ex.: estruturar demanda
carrega skill; calcular progresso por milestone não).

**Implementação, quando materializar**: **tabela própria** (`skills`), não
`soul_versions`. Cogitado primeiro reaproveitar `soul_versions` com
`kind="fragment"` (mesma forma de conteúdo) ou um terceiro valor
`kind="skill"` — descartado: `soul_versions` já significa algo específico
(comportamento/identidade, "fonte comportamental do agente, independente do
prompt de tarefa"), e skill é metodologia situacional, eixo conceitual
diferente (mesma separação já fechada em "Vocabulário de domínio vs. molde"
acima). Um `kind="skill"` resolveria a forma do dado, mas deixaria a skill
**visível pra `soul_composition`** — vazando pra lista de candidatos de um
profile, sem nada impedindo estruturalmente. Tabela separada resolve isso de
graça: skill fisicamente fora do que `soul_composition` enxerga, não uma
convenção de filtro que alguém pode esquecer de aplicar.

Forma mínima, mesmo padrão de versionamento que a SOUL já usa, sem
reinventar:

```
skills
  id
  name
  version
  content
  created_at
  created_by
```

Sem binding/promoção própria (mesma decisão de manter simples): skill
referenciada por `id`/versão fixa direto no código (`agent.py`), buscada ad
hoc por nome só no momento em que o critério/tarefa que a dispara acontece —
sem passar por `soul_composition`. Perde a ergonomia de promover sem deploy
que o profile tem; revisitar se/quando o dashboard de SOUL precisar disso
pra skill também.

**Nada codado** — mesma postura do resto do molde: conceitual até um
critério/tarefa real pedir a separação. Hoje `priorizacao_desalinhada` e
`escopo_descontrolado` (candidatos mais óbvios) ainda vivem só no prompt
principal do A10, sem skill dedicada.

**Critério real apareceu, 2026-09-10** — ver
[[2026-09-10 A10 - critérios julgados por skill, não determinísticos em Python]]:
todo critério do A10 (incluindo os que eram determinísticos em Python)
passa a exigir skill própria, mais o critério novo `fluxo_represado`. A
tabela `skills` deixa de ser hipotética, vira pré-requisito real — ainda
sem implementação autorizada. Mesma sessão também resolveu o dispatch de
skill no chat (sem critério fixo pra disparar por, ao contrário do batch):
skills relevantes entram sempre no prompt do chat, sem mecanismo de
roteamento.

**Confirmado no A14 também, mesmo dia** — ver
[[2026-09-10 A14 - retrofit dos verdicts para skill (mesmo princípio do A10)]]:
os três verdicts que `a14/rules.py` decidia sozinho (`health`, alerta de
atraso, sinal de PR parado) passam pelo mesmo retrofit — Python só número,
skill decide gravidade. Confirma que `skills` não é peculiaridade do A10,
serve os dois agentes com critérios próprios de cada um.

## Trigger e Trigger Channel: sem representação em código — sessão de código, 2026-09-10

Puxado por msilva ao revisitar a pergunta "qual problema o molde resolve
primeiro" (ver resolução acima: **nortear criação de agentes, padronização,
e uma factory low-code futura**). Auditado no repo real
(`livemode-fluxo-agentico`, `grep -i trigger`): zero representação. O único
hit é homônimo — *database triggers* do Postgres em `db.py`
(`soul_versions_immutable`, etc.), sem relação com o conceito do Harness.
Hoje Trigger/Channel só existe como fato observável de fora (qual
entrypoint chamou), nunca declarado no código.

**Trigger (Human|Machine) descartado por ora — sem evidência.** Auditando os
3 pontos reais de invocação (`cron.py` → Machine, rota `/run` de
`a10/api.py`/`a14/api.py` → Human, blocos `__main__` → Human), **Channel
determina Trigger 1:1 hoje** — nenhum canal atende os dois tipos de
disparo. Separar em dois campos agora não carregaria informação nenhuma além
do que `TriggerChannel` sozinho já dá; mesma regra de amostra aplicada ao
resto do molde. Valor real só aparece no dia em que um canal (o candidato
óbvio é HTTP) atender tanto humano quanto outro agente chamando
programaticamente — aí sim Trigger vira informação, não redundância.

**TriggerChannel materializa sozinho — valor já comprovado, não hipotético.**
`cron_runs` (`cron_history.py`) guarda só `id, ran_at, result`, sem campo de
canal. Na investigação de um bug real desta mesma sessão (cron da Vercel disparando
via GET contra uma rota que só aceitava POST, corrigido em `cron.py:249`),
diagnosticar "essas 6 execuções foram automáticas ou manuais?" exigiu inferir
pelo horário bater perto de 10:00 UTC, em vez de uma query direta. Um campo `trigger_channel` gravado no `cron_runs` (e propagado
como metadata de trace do Langfuse) transformaria isso numa filtragem
trivial.

**Onde materializar**: `Literal["cron", "http", "cli"]` em `domain.py`,
mesmo padrão de `Criterion`/`SourceType` já usado no repo. Passado como
parâmetro nomeado em `run_a10`/`run_a14` (mesmo lugar de `reader`), não como
campo de `A10Input`/`A14Input` — misturaria infra (como foi chamado) com
domínio (o que o agente processa), quebrando a regra já fixada de "contrato
definido por formato, nunca por quem chama".

**Achado à parte: "cron" não é um canal que uma factory consiga criar
sozinha em runtime, ao contrário de HTTP/CLI.** HTTP e CLI são canais que o
próprio runtime cria (rota nova registrada ao subir, bloco `__main__` novo)
— não tocam config de deploy. Cron na Vercel é **declarativo, resolvido em
tempo de deploy** (`vercel.json`, array `crons`, lido só quando a app é
publicada — não uma API que se chama em runtime pra agendar algo novo; foi
exatamente essa característica que causou o bug do cron nunca disparando
antes desta sessão). Uma factory criar um canal "cron" de verdade pra um
agente novo pedido por usuário exigiria: (1) gerar/registrar uma rota pro
agente, (2) adicionar entrada no `crons` do `vercel.json`, (3) **disparar um
deploy real de produção** — permissão de risco/blast-radius bem maior que
escrever uma linha em tabela. Acompanha as outras peças do molde: nada disso
foi codado, fica registrado como limite conhecido da ambição de factory.

## Questões em aberto

- ~~Qual problema concreto o molde precisa resolver primeiro: criação de
  novos agentes, padronização dos existentes, delegação da configuração para
  pessoas não técnicas ou comparação entre implementações?~~ **Resolvido em
  2026-09-10** (msilva): nortear a criação de novos agentes, permitindo
  padronização e, no horizonte, uma **factory** — criação de agente novo de
  forma não programática/low-code. Isso eleva o peso de duas peças que antes
  pareciam só detalhe: Trigger/Trigger Channel precisam ser configuráveis sem
  código (ver seção acima), e o molde precisa virar **dado máquina-legível**,
  não só prosa de wiki — a "Hipótese de estrutura" (bloco `yaml` acima) é o
  rascunho desse schema, não só referência conceitual. Ainda não é hora de
  construir a factory (duas instâncias do mesmo arquétipo não são amostra
  suficiente) — a direção é registrar candidatos a campo configurável
  conforme aparecem, pro terceiro agente real testar o schema de verdade.
- Quais campos pertencem ao molde e quais só aparecem na instância?
- Quais mudanças podem ser feitas sem código, e quais exigem revisão técnica?
- Como versões de molde e de instância se relacionam quando o molde evolui?
- Quais invariantes não podem ser sobrescritas por uma instância?
- Como o molde representa componentes que não são agentes independentes?
- Quais agentes de fato compartilham a mesma metodologia de estruturação de
  demanda (A7, A14, A2?) — não confirmado com Luís nem testado contra os
  specs reais de cada um.
- Vale a pena migrar `CLOSED_STATE_TYPES` (e regras hardcoded parecidas) de
  constante Python pra campo computado no tipo (`Issue.is_closed`) agora, ou
  só quando um segundo sistema de tickets entrar?
- Vale usar o tier gratuito da Bossabox pra validar/acelerar a camada de
  memória compartilhada em vez de reconstruir do zero? (ver [[What Bossabox's
  Assessment suggests for Agent Flow]], ponto 7)
