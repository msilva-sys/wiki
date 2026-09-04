---
type: meeting-prep
status: superseded
updated: 2026-09-04
date: 2026-09-04
attendees: [Matheus Silva, Carolina Bezerra]
tags: [agent-flow, a10, a14, métricas]
---
# Prep — Carolina × Matheus (2026-09-04)

**A reunião "Carolina / Matheus" das 17h foi cancelada.** As mesmas dúvidas
foram reformatadas (segunda pessoa, sem checklist de reunião ao vivo, sem a
seção de contexto interno sobre a Carol) num HTML pra msilva enviar a ela de
forma assíncrona pelo Slack. Esta página fica como o registro do prep
original — se a Carol responder de fato, as respostas entram como fan-out
normal (decisions/concepts/pages afetadas), igual sairia de uma reunião.

Prep pontual para a reunião "Carolina / Matheus" às 17h (Sala Handebol). Pauta:
métricas de saúde de portfólio (A10), métricas de progresso de projeto (A14), e
onde essas entregas de insight acontecem hoje (Linear? Slack?). Puxada de
[[Agent Flow]], [[Fronteira A10×A14 (informação e métricas)]] e das decisions
de 2026-09-01/02/03.

## Pra situar: o que são A10 e A14

- **A10 Portfolio** — agente que ajuda a decidir **onde a empresa aloca
  capacidade**, comparando iniciativas que disputam os mesmos times. Olha só
  no nível de **iniciativa**, nunca desce a uma tarefa específica (regra
  reforçada por feedback do Luís: portfólio é visão gerencial, produto é
  granular).
- **A14 PM** — agente que ajuda a decidir **como entregar** uma iniciativa já
  aprovada: escopo, sequência, dependências, prazo. Olha no nível de
  **entrega/tarefa** dentro de uma iniciativa — é quem "desce" onde o A10 não
  desce.

## Já resolvido (não precisa perguntar)

| Termo | O que quer dizer | Como é calculado hoje |
|---|---|---|
| **Capacidade** (`capacity_share`) | A fatia da atenção/esforço do time que uma iniciativa está consumindo — o sinal que existe pra dizer "essa iniciativa está tomando um pedaço desproporcional do time" quando várias disputam a mesma equipe ao mesmo tempo. | Na prática, é uma **contagem de issues**, não de esforço: nº de issues ativas da iniciativa ÷ nº total de issues ativas do backlog do time inteiro. Uma issue de 1 ponto conta igual a uma de 8 — não usa Estimate/story points. Código: `a10/rules.py:87-118`. **Exemplo real (2026-09-04)**: a iniciativa *Airtable GC* apareceu no status do A10 com 15,4% de capacidade — ou seja, das issues ativas de todo o time, 15,4% são dessa iniciativa. Numa conta de brinquedo: time com 50 issues ativas ao todo, 8 delas de uma iniciativa → `8/50 = 16%`. |
| **Prioridade média** (`avg_priority`) | Tentativa de resumir num número só se as issues de uma iniciativa estão sendo tratadas como urgentes pelo time, pra detectar "priorização desalinhada" (iniciativa importante recebendo tratamento de baixa prioridade, ou vice-versa). | Média aritmética simples do campo `priority` bruto do Linear: `0=Sem prioridade, 1=Urgente, 2=Alta, 3=Média, 4=Baixa`. Sem nenhum tratamento — um `0` entra na conta como se fosse a prioridade mais baixa, quando na real quer dizer "ninguém preencheu esse campo". **Exemplo real (2026-09-04)**: a mesma iniciativa *Airtable GC* apareceu com prioridade média **0** — não porque o time decidiu que é baixa prioridade, e sim porque nenhuma issue dela tinha o campo Priority preenchido no Linear (bate com outro achado do mesmo levantamento: 28 de 35 issues dessa iniciativa com descrição curta — sinal de higiene de dados ruim, não de despriorização real). |
| Onde o A10 publica hoje? | — | Status update nativo do projeto no Linear (`projectUpdateCreate`) — um post agregado por projeto, não comentário em issue individual. Foi revertido de comentário-por-issue em 2026-09-01 por reclamação do Luís (poluía a issue). **Exemplo**: foi assim que os 15,4%/prioridade 0 da Airtable GC chegaram até o msilva — como uma atualização de status do projeto, não um comentário numa issue específica. |
| Onde o A14 publica hoje? | — | Também via `projectUpdateCreate` — mas o log de 2026-08-31 registrava "A14 em comentário na issue", que parece defasado; **confirmar contra o código antes de afirmar isso pra ela**. |
| O Slack já recebe algo hoje? | — | Só indiretamente: projetos do Linear podem ser plugados a canais do Slack, e quando o cron do A10/A14 falha ele publica um alerta no Linear que aparece automaticamente lá (confirmado ao vivo em 2026-09-03). Não existe hoje um push de insight (digest, status) dedicado ao Slack — só esse alerta de erro. **Exemplo real**: em 2026-09-03, um teste do cron gerou um alerta de verdade (`health="offTrack"`, projeto Fluxo Agêntico) que apareceu automaticamente no Slack do time — inclusive sem aviso de que era teste. |

## Perguntas reais para a Carol

