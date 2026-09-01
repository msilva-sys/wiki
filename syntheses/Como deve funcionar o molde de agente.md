---
type: synthesis
status: active
updated: 2026-09-01
date: 2026-09-01
aliases: [molde de agente, agent blueprint, agent template configurável]
tags: [agent-flow, agents, harness, soul, configuration]
---

# Como deve funcionar o molde de agente

## Origem da questão

msilva relatou em 2026-09-01 dois pontos levantados numa conversa com
[[Luís Fernandez]]:

1. permitir adicionar ou editar o comportamento dos agentes de forma não
   programática — com um dashboard semelhante ao Langfuse no qual seja possível
   configurar atributos da Soul;
2. ter uma forma de **molde de agente**.

Esta página registrava inicialmente só a segunda questão — a Soul seria a
próxima investigação, não definida aqui por antecipação. **Isso mudou no
mesmo dia**: a Soul V1 foi decidida horas depois (ver seção abaixo) e acabou
registrada aqui mesmo, por estar diretamente ligada ao desenho do molde.

## Relação com o modelo existente

[[Agent Harness Template]] já descreve a anatomia genérica proposta por Luís:
Trigger + Trigger Channel → Input → Harness (`Soul.md`, Skills, Tools, MCP) →
Output. O molde parece ser a transformação dessa anatomia em um **contrato
reutilizável e instanciável**, não um segundo modelo concorrente.

A distinção inicial é:

- **molde** — define quais partes existem, quais campos são obrigatórios, quais
  variações são permitidas e quais invariantes toda instância deve preservar;
- **instância** — escolhe valores concretos para um agente, como objetivo,
  entradas, saídas, Soul e capabilities;
- **runtime** — executa a instância e conecta adapters, Tools, MCPs, memória e
  observabilidade.

Essa separação é uma hipótese de trabalho, não uma decisão tomada com Luís.

## Hipótese inicial de estrutura

Um molde poderia declarar, no mínimo:

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

O ponto central é que o molde não contém apenas um prompt-base. Ele contém o
**contrato de produto e operação** do agente: o que o dispara, o que aceita, o
que entrega, o que pode fazer e como sua qualidade será verificada.

## Um molde universal ou uma família de moldes

Uma base comum pode coexistir com arquétipos mais específicos, por exemplo:

- leitor/analista;
- consultor;
- operador com permissão de escrita;
- monitor disparado por eventos;
- orquestrador.

Isso evita dois extremos ainda não avaliados: um molde universal tão genérico
que não impõe contrato nenhum, ou um molde diferente para cada agente que não
gera reaproveitamento real. [[Harness engineering for coding agent users]] já
registra ideia próxima — bundles de guias e sensores por topologia de serviço —,
mas não valida que esses sejam os arquétipos corretos para [[Agent Flow]].

## Restrição importante

O molde não deve transformar automaticamente toda capacidade em agente
independente. [[Agent Harness Template]] registra que parte dos 14 candidatos
pode existir como Skill ou Tool dentro do harness de outro agente. Portanto, o
primeiro campo conceitual talvez seja a própria natureza do componente:
`standalone_agent`, `skill`, `tool` ou `guardrail`.

## Relação com configuração não programática

O molde fornece o schema que torna um dashboard possível: a interface sabe
quais campos expor, quais valores validar e qual configuração materializar para
uma instância. Sem molde, “editar o agente sem código” tende a virar apenas um
editor de prompt livre; com molde, pode virar configuração estruturada,
versionada e testável.

[[Desenho do agente LangGraph para A10+A14]] já adotou Langfuse Prompt
Management para versionar prompts, promover versões por label e ligar cada
trace à versão executada. A
[[2026-09-01 Modelar SOUL em tabelas com composição plana|decisão da SOUL]]
respondeu a fronteira: a SOUL é fonte independente, persistida em
tabelas próprias; o Langfuse pode receber a SOUL efetiva e seus identificadores
para observabilidade, mas não é sua fonte de verdade.

