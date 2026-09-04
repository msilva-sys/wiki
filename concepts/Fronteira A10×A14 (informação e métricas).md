---
type: concept
status: draft
updated: 2026-09-04
aliases: [fronteira A10 A14, N0-N4, niveis de informacao A10 A14, painel de métricas A10 A14, teste da pergunta A10 A14, efeito medido]
tags: [agents, agent-flow, a10, a14, product-scope, metrics]
---

# Fronteira A10×A14 (informação e métricas)

Framework de uma resposta de IA que Luís trouxe, a pedido de msilva em
[[2026-09-02 1-1 Matheus - Luís]] ("fontes e argumentos, inclusive resposta
de IA crua, sem viés"). **Não é opinião original do Luís** — é conteúdo de
IA genérico, ajustado ao nosso contexto pelo próprio doc. Fonte completa:
[[A10 A14 Fronteira, Informação e Métricas]]
(`raw/2026-09-02 A10 A14 fronteira - resposta IA trazida por Luís.html`).
**Ainda não adotado como decisão** — é material de referência para desenhar
a fronteira final entre A10 (Portfolio) e A14 (PM Agent); ver a nuance em
[[2026-09-02 A10 para de expor detalhe de issue, encaminha pro A14]].

## Ajuste explícito ao nosso contexto

O doc se corrige na própria abertura: aqui A14 é "menos produto e mais
entrega" — a maior parte do que se constrói é interna, o "usuário" é uma
área da casa, não existe mercado para validar. Isso reposiciona A14 para
escopo, sequência, dependências e prazo, e devolve a pergunta "isso deveria
ser feito?" para o A10.

## Teste rápido de uma pergunta

Cinco perguntas-diagnóstico para saber de quem é uma decisão:

| Pergunta | De quem é |
|---|---|
| A resposta muda se orçamento/capacidade dos times mudar? | A10 |
| A resposta muda se a área solicitante mudar de necessidade? | A14 |
| A pergunta compara iniciativas diferentes entre si? | A10 |
| A pergunta é sobre como entregar algo já aprovado? | A14 |
| Responder exige autoridade para parar de gastar capacidade? | A10 |

## O que cada um possui

**A10 (Portfolio)** — decide onde a empresa coloca capacidade, comparando
demandas e iniciativas que disputam os mesmos times. Granularidade =
iniciativa, nunca desce a tarefa.
- Recebe: carteira de iniciativas (custo/dono/status), capacidade disponível
  por time, fila de demandas não aprovadas, direcionador estratégico do
  ciclo, **resultado agregado reportado pelo A14**.
- Entrega: aprovar/adiar/recusar demanda nova, ranking de iniciativas com
  critério explícito, recomendação de realocação de capacidade,
  recomendação de encerrar iniciativa em curso.
- Nunca faz: escrever requisito/critério de aceite, ordenar backlog/épico/
  sprint, definir solução técnica, decidir com base em métrica de uma tela.

**A14 (PM Agent, perfil entrega interna)** — decide como entregar uma
iniciativa já aprovada: escopo, sequência, dependências e prazo, com a área
solicitante como cliente. Granularidade = entrega e tarefa, dentro de uma
iniciativa.
- Recebe: iniciativa aprovada (objetivo + capacidade definidos), necessidade
  declarada da área solicitante, dependências técnicas e de terceiros,
  **uso real da entrega depois que ela sobe**, a carteira como contexto em
  leitura.
- Entrega: escopo acordado e critério de aceite, sequência de entrega e
  mapa de dependências, replanejamento diante de risco/bloqueio,
  recomendação de cortar escopo para caber na janela, **resultado medido
  devolvido ao A10**.
- Nunca faz: decidir se a demanda deve ser atendida, mover capacidade entre
  times/iniciativas, aceitar demanda nova direto da área, comparar
  iniciativas distintas entre si.

## Nível de informação (N0–N4)

Regra central: **o A10 nunca recebe item individual abaixo de N1** — só
agregado ("a iniciativa tem 12 entregas, 3 atrasadas, 68% da capacidade
consumida"), nunca a lista das 12. Mais barato de garantir na camada de
contexto do que no prompt.

| Nível | O que é | A10 | A14 |
|---|---|---|---|
| N0 | Direcionador estratégico | lê | — |
| N1 | Iniciativa (dono, capacidade, custo, status) | escreve | lê |
| N2 | Entrega/épico (escopo, data, dependências, aceite) | só agregado | escreve |
| N3 | Tarefa | — | escreve |
| N4 | Evento operacional (PR, chamado, incidente, log de build) | — | só agregado |

Bate com o que já foi implementado em
[[2026-09-02 A10 para de expor detalhe de issue, encaminha pro A14]] (A10
nunca fala de issue específica) — essa implementação é o caso mais estreito
da regra N1/N2 acima, não a regra completa. A permissão de leitura de N0 e,
principalmente, a leitura do **resultado agregado do A14 pelo A10** ainda
não estão desenhadas nem implementadas.

## Painel de métricas — cada agente só argumenta com o seu

Métrica emprestada é o sinal mais confiável de que um agente respondeu à
pergunta do outro (ex.: A14 justificando com custo de oportunidade da
carteira; A10 justificando com velocity do time).

**Painel A10**: capacidade alocada por iniciativa · custo acumulado contra o
previsto · concentração de risco · iniciativas sem entrega há N ciclos ·
tempo desde a última reavaliação · fila de demandas não atendidas (e idade
da fila) · taxa de encerramento por ciclo.

**Painel A14**: lead time da entrega (aprovação → uso real, não o merge) ·
aderência à data acordada (vs. a primeira data, não a replanejada) ·
mudança de escopo após o aceite · tempo bloqueado por dependência ·
retrabalho após a entrega · adoção pela área solicitante · **efeito medido
na área** (horas economizadas, chamados reduzidos, erros evitados).

**Métricas que não servem em contexto interno** — parecem rigorosas e não
são: velocity/story points reportado ao A10 (mede capacidade do time, não
valor entregue); NPS de ferramenta interna com poucas dezenas de usuários
(amostra não sustenta o número); contagem de entregas concluídas (mede
atividade, não resultado — uma iniciativa pode concluir tudo e não mover
nada na área); adoção de ferramenta de uso obrigatório ("meça o efeito, não
o login").

## Faça / não faça — padrões de erro mais comuns

- **Escopo da decisão**: A14 não decide "pausar a iniciativa X" (só metade
  da informação) — A14 escala o sinal ("bloqueado há 3 semanas, consumiu
  60% da capacidade prevista, escalo ao A10 para reavaliar continuidade").
- **Demanda nova**: nunca entra direto no escopo em andamento pelo A14 —
  toda demanda nova passa pelo A10 (registrada na fila com esforço
  estimado; o A14 pode opinar sobre viabilidade, não aprovar consumo de
  capacidade).
- **Granularidade**: A10 nunca desce a nível de tarefa — já é a regra
  implementada em
  [[2026-09-02 A10 para de expor detalhe de issue, encaminha pro A14]].
- **Métrica emprestada**: cada um só sustenta recomendação com o próprio
  painel — a lista de métricas permitidas vira validação de saída no eval,
  quase de graça.
- **Permissão de escrita**: dono único por nível — A10 escreve N1/lê N2
  agregado; A14 escreve N2+N3/lê N1. Base compartilhada sem dono único =
  o último a rodar vence, e ninguém audita quem mudou a prioridade.
- **Handoff A10→A14**: precisa de contrato explícito — objetivo,
  solicitante, capacidade, critério de aceite, prazo de reavaliação.
  Repasse vago ("melhorar o processo de faturamento") faz o A14 preencher
  os buracos sozinho e virar portfólio na marra.
- **Recusa explícita**: se a pergunta exige comparar iniciativas, o agente
  devolve ao A2 com o sinal observado, em vez de inventar a decisão do
  outro agente — modo de falha mais caro é a saída parecer completa e
  confiante sem ninguém perceber que a fronteira foi cruzada.

## Roteamento de exemplo (para o A2)

| Pedido que chega | Vai para | Por quê |
|---|---|---|
| "Financeiro pediu um relatório novo, a gente faz?" | A10 | demanda nova consome capacidade |
| "Em que ordem entregamos os três módulos?" | A14 | sequência dentro do escopo aprovado |
| "Temos time para começar a integração este ciclo?" | A10 | capacidade e trade-off entre iniciativas |
| "A entrega vai atrasar duas semanas, e agora?" | A14 | replaneja; só escala se estourar a janela |
| "Ninguém do Financeiro está usando o que subimos" | A14 | adoção na área é painel do A14 |
| "Essa automação ainda faz sentido?" | A10 | continuidade da iniciativa |
| "Comprar ou construir esse componente?" | A10 + A14 | A14 levanta requisito/esforço; A10 decide, porque muda alocação |

## Heurísticas de detecção de vazamento no eval

- **Teste de verbo**: A14 não deveria dizer "encerrar iniciativa",
  "realocar time", "aprovar demanda", "priorizar entre iniciativas", "custo
  de oportunidade". A10 não deveria dizer "sprint", "tarefa", "critério de
  aceite", "pull request", "bug".
- **Teste do prompt espelhado**: mesmo pedido nos dois agentes — se as
  saídas ficarem parecidas, a separação existe só no nome.
- **Teste de contexto vazio**: tirar a carteira do contexto do A10 — se ele
  ainda responder, está inventando a base da decisão em vez de consultar.

## A lacuna: falta o loop de retorno A14→A10

Com o A14 puxado para "entrega" (não "produto"), ninguém mais pergunta se a
solução resolveu o problema — só se ela foi entregue como combinado. O doc
propõe dois pontos de dono para essa pergunta:

1. **Na aprovação (A10)**: receber o problema declarado + o efeito esperado
   da área, não só o pedido de solução. "Financeiro quer um relatório" é
   pedido de solução; "Financeiro gasta 6 horas por semana conferindo nota"
   é problema, com efeito esperado implícito, e admite respostas mais
   baratas.
2. **Na entrega (A14)**: o efeito medido é o que fecha o ciclo e sobe para
   o A10 decidir o próximo passo. Sem esse retorno, o A10 aprova o ciclo
   seguinte às cegas e a carteira vira registro cartorial.

**Estado real, auditado direto no código em 2026-09-04**: esse loop não
existe. `run_a10` lê só o backlog do Linear via `linear_adapter.list_issues`;
as sugestões do A10 (critérios `item_estagnado`/`priorizacao_desalinhada`/
`gargalo_de_capacidade`/`escopo_descontrolado`, em `a10/contracts.py`) não
dependem de nada do A14; as únicas menções a "A14" dentro do módulo `a10/`
são texto de redirecionamento de chat (`a10/formatting.py`), não dado.
`run_a14` roda em paralelo, por projeto, sem expor nenhum resultado
consumível pelo A10.

**Em aberto para desenhar**:
- o que conta como "efeito medido" no nosso contexto — o painel acima já
  propõe uma definição (lead time, aderência a prazo, retrabalho, adoção,
  efeito na área), mas nada disso é coletado ou persistido hoje;
- o contrato de dado (`A14Outcome`?), persistido reaproveitando o padrão de
  `a10/memory.py` (Postgres) em vez de mecanismo novo;
- como o A10 consome isso — provavelmente um campo novo em
  `PortfolioHealth`, computado em `a10/rules.py::portfolio_health()`;
- a cadência — o cron hoje é diário, mas efeito medido de uma entrega
  provavelmente não muda dia a dia.

## Linhas de guardrail (candidatas para o system prompt)

Resumo — texto completo no raw source:
- **A10**: só recebe dado agregado abaixo do nível de iniciativa; toda
  recomendação cita capacidade, custo, risco ou fila; nunca escreve em
  entrega, épico ou tarefa; se a pergunta não exigir conhecer capacidade e
  custo, devolve ao A2.
- **A14**: nunca decide se uma demanda deve ser atendida, nem realoca
  capacidade; demanda nova da área é estimada e registrada na fila, nunca
  aceita no escopo em andamento; toda recomendação cita prazo, dependência,
  escopo ou uso real; se a resposta exigir comparar iniciativas, devolve ao
  A2 com o sinal observado; nunca escreve na carteira de iniciativas.

## Ligação com o resto do projeto

Alimenta a questão em aberto de
[[2026-09-02 A10 para de expor detalhe de issue, encaminha pro A14]]
(fronteira PM vs. Portfolio, versão ampla ainda não fechada) e nomeia o
design ainda não feito do loop de retorno A14→A10 em [[Agent Flow]].