**A10 — saúde de portfólio**
- A `avg_priority` bruta (sem tratar `0 = sem prioridade` como "indefinido", e não "baixa prioridade") é confiável pra ela decidir algo hoje, ou é um viés que ela já compensa mentalmente pelas issues mal preenchidas?
- Existe um painel mais amplo desenhado, ainda não implementado, com estas ideias (exemplos abaixo são hipotéticos, só pra ilustrar o mecanismo — não são dados reais) — vale confirmar se ainda é isso que ela esperaria ver:
  - *capacidade alocada por iniciativa* — a mesma métrica de capacidade acima, olhada lado a lado entre iniciativas (ex.: iniciativa A com 40%, B com 15,4%, C com 5% — mostra onde o time está concentrado);
  - *custo acumulado vs. previsto* — quanto já foi gasto numa iniciativa comparado ao que foi orçado (ex.: orçado 20 issues pra entregar, já consumiu 35 — estourou 75%);
  - *concentração de risco* — quanto do portfólio depende de poucas iniciativas (ou poucas pessoas): se uma travar, quanto do todo trava junto (ex.: 60% da capacidade do time está em só 2 das 15 iniciativas ativas);
  - *iniciativas sem entrega há N ciclos* — iniciativas "paradas", sem nenhuma issue fechada em N sprints (ex.: iniciativa sem nenhum fechamento nos últimos 3 ciclos);
  - *fila de demandas não atendidas* — quantas iniciativas já aprovadas ainda nem começaram (ex.: 5 iniciativas aprovadas, zero issues abertas nelas ainda);
  - *taxa de encerramento por ciclo* — quantas iniciativas realmente fecham por ciclo, como indicador de vazão do time (ex.: de 10 iniciativas ativas, 2 fecharam nesse ciclo → 20%).
  
  Isso ainda bate com o que ela disse em 2026-08-24 sobre priorização ser "inteligência transversa" — ou a visão dela mudou o que faria sentido aqui?
- Falta um **loop de retorno A14→A10**: o efeito real de uma entrega (economizou tempo? reduziu erro?) não volta pro A10 pra influenciar a próxima decisão de alocação — hoje é uma via de mão única. Ela já sente falta disso na prática, ou ainda não chegou nesse ponto de maturidade?

**A14 — progresso de projeto**
- Métricas propostas (ainda não implementadas; exemplos abaixo também são hipotéticos) — fazem sentido pra ela, falta ou sobra alguma?
  - *lead time* — tempo entre a iniciativa ser aprovada e a entrega estar realmente em uso, não só "pronta" (ex.: aprovada em 01/08, time começou a usar de fato em 20/08 → ~19 dias de lead time);
  - *aderência à data combinada* — entregou no prazo que foi acordado ou não (ex.: prazo combinado 15/09, entregou 22/09 → não aderiu);
  - *mudança de escopo pós-aceite* — quanto o escopo mudou depois que a entrega já tinha sido aceita/iniciada (ex.: escopo aceito era "relatório mensal", virou "relatório + dashboard" sem reaprovação formal);
  - *tempo bloqueado* — quanto tempo a entrega passou travada esperando outra coisa, pessoa, decisão, dependência (ex.: dos 20 dias corridos da entrega, 6 foram esperando aprovação de outra área → 30% do tempo total foi bloqueio, não trabalho);
  - *retrabalho* — quanto do que foi feito precisou ser refeito (ex.: 3 de 10 issues marcadas "Done" tiveram que ser reabertas depois);
  - *adoção pela área* — a entrega está realmente sendo usada por quem pediu, não só "encerrada" no Linear (ex.: entrega fechada há 3 semanas, mas ninguém na área começou a usar);
  - *efeito medido na área* — o ganho real e concreto, não só "entregamos" (ex.: depois de automatizar um relatório, a área reportou 5h/semana economizadas e queda de 10 pra 2 chamados de suporte por mês).
- Granularidade do A14 é entrega/tarefa dentro da iniciativa — é o nível que ela acompanha, ou ela olha mais fino (subtarefa) ou mais grosso (a iniciativa como um todo)?

**Entrega dos insights**
- Status update nativo do Linear já é suficiente pra ela, ou ela realmente quer um canal do Slack dedicado a receber esses insights (e não só o alerta de falha do cron)? Isso ficou em aberto — "disponível, não configurado", segundo [[Agent Flow]].
- Se quiser Slack dedicado: um canal específico pra isso, ou o canal geral do time já resolve?
- A discrepância comentário-vs-status-update do A14 (ver tabela acima) — ela tem preferência entre os dois formatos? Foi uma reclamação do Luís que mudou o formato do A10 de comentário-por-issue pra status update agregado; vale saber se ela concorda com esse mesmo padrão pro A14.

## Contexto de fundo (lembrar, não perguntar)
- Carol diverge do Luís sobre o que é "transversal": pra ela, priorização é uma inteligência transversa (atravessa vários projetos/times); análise de uso não é (2026-08-24). Isso pode colorir a resposta dela sobre o painel do A10.
- Foi ela quem confirmou que o status readout atual do Linear (o "nosso repórter" da Gabrielle) **não** cobre priorização cross-projeto — só dá visibilidade do que cada um está fazendo, isolado por projeto.
