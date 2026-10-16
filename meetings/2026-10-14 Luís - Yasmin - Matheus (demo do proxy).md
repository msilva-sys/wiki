---
type: meeting
status: stable
date: 2026-10-14
updated: 2026-10-16
attendees: [Luís Fernandez, Yasmin Macedo, Matheus Oliveira da Silva]
tags: [airtable, proxy, grafana, observabilidade, transicao]
---

# 2026-10-14 Luís - Yasmin - Matheus (demo do proxy)

Demo do [[Airtable Proxy]] e dos painéis do Grafana para quem assume o
sistema. Fonte: `raw/2026-10-14 Luis - Yasmin - Matheus (demo do proxy).md`
(ata e transcrição do Gemini, 35 min).

## Decisões

- **Migração obrigatória para o proxy** — distribuir uma skill de
  integração e desabilitar todas as chaves diretas do Airtable após um
  prazo de transição de cerca de dois meses. Registrada em
  [[2026-10-14 Migração obrigatória para o proxy do Airtable]].
- **Os painéis do Grafana são a ferramenta de auditoria do tráfego** e de
  diagnóstico de falhas nas chamadas das aplicações.
- **A evolução contínua do dashboard passa a ser do time** — Luís: *"o a
  evolução disso no final das contas vai ficar com vocês"*.

## Commitments

- **msilva** vai consultar [[Gabrielle Ferreira]] para esclarecer a
  estrutura e os limites de acesso nas contas do Airtable — quem consegue
  criar PAT, com que permissões, e em qual conta. Levantado porque a
  listagem consultada ao vivo não mostrou uma PAT pessoal que ele mesmo
  tinha criado com leitura e escrita.
- **O time** vai implementar o mecanismo de bucket para detectar estouro de
  requisições por segundo em janelas temporais. *(Parcialmente já existente
  — ver "Correções pelo código" abaixo.)*
- **O time** vai identificar informações ausentes nos painéis e organizar o
  layout.
- **O time** vai exportar as métricas para investigar gargalos e corrigir o
  código das aplicações.

## Perguntas em aberto

- **De qual conta do Airtable saem os PATs por app?** Levantada por msilva
  sem conclusão — ver
  [[2026-10-14 Migração obrigatória para o proxy do Airtable]].
- **Como os dados do Grafana viram mudança de código no [[LiveScript]]?**
  msilva perguntou como entrar no detalhe das chamadas; a resposta foi
  exportar os dados e trabalhar a partir deles. O painel mostra rota e
  método, **não** mostra a tela ou o handler que disparou — foi decisão
  deliberada de não instrumentar isso, por custo.
- **Não há análise qualitativa dos full scans.** Um full scan pode ser
  legítimo; o painel sinaliza, não julga.
- **Ambiente para corrigir e revalidar** — Luís levantou que vai ser
  preciso homologação ou execução local para iterar nas correções, sem
  definir qual.

## Fatos declarados

- **Limites do Airtable** (msilva e Luís): 5 req/s por base; somatório por
  conta de 50 req/s; estouro derruba tudo com 429 por cerca de 30 segundos.
  *O escopo do segundo teto foi corrigido depois contra a documentação
  oficial — ver [[Airtable Rate Limits]].*
- **Antipadrões detectados** (msilva, demonstrando): full scan (select sem
  `filterByFormula`), over-fetch (sem projeção de campos), candidatos a
  cache, paginação profunda (offset a partir da página 2), e referência a
  tabela/view/field por nome em vez de ID.
- **Primeira medição real**: 157 chamadas de teste do LiveScript nas horas
  anteriores produziram **28 possíveis full scans**.
- **Exemplo do antipadrão** (Luís, no quadro): trazer 200 mil registros da
  tabela de marcas para filtrar a Samsung em memória.
- **O Grafana local em Docker perde histórico** — recriar os containers
  redefine os dados. Histórico persistente exige Grafana Cloud. Explica a
  ausência de dados ao tentar visualizar três ou seis meses durante a demo.
- **O proxy está estável e pronto para produção, mas ainda não está em
  produção** (msilva), e precisa entrar antes da saída de
  [[Luís Fernandez]] — ver [[2026-10-07 Matheus - Luis (Transição)]].
- **Origem histórica do problema** (Luís): grande parte das falhas do
  projeto de front desenvolvido por terceiros vinha exatamente desses
  antipadrões — *"no nível bizarro de fazer cinco chamadas desse tipo na
  mesma tela para fazer uma parada que era relativamente simples"*.

## Correções pelo código

Conferido no repo em 2026-10-16; o código discorda da ata em três pontos.

**1. Os antipadrões não são detectados no proxy — são classificados no
Grafana.** O Go emite sinais crus em `internal/proxy/telemetry.go`
(`HasFilter`, `HasFieldProjection`, `FieldsByName`, `FilterHash`,
`PageSize`, `Offset`, `TableByName`, `ViewByName`, `IsList`). Quem rotula
"full scan", "over-fetch", "cache candidate" e "paginação profunda" são as
queries dos painéis em `grafana/dashboards/airtable-usage.json`. A
classificação é, portanto, barata de mudar — é query, não deploy.

**2. Paginação profunda não é duplicata de full scan.** Dúvida levantada ao
vivo por Luís (*"qual a diferença desse pro full scan que traz tudo? tô
achando que tá duplicado"*), sem resposta na reunião. São sinais
independentes: `Offset` versus ausência de `filterByFormula`, em painéis
distintos.

**3. O "algoritmo de bucket" já existe em parte.** O dashboard
`Airtable Proxy — Rate-limit & Fairness` já traz consumo por base contra o
teto de 5 req/s por app, utilização percentual do teto, e minutos acima de
80%. O que não existe é qualquer modelagem do teto por conta emissora. O
próximo passo real é mais estreito do que "fazer bucket".

Vale notar o enquadramento do repo: o README diz que o
**`v1` é observability-only — sem rate-limiting e sem cache**, e o §10 do
design doc defere o rate-limiter de propósito.

## Citações

> "O proxy é uma iniciativa pra gente tentar ter algum tipo de governança
> sobre isso."
> — msilva, abrindo a demo

> "O que a gente tá fazendo é tirar esse poder deles. [...] ninguém tem a
> chave real de comunicação; eles passam a ter uma chave de comunicação com
> a gente, e nós vemos tudo que eles estão fazendo."
> — Luís Fernandez, explicando o modelo de custódia

> "O mais importante é a gente entender se tá todo mundo criando as suas
> próprias chaves, porque aí temos um problema."
> — Luís Fernandez, sobre o risco que motiva a migração

> "O ideal é que o próprio Claude, enquanto a gente tá desenvolvendo, veja
> isso, saiba e já corrija. Não sei como é que a gente vai fazer. Dá pra
> fazer. Só não sei como que a gente automatiza esse processo inteiro
> ainda."
> — Luís Fernandez, sobre levar os antipadrões para o momento de escrever o
> código
