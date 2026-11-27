---
type: synthesis
status: draft
updated: 2026-11-27
aliases: [construtor de agentes, agent builder, agent_builder, criar agentes pelo MCP]
tags: [agent-flow, liveagents, mcp, design-doc, claude-agent-sdk]
---

# Construtor de agentes pelo MCP

Design doc montado com msilva em 2026-11-27 (sessão de `/design-doc`), seção por
seção. Conteúdo aprovado em chat; implementação ainda não começou. Cópia para
sign-off do time no Linear, sem nomes de pessoas:
[Design doc: construtor de agentes pelo MCP](https://linear.app/projetos-livemode/document/design-doc-construtor-de-agentes-pelo-mcp-84e266b09a97)
(projeto Infra do Fluxo Agêntico). Sistema-alvo: [[Agent Flow]] (repo `livemode-fluxo-agentico`).

## Objective

Uma pessoa da Livemode pede, pelo próprio assistente de IA, um agente novo para o
LiveAgents ou uma mudança num agente que já existe, e recebe de volta um pull request
pronto para revisão, com o agente montado no padrão do arcabouço: manifesto,
instruções, testes e avaliação. O agente continua sendo código revisado antes de entrar
em produção; o que muda é quem escreve o primeiro rascunho. Hoje só quem conhece o
repositório escreve um agente; com isso, qualquer pessoa autenticada pelo LiveAuth que
descreva o que quer chega a um rascunho revisável.

## Background

O arcabouço de agentes foi feito para que um agente novo não exija mexer no núcleo. Um
agente é um pacote com manifesto, mais pontos de contato fora dele: uma linha na
instalação da Livemode, a migration se ele tiver tabela, o horário no worker se tiver
rodada agendada, os prompts no Langfuse e, opcionalmente, uma página no painel. Há dois
exemplos completos para servir de modelo (o mínimo do núcleo e o do CrewAI), o padrão
está escrito no `CODING_STANDARDS.md` e nas ADRs, e o CI reprova dependência proibida
entre camadas. Um assistente de IA que lê esse material consegue escrever um pacote que
passa na revisão — hoje, só quando quem pede já trabalha no repositório.

O non-goal do arcabouço "criar agentes sem código ou carregá-los em tempo de execução"
(`docs/agent-framework.md`) continua valendo: este desenho não o substitui.

Três coisas faltavam para outra pessoa chegar ao mesmo resultado:

- **Acesso.** Quem não é do time técnico não tem o repositório nem escrita no GitHub. O
  LiveAgents só lê o GitHub (ADR 0046) e não abre PR.
- **O PR sozinho não monta o agente.** Prompts moram só no Langfuse (ADR 0003) e são
  criados à mão; o horário da rotina fica no worker, com deploy próprio.
- **Contexto.** Quem pede não conhece as ferramentas comuns, as portas, as escritas que
  já existem nem os agentes parecidos. O construtor resolve isso, porque trabalha dentro
  do repositório.

O MCP do LiveAgents já existe, autenticado pelo LiveAuth, mas só expõe ferramentas dos
agentes. O caso mais parecido é o das skills: a pessoa propõe pelo MCP do LiveStry, o
curador julga e o que passa vira PR (reunião "Agente de Skills" de 2026-11-19, no Granola, onde msilva
defendeu um validador só em vez de um por catálogo).

## Goals

1. **Chegar ao PR sem o repositório.** A pessoa descreve o agente no próprio assistente
   e o LiveAgents abre o PR. Ela não clona nada, não instala ambiente e não precisa de
   escrita no GitHub.
2. **PR completo.** Tudo de que o agente precisa vem no PR ou é declarado nele: prompts
   (gravados no Langfuse fora da produção), horário da rotina no worker, migration e os
   segredos necessários, só pelo nome. O revisor não descobre peça faltando depois do
   merge.
3. **Revisão sobre a decisão, não sobre o padrão.** O agente gerado reaproveita
   ferramentas, portas e escritas existentes; o construtor roda os testes do pacote antes
   de abrir o PR, e o PR só chega ao revisor depois de passar no CI. O time revisa o que o
   agente faz e o que ele escreve fora do sistema.
4. **Editar pelo mesmo caminho.** Mudar um agente existente vira PR sobre o pacote dele,
   com quem responde pelo agente como revisor.
5. **Quem pediu fica registrado.** O PR mostra a pessoa do LiveAuth que pediu, mesmo
   aberto por uma conta de serviço.
6. **Acompanhar sem sair da conversa.** A pessoa pergunta ao assistente como está o
   pedido. Falha de CI e mudança pedida pelo revisor são corrigidas pelo próprio
   construtor; a pessoa só entra quando a dúvida é sobre o que o agente deve fazer.
7. **Os admins acompanham tudo pelo Slack.** Cada mudança de estado gera aviso numa
   conversa em grupo dos admins com o bot, numa thread por pedido, com quem pediu
   marcado: pedido recebido, construtor trabalhando, PR aberto, CI falhou e construtor
   corrigindo, revisor pediu mudança, PR aprovado, PR mergeado, prompt esperando
   confirmação, prompt promovido, falha do construtor.

## Non-goals

- **Agente sem código ou carregado com o sistema rodando.** Todo agente é código num
  pacote, por PR.
- **Mudança sem aprovação humana.** Nenhum código entra sem PR aprovado pelo time
  técnico; nenhum prompt vai para produção sem confirmação de um admin (no PR ou no
  Slack).
- **Promover ou fazer deploy no merge.** O merge é em `develop`, que não é produção.
  Prompt e horário da rotina vão para produção na release, pelo processo de hoje; promover
  no merge poria instruções novas no ar antes do código que as usa.
- **Receber segredos.** O construtor nunca recebe senha nem token; o PR declara o nome e
  o time técnico configura.
- **Mexer fora do agente.** O construtor só escreve no pacote do agente e nos pontos de
  contato admitidos, com a trava no código. O núcleo não muda por esse caminho. Uma
  integração nova (por exemplo, ler o Airtable) cabe no pacote do agente, porque a porta
  fica no domínio de quem a usa (`CODING_STANDARDS.md`).
- **Conversa de levantamento dentro do LiveAgents.** O formulário é preenchido no
  assistente da pessoa; não há chat próprio, nem pelo Slack.
- **Ambiente para testar antes do merge.** A pessoa não roda o agente antes da aprovação.
- **Apagar agentes.** Segue com o time técnico.
- **Funcionar em qualquer cliente MCP.** Testado com o Claude (desktop e Claude Code).

## Interfaces

As ferramentas ficam num pacote novo, `agents/agent_builder`, com manifesto próprio:
prefixo da chave, registro na montagem e LiveAuth, como os outros agentes. O núcleo não
muda.

**O caminho do pedido:**

1. **Formulário, no assistente da pessoa.** `agent_builder_template()` devolve o
   formulário (o que o agente faz, quando roda, o que lê, o que escreve fora e com qual
   aprovação, dois ou três exemplos de saída, o que não faz) e a lista do que já existe:
   agentes, peças de leitura e escritas disponíveis. O assistente conduz a conversa e a
   pessoa aprova o formulário.
2. **Pedido.** `agent_builder_request(formulario, agente=None)` põe o pedido na fila.
   Sem `agente`, é agente novo; com, é mudança. Resposta imediata com o número do
   pedido; o aviso "pedido recebido" abre a thread dos admins.
3. **Construção.** A rodada do construtor pega o pedido na fila (ADR 0042), num processo
   próprio (ADR 0028), com uma cópia do repositório: lê, escreve, roda os testes do
   pacote. Trava no código: só escreve no pacote do agente e nos pontos de contato
   admitidos, só roda comandos de teste e checagem. Abre o PR contra `develop`, ou grava
   só o prompt quando a mudança é só de instrução.
4. **Acompanhamento.** `agent_builder_status(pedido)` mostra o estado e a pergunta
   pendente do construtor, se houver. `agent_builder_reply(pedido, texto)` responde e
   devolve o pedido à fila.

O guia de como montar um agente vira uma **skill no repositório**: o construtor a usa, o
time técnico a usa no Claude Code e o formulário do MCP a lê. Um guia só.

**Escritas declaradas no manifesto:**

| Escrita | Aprovação |
|---|---|
| Abrir e atualizar o PR | nenhuma — o PR é o portão |
| Gravar versão de prompt no Langfuse, rótulo do PR (ex.: `pr-631`), nunca `production` | nenhuma — não afeta quem está no ar |
| Promover prompt de mudança só de instrução | confirmação de um admin na thread (ADR 0060) |
| Avisar na thread dos admins | nenhuma |

**Eventos do GitHub.** Webhook do GitHub para o LiveAgents (CI terminou, revisão,
merge), na mesma fila. Falha de CI ou mudança pedida acordam o construtor; o resto só
avisa na thread.

**Fora do código:** GitHub App com escrita só neste repositório; chave da Anthropic para o
construtor; job no Cloud Run com o ambiente de testes; conversa em grupo dos admins com o
bot (o escritor comum do Slack já abre conversa em grupo por `conversations.open`).

**Framework do construtor:** Claude Agent SDK (hipótese, ver Open Issue 1). Traz pronto o
ciclo de ler, buscar, editar, rodar testes e corrigir; o controle de contexto em tarefa
longa; a lista de ferramentas permitidas e a interceptação de cada ação; e carrega o
`CLAUDE.md`, o `AGENTS.md` e as skills do projeto. É Python e entra pelo manifesto como o
CrewAI (ADR 0055), com import tardio. Custo: chave e fatura da Anthropic; trace no
Langfuse não vem pronto; job mais pesado (fora da API).

## Security

Premissa: o texto do pedido pode tentar mandar no construtor (má-fé ou trecho colado).
Nenhuma trava depende da instrução do construtor; todas ficam no código ou na
infraestrutura.

- **Mexer onde não deve** (outro agente, núcleo, CI). Lista de caminhos conferida antes de
  cada escrita, fora do modelo. `.github/` nunca entra: mudar workflow pode expor os
  segredos do CI.
- **Código gerado roda nos testes.** O job é tratado como possivelmente comprometido: sem
  segredo de produção (sem banco, Slack ou Linear), só token do GitHub App, chave do
  Langfuse e da Anthropic; testes com a configuração de teste. Os avisos no Slack saem da
  rodada na API, não do job.
- **Construtor aprova ou faz merge do próprio PR.** `develop` e `main` exigem aprovação
  humana sem exceção para o App. (unverified: proteção de branch atual não conferida.)
- **Revisor aprova o plausível.** O servidor, não o modelo, monta no PR uma seção com as
  escritas externas e a aprovação de cada uma, comparando o manifesto antes e depois.
  Escrita nova sem confirmação humana ou segredo novo ganham destaque na thread.
- **Prompt em produção sem revisão.** A gravação no Langfuse nunca aplica `production` e
  só aceita prompts com o nome começando pela chave do agente. Promoção de mudança só de
  prompt exige confirmação de quem está na lista de admins (`may_decide`).
- **Segredo no pedido.** Pedido com algo com cara de credencial é recusado e não fica
  guardado, como o curador de skills faz.
- **Pedido exposto.** Andamento e resposta só para quem pediu e para os admins; a thread
  fica na conversa dos admins, não em canal aberto.

## Open Issues

1. **O Claude Agent SDK gera PR que passa no CI no nosso padrão?** Próximo passo: prova
   com o pedido do resumo semanal do time de Dados; conferir também retomada de sessão e
   limite de gasto num job que nasce e morre a cada pedido. Se falhar, o Codex SDK volta
   a ser candidato.
2. **Avaliação do agente gerado.** Os exemplos do formulário viram casos de avaliação, mas
   a avaliação chama o modelo e o job não tem chave do produto; o CI não roda avaliação.
   Opções: chave separada com limite próprio (como a dos experimentos) ou o revisor roda
   antes de aprovar. Próximo passo: decidir junto com a prova do item 1.
3. **Confirmação na conversa em grupo.** O app precisa receber mensagens de conversa em
   grupo para o admin confirmar prompt pela thread. Próximo passo: conferir permissões e
   eventos do app do Slack.
4. **Proteção das branches.** Próximo passo: conferir se `develop` e `main` exigem
   aprovação humana sem exceção para apps; ajustar antes de ligar o construtor.
5. **Rede do job.** Opções: liberar só GitHub, Anthropic, Langfuse e índice de pacotes, ou
   não restringir. Próximo passo: medir o esforço no Cloud Run; decidir antes de produção.
6. **Quem responde por cada agente.** O Goal 4 pede revisão do dono, que não está
   registrado. Opções: CODEOWNERS ou campo no manifesto. Próximo passo: decidir com o
   time técnico; até lá, o PR vai para os admins.
7. **O que o construtor roda antes do PR.** Opções: só os testes do pacote (sem
   Postgres) ou a suíte inteira (banco no job). Recomendação: só o pacote; o resto fica
   com o CI.

## Resolved Issues

- **Que tipo de agente a pessoa cria.** Opções: configurável sem código (instruções +
  peças prontas + gatilho num executor comum), só editar os existentes, ou gerado como
  código. msilva escolheu gerado como código; o non-goal do arcabouço fica de pé.
- **Quem escreve o código.** Primeiro: o assistente da própria pessoa, com o servidor só
  entregando contexto e abrindo o PR (a pessoa não precisaria clonar; os arquivos iriam
  como argumento da ferramenta, como no `propose_new_skill` do LiveStry). Depois msilva
  trocou por um construtor no LiveAgents que faz tudo, com o PR como único portão humano:
  a qualidade deixa de depender do modelo de quem pede e os testes rodam antes do PR.
- **Prompts.** Primeiro: texto na descrição do PR para o time copiar. Decidido: gravar
  direto no Langfuse, fora da produção, com o rótulo do PR.
- **Mudança só de prompt.** O GitHub só abre PR com arquivo mudado. Proposta inicial: um
  arquivo de registro no pacote para forçar o PR. Decidido (msilva): avisar os admins no
  Slack e um admin confirmar, pela escrita com confirmação humana (ADR 0060).
- **Levantamento do pedido.** msilva propôs guiar a pessoa por um design doc; ficou um
  formulário curto, conduzido no assistente da pessoa, que vai no PR e semeia a
  avaliação.
- **Integração com sistema novo.** Cogitado parar o fluxo; a porta fica no pacote do
  agente, como o padrão manda, e o PR declara o segredo.
- **Avisos.** msilva: toda mudança de estado avisa os admins; ficou uma conversa em grupo
  dos admins com o bot, thread por pedido, quem pediu marcado.
- **Mudança em agente de outra pessoa.** Livre; o PR segura (msilva).
- **Custo.** Sem limite por pessoa (msilva).
- **Eventos do GitHub.** Webhook, não consulta periódica.
- **Construtor em framework nosso.** msilva quis um agente nosso; Claude Agent SDK em vez
  de LangGraph, porque traz o ciclo de edição, as travas e lê as instruções do repo.

## Alternatives Considered

- **Agente configurável sem código.** Recusado: substituiria o non-goal do arcabouço e
  poria agentes no ar sem revisão de código.
- **Só editar os agentes existentes.** Não entrega agente novo.
- **Geração no assistente da pessoa.** Recusado: qualidade varia com o modelo e o plano de
  quem pede, nada é testado antes do PR, ciclo de correção pelo CI lento.
- **Construtor em LangGraph.** Teríamos de construir e manter o ciclo de editar, testar e
  corrigir.
- **Codex SDK.** Mesmo motor, sem as instruções do repositório; exigiria chave separada da
  de produção de qualquer jeito (a chave de produção bloqueia tudo ao estourar o limite) e
  é TypeScript (unverified: não conferido se há SDK Python). Volta se a prova do Claude
  Agent SDK falhar.
- **Consultar o GitHub periodicamente.** Mais simples, atrasa cada passo.
