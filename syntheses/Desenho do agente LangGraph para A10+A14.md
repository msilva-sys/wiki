---
type: synthesis
status: active
updated: 2026-08-26
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

## Segurança contra loop e observabilidade

Dois mecanismos complementares, não concorrentes:

- **`recursion_limit`** — teto de passos nativo do LangGraph/`create_agent`,
  default **25**, configurável via `config={"recursion_limit": N}` no
  `.invoke()`. Levanta `GraphRecursionError` e para a execução — trava
  mecânica, funciona mesmo sem ninguém observando.
- **Langfuse Cloud** — só para observabilidade (trace de tool-call, prompt,
  tokens), decisão explícita de msilva, não como mecanismo de trava. Setup:
  `pip install langfuse`, três env vars (`LANGFUSE_PUBLIC_KEY`,
  `LANGFUSE_SECRET_KEY`, `LANGFUSE_BASE_URL`, já configuradas no `.env` do
  repo), um `CallbackHandler()` passado no mesmo `config` do `.invoke()`.

Sources: [GRAPH_RECURSION_LIMIT — Docs by LangChain](https://docs.langchain.com/oss/python/langgraph/errors/GRAPH_RECURSION_LIMIT),
[Open Source Observability for LangGraph — Langfuse](https://langfuse.com/guides/cookbook/integration_langgraph).

## Modelo e empacotamento

- **OpenAI, não Anthropic** — `langchain-openai`. Resolve as duas pendências
  que ficaram em aberto na página-mãe (origem da key, quem paga): a chave já
  existe e já está configurada no `.env` do repo.
- **`pip` + `venv`** — sem convenção prévia da Livemode pra Python (o
  [[Airtable Proxy]] é Go); escolha livre de msilva.

## Estrutura de pastas e tooling (fechada em sessão de continuação, mesmo dia)

Depois do desenho inicial, mais uma rodada de brainstorm em chat acrescentou
contratos tipados e um task runner — encaixe, não mudança de direção:

- **Pydantic para contratos** — já vem transitivamente via `langchain`/
  `langgraph` (Pydantic v2), agora declarado explícito. Dois usos
  distintos: os modelos que espelham o shape do GraphQL do Linear (`Issue`,
  `Project`, `Milestone`) ficam dentro de `linear_client.py`, junto do único
  código que fala com a API; os modelos de **saída** dos agentes
  (`SugestaoA10`, `RelatorioA14`) ficam em `contracts.py`, na raiz — são o
  que dá pra forçar como `response_format` do `create_agent`, então cada
  agente devolve um objeto validado, não texto livre, e `main.py` consome
  isso direto pra montar o HTML.
- **`poethepoet` (comando `poe`) para task management** — só task runner,
  não substitui `pip`+`venv` como gerenciador de dependência (opção
  considerada e descartada: trocar tudo por Poetry). Precisa de um
  `pyproject.toml`; já que ele vai existir mesmo, as dependências passam a
  ser declaradas ali também (`[project.dependencies]`, PEP 621, que o
  `pip` já entende via `pip install -e .`), em vez de um `requirements.txt`
  separado.
- **Cada agente roda sozinho** — `a10/agent.py` e `a14/agent.py` ganham um
  bloco `if __name__ == "__main__":` que chama a própria função
  (`rodar_a10()`/`rodar_a14()`) e despeja o contrato em JSON
  (`.model_dump_json()`) no stdout. `main.py` fica só o orquestrador do
  pipeline completo (A10 + A14 + HTML final) — rodar um agente isolado não
  gera HTML, só o contrato bruto: o ponto é depurar um sem esperar o outro.

```
livemode-fluxo-agentico/
  .env                    # gitignored
  .gitignore
  pyproject.toml          # deps (PEP 621) + [tool.poe.tasks]
  linear_client.py        # GraphQL cru + modelos Pydantic do shape do Linear —
                           # compartilhado entre A10 e A14; não fere "uma
                           # frente não chama a outra", isso vale entre
                           # LangGraph/Skill/Agent SDK, não dentro da mesma frente
  contracts.py            # modelos Pydantic de saída: SugestaoA10, RelatorioA14
  a10/
    agent.py               # create_agent + bloco __main__ p/ rodar isolado
    tools.py
  a14/
    agent.py               # create_agent + bloco __main__ p/ rodar isolado
    tools.py
  main.py                  # orquestrador: A10 + A14 (loop por projeto) + HTML
```

Tasks do `poe`:

```
[tool.poe.tasks]
a10 = "python -m a10.agent"     # roda só o A10, imprime as sugestões (JSON)
a14 = "python -m a14.agent"     # roda o A14 (loop por projeto), imprime os relatórios
run = "python main.py"          # pipeline completo: A10 + A14 + HTML final
```

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
