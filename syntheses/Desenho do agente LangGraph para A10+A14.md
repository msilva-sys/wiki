---
type: synthesis
status: active
updated: 2026-08-27
date: 2026-08-26
tags: [agents, agent-flow, a10, a14, poc, langgraph, langchain, linear-api, langfuse]
aliases: [desenho do LangGraph, agent vs workflow A10+A14, PRO-392 design]
---

# Desenho do agente LangGraph para A10+A14

Continuação de [[Como implementar a PoC do A10+A14 (LangGraph, Skill, Agent SDK)]] —
aquela página fechou o desenho das **três frentes**; esta cobre só o **interior
da frente LangGraph** (issue [PRO-392](https://linear.app/projetos-livemode/issue/PRO-392/implementar-poc-em-langgraph)),
resultado de uma sessão de discovery em chat no dia 2026-08-26, feita antes de
qualquer código. Repositório: `C:\Users\msilva\projects\livemode-fluxo-agentico`,
branch `langgraph` — ver `HANDOFF.md` nesse repo, que carrega o mesmo desenho
pra uma sessão de código sem acesso a esta wiki.

## A pergunta que abriu o desenho

O próprio Luís, ao tarefar esse protótipo, avisou: *"não caia na besteira de
falar assim: 'Pô, vou fazer lá em [Lang]Graph.'"* — ver
[[2026-08-20 Fluxo Agêntico diagram walkthrough with Luís]]. Isso ecoa direto o
contraponto do [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]]:
Graph Engineering só compensa quando o fluxo **genuinamente ramifica** — a
maioria dos projetos é uma linha reta com um `if` no meio.

Mais direto ainda: [[2026-08-26 1-1 Matheus - Luís (agentes A10-A14)]], mais
cedo no mesmo dia, tem Luís questionando ao vivo o argumento de msilva ("não
determinístico" não justifica sozinho pular LangGraph — ele também suporta
zero-determinismo) e pedindo uma referência resumida com critérios claros
antes de fechar o lado. A distinção **"agent" vs. "workflow"** abaixo lê como
a resposta direta a esse pedido — não confirmado explicitamente como entregue
a Luís (ver Commitments naquela página).

**Resolvido nesta sessão**: a ramificação real do Fluxo Agêntico só existe no
**sistema inteiro** (A2 roteando pra A3/A4/A7...), não dentro do A10/A14
isolados — msilva, 2026-08-26. Isso significa que desenhar um grafo à mão
(`StateGraph` com nós/arestas fixos) não se justifica nessa PoC. A saída não é
descartar o LangGraph — é usar a peça certa dele.

## Decisão central: "agent", não "workflow"

A doc oficial do LangChain distingue dois padrões
([workflows-agents](https://docs.langchain.com/oss/python/langgraph/workflows-agents)):
**workflow** é fluxo de controle pré-definido por quem programa; **agent** é um
loop onde o próprio LLM decide, a cada passo, qual tool chamar e quando parar.

- **API atual**: `create_agent`, do pacote `langchain` (não `langgraph`
  diretamente) — roda o LangGraph por baixo, com um sistema de middleware.
  `langgraph.prebuilt.create_react_agent`, que seria a resposta óbvia, **está
  depreciado**.
- Instalação: `pip install langchain langgraph langchain-openai`, versões
  casadas entre si — já houve quebra real reportada entre `langchain`/
  `langgraph`/`langgraph-prebuilt` desalinhados.
- Isso também deixa mais justo o experimento que Luís pediu: não é mais "grafo
  desenhado à mão vs. Claude direto" (onde o grafo perderia por design, sem
  ramificação real pra justificar), é "agente rodando no runtime do LangGraph
  vs. agente rodando no Claude Code" — mesma arquitetura de loop, runtime
  diferente.

Sources: [LangChain and LangGraph Agent Frameworks Reach v1.0 Milestones](https://blog.langchain.com/langchain-langgraph-1dot0/),
[Migrating from langgraph.prebuilt.create_react_agent to langchain.agents.create_agent](https://forum.langchain.com/t/migrating-from-langgraph-prebuilt-create-react-agent-to-langchain-agents-create-agent-missing-feature/1985).

## A10 e A14 são agentes independentes

Cada um com seu próprio `create_agent`, sem chamar um ao outro por baixo dos
panos (mesma regra da página-mãe, só que aplicada dentro da frente, não entre
frentes).

- **A14 roda por projeto** — não é uma visão geral do setor de projetos, é um
  output por projeto do Linear, decisão de msilva 2026-08-26. Despachado por
  **loop Python simples** chamando o agente uma vez por projeto — não pela API
  `Send` (fan-out dinâmico) do LangGraph. Ambos resolvem o mesmo problema
  (paralelismo sem dependência entre itens, o "map-reduce" que
  [[Fabio Akita - Harness, Loop and Graph Engineering are bullshit]] chama de
  "não precisa de nome novo"); msilva escolheu o loop pela simplicidade.
- **Validação contra o Farol** (caso de teste único, ver a página-mãe): uma
  **pergunta simples feita ao próprio agente A10**, não um node/avaliador
  separado dentro do grafo.

## Toolset — determinístico onde dá, julgamento onde precisa

Acesso ao Linear é GraphQL cru contra `api.linear.app/graphql` (sem SDK Python
oficial, sem endpoint de histórico — ver a página-mãe). Dos quatro critérios do
A10, dois são computáveis e dois são de julgamento; LLM é ruim em aritmética de
data feita "de cabeça", então **critérios computáveis viram tool determinística**,
e só os de julgamento ficam livres pro agente raciocinar — decisão de msilva,
2026-08-26.

| Agente | Tools |
|---|---|
| **A10** | `listar_issues(filtros?)` · `calcular_dias_sem_update(issue)` → critério **estagnado** · `calcular_carga_por_assignee()` → critério **gargalo de capacidade**. "Priorização desalinhada" e "escopo descontrolado" sem tool própria — julgamento sobre o que `listar_issues` já traz. |
| **A14** | `listar_projetos()` · `buscar_milestones_do_projeto(project_id)` · `calcular_progresso_do_projeto(project_id)` |

Todas as tools são **só leitura** nessa fase — nada escreve de volta no Linear,
o que bate com "sugere, nunca executa" (A10) e "sem entrega automática em canal
real" (A14), já fechados na página-mãe.

### Escopo: backlog inteiro, não um projeto só

Lacuna identificada revisando o desenho (nenhuma sessão anterior tinha
decidido isso) e fechada por msilva, 2026-08-26: `listar_issues()` cobre o
**backlog inteiro do time** `Projetos-livemode` — todos os projetos, não um
`project_id` fixo — batendo com a spec do A10 em
[[Fluxo Agêntico project instruction]] ("analyses **full** backlog and
capacity"). `filtros?` continua opcional em cima disso (por estado, label,
assignee etc.), não troca escopo por projeto.

Isso também fecha a mesma pergunta pro A14: `listar_projetos()` lista todos
os projetos do mesmo time — é a lista que o loop por projeto ("A14 roda por
projeto", acima) percorre.

### `calcular_progresso_do_projeto` é por milestone, não por issue/subtask

Achado direto e relevante: a área já rodou um sistema de status reporting sobre
Linear antes — [[AI status reporting on Linear]], o board interno da Gabrielle
(*"nosso repórter"*) — e errou de duas formas documentadas:

1. **Subtask blindness** — contou entrega no nível de issue-pai, ignorando
   progresso nas subtasks. O [[Farol]] apareceu como *"sem entregas"* quando
   tinha andado bastante, tudo dentro de uma issue-pai só.
2. **Raciocinar além da evidência** — apontou o Orca como retrabalho corrigindo
   erro, quando era capability nova; tinha o dado, tirou conclusão que o dado
   não sustentava.

O time resolveu o problema 1 **reorganizando o board por milestone**, não
mudando o agente — [[Linear Project Structure]] registra a lição: granularidade
de milestone é o que torna progresso legível. `calcular_progresso_do_projeto`
foi desenhada olhando **milestones**, não contagem crua de issue, especificamente
pra não repetir esse erro já documentado na própria área.

Pro problema 2 (sem solução mecânica — é o LLM tirando conclusão além da
evidência): o prompt do A14 é instruído a **não inferir causa/motivo sem
evidência textual** — se não tiver comentário/descrição sustentando, reporta só
o dado, sem interpretação. Mesma trava de design contra o mesmo erro.

## Saída

- **A10**: por sugestão — issue (link/id), critério(s) disparado(s), justificativa
  curta em texto.
- **A14**: por projeto — % de progresso por milestone, o que mudou desde a
  última vez, riscos/bloqueios citados em comentários, sem inferência sem
  evidência (ver acima).
- **Formato do artefato**: HTML, gerado por uma invocação manual via CLI (sem
  cron, sem Slack, sem cadência automática nessa fase) — msilva quer algo que
  dê pra apresentar.

## Segurança contra loop, observabilidade e prompts

Três mecanismos complementares, não concorrentes:

- **`recursion_limit`** — teto de passos nativo do LangGraph/`create_agent`,
  default **25**, configurável via `config={"recursion_limit": N}` no
  `.invoke()`. Levanta `GraphRecursionError` e para a execução — trava
  mecânica, funciona mesmo sem ninguém observando.
- **Langfuse Cloud — observabilidade**: trace de tool-call, prompt, tokens.
  Setup: `pip install langfuse`, três env vars (`LANGFUSE_PUBLIC_KEY`,
  `LANGFUSE_SECRET_KEY`, `LANGFUSE_BASE_URL`, já configuradas no `.env` do
  repo), um `CallbackHandler()` passado no mesmo `config` do `.invoke()`.
- **Langfuse — Prompt Management** (decisão 2026-08-27, mesmo dia,
  discussão em chat separada): prompts vivem no Langfuse, versionados
  (cada edição vira versão imutável), com **labels** apontando pra qual
  versão está ativa (ex.: `production`). O código busca o prompt em
  runtime pelo nome+label via SDK — trocar de versão é trocar o label na
  UI, sem redeploy, e cada execução no trace fica linkada à versão exata
  do prompt que a gerou. Decisão explícita de msilva: expande o escopo do
  Langfuse além de "só observabilidade" (framing original desta mesma
  página), julgado como valendo a pena mesmo em fase de PoC — ajuda
  diretamente a iterar o prompt do A10 contra o caso do Farol sem
  redeploy a cada tentativa. Alternativa mais simples que foi descartada:
  prompt como arquivo versionado no git.

Sources: [GRAPH_RECURSION_LIMIT — Docs by LangChain](https://docs.langchain.com/oss/python/langgraph/errors/GRAPH_RECURSION_LIMIT),
[Open Source Observability for LangGraph — Langfuse](https://langfuse.com/guides/cookbook/integration_langgraph),
[Open Source Prompt Management — Langfuse](https://langfuse.com/docs/prompt-management/overview).

## Modelo e empacotamento

- **OpenAI, não Anthropic** — `langchain-openai`. Resolve as duas pendências
  que ficaram em aberto na página-mãe (origem da key, quem paga): a chave já
  existe e já está configurada no `.env` do repo.
- **Modelo exato: `gpt-5.6-terra`** — checado na doc oficial da OpenAI
  (2026-08-26, pós-cutoff de conhecimento, pesquisado ao vivo): a família
  atual pra tool-calling agêntico tem três variantes, `gpt-5.6-sol`
  (frontier), `gpt-5.6-terra` (equilíbrio custo/capacidade), `gpt-5.6-luna`
  (alto volume/eficiência). `-terra` escolhido pra essa PoC — critérios de
  julgamento reais (priorização desalinhada, escopo descontrolado) mas
  volume baixo, não precisa do topo de linha `-sol`. Recomendação, não
  testada ainda contra o caso do Farol — ajustar se a qualidade da sugestão
  não convencer.
- **`pip` + `venv`** — sem convenção prévia da Livemode pra Python (o
  [[Airtable Proxy]] é Go); escolha livre de msilva.

## Estrutura de pastas e tooling (fechada em sessão de continuação, mesmo dia)

Depois do desenho inicial, mais uma rodada de brainstorm em chat acrescentou
contratos tipados e um task runner — encaixe, não mudança de direção:

- **Pydantic para contratos** — já vem transitivamente via `langchain`/
  `langgraph` (Pydantic v2), agora declarado explícito. Dois usos
  distintos: os modelos que espelham o shape do GraphQL do Linear (`Issue`,
  `Project`, `Milestone`) ficam dentro de `linear_client.py`, junto do único
  código que fala com a API; os modelos de **saída** dos agentes ficam
  dentro de cada agente (ver "Ports and adapters", abaixo) — são o que dá
  pra forçar como `response_format` do `create_agent`, então cada agente
  devolve um objeto validado, não texto livre, e `main.py` consome isso
  direto pra montar o HTML.
- **`poethepoet` (comando `poe`) para task management** — só task runner,
  não substitui `pip`+`venv` como gerenciador de dependência (opção
  considerada e descartada: trocar tudo por Poetry). Precisa de um
  `pyproject.toml`; já que ele vai existir mesmo, as dependências passam a
  ser declaradas ali também (`[project.dependencies]`, PEP 621, que o
  `pip` já entende via `pip install -e .`), em vez de um `requirements.txt`
  separado.
- **Cada agente roda sozinho** — `a10/agent.py` e `a14/agent.py` ganham um
  bloco `if __name__ == "__main__":` que monta a própria `Entrada` (ver
  abaixo) e despeja a `Saída` em JSON (`.model_dump_json()`) no stdout.
  `main.py` fica só o orquestrador do pipeline completo (A10 + A14 + HTML
  final) — rodar um agente isolado não gera HTML, só o contrato bruto: o
  ponto é depurar um sem esperar o outro.

## Ports and adapters — cada agente define a própria interface (2026-08-26)

Ideia de msilva, registrada e adotada: cada agente é uma fronteira
`Entrada → Saída` que ele mesmo define — não um shape imposto de fora. É
o padrão **ports and adapters** (arquitetura hexagonal) aplicado aos
agentes: tools, `linear_client.py`, o modelo — tudo isso é "adapter",
escondido atrás do "port" (o contrato). Motivação: agente testável isolado
(dá pra chamar com uma `Entrada` fabricada na mão, sem Linear de verdade),
portável (reusar a lógica em outro lugar só exige entender o contrato, não
o shape do GraphQL do Linear), e verdadeiramente modular.

Isso corrigiu algo que a decisão de escopo (acima) tinha deixado errado: o
`team_id` do time `Projetos-livemode` ia ficar **embutido dentro do
agente** — o que contradiz "agnóstico". Agora ele é campo da `Entrada`; o
agente não sabe de cor qual time analisa, só recebe o que a `Entrada`
mandar.

Isso força uma linha entre dois tipos de parâmetro que antes estavam
misturados:

| Tipo | Onde mora | Exemplo |
|---|---|---|
| Parâmetro de domínio (o que o agente analisa) | Campo da `Entrada` | `team_id`, `project_id`, `filtros?` |
| Infra/segredo (como o agente funciona por baixo) | Continua env var, carregado por `linear_client.py`/cliente do modelo | `OPENAI_API_KEY`, `LINEAR_API_KEY`, Langfuse |

Contratos por agente, cada um na própria pasta (não mais um `contracts.py`
único na raiz):

- **`a10/contracts.py`** — `EntradaA10(team_id: str, filtros: dict | None)`,
  `SaidaA10(sugestoes: list[SugestaoA10])`.
- **`a14/contracts.py`** — `EntradaA14(project_id: str)`, `RelatorioA14`
  (saída já desenhada antes).

`main.py` é quem sabe que o time é `Projetos-livemode` (via env var
`LINEAR_TEAM_ID`, não constante hardcoded), descobre os `project_id`s
chamando `linear_client.listar_projetos(team_id)`, monta cada `Entrada` e
chama `rodar_a10(entrada)`/`rodar_a14(entrada)` — sem nunca olhar pra
dentro de `a10/`/`a14/` além do contrato.

**Em aberto** (baixo risco, decidir na hora de codar): como o `__main__`
standalone do A14 recebe um `project_id` pra montar sua própria `Entrada`
— provável candidato é argumento de CLI (`python -m a14.agent
<project_id>`), já que não existe um "projeto default" razoável.

## Gap-check 2026-08-26 — cinco lacunas fechadas

msilva pediu uma revisão do desenho ("há algo que nos escapou?") antes de
começar a codar. Cinco pontos identificados, todos fechados na mesma sessão:

- **Escopo do backlog** — ver a seção "Escopo: backlog inteiro, não um
  projeto só", acima.
- **`load_dotenv()` por entrypoint** — a decisão de rodar cada agente
  isolado (`python -m a10.agent`) abriu essa lacuna: se só `main.py`
  carregasse o `.env`, `poe a10` sozinho rodaria sem nenhuma env var.
  Fechado: `load_dotenv()` é chamado no **nível de módulo** dos três
  entrypoints (`a10/agent.py`, `a14/agent.py`, `main.py`) — é idempotente,
  chamar mais de uma vez (quando `main.py` importa os dois) não tem efeito
  colateral.
- **Paginação do Linear GraphQL** — a API pagina por cursor
  (`pageInfo.hasNextPage`/`endCursor`); como "backlog inteiro" virou
  escopo explícito (acima), isso deixou de ser opcional. Fechado: as
  funções de query dentro de `linear_client.py` fazem o loop de paginação
  internamente, devolvendo a lista já completa pros callers — quem chama
  `listar_issues()`/`listar_projetos()` nunca vê cursor nem página parcial.
- **Versões dos pacotes não pinadas** — testado de verdade, não só
  decidido: `.venv` criado no repo, `pip install langchain langgraph
  langchain-openai langfuse python-dotenv poethepoet` rodou limpo no
  Python 3.14.7. Versões resolvidas por esse install, a pinar em
  `[project.dependencies]` do `pyproject.toml`:
  `langchain==1.3.17`, `langgraph==1.2.11`, `langchain-openai==1.6.0`,
  `langfuse==4.14.5`, `python-dotenv==1.2.3`, `poethepoet==0.48.0`,
  `pydantic==2.13.4`.
- **Risco do Python 3.14** — descartado pelo mesmo teste acima: instalação
  limpa, sem erro de wheel ausente pra nenhum pacote (incluindo
  `pydantic-core`, que é a parte em Rust mais provável de atrasar suporte
  a uma versão nova do Python).

Sources: [GPT-5.6 model guidance — OpenAI API docs](https://developers.openai.com/api/docs/guides/latest-model).

```
livemode-fluxo-agentico/
  .env                    # gitignored
  .gitignore
  pyproject.toml          # deps (PEP 621) + [tool.poe.tasks]
  linear_client.py        # GraphQL cru + modelos Pydantic do shape do Linear —
                           # compartilhado entre A10 e A14; não fere "uma
                           # frente não chama a outra", isso vale entre
                           # LangGraph/Skill/Agent SDK, não dentro da mesma frente
  a10/
    contracts.py            # EntradaA10, SaidaA10 — porta pública do agente
    regras.py                # cálculo puro: dias_sem_atualizacao, carga_por_assignee
    tools.py
    agent.py                # create_agent + bloco __main__ p/ rodar isolado
    tests/
  a14/
    contracts.py            # EntradaA14, RelatorioA14 — porta pública do agente
    regras.py                # cálculo puro: progresso_por_milestone
    memoria.py                # snapshot store: ler/salvar último RelatorioA14 por projeto
    tools.py
    agent.py                # create_agent + bloco __main__ p/ rodar isolado
    tests/
  main.py                  # orquestrador: monta as Entradas, chama os agentes, gera HTML
```

Tasks do `poe`:

```
[tool.poe.tasks]
a10 = "python -m a10.agent"     # roda só o A10, imprime as sugestões (JSON)
a14 = "python -m a14.agent"     # roda o A14 (loop por projeto), imprime os relatórios
run = "python main.py"          # pipeline completo: A10 + A14 + HTML final
```

## Memória entre execuções (A14) e testes/benchmark (2026-08-26)

msilva perguntou como a memória dos agentes persiste, e pediu suíte de
testes por agente + benchmark no Langfuse — "pode ser simples".

**Memória — só existe uma lacuna real, e é do A14.** Dois tipos de
"memória" diferentes, que não devem ser confundidos:

- **Memória de execução** (histórico de mensagens/tool-calls dentro de um
  `.invoke()`) — já resolvida pelo próprio `create_agent`, efêmera por
  natureza. Não precisa de desenho: isso só importaria se houvesse um
  humano conversando com o agente em várias rodadas (checkpointer,
  `thread_id`), e não é o caso — é CLI single-shot.
- **Memória de domínio entre execuções** — real e já estava implícita na
  saída do A14 ("o que mudou desde a última vez", seção "Saída" acima),
  só ninguém tinha notado que isso exige guardar o relatório anterior em
  algum lugar. Fechado: `a14/memoria.py` — um port simples
  (`ler_ultimo_relatorio(project_id)` / `salvar_relatorio(project_id,
  relatorio)`), adapter de arquivo pra essa fase (`.state/` local,
  gitignored — sem banco). **O A10 não precisa disso** — sua saída é
  sugestão fresca a cada rodada, sem delta na spec.

**Testes por agente** — três níveis, deliberadamente simples:
1. Cálculo puro (`regras.py`) — testado direto, sem rede/LLM.
2. Agente com um `PortfolioReader` fake — sem bater no Linear de verdade.
3. Caso do Farol como teste de aceitação único.

**Benchmark no Langfuse** — Datasets + Experiments (recurso nativo do
Langfuse, não um framework de eval à parte): um dataset pequeno (Farol +
1–2 casos), roda o agente contra cada item, trace linkado ao dataset,
avaliação visual na UI do Langfuse — sem scorer automatizado nessa fase.

## Revisão de uma análise externa (GPT) sobre arquitetura hexagonal

msilva trouxe uma proposta de arquitetura escrita por outro assistente
(GPT) — `src/fluxo_agentico/{domain,application,adapters}/` completo,
`bootstrap.py` como composition root, `Protocol`s pra tudo, classes
`RunA10`/`RunA14`/`RunPipeline`, suíte de 4 níveis. Avaliada e só
parcialmente adotada:

**Adotado:**
- DTO do Linear ≠ tipo de domínio, com mapper explícito — o cálculo e o
  julgamento do agente trabalham em cima de `Issue`/`Project`/`Milestone`
  estáveis, não do shape cru do GraphQL.
- Cálculo determinístico como função pura, separada do wrapper de tool
  (`regras.py`) — inclui a dica de injetar `agora` explicitamente em vez
  de `datetime.now()`, evita teste não-determinístico.
- `PortfolioReader` como `typing.Protocol` — formaliza o port que já
  existia informalmente.

**Rejeitado:**
- **A árvore `domain/application/adapters/` completa** — hexagonal "de
  livro-texto", pesada demais pro tamanho desta PoC; contradiz o padrão
  que a gente vem seguindo a cada rodada (cortar pra estrutura mínima).
  `a10/`/`a14/` continuam pastas simples, sem 4 níveis de profundidade.
- **`A10Analyzer`/`A14Reporter` como `Protocol` que o agente LangChain
  implementaria** — o ponto mais importante rejeitado, não é só peso:
  isso reabre exatamente o padrão que
  [[Como implementar a PoC do A10+A14 (LangGraph, Skill, Agent SDK)]] já
  descartou — uma interface compartilhada que implementações LangGraph e
  Claude/Agent SDK poderiam ambas satisfazer colapsaria as três frentes
  numa só, com portas de entrada diferentes, destruindo o ponto real da
  PoC (ter opções de verdade). "Agente define a própria interface" é
  `Entrada → Saída` do agente — não mais uma camada por cima pra trocar o
  framework por baixo.
- **Classes `RunA10`/`RunA14`/`RunPipeline`** — cerimônia sem ganho em
  Python pra empacotar uma chamada de função; `rodar_a10(entrada)` como
  função já é testável e substituível sozinha.
- **Suíte de 4 níveis** (unit/integration/acceptance/smoke) — mais do que
  "pode ser simples" pedia; os 3 níveis acima (cálculo puro, agente+fake,
  Farol) cobrem o mesmo território sem a cerimônia extra.

## O que isso resolve na página-mãe

As duas pendências operacionais que [[Como implementar a PoC do A10+A14
(LangGraph, Skill, Agent SDK)]] deixou em aberto (origem da API key, quem paga)
estão fechadas aqui — ver "Modelo e empacotamento" acima.

## Um risco encontrado no caminho, não sobre design

Ao configurar a `OPENAI_API_KEY` no `.env` do repo, o repo **não tinha
`.gitignore`** — o segredo estava exposto pro próximo `git add` amplo. Criado
`.gitignore` cobrindo `.env`/`.venv`/`__pycache__`, checado que `.env` agora
está de fato ignorado. Não é uma decisão de desenho, mas fica registrado porque
é exatamente o tipo de coisa que este vault existe pra não deixar passar batido
— ver a regra de credenciais em `CLAUDE.md`.

## Open questions

- Prompt exato de cada agente, corpo real de `linear_client.py`, e a forma
  exata do HTML — código, não desenho; é a parte que msilva quer construir e
  estudar ele mesmo.
- Estimativa de esforço (Estimate) da issue `PRO-392` não foi definida nesta
  sessão.
- ~~**Se o agente precisar ser conversacional** (Slack, chat interno,
  Telegram), a escolha "agent" via `create_agent`/LangGraph ainda se
  sustenta?~~ **Fechada 2026-08-27.** Pergunta de Luís em
  [[2026-08-26 1-1 Matheus - Luís (agentes A10-A14)]], anterior a esta
  sessão; tinha dois sentidos diferentes. **Múltiplos triggers/adapters**
  (Slack, cron, outro agente chamando) já está resolvido por design — é
  exatamente pra isso que serve o port `Entrada → Saída` da seção "Ports
  and adapters" abaixo, que não sabe nem se importa quem chamou.
  **Memória dentro de uma mesma conversa** (várias mensagens seguidas,
  contexto acumulado) é uma pergunta diferente — este desenho assume CLI
  single-shot, sem `thread_id`/checkpointer (ver "Memória entre execuções"
  acima). **Respondida por Luís, 2026-08-27** (Slack, DM, não uma reunião):
  era essa segunda leitura que ele tinha em mente. Resposta dele, verbatim:
  *"O 2 diria que é desejável não travarmos a solução para inviabilizar,
  mas se for mais simples não pensar nisso, pode seguir sem isso."* Ou
  seja: não precisa construir agora, mas o desenho não pode fechar a porta
  pra isso depois. A ideia de `thread_id`/`chat_id` por adapter (proposta
  por msilva no mesmo dia, ver acima) já cumpre essa condição — nenhum
  adapter atual precisa de memória, e um futuro adapter conversacional
  poderia ganhar sem redesenhar o agente.
