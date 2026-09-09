---
type: meeting
status: stable
updated: 2026-09-09
date: 2026-09-08
attendees: [Matheus Silva, João Victor Andrade]
aliases: [overview de linear]
tags: [linear, process, project-management, skills]
---

# 2026-09-08 Overview de Linear com João Victor

15:12, ~19 min. [[João Victor Andrade]] pediu um overview do Linear pra
migrar seu acompanhamento de demandas do ClickUp — fechando a decisão que
ele tinha deixado em aberto em [[2026-08-25 1-1 Matheus - João Victor]]
("não decidido ainda"). msilva conduz, ele executa no mesmo dia.
Transcrição do Gemini com atribuição correta dos dois falantes; garbling
recorrente de nomes de produto (`Linear` → "liner"/"linha", `issue` →
"iso"/"isso", `milestone` → "mais tom"/"myestone", `Monday` → "manda"/
"mandei").

## Decisions

- **Cada demanda ou entregável vira um projeto distinto**, sob o
  guarda-chuva de uma iniciativa — mesmo demandas simples e
  determinísticas, sem desmembramento. O exemplo trabalhado foi a limpeza
  dos dashboards do CRM, que viraria um projeto do tipo "Sanitizar o
  Dashboard Monday".
- **Demanda de N8N que esbarra no Monday entra na iniciativa do Monday
  CRM**, não numa iniciativa própria — mesmo quando o trabalho acontece
  fora do Monday. msilva deu como precedente a própria estrutura:
  mudanças no [[LiveScript]] ficam sob a iniciativa do proxy porque se
  relacionam indiretamente a ele. Atribuiu a regra ao Luís: *"pelo menos
  foi assim que o Luiz também me instruiu."*

## Commitments

- **msilva: localizar o documento com as definições formais de
  "iniciativa" e "projeto"** que a Carol apresentou na reunião dela —
  provavelmente anexado ao convite no Google Calendar. Rastreado em
  [PRO-567](https://linear.app/projetos-livemode/issue/PRO-567).
- João Victor: configurar os projetos no Linear usando a skill de Linear
  compartilhada pela Carol; conectar o MCP do Linear ao ClickUp pra dar
  contexto; migrar as demandas pendentes do ClickUp. Alvo dele: entre
  08/09 e 09/09, pra apresentar à Gabrielle quando ela voltar em 10/09.
  **Executado** — ver *Facts stated*.

## Open questions

- **A definição de "projeto" que msilva ensinou é mais frouxa que a
  registrada.** Aqui ele disse *"cada demanda / cada entregável é um
  projeto"*; a definição de Gabrielle em
  [[2026-08-18 1-1 Matheus - Gabrielle]], registrada em
  [[Linear Project Structure]], é *"projeto como um pedaço, uma parte
  daquela iniciativa"* — segmento de entrega de valor, não unidade de
  demanda. Ele mesmo reconheceu na call que não tem a fonte escrita:
  *"eu não tenho esse conceito escrito, não lembro como é que ela
  escreveu."* Se as duas leituras divergem de fato, a convenção já foi
  propagada pra outra pessoa antes de ser verificada. Resolve junto com o
  commitment acima.

## Facts stated

- **msilva**: existe uma **skill de Linear compartilhada pela Carol** com
  o time, que "já cria no formatinho direitinho". Ele já usava uma versão
  anterior dela, obtida com o Luís antes da Carol distribuir pro time —
  *"eu tinha pegado uma versão dessa skill com o Luiz."* Relevante pra
  [[Packaging as skills]]: é distribuição de skill acontecendo de fato no
  time, por dois caminhos independentes.
- **João Victor**: pegou a skill mas ainda não tinha aparecido no cloud
  dele.
- **msilva**, ensinando milestones com exemplo real do próprio trabalho: o
  projeto *Proxy em produção validado c/ LiveScript* tem uma milestone de
  "funcionalmente completo" (rodando em dev sem bug) e outra de "proxy em
  produção" (deploy no Cloud Run). Bate com o que
  [[Linear Project Structure]] registra da reestruturação de 2026-08-19.
- **msilva**: o Linear permite marcar qual issue bloqueia outra, e ele usa
  isso pra paralelizar — *"se tem, sei lá, três eixos que não são
  bloqueantes entre si, só spawna três subagentes e eles vão fazendo ao
  mesmo tempo."* Observa que a Carol não mostrou essa feature na reunião
  dela: *"não sei se ela não sabe ou esqueceu de mostrar."*
- **msilva**: recomendou o `status update` na aba de atividade do projeto
  como o lugar do resumo rotineiro sobre o projeto em si, não sobre issue
  individual — e citou que o agente que ele construiu comenta ali. É o
  A10 de [[Agent Flow]] visto de fora, sem nomeá-lo.
- **João Victor**: usa o campo de comentário pra registrar dependência de
  terceiros — *"tô no aguardo da resposta de fulano […] para caso
  extrapole algum prazo, algo do gênero, eu tenho esse respaldo."*
- **Gabrielle volta 10/09** (msilva corrige João Victor, que achava 13/09)
   — consistente com a licença registrada em [[index]].
- **Executado, verificado no Linear em 2026-09-09**: a iniciativa
  **Monday - CRM** existe, `Active`, com João Victor como owner,
  `targetDate` 2026-09-30, e três projetos — *Operação CRM*,
  *Redefinição do fluxo comercial e processos do CRM*, e ***Fragmentação
  dos fluxos de n8n***. O terceiro é a regra da segunda decisão acima
  aplicada literalmente: trabalho de N8N alocado sob a iniciativa do
  Monday.

## Notable quotes

> "O projeto é tipo como se fosse uma frente que você vai atacar dentro
> de uma iniciativa, né, do guarda-chuva de uma iniciativa." — msilva

> "Se tem uma ligação com a Monday, mesmo indiretamente, eu criaria
> dentro do Monday mesmo." — msilva

> "Eu não tenho esse conceito escrito, não lembro como é que ela
> escreveu." — msilva, sobre as definições de iniciativa e projeto da
> Carol

## Referências

- Doc do Drive: *Overview de linear - 2026/09/08 15:12 GMT-03:00 -
  Anotações do Gemini* (`1hxqgWF375FzEvpcIMCRaQb2Env0RY1V4KSbHCzMuUSg`),
  com resumo e transcrição completa. **Ainda não baixado pra `raw/`** —
  esta página foi escrita a partir do doc no Drive, não do arquivo local.
  Quando o arquivo chegar, trocar esta linha pelo caminho em `raw/`.
- [[Linear Project Structure]]
- [[João Victor Andrade]]
- [[Packaging as skills]]
