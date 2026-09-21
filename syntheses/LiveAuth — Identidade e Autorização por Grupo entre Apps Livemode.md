---
type: synthesis
status: active
updated: 2026-09-21
date: 2026-09-21
tags: [auth, security, proxy, agent-flow, liveauth]
aliases: [LiveAuth, autorização por grupo, grupo de acesso, liveauth-connect]
---

# LiveAuth — Identidade e Autorização por Grupo entre Apps Livemode

Design doc construído em chat, seção por seção, com a skill `design-doc`,
2026-09-21. Motivado pela necessidade concreta de restringir a rota
`/dashboard` (planejada, ainda não implementada) da [[Airtable Proxy]] por
grupo — mas desenhado deliberadamente em nível abstrato, multi-app, não
amarrado ao proxy. **Ainda não linkado das páginas de entidade (Airtable
Proxy, trava de domínio do Fluxo Agêntico) por pedido explícito de
msilva — o projeto ainda precisa amadurecer antes disso.**

Uma sessão paralela (link `claude.ai/share/803d8669-...`, resumo colado por
msilva) chegou a um desenho concorrente no mesmo problema — RBAC por grupo,
Firestore como fonte de verdade, Google IAP descartado por não cobrir
Vercel/Cloudflare. Vários pontos convergem (RBAC, grupo em vez de time,
Google IAP descartado pelo mesmo motivo); a diferença principal é onde
mora o registro de grupo — aqui decidido como dado do próprio LiveAuth
(ver Interfaces), não Firestore de terceiros.

## Objective

Dar aos apps internos da Livemode uma forma compartilhada de saber não só
**quem** é o usuário logado, mas **o que ele pode acessar** — pra um app
restringir um recurso a grupos específicos sem escrever lógica de
autorização própria.

## Background

Hoje existe autenticação (quem é o usuário), mas não autorização (o que ele
pode ver). O [[Agent Flow|Fluxo Agêntico]] restringe seu dashboard a e-mails
`@livemode.com` via Google OAuth (`auth_gate.py` + `middleware.js`, decisão
[[2026-08-28 Trava de domínio no Fluxo Agêntico via Google OAuth]]) — mas
essa trava é **binária**: passa quem tem cookie válido, o backend não sabe
*quem* passou nem de *que grupo*. O OAuth inteiro roda dentro do próprio
app (`auth_gate.py`), sem nenhuma camada compartilhada.

A [[Airtable Proxy]] não tem esse problema hoje porque não tem usuário
humano: `PROXY_APPS` autentica *apps* entre si (chave por app,
`internal/proxy/auth.go`), não pessoas — e o proxy é **público desde
2026-09-04**, não mais atrás de VPN (a chave por app substituiu a rede
privada como fronteira de confiança). Está prestes a ganhar uma rota
`/dashboard` voltada a humanos — primeiro caso concreto onde "autorizar por
grupo" vira necessário de verdade (ex.: só quem está no grupo `admin-proxy`
deveria acessar).

