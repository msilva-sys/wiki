---
type: synthesis
status: active
updated: 2026-09-16
date: 2026-09-16
aliases: [intake design doc, design doc intake, design doc a1 a2, intake]
tags: [agent-flow, intake, design-doc, a1, a2]
---

# Design Doc — Intake

Construído em chat, seção por seção, usando a skill `design-doc` (baseada em
[[How to Write an Effective Software Design Document]]). Ponto de partida
era o projeto **A1 & A2** no Linear (ver [[linear-a1-a2-project]] — memória,
não página da wiki) e a discovery/decisões de 2026-09-15
([[2026-09-15 Discovery A1 e A2 com Gabrielle]],
[[2026-09-15 Proxy e Fluxo Agêntico com Luís]],
[[2026-09-15 Agente voltado a usuário externo precisa de identidade própria e resposta imediata]]).

**Mudança de arquitetura decidida nesta sessão**: A1 e A2 deixam de ser dois
agentes numerados e viram um agente único, chamado **Intake** — resolve a
Open Issue original "A1+A2 um agente ou dois?" a favor da simplificação que
o próprio Luís já tinha sugerido. As duas responsabilidades (captar/
catalogar vs. buscar contexto/classificar) seguem existindo, mas como
estágios internos descritos, não como agentes numerados separados.

## Objective

O **intake** recebe toda demanda dos sistemas internos do time, independente
de como chegou — canal (Slack, e-mail, etc.) ou lançamento manual direto
numa base — estrutura e persiste cada uma num catálogo central, busca o
contexto de cada demanda, classifica e roteia.

## Background

O problema central hoje é a fragmentação e a falta de integridade/
centralização das demandas: elas entram por vias distintas e desconectadas
— Slack DM (maioria do volume real), o canal oficial
`#resolveaqui-livemode` (minoria), e Airtable (sistema legado, pré-migração
pro Linear) — sem um registro único que amarre tudo. Essa fragmentação é o
que sustenta o roteamento por conhecimento tribal: sem um catálogo central,
só quem está há mais tempo sabe pra quem mandar cada tipo de pedido.

## Goals

- Toda demanda, venha de onde vier (canal ou lançamento manual), fica
  registrada num catálogo central único — acaba a fragmentação nomeada no
  Background.
- Quem faz um pedido recebe resposta em minutos, não depois de um ciclo
  assíncrono completo.
- A rota de uma demanda deixa de depender de quem está há mais tempo saber
  pra quem mandar — decisão de classificação com critério explícito, não
  conhecimento tribal.
- A demanda chega classificada e roteada pro fluxo certo (operacional,
  enablement, projeto) sem que outro agente ou humano precise refazer a
  triagem do zero.

## Non-goals

- **Não é intake geral pra qualquer área da empresa.** Escopo é só os
  sistemas internos do próprio time — LiveScript, Orca, proxy — com
  usuário interno. (Correção de escopo trazida por Luís em 15/09.)
- **Não avalia risco de execução nem decide esteira automática de PR.**
  Isso é de outro agente ou humano, a partir da classificação entregue.
- **Não implementa a mudança nem escreve código.** Execução é do agente
  executor a jusante, não deste.
- **Não decide priorização de portfólio.** O critério de classificação
  usado aqui é independente do critério de priorização de portfólio usado
  em outro lugar do sistema — resolve por tabela a tensão que existia entre
  os dois (ver [[A10 avalia saúde, não prioriza portfólio]]).
- **Não faz discovery de projeto nem escreve PRD.** Isso é de um agente
  específico de descoberta — aqui só se reconhece que é um pedido de
  projeto e se roteia pra lá.

## Interfaces

- **Entrada**: qualquer canal onde demanda já aparece hoje — Slack (DM ou
  canal), e-mail, ou lançamento manual direto numa base (planilha, Linear).
  Sem formato padronizado — é isso que o intake existe pra resolver.
- **Estágio de captação**: estrutura a demanda crua num registro persistido
  no catálogo central (schema em aberto: no mínimo origem/canal, conteúdo
  bruto, timestamp) e responde ao solicitante no mesmo canal, em minutos —
  só um ack, não a decisão final.
- **Estágio de classificação**: lê o registro do catálogo, busca contexto,
  classifica (tipo, e o que mais for definido) e grava de volta a rota
  indicada — não executa nada em outro sistema, só informa pra onde a
  demanda deveria ir.

## Scenarios

**Bug relatado via Slack DM**: alguém manda mensagem direto pro Luís
perguntando por que um filtro do LiveScript não funciona. O intake capta a
mensagem, responde em minutos ("recebi, já te aviso"). No estágio de
classificação, lê o registro do catálogo, busca contexto (Linear, GitHub,
código), classifica como bug operacional e grava a rota indicada. O intake
para por aqui.

**Demanda lançada direto numa base**: alguém (ou um alerta automático de
sistema) cadastra a demanda direto no Airtable ou no Linear, sem passar por
chat nenhum. O intake reconhece esse lançamento como entrada válida,
estrutura, persiste no catálogo e classifica do mesmo jeito. Lacuna real:
sem conversa de origem, não tem solicitante óbvio pra receber o ack (ver
Open Issues).

## Constraints

- **Custo de token é constraint de primeira classe** em todo o Fluxo
  Agêntico — evitar tool calls que trazem contexto cru demais.
- **Não pode introduzir mais um lugar pra checar** — uma ferramenta nova
  que ninguém olha piora a fragmentação em vez de resolver. O catálogo
  precisa viver em algo que o time já usa.
- **Acesso restrito** aos sistemas do próprio time e a usuários internos.

## Open Issues

1. **Onde mora o catálogo central?** Linear (Triagem), Airtable, ou outro —
   restringido pela constraint de não introduzir ferramenta nova.
2. **Quem recebe o ack quando a demanda é lançada direto numa base, sem
   conversa de origem?**
3. **Schema do catálogo** — quais campos além de origem/conteúdo/timestamp.
4. **O intake precisa de persona própria** (nome, "cara")? Ele fala com
   pessoas de outras áreas usando os sistemas do time, o que pode entrar na
   regra de identidade própria por agente
   ([[2026-09-15 Agente voltado a usuário externo precisa de identidade própria e resposta imediata]]),
   mas isso nunca foi confirmado especificamente pra ele.
5. **Canal de exposição do intake** — bot mencionável em DM vs. canal que
   só encaminha, ainda não decidido.

## Relacionado

- [[Agent Flow]]
- [[How to Write an Effective Software Design Document]]
- [[2026-09-15 Discovery A1 e A2 com Gabrielle]]
- [[2026-09-15 Proxy e Fluxo Agêntico com Luís]]
- [[2026-09-15 Agente voltado a usuário externo precisa de identidade própria e resposta imediata]]
- [[A10 avalia saúde, não prioriza portfólio]]