## Decisão V1 da SOUL — 2026-09-01

msilva escolheu um modelo simples e composable: `soul_versions` guarda um bloco
de texto direto por versão e `soul_composition` é a join table ordenada entre um
profile e seus fragments. A composição é plana, sem YAML como fonte de verdade,
sem campos comportamentais aninhados e sem propagação automática de mudanças.
Ver [[2026-09-01 Modelar SOUL em tabelas com composição plana]] para o schema e
as regras.

## Paralelo com a camada de ontologia da Bossabox — 2026-09-01

msilva perguntou, em chat, como replicar a lógica de "camada de ontologia" a
que a [[Bossabox Engagement]] chegou com a OS V2: uma camada de sustentação
tool-agnostic — um vocabulário comum ("sistema de inteligência") por baixo de
qualquer ferramenta que o time já use (Jira, GitHub, Linear, Notion), em vez de
travar a lógica dentro de uma delas. Cruzando essa pergunta com o que já existe
nesta página e em páginas vizinhas:

**Peças que já são essa lógica, em miniatura:**

- **Contrato / ports-and-adapters** ([[Vocabulário do Fluxo Agêntico]],
  [[Desenho do agente LangGraph para A10+A14]]) — regra central já registrada:
  *"Contrato: definido por formato, nunca por quem chama."* O `team_id` foi
  tirado de dentro do agente e virou campo de `Entrada` justamente para o
  agente não precisar saber qual ferramenta o alimenta. Hoje existe só para o
  Linear.
- **Agent Harness Template** ([[Agent Harness Template]]) — Trigger/Trigger
  Channel (Slack, Webhook, Cron) separado do Input generaliza o mesmo
  princípio para qualquer canal.
- **O próprio molde** (esta página) é o candidato mais direto: um contrato
  reutilizável e instanciável, independente da implementação — a mesma
  ambição da ontologia da Bossabox, hoje ainda com escopo mais estreito
  (comportamento/contrato do agente, não vocabulário de domínio entre
  sistemas de registro).

**Duas lacunas concretas que faltam para virar de fato uma camada de
ontologia, não só contratos de agente:**

1. **O mapeamento DTO≠domínio só existe para o Linear.** `linear_client.py`
   já separa o shape cru do GraphQL de um modelo de domínio estável
   (`Issue`, `Project`, `Milestone`). A Bossabox generaliza exatamente esse
   padrão para Jira/GitHub/Confluence/Notion ao mesmo tempo. O GitHub já foi
   integrado ([[2026-08-31 A10 e A14 ganham acesso real ao GitHub]]), mas via
   `repo_tools.py` separado — não está claro se compartilha vocabulário de
   domínio com o Linear ou se é um segundo silo.
2. **"Sinal" e "memória do sistema agêntico" não têm dono** — já registrado
   como lacuna aberta em [[Vocabulário do Fluxo Agêntico]] e em
   [[Agent Flow]] desde 2026-08-20 (Luís: organizar essa memória bem é
   "talvez a coisa mais importante do projeto todo"). É exatamente a peça que
   a camada de sustentação da Bossabox resolve — contexto que sobrevive entre
   ferramentas e entre agentes. Hoje a memória que existe (`a14/memoria.py`)
   é por agente e por projeto, o oposto de uma camada compartilhada.

**Caminho prático de replicar, ainda não decidido com Luís:**

- Quando o molde for fechado, garantir que ele carregue não só o contrato
  comportamental do agente, mas também um vocabulário de domínio
  compartilhado — hoje não está claro se essas são a mesma peça ou duas
  questões distintas sendo tratadas como uma.
- Estender o padrão DTO-vs-domínio do `linear_client.py` para os demais
  sistemas de registro conforme forem entrando.
