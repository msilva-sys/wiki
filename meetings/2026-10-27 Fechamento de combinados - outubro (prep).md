---
type: meeting-prep
status: stable
date: 2026-10-27
updated: 2026-10-27
attendees: [Matheus Oliveira da Silva, Gabrielle Ferreira]
aliases: [fechamento de outubro, combinados de novembro]
tags: [combinados, fechamento, pdi, agent-flow, airtable-proxy, liveauth]
---

# Fechamento de combinados — Matheus · 01/10 a 26/10/2026

Preparado com a skill `combinados-do-mes`, modo `preparar`, para a 1:1 de fechamento com [[Gabrielle Ferreira]]. Combinados de origem: DM com a Gabi, 08/10 (ajuste do item 2 aceito por ela em 09/10). PDI: [[PDI Matheus 2026]].

## 1. Resumo do mês

4 combinados: 0 entregues · 0 com atraso · 3 parciais · 1 não entregue · 0 renegociados.

Alguns itens fecham até 31/10 e mudam este quadro: proxy e PRO-903 previstos para esta semana.

## 2. Entregue × combinado

| # | Combinado | Prazo | Entregue em | Status | Evidência |
|---|---|---|---|---|---|
| 1 | Passagem do Luís + Proxy Airtable em produção (com consumidor real) | 23/10 | — | Não entregue | Proxy no Cloud Run desde 14/09 com `min=0`, sem consumidor. PRO-587 (LiveScript) em Todo desde 25/09. Meta atual: esta semana |
| 2 | Ajustar os agentes conforme a v1 da doc da Gabi (PRO-881: 9 responsabilidades, PRO-912 a PRO-920) | 31/10 | 26/10 (só a 2.7) | Parcial: 1 de 9 | PRO-912 e 10 sub-issues em Done; PR #203 e commits de 16 a 26/10 em `livemode-fluxo-agentico` |
| 3 | Levar achados do discovery do [[LiveAuth]] pra Gabi (com decisão sobre ampliar a POC) | 31/10 | 22/10 | Parcial | Relatório na DM com a Gabi, 22/10. Gabi em 23/10: "vou ler com calma". Sem conversa nem decisão sobre a POC |
| 4 | Tarefas multiplayer: PRO-910 e PRO-903 | 31/10 | PRO-903 prevista para 30/10 | Parcial | PRO-903 em Todo, com prazo 23/10 no Linear (desalinhado do combinado). PRO-910 não começou: escopo errado |

## 3. Entregas fora dos combinados

- **Reestruturação do [[Agent Flow]], pedida pelo Luís** (09/10): reorganização do código (PRO-1005, 09 a 14/10), redução do custo de criar e manter agentes (PRO-1143, 22/10), refatoração do fluxo em `main` (23/10) e MCP dos agentes para a Mafê testar (pedido da Gabi em 23/10, resolvido). Pagou um débito técnico aceito antes. Tamanho desconhecido em 08/10. **Impacto:** a v1 só começou em 16/10 e dividiu tempo com ela (combinado 2).
- **A10/A14 vindo de setembro e do feedback da Carol (05/10):** report do A14 em três blocos e legível no celular (PRO-860 e filhas), dashboard no celular (PRO-887 e filhas) e priorização no Linear (PRO-595, 707, 785, 805, 783, 784). Cerca de 25 issues, de 01 a 07/10. **Impacto:** primeira semana do mês fora da v1 (combinado 2).
- **Opta e n8n, herdados da transição** ([[2026-10-07 Divisão de projetos na transição de Luís]]): MCP da Opta (PRO-1097, demanda externa), mapeamento e riscos do n8n (PRO-1198), quebra da credencial da Opta em 22/10. **Impacto:** concorreu com o proxy e o multiplayer na segunda quinzena (combinados 1 e 4).
- **Proxy, preparação:** riscos e melhorias (PRO-997), demo com Luís e Yasmin (PRO-1004), plano de estresse (PRO-1127), medição de carga (PRO-1197, aberta). Prepararam o combinado 1 sem fechá-lo.
- **Segurança do LiveAuth:** correções médias e baixas (PRO-1209, 26/10) e 4 bugs abertos (PRO-1205 a 1208). Sem impacto em outubro.

## 4. Atrasos e não entregas

- **1. Proxy em produção — não entregue.** Causa: dependência de outra pessoa. O go-live seria feito junto com o Luís, que ficou indisponível nas últimas semanas. Risco avisado à Gabi em 27/10, depois do prazo. Agora: terminar esta semana.
- **2. Agentes v1 — parcial (1 de 9).** Causa: tempo dividido com demandas paralelas, principalmente a reestruturação do fluxo pedida pelo Luís. A Gabi já sabe do débito técnico, resolvido na reestruturação. Agora: o restante da v1 na primeira quinzena de novembro.
- **3. LiveAuth — parcial.** Causa: o discovery foi entregue por escrito, sem a conversa que leva à decisão. Agora: pedir a decisão sobre ampliar a POC.
- **4. Multiplayer — parcial.** PRO-903 fecha esta semana. PRO-910 junta itens da v1 (2.4 · E, PRO-948), um pré-requisito (memória de leituras, decisão 5 do mapa) e uma evolução (2.10, aprender sozinho), e parte disso já foi entregue. Agora: refinar o escopo em novembro.

