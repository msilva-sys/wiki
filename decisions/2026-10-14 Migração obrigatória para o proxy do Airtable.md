---
type: decision
status: active
date: 2026-10-14
updated: 2026-10-16
aliases: [migração do proxy, desligar as chaves diretas]
tags: [airtable, proxy, governanca, seguranca]
---

# 2026-10-14 Migração obrigatória para o proxy do Airtable

Toda integração com o Airtable passa a ser obrigada a usar o
[[Airtable Proxy]]. Distribui-se uma skill de integração, e depois de um
prazo de transição de cerca de dois meses **todas as chaves diretas de
acesso ao Airtable são desabilitadas**. Nenhuma aplicação configura chave
própria no Airtable a partir daí.

Decidido em [[2026-10-14 Luís - Yasmin - Matheus (demo do proxy)]]
(`raw/2026-10-14 Luis - Yasmin - Matheus (demo do proxy).md`), na demo do
proxy e dos painéis do Grafana para o time que assume o sistema.

## Por quê

Hoje não se sabe quantas chaves existem, quais contas acessam o Airtable,
nem quem causou um estouro de limite. A criação descentralizada de chaves
foi tratada na reunião como risco crítico: enquanto qualquer pessoa puder
gerar a própria chave, não há como limitar consumo nem auditar acesso —
*"a gente nunca vai conseguir limitar a galera. Ah, usa o próx, vou criar
outra livre"*.

Com o proxy, as aplicações passam a usar uma chave emitida por ele; o PAT
do Airtable fica sob custódia do proxy, que observa cada chamada. Ver
[[Airtable Rate Limits]] para os tetos que motivam a governança.

## Questão em aberto: de qual conta saem os PATs por app

O proxy mantém **um PAT por app** (`internal/config/config.go`, campo
`Token` de cada entrada de `PROXY_APPS`) — a custódia sai do app e vai pro
proxy, mas a granularidade por aplicação permanece. Não há PAT único.

O que não está definido é **qual conta do Airtable emite esses PATs**. Os
dois tetos documentados são 5 req/s por base e 50 req/s para todo o tráfego
de PATs de um mesmo usuário ou service account; o teto maior é por conta
emissora, não por token. PATs separados por app só distribuem o consumo se
vierem de contas distintas — emitidos todos de uma conta de serviço do
proxy, somam no mesmo teto de 50 req/s.

msilva levantou exatamente isso na reunião, sem conclusão: *"se todas as
chaves forem do proxy, a gente pode pegar o rate limit da conta, que é 50
por segundo"*. Ficou como consenso que o teto por base era o alvo principal
e que o de conta precisava ser confirmado.

Hoje o sinal não existe: nenhum painel modela o teto por conta emissora, e
`airtable-proxy-design.md` só documenta os 5 req/s por base. Ver
[[Airtable Rate Limits]].

## O que não foi decidido

- **Como o prazo de dois meses é contado e comunicado.** Falou-se em dar um
  tempo de uns dois meses e então "começar a cair tudo" — sem data de
  início definida.
- **Quem desliga as chaves legadas**, e se existe inventário delas. A
  própria reunião mostrou que não existe: msilva tinha criado uma PAT
  pessoal com leitura e escrita que não aparecia na listagem consultada ao
  vivo. Validar a estrutura de permissões das contas do Airtable ficou como
  compromisso dele.
- **Enforcement no proxy.** O `v1` é observability-only por desenho — sem
  rate-limiting e sem cache (README do repo, e §10 do design doc, que defere
  o rate-limiter). A migração obrigatória não muda isso: ela centraliza
  custódia e visibilidade, não controle de vazão.

## Consequências

- A skill de integração passa a ser o caminho oficial de onboarding de um
  app — ver `skills/` no repo do proxy e [[Airtable Proxy]].
- O proxy entra no caminho crítico de toda aplicação que fala com o
  Airtable, o que torna a disponibilidade dele um problema de primeira
  ordem (já reconhecido como trade-off no §13 do design doc).
- O proxy **ainda não está em produção** na data da decisão, e precisa
  entrar antes da saída de [[Luís Fernandez]] — ver
  [[2026-10-07 Matheus - Luis (Transição)]].
