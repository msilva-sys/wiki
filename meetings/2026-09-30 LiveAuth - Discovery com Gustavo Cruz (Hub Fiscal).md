---
type: meeting
status: stable
updated: 2026-09-30
date: 2026-09-30
attendees: [Matheus Silva, Gustavo Cruz]
transcription_confidence: low
aliases: [discovery liveauth gustavo, hub fiscal gustavo]
tags: [liveauth, auth, security, fiscal, discovery, sentinela]
---

# LiveAuth — Discovery com Gustavo Cruz (Hub Fiscal) — 2026-09-30

Reunião de discovery do [[LiveAuth]]: msilva levanta requisitos com Gustavo
Cruz, dono do "Hub de Automações Fiscais" (projeto de contabilidade), um
caso real de autenticação própria por senha que o LiveAuth quer substituir.

**Confiança de transcrição baixa** — call gravada num único canal de
microfone, sem diarização entre os dois falantes. Atribuição abaixo
reconstruída por conteúdo e confirmada por msilva em chat (2026-09-30), não
por rótulo confiável do Granola — que, pelos nomes nos itens de ação
originais, parece ter invertido Matheus e Gustavo.

## Decisions

Nenhuma decisão formal fechada — reunião de discovery.

## Commitments

- Gustavo vai rodar o **Sentinela** no hub de automações fiscais (projeto
  de contabilidade), pra cobrir a questão de força bruta / rate limiting.

## Open questions

- Se o Hub de Automações Fiscais do Gustavo vai migrar pro LiveAuth ou só
  recebe mitigação pontual via Sentinela por enquanto — não ficou definido.
- Desenho de granularidade de permissão por time (ex.: leitura sem edição)
  ainda não modelado tecnicamente, só citado como requisito.
- Caso de uso da Marina Ferrão (atendimento) chegou de segunda mão via
  Gustavo — não confirmado diretamente com ela.

## Facts stated

- Gustavo: hub tem ao menos 4 dashboards protegidos por senha (fechamento
  fiscal, painel de faturamento, impostos pagos, painel de compras); dados
  sensíveis incluem salários e valores de fornecedores.
- Gustavo: o hub tem outros usuários ativos além dele, todos autenticados
  por senha — não é só ele quem acessa.
- Gustavo: dados extraídos via API própria sobre o ERP **TOTVS**; considera
  isso uma camada extra de segurança (dado já sai de um sistema fechado).
- Gustavo: senhas e credenciais de API ficam no GitHub (plano Pro);
  processos e encaminhamentos também são geridos lá dentro.
- Gustavo: hub é acessível externamente, travado só por senha — sem rate
  limiting, vulnerável a força bruta via bot.
- Gustavo: tinha visto o Sentinela "ontem", ainda não tinha rodado no
  projeto de contabilidade.
- msilva: LiveAuth começou escopado a projetos (POC atual) e ele quer
  expandir pra autenticação/autorização da organização inteira.
- msilva: motivação é evitar retrabalho — cada área implementa sua própria
  lógica de auth — e oferecer granularidade de permissão por time.
- msilva: já tinha conversado com [[Marina Ferrão]] (atendimento) sobre um
  caso de uso de permissão diferenciada: planilha do núcleo criativo que
  outros times precisam ler, mas só o time dono pode editar.
- msilva: acha que o Sentinela já deve cobrir força bruta/rate limiting no
  hub do Gustavo sem prompt adicional — só lembrou disso depois de já ter
  oferecido mandar um prompt de rate limiting por Slack (oferta descartada
  depois, não será enviada).

## Notable quotes

- Gustavo: *"Livemode é tudo acessível por fora, né? É uma pessoa com um
  bot, alguma coisa ali, conseguiria por força bruta descobrir a senha."*
- msilva: *"A gente queria expandir isso... para organização inteira uma
  questão de a gente lidar com esse processo de autenticação e
  autorização."*

## Relacionado

- [[LiveAuth]]
- [[Gustavo Cruz]]