Sem um padrão compartilhado, cada app tende a reimplementar essa checagem
do zero — a própria trava de domínio já foi descrita como pensada pra ser
uma skill genérica e não saiu assim na prática (mesma decisão, seção "Por
que o fluxo OAuth é Python, não JS").

## Goals

- LiveAuth **decide** a autorização (login + checagem de grupo) e emite um
  token assinado de curta duração dizendo "autorizado pra este app" — o app
  não escreve regra de autorização nenhuma, só confia na assinatura.
- Grupo é um roster arbitrário de e-mails (pode misturar gente de times
  organizacionais diferentes — ex. alguém do jurídico + alguém específico
  de projetos no mesmo grupo), mantido inteiramente dentro do LiveAuth, com
  uma UI mínima de gestão (criar grupo, add/remove membro) e autocomplete
  de e-mail via Google Workspace Directory (evita erro de digitação e
  adicionar quem não é funcionário).
- Dono de um app só faz duas coisas: mantém a allow-list (o grupo) e roda a
  skill **`liveauth-connect`** pra integrar o app — sem escrever middleware
  à mão. Espelha o par já existente `airtable-proxy-connect`/
  `airtable-proxy-doctor`; um `liveauth-doctor` companheiro é provável.
- `/dashboard` do proxy é o primeiro consumidor concreto, validando que o
  padrão generaliza de verdade.
- Fluxo Agêntico consegue adotar o mesmo padrão depois, de forma aditiva —
  trocando o corpo do `middleware.js` atual (hoje roda o OAuth completo)
  pela verificação do token do LiveAuth.

## Non-goals

- Permissão granular por pessoa (ACL por usuário/recurso) fora do conceito
  de grupo — o nível de controle é **grupo**.
- Migrar a trava de domínio já implementada no Fluxo Agêntico agora — este
  doc é aditivo, não força migração.
- Sincronizar dado de perfil de funcionário (nome, cargo, foto) numa base
  própria — o autocomplete de e-mail consulta o Google Workspace Directory
  direto, sem cópia.
- Autorização app-to-app (`PROXY_APPS`/API key do proxy) — escopo
  diferente, já resolvido, não mexe aqui.
- **Isolamento de rede como mecanismo de enforcement** — considerado
  (LiveAuth como reverse proxy de tráfego completo, mesmo padrão que o
  próprio `livemode-airtable-proxy` já implementa com `httputil.ReverseProxy`/
  `Director`, `internal/proxy/proxy.go:147`) e descartado: Cloud Run
  restringe ingress por **serviço inteiro**, não por rota — `/dashboard`
  vive no mesmo binário que precisa ficar público pra servir a API do
  proxy, então isolar só essa rota exigiria um deployable novo. A
  verificação de token dentro de um middleware mínimo resolve sem esse
  custo — ver Interfaces.

## Interfaces

LiveAuth é uma abstração fina em cima do **Firebase Authentication** (já em
uso na Livemode — LiveScript usa o projeto `livemode-roteiros-dev`), não
uma implementação OAuth própria, mais um **registro de app** e um
**roster de grupo**.

- **Login**: LiveAuth hospeda o fluxo (Google Sign-In do Firebase Auth por
  baixo); restrição a `@livemode.com` aplicada via *Blocking Function*
  (`beforeSignIn`) — `hd=` no client é só hint pro seletor de conta, não a
  checagem real.
- **Registro de app**: cada app registra no LiveAuth `app_id → grupo
  exigido → URL de destino/redirect` (ex.: `proxy-dashboard → admin-proxy
  → https://.../dashboard`).
- **Roster de grupo**: lista de e-mails, mantida no LiveAuth, sem
  restrição a fronteira organizacional. UI mínima autenticada com o
  próprio LiveAuth pra criar grupo e add/remover membro, com autocomplete
  via Google Workspace Directory.
- **Fluxo de autorização**: usuário sem sessão local bate numa rota
  protegida → app redireciona pro LiveAuth → LiveAuth faz login + decide
  se o e-mail está no grupo exigido por aquele `app_id` → emite um token
  assinado de curta duração, escopado ao app (`aud`), carregando identidade
  + a decisão de autorização → app verifica só a assinatura e o `aud`,
  nunca recebe nome de grupo nem decide nada → seta sessão local própria
  (cookie assinado, mesmo padrão do `lm_session` de 12h que o Fluxo
  Agêntico já usa) — sem chamada ao LiveAuth nas requests seguintes até a
  sessão expirar.
- **`liveauth-connect`** (skill, desvinculada do repo do LiveAuth, roda a
  partir do projeto cliente — mesmo modelo de `airtable-proxy-connect`):
  adiciona o middleware certo pro stack do app (Go, Python, JS, ...) e
  registra o app no LiveAuth. Ninguém escreve essa integração à mão.

## Security

- **Token de handoff, não de sessão** — vida curta, consumido uma vez no
  redirect de volta, escopado por `aud` (impede reusar um token emitido
  pra outro app). Cada app monta sua própria sessão a partir daí.
- **Nunca logar o token** — mesma regra já aplicada a PAT/Authorization no
  proxy hoje (`internal/proxy/auth.go`, "Security invariants: PAT/
  Authorization is never logged").
- **Domínio validado de fato via Blocking Function** no Firebase Auth, não
  só pelo hint `hd=` do client.
- **Redirect URI allowlist por app** — LiveAuth só devolve o token pra uma
  URL de callback previamente registrada; sem isso, um app malicioso
  poderia roubar o handoff (open redirect).
- **Chave de assinatura e segredo do client OAuth do Google geridos pelo
  Firebase** — sem rotação nem custódia própria do LiveAuth.
- **LiveAuth fora do ar afeta só login novo.** A decisão de autorização é
  tomada uma vez, no login, e cacheada na sessão local do app — não é
  dependência de runtime por request.

## Open Issues

- **Qual projeto Firebase o LiveAuth usa** — o mesmo `livemode-roteiros-dev`
  do LiveScript, ou um novo dedicado? Próximo passo: decidir no desenho
  técnico do LiveAuth.
- **Quem pode criar/editar um grupo no LiveAuth** — default proposto em
  chat, não confirmado: qualquer `@livemode.com` logado pode criar um
  grupo novo e vira automaticamente editor dele. Próximo passo: validar
  esse default, ou definir algo mais restrito, com Luís.
- **Quem é dono/mantém a infraestrutura do LiveAuth** — vira dependência
  compartilhada por múltiplos apps; sem dono claro é bus factor. Próximo
  passo: alinhar com Luís antes de começar a construir.
- **Quando (ou se) o Fluxo Agêntico migra pro LiveAuth** — hoje tem sua
  própria trava (`auth_gate.py`); este doc é aditivo, não força migração.
  Próximo passo: decisão separada, não bloqueia o proxy como primeiro
  consumidor.
