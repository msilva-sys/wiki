---
type: project
status: active
updated: 2026-10-16
aliases: [opta]
tags: [opta, transição]
---

# Opta

Integração interna com a **Opta**, fornecedor externo de dados estatísticos
de futebol (muito usado pelo time de conteúdo, e conhecido fora da empresa —
é o "supercomputador" citado por comentaristas esportivos). Compartilhado
por Matheus e [[Luís Fernandez]] enquanto ele estiver presente na empresa
([[2026-10-07 Divisão de projetos na transição de Luís]]) — Matheus como
guardião técnico e revisor de PR. Detalhado em
[[2026-10-09 Opta - O que é isso]].

## Histórico de ownership

Nenhum dono formal/único até hoje. [[Pessanha]] começou a assumir a
responsabilidade antes de Luís entrar na empresa (já tinha saído quando
Luís chegou). [[Pedro Alves]] (também ligado ao [[Orca (CDE)]]) virou o
dono informal — conhecia bem o sistema, era procurado por quem tinha
dúvida.

> [!warning] Correção 2026-10-16 — quem construiu foi o Peçanha
> Luís atribuiu a construção da Opta API e do Opta MCP a [[Pedro Alves]].
> Os repos dizem outra coisa: todo commit inicial de `opta-api`, `opta-mcp`
> e `goal-webhook` é de `ppecanha`/`pedrompecanha`, e Pedro Peçanha
> anunciou a API no `#general` em 2026-05-11 e o [[Golzinho]] em
> 2025-10-03. O papel de Pedro Alves como **dono informal** (quem era
> procurado, quem conhecia o sistema) não é contraditado — só a autoria.
> Consequência prática: o autor das quatro peças saiu e não está acessível
> (msilva, 2026-10-16), e Luís, o único que ainda carregava contexto, está
> de saída.

[[Vini]] aprendeu bastante durante a Copa, perto do Pedro. Quem hoje é o
responsável interno pelo **contrato comercial** com a Opta não é conhecido —
pergunta em aberto, Luís ia levar a [[Carolina Bezerra]] e possivelmente
[[Gabrielle Ferreira]].

## Arquitetura — três repos, quatro peças

> [!note] Verificado no código em 2026-10-16
> O relato de Luís em [[2026-10-09 Opta - O que é isso]] descreve três
> projetos. A leitura dos repos mostra uma quarta peça que ninguém citou: o
> banco de gols e partidas do [[Golzinho]], que nasceu sete meses antes da
> API e que ela apenas expõe.

- **[[Opta API]]** — proxy dos feeds da Opta **e** leitura do banco do
  [[Golzinho]] (`/ourGoals`, `/ourMatches`, WebSocket). Duas naturezas no
  mesmo serviço, unidas só pela `X-API-Key`. Não confirmado se o proxy
  cobre todos os endpoints licenciados.
- **[[Opta MCP]]** — seis ferramentas pro Claude, edição corrente de 18
  competições, chave única compartilhada.
- **Opta Docs** — repo `livemode-opta-docs`, Astro Starlight na Vercel com
  login Google. Documentação gerada por Luís via Claude, autenticada (não
  exposta fora da empresa). Mistura orientação de uso (como integrar, lista
  de competições disponíveis) com uma seção de operação interna (como
  adicionar competição, gestão de chave) que não deveria ser pública —
  ainda sem solução de separação.
- **[[Golzinho]]** — o produtor dos dados próprios, e a peça sem dono
  mapeado. Ver [[Quem escreve nas tabelas goals e matches]].

Luís já cogitou unificar os três repos num monorepo; não chegou a fazer —
hoje é incômodo trabalhar com Claude Code cruzando os três.

## Contrato e chave de API

Contrato não é barato; mudar o escopo (ex: licenciar uma nova competição,
tipo La Liga) é renegociação de contrato, não um simples gerar-de-chave —
valores citados perto de R$150.000, contratos de longo prazo. A empresa usa
**uma única chave de API compartilhada por todo mundo** (ao contrário das
chaves de agente, individuais e revogáveis) — comparada por Luís a "escova
de dente compartilhada." Plano verbal de rotação (não implementado): avisar
a empresa toda, ~30 dias pra migrar pra chave nova, ~60 dias depois desligar
a antiga.

**Credencial hoje é buscada direto do BigQuery, não é secret do GCP** —
mesma classe de dívida que o [[Airtable Proxy]] já resolveu, apontada por
Luís como "um problema" real.

## Uso em produção

No ar desde **30/03/2026**. Relatório de uso feito por Luís a pedido de
[[Maria Fernanda Lemos]] (~2026-08-25): até 28/08/2026, 1556 requests em 31
dias; principal consumidor é tráfego via agente (Claude/Cloud), +25% Node,
7% Python; chamadas de ferramenta estáveis em ~230/mês.