- Dar dono explícito à "memória do sistema agêntico" — a lacuna que mais se
  parece com o que falta para replicar a lógica da Bossabox, mais do que os
  contratos por agente (que já andam bem).

**Atacada primeiro, 2026-09-01**: msilva decidiu priorizar a lacuna de
memória sobre a de DTO≠domínio — o padrão DTO já tem modelo pra copiar
(`linear_client.py`), a memória não tem nem padrão nem dono. Como Luís já
havia pedido explicitamente, em 2026-08-20, para **não desenhar memória
ainda** (assentar as entidades primeiro — ver [[Vocabulário do Fluxo
Agêntico]]), o primeiro passo não é desenho, é revisitar com ele se essa
objeção ainda se sustenta agora que A10/A14 estão em construção e o molde
está sendo fechado. Rastreado como spike:
[PRO-517](https://linear.app/projetos-livemode/issue/PRO-517/revisitar-se-ja-da-pra-desenhar-a-memoria-compartilhada-dos-agentes).

Ver também o ponto 7 de [[What Bossabox's Assessment suggests for Agent
Flow]]: ainda em aberto se compensa usar o tier gratuito da Bossabox para
validar/acelerar essa camada em vez de reconstruí-la do zero.

**Lacuna 1 auditada e corrigida no código real, 2026-09-01** — lida
`linear_client.py`, `repo_tools.py`, `github_client.py` e `a10/tools.py` no
repo `livemode-fluxo-agentico` (não só hipótese de wiki):

- **Não é um silo completo.** `linear_client.Issue` já tem um campo
  `github_pr: GithubPR | None` — um PR vinculado a uma issue do Linear já
  entra como parte do domínio da própria issue. Esse ponto de integração
  está certo.
- **Fora desse ponto, o GitHub não tem vocabulário de domínio — e está
  certo assim.** `repo_tools.py`/`github_client.py` devolvem dicts crus
  (arquivos, commits, branches, métricas de repo) direto como tool output
  pro LLM em `a10/tools.py`/`a14/tools.py`. Isso bate com o padrão de
  **"sinal"** já definido em [[Vocabulário do Fluxo Agêntico]] — dado bruto
  que o agente julga, não precisa de tipo Pydantic pra isso. Não é gap.
- **Achado real: duplicação, não silo.** Um Pull Request era modelado
  **duas vezes**, sem relação entre si — `GithubPR` (dentro de `Issue`:
  `status, url, repo_name, created_at, updated_at, merged_at, closed_at`) e
  o dict solto de `get_pr_status`/`repo_pr_status` (`number, title, url,
  author, created_at, updated_at, draft, review_status, ci_status`). Mesma
  coisa do mundo real, dois shapes, zero cross-check.
- **`Issue`/`Project`/`Milestone` não precisam de trabalho nenhum agora.**
  Os tipos já são genéricos (sem nome nem shape do Linear vazando) — a
  tradução específica do GraphQL fica isolada nas funções de
  `linear_client.py`. Se uma segunda ferramenta de tickets aparecer um dia,
  o trabalho é só escrever um novo mapper pros mesmos tipos, não redesenhar
  nada. Não há hoje uma segunda fonte competindo por esses conceitos —
  GitHub tem PR/commit/branch, que são coisas diferentes, não um Project ou
  Milestone alternativo.
- **Detalhe pra guardar, não pra agir agora**: `a10/tools.py` tem
  `CLOSED_STATE_TYPES = {"completed", "canceled", "duplicate"}` — hardcoda
  os valores específicos que o *Linear* usa pro campo `state_type`. O tipo
  `Issue` é genérico, mas esse ponto de consumo assume o vocabulário do
  Linear como universal. Só vira problema se uma segunda fonte de tickets
  entrar com categorias diferentes; não vale generalizar preventivamente.

**Fix aplicado, mesma sessão**: `GithubPR` ganhou os campos que só existiam
no dict solto (`number, title, author, draft, review_status, ci_status`,
todos opcionais); `github_client.get_pr_status` passa a devolver
`list[GithubPR]` em vez de `list[dict]`; `repo_tools.repo_pr_status`
serializa pra dict só na borda (`model_dump(mode="json")`), mesmo padrão que
`linear_client.list_issues()` já usa em `a10/tools.py`. Testado: imports OK,
suite `a10.test_agent` (`unittest`) passa. **Ainda não commitado** — o repo
já tinha bastante trabalho pendente de outras sessões (SOUL, `db.py`,
frontend) não relacionado a esta mudança; a decisão de como/quando
commitar cada frente fica com msilva.

**Correção de nome, mesma passada**: esta página e o [[log]] citavam
`a14/memoria.py` — o arquivo real é `a14/memory.py`. Corrigido aqui; ver
nota equivalente no `log.md`, que não é reescrito por ser append-only.

## Generalização: workflow autônomo guiado pelo vocabulário interno, adapters também na escrita — msilva, 2026-09-01

Msilva reformulou, em chat, a mesma lacuna acima como princípio arquitetural
mais amplo, não restrito ao molde do A14: qualquer agente do [[Agent Flow]]
deveria ter um **fluxo de trabalho autônomo guiado pelo vocabulário/ontologia
interna** (issue, milestone, PR, projeto...), não pela API nativa da
ferramenta em que grava. O contrato/ports-and-adapters já resolve isso do
lado da entrada (Trigger/Input); a peça nomeada aqui é o mesmo padrão do lado
da **saída** — um adapter por plataforma de destino traduzindo o modelo de
domínio interno pro formato nativo dela.

Exemplo de trabalho dele: enviar ao A14 um arquivo detalhando um projeto, e o
agente gerar toda a configuração (projeto, milestones, issues) em **qualquer
plataforma de gerenciamento de projeto**, usando a linguagem interna e um
adapter específico da plataforma-alvo. Jira foi citado como exemplo mais
familiar de "outra plataforma", **não como alvo real** — confirmado por
msilva: o `AIRTABLEGC` no Jira é legado, em migração pro Linear (ver
[[Agent Flow]]).

Não é capacidade nova a construir agora — é a mesma "Lacuna 1" que hoje só
está resolvida pro Linear (`linear_client.py`) e ainda precisa generalizar,
já listada em "Caminho prático de replicar" acima. O que essa reformulação
confirma explicitamente é o **escopo**: vale pra todo agente que escreve num
sistema externo (A14 no Linear, A10 idem), não só um recurso específico do
A14. Ainda não desenhado — mesmas questões em aberto abaixo: se o molde
carrega esse vocabulário compartilhado ou se é camada separada da ontologia
do agente.

## Questões em aberto

- Qual problema concreto o molde precisa resolver primeiro: criação de novos
  agentes, padronização dos existentes, delegação da configuração para pessoas
  não técnicas ou comparação entre implementações?
- Existe um molde-base único com módulos opcionais ou uma família de arquétipos?
- Quais campos pertencem ao molde e quais só aparecem na instância?
- Quais mudanças podem ser feitas sem código, e quais exigem revisão técnica?
- Como versões de molde e de instância se relacionam quando o molde evolui?
- Quais invariantes não podem ser sobrescritas por uma instância?
- Como o molde representa componentes que não são agentes independentes?
- ~~O que exatamente compõe a Soul e como seus atributos viram comportamento
  observável?~~ **Parcialmente respondida em 2026-09-01:** o V1 abandona
  atributos estruturados em favor de texto direto, composição plana e versões
  rastreáveis; o conteúdo real dos fragments ainda precisa ser definido.
- **Novo, 2026-09-01**: o molde deve incluir vocabulário de domínio
  compartilhado entre sistemas de registro (Linear, GitHub, futuros), ou essa
  é uma camada separada da ontologia do agente em si? Ver seção acima.
