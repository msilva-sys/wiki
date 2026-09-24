---
type: synthesis
status: active
updated: 2026-09-24
date: 2026-09-24
tags: [auth, security, liveauth, trava-de-dominio]
aliases: [inventário trava de domínio, impacto liveauth, inventário de auth]
---

# Trava de domínio e autenticação — inventário para o LiveAuth

Levantamento pedido por msilva para medir o impacto de uma eventual
migração para o [[LiveAuth]]: quais projetos, além do
[[Agent Flow|Fluxo Agêntico]], já implementam algum tipo de gate de
autenticação hoje. Pesquisa via `gh` na conta `tech-livemode`,
2026-09-24, cobrindo a org GitHub `livemode-org` (34 repos) e os
repositórios pessoais da própria conta `tech-livemode` (59 repos).

## Método (e uma lacuna descoberta no caminho)

Primeira rodada usou `gh api search/code` (busca de código do GitHub) por
termos como `trava-de-dominio`, `_middleware.js`, `DOMINIO_PERMITIDO`.
msilva achou por fora, na Vercel, um deploy (`tasks-projetos`) com
`/login` que a busca não retornou — o arquivo era `middleware.ts`, não
`.js`, e mesmo `filename:middleware.ts` deu zero resultado pra um arquivo
que existia no repo. **A busca de código do GitHub tem lacuna de
indexação real** (provável recência do push), não é confiável sozinha
pra esse tipo de varredura.

Segunda rodada: listagem direta da raiz de cada repo (API de conteúdo,
não busca) nos 34 + 59 repos, procurando `middleware.js`, `middleware.ts`,
`functions/`, `vercel.json`. Mais lento, mas não depende de indexação.
Cada acerto foi lido por inteiro pra confirmar se é de fato um gate de
auth (alguns `functions/` eram proxy reverso ou Cloud Functions sem
relação com autenticação, descartados: `hub-administrativo` é um proxy
reverso pra outros Pages projects, `status-salas` só redireciona domínio
antigo, `whatsapp-image-decryptor/functions` é Cloud Function de
descriptografia de imagem).

## Padrão 1 — skill `trava-de-dominio` (Google OAuth por domínio, 5 deploys)

Fonte: `livemode-brain/skills/trava-de-dominio`. Cada instalação é
isolada — credencial OAuth do Google e `SESSION_SECRET` próprios por
projeto, sem registro central, falha aberta se faltar secret.

| Repo (org) | Adaptador |
|---|---|
| `projetos-vitrine` | Vercel (`middleware.js` + `api/auth/*`) |
| `projetos-hub-interno` | Vercel |
| `livemode-pulso` | Vercel |
| `guia-de-um-builder` | Cloudflare Pages (`functions/_middleware.js`) — único com trava extra no CI que bloqueia deploy sem os 3 secrets |
| `vitrine-ia-lmarques` | Cloudflare Pages |

`client-intelligence` e `livemode-template` têm a skill copiada mas não
instalada (sem gate ativo na raiz). O próprio [[Agent Flow|Fluxo
Agêntico]] usa o mesmo modelo mental mas com implementação própria em
`auth_gate.py`, não a skill (`livemode-fluxo-agentico`, ver
[[2026-08-28 Trava de domínio no Fluxo Agêntico via Google OAuth]]).

## Padrão 2 — Auth.js/NextAuth própria, domínio restrito via callback `signIn` (5 deploys)

Cada um reimplementa o mesmo gate (Google provider + checar sufixo do
e-mail em `signIn()`) do zero, em código próprio — nenhum usa a skill:

| Repo | Conta | Detalhe |
|---|---|---|
| `escala-producao` | pessoal | `ALLOWED_EMAIL_DOMAIN`, hard gate no callback |
| `cazetv-posts-app` | pessoal | mesmo padrão, `ALLOWED_EMAIL_DOMAIN` |
| `ai-enablement-hub` | pessoal | + rota admin restrita por `role` no token |
| `sistemas-visuais-hub` | pessoal | + acesso total restrito a uma allow-list de pessoas nomeadas, + exceção pra convidados externos numa única rota |
| `livemode-juridico` | pessoal (não a org — ver abaixo) | domínio checado em `app/page.tsx` (Node runtime), middleware só confere presença de cookie |

Estes já têm **RBAC por pessoa/rota** dentro do próprio app (admin vs.
viewer, allow-lists nomeadas) — mais granular que o modelo do LiveAuth
hoje (grupo binário por `app_id`, ver Non-goals do design doc). Se
migrarem, perdem essa granularidade a menos que criem um grupo por role.