Taxas de sucesso por ferramenta (mesmo relatório):
- `get_schedule`: **51%** de 246 chamadas — muito baixo, bug que Luís acha
  que corrigiu parcialmente, sem certeza; sem detalhamento do erro.
- `get_standings`: 100%.
- `squads`: 97%.

Outros achados: lista de competições disponíveis é um JSON fixo atualizado
manualmente em produção (deveria vir dinamicamente da própria API); log de
uso só começou perto do 5º dia da Copa (não cobre o histórico real); alguns
erros 401 por chave mal configurada do lado de quem consome, sem
identificação de quem é.

Cobrança da Opta: fixa até um limite de chamadas, por faixa adicional acima
disso. Pedro Alves teria feito algum cache seletivo de dado pra reduzir
custo (nível de detalhe não levantado). **Cuidado**: cachear resposta pode
esbarrar em cláusula contratual que proíbe — não ficou claro qual, ver
[[2026-10-09 Opta - O que é isso]].

## Visão de produto

Luís quer que o time trate a Opta como produto próprio (mesmo discurso do
[[LiveScript]]): dar visibilidade real (dashboard no telão da sala de
reunião, como o dashboard "Insights" do [[Airtable Proxy]]), tratar quem usa
como cliente, citar a Opta como case de sucesso do time (como já fizeram com
LiveScript e [[Orca (CDE)]]). Pediu a Matheus e [[Yasmin Macedo]] que
considerassem apresentar a Opta pro time como dupla.

## Riscos levantados (2026-10-16)

Consolidado da leitura dos repos, da doc interna e do relato de 09/10.
Ordenado por gravidade.

| # | Risco | Origem | No Linear? |
| --- | --- | --- | --- |
| 1 | [[Opta MCP]] sobe **sem autenticação** se `MCP_AUTH_KEY` sumir | código | não |
| 2 | Produtor do [[Golzinho]] invisível, falha em silêncio, autor fora da empresa | código | não |
| 3 | Chave única do MCP: sem revogação individual nem atribuição | doc + 09/10 | não |
| 4 | 28 chaves de API sem registro de dono | doc | `PRO-694` |
| 5 | Chaves em claro no log e no BigQuery | doc | não |
| 6 | Credencial do contrato no BigQuery, não no Secret Manager | 09/10 | não |
| 7 | Três clientes no Supabase direto, fora da auditoria | código | não |
| 8 | Duas regiões da API em revisões divergentes | doc | não |
| 9 | `service-account.json` versionado no `goal-webhook` (conteúdo não lido) | repo | não |
| 10 | Rotação de chave é plano verbal, nunca executada | 09/10 | não |
| 11 | `get_schedule` com 51% de sucesso em 246 chamadas | 09/10 + doc | `PRO-680`, `PRO-682` |
| 12 | `competitions.json` fixo, editado à mão em produção, duplicado no docs | 09/10 + código | `PRO-679`, `PRO-683` |
| 13 | Credencial lida do BigQuery a cada chamada do proxy | código | `PRO-693` |
| 14 | README do `opta-mcp` anuncia ferramenta removida (`opta_raw`) | código | não |
| 15 | `401`/`403` não entram no log de auditoria | doc | `PRO-699` |

Os itens 1, 2, 7, 8 e 9 **não têm issue** e não aparecem no backlog da
transição. 1 e 2 são os mais graves: um é exposição de credencial, o outro é
falha silenciosa num caminho que alimenta transmissão ao vivo — agravado por
2 e 3 juntos, já que o produtor não tem dono, não tem repo e seu autor saiu.

## Backlog de melhorias (não aprovado/priorizado ainda)

- Achar quem é o responsável interno pelo contrato comercial.
- Implementar a rotação de chave planejada acima.
- Investigar e corrigir de vez os 401s e o bug do `get_schedule`.
- Migrar a credencial pra GCP Secret Manager.
- Página de competições disponíveis dinâmica, com fluxo de pedido de acesso
  a competição nova (abre canal no Slack / dispara e-mail pro responsável
  pelo contrato).
- Separar documentação pública de documentação de operação no Opta Docs.
- Considerar reaproveitar um bot/agente de chat interno antigo (parado
  desde fevereiro) dentro do repo do Opta MCP.
- Considerar monorepo pros três projetos.
- Dashboard de uso no telão.

Nada disso está decidido — Luís foi claro que não é pra agir sozinho sem
aprovar com Gabi/Carol primeiro, embora seja a favor e vá levar a elas.
