---
type: decision
status: active
updated: 2026-09-09
date: 2026-09-09
aliases: [não unificar os projetos de proxy, projetos de proxy separados]
tags: [linear, airtable-proxy, process, project-management]
---

# Manter os projetos de proxy separados no Linear

## Resumo

Os projetos da iniciativa **Airtable GC — Governança e Confiabilidade**
continuam separados. Nada é fundido. A proposta de unificação levantada por
[[Carolina Bezerra]] na [[2026-09-08 Weekly - Projetos e Tarefas]] não é
adotada.

Os quatro seguem como estão:

- `Proxy do Airtable`
- `Proxy expandido para outros apps`
- `Proxy em produção validado c/ LiveScript`
- `LiveMode Data Hub (POC)`

## Origem

Na weekly de 08/09, olhando a lista de projetos, Carol: *"eu acho que esses
três aí deveriam ser uma coisa só."* [[Maria Fernanda Lemos]] concordou —
*"é a mesma coisa pelo que eu tô entendendo."* O Luís, que desenhou a
estrutura, estava ausente (indisposto), então ninguém defendeu o desenho na
hora.

## A análise: as duas partes falavam de coisas diferentes

Levantado como hipótese ao registrar a reunião em 2026-09-09, e
**confirmado por msilva na mesma sessão**:

> "Eles estão falando de coisas diferentes, de fato. O ponto do Luís é
> mantermos frentes de trabalhos diferentes, enquanto a Carol acha que são
> a mesma coisa." — msilva, 2026-09-09

- **Luís** separou por **frente de trabalho** — cada projeto é um segmento
  distinto de entrega dentro da mesma iniciativa. É a definição registrada
  em [[Linear Project Structure]]: projeto como *"um pedaço, uma parte
  daquela iniciativa"*, não como "coisa grande / coisa pequena".
- **Carol** olhou para **três nomes parecidos numa lista** e leu
  redundância. A objeção dela é de legibilidade do portfólio, não de
  modelagem.

Como as duas leituras respondem perguntas diferentes, fundir resolveria o
problema da Carol destruindo a estrutura do Luís — o custo cai todo de um
lado só. **Prevalece a separação.**

## O que isso não resolve

A objeção da Carol continua legítima no que ela realmente é: os nomes
confundem quem lê o painel. Duas coisas seguem em aberto e são o caminho
certo pra endereçar isso **sem** fundir nada:

1. **Renomear `Proxy em produção validado c/ LiveScript`.** msilva já
   admitiu na própria weekly que o nome está errado — *"seria validado com
   algum projeto"* — porque presume que o LiveScript é a aplicação que vai
   apontar pro proxy, decisão que ainda não foi tomada e que é do Luís (ver
   [[2026-09-04 1-1 Matheus - Luís]]). Renomear resolve boa parte da
   confusão de nomes de uma vez. **Não decidido aqui** — depende de saber
   qual aplicação será.
2. **Corrigir o projeto que o painel de portfólio lê.** O painel aponta pra
   `Proxy do Airtable`, e não pro projeto onde o trabalho de fato acontece
   — parte da sensação de que os projetos são "a mesma coisa" vem daí: um
   deles parece vazio de progresso porque o painel está lendo o outro. Ação
   da Mafê com a Gabrielle, não de msilva. Ver
   [[2026-09-08 Weekly - Projetos e Tarefas]].

## Pendência de comunicação

Carol levantou a proposta em fórum e não recebeu resposta na hora — o Luís
não estava lá. A decisão de manter separado é de msilva, com autonomia já
registrada para decisões de organização no Linear
([[2026-08-19 1-1 Matheus - Luís]]: Luís explicitamente *"não me considero
dono do projeto"*, só quer ser avisado do que mudou). **Mas ela não sabe
disso ainda** — e a próxima weekly é 10/09, 15:00. Vale levar a distinção
frente-de-trabalho × nome-confuso pra mesa, junto com a proposta de
renomear, em vez de deixar a proposta de fusão voltar sem resposta.

## Referências

- [[2026-09-08 Weekly - Projetos e Tarefas]] — onde a proposta surgiu
- [[Linear Project Structure]] — a estrutura, sua origem com Luís, e a
  tensão registrada
- [[Airtable Proxy]]
