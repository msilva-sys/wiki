---
type: source
status: stable
updated: 2026-09-16
date: 2026-06-23
aliases: [design doc guide, michael lynch design doc, effective design doc]
tags: [design-docs, writing, process, agent-flow]
---

# How to Write an Effective Software Design Document

Artigo de Michael Lynch (ex-Google, ex-Microsoft), publicado 2026-06-23
(raw/Clippings/How to Write an Effective Software Design Document.md).
Guia prático de como escrever design docs de software — não específico de
nenhum projeto da Livemode, trazido como referência pro design do
[[Agent Flow]] (A1/A2).

## Resumo

Um bom design doc força pensar as decisões difíceis antes de implementar
errado, e é o jeito mais eficiente de coordenar decisões entre pessoas/times.
O quanto investir varia — de um one-pager a 50 páginas — e a régua proposta
é: **qual o custo de errar essa decisão?** Decisão cara e difícil de
reverter (linguagem, storage) merece detalhe; decisão barata de trocar
depois não merece nem debate.

## Principais claims

- **Quando vale escrever um**: responder "sim" pra 2+ destas perguntas já
  justifica o esforço — múltiplas pessoas coordenando implementação; >3
  meses de trabalho full-time; roda em produção por anos; envolve
  colaboração cross-team; requisitos/objetivos ambíguos; existe risco
  catastrófico evitável em tempo de design (segurança, legal).
- **Seções comuns** (nem todas cabem em todo doc): Título, Metadata (autor,
  data, URL, aprovação), Objective (uma frase), Background (por que agora,
  tentativas anteriores), Related documents, **Goals** (em termos de
  impacto pro usuário/negócio, nunca de implementação — "reduzir outages
  de deploy", não "adicionar Kubernetes"), **Non-goals** (o que fica
  explicitamente fora, especialmente o que o leitor poderia presumir estar
  dentro), Scenarios (walkthrough concreto passo a passo), Diagramas,
  Glossário, Constraints, SLOs (uptime/latência/escala), Monitoring/
  alerting, Timeline (por milestone, cada um com um artefato tangível pro
  stakeholder — ex.: UI com dado fake primeiro, pra validar entendimento
  antes de plugar dado real), Interfaces (API/UI/formato de arquivo),
  Dependencies/infra (linguagem, hardware, onde o dado persiste — pondera
  peso pela dificuldade de trocar depois), Security (superfície de ataque,
  trust boundaries), Privacy (dado sensível, retenção, acesso), Legal,
  Logging, **Open Issues** (problema · opções consideradas · próximo
  passo), Resolved Issues (mesma estrutura, já decidido — mantém o
  histórico completo, não só a conclusão), Alternatives Considered (poucas
  linhas por alternativa forte rejeitada, não um ensaio).
- **Diagramas importam mais do que parecem** — o autor já enxerga a
  arquitetura na cabeça, o revisor não; ferramenta de diagrama deve ser
  editável (evitar foto de quadro branco como artefato final).
- **Um design doc que não faz sentido sem contexto externo já nasceu
  quebrado** — a primeira página precisa valer sozinha pra quem nunca
  ouviu você explicar o projeto ao vivo.

## Onde usei

Estrutura de **Non-goals** e **Open Issues** aplicada direto no design doc
de A1/A2 (em construção, ver [[Agent Flow]]): a correção de escopo do Luís
(sistemas internos do time, não qualquer demanda de qualquer área) vira uma
non-goal explícita; as duas perguntas não resolvidas do M0 (A1+A2 um agente
ou dois; A2 acoplado ou não ao critério de complexidade do A10) viram Open
Issues formais em vez de bloquear o design.

## Perguntas em aberto

Nenhuma — é um guia de referência, não uma fonte com claims empíricos a
verificar.
