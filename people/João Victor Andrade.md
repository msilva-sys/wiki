---
type: person
status: active
updated: 2026-09-09
aliases: [João Victor, JV]
tags: [people, crm, monday, linear]
---

# João Victor Andrade

**Data de início corrigida para ~2026-08-03.** Antes registrada como "novo
em 2026-08-17" (a data em que ele foi *mencionado*, em
[[2026-08-17 Weekly - Projetos e Tarefas]], não necessariamente quando
entrou). As palavras dele em [[2026-08-25 1-1 Matheus - João Victor]] — na
quarta semana em 2026-08-25, completando um mês em 2026-09-03 — colocam o
início por volta de 2026-08-03. Primeira mão e autoconsistente, preferido
ao enquadramento anterior de segunda mão. As três primeiras semanas foram
um contrato de onboarding definido.

## Dono / decide

- Trabalho de onboarding: cruzar os fluxos de N8N e Monday sob
  responsabilidade de [[Yasmin Macedo]], construir um mapa detalhado da
  arquitetura do CRM, padronizar e limpar dados.
- Criou um canal no Slack para o trabalho de CRM com a [[Júlia]], mais um
  documento semanal de demandas.
- **Construiu um "raio-X" completo do CRM** (com ajuda do Claude) que
  encontrou fluxo errado, falta de padronização, propriedade redundante e
  falta de preenchimento — e transformou isso num roadmap estrutural de
  demandas, mantido como arquivo no Claude e riscado conforme resolve
  ([[2026-08-25 1-1 Matheus - João Victor]]).
- **Gerencia o próprio backlog de CRM.** Era ClickUp — pipeline: Backlog →
  Qualificação de demanda → Em progresso → Atrasado → Concluído — ligado a
  uma automação N8N que posta num canal do Slack (ele, Gabrielle, Carol,
  "Red Comercial"): digest às segundas, ping em tempo real em card novo, e
  visão semanal na sexta. **Migrou para o Linear em 2026-09-08** — ver
  abaixo.
  > [!important] Correção, 2026-08-25 — é ClickUp, não planilha
  > Antes registrado (via Carol, segunda mão) como "planilha pessoal
  > dele". A versão dele é mais específica e mais rastreada que isso:
  > pipeline definido no ClickUp alimentado por um arquivo de roadmap no
  > Claude, com automação Slack já rodando. Ainda assim não era um sistema
  > compartilhado com o resto do time — que é a lacuna real para o A10
  > Portfolio de [[Agent Flow]]. Ver a migração abaixo: essa lacuna
  > específica fechou.
- **Triagem de demandas ad-hoc ao vivo** (alguém chega e pede direto):
  julga complexidade baixa/média/alta na hora — complexidade baixa é
  resolvida ou ensinada na hora (um card ainda é aberto depois e arrastado
  direto para concluído, para manter o rastro da automação intacto);
  complexidade maior vira card de backlog em vez de conserto imediato.
  Precedente vivo para a função de classificação do A2.
- **Repassa uma dor da Gabrielle, de segunda mão**: a automação de Slack
  dele dá boa visibilidade **micro** (status por card), mas ela disse, dias
  antes de sair de licença, que ainda falta uma visão **macro/agregada do
  todo** — ele não resolveu isso, e estava receoso de simplesmente dar
  acesso direto ao ClickUp ("mais uma ferramenta"). Uma reformulação
  concreta e independente da lacuna exata que o A10 Portfolio existe para
  fechar.
- Apareceu em 2026-08-24, ao escopar fontes de dados do A10 Portfolio
  ([[2026-08-24 Agent Flow discovery with Carol]]), como fonte de dados não
  rastreada; o 1:1 de 2026-08-25 acima refina em vez de fechar — ver a
  correção acima.

## Migração para o Linear — decidida e executada em 2026-09-08

A pergunta que ele deixou explicitamente em aberto em
[[2026-08-25 1-1 Matheus - João Victor]] ("aberto a migrar, ainda não
decidido") foi fechada. Ele pediu um overview do Linear a msilva
([[2026-09-08 Overview de Linear com João Victor]]), executou no mesmo dia,
e anunciou na [[2026-09-08 Weekly - Projetos e Tarefas]] que está
abandonando o ClickUp.

**Verificado no Linear em 2026-09-09**: a iniciativa **Monday - CRM**
existe, `Active`, ele como owner, `targetDate` 2026-09-30, com três
projetos:

- *Operação CRM*
- *Redefinição do fluxo comercial e processos do CRM*
- *Fragmentação dos fluxos de n8n*

O terceiro é a aplicação literal da regra que msilva passou no overview:
demanda de N8N que esbarra no Monday entra na iniciativa do Monday, não
numa iniciativa própria.

Método dele: pegar a **skill de Linear compartilhada pela Carol**, criar os
projetos com ela, depois conectar o MCP do Linear ao ClickUp para migrar o
que já existe. Motivação declarada de prazo: ter tudo pronto para
apresentar à Gabrielle quando ela voltar, em 10/09.

**Isso fecha, na prática, a lacuna de "sistema não compartilhado" registrada
acima** — o backlog dele agora vive onde o resto da área enxerga. Vale
notar para [[Agent Flow]]: uma fonte de dados que o A10 Portfolio teria que
integrar por fora agora entrou no Linear sozinha.

> [!note] Ele aprendeu a convenção de uma fonte não verificada
> A hierarquia que ele aplicou veio de msilva, que ensinou sem ter em mãos
> o documento formal de definições da Carol. Se a leitura de msilva
> divergir da oficial, a estrutura Monday - CRM foi construída em cima
> dela. Rastreado em
> [PRO-567](https://linear.app/projetos-livemode/issue/PRO-567); ver
> [[Linear Project Structure]].

## Frentes ativas (2026-09-08)

Do status dele na [[2026-09-08 Weekly - Projetos e Tarefas]]:

- **Automações de CRM**: `record ID`, data de criação e data de fechamento
  já são preenchidos automaticamente em casos novos, com monitoria semanal
  dele. O desafio aberto é o **preenchimento retroativo** dos registros
  antigos — e ele descobriu que dá para fazer nativamente dentro da Monday,
  sem N8N nem integração externa.
- **Funil de creators**: desenhou na Monday um pipeline de fechamento de
  projetos com creators, para substituir um processo 100% em planilha
  tocado pela agência ESIP. Pedido original da [[Júlia]] quando ele entrou.
  Próximo passo: apresentar à Débora, a responsável, e treiná-la.
- **Dashboard de receita**: criou um dashboard interativo na cloud
  conectando MCP do Airtable **e** da Monday, fragmentado por competição e
  período, a pedido da [[Júlia]] — que está validando os números.
- **View de planejamento na Monday**: investigando se o projeto foi
  entregue ou ficou à deriva. A [[Júlia]] passou a percepção de que tinha
  parado; a [[Yasmin Macedo]] diz que foi entregue. Carol deu o contexto
  que reconcilia: é o *workflow* da Monday, não o CRM, e nem ela tem
  visibilidade do uso diário. Ele vai falar com o Esbarai para achar o
  responsável real.