## 5. Padrão e degrau

- **Padrão que ajuda:** entrega rápida quando o critério de aceite está escrito. A 2.7 levou uns 5 dias de trabalho, com os cenários da doc como critério. O report do A14 saiu em 06/10, antes dos prazos de 07, 08 e 09/10. O dashboard no celular saiu num dia.
- **Padrão que atrapalha:** demanda paralela entra sem renegociar o combinado. Cerca de 35 issues fora dos combinados contra 1 combinado entregue. O impacto nos combinados só aparece no fechamento. O go-live do proxy já escorregou de 10/09 para 16/10, depois para 23/10 e agora para esta semana, sempre dependendo de outra pessoa.
- **Cruzamento com o PDI:** **F1.2** (comunicar async e frequente; meta de que nenhuma decisão relevante chegue ao 1:1 como novidade). Trocar a v1 pela reestruturação foi decisão de prioridade que chegou ao fechamento. **F2.3** ("vender" projeto em valor de negócio): o LiveAuth ficou sem a conversa que leva à decisão. **Fora do PDI:** dimensionar combinados e gerenciar dependências, candidatos ao CP2 (27/10) ou ao CP3 (10/11).
- **Próximo degrau:** renegociar no dia. Quando entrar uma demanda fora do combinado, mandar no mesmo dia uma mensagem à Gabi com o que entra e qual combinado atrasa ou sai. **Como se percebe:** nenhum combinado aparece atrasado pela primeira vez no fechamento.

## 6. Prévia dos combinados de novembro

Fluxo Agêntico na primeira quinzena; o resto na segunda.

| # | Combinado | Prazo | Como a gente sabe que deu certo | Origem | Issues |
|---|---|---|---|---|---|
| 1 | Escopo da PRO-910 refinado e validado com a Gabi | 05/11 | 910 reescrita, sem sobreposição com a v1 e com a evolução 2.10, com o ok da Gabi | Pendente do mês anterior | PRO-910 |
| 2 | v1 · marco 1: memória de leituras + 2.2 Alinhar escopo + 2.1 Garantir o prazo | 06/11 | Cenários de cada doc reproduzidos no projeto de teste | Pendente do mês anterior | PRO-913, PRO-914, memória (criar depois da 1:1) |
| 3 | v1 · marco 2: v1 completa (2.3, 2.4, 2.9, 3.1, 3.2, 3.3) | 13/11 | Cenários reproduzidos; report semanal do A14 rodando com as 6 responsabilidades; carteira do A10 no dashboard | Pendente do mês anterior | PRO-915 a PRO-920 |
| 4 | 4 bugs de segurança do LiveAuth corrigidos + decisão da Gabi sobre ampliar a POC | 25/11 | PRO-1205 a 1208 em Done; decisão registrada no Slack ou no Linear | Pendente do mês anterior + novo | PRO-1205 a PRO-1208 |
| 5 | Renegociar no dia toda demanda fora dos combinados | 30/11 | Cada demanda nova tem mensagem à Gabi no mesmo dia; nenhum atraso chega ao fechamento sem aviso | Degrau | — |

Fora da prévia: proxy e PRO-903 (fecham em outubro; voltam como pendência se escorregarem); v2 do roadmap (intenção, não entrega); PoCs de A1/A2 e Cognee (PRO-870).

## 7. Observações

- **Fora do Linear:** nada.
- **Para conversar na 1:1:**
  - **Tamanho do marco 2:** 6 responsabilidades em 5 dias úteis (09 a 13/11), com a 2.7 de referência em uns 5 dias de trabalho. Fecha só se as outras forem bem menores ou reaproveitarem a base da 2.7. Definir na 1:1 a data de recuo (por exemplo, A10 em 27/11) e aplicar o degrau no primeiro sinal.
  - **Bugs de segurança do LiveAuth na segunda quinzena:** PRO-1205 ("qualquer pessoa de fora consegue usar o LiveAuth em nome de um colaborador que clicar num link") é grave. Confirmar com a Gabi se pode esperar até 25/11.
  - **Decisão sobre ampliar a POC do LiveAuth:** pedir na própria 1:1.
  - **PRO-910:** a sugestão é cancelar e redistribuir (PRO-948, issue de memória de leituras, evolução 2.10), com o ok da Gabi, porque o item veio da votação do time.
  - **[[Onboarding]] (PRO-880):** a confirmar se entra na segunda quinzena de novembro.
  - **PRO-903:** alinhar o prazo do Linear (23/10) com a data real.
