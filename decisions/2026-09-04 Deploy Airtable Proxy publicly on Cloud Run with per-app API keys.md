---
type: decision
status: stable
updated: 2026-09-04
date: 2026-09-04
decided_by: Luís Fernandez
source: "sources/Direcionamento PRO-84 PRO-87.md (raw/2026-09-04 Direcionamento PRO-84 PRO-87.html)"
tags: [proxy, gcp, cloud-run, networking, security, api-key]
aliases: [Cloud Run público, deploy de produção do proxy, PRO-84]
---

# Deploy Airtable Proxy publicly on Cloud Run with per-app API keys

> [!warning] Supersedes [[2026-08-21 Deploy Airtable Proxy privately behind VPN]]
> Aquela página tratava rede privada (VPN/LB interno) como decidido. Está
> superada por esta: o [[Airtable Proxy]] vai para produção público, sem LB,
> sem VPN, sem VM.

**Decisão.** O [[Airtable Proxy]] é implantado no Cloud Run
(`min=1 max=3`), público (`ingress=all`), em `us-east4`, em uma URL nativa
`*.run.app` — sem Load Balancer, sem NEG, sem VPN, sem VM. A fronteira de
segurança passa a ser uma **API key por app** (`Authorization: Bearer
<key>`), pré-requisito de go-live, e não a rede.

Escopo explícito: **só os 3 apps internos do time**, na fase atual. A
aceitação do proxy público é condicionada a isso — revisitar quando ele
passar a atender apps de fora do time.

## Por que isso muda em relação à decisão de rede privada

O que encarecia e complicava o desenho de 2026-08-21 não era alta
disponibilidade (o Cloud Run já dá isso de graça com `min=1 max=3`) — era a
exigência de rede privada, que só existia para resolver DNS/IP dentro da
VPN. Essa exigência custava sozinha US$ 55,84 dos US$ 70,84/mês do desenho
anterior (o Load Balancer interno) e trazia peças de infra (NEG, subnet
proxy-only, DNS privado, certificado) sem contrapartida em disponibilidade.

Sem VPN, a VM perde sua única vantagem (IP interno de graça) e vira ponto
único de falha na frente de todos os apps — descartada também.

> [!important] Rede privada nunca teria funcionado para o LiveScript de qualquer forma — Luís, [[2026-09-04 1-1 Matheus - Luís]]
> Confirmado na call que discutiu este memo: ninguém usa VPN para acessar
> as aplicações internas da Livemode — nem o próprio LiveScript, cujos
> usuários trabalham de casa sem VPN. *"O que eu tenho certeza é as
> pessoas não usam VPN para acessar as nossas aplicações."* Isso reforça
> a decisão além do argumento de custo do memo: o desenho de rede privada
> de [[2026-08-21 Deploy Airtable Proxy privately behind VPN]] não era só
> mais caro, seria **inviável na prática** para o consumidor real do
> proxy. Pode existir VPN pra alguma outra finalidade na empresa — Luís
> não tem certeza — mas nunca foi usada para estas aplicações, e vale
> confirmar com o time de infra só se isso um dia importar.

## Por que abrir para a internet é aceitável agora

Hoje, sem proxy, as PATs do Airtable já estão espalhadas nos 3 apps: uma
PAT vazada dá acesso direto e invisível ao Airtable, e revogar significa
rotacionar em N lugares. Com o proxy público e uma API key por app, o raio
de estrago de um vazamento é o mesmo de hoje (a key é tão sensível quanto a
PAT que substitui) — mas com visibilidade (IP, app, base, tabela por
request), revogação cirúrgica por app, e a PAT real centralizada.

O que se perde: a rede como segundo fator, e exposição a scanners/flood —
mitigado por 401 antes de tocar o Airtable e pelo teto `max=3`. Se virar
problema real, Cloud Armor entra na frente com um LB externo (~US$
18/mês) sem mudar o desenho.

## Mecanismo da API key

O `Director` do reverse proxy já apaga o `Authorization` recebido do
cliente e injeta a PAT real antes de chamar o Airtable — funciona hoje
descartando o valor recebido sem validar. A única mudança necessária é
**validar** esse valor antes de descartá-lo. Funciona com qualquer cliente
porque o próprio Airtable exige `Authorization: Bearer <token>` em toda
chamada — não há SDK ou chamada REST que não mande esse header.

Mantém o `/{appId}/` no path ([[2026-08-19 Identify proxy apps by URL path,
not header]]) — a key precisa bater com o app do path. Duas keys válidas
por app, para rotacionar sem downtime. Isso ativa o "future app-key phase"
que [[2026-08-21 Deploy Airtable Proxy privately behind VPN]] e
[[2026-08-19 Identify proxy apps by URL path, not header]] deixaram como
camada futura — deixa de ser futura.

## Região e URL

`us-east4` (Virgínia), ao lado do Airtable (AWS us-east-1, não GCP — "mesma
rede do Airtable" não existe de fato). Nunca pior que hoje para qualquer
app, em qualquer lugar; São Paulo seria +115–230ms para apps nos EUA. Custo
não é o motivo (~US$ 4–5/mês de diferença Tier 1 vs Tier 2).

URL nativa `*.run.app`, sem domínio custom — `proxy.livemode.space` exigiria
domain mapping (preview, sem SLA) ou LB externo (~US$ 18/mês, GA). Com 3
apps, a URL é uma env var em 3 lugares; trocar depois é trivial.

## `min=1 max=3`

Não depende do número de apps — Cloud Run escala por requests simultâneas
(80/instância). `min=1` evita cold start (custo fixo ~US$ 10/mês). `max=3`
é trava de segurança, não dimensionamento: ~2.000 req/s de teto, muito
acima do limite do Airtable (5 req/s por base) e do que uma única instância
Go (I/O-bound) já atenderia sozinha.

## Custo

~US$ 10/mês (compute Cloud Run, Tier 1), contra US$ 20,91–42/mês da VM (sem
HA, com operação recorrente própria) e US$ 92–142/mês do Cloud Run + LB
interno (HA, mas ainda com Grafana em compute próprio). Estimativas do GCP
Pricing Calculator em 2026-09-04, a validar contra billing real.

## Ainda em aberto

- Revisitar a aceitação de proxy público quando ele passar a atender apps
  de fora do time.
- Revisar depois de semanas de uso real: volume, custo, cardinalidade de
  séries — só então decidir domínio próprio, LB, Cloud Armor ou `min=2`.
- Ajuste dos tickets `PRO-90`/`PRO-93` e criação do ticket de API key no
  Linear — ver a ressalva de mapeamento em [[Direcionamento PRO-84
  PRO-87]], ainda não executado.
