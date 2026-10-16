---
type: concept
status: active
updated: 2026-10-16
aliases: [429, rate limit, rate limiting, limite de uso]
tags: [airtable, rate-limits, http]
---

# Airtable Rate Limits

A restrição que o programa do [[Airtable Proxy]] inteiro existe para
administrar.

## Os limites

São **dois tetos distintos**, e confundi-los leva a conclusões opostas sobre
o desenho do proxy:

| Teto | Escopo | Valor |
|---|---|---|
| Por base | uma base do Airtable, independente de quem chama | **5 req/s** |
| Por emissor do token | **todo o tráfego de PATs de um mesmo usuário ou service account**, somado entre bases | **50 req/s** |

Estourar qualquer um devolve **HTTP 429** e exige esperar cerca de **30
segundos** antes que as requisições voltem a passar. A resposta 429 carrega
o header **`Retry-After`**, então o backoff pode ser guiado pela API em vez
de chutado.

Fonte: [Rate limits — Airtable Web API](https://airtable.com/developers/web/api/rate-limits),
conferido em 2026-10-16. A documentação também reserva o direito de mudar os
tetos ou criar novos, possivelmente escalonados por plano.

> **Correção de escopo, 2026-10-16.** Em
> [[2026-10-14 Luís - Yasmin - Matheus (demo do proxy)]] o segundo teto foi
> descrito como "somatório por conta". O texto oficial é mais estreito:
> vale para o tráfego de PATs de **um dado usuário ou service account**. A
> diferença importa — vários PATs emitidos pela mesma conta continuam
> somando no mesmo teto de 50 req/s, então separar PATs por aplicação só
> distribui o consumo se as contas emissoras também forem distintas. Ver a
> questão em aberto em
> [[2026-10-14 Migração obrigatória para o proxy do Airtable]].

**Nenhum dos dois tetos está completamente instrumentado.** O painel
`Airtable Proxy — Rate-limit & Fairness` cobre bem o de 5 req/s por base
(consumo por app contra o teto, utilização percentual, minutos acima de
80%), mas **não há painel algum para o teto por conta emissora**, e
`airtable-proxy-design.md` só documenta o limite por base. É a lacuna que
mantém `PRO-996` de pé.

## Limites vizinhos que não são este

- **Cap mensal de chamadas de API do plano** — painel
  `Budget mensal de API calls` no mesmo dashboard, default de 100 mil no
  plano Team; o alerta de 429 cita `PUBLIC_API_BILLING_LIMIT_EXCEEDED` como
  causa possível. É cota mensal, não vazão por segundo.
- **Cap mensal de automações por workspace** — também na casa de 100 mil,
  mencionado em [[2026-10-15 Recap da Semana]] no contexto do inventário de
  automações do time. Coincidência de ordem de grandeza; não tem relação com
  os tetos de API acima.

## Por que o limite existe pra nós

O Airtable é uma API com limites de uso, **não um banco de dados** — não dá
pra escrever direto nele, e ele restringe quem chama de um jeito que um
banco não restringiria. Essa distinção é a raiz do problema: o
[[LiveScript]] precisa de comportamento colaborativo em tempo real sobre um
backend que não sustenta isso.

## Como o limite é sentido na prática

A carga não é uniforme — ela dispara com eventos do mundo real. A **Copa do
Mundo** produziu um dia de uso concorrente que furou o limite e disparou a
iniciativa inteira ([[2026-08-10 Onboarding Técnico - Matheus]]).

Duas causas diferentes produzem o mesmo 429, e pedem correções opostas:

| Causa | Correção |
|---|---|
| Leituras duplicadas ou desperdiçadas — rebuscar o que já se sabe | Cache, ou corrigir quem chama |
| Volume legítimo sustentado | Rate limiting, enfileiramento, ou reduzir demanda |

**Qual das duas está realmente acontecendo era desconhecido**, e descobrir é
o ponto inteiro da abordagem observability-first do [[Airtable Proxy]]. O
dashboard `Airtable Proxy — Usage & Anti-patterns` existe para separar as
duas.

Desde 2026-10-14 há o primeiro dado concreto: numa sessão de testes de 157
chamadas do [[LiveScript]], o painel acusou **28 possíveis full scans**
([[2026-10-14 Luís - Yasmin - Matheus (demo do proxy)]]). Pende o
julgamento qualitativo — nem todo full scan é erro.

## Tratamento atual

- **As aplicações contornam o limite restringindo pessoas usuárias** — as
  *travas* do [[LiveScript]]: lock de linha inteira, restrição de criador
  único.
- **O proxy pretende absorver os 429** com retry/backoff para que as
  aplicações não implementem cada uma o seu. Declarado como intenção, ainda
  não construído — ver a tensão em aberto em [[Airtable Proxy]].
- **A folga contra os 5 req/s por base já está instrumentada** em painel, e
  `airtable_429_alert` dispara em qualquer 429 numa janela de 5 minutos.
- **A folga contra os 50 req/s por conta emissora não está instrumentada**
  em lugar nenhum — ver a correção de escopo acima.

## Relacionado

- URLs de anexo em payloads de registro expiram em ~2 horas, que é por que
  dados de registro não podem simplesmente ser cacheados para reduzir volume
  de requisições — [[Airtable Proxy]].