## Padrão 3 — sessão Firebase própria (1 deploy)

`tasks-projetos` (pessoal, o que msilva achou na Vercel): Firebase Auth
client + Admin SDK, cookie de sessão verificado no server, role
(`admin`/`viewer`) por `ADMIN_EMAILS`. Middleware só confere presença do
cookie (Edge Runtime não roda o Admin SDK) — mesma técnica de
"checagem barata no edge, checagem real no server" que `livemode-juridico`
usa e que o próprio design do LiveAuth também usa (Blocking Function +
verificação de assinatura). É o deploy mais parecido em arquitetura com o
LiveAuth, mas construído antes e isolado.

## Padrão 4 — Supabase Auth (1 deploy, sem confirmação de domínio)

`live-content` (pessoal): sessão via Supabase, redireciona pra
`/auth/signin` sem sessão. Não achei checagem de domínio de e-mail no
código lido — se restringe a `@livemode.com`, é por outro mecanismo não
identificado nesta varredura.

## Padrão 5 — HTTP Basic Auth, credencial compartilhada (2 deploys)

`world-cup-picks` e `Monitor-de-Dados` (pessoal): usuário/senha únicos
via env var, não por pessoa. Modelo de segurança diferente dos outros —
mais parecido com a "senha compartilhada" que o `guia-de-um-builder`
documentou ter abandonado (ver `governanca.md` desse repo, trava de
domínio por e-mail substituiu senha única em 2026-09-22). Ambos falham
aberto se a env var não estiver configurada.

## Sem gate detectável (não é prova de ausência)

Têm `vercel.json` mas nenhum `middleware`/`functions` na raiz —
`bq-quality`, `caz-tv-escala-hub`, `content-pulse`, `copa-audiencia`,
`copa2026-labelling`, `dashboard_ao_vivo_copa`, `livemode-roteiros-nextjs`,
`narradores-cztv-web`, `portal-audiencia-programacao`,
`social-media-thumb-collector`. Não confirma ausência de proteção — pode
haver Vercel Deployment Protection configurado só no painel (não aparece
no repo), ou o conteúdo pode ser deliberadamente público (ex. picks de
Copa do Mundo). Não investigado a fundo; relevante pro escopo da issue
PRO-717 (skill que aponta apps com dado sensível sem autenticação).

## `livemode-juridico`: repo fantasma na org

`livemode-org/livemode-juridico` existe mas está **vazio**. O app real
mora em `tech-livemode/livemode-juridico` (conta pessoal, Padrão 2
acima). Mesmo tipo de fragmentação de ownership que a trava de domínio
já tem — custódia não migrou junto com o nome do repo.

## LiveAuth já tem repo e projeto Firebase definido

`livemode-org/livemode-liveauth` já existe (Cloudflare Worker,
`wrangler.jsonc`, skill `liveauth-connect` já no repo). `.firebaserc`
aponta pro projeto **`livemode-liveauth-42ca1`** — dedicado, não o
`livemode-roteiros-dev` compartilhado com o LiveScript. **Resolve o Open
Issue "qual projeto Firebase" na synthesis de design** — ver
[[LiveAuth — Identidade e Autorização por Grupo entre Apps Livemode]].

## Implicação para o LiveAuth

Superfície de apps com auth próprio identificados: **11 deploys** (5
skill + 5 Auth.js própria + 1 Firebase própria), fora os 2 de Basic Auth
(modelo diferente, não comparável) e o Fluxo Agêntico. O design doc do
LiveAuth cobre migração do Fluxo Agêntico como não-objetivo por ora, mas
não menciona nenhum destes 11 — se um dia virar migração, não é troca de
um lugar só, e pelo menos 2 apps (`sistemas-visuais-hub`,
`ai-enablement-hub`) têm RBAC por pessoa que o modelo atual de grupo
binário do LiveAuth não cobre sem adaptação.

## Open questions

- Não confirmado se algum dos 11 apps tem plano de virar consumidor do
  LiveAuth.
- `live-content` (Supabase) — não confirmado se restringe por domínio ou
  não.
- 10 repos com `vercel.json` sem gate detectável no código — não
  investigado se têm proteção fora do repo (Vercel Deployment
  Protection) ou se são intencionalmente públicos.
- Escopo desta pesquisa: conta `tech-livemode` (org + pessoal). Não
  cobre outras contas/orgs GitHub da Livemode, se existirem.
