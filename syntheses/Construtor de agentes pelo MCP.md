---
type: synthesis
status: draft
updated: 2026-11-27
aliases: [construtor de agentes, agent builder, agent_builder, criar agentes pelo MCP]
tags: [agent-flow, liveagents, mcp, design-doc, claude-agent-sdk, cloud-run]
---

# Construtor de agentes pelo MCP

Design doc montado com msilva em 2026-11-27 (sessão de `/design-doc`), seção por seção. Conteúdo aprovado em chat; implementação ainda não começou. Cópia para sign-off do time no Linear, sem nomes de pessoas: [Design doc: construtor de agentes pelo MCP](https://linear.app/projetos-livemode/document/design-doc-construtor-de-agentes-pelo-mcp-84e266b09a97) (projeto Infra do Fluxo Agêntico). Sistema-alvo: [[Agent Flow]] (repo `livemode-fluxo-agentico`).

## Objective

Uma pessoa da Livemode pede, pelo próprio assistente de IA, um agente novo para o LiveAgents ou uma mudança num agente que já existe, e recebe de volta um pull request pronto para revisão, com o agente montado no padrão do arcabouço: manifesto, instruções, testes e avaliação. O agente continua sendo código revisado antes de entrar em produção; o que muda é quem escreve o primeiro rascunho. Hoje só quem conhece o repositório escreve um agente; com isso, qualquer pessoa autenticada pelo LiveAuth que descreva o que quer chega a um rascunho revisável.

## Background

O arcabouço de agentes foi feito para que um agente novo não exija mexer no núcleo. Um agente é um pacote com manifesto, mais pontos de contato fora dele: uma linha na instalação da Livemode, a migration se ele tiver tabela, o horário no worker se tiver rodada agendada, os prompts no Langfuse e, opcionalmente, uma página no painel. Há dois exemplos completos para servir de modelo (o mínimo do núcleo e o do CrewAI), o padrão está escrito no `CODING_STANDARDS.md` e nas ADRs, e a suíte reprova dependência proibida entre camadas. Um assistente de IA que lê esse material consegue escrever um pacote que passa na revisão — hoje, só quando quem pede já trabalha no repositório.

O non-goal do arcabouço "criar agentes sem código ou carregá-los em tempo de execução" (`docs/agent-framework.md`) continua valendo: este desenho não o substitui.

Três coisas faltavam para outra pessoa chegar ao mesmo resultado:

- **Acesso.** Quem não é do time técnico não tem o repositório nem escrita no GitHub. O LiveAgents só lê o GitHub (ADR 0046) e não abre PR.
- **O PR sozinho não monta o agente.** Prompts moram só no Langfuse (ADR 0003) e são criados à mão; o horário da rotina fica no worker, com deploy próprio.
- **Contexto.** Quem pede não conhece as ferramentas comuns, as portas, as escritas que já existem nem os agentes parecidos. O construtor resolve isso, porque trabalha dentro do repositório.

**O CI não roda nos PRs para `develop`.** Ele só roda no PR de release (`develop` para `main`, `.github/workflows/ci.yml`); o teste dos PRs normais fica com quem abre. Por isso os testes do agente gerado rodam na máquina do construtor, não no CI.

O MCP do LiveAgents já existe, autenticado pelo LiveAuth, mas só expõe ferramentas dos agentes. O caso mais parecido é o das skills: a pessoa propõe pelo MCP do LiveStry, o curador julga e o que passa vira PR (reunião "Agente de Skills" de 2026-11-19, no Granola, onde msilva defendeu um validador só em vez de um por catálogo).

## Goals

1. **Chegar ao PR sem o repositório.** A pessoa descreve o agente no próprio assistente e o LiveAgents abre o PR. Ela não clona nada, não instala ambiente e não precisa de escrita no GitHub.
2. **PR completo.** Tudo de que o agente precisa vem no PR ou é declarado nele: prompts (gravados no Langfuse fora da produção), horário da rotina no worker, migration e os segredos necessários, só pelo nome. O revisor não descobre peça faltando depois do merge.
3. **Revisão sobre a decisão, não sobre o padrão.** O agente gerado reaproveita ferramentas, portas e escritas existentes, e o PR só chega ao revisor com a suíte inteira passando na máquina do construtor, conferida por código nosso e não pela palavra do modelo. O time revisa o que o agente faz e o que ele escreve fora do sistema.
4. **Editar pelo mesmo caminho.** Mudar um agente existente vira PR sobre o pacote dele, com quem responde pelo agente como revisor.
5. **Quem pediu fica registrado.** O PR mostra a pessoa do LiveAuth que pediu, mesmo aberto por uma conta de serviço.
6. **Acompanhar sem sair da conversa.** A pessoa pergunta ao assistente como está o pedido. Teste falhando e mudança pedida pelo revisor são corrigidos pelo próprio construtor; a pessoa só entra quando a dúvida é sobre o que o agente deve fazer.
7. **Os admins acompanham tudo pelo Slack.** Cada mudança de estado gera aviso numa conversa em grupo dos admins com o bot, numa thread por pedido, com quem pediu marcado: pedido recebido, construtor trabalhando, testes falharam e construtor corrigindo, PR aberto, revisor pediu mudança, PR aprovado, PR mergeado, prompt esperando confirmação, prompt promovido, falha do construtor.

## Non-goals

- **Agente sem código ou carregado com o sistema rodando.** Todo agente é código num pacote, por PR.
- **Mudança sem aprovação humana.** Nenhum código entra sem PR aprovado pelo time técnico; nenhum prompt vai para produção sem confirmação de um admin (no PR ou no Slack).
- **Promover ou fazer deploy no merge.** O merge é em `develop`, que não é produção. Prompt e horário da rotina vão para produção na release, pelo processo de hoje; promover no merge poria instruções novas no ar antes do código que as usa.
- **Receber segredos.** O construtor nunca recebe senha nem token; o PR declara o nome e o time técnico configura.
- **Mexer fora do agente.** O construtor só escreve no pacote do agente e nos pontos de contato admitidos, com a trava no código. O núcleo não muda por esse caminho. Uma integração nova (por exemplo, ler o Airtable) cabe no pacote do agente, porque a porta fica no domínio de quem a usa (`CODING_STANDARDS.md`).
- **Conversa de levantamento dentro do LiveAgents.** O formulário é preenchido no assistente da pessoa; não há chat próprio, nem pelo Slack.
- **Avaliação do modelo na máquina do construtor.** O `poe eval` chama o modelo de verdade e a máquina não tem a chave do produto (Open Issue 2).
- **Ambiente para a pessoa testar antes do merge.** A pessoa não roda o agente antes da aprovação.
- **Apagar agentes.** Segue com o time técnico.
- **Funcionar em qualquer cliente MCP.** Testado com o Claude (desktop e Claude Code).

## Interfaces

O construtor é um pacote novo, `agents/agent_builder`, com manifesto próprio: prefixo da chave, registro na montagem e LiveAuth, como os outros agentes. O núcleo não muda. Ele tem duas metades: a rodada, que roda na API e faz todas as escritas, e a construção, que roda num job no Cloud Run e não escreve em nada.

**O caminho do pedido:**

1. **Formulário, no assistente da pessoa.** `agent_builder_template()` devolve o formulário (o que o agente faz, quando roda, o que lê, o que escreve fora e com qual aprovação, dois ou três exemplos de saída, o que não faz) e a lista do que já existe: agentes, peças de leitura e escritas disponíveis. O assistente conduz a conversa e a pessoa aprova o formulário.
2. **Pedido.** `agent_builder_request(formulario, agente=None)` põe o pedido na fila (ADR 0042). Sem `agente`, é agente novo; com, é mudança. Resposta imediata com o número do pedido; o aviso "pedido recebido" abre a thread dos admins.
3. **Construção, no job.** A rodada pega o pedido na fila e dispara o job no Cloud Run pela conta de serviço da API. Na máquina do job:
   1. sobe o Postgres com pgvector local, clona a `develop`, roda `uv sync` e `alembic upgrade head`, como o job de backend do `ci.yml`;
   2. o construtor (Claude Agent SDK) escreve o agente e roda os testes ele mesmo (`uv run poe test`, com os valores falsos do CI), corrigindo até passar ou atingir o limite de voltas;
   3. o script do job, e não o construtor, roda a suíte inteira de novo, mais o `alembic check` quando há migration nova;
   4. devolve o diff e o relatório dos testes e a máquina é apagada.

   Trava no código: o construtor só escreve no pacote do agente e nos pontos de contato admitidos, e só roda comandos de teste e checagem.
4. **Escritas, na rodada.** Com o diff aprovado nos testes, a rodada cria a branch e abre o PR contra `develop` pelo GitHub App, grava os prompts no Langfuse e avisa na thread. Quando a mudança é só de instrução, grava só o prompt e pede confirmação na thread.
5. **Acompanhamento.** `agent_builder_status(pedido)` mostra o estado e a pergunta pendente do construtor, se houver. `agent_builder_reply(pedido, texto)` responde e devolve o pedido à fila.

O guia de como montar um agente vira uma **skill no repositório**: o construtor a usa, o time técnico a usa no Claude Code e o formulário do MCP a lê. Um guia só.

**Escritas declaradas no manifesto (todas na rodada, nenhuma no job):**

| Escrita | Aprovação |
|---|---|
| Abrir e atualizar o PR | nenhuma — o PR é o portão |
| Gravar versão de prompt no Langfuse, rótulo do PR (ex.: `pr-631`), nunca `production` | nenhuma — não afeta quem está no ar |
| Promover prompt de mudança só de instrução | confirmação de um admin na thread (ADR 0060) |
| Avisar na thread dos admins | nenhuma |

**Eventos do GitHub.** Webhook do GitHub para o LiveAgents (revisão, merge), na mesma fila. Mudança pedida pelo revisor acorda o construtor; o resto só avisa na thread.

**Fora do código:**

- **Job no Cloud Run**, no mesmo projeto da API, disparado pela conta de serviço dela, sem segredo compartilhado. Logs no Cloud Logging, junto dos da API.
- **Imagem `builder`**: a do `apps/worker/Dockerfile` mais git, as dependências de desenvolvimento, o Claude Agent SDK e o Postgres com pgvector.
- **Saída de rede do job limitada** por rede privada e regra de firewall: GitHub (só leitura), Anthropic e o índice de pacotes.
- **Segredos do job**, pelo Secret Manager: um token de leitura do GitHub e uma chave da Anthropic só do construtor, num workspace com limite de gasto próprio.
- **GitHub App** com escrita só neste repositório, usado pela rodada.
- **Conversa em grupo dos admins** com o bot (o escritor comum do Slack já abre conversa em grupo por `conversations.open`).

**Framework do construtor:** Claude Agent SDK (hipótese, ver Open Issue 1). Traz pronto o ciclo de ler, buscar, editar, rodar testes e corrigir; o controle de contexto em tarefa longa; a lista de ferramentas permitidas e a interceptação de cada ação; e carrega o `CLAUDE.md`, o `AGENTS.md` e as skills do projeto. É Python e entra pelo manifesto como o CrewAI (ADR 0055), com import tardio. Custo: chave e fatura da Anthropic; trace no Langfuse não vem pronto.

**Custo por pedido (estimativa de 2026-11-27, a medir na prova).** Máquina: cerca de US$ 0,06 num job de 20 minutos com 2 vCPUs e 8 GB ($0,000018 por vCPU-segundo, $0,000002 por GiB-segundo). Modelo: entre US$ 2 e 10, porque o construtor relê muito contexto (Opus 5.5: US$ 4 por milhão de tokens de entrada, 20 de saída, 0,20 na leitura de cache; Sonnet 5.5 pela metade). O modelo custa 50 a 100 vezes a máquina.

## Security

Premissa: o texto do pedido pode tentar mandar no construtor (má-fé ou trecho colado). Nenhuma trava depende da instrução do construtor; todas ficam no código ou na infraestrutura.

- **Mexer onde não deve** (outro agente, núcleo, CI). Lista de caminhos conferida antes de cada escrita, fora do modelo. `.github/` nunca entra: mudar workflow pode expor os segredos do CI.
- **Código gerado roda nos testes.** O job é tratado como possivelmente comprometido. Ele não tem token de escrita, nem segredo de produção (banco, Slack, Linear, Langfuse): só o token de leitura do GitHub e a chave da Anthropic com limite próprio. A saída de rede é limitada. Quem escreve em qualquer lugar é a rodada, na API, a partir do diff.
- **Construtor aprova ou faz merge do próprio PR.** `develop` e `main` exigem aprovação humana sem exceção para o App. (unverified: proteção de branch atual não conferida.)
- **Revisor aprova o plausível.** A rodada, não o modelo, monta no PR uma seção com as escritas externas e a aprovação de cada uma, comparando o manifesto antes e depois, e anexa o relatório dos testes. Escrita nova sem confirmação humana ou segredo novo ganham destaque na thread.
- **Prompt em produção sem revisão.** A gravação no Langfuse nunca aplica `production` e só aceita prompts com o nome começando pela chave do agente. Promoção de mudança só de prompt exige confirmação de quem está na lista de admins (`may_decide`).
- **Segredo no pedido.** Pedido com algo com cara de credencial é recusado e não fica guardado, como o curador de skills faz.
- **Pedido exposto.** Andamento e resposta só para quem pediu e para os admins; a thread fica na conversa dos admins, não em canal aberto.

## Open Issues

1. **O Claude Agent SDK gera um agente que passa nos testes no nosso padrão?** Próximo passo: prova com o pedido de um resumo semanal dos projetos de um time; conferir também retomada de sessão, limite de gasto num job que nasce e morre a cada pedido, e o custo real por pedido. Se falhar, o Codex SDK volta a ser candidato.
2. **Avaliação do agente gerado.** Os exemplos do formulário viram casos de avaliação, mas a avaliação chama o modelo e o job não tem chave do produto. Opções: chave separada com limite próprio (como a dos experimentos) ou o revisor roda antes de aprovar. Próximo passo: decidir junto com a prova do item 1.
3. **Confirmação na conversa em grupo.** O app precisa receber mensagens de conversa em grupo para o admin confirmar prompt pela thread. Próximo passo: conferir permissões e eventos do app do Slack.
4. **Proteção das branches.** Próximo passo: conferir se `develop` e `main` exigem aprovação humana sem exceção para apps; ajustar antes de ligar o construtor.
5. **Como o resultado volta do job.** O job não devolve valor. Opções: gravar o diff e o relatório num bucket que a rodada lê quando o job termina, ou o job chamar a API de volta com uma credencial própria. Próximo passo: decidir no desenho da implementação; o bucket mantém o job sem nenhuma credencial do LiveAgents.
6. **Quem responde por cada agente.** O Goal 4 pede revisão do dono, que não está registrado. Opções: CODEOWNERS ou campo no manifesto. Próximo passo: decidir com o time técnico; até lá, o PR vai para os admins.

## Resolved Issues

- **Que tipo de agente a pessoa cria.** Opções: configurável sem código (instruções + peças prontas + gatilho num executor comum), só editar os existentes, ou gerado como código. msilva escolheu gerado como código; o non-goal do arcabouço fica de pé.
- **Quem escreve o código.** Primeiro: o assistente da própria pessoa, com o servidor só entregando contexto e abrindo o PR (a pessoa não precisaria clonar; os arquivos iriam como argumento da ferramenta, como no `propose_new_skill` do LiveStry). Depois msilva trocou por um construtor no LiveAgents que faz tudo, com o PR como único portão humano: a qualidade deixa de depender do modelo de quem pede e os testes rodam antes do PR.
- **Prompts.** Primeiro: texto na descrição do PR para o time copiar. Decidido: gravar direto no Langfuse, fora da produção, com o rótulo do PR.
- **Mudança só de prompt.** O GitHub só abre PR com arquivo mudado. Proposta inicial: um arquivo de registro no pacote para forçar o PR. Decidido (msilva): avisar os admins no Slack e um admin confirmar, pela escrita com confirmação humana (ADR 0060).
- **Levantamento do pedido.** msilva propôs guiar a pessoa por um design doc; ficou um formulário curto, conduzido no assistente da pessoa, que vai no PR e semeia a avaliação.
- **Integração com sistema novo.** Cogitado parar o fluxo; a porta fica no pacote do agente, como o padrão manda, e o PR declara o segredo.
- **Avisos.** msilva: toda mudança de estado avisa os admins; ficou uma conversa em grupo dos admins com o bot, thread por pedido, quem pediu marcado.
- **Mudança em agente de outra pessoa.** Livre; o PR segura (msilva).
- **Custo.** Sem limite por pessoa (msilva).
- **Eventos do GitHub.** Webhook, não consulta periódica.
- **Construtor em framework nosso.** msilva quis um agente nosso; Claude Agent SDK em vez de LangGraph, porque traz o ciclo de edição, as travas e lê as instruções do repo.
- **Onde o construtor roda.** Comparados job no Cloud Run, container na Cloudflare (precedente das rotinas, ADR 0028; preferência inicial de msilva) e GitHub Actions (ambiente pronto no `ci.yml`). msilva decidiu pelo job no Cloud Run: único que limita a saída de rede de forma nativa, no mesmo provedor da API, disparado por permissão. Preço não decidiu: centavos por pedido nos três. A imagem é a mesma para job e container, então trocar depois não perde o trabalho.
- **Onde os testes rodam.** O CI não roda nos PRs para `develop`; a máquina do construtor repete a receita do job de backend do `ci.yml` com Postgres local e roda a suíte inteira, e um script nosso confere o resultado final.
- **Escritas fora do job.** O job só lê e devolve o diff; PR, Langfuse e Slack ficam com a rodada na API, para que código gerado malicioso não encontre token de escrita.

## Alternatives Considered

- **Agente configurável sem código.** Recusado: substituiria o non-goal do arcabouço e poria agentes no ar sem revisão de código.
- **Só editar os agentes existentes.** Não entrega agente novo.
- **Geração no assistente da pessoa.** Recusado: qualidade varia com o modelo e o plano de quem pede, nada é testado antes do PR, ciclo de correção lento.
- **Construtor em LangGraph.** Teríamos de construir e manter o ciclo de editar, testar e corrigir.
- **Codex SDK.** Mesmo motor, sem as instruções do repositório; exigiria chave separada da de produção de qualquer jeito (a chave de produção bloqueia tudo ao estourar o limite) e é TypeScript (unverified: não conferido se há SDK Python). Volta se a prova do Claude Agent SDK falhar.
- **Container na Cloudflare.** Precedente das rotinas, mas não conferimos se limita a saída de rede, e põe dois provedores no caminho.
- **GitHub Actions.** Ambiente pronto, mas sem limite de rede na máquina padrão, segredos no GitHub e o construtor fora do nosso sistema.
- **Consultar o GitHub periodicamente.** Mais simples, atrasa cada passo.
