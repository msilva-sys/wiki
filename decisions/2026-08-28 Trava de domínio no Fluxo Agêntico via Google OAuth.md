---
type: decision
status: active
updated: 2026-08-28
date: 2026-08-28
decided_by: Matheus Silva
source: "chat with Claude, 2026-08-28"
tags: [agents, agent-flow, a10, a14, vercel, auth, security, langgraph]
aliases: [trava de domínio, domain lock, Google OAuth Fluxo Agêntico]
---

# Trava de domínio no Fluxo Agêntico via Google OAuth

**Decisão.** O dashboard do [[Agent Flow|Fluxo Agêntico]] (frontend + gateway,
repo `livemode-fluxo-agentico`) fica restrito a e-mails `@livemode.com` via
login Google, implementado direto no repo — sem depender de nenhuma
plataforma de auth externa.

## Duas metades

1. **FastAPI só aceita a origin do frontend** — `CORSMiddleware` em
   `gateway.py`, restrito à env var `ALLOWED_ORIGINS`.
2. **Login restrito por domínio de e-mail** — `middleware.js` (Vercel Routing
   Middleware, via `proxy.entrypoint` em `vercel.json` — roda antes de
   qualquer rewrite decidir o service, protegendo frontend e `/api/*` de
   uma vez) verifica um cookie de sessão assinado (HMAC) e redireciona pro
   login do Google quando ausente/inválido.

## Por que o fluxo OAuth é Python, não JS

O desenho original (de uma skill genérica de "trava de domínio") esperava um
projeto Vercel simples, com os handlers de login/callback/logout como
Vercel Functions em JS dentro do próprio service do frontend. Não funcionou
nesse repo: o preset do Vite não deixava esses arquivos virarem functions de
verdade dentro do service (`/api/auth/login` voltava 404 mesmo com a rota
certa) — várias tentativas, sem causa raiz 100% confirmada. O service
`backend` (Python/FastAPI) já era comprovadamente roteável via `/api/*`, então
o fluxo OAuth (troca de código com o Google, checagem de `email_verified` +
domínio, assinatura do cookie) foi reimplementado em Python
(`auth_gate.py`), reaproveitando a mesma infra que já funcionava — sem
precisar entender por que o caminho JS falhava.

## Estado atual

- `middleware.js` (raiz do repo) — checa cookie `lm_session`, redireciona
  pra `/api/auth/login` se ausente/inválido/expirado (sessão de 12h).
- `auth_gate.py` — rotas `/api/auth/{login,callback,logout}` no `gateway.py`,
  fluxo OAuth completo (troca de código, `hd=` hint pro seletor de conta do
  Google, `ALLOWED_DOMAIN` checado no callback).
- Variáveis de ambiente na Vercel (production/preview/development):
  `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `SESSION_SECRET`,
  `ALLOWED_DOMAIN=livemode.com`.
- Alias estável de preview (`livemode-fluxo-agentico-preview.vercel.app`)
  criado só pra ter uma URI de callback registrada no Google que funcione em
  preview (preview normal tem domínio aleatório a cada deploy, nunca
  registrável).

## Em aberto — identificação de usuário

A trava hoje é **binária**: passa quem tem cookie válido, mas o backend não
sabe **quem** passou — o middleware verifica o e-mail mas nunca repassa
adiante. Isso trava, por exemplo, o `thread_id` do chat do A10/A14 ser por
**e-mail** (seguindo a pessoa entre navegador/dispositivo) — hoje é um
`crypto.randomUUID()` gerado no navegador e preso no `localStorage`, órfão
se trocar de navegador ou limpar o storage.

Desenho já discutido, não implementado: `middleware.js` passa a injetar um
header confiável `x-lm-user-email` (sobrescrevendo qualquer valor vindo do
cliente, pra não dar pra forjar) depois de verificar o cookie; endpoint novo
`GET /api/me` no backend lê esse header; frontend busca isso uma vez e usa
o e-mail pra montar o `thread_id` em vez do UUID aleatório. Precisa de
fallback pra dev local (sem `middleware.js` rodando, sem header) — variável
tipo `DEV_USER_EMAIL`, ou cair de volta pro UUID quando o header não vier.

**Adiado por decisão explícita 2026-08-28** ("depois fazemos isso") — sem
prazo definido, só registrado pra não se perder.
